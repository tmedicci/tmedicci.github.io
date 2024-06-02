---
layout: post
title:  ":portugal: Home Theater DIY (Caixas de Som, Amplificadores e Central de Mídia) - Parte 5"
author: Tiago
date:   2024-06-02 08:00:00 -0300
categories: projetos
tags: projetos audio som eletronica marcenaria
background: '/img/posts/2024-06-02-home_theater_diy_5/capa.png'
---

Home Theater DIY - A Saga das Caixas de Som
===========================================

*The translated version of this article can be accessed [here]({% post_url 2024-06-02-home_theater_diy_5_en %}).*

Caixas de Som, Amplificadores, Central de Mídia e Aprendizagem!

## Servidor de Som e Media Center (Raspberry Pi 4)
O servidor de som é o componente que, de certa forma, liga os demais componentes do sistema: ele faz a comunicação com a mídia sendo reproduzida e a placa de som que, por sua vez, é ligada aos amplificadores. O servidor de som será implementado numa Raspberry Pi 4 que, além desta funcionalidade, serve também de central de mídia do sistema, provendo conteúdo a ser reproduzido em uma tela através da saída HDMI.

### Visão Geral
Eu venho apresentando, desde o início deste tópico, os componentes do sistema. Agora, apresento estes componentes - acrescentando também o servidor de som - em forma de um diagrama de blocos para melhor entendimento:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image10.jpg alt="Visão Geral - Sistema de Som DIY" %}{: .img-fluid}

### Placa de Som: Creative Soundblaster X-Fi Surround 5.1 Pro

A placa de som é dos componentes principais do sistema: a placa da Creative foi escolhida por apresentar excelente desempenho sonoro, ser compatível com sistemas operacionais Linux e Windows e possuir, ainda, um controle de volume através de um botão rotativo na própria placa além de um controle remoto.

A placa de som, como mostra o diagrama, faz o papel de pré-amplificação do sinal de áudio que é enviado aos amplificadores Gainclones através de suas saídas de áudio, tal como na imagem a seguir:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image11.jpg alt="Placa de Som Creative Soundblaster X-Fi Surround 5.1 Pro" %}{: .img-fluid}

#### Saídas de Áudio da Placa de Som Creative SB X-Fi
A saída das caixas frontais esquerda (*LEFT*) e direita (*RIGHT*) são do tipo RCA, sendo que um cabo RCA-RCA é utilizado para ligá-los ao amplificador de potência (Gainclone) responsável pela amplificação destas caixas. As saídas de áudio referentes às caixas laterais esquerda e direita (*REAR*) e à caixa central e unidade de graves (*C-SUB*), no entanto, possuem saídas do tipo P2 estéreo. A saída P2 estéreo (muito comum para fone de ouvidos, por exemplo) possui o sinal de dois canais de áudio no mesmo conector. Desta forma, é necessário um cabo P2-RCA para ligá-las aos respectivos amplificadores de potência.

#### Entrada de áudio da Placa de Som Creative SB X-Fi
A placa de som possui ainda entradas de áudio *LINE-IN* e *MIC-IN*, tornando-se possível a ligação de uma fonte de áudio externo na placa de som. Esta fonte será o Toca Discos DD-100Q da Gradiente. Uma vez conectada à placa de som, este sinal pode ser processado pelo servidor de som e até mesmo ser reproduzido nas saídas de áudio.

#### Placa de Som Creative SB X-Fi e Raspberry Pi 4
De forma a poder utilizar a placa de som para reproduzir sons de distintas fontes, a placa foi conectada (através de uma porta USB) a uma Raspberry Pi 4. A placa é prontamente reconhecida pelo sistema operacional. De modo que outros dispositivos possam executar áudio na placa de som conectada à Raspberry Pi 4 conectando-se via Bluetooth ou através da rede local (ethernet ou Wi-Fi).

