---
layout: post
title:  ":portugal: Workshop no LHC: Meshtastic Install Fest"
author: Tiago
date:   2024-12-27 08:00:00 -0300
categories: workshop
tags: workshop, meshtastic, diy, esp32, lora, mesh
background: '/img/posts/2024-12-07-workshop_meshtastic_1/installfestmehstatic3.jpg'
---

Rede de Comunicação LoRa mesh aberta, livre e de baixo custo!
=============================================================

No dia 07 de dezembro de 2024, a partir das 9h, aconteceu o workshop intitulado "*Meshtastic Install Fest - Rede de Comunicação LoRa mesh aberta, livre e de baixo custo!*", no [Laboratório Hacker de Campinas](https://lhc.net.br).

## Preparação

O workshop sobre o *Meshtastic* foi pensado como uma extensão de um artigo introdutório publicado no portal *embarcados.com.br*: [Meshtastic: Rede de Comunicação Mesh LoRa Aberta e Livre](https://embarcados.com.br/meshtastic-rede-de-comunicacao-mesh-lora-aberta-e-livre/):

<iframe src="https://embarcados.com.br/meshtastic-rede-de-comunicacao-mesh-lora-aberta-e-livre/" height="600px" width="100%" frameBorder="0"></iframe>

Eu, Tiago, tenho sido um entusiasta do projeto *Meshtastic*, que permite a comunicação através de uma rede mesh LoRa, off-grid, com dispositivos de baixo-custo baseados no ESP32. Tive, então, a ideia de organizar este workshop no *Laboratório Hacker de Campinas* para apresentar este projeto a outros membros do hackerspace e auxiliá-los na configuração de seus nós.

## Rede Mesh LoRa em Campinas?

Sim, a ideia é que os membros do LHC possam adquirir, configurar e manter seus próprios nós e - associados ao hackerspace - contribuir para o aumento da cobertura da rede *Meshtastic* na região metropolitana de Campinas.

## O Workshop

O evento foi divulgado [aqui](https://eventos.lhc.net.br/event/meshtastic-install-fest-rede-de-comunicacao-lora-mesh-aberta-livre-e-de-baixo-custo).

O workshop focou em apresentar o projeto Meshtastic, elucidando as principais questões associadas à rede mesh LoRa e desmistificando as questões associadas ao uso do projeto, que não é restrito para comunicação, mas também para o envio e recebimento de dados. A seguir, o workshop seguiu para a sessão de *hands-on*, com um passo-a-passo de como configurar um nó *Meshtastic*.

Os slides da apresentação foram disponibilizados online, em [tmedicci.github.io/workshop-meshtastic-lhc](https://tmedicci.github.io/workshop-meshtastic-lhc/):

<iframe src="https://tmedicci.github.io/workshop-meshtastic-lhc/" height="600px" width="100%" frameBorder="0"></iframe>

### Ah, a troca de experiências...

O chamado para o Workshop - assim como para outros eventos do LHC - é público. Ou seja, não somente membros do *Laboratório Hacker de Campinas* são convidados a participar. Pelo contrário, sempre contamos com entusiastas que visitam o LHC e participam dos eventos trazendo, é claro, sua bagagem de conhecimento e experiência para compartilhar. Não foi diferente neste workshop...

Um dos participantes do Workshop nos ajudou a avaliar uma antena Yagi de 4 elementos que havíamos recebido de doação para o nó *Meshtastic* do LHC: o Array de Antenas Yagi (com 4 elementos) para 900MHz, da *ARS*:

 {% pdf "/files/2024-12-07-workshop_meshtastic_1/504230-DIRU-110-28C9.pdf" %}

Com o auxílio de um *NanoVNA*, pudemos realizar as seguintes medições:

{% cloudinary /img/posts/2024-12-07-workshop_meshtastic_1/nanovna.jpeg alt="Medição da Antena YAGI de 4 elementos no NanoVNA" %}{: .img-fluid}

E, a partir destes resultado, pudemos validar a ideia de utilizá-la como antena para o nó *Meshtastic* do LHC, que será instalado acima da nossa sede no bairro da Ponte Preta, em Campinas/SP.

## Fotos

Algumas fotos do workshop:

{% include image-gallery.html folder="/img/posts/2024-12-07-workshop_meshtastic_1" %}

## Próximos Eventos

Para próximos eventos do LHC, fique ligado em [eventos.lhc.net.br](https://eventos.lhc.net.br/)