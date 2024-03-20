---
layout: post
title:  ":uk: Apache NuttX: Implementing On-Demand Paging"
author: Tiago
date:   2024-03-20 07:00:00 -0300
categories: articles
tags: nuttx embedded
background: '/img/posts/common/nuttx_logo.svg'
---

Apache NuttX - Implement On-Demand Paging
=========================================

Hi!

Just a quick break on the series of articles that I have been publishing regarding my homemade Home-Theater system: I'll switch to another subject that got my attention during the last few weeks: On-demand Paging on Apache NuttX.

For those who don't know yet, I have been working to support Espressif's SoCs on Apache NuttX since August 2022. NuttX provides a lot of interesting features that are not commonly available for embedded devices, namely the POSIX compatibility and [Kernel Build](https://nuttx.apache.org/docs/12.4.0/implementation/processes_vs_tasks.html#nuttx-build-types) mode, which provides a kernel without any applications, completely isolating the processes very similar as it's done in Linux (of course, this requires the device to have an MMU (Memory Management Unit) to enable such memory separation).

This post tries to summarize my journey while experimenting On-Demand Paging on NuttX.

## What is On-Demand Paging?

Well, first things first: On-demand paging is a technique used to manage physical memory which may provide lazy-loading and overcommit memory. The basic idea is to allow a program to execute even though the entire program is not resident in memory. The program is loaded into memory on demand. This is done in many operating systems - Linux, for instance - to allow large programs to execute on small memory systems.

Commonly, a Memory Management Unit (MMU) is used to map virtual memory into physical memory. Applications are then loaded into virtual memory address spaces and access to physical memory is managed by the MMU. If the virtual memory is not resident in physical memory, then a page fault occurs. The operating system then loads the missing page into memory and resumes execution.

**Lazy-loading** refers to the fact of the program not being loaded into the main memory at once: it may reside in a non-volatile media (the hard drive, a flash memory, a memory card, etc) and it is loaded into the main memory "on demand", i.e. when it tries to access a not yet mapped memory page, generating a page-fault exception that needs to map the program's contents into main memory.

As a secondary effect, On-Demand Paging (and Lazy-Loading) enables us to use **Overcommit Memory**: the sum of all the processes' required memory (virtual memory) may be greater than the actual available physical memory. This is possible because not all processes use the amount of reserved memory and
run at the same time. Thus, it is possible to allocate more virtual memory to a single process without "worrying" about the other processes' memory. Of course, the physical memory may be completely allocated at the same time, but there are other mechanisms to deal with it when this condition happens (like freeing an unused memory region or even saving it on the non-volatile storage temporarily).

### Why On-Demand Paging is Important?

For memory-constrained devices, like embedded systems, on-demand paging enables us to run larger programs. In flat-build mode - when all the tasks run in the same memory environment - the operating system previously allocated a fixed amount of memory for the task's stack. The memory heap is common for all applications (and, eventually, to the kernel itself) and the applications are either executed directly from the flash storage when Execute-In-Place (XIP) or loaded to the main memory as the device boots up. On the other hand, if we have an MMU-enabled device, we can use *Kernel Build* mode, whereas the kernel is completely isolated from the userspace (the place where the applications are into). The applications are completely isolated from the kernel by using its memory address environment. Each memory addresses environment contains its own `.text`, `.data`, and `.heap` sections (the place where the applications' instruction, data, and heap are allocated, respectively) in the virtual memory. Finally, the application's stack is allocated from its heap memory. If on-demand paging is not available, this virtual memory is mapped 1-to-1 to the available physical memory and loaded at once when the program is loaded. Considering a 1-to-1 mapping happening before the application starts executing, the sum of all loaded applications' memory must be smaller than the system's available memory (physical memory)
By using on-demand paging, on the other hand, one can delay the memory mapping and use more than the system's available memory because the memory mapping occurs only when needed.

This is particularly useful for running applications (and userspace libraries) that allocate "huge" amounts of memory because they don't know how much memory will be actually used beforehand. This is true, for instance, for language interpreters. **By not being required to use lower fixed memory (1-to-1 virtual-to-physical mapping) for an application's process, on-demand paging actually frees up more physical memory to the system, enabling us to run more complex applications and libraries in a memory-constrained embedded system.**