### Servidor de Som: Raspberry Pi 4
O servidor de som, implementado através de uma Raspberry Pi 4, é o componente que permite que outros computadores e celulares reproduzam fontes de áudio no sistema de som. Para tal, utilizaremos as funcionalidades do **Pulseaudio**!

#### Pulseaudio
O Pulseaudio é um "*[servidor de som em rede multi-plataforma , comumente usado em sistemas baseados no Linux e FreeBSD](https://wiki.archlinux.org/index.php/PulseAudio_(Portugu%C3%AAs)#:~:text=PulseAudio%20%C3%A9%20um%20software%20livre,GNU%20Lesser%20General%20Public%20License.)*". De forma resumida, o Pulseaudio permite controlar fontes de áudio, dispositivos de saída (placas de som ou dispositivos virtuais) e entradas de áudio. Em nossa aplicação, utilizamos o Pulseaudio na Raspberry Pi 4 para controlar a placa de som Creative SB X-Fi. É possível, através do Pulseaudio, reproduzir fontes de áudio gerados no próprio sistema (por exemplo, ao executarmos vídeos na própria Raspberry Pi) e também é possível reproduzir fontes de áudio que se conectam ao servidor do Pulseaudio da Raspberry Pi 4, possibilitando a reprodução do áudio na placa de som Creative SB X-Fi.

Esta solução foi baseada em um artigo denominado "*[Multi-room audio over Wi-Fi with PulseAudio and Raspberry Pi(s)](https://partofthething.com/thoughts/multi-room-audio-over-wi-fi-with-pulseaudio-and-raspberry-pis/)*". Diferentemente do que propõe o artigo, no entanto, a solução desenvolvida conta com um único servidor de áudio conectado a uma placa de som dedicada.

##### Instalando e Configurando o Pulseaudio na Raspberry Pi 4
A boa (melhor) notícia é que o Pulseaudio já vem instalado e configurado como gerenciador de áudio nas últimas [versões](https://downloads.raspberrypi.org/raspios_full_armhf/release_notes.txt) do [Raspberry Pi OS](https://www.raspberrypi.org/software/operating-systems/) (antigo Raspbian), que é o sistema operacional oficial do fabricante. Também baseado no Raspberry Pi OS, existe também o *[Twister OS](https://twisteros.com/about.html)*, que é um sistema operacional mais completo para SBCs (*Single Board Computers*) e, em específico, para a Raspberry Pi 4. Como em meu projeto utilizarei a Raspberry Pi 4 também como central de mídia e de automação, o Twister OS foi o sistema operacional escolhido para desenvolver minhas aplicações, servindo também como servidor de som.

Uma vez que o Pulseaudio já vem instalado na imagem, basta configurá-lo como servidor de som.

###### Instalando o Controlador de Volume
Para facilitar a operação do Pulseaudio, recomendo utilizar o [PulseAudio Volume Control (pavucontrol)](https://freedesktop.org/software/pulseaudio/pavucontrol/), que é uma aplicação que fornece uma interface visual de configuração e ajuste de volume para o Pulseaudio.

    $ sudo apt-get update
    $ sudo apt-get install pavucontrol

Após instalado, o PulseAudio Volume Control deve criar um atalho nos menus do sistema operacional (no Twister OS versão 1.9.6, está disponível em *Menu -> Multimedia -> PulseAudio Volume Control*). Para executá-lo através do *Terminal Emulator*:

`$ pavucontrol`

Ao abrir o controlador, iremos primeiramente verificar a aba *Configuration*. Uma vez que a placa Creative SB X-Fi estiver conectada à Raspberry Pi, ela será exibida nesta tela juntamente com as outras interfaces de reprodução disponível localmente. As duas interfaces denominadas *Built-in Audio* (referentes às saídas de áudio analógico e do HDMI da Raspberry Pi) foram desligadas. Para a placa de som *SB X-Fi Surround 5.1 Pro*, foi selecionado o Profile *Analog Surround 5.1 Output* para indicar que utilizaremos a saída de som 5.1 da placa de som.

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image13.png alt="Pulseaudio Volume Control - Seleção da saída de som analógica 5.1 da placa de som" %}{: .img-fluid}

A seguir, podemos verificar na aba *Output Devices* a saída de som disponível com o volume geral da placa de som para cada canal do sistema 5.1:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image5.png alt="Pulseaudio Volume Control - Visualização da saída de som analógica 5.1 da placa de som" %}{: .img-fluid}

