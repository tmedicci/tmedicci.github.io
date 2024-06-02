---
layout: post
title:  ":uk: Home Theater DIY (Loudspeakers, Amplifiers and HTPC) - Part 5"
author: Tiago
date:   2024-06-02 08:00:00 -0300
categories: projects
tags: project audio sound electronics
background: '/img/posts/2024-06-02-home_theater_diy_5/capa.png'
---

Home Theater DIY - The Saga of the Loudspeakers
===============================================

*Este artigo também foi publicado em português e pode ser acessado [aqui]({% post_url 2024-06-02-home_theater_diy_5 %}).*

## Sound Server and Media Center (Raspberry Pi 4)
The sound server is the component that connects the other components of the system: it communicates with the media being played and the sound card, which, in turn, is connected to the power amplifiers. The sound server will be implemented on a Raspberry Pi 4 that is also being used as the media center of the system, providing content to be played on a screen through the HDMI output.

### Overview
Since the beginning of this topic, I've been presenting the system components. Now, I present these components - also adding the sound server - in the form of a block diagram for better understanding:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image10.jpg alt="Overview - DIY Sound System" %}{: .img-fluid}

### Sound Card: Creative Soundblaster X-Fi Surround 5.1 Pro

The sound card is one of the main components of the system: the Creative card was chosen for its excellent sound performance, compatibility with Linux and Windows operating systems, and also for having volume control through a rotary button on the card itself as well as a remote control.

As shown in the diagram, the sound card acts as the pre-amplifier for the audio signal sent to the Gainclone amplifiers through its audio outputs, as shown in the following image:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image11.jpg alt="Creative Soundblaster X-Fi Surround 5.1 Pro Sound Card" %}{: .img-fluid}

#### Audio Outputs of the Creative SB X-Fi Sound Card
The output of the left (*LEFT*) and right (*RIGHT*) front speakers is RCA type, with an RCA-RCA cable used to connect them to the power amplifier (Gainclone) responsible for amplifying these speakers. However, the audio outputs for the left and right side speakers (*REAR*) and the center speaker and subwoofer (*C-SUB*) are stereo P2 outputs. The stereo P2 output (commonly used for headphones, for example) carries the signal of two audio channels in the same connector. Therefore, a P2-RCA cable is needed to connect them to the respective power amplifiers.

#### Audio Input of the Creative SB X-Fi Sound Card
The sound card also has *LINE-IN* and *MIC-IN* audio inputs, making it possible to connect an external audio source to the sound card. This source will be the Gradiente DD-100Q Turntable. Once connected to the sound card, this signal can be processed by the sound server and even be played on the audio outputs.

#### Creative SB X-Fi Sound Card and Raspberry Pi 4
In order to use the sound card to play sounds from different sources, the card was connected (via a USB port) to a Raspberry Pi 4. The card is recognized by the operating system and, then, other devices can play audio on the sound card connected to the Raspberry Pi 4 by connecting via Bluetooth or through the local network (Ethernet or Wi-Fi).

### Sound Server: Raspberry Pi 4
The sound server, implemented using a Raspberry Pi 4, is the component that allows other computers and smartphones to play audio sources in the sound system. For this, we will use the features of **Pulseaudio**!

#### Pulseaudio
Pulseaudio is a "*[multi-platform network sound server, commonly used in Linux and FreeBSD-based systems](https://wiki.archlinux.org/index.php/PulseAudio_(Portugu%C3%AAs)#:~:text=PulseAudio%20%C3%A9%20um%20software%20livre,GNU%20Lesser%20General%20Public%20License.)*". In summary, Pulseaudio allows you to control audio sources, output devices (sound cards or virtual devices), and audio inputs. In our application, we use Pulseaudio on the Raspberry Pi 4 to control the Creative SB X-Fi sound card. Through Pulseaudio, it is possible to play audio sources generated on the system itself (for example, when playing videos on the Raspberry Pi itself) and it is also possible to play audio sources that connect to the Pulseaudio server of the Raspberry Pi 4, allowing audio playback on the Creative SB X-Fi sound card.