### ~~Legacy~~ On-Demand Paging on NuttX

> Wait! Does NuttX already support On-Demand Paging?

Technically speaking, yes. At least, there was a config named `CONFIG_PAGING` and the associated [entry](https://nuttx.apache.org/docs/12.4.0/components/paging.html) in the Documentation (in *OS Components* -> *On-Demand Paging*). But, according to Gregory Nutt:

> "The on-demand paging logic is obsolete and incomplete as well as incorrect for the current architecture.  It was an experiment only for the LPC3131 when I was running out of a small SRAM with dynamic paging from SPI flash.  It really should be removed."

This was the response when I first asked about On-Demand Paging on NuttX's dev mailing list, which can be seen [here](https://www.mail-archive.com/dev@nuttx.apache.org/msg11005.html).

## Address Environments on NuttX

NuttX supports address environments. According to the [Documentation](https://nuttx.apache.org/docs/12.4.0/reference/os/addrenv.html):

> CPUs that support memory management units (MMUs) may provide address environments within which tasks and their child threads execute.

In particular, when we are running NuttX in *Kernel Build* mode, we are interested in the **Binary Loader Support** interface, in which whenever an application is loaded into main memory during runtime, a new process will be created and, thus, a new memory address environment, by calling [`up_addrenv_create`](https://nuttx.apache.org/docs/latest/reference/os/addrenv.html#c.up_addrenv_create). This function takes the following arguments:

 * `textsize` - The size (in bytes) of the .text address environment needed by the task.  This region may be read/execute only.
 * `datasize` - The size (in bytes) of the .data/.bss address environment needed by the task.  This region may be read/write only.
 * `heapsize` - The initial size (in bytes) of the heap address environment needed by the task.  This region may be read/write only.

Whereas the `textsize` and the `datasize` are parameters completely dependent on the application being loaded, the `heapsize` is defined by `MAX(ARCH_HEAP_SIZE, CONFIG_ELF_STACKSIZE)`, `ARCH_HEAP_SIZE` being `CONFIG_ARCH_HEAP_NPAGES * CONFIG_MM_PGSIZE`.

The memory address environment is created whenever an application is loaded. It 1) creates virtual memory addresses for the `.text`, `.data`, and `.heap` sections of the application, 2) maps this virtual memory to physical memory before running the application and, 3) after the application exits, this memory address environment is destroyed, releasing the previously allocated physical memory.

### RISC-V Example: Kernel Build mode + Address Environments

Let's take the RISC-V QEMU "board" as an example to verify how an application is loaded in the Kernel Build mode. For this example, the [`knsh32-romfs`](https://nuttx.apache.org/docs/latest/platforms/risc-v/qemu-rv/boards/rv-virt/index.html#knsh32-romfs) `defconfig` will be used.

#### Loading an Application

Let's execute the `hello` app:

```
NuttShell (NSH) NuttX-10.4.0
nsh> /system/bin/hello
```

By throwing this command in the *NuttShell* terminal, the following functions are called in the userspace:
```
#0  sys_call0 (nbr=130) at /home/tiago/Documents/tiago/learning/nuttx/nuttxspace/nuttx/include/arch/syscall.h:175
#1  0xc000db0c in get_environ_ptr () at proxies/PROXY_get_environ_ptr.c:11
#2  0xc0007272 in nsh_fileapp (vtbl=0xc0800370, cmd=0xc08005f8 "/system/bin/hello", argv=0xc0900a5c, redirfile=0x0, oflags=0) at nsh_fileapps.c:166
#3  0xc0003466 in nsh_execute (vtbl=0xc0800370, argc=1, argv=0xc0900a5c, redirfile=0x0, oflags=0) at nsh_parse.c:674
#4  0xc0004af0 in nsh_parse_command (vtbl=0xc0800370, cmdline=0xc08005f8 "/system/bin/hello") at nsh_parse.c:2740
#5  0xc0004b9c in nsh_parse (vtbl=0xc0800370, cmdline=0xc08005f8 "/system/bin/hello") at nsh_parse.c:2824
#6  0xc0002c78 in nsh_session (pstate=0xc0800370, login=1, argc=1, argv=0xc0900010) at nsh_session.c:245
#7  0xc00029e6 in nsh_consolemain (argc=1, argv=0xc0900010) at nsh_consolemain.c:75
#8  0xc000078c in main (argc=1, argv=0xc0900010) at nsh_main.c:74
```