###### Instalando o PulseAudio Preferences
Para facilitar a operação do Pulseaudio existe um módulo também em interface gŕafica que permite ajustar algumas configurações do Pulseaudio chamado [PulseAudio Preferences (paprefs)](https://freedesktop.org/software/pulseaudio/paprefs/). Para instalá-lo:

`$ sudo apt-get install paprefs`

Tal como o *PulseAudio Volume Control*, um ícone será criado no sistema operacional (no caso do Twister OS, em *Menu -> Settings -> PulseAudio Preferences*), mas que também pode ser executado pelo *Terminal Emulator*:

    $ paprefs

Na aba *Network Access*, selecione a opção "*Make discoverable PulseAudio network sound devices available locally*".

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image3.png alt="Pulseaudio Preferences - Aba 'Network Access'" %}{: .img-fluid}

A seguir, na aba *Network Server*, selecione as opções conforme imagem a seguir para permitir que o servidor de som (e a placa de som SB X-Fi 5.1) possam ser descobertos e exibidos em outros dispositivos:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image1.png alt="Pulseaudio Preferences - Aba 'Network Server'" %}{: .img-fluid}

###### Configurando a Entrada de Áudio
A entrada de áudio (*LINE-IN*) da placa SB X-Fi Pro é ligada ao toca discos (mais especificamente, ao pré-amplificador de phono, que é um equipamento necessário para qualquer toca-disco). No entanto, a entrada de áudio não é automaticamente ligado à saída de áudio da placa de som. Para tal, precisaremos utilizar um módulo do Pulseaudio chamado *[loopback](https://www.freedesktop.org/wiki/Software/PulseAudio/Documentation/User/Modules/#module-loopback)*. O Pulseaudio possui também alguns arquivos de configuração em formato de texto que definem os parâmetros do sistema. Para configurar o módulo *loopback*, atualizaremos estes arquivos.

Estes arquivos de configuração do Pulseaudio estão, originalmente, na pasta `/etc/pulse/`. No entanto, é possível copiar estes arquivos para a pasta `~/.config/pulse`, que é uma pasta específica ao usuário, e alterá-los (e, assim, manter os arquivos "originais" na pasta `/etc/pulse/`). Para tal, copie estes arquivos para a pasta `~/.config/pulse`:

    $ mkdir -p ~/.config/pulse
    $ cp /etc/pulse/default.pa ~/.config/pulse
    $ cp /etc/pulse/client.conf ~/.config/pulse
    $ cp /etc/pulse/daemon.conf ~/.config/pulse
    $ cp /etc/pulse/system.pa ~/.config/pulse

Estes são os arquivos de texto que definem configurações do Pulseaudio. Após editá-los, é possível "reiniciar" a instância do Pulseaudio com o comando:

    $ pulseaudio -k

Para incluir o módulo *loopback* no Pulseaudio, editaremos o arquivo `~/.config/pulse/default.pa`, incluindo a seguinte linha ao final do arquivo:

    load-module module-loopback latency_msec=1 source='alsa_input.usb-Creative_Technology_Ltd_SB_X-Fi_Surround_5.1_Pro_00000BFj-00.analog-stereo' sink='alsa_output.usb-Creative_Technology_Ltd_SB_X-Fi_Surround_5.1_Pro_00000BFj-00.analog-stereo.monitor'

Após a inclusão da linha, salvamento do arquivo e reinício do Pulseaudio, podemos verificar a inclusão do módulo loopback da entrada de áudio na aba *Playback* do *PulseAudio Volume Controle*:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image12.png alt="Pulseaudio Volume Control - Módulo loopback" %}{: .img-fluid}