This solution was based on an article entitled "*[Multi-room audio over Wi-Fi with PulseAudio and Raspberry Pi(s)](https://partofthething.com/thoughts/multi-room-audio-over-wi-fi-with-pulseaudio-and-raspberry-pis/)*". However, unlike what the article proposes, the developed solution features a single audio server connected to a dedicated sound card.


##### Installing and Configuring Pulseaudio on Raspberry Pi 4
The good (and best) news is that Pulseaudio comes pre-installed and configured as the audio manager in the latest [versions](https://downloads.raspberrypi.org/raspios_full_armhf/release_notes.txt) of [Raspberry Pi OS](https://www.raspberrypi.org/software/operating-systems/) (formerly Raspbian) - the official operating system from the manufacturer. Also based on Raspberry Pi OS, there is also *[Twister OS](https://twisteros.com/about.html)*, which is a more comprehensive operating system for SBCs (Single Board Computers) and specifically for the Raspberry Pi 4. Since I will be using the Raspberry Pi 4 as a media center and for automation in my project, Twister OS was the chosen operating system to develop my applications, also acting as a sound server.

Since Pulseaudio comes pre-installed in the image, it just needs to be configured as the sound server.

###### Installing Volume Controller
To facilitate the operation of Pulseaudio, I recommend using [PulseAudio Volume Control (pavucontrol)](https://freedesktop.org/software/pulseaudio/pavucontrol), which is an application that provides a visual interface for configuring and adjusting volume for Pulseaudio.

    $ sudo apt-get update
    $ sudo apt-get install pavucontrol

Once installed, the PulseAudio Volume Control should create a shortcut in the system menus (in Twister OS version 1.9.6, it's available in *Menu -> Multimedia -> PulseAudio Volume Control*). To run it through the Terminal Emulator:

`$ pavucontrol`

When opening the controller, we'll first check the *Configuration* tab. Once the Creative SB X-Fi card is connected to the Raspberry Pi, it will be displayed on this screen along with other locally available playback interfaces. The two interfaces named *Built-in Audio* (referring to the Raspberry Pi's analog audio and HDMI outputs) have been disabled. For the *SB X-Fi Surround 5.1 Pro* sound card, the *Analog Surround 5.1 Output* profile has been selected to indicate that we will be using the 5.1 sound output from the sound card.

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image13.png alt="Pulseaudio Volume Control - Selecting the 5.1 analog sound output of the sound card" %}{: .img-fluid}

Next, we can check in the *Output Devices* tab the available sound output with the overall volume of the sound card for each channel of the 5.1 system:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image5.png alt="Pulseaudio Volume Control - Viewing the 5.1 analog sound output of the sound card" %}{: .img-fluid}

###### Installing PulseAudio Preferences
To facilitate the operation of Pulseaudio, there is also a graphical interface module that allows adjusting some Pulseaudio settings called [PulseAudio Preferences (paprefs)](https://freedesktop.org/software/pulseaudio/paprefs/). To install it:

`$ sudo apt-get install paprefs`

Just like the *PulseAudio Volume Control*, an icon will be created in the operating system (in the case of Twister OS, in *Menu -> Settings -> PulseAudio Preferences*), but it can also be run through the Terminal Emulator:

    $ paprefs

In the *Network Access* tab, select the option "*Make discoverable PulseAudio network sound devices available locally*".

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image3.png alt="Pulseaudio Preferences - 'Network Access' Tab" %}{: .img-fluid}

Next, in the *Network Server* tab, select the options as shown in the image below to allow the sound server (and the SB X-Fi 5.1 sound card) to be discovered and displayed on other devices:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image1.png alt="Pulseaudio Preferences - 'Network Server' Tab" %}{: .img-fluid}