Note that the "last" function is a syscall (`sys_call0`). This is the system call to enable the kernel to set up the application before running it. Let's take a look at the kernel side up to the call to `up_addrenv_create`:

```
#0  up_addrenv_create (textsize=4400, datasize=8, heapsize=8388608, addrenv=0x80408158) at common/riscv_addrenv.c:388
#1  0x8001c4c0 in elf_addrenv_alloc (loadinfo=0x804093b8, textsize=4400, datasize=8, heapsize=8388608) at libelf/libelf_addrenv.c:104
#2  0x8001c822 in elf_load (loadinfo=0x804093b8) at libelf/libelf_load.c:359
#3  0x8001bbfa in elf_loadbinary (binp=0x804080b0, filename=0xc08005f8 "/system/bin/hello", exports=0x0, nexports=0) at elf.c:257
#4  0x8001cde8 in load_absmodule (bin=0x804080b0, filename=0xc08005f8 "/system/bin/hello", exports=0x0, nexports=0) at binfmt_loadmodule.c:115
#5  0x8001ce9a in load_module (bin=0x804080b0, filename=0xc08005f8 "/system/bin/hello", exports=0x0, nexports=0) at binfmt_loadmodule.c:219
#6  0x8001baaa in exec_internal (filename=0xc08005f8 "/system/bin/hello", argv=0xc0900a5c, envp=0xc0800338, exports=0x0, nexports=0, actions=0x0, attr=0xc09009a0, spawn=true) at binfmt_exec.c:98
#7  0x8001bb68 in exec_spawn (filename=0xc08005f8 "/system/bin/hello", argv=0xc0900a5c, envp=0xc0800338, exports=0x0, nexports=0, actions=0x0, attr=0xc09009a0) at binfmt_exec.c:220
#8  0x80020546 in nxposix_spawn_exec (pidp=0xc090099c, path=0xc08005f8 "/system/bin/hello", actions=0x0, attr=0xc09009a0, argv=0xc0900a5c, envp=0xc0800338) at task/task_posixspawn.c:107
#9  0x80020598 in posix_spawn (pid=0xc090099c, path=0xc08005f8 "/system/bin/hello", file_actions=0xc09009b0, attr=0xc09009a0, argv=0xc0900a5c, envp=0xc0800338) at task/task_posixspawn.c:224
#10 0x8000be60 in STUB_posix_spawn (nbr=108, parm1=3230665116, parm2=3229615608, parm3=3230665136, parm4=3230665120, parm5=3230665308, parm6=3229614904) at stubs/STUB_posix_spawn.c:11
#11 0x800022de in dispatch_syscall () at common/riscv_swint.c:76
```

Please note that the syscall is treated in the kernel, calling the functions to load the application (`elf_loadbinary`, for instance). Let's take a look at `up_addrenv_create` implementation in [`arch/risc-v/src/common/riscv_addrenv.c`](https://github.com/apache/nuttx/blob/releases/12.4/arch/risc-v/src/common/riscv_addrenv.c#L367):