###### Outros parâmetros do Pulseaudio

Um dos arquivos de configuração do Pulseaudio é o `daemon.conf`. Este arquivo possui variáveis que permitem configurar o Pulseaudio. Por padrão, estas variáveis estão comentadas (indicado pelo ponto-vírgula antes da variável `;`) e com os valores que são assumidos como padrão. A seguir, o conteúdo do arquivo `daemon.conf` que utilizo em meu sistema:

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

No meu caso, só alterei a variável `enable-remixing = no` para impedir que o Pulseaudio faça o *upmix* das fontes de áudio em 5.1 (sim, o Pulseaudio consegue transformar fontes de áudio estéreo em 5.1! Mas escolhi não fazer isso no servidor, uma vez que os clientes podem fazer o mesmo).

### Reproduzindo Áudio no Servidor de Som

Podemos entender a Raspberry Pi 4 como nosso "servidor de reprodução de áudio", ou seja, clientes se conectam nele e enviam seu áudio (pela ethernet ou WI-Fi) para ser reproduzido na placa *Creative Soundblaster X-Fi Surround Pro 5.1*. Os "clientes" podem ser outros computadores conectados à mesma rede do *Servidor de Som* ou, até mesmo, conteúdo que está sendo reproduzido diretamente na Raspberry Pi 4 (que também atuaria, nesse caso, como central de mídia).

Já adianto que os clientes podem ser computadores Linux (que também contará com o Pulseaudio) e, até mesmo, computadores com Windows!

#### Enviando Áudio de um PC com Linux
Para reproduzir o áudio de um PC que roda um sistema operacional baseado no Linux, é possível utilizar o próprio *Pulseaudio* para redirecionar o som da máquina para o *Servidor de Som*. Como base, utilizarei o Ubuntu 18.04 para ilustrar as etapas necessárias.

##### Configurando o Pulseaudio no PC Linux
A instalação e configuração do Pulseaudio no computador que enviará o áudio a ser reproduzido no *Servidor de Som* é muito semelhante à configuração que realizamos na Raspberry Pi 4. Primeiramente, é necesário instalar o *Pulseaudio* no sistema operacional, bem como o *PulseAudio Volume Control (pavucontrol)* e o *PulseAudio Preferences (paprefs)*. Para tal, no Ubuntu 18.04 é possível executar exatamente os mesmos comandos executados para instalar estas aplicações.

A configuração é ainda mais fácil. Após a instalação das aplicações, execute o utilitário *paprefs*. Pela terminal, é possível executá-lo com:

`$ paprefs`

A imagem a seguir exibe a tela do utilitário. Na aba *Network Access*, marque a primeira opção conforme a seguir:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image3.png alt="Pulseaudio Preferences - Aba 'Network Access'" %}{: .img-fluid}

A partir desse momento, se o computador estiver ligado à mesma rede (cabeada ou Wi-Fi) do *Servidor de Som*, a placa de som já será identificada como um dispositivo disponível a ser escolhido para reprodução remota. Para verificar, execute o *pavucontrol*:

`$ pavucontrol`

A imagem a seguir exibe a aba *Output Devices* do *PulseAudio Volume Control*:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image17.png alt="Pulseaudio Volume Control - Placa de Som Remota ('SB X-Fi Surround 5.1 Pro Analog Surround 5.1 on pi@raspberrypi')" %}{: .img-fluid}

Observe que dois dispositivos denominados *SB X-Fi Surround 5.1 Pro Analog Surround 5.1 on pi@raspberrypi* apareceram nesta aba. Estes dois dispositivos correspondem, justamente, à placa de som do *Servidor de Som*. Por esta tela é possível controlar também o volume geral de cada placa de som (local ou remota).