###### Configuring Audio Input
The audio input (*LINE-IN*) of the SB X-Fi Pro card is connected to the turntable (more specifically, to the phono preamplifier, which is necessary for any turntable). However, the audio input is not automatically connected to the sound output of the sound card. For this, we will need to use a Pulseaudio module called *[loopback](https://www.freedesktop.org/wiki/Software/PulseAudio/Documentation/User/Modules/#module-loopback)*. Pulseaudio also has some text configuration files that define system parameters. To configure the *loopback* module, we will update these files.

These Pulseaudio configuration files are originally located in the `/etc/pulse/` path. However, it is possible to copy these files to the `~/.config/pulse` path, which is a user-specific folder, and edit them (thus keeping the "original" files in the `/etc/pulse/` path). To do this, copy these files to the `~/.config/pulse` path:

    $ mkdir -p ~/.config/pulse
    $ cp /etc/pulse/default.pa ~/.config/pulse
    $ cp /etc/pulse/client.conf ~/.config/pulse
    $ cp /etc/pulse/daemon.conf ~/.config/pulse
    $ cp /etc/pulse/system.pa ~/.config/pulse

These are the text files that define Pulseaudio configurations. After editing them, you can "restart" the Pulseaudio instance with the command:

    $ pulseaudio -k

To include the *loopback* module in Pulseaudio, we will edit the `~/.config/pulse/default.pa` file, adding the following line at the end of the file:

    load-module module-loopback latency_msec=1 source='alsa_input.usb-Creative_Technology_Ltd_SB_X-Fi_Surround_5.1_Pro_00000BFj-00.analog-stereo' sink='alsa_output.usb-Creative_Technology_Ltd_SB_X-Fi_Surround_5.1_Pro_00000BFj-00.analog-stereo.monitor'

After adding the line, saving the file, and restarting Pulseaudio, we can verify the inclusion of the audio input loopback module in the *Playback* tab of the *PulseAudio Volume Control*:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image12.png alt="Pulseaudio Volume Control - Loopback module" %}{: .img-fluid}

###### Other Pulseaudio Parameters

One of the Pulseaudio configuration files is `daemon.conf`. This file contains variables that allow you to configure Pulseaudio. By default, these variables are commented out (indicated by the semicolon before the variable `;`) and have values that are assumed as default. Below is the content of the `daemon.conf` file that I use in my system:

    ; daemonize = no
    ; fail = yes
    ; allow-module-loading = yes
    ; allow-exit = yes
    ; use-pid-file = yes
    ; system-instance = no
    ; local-server-type = user
    ; enable-shm = yes
    ; enable-memfd = yes
    ; shm-size-bytes = 0 # setting this 0 will use the system-default, usually 64 MiB
    ; lock-memory = no
    ; cpu-limit = no

    ; high-priority = yes
    ; nice-level = -11

    ; realtime-scheduling = yes
    ; realtime-priority = 5

    ; exit-idle-time = 20
    ; scache-idle-time = 20

    ; dl-search-path = (depends on architecture)

    ; load-default-script-file = yes
    ; default-script-file = /etc/pulse/default.pa

    ; log-target = auto
    ; log-level = notice
    ; log-meta = no
    ; log-time = no
    ; log-backtrace = 0

    ; resample-method = speex-float-1
    ; avoid-resampling = false
    enable-remixing = no
    ; remixing-use-all-sink-channels = yes
    ; enable-lfe-remixing = no
    ; lfe-crossover-freq = 0

    flat-volumes = no

    ; rlimit-fsize = -1
    ; rlimit-data = -1
    ; rlimit-stack = -1
    ; rlimit-core = -1
    ; rlimit-as = -1
    ; rlimit-rss = -1
    ; rlimit-nproc = -1
    ; rlimit-nofile = 256
    ; rlimit-memlock = -1
    ; rlimit-locks = -1
    ; rlimit-sigpending = -1
    ; rlimit-msgqueue = -1
    ; rlimit-nice = 31
    ; rlimit-rtprio = 9
    ; rlimit-rttime = 200000

    ; default-sample-format = s16le
    ; default-sample-rate = 44100
    ; alternate-sample-rate = 48000
    ; default-sample-channels = 2
    ; default-channel-map = front-left,front-right

    ; default-fragments = 4
    default-fragment-size-msec = 15

    ; enable-deferred-volume = yes
    ; deferred-volume-safety-margin-usec = 8000
    ; deferred-volume-extra-delay-usec = 0