```
/* Create the static page tables */

  ret = create_spgtables(addrenv);

  if (ret < 0)
    {
      serr("ERROR: Failed to create static page tables\n");
      goto errout;
    }

  /* Map the kernel memory for the user */

  ret = copy_kernel_mappings(addrenv);

  if (ret < 0)
    {
      serr("ERROR: Failed to copy kernel mappings to new environment");
      goto errout;
    }

  /* Calculate the base addresses for convenience */

  resvbase = CONFIG_ARCH_DATA_VBASE;
  resvsize = ARCH_DATA_RESERVE_SIZE;
  textbase = CONFIG_ARCH_TEXT_VBASE;
  database = resvbase + MM_PGALIGNUP(resvsize);
  heapbase = CONFIG_ARCH_HEAP_VBASE;

  /* Allocate 1 extra page for heap, temporary fix for #5811 */

  heapsize = heapsize + MM_PGALIGNUP(CONFIG_DEFAULT_TASK_STACKSIZE);

  /* Map the reserved area */

  ret = create_region(addrenv, resvbase, resvsize, MMU_UDATA_FLAGS);

  if (ret < 0)
    {
      berr("ERROR: Failed to create reserved region: %d\n", ret);
      goto errout;
    }

  /* Map each region in turn */

  ret = create_region(addrenv, textbase, textsize, MMU_UTEXT_FLAGS);

  if (ret < 0)
    {
      berr("ERROR: Failed to create .text region: %d\n", ret);
      goto errout;
    }

  ret = create_region(addrenv, database, datasize, MMU_UDATA_FLAGS);

  if (ret < 0)
    {
      berr("ERROR: Failed to create .bss/.data region: %d\n", ret);
      goto errout;
    }

  ret = create_region(addrenv, heapbase, heapsize, MMU_UDATA_FLAGS);

  if (ret < 0)
    {
      berr("ERROR: Failed to create heap region: %d\n", ret);
      goto errout;
    }
```