**Observação**: ao lado de cada fonte de áudio há três ícones. O terceiro ícone, com um
 sinal de "correto" em cor verde, permite configurar aquela placa de som como *fallback*, uma espécie de dispositivo padrão para reprodução de áudio. Experimente utilizar os botões de controle de volume do teclado de seu computador: o volume do dispositivo configurado como *fallback* será alterado!

##### (Finalmente) Reproduzindo o Áudio do PC no Servidor de Som
Execute alguma mídia em seu computador (um vídeo do *Youtube*, ou do [*Peertube do LHC*](https://peertube.lhc.net.br/). Ainda no *pavucontrol*, vá na aba *Playback*. A imagem a seguir exibe uma fonte de áudio sendo reproduzida no computador e o áudio sendo reproduzido no Sistema de Som:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image2.png alt="Pulseaudio Volume Control - Reproduzindo som na Placa de Som remota" %}{: .img-fluid}

Observe que existe uma linha mostrando que uma aba do *Google Chrome* está reproduzindo áudio no dispositivo do *Servidor de Som*. Através desta caixa de seleção, é possível selecionar outras placas de som (por exemplo, do próprio computador) para reproduzir aquela fonte.

#### Enviando Áudio de um PC com Windows
Para os usuários de Windows, também é possível reproduzir o som do computador em nosso servidor de Som. Para tal, é possível usar o *[Scream - Virtual network sound card for Microsoft Windows](https://github.com/duncanthrax/scream)*.
O Scream atua como uma placa de áudio virtual para o Windows e, ao selecionar esta placa de áudio, é possível enviar o som do computador ao *Servidor de Som* através de uma conexão ethernet ou Wi-Fi.

##### Configurando o Scream no PC Windows
Para configurar a placa de áudio virtual do Windows, basta utilizar o instalador fornecido na página de *[Releases](https://github.com/duncanthrax/scream/releases/tag/3.6)* (a versão de Relase atual, no momento da escrita deste artigo é a versão 3.6). Baixe o arquivo compactado (.zip), descompacte-o em alguma pasta do Windows. Ao entrar na pasta *Install*, haverá um arquivo denominado *Install.bat*. Clique com o botão direito do mouse sobre este arquivo e selecione a opção *Executar como administrador*.

##### Configurando o Scream no PC no Servidor de Som (Raspberry Pi 4)
Para utilizar o Scream para reproduzir o som de um computador Windows no *Servidor de Som* será necessário também configurá-lo na Raspberry Pi 4 para receber os pacotes via ethernet ou Wi-Fi e reproduzi-los no Sistema de Som.

Vamos, então, precisar clonar o repisitório do projeto, compilar os *Receivers* e utilizar o *Receiver* específico para o *Pulseaudio*. Para tal, acesse novamente a Raspberry Pi 4 em, no terminal, execute:

`$ git clone https://github.com/duncanthrax/scream.git`

Uma pasta será chamada *scream* será criada. Precisaremos, ainda, dar um checkout para a última versão de release (no caso, é a 3.6):

`$ git checkout 3.6`

A seguir, precisaremos ir à pasta dos *Receivers*:

`$ cd Receivers/unix`

Agora, precisaremos buildar os Receivers. Antes de prosseguir, no entanto, precisaremos de uma biblioteca do Pulseaudio para tal. Instale-a com o seguinte comando:

`$ sudo apt-get install libpulse-dev`

Agora, siga os seguintes passos para buildar as aplicações referentes aos *Receivers*:

    $ mkdir build && cd build
    $ cmake ..
    $ make

Após o proceso ser finalizado com sucesso, observe que uma aplicação denominada *scream* será criada na pasta. É esta a aplicação que será o *Receiver* no Servidor de Som. Podemos executá-la pelo terminal (e o sistema já vai estar apto a receber o som do Windows), mas como gostaríamos que esta configuração seja iniciada juntamente à Raspberry Pi 4, irei configurá-la para ser iniciada junto ao sistema. Há diversas formas de se fazer isso (como um serviço do sistema, por exemplo). Por simplicidade, utilizarei uma configuração do sistema operacional (estou utilizando o Twister OS, que é baseado no Raspberry Pi OS) que permite fazer esta configuração utilizando a interface gráfica.

###### Inicializando o Receiver Scream junto com o Servidor de Som
O sistema operacional apresenta, normalmente, uma configuração que permite que aplicações sejam incializadas junto ao sistema. No caso do Twister OS, esta configuração está em *Settings -> Session and Startup*, conforme a figura a seguir:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image16.jpg alt="Receiver Scream - Inicializando junto com Twister OS - 1" %}{: .img-fluid}

A seguinte tela será aberta. Navegue até a aba *Application Autostart* e clique no botão *Add*, no canto inferior esquerdo:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image7.jpg alt="Receiver Scream - Inicializando junto com Twister OS - 2" %}{: .img-fluid}

Agora, basta preencher um nome e uma descrição relacionada ao *Receiver Scream* nos campos *Name* e *Description* e, no campo *Command* inserir o comando para a aplicação que buildamos anteriormente. Em meu sistema, o caminho completo da pasta que tem aplicação que buildamos é */home/pi/scream/Receivers/unix/build/scream*.

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image14.jpg alt="Receiver Scream - Inicializando junto com Twister OS - 3" %}{: .img-fluid}

Clicando-se em *Close*, o sistema operacional tentará sempre executar o *Receiver Scream* junto à inicialização do sistema.

#### Enviando Áudio via Bluetooth
A *Raspberry Pi 4* - assim como a *Raspberry Pi Zero W* e *Raspberry Pi 3/B+* - possuem suporte nativo ao Bluetooth. Desta forma, outra forma de utilização de nosso *Servidor de Som* é transformá-lo em uma *caixa de som Bluetooth*. Dispositivos como *Smartphones* e *Computadores* poderiam, então, se conectar ao *Servidor de Som* por Bluetooth e reproduzir o som diretamente no sistema de som, independente de estarem conecatados por ethernet ou Wi-Fi. Esta solução também apresenta a vantagem de ser totalmente independente em relação ao sistema operacional da fonte de áudio. Para tal, precisaremos configurar esta funcionalidade em nosso *Servidor de Som*, ou seja, diretamente na *Raspberry Pi 4*. Esta solução foi baseada em um post no fórum oficial da *[Raspberry Pi](https://www.raspberrypi.org/forums/viewtopic.php?f=35&t=235519&sid=30fe55fd1499dd4f7baaed9e00227e22)*.

##### Configurando o Bluetooth no Servidor de Som
Primeiramente, é necessário verificar se não há outros utilitários do sistema operacional já instalados que interagem com o Bluetooth. Em caso positivo, é necessário configurá-los ou desativá-los para seguir as etapas presentes neste [tutorial](https://www.raspberrypi.org/forums/viewtopic.php?f=35&t=235519&sid=30fe55fd1499dd4f7baaed9e00227e22).

No *Twister OS*, eu desabilitei todos os "plugins" relacionados ao Bluetooth. Clicando-se com o botão direito do mouse no ícone do Bluetooth próximo ao relógio e selecionando, a seguir, a opção *Plugins*:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image9.jpg alt="Enviando Áudio via Bluetooth - 1" %}{: .img-fluid}

Na tela que se abre, desative todos os plugins relacionados ao Bluetooth:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image15.jpg alt="Enviando Áudio via Bluetooth - 2" %}{: .img-fluid}

Após desativá-los, basta sair desta tela. Ainda no mesmo ícone do Bluetooth, selecionaremos agora a opção *Adapters*. Na tela que se abre, selecionamos a opção *Always visible* para que o Servidor de Som possa sempre ser encontrado como um dispositivo bluetooth:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image18.jpg alt="Enviando Áudio via Bluetooth - 3" %}{: .img-fluid}