In my case, I only changed the `enable-remixing = no` variable to prevent Pulseaudio from upmixing stereo audio sources to 5.1 (yes, Pulseaudio can transform stereo audio sources into 5.1! But I chose not to do this on the server, as clients can do the same).

### Playing Audio on the Sound Server

We can think of the Raspberry Pi 4 as our "audio playback server", meaning clients connect to it and send their audio streams (via Ethernet or Wi-Fi) to be played on the *Creative Soundblaster X-Fi Surround Pro 5.1* card. The "clients" can be other computers connected to the same network as the *Sound Server*, or even content being played directly on the Raspberry Pi 4 (which would also act, in this case, as a media center).

I'll mention that the clients can be Linux computers (which also have Pulseaudio) and even Microsoft Windows machines!

#### Sending Audio from a Linux PC
To play audio from a PC running a Linux-based operating system, you can use Pulseaudio itself to redirect the machine's sound to the *Sound Server*. As a basis, I'll use Ubuntu 18.04 to illustrate the necessary steps.

##### Configuring Pulseaudio on the Linux PC
Installing and configuring Pulseaudio on the computer that will send the audio to be played on the *Sound Server* is very similar to the configuration we did on the Raspberry Pi 4. Firstly, it's necessary to install Pulseaudio on the operating system, as well as *PulseAudio Volume Control (pavucontrol)* and *PulseAudio Preferences (paprefs)*. For this, on Ubuntu 18.04, you can execute exactly the same commands used to install these applications.

The configuration is even easier. After installing the applications, run the *paprefs* utility. You can run it from the terminal with:

`$ paprefs`

The following image shows the utility screen. In the *Network Access* tab, check the first option as follows:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image3.png alt="Pulseaudio Preferences - 'Network Access' Tab" %}{: .img-fluid}

From this moment on, if the computer is connected to the same network (wired or Wi-Fi) as the *Sound Server*, the sound card will already be identified as an available device to be chosen for remote playback. To check, run *pavucontrol*:

`$ pavucontrol`

The following image shows the *Output Devices* tab of the *PulseAudio Volume Control*:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image17.png alt="Pulseaudio Volume Control - Remote Sound Card ('SB X-Fi Surround 5.1 Pro Analog Surround 5.1 on pi@raspberrypi')" %}{: .img-fluid}

Note that two devices named *SB X-Fi Surround 5.1 Pro Analog Surround 5.1 on pi@raspberrypi* appeared in this tab. These two devices correspond precisely to the sound card of the *Sound Server*. From this screen, you can also control the overall volume of each sound card (local or remote).

**Note**: Next to each audio source, there are three icons. The third icon, with a green "check" sign, allows you to configure that sound card as the *fallback*, a kind of default device for audio playback. Try using the volume control buttons on your computer's keyboard: the volume of the device configured as *fallback* will be changed!