1. [`create_spgtables`](https://github.com/apache/nuttx/blob/releases/12.4/arch/risc-v/src/common/riscv_addrenv.c#L156) creates the static page tables using [`mm_pgalloc`](https://github.com/apache/nuttx/blob/releases/12.4/mm/mm_gran/mm_pgalloc.c#L146) (where the page memory comes from?)
2. [`create_region`](https://github.com/apache/nuttx/blob/releases/12.4/arch/risc-v/src/common/riscv_addrenv.c#L233) creates the reserved area, text, data, and heap sections. All necessary memory pages are then allocated up to the required size of the region. This is done in line [285](https://github.com/apache/nuttx/blob/releases/12.4/arch/risc-v/src/common/riscv_addrenv.c#L285) of the function. 

#### Page Pool Heap

> Where the "memory" allocated by [`mm_pgalloc`](https://github.com/apache/nuttx/blob/releases/12.4/mm/mm_gran/mm_pgalloc.c#L146) come from?

It comes from the granule page allocator, which is initialized by [`mm_pginitialize`](https://github.com/apache/nuttx/blob/releases/12.4/mm/mm_gran/mm_pgalloc.c#L97) in [`nx_start`](https://github.com/apache/nuttx/blob/releases/12.4/sched/init/nx_start.c#L472). `mm_pginitialize`'s arguments are given by [`up_allocate_pgheap`](https://github.com/apache/nuttx/blob/releases/12.4/arch/risc-v/src/qemu-rv/qemu_rv_pgalloc.c#L61), which evaluates the symbols defined in the linker script. Let's take a look at the linker script for the RISC-V QEMU board (for Kernel Build mode) in [`boards/risc-v/qemu-rv/rv-virt/scripts/ld-kernel32.script`](https://github.com/apache/nuttx/blob/releases/12.4/boards/risc-v/qemu-rv/rv-virt/scripts/ld-kernel32.script):

```
MEMORY
{
    kflash (rx) : ORIGIN = 0x80000000, LENGTH = 4096K   /* w/ cache */
    ksram (rwx) : ORIGIN = 0x80400000, LENGTH = 4096K   /* w/ cache */
    pgram (rwx) : ORIGIN = 0x80800000, LENGTH = 4096K   /* w/ cache */
}

OUTPUT_ARCH("riscv")

/* Provide the kernel boundaries */

__kflash_start = ORIGIN(kflash);
__kflash_size = LENGTH(kflash);
__ksram_start = ORIGIN(ksram);
__ksram_size = LENGTH(ksram);
__ksram_end = ORIGIN(ksram) + LENGTH(ksram);

/* Page heap */

__pgheap_start = ORIGIN(pgram);
__pgheap_size = LENGTH(pgram);
```

`__pgheap_start` and `__pgheap_size` defines the region that is used by the granule allocator. Finally, considering the MMU specification for RISC-V devices, the document [*Volume 2, Privileged Specification version 20211203*](https://github.com/riscv/riscv-isa-manual/releases/tag/Priv-v1.12) states that *"A page table is exactly the size of a page and must always be aligned to a page boundary"*.

In conclusion: The page table maps a virtual memory to a physical memory address. This physical memory address region is known as "Page Heap", which starts at address `0x80800000` and contains 4096 KiB (4MBi). This memory is allocated by `mm_pgalloc` in chunks of a page table size (4KiB, in this case). Whenever an application is loaded, memory is "dished out" from this region, creating an address environment with the same virtual memory addresses for all the applications.

#### Memory Limits

The virtual memory addresses are defined by `CONFIG_ARCH_DATA_VBASE`, `CONFIG_ARCH_TEXT_VBASE` and `CONFIG_ARCH_HEAP_VBASE` configs for each region and set up in [`up_addrenv_create`](https://github.com/apache/nuttx/blob/releases/12.4/arch/risc-v/src/common/riscv_addrenv.c#L406). But what is the maximum size for each region? These values are defined by the number of pages of the virtual memory - defined by `CONFIG_ARCH_DATA_NPAGES`, `CONFIG_ARCH_TEXT_NPAGES` and `CONFIG_ARCH_HEAP_NPAGES` multiplied by the size of a page (4KiB). Considering the [`knsh32_romfs`](https://github.com/apache/nuttx/blob/cb32e6d50a87b3c9c723cacc726aee3465a2868e/boards/risc-v/qemu-rv/rv-virt/configs/knsh32_romfs/defconfig) defconfig, we have:

```
CONFIG_ARCH_ADDRENV=y
CONFIG_ARCH_DATA_NPAGES=128
CONFIG_ARCH_DATA_VBASE=0xC0100000
CONFIG_ARCH_HEAP_NPAGES=128
CONFIG_ARCH_HEAP_VBASE=0xC0800000
CONFIG_ARCH_TEXT_NPAGES=128
CONFIG_ARCH_TEXT_VBASE=0xC0000000
CONFIG_RAM_SIZE=4194304
CONFIG_RAM_START=0x80400000
CONFIG_ARCH_PGPOOL_PBASE=0x80800000
CONFIG_ARCH_PGPOOL_SIZE=4194304
CONFIG_ARCH_PGPOOL_VBASE=0x80800000
```

Which states that each virtual memory section has up to (128 * 4KiB) 512 KiB. **This is the limit an application may reserve for the `.text`, `.data` and its stack** (stack is allocated from the process heap).

## Implementing On-Demand Paging

The simpler the better. As we have seen, NuttX already supports a Kernel Build mode with Address Environments. The RISC-V implementation already deals with its MMU page tables and isolates the physical memory from the userspace, creating an address environment for each application. What if we simply delay memory mapping?

Again, according to [*Volume 2, Privileged Specification version 20211203*](https://github.com/riscv/riscv-isa-manual/releases/tag/Priv-v1.12), which defines the `Sv32` and `Sv39` MMUs (already implemented on NuttX) states that *"Attempting to fetch an instruction from a page that does not have execute permissions raises a fetch page-fault exception. Attempting to execute a load or load-reserved instruction whose effective address lies within a page without read permissions raises a load page-fault exception. Attempting to execute a store, store-conditional, or AMO instruction whose effective address lies within a page without write permissions raises a store page-fault exception"*.

Theoretically, on-demand paging could be implemented by simply not mapping all the application's memory and treating page faults when the associated exception arises.

### Implementation Details 

The PR [#11824](https://github.com/apache/nuttx/pull/11824) was submitted to implement this preliminary version of on-demand paging for RISC-V devices on NuttX.

In summary, it delayed the mapping of all the required memory pages in [`create_region`](https://github.com/apache/nuttx/blob/c67502d9b49b2d9bb8c404c9d88d96a07f00a7ae/arch/risc-v/src/common/riscv_addrenv.c#L285), mapping only the first page if `CONFIG_PAGING=y` and created the function [`riscv_fillpage`](https://github.com/apache/nuttx/blob/c67502d9b49b2d9bb8c404c9d88d96a07f00a7ae/arch/risc-v/src/common/riscv_exception.c#L99) to treat the page-fault exception, mapping the necessary pages on-demand.

## Testing

One can test this On-Demand paging implementation by running the RISC-V QEMU emulator with testing applications from [this](https://github.com/tmedicci/incubator-nuttx-apps/tree/feature/on-demand_paging) repository.

The PR provided a new defconfig in `boards/risc-v/qemu-rv/rv-virt/configs/knsh32_paging/defconfig`, very similar to `knsh32_romfs `, but enabling on-demand paging. This configuration simulates a 4MiB device (using QEMU), but sets the number of heap pages equal to ``CONFIG_ARCH_HEAP_NPAGES=2048``. This means that each process's heap is 8MiB, whereas `CONFIG_POSIX_SPAWN_DEFAULT_STACKSIZE` is `1048576` (1MiB) represents the stack size of the processes (which is allocated from the process's heap). This configuration is used for 32-bit RISC-V which implements the `Sv32` MMU specification and enables processes to have their own address space larger than the available physical memory.

To build and run it:
```
make -j distclean
./tools/configure.sh -l rv-virt:knsh32_paging
kconfig-tweak -e EXAMPLES_ALLOC_TEST
kconfig-tweak -e EXAMPLES_STACK_TEST
make olddefconfig
make -j$(nproc)
make export -j$(nproc)
pushd ../apps
./tools/mkimport.sh -z -x ../nuttx/nuttx-export-*.tar.gz
make import -j$(nproc)\
./tools/mkromfsimg.sh ../nuttx/arch/risc-v/src/board/romfs_boot.c
popd
make -j$(nproc)
qemu-system-riscv32 -M virt,aclint=on -cpu rv32 -smp 8 -bios none -kernel nuttx -nographic
```

Some simple tests:

Please note that the `riscv_fillpage` log just after executing the application refers to `text`, `data` and the `top` of the virtual heap sections and are the same for both test scenarios:

### Allocation tests:

The `alloc_test` allocates buffers of a specific size:

```
nsh> /system/bin/alloc_test
Usage: /system/bin/alloc_test <buffer size> <number of buffers>
```

Running it to allocate 100 buffers the 128 bytes:
```
nsh> /system/bin/alloc_test 128 100 &
[   42.848000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000d63e, MTVAL: c0001000
[   42.849000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000d63e, MTVAL: c0002000
[   42.850000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000d63e, MTVAL: c0003000
[   42.851000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000d63e, MTVAL: c0004000
[   42.852000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000de68, MTVAL: c1000ffc
[   42.852000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000e3e8, MTVAL: c0b00374
[   42.852000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000e5b0, MTVAL: c08ffffc
[   42.852000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000f428, MTVAL: c0a00004
[   42.852000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000d508, MTVAL: c0900000
/system/bin/alloc_test [0:100]
nsh> [   42.854000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: c000001e, MTVAL: c09ffffc
[   42.856000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: c00009be, MTVAL: c080300c
100 buffers of size 128 allocated
```

Shows that it was required to map the page that contains the virtual memory `c080300c` (`CONFIG_ARCH_HEAP_VBASE=0xC0800000` is the virtual base for the process heap). This program finishes running in a loop to enable checking the overall system memory:

```
nsh> ps
  PID GROUP PRI POLICY   TYPE    NPX STATE    EVENT     SIGMASK           STACK COMMAND
    0     0   0 FIFO     Kthread   - Ready              0000000000000000 003056 Idle_Task
    1     1 100 RR       Kthread   - Waiting  Semaphore 0000000000000000 001984 lpwork 0x80405c90 0x80405ca4
    3     3 100 RR       Task      - Running            0000000000000000 003024 /system/bin/init
   14    14 100 RR       Task      - Waiting  Signal    0000000000000000 1048512 /system/bin/alloc_test 128 100
   15    15 100 RR       Task      - Waiting  Signal    0000000000000000 1048512 /system/bin/alloc_test 128 100
nsh> free
                   total       used       free    maxused    maxfree  nused  nfree
        Kmem:    4166268      20100    4146168    4234628    3143672     84      6
        Page:    4194304     290816    3903488    3903488
```

`Page` is the amount of physical memory that could be used for virtual memory mapping (from the Page Pool Heap). Killing the processes shows the same amount of free memory before the execution (killing the process calls `up_addrenv_destroy` which unmaps the virtual memory and frees the physical page. There is no need to call free in the app). It's possible to run multiple instances of this application. Each process has 8MiB virtual memory (more than the 4MiB physical memory available). It would only fail if the total used memory exceeds the physical memory and there is no room for allocating more memory pages in the page-fault exception.

### Stack usage test:

`stack_test` calls a function recursively to check stack usage. It was run with `10` and `1000` calls to the function, respectively. In the first case, it's possible to check that it finished the test application (the recursion) without mapping any subsequent pages on stack. Running with `1000`, we can check that `riscv_fillpage` mapped subsequent addresses (highest to lowest, as expected):

```
NuttShell (NSH) NuttX-10.4.0
nsh> /system/bin/stack_test 10
[   61.934000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000d63e, MTVAL: c0001000
[   61.935000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000d63e, MTVAL: c0002000
[   61.936000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000d63e, MTVAL: c0003000
[   61.937000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000de68, MTVAL: c1000ffc
[   61.937000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000e3e8, MTVAL: c0b00374
[   61.937000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000e5b0, MTVAL: c08ffffc
[   61.937000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000f428, MTVAL: c0a00004
[   61.937000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000d508, MTVAL: c0900000
[   61.939000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: c000001e, MTVAL: c09ffffc
Recursion depth: 10
Reached the base of recursion
nsh> /system/bin/stack_test 1000
[   67.424000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000d63e, MTVAL: c0001000
[   67.425000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000d63e, MTVAL: c0002000
[   67.426000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000d63e, MTVAL: c0003000
[   67.427000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000de68, MTVAL: c1000ffc
[   67.427000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000e3e8, MTVAL: c0b00374
[   67.427000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000e5b0, MTVAL: c08ffffc
[   67.427000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000f428, MTVAL: c0a00004
[   67.427000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: 8000d508, MTVAL: c0900000
[   67.428000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: c000001e, MTVAL: c09ffffc
Recursion depth: 1000
[   67.431000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: c0000040, MTVAL: c09feffc
[   67.432000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: c0000040, MTVAL: c09fdffc
[   67.433000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: c0000040, MTVAL: c09fcffc
[   67.434000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: c0000040, MTVAL: c09fbffc
[   67.434000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: c0000040, MTVAL: c09faffc
[   67.435000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: c0000040, MTVAL: c09f9ffc
[   67.435000] riscv_fillpage: EXCEPTION: Store/AMO page fault. MCAUSE: 0000000f, EPC: c0000040, MTVAL: c09f8ffc
Reached the base of recursion
```

Again, log messages that refer addresses from `0xc09feffc` to `0xc09f8ffc` show stack usage beyond the single physical memory page already allocated and after the program started (i.e. allocated according to the application stack usage needs).

## Conclusion

This article is intended to explore NuttX's memory management subsystem and highlights some aspects of how on-demand paging is possible (and very welcomed!) in NuttX.

Please note that it isn't my intention to show the inner details of how the `Sv32` or `Sv39` MMU of RISC-V works (nor how the virtual-to-physical memory translation occurs). I highly recommend reading Mr. Lup Yuen Lee's article about this topic (released just a few days after I submitted the PR!): [On-Demand Paging with Apache NuttX RTOS on Ox64 BL808 SBC](https://github.com/lupyuen2/wip-nuttx/blob/on-demand-paging3/README.md#mmu-log-for-qemu-with-on-demand-paging).

This implementation is quite simplistic. It's an introduction to the on-demand feature on NuttX. On-demand paging still may fail because the available physical memory is full and there are no more free pages to be allocated. In this case, other techniques may be applied to free old memory pages, but this subject is beyond the scope of this article.

## References
 - [RISC-V ISA specifications: Volume 2, Privileged Specification version 20211203](https://github.com/riscv/riscv-isa-manual/releases/tag/Priv-v1.12)
 - [NuttX Documentation: Address Environments](https://nuttx.apache.org/docs/latest/reference/os/addrenv.html)
 - [RISC-V Ox64 BL808 SBC: NuttX Apps and Initial RAM Disk](https://lupyuen.codeberg.page/articles/app.html)
 - [RISC-V Ox64 BL808 SBC: Sv39 Memory Management Unit](https://lupyuen.codeberg.page/articles/mmu.html)
 - [Text describing xv6 on RISC-V](https://github.com/mit-pdos/xv6-riscv-book)