Agora, iremos configurar o Bluetooth da Raspberry Pi 4 para funcionar como uma caixa de som Bluetooth. Primeiramente, precisamos instalar um módulo do Pulseaudio que o permite associar-se ao Bluetooth:
`$ sudo apt-get install pulseaudio-module-bluetooth`

A seguir, precisamos adicionar o usuário ao grupo *bluetooth*:
`sudo usermod -a -G bluetooth pi`

A seguir, editaremos o arquivo de configuração da funcionalidade do Bluetooth na Raspberry Pi 4 para que ela possa ser reconhecida como um dispositivo de reprodução de áudio:

`sudo nano /etc/bluetooth/main.conf`

Edite este arquivo acrescentando (ou descomentando e ajustando o valor) as seguintes linhas:

    ...
    Class = 0x41C
    ...
    DiscoverableTimeout = 0
    ...

A partir deste momento, se reiniciássemos a Raspberry Pi 4, já teríamos o serviço configurado. No entanto, não conseguiríamos conectar qualquer dispositivo Bluetooth ao Servidor de Som, uma vez que são dispositivos "não confiáveis". Assim, instalaremos um utilitário para que os dispositivos consigam se conectar de forma "transparente" à Raspberry Pi 4:

`$ sudo apt-get install bluez-tools`

