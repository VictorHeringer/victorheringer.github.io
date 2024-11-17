---
title: "Parte 1 - Criando seu próprio Tamagotchi"
draft: true
date: 2024-02-10T20:28:00-03:00
categories:
- Development
tags:
- Gameboy
- C
- Game Dev
- Arduino
# bookComments: false
# bookSearchExclude: false
---

Desde sua criação no final da década de 1990, o Tamagotchi capturou a imaginação de crianças e adultos em todo o mundo, tornando-se um fenômeno cultural e um ícone da cultura pop. O Tamagotchi, um dispositivo eletrônico portátil desenvolvido pela empresa japonesa Bandai, introduziu uma nova forma de interação digital, permitindo que os usuários cuidassem de uma criatura virtual, desde alimentação até higiene e entretenimento, em uma pequena tela LCD.

No Brasil, o Tamagotchi conquistou uma popularidade excepcional, tornando-se um fenômeno cultural por si só. No entanto, uma variante particular do Tamagotchi ganhou destaque e se tornou uma das mais populares no país: o Haku Haku Dinokun. Esta versão do dispositivo, embora seja um clone do Tamagotchi original, teve uma presença significativa no mercado brasileiro. Os Dinokuns originais, encontrados em cores como azul-turquesa, amarelo, vermelho e branco, foram alvo de uma série de clones, que se destacaram por sua variedade de cores e algumas diferenças de design.

Muitos clones dos próprios clones foram fabricados, ampliando a disponibilidade do produto no mercado. Estes clones, muitas vezes referidos como "Falso Dinokun", apresentavam variações em relação ao modelo original, como telas menores e diferenças na disposição dos botões e na apresentação da marca. Apesar dessas variações, o Haku Haku Dinokun e seus clones desempenharam um papel significativo na cultura do entretenimento eletrônico no Brasil, cativando uma ampla audiência e deixando sua marca na memória coletiva do país.

| Tamagotchi  | Haku Haku Dinokun | Cópia do Dinokun |
|---|---|---|
| ![tamagochi](../images/tamagotchi.png) |  ![haku haku dinokun](../images/hakuhaku.png) | ![dino kun cloke](../images/hakuhaku-clone.png) |

Hoje em dia adquirir um pequeno pedaço dessa nostalgia está salgado. Para quem deseja relembrar como é ter o Dinokun da época, pode ter que chegar a pagar até 600 reais. 
Então para quem tiver interesse em como fazer o seu próprio
Rakuraku Dinokun e aprender um pouco mais sobre Arduído, seguirá uma série de posts de como criar o seu do zero, tentando oferecer uma experiência próxima do que foi o 
brinquedo original, quer dizer, no caso a cópia original.

## Funcionalidades

Existe pouquíssima informação sobre o Dinokun, principalmente sobre a implementação das suas funcionalidades. Ao procurar por um `disassembly`, código fonte, ou
qualquer outro tipo de coisa que nos guie para uma implementação fiel à original, acabei não encontrando nada relevante, infelizmente parece que nosso amigo
"paralelo" não recebeu tanto amor quando o produto original, que possui [apresentações](https://www.youtube.com/watch?v=c4PkcZScBV8) e [projetos](https://github.com/anabolyc/Tamagotchi) incríveis que mostram a fundo as entranahas desses bixinhos virtuais. Para nosso projeto em específico, como existe uma certa barreira para um disassembly, como
preço alto do hardware, falta de ferramentas além do fato que, eu tenho zero experiência com esse tipo de coisa, o caminho aqui será tentar "a partir das regras de negócios"
que pudermos inferir, criamos essa experiência.