##### (Finally) Playing Audio from PC to Sound Server
Play some media on your computer (a YouTube video, or from the [*LHC Peertube*](https://peertube.lhc.net.br/)). In *pavucontrol*, navigate to the *Playback* tab. The following image shows an audio source being played on the computer and the audio being played on the Sound System:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image2.png alt="Pulseaudio Volume Control - Playing sound on the Remote Sound Card" %}{: .img-fluid}

Note that there is a line showing that a tab in *Google Chrome* is playing audio on the *Sound Server* device. Through this drop-down box, you can select other sound cards (for example, from the computer itself) to play that source.

#### Sending Audio from a Windows PC
For Windows users, it is also possible to play the computer's sound on our Sound Server. For this, you can use *[Scream - Virtual network sound card for Microsoft Windows](https://github.com/duncanthrax/scream)*.
Scream acts as a virtual audio card for Windows, and by selecting this audio card, it is possible to send the computer's sound to the *Sound Server* through an Ethernet or Wi-Fi connection.

##### Setting Up Scream on a Windows PC
To configure the Windows virtual audio card, simply use the installer provided on the *[Releases](https://github.com/duncanthrax/scream/releases/tag/3.6)* page (the current Release version at the time of writing this article is version 3.6). Download the compressed file (.zip), extract it to a folder on Windows. Upon entering the *Install* folder, there will be a file named *Install.bat*. Right-click on this file and select the option *Run as administrator*.

##### Configuring Scream on the Sound Server PC (Raspberry Pi 4)
To use Scream to play audio from a Windows computer on the *Sound Server*, it will also be necessary to configure it on the Raspberry Pi 4 to receive packets via Ethernet or Wi-Fi and play them on the Sound System.

Let's clone the project repository, compile the *Receivers*, and use the specific *Receiver* for Pulseaudio. To do this, access the Raspberry Pi 4 again and in the terminal, execute:

`$ git clone https://github.com/duncanthrax/scream.git`

A folder named *scream* will be created. We still need to check out the latest release version (in this case, version 3.6):

`$ git checkout 3.6`

Next, we need to go to the *Receivers* folder:

`$ cd Receivers/unix`

Now, we need to build the Receivers. Before proceeding, however, we need a Pulseaudio library for this. Install it with the following command:

`$ sudo apt-get install libpulse-dev`

Now, follow these steps to build the applications related to the *Receivers*:

    $ mkdir build && cd build
    $ cmake ..
    $ make

After the process is successfully completed, you will notice that an application named *scream* is created in the folder. This is the application that will be the *Receiver* on the Sound Server. We can run it from the terminal (and the system will be ready to receive sound from Windows), but since we want this configuration to start along with the Raspberry Pi 4, I will configure it to start with the system. There are several ways to do this (such as a system service, for example). For simplicity, I will use a configuration from the operating system (I'm using Twister OS, which is based on Raspberry Pi OS) that allows this configuration using the graphical interface.

###### Initializing the Scream Receiver with the Sound Server
The operating system typically provides a configuration that allows applications to be initialized with the system. In the case of Twister OS, this configuration is in *Settings -> Session and Startup*, as shown in the figure below:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image16.jpg alt="Scream Receiver - Initializing with Twister OS - 1" %}{: .img-fluid}

The following screen will be opened. Navigate to the *Application Autostart* tab and click the *Add* button in the bottom left corner:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image7.jpg alt="Scream Receiver - Initializing with Twister OS - 2" %}{: .img-fluid}

Now, simply fill in a name and a description related to the *Scream Receiver* in the *Name* and *Description* fields, and in the *Command* field insert the command for the application we built earlier. In my system, the full path of the folder that has the application we built is */home/pi/scream/Receivers/unix/build/scream*.

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image14.jpg alt="Scream Receiver - Initializing with Twister OS - 3" %}{: .img-fluid}

Clicking *Close*, the operating system will attempt to always run the *Scream Receiver* along with the system startup.

#### Sending Audio via Bluetooth
The *Raspberry Pi 4* - like the *Raspberry Pi Zero W* and *Raspberry Pi 3/B+* - have native Bluetooth support. Therefore, another way to use our *Sound Server* is to turn it into a *Bluetooth speaker*. Devices like *Smartphones* and *Computers* could then connect to the *Sound Server* via Bluetooth and play the sound directly on the sound system, regardless of whether they are connected via Ethernet or Wi-Fi. This solution also has the advantage of being completely independent of the source device's operating system. To do this, we need to configure this functionality on our *Sound Server*, i.e., directly on the *Raspberry Pi 4*. This solution was based on a post on the official *[Raspberry Pi forum](https://www.raspberrypi.org/forums/viewtopic.php?f=35&t=235519&sid=30fe55fd1499dd4f7baaed9e00227e22)*.

##### Setting Up Bluetooth on the Sound Server
First, it is necessary to check if there are no other system utilities already installed that interact with Bluetooth. If so, you need to configure or disable them to follow the steps in this [tutorial](https://www.raspberrypi.org/forums/viewtopic.php?f=35&t=235519&sid=30fe55fd1499dd4f7baaed9e00227e22).

In *Twister OS*, I disabled all the Bluetooth-related "plugins". By right-clicking on the Bluetooth icon next to the clock and selecting the *Plugins* option:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image9.jpg alt="Sending Audio via Bluetooth - 1" %}{: .img-fluid}

In the window that opens, deactivate all the plugins related to Bluetooth:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image15.jpg alt="Sending Audio via Bluetooth - 2" %}{: .img-fluid}

