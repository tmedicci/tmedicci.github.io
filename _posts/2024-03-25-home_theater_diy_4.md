---
layout: post
title:  ":portugal: Home Theater DIY (Caixas de Som, Amplificadores e Central de Mídia) - Parte 4"
author: Tiago
date:   2024-03-25 08:00:00 -0300
categories: projetos
tags: projetos audio som eletronica marcenaria
background: '/img/posts/2024-03-25-home_theater_diy_4/montagem_6.jpeg'
---

Home Theater DIY - A Saga das Caixas de Som
===========================================

*The translated version of this article can be accessed [here]({% post_url 2024-03-25-home_theater_diy_4_en %}).*

Caixas de Som, Amplificadores, Central de Mídia e Aprendizagem!

## Amplificadores para o Sistema 5.1 (Gainclones)

Continuando a saga do sistema de som DIY, precisaria agora de ter um sistema de amplificação para as 6 caixas do sistema 5.1, ou seja, para as caixas 1) frontal esquerda; 2) frontal direita; 3) lateral esquerda; 4) lateral direita; 5) frontal e 6) subwoofer (unidade de graves).

### E os Amplificadores PL1050?

Como apresentei no primeiro post, o projeto das caixas frontais começou junto aos amplificadores PL1050: estes amplificadores representam um ótimo custo x benefício, além de ter sido um ótimo projeto de introdução à eletrônica. No entanto, eu só possuía 4 canais de amplificação e, para fazer mais dois canais, não seria viável em termos de espaço. Assim, busquei uma nova solução de amplificação para o sistema que apresentasse, igualmente, um bom custo, ótima qualidade e ocupasse um espaço pequeno. 

### Gainclone

Esta solução de amplificação seria, justamente, o **Gainclone**!

O Gainclone é um amplificador de "baixo custo" que surgiu há alguns anos na internet como um projeto altamente executado por *DIYers* ao redor do mundo. Este amplificador é considerado de alta qualidade (High-End) e é baseado em um componente central que é um *chipamp*, ou seja, um amplificador que é baseado em um chip (e não construído a partir de componentes discretos). 