Criaremos, a seguir, um serviço para que este utilitário torne-se um serviço do sistema. Para tal, crie/edite o seguinte arquivo de texto:

`$ sudo nano /etc/systemd/system/bt-agent.service`

Adicionando o seguinte conteúdo:

    [Unit]
    Description=Bluetooth Auth Agent
    After=bluetooth.service
    PartOf=bluetooth.service

    [Service]
    Type=simple
    ExecStart=/usr/bin/bt-agent -c NoInputNoOutput

    [Install]
    WantedBy=bluetooth.target

Agora, precisaremos que este serviço incie junto à Raspberry Pi 4:

`$ sudo systemctl enable bt-agent`

Por fim, reinicie a Raspberry Pi 4! As alterações que fizemos deverão ser aplicadas e, ao procurar por dispositivos Bluetooth em seus celulares/computadores, nosso *Servidor de Som* deverá aparecer como *raspberrypi*. Conectando-se a este dispositivo, é possível reproduzir o áudio dos dispositivos no Sistema de Som!

#### Dicas e Observações Gerais
Configurados o Pulseaudio no computador Linux e no Windows (através do *Scream*), podemos verificar estas fontes de som no Servidor de Som (Raspberry Pi 4). Ao executar o *pavucontrol* na Raspberry Pi 4, teremos:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image6.png alt="Pulseaudio Volume Control - Fontes de Som do Servidor de Som" %}{: .img-fluid}

Observe na aba *Playback* que temos 4 entradas de fontes de áudio:

1. **System Sounds**: representa o som da própria Raspberry Pi 4;
2. ***Scream**: Audio*: representa o *Receiver do Scream*, ou seja, o áudio recebido do computador que executa o Windows;
3. ***pulseaudio**: SB X-Fi Surround 5.1 Pro Analog Surround 5.1 for tiago@tiago-ThinkPad-T430*: que representa o áudio vindo do computador que executa o Linux (e envia o áudio pelo Pulseaudio ao Servidor de Som);
4. ***pulseaudio**: SB X-Fi Surround 5.1 Pro Analog Surround 5.1 for tiago@tiago-ThinkPad-T430*: esta entrada duplicada representa exatamente a mesma entrada anterior. Ela aparece duplicada pois é "descoberta" automaticamente pela rede e, como temos IPv4 e IPv6, ambos os endereços são exibidos.

O volume destas entradas podem ser controlados pelo computador de origem e não precisa ser alterado no *Servidor de Som*. Por exemplo, no computador Linux que possui o *Pulseaudio* e o *pavucontrol*, ao aumentar ou diminuir o volume na aba *Output Devices*, esta alteração será refletida na entrada correspondente no *pavucontrol* da Raspberry Pi 4.