After deactivating them, simply exit this window. On the same Bluetooth icon, we will now select the *Adapters* option. In the window that opens, select the *Always visible* option so that the Sound Server can always be found as a Bluetooth device:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image18.jpg alt="Sending Audio via Bluetooth - 3" %}{: .img-fluid}

Now, we will configure the Bluetooth of the Raspberry Pi 4 to work as a Bluetooth speaker. First, we need to install a Pulseaudio module that allows it to associate with Bluetooth:
`$ sudo apt-get install pulseaudio-module-bluetooth`

Next, we need to add the user to the *bluetooth* group:
`sudo usermod -a -G bluetooth pi`

Next, we will edit the configuration file for Bluetooth functionality on the Raspberry Pi 4 so that it can be recognized as an audio playback device:

`sudo nano /etc/bluetooth/main.conf`

Edit this file by adding (or uncommenting and adjusting the value) the following lines:

    ...
    Class = 0x41C
    ...
    DiscoverableTimeout = 0
    ...

From this moment on, if we were to restart the Raspberry Pi 4, the service would already be configured. However, we would not be able to connect any Bluetooth devices to the Sound Server since they are "untrusted" devices. Thus, we will install a utility so that devices can connect to the Raspberry Pi 4 "transparently":

`$ sudo apt-get install bluez-tools`

Next, we will create a service so that this utility becomes a system service. To do this, create/edit the following text file:

`$ sudo nano /etc/systemd/system/bt-agent.service`

Adding the following content:

    [Unit]
    Description=Bluetooth Auth Agent
    After=bluetooth.service
    PartOf=bluetooth.service

    [Service]
    Type=simple
    ExecStart=/usr/bin/bt-agent -c NoInputNoOutput

    [Install]
    WantedBy=bluetooth.target

Now, we need this service to start with the Raspberry Pi 4:

`$ sudo systemctl enable bt-agent`

Finally, restart the Raspberry Pi 4! The changes we made should be applied, and when searching for Bluetooth devices on your phones/computers, our *Sound Server* should appear as *raspberrypi*. By connecting to this device, it is possible to play the audio from the devices on the Sound System!

#### General Tips and Observations
With Pulseaudio configured on the Linux and Windows computers (through *Scream*), we can check these sound sources on the Sound Server (Raspberry Pi 4). When running *pavucontrol* on the Raspberry Pi 4, we will have:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image6.png alt="Pulseaudio Volume Control - Sound Sources on the Sound Server" %}{: .img-fluid}

Note in the *Playback* tab that we have 4 audio source entries:

1. **System Sounds**: represents the sound from the Raspberry Pi 4 itself;
2. ***Scream**: Audio*: represents the *Scream Receiver*, i.e., the audio received from the Windows computer;
3. ***pulseaudio**: SB X-Fi Surround 5.1 Pro Analog Surround 5.1 for tiago@tiago-ThinkPad-T430*: which represents the audio coming from the Linux computer (and sends the audio via Pulseaudio to the Sound Server);
4. ***pulseaudio**: SB X-Fi Surround 5.1 Pro Analog Surround 5.1 for tiago@tiago-ThinkPad-T430*: this duplicate entry represents exactly the same entry as the previous one. It appears duplicated because it is automatically "discovered" over the network, and since we have IPv4 and IPv6, both addresses are displayed.

The volume of these entries can be controlled by the source computer and does not need to be changed on the *Sound Server*. For example, on the Linux computer that has *Pulseaudio* and *pavucontrol*, when increasing or decreasing the volume in the *Output Devices* tab, this change will be reflected in the corresponding entry in the *pavucontrol* of the Raspberry Pi 4.

**To control the overall volume of the sound system via the Raspberry Pi 4, use the *Output Devices* tab, where the volume of the sound card *SB X-Fi Surround 5.1 Pro Analog Surround 5.1* will appear as fallback!**

Similarly to the source computer sending sound to the *Sound Server*, it is recommended that the *SB X-Fi Surround 5.1 Pro Analog Surround 5.1* sound card be set as the fallback device on the Raspberry Pi 4:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image4.png alt="Pulseaudio Volume Control - Sound Card as fallback" %}{: .img-fluid}

##### Playing 5.1 Audio on All Speakers of the Sound System
But how can we play sound in 5.1?

Many audio sources provide 5.1 audio. *Netflix*, for example, offers this option for many movies in its catalog. The same can be said for DVDs, YouTube videos, and other files available on the internet. In this case, when playing these sources on computers that send audio to the *Sound Server*, these sources will already be reproduced on all speakers according to the original media mastering.

But what if my audio source is stereo (2.0)? In this case, the choice "lies with the client." Pulseaudio is capable of transforming different audio sources from 5.1 into 5.1 sources. For example, if the audio source is stereo (2.0), it can be encoded into 5.1 to be played on the Sound System. This process is called *upstream* and is enabled by default.
To provide some versatility in the settings, we disabled this functionality on the Raspberry Pi 4 (in the file `~/.config/pulse/daemon.conf`). That is, our server will never do *upstream*. This does not mean that the client cannot do it and send the audio already in 5.1 to the *Sound Server*.

###### Configuring *Upstream* on the Linux Computer
The same Pulseaudio configuration file on the Linux computer that sends audio to the *Sound Server* (`~/.config/pulse/daemon.conf`) contains, in the same section, the following settings:

    ; resample-method = speex-float-1
    ; avoid-resampling = false
    enable-remixing = yes
    ; remixing-use-all-sink-channels = yes
    enable-lfe-remixing = yes
    lfe-crossover-freq = 150

With these settings, the option *enable-remixing = yes* allows Pulseaudio to perform upstreaming of stereo audio sources to be played on all speakers. In addition to this option, I also enabled the option *enable-lfe-remixing = yes* which allows sending audio to the subwoofer and the option *lfe-crossover-freq = 150*, which sets the crossover frequency (in Hz) for the audio sent to the subwoofer.

There are many variables that can be explored with Pulseaudio. The fact is that this system has perfectly met my needs in terms of versatility and quality.

## Farewell (but not just yet!)
With this article, I believe I have covered all the components of my *Sound System*, which I allow myself to call *DIY*. This project that started many years ago is still not 100% finished: there is always something new that makes me think "Why not try this too?".

The idea of this series of articles is to report what (and how) I made my sound system, encouraging other enthusiasts to follow similar paths and, above all, inspiring them to new uses and contributions for those who, like me, believe that you can do something cool and cheap using knowledge that's out there!

Cheers!