Um resumo do Gainclone é apresentado no artigo da [Wikipedia](http://en.wikipedia.org/wiki/Gainclone) :wink:

Em relação a custos para montagem deste amplificador, eles estão mais concentrados no chip utilizado (o LM3886) e alguns poucos componentes (dissipadores de calor, transformadores, capacitores etc). Além disso, existe o custo do gabinete e do acabamento para o amplificador. 

#### Clube de Compras para o Gainclone

De forma a diminuir os custos, eu e um amigo organizamos um "clube de compras" de componentes para ganharmos em escala. Reproduzo, agora, o post no HTFORUM que explicou o funcionamento do clube de compras e também apresenta os componentes necessários para montagem do Gainclone (lembrem-se, o post e os preços são de 2013!):

> Galera, atualizo este primeiro post constantemente com o que anda rolando no fórum, as informações resumidas, preços e funcionamento do clube estão sempre explicados aqui.
> 
> Amigos do htforum,
> 
> O motivo deste post é bem simples: sou estudante de eng. elétrica e amante da boa música. Alguns dias atrás um amigo me procurou perguntando sobre algum projeto de um bom amp. Eu já conhecia a fama do Gainclone e o belíssimo trabalho que o senhor Miguel Nabuco havia feito com seu kit de montagem. Infelizmente, não temos mais o kit a disposição...
> 
> No entanto, sempre é bom reviver projetos que fizeram sucesso, e me meti a tentar construir o Gainclone juntamente com amigos. Pedindo autorização para o Miguel Nabuco, o qual me tratou muito bem e se mostrou interessado a nos ajudar, poderíamos então construir um Gainclone. Mas e os componentes?
> 
> Pois bem, vamos ao que interessa: pensei em levantar a questão e ver se mais alguém estaria interessado em montar o Gainclone e, assim, comprar componentes juntos, como um clube de compras para os CIs, transformador toroidal, gabinete, conectores etc... sempre visando conseguir bons preços e, claro, sem lucro algum envolvido.
> 
> Aos que não conhecem: [https://nabucolab.com/2023/11/08/gainclone-lm3886-2008/](https://web.archive.org/web/20240316122054/https://nabucolab.com/2023/11/08/gainclone-lm3886-2008/) (a página que o Nabuco ainda deixou no ar, para apresentar o Gainclone). Temos ainda os esquemáticos e o layout da PCB do mesmo.
> Ainda como referências sobre o Gainclone, leiam [este](/files/2024-03-25-home_theater_diy_4/LM3886-Elektor.pdf) artigo, entitulado "100-Watt single-IC amplifier", publicado na 7ª edição do ano de 1998 da revista Elektor UK.
> 
> O próprio Nabuco nos sugeriu e, cordialmente, se ofereceu para cuidar das PCBs. Caso conseguíssemos bastante pessoas, poderíamos mandar fazer a PCB em indústria, tendo um preço bastante acessível e com qualidade profissional! De antemão, deixo aqui meu agradecimento ao Nabuco, por nos oferecer ajuda neste projeto, ele é uma pessoa magnífica. Mesmo se não conseguirmos baixar muito o preço da placa, podemos cada um confeccionar a sua própria placa e, é claro, nos ajudarmos nas outras etapas da montagem.
> 
> Ainda não temos preços disponíveis, seria legal ter uma base de pessoas para estimar os gastos, mas acredito que, para montarmos um exemplar estéreo de qualidade, devemos gastar por volta 300 reais. Algo que agrade nossos ouvidos, sem fugir tanto de nosso orçamento.
> 
> 
> E aí pessoal, interessados?
> 
> **TABELA DE PREÇOS ATUALIZADA** (confira sempre a nova versão, mais explicações em mensagens na frente):
> 
> - **Transformador toroidal**: o transformador é uma peça cara, talvez a mais cara do projeto, principalmente se fabricado em pequenas quantidades. Ou seja, quanto mais, melhor. O orçamento atual é de uma peça com entrada em 0-127-220V (ou seja, para se usar tanto em 127 ou 220V) e saída com 5 fios (26-22-0-22-26) x 5A (por enrolamento, ou seja, potencia de 220VA ou 260VA). A tensão de +/- 26V foi incluída de modo a obter maior potência em 8Ohms, usa-se +/-26V OU +/-22V no secundário do trafo. É necessário uma única peça para alimentar os dois canais do Gainclone. A peça será encomendada na Hortrafo-Hortec em Hortolândia-SP. As dimensões exatas são 96 x 65mm, pesando 1,9Kg:
> TRANSFORMADOR: 75 reais
> 
> 
> - **Gabinete**: O Gabinete do clube de compras seria o NDT/2 da Naoko. O modelo é capaz de abrigar uma unidade estéreo do Gainclone (dois canais) e é relativamente pequeno. Mais informações do gabinete: [https://www.naokoloja.com.br/MLB-2768590087-gabinete-caixa-metalica-p-montagem-340x80x240-ndt2-_JM#](https://web.archive.org/web/20240316123214/https://www.naokoloja.com.br/MLB-2768590087-gabinete-caixa-metalica-p-montagem-340x80x240-ndt2-_JM) e, para aqueles que desejam dar um bom acabamento à peça, sugiro utilizarem uma chapa de acrílico preto (algo como esta: [https://produto.mercadolivre.com.br/MLB-2745700134-chapa-placa-ps-colorido-tipo-acrilico-30x30-cm-2mm-_JM](https://web.archive.org/web/20240316123713/https://produto.mercadolivre.com.br/MLB-2745700134-chapa-placa-ps-colorido-tipo-acrilico-30x30-cm-2mm-_JM)) na frente do gabinete, com um led azul. Ficaria um visual bem simples e elegante. O acrílico e led não estou inclusos, foram só uma sugestão de acabamento.
> GABINETE: 37 reais
> 
> - **Dissipadores**: conforme dica do Miguel Nabuco, orcei na dissipadores Fenite Dissipadores (modelo utilizado: [FNT-012-AL](/files/2024-03-25-home_theater_diy_4/fnt12al.pdf), cortado com 7cm, ou seja, a base onde se aparafusa o CI seria de 154x70 mm). Cada peça, para um único canal, sairia por R$19,00 (acima de 10 peças). São necessárias duas peças para o Gainclone, uma para cada CI.
> DISSIPADORES: 38 reais
> 
> - **CIs**: O LM3886TF (versão utilizada por ter o caso isolado e não precisar de isolador no disspador) pode ser adquirido na Farnell por R$19,00 + frete. São necessários dois CIs para o Gainclone.
> CIs: 40 reais;
> 
> - **Conectores RCA e bornes**: Os conectores RCAs entraram no clube e serão adquiridos junto ao João Yazbek por 17 reias o par (FOTOS DO CONECTOR RCA), estes são usados pelo João nos produtos de sua empresa. Já em relação aos bornes de saída (necessários 4 peças e dois pares), conforme dica do Gabriel (upking), podemos adquirir junto a Nakamichi [http://www.nakamichiplug.com/product-0520L.html](https://web.archive.org/web/20240316125400/http://www.nakamichiplug.com/product-0520L.html). É a opção com amior custo benefício e, como entregam com frete grátis, é mais interessante mantê-los fora do clube, cada um comprando o seu próprio conjunto de bornes.
> CONECTORES RCAs: 17 reais.
> 
> - **Placas de circuito impresso**: O Miguel Nabuco, gentilmente se responsabilizou em orçar a placa de circuito impresso. A mesma foi alterada de modo a facilitar a aquisição de componentes acessíveis, entre outras modificações de modo a termos mais qualidade, também. Esse orçamento é para placas em fibra de vidro, dupla face, com furos metalizados e acabamento estanhado e furação CNC, cada placa sai por R$14,00 e são necessárias duas placas, uma placa para cada canal. Cada placa possui 10x9 cm, conforme [esta](/img/posts/2024-03-25-home_theater_diy_4/gainclone_miguel_revE.jpg) ilustração (gentilmente cedida pelo próprio Miguel Nabuco):
> PLACAS DE CI: 28 reais.
> 
> - **Capacitores da fonte**: a nova versão da placa do Nabuco conta, cada uma, com 2 capacitores que podem ser de até 10.000uF. Sendo assim, orcei o modelo da EPCOS de 10.00uF x 50V, precisaríamos de quatro unidades para o Gainclone. A unidade sai por R$5,22 na Radio Emegê (atacadista da região da Sta. Efigênia). Achei o preço deles bastante atrativos, mas a quantidade mínima de venda é de 150 reais.
> CAPACITORES: 20,88 reais.
> 
> 
> "**Total**": 254 reais. (no pior caso, estamos estudando métodos de abaixar este valor). Lembrando que este valor corresponde a aproximadamente 90% da unidade pronta e montada! Faltam ainda, não inclusos no clube, os componentes passivos (resistores e capacitores do Gainclone), knobs, cabos, parafusos, solda etc...
> Não estou considerando também os fretes para cada comprador. E também o frete dos produtos, que creio que não serão tão altos. Lembrando que se pode, de acordo com o orçamento, adquirir um ou outro item da lista acima.
> 
> Primeiramente, dúvidas gerais:
>
> **1 - PARTICIPAÇÃO**: Primeiramente, o clube iria se encerrar dia 31 de março, mas como ainda haviam interessados, continuei acrescentando-os na lista. Sendo assim, a lista superou bastante as expectativas. Para facilitar a questão dos fretes até minha residência, resolvi deixar o clube em aberto até, sexta-feita, dia 5 de abril, até às 23:59 com uma condição: o limite máximo de 40 participantes no clube (estamos em 34), isto é devido ao trabalho envolvido. Ou seja, quem me mandar e-mail até esta data ainda pode estar dentro. Outro ponto importante é sobre a resposta aos e-mails. Por favor, aos que já mandaram e-mail mas ainda não responderam com os dados, façam o mais rápido possível. A participação ao clube não está garantida até que os nomes estejam na planilha dos participantes.
> 
> **2 - PRAZOS**: Terminado o prazo acima, terei a quantidade exata dos componentes em mãos e assim saberei o preço dos fretes dos produtos até minha casa. Somente o pagamento garante a encomenda dos componentes e a participação no clube. Faria todas as encomendas dia 15 de abril e, então, aguardaremos todos os componentes chegarem até a mim. Neste momento, com todos os componentes em mãos, aguardaria mais 3 dias úteis para o depósito do frete calculado para a casa de cada participante. Finalmente, faria a seleção, embalagem e envio dos componentes. Todos concordam?
> 
> **3 - COMPONENTES E ALTERAÇÕES**: Caso desejem participar ou desejem alguma alteração nos componentes (aos participantes), me comuniquem via e-mail (meu e-mail está no tópico, só procurar corretamente), para ficarmos mais organizados. As alterações pedidas pelo tópico serão feitas, mas novas alterações, via e-mail. Embalagens serão divididas percentualmente entre os compradores, para cada tipo de componentes. Correto? Especificarei na planilha também.
> 
> 
> Abraços!
  
Este *Clube de Compras* aconteceu em abril de 2013. Assim, o custo estimado à época para montagem de um unidade estéreo (2 canais) foi de, aproximadamente, R$254,00. 
Apesar do trabalho envolvido em organizá-lo, comprar os componentes, separá-los e enviar aos participantes do grupo, foi uma tarefa bastante recompensadora: conheci diversas pessoas interessadas em áudio DIY, pude diminuir bastante os custos envolvidos para a montagem de meus amplificadores e, no fim, até mesmo ganhei alguns presentes e brindes dos participantes do clube de compras (uma espécie de reconhecimento do meu trabalho em organizá-lo).

## Montagem do Gainclone

Por fim, após a organização do Clube de Compras, tive separado minhas unidades para que eu pudesse montar 3 amplificadores estéreo (ou seja, 6 canais!) que usuaria - e ainda uso - no meu sistema 5.1 DIY. 

As imagens a seguir exibem a montagem dos amplificadores. A seguir, está o primeiro teste de montagem do kit de componentes do Gainclone:

{% cloudinary /img/posts/2024-03-25-home_theater_diy_4/montagem_1.jpeg alt="Gainclone - Primeira montagem de teste" %}{: .img-fluid}
É possível notar os dois amplificadores na lateral esquerda inferior e direita superior. As placas de circuito impresso possuem alguns poucos componentes discretos necessários ao *chipamp* e também a fonte para alimentação do circuito (uma para cada amplificador). O *chipamp* LM3886 - "coração do Gainclone" está acoplados aos dissipadores de calor. Falta ainda, no entanto, o transformador toroidal que alimentará os dois circuitos.

A imagem a seguir exibe a montagem finalizada do amplificador, agora já com o transformador toroidal instalado:
{% cloudinary /img/posts/2024-03-25-home_theater_diy_4/montagem_3.jpeg alt="Gainclone - Montagem finalizada" %}{: .img-fluid}

A parte traseira do amplificador é exibida na imagem a seguir:
{% cloudinary /img/posts/2024-03-25-home_theater_diy_4/montagem_4.jpeg alt="Gainclone - Montagem finalizada - parte traseira" %}{: .img-fluid}
Em cima, nas laterais esquerda e direita, temos o par de bornes para ligação das caixas de som. Logo abaixo, temos os conectores RCA da entrada de sinal referente àquele amplificador. Para a entrada de energia foi utilizado um conector IEC 320 C14 (igual ao utilizado em computadores) já com porta-fusível. Por fim, ainda há uma chave seletora de 127-220V.

#### Mas e o Controle de Volume?

O Gainclone pode ser considerado um amplificador de potência, ou seja, ele trata sinais de áudio já pré-amplificados. A instalação de um potenciômetro - componente passivo que permite controlar o volume - apresentaria algumas questões: 
- Eu instalaria um único pot. para ambos os canais? Ou seriam dois potenciômetros?
- Como seria o acabamento do amplificador com o(s) potenciômetro(s)? 
- Qual(is) potenciômetros (lembrando que um componente passivo no "caminho do áudio" sempre é crítico)?

De modo a facilitar a montagem dos amplificadores, diminuir custos e sabendo *a priori* que eu usaria algum tipo de pré-amplificador, optei por não instalar qualquer potenciômetro.

### Acabamento

Como nenhum potenciômetro foi utilizado, o acabamento do amplificador precisaria contar somente com um botão de *liga-desliga*. Para isso, utilizei o botão com um LED iluminado tal como o da imagem a seguir:

{% cloudinary /img/posts/2024-03-25-home_theater_diy_4/push_button_switch.jpeg alt="Botão de liga-desligada" %}{: .img-fluid}

Ainda sobre o acabamento, lembram-se da chapa de poliestireno preto que usei no acabamento das caixas laterais e frontal? Também usei ela para descoração na parte frontal do gabinete do amplificador, trazendo um efeito *black piano* que ficou bastante interessante:
{% cloudinary /img/posts/2024-03-25-home_theater_diy_4/montagem_5.jpeg alt="Gainclone - Montagem finalizada - acabamento" %}{: .img-fluid}

Agora com o amplificador ligado (o LED do botão dá um efeito bem interessante!):
{% cloudinary /img/posts/2024-03-25-home_theater_diy_4/montagem_6.jpeg alt="Gainclone - Montagem finalizada - acabamento" %}{: .img-fluid}

Foram montados três amplificadores Gainclones, totalizando 6 canais! O resultado foi bastante satisfatório (sim, utilizo eles até hoje!): são amplificadores robustos, confiáveis e que apresentam uma ótima resposta. Recomendo a todos que procuram um projeto DIY de amplificadores de alta fidelidade. 

Com este post, eu apresentei (quase) todos os componentes utilizados no meu projeto de som DIY. O próximo - e último - post de apresentação do sistema irá falar sobre a pré-amplificação, seleção da fonte de áudio e possibilidades de ligação e reprodução de diversas fontes no sistema. 

De novo, estou a disposição para quaisquer dúvidas! Vamos conversar sobre?

Abraços!