**Para controlar o volume geral do sistema de som pela Raspberry Pi 4, utilize a aba *Output Devices*, onde aparecerá o volume da placa de som *SB X-Fi Surround 5.1 Pro Analog Surround 5.1***!

Da mesma forma que no computador que envia o som ao *Servidor de Som*, recomenda-se que a placa de som  *SB X-Fi Surround 5.1 Pro Analog Surround 5.1* seja o dispositivo configurado como *fallback* na Raspberry Pi 4:

{% cloudinary /img/posts/2024-06-02-home_theater_diy_5/image4.png alt="Pulseaudio Volume Control - Placa de Som como fallback" %}{: .img-fluid}

##### Reprodução de Áudio 5.1 em Todas as Caixas do Sistema de Som
Mas afinal, como reproduzir o som em 5.1?

Muitas fontes de áudio fornecem áudio em 5.1. O *Netflix*, por exemplo possui esta opção para muitos dos filmes em catálogo. O mesmo se pode dizer de DVDs, vídeos do *Youtube* e outros arquivos disponibilizados na internet. Neste caso, ao reproduzir estas fontes em computadores que enviam o áudio para o *Servidor de Som*, estas fontes já serão repoduzidas em todas as caixas de som de acordo com a masterização original da mídia.

Mas e se a minha fonte de áudio for estéro (2.0)? Neste caso, a escolha "fica a cargo do cliente". O Pulseaudio é capaz de transformar fontes de áudio diferentes de 5.1 em fontes 5.1. Por exemplo, se a fonte de áudio é estéreo (2.0), ela pode ser codificada em 5.1 para ser reproduzida no Sistema de Som. Este processo se chama *upstream* e é habilitado por padrão.
De modo a prover certa versatilidade nas configurações, nós desabilitamos esta funcionalide na Raspberry Pi 4 (no arquivo `~/.config/pulse/daemon.conf`). Ou seja, nosso servidor nunca fará *upstream*. Isto não quer dizer que o cliente não possa fazer e mandar o áudio já em 5.1 para o *Servidor de Som*.

###### Configurando o *Upstream* no Computador Linux
O mesmo arquivo de configuração do Pulseaudio no computador Linux que envia o áudio ao *Servidor de Som* (`~/.config/pulse/daemon.conf`) contém, no mesmo trecho, as seguintes configurações:

    ; resample-method = speex-float-1
    ; avoid-resampling = false
    enable-remixing = yes
    ; remixing-use-all-sink-channels = yes
    enable-lfe-remixing = yes
    lfe-crossover-freq = 150

Com estas configurações, a opção *enable-remixing = yes* permite o Pulseaudio a fazer o upstream de fontes de áudio estéreo para ser reproduzida em todas as caixas de som. Além desta opção, habilitei também a opção *enable-lfe-remixing = yes* que permite enviar o áudio à unidade de graves (subwoofer) e a opção *lfe-crossover-freq = 150*, que fixa a frequência de corte (em Hz) para o áudio enviado à unidade de graves.

São muitas as variáveis que podem ser exploradas com o Pulseaudio. Fato é que este sistema temme atendido perfeitamente em termos de versatiidade e qualidade.

## Despedida (mas não só!)
Com este artigo, acredito que passei por todos os componentes de meu *Sistema de Som*, que me permito chamar de *DIY*. Este projeto que começou há muitos anos ainda não está 100% finalizado: sempre existe algo novo que me permite pensar "Por que não tentar isso também?".

A ideia desta série de artigos é relatar o que (e como) fiz meu sistema de som, encorajando outros entusiastas a seguirem caminhos semelhantes e, principalmente, inspirá-los a novos usos e contribuições para quem, assim como eu, acredita que dá pra fazer algo legal e barato utilizando um conhecimento que está aí!

Abraços!
