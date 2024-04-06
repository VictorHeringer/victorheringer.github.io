---
title: "O que significa 2 bits por pixel?"
date: 2024-02-10T20:28:00-03:00
categories:
- Development
tags:
- Gameboy
- C
- Gamed Dev
# bookComments: false
# bookSearchExclude: false
---

Se você tem interesse em interfaces, compreender como funcionam gráficos de jogos em videogames antigos pode ser útil para reduzir a abstração que às vezes parece mágica nas engines de renderização modernas. Tais conceitos ajudam a formar uma base para entender diversas ferramentas, desde o desenvolvimento de jogos até navegadores.

O texto a seguir te guiará pelos fascinantes detalhes do formato gráfico 2BPP do Nintendo Gameboy e mergulhará na eficiente paleta de quatro cores utilizada por esse console clássico. Este artigo, explora a representação gráfica do Gameboy, destacando como o formato 2BPP, também conhecido como "2 bits por pixel", oferece uma maneira direta e eficaz de armazenar dados visuais. A análise investiga o uso de tiles de 8x8 pixels, revelando a precisa codificação de cada pixel em dois bits.

## O Game boy

Se nos últimos 34 anos, você esteve em um planeta distante e não conhece esse hardware, permita-me apresentar o Game Boy, um ícone eterno no universo dos videogames, cuja estreia revolucionária ocorreu em 21 de abril de 1989, pelas mãos talentosas de Gunpei Yokoi, uma lenda na Nintendo.

Este console portátil não apenas redefiniu a experiência de jogos, mas também moldou toda a cultura dos videogames. Com uma tela monocromática capaz de exibir apenas 4 cores, seu design compacto e dimensões modestas proporcionaram uma mobilidade sem precedentes. Além disso, equipado com um processador de 8 bits operando a notáveis 4.194304 MHz, o Game Boy oferecia um desempenho surpreendente para a época. Com uma quantidade modesta, mas eficaz, de 8 KB de memória RAM, permitiu que os jogadores levassem consigo seus títulos favoritos para qualquer lugar.

Vale destacar que o tamanho da ROM poderia variar, devido à utilização de uma técnica de *memory map*, ficando em torno de 32 KB até 2 MB, mais comumente.

Você não leu errado: eram apenas 8 KB de RAM para o sistema (e mais 8 KB de memória de vídeo). Essa quantidade tão limitada de memória torna-se evidente quando consideramos o contexto atual, onde frameworks modernos, como o React, demandam uma capacidade considerável. Se dependêssemos de um framework como o React para renderizar a tela, seria possível carregar para a RAM apenas 1/4 dos 32 KB que a biblioteca possui.

Caso seja usuário do Preact em vez do React, você conseguiria carregar todos os seus 4 KB para a RAM e ainda te sobrariam mais 4 KB! Já pensou que interessante um framework declarativo para construção de interface rodando em um hardware de 1989? Quem sabe um dia a gente tenta uma loucura dessas.

## Hexdecimais & Binários

Antes de prosseguir, vale uma rápida recapitulação sobre números hexadecimais.

Os números hexadecimais são uma notação numérica que utiliza a base 16, em contraste com a base 10 que usamos no sistema decimal comum. A base 16 significa que há 16 símbolos diferentes para representar valores, indo de 0 a 9 e de A a F. No sistema hexadecimal, os dígitos A a F representam valores de 10 a 15.

Aqui está a sequência completa de dígitos hexadecimais: 0, 1, 2, 3, 4, 5, 6, 7, 8, 9, A, B, C, D, E, F. Cada dígito hexadecimal representa quatro bits (ou meio byte) de informação.

Os números binários, por outro lado, são representados na base 2, utilizando apenas dois dígitos: 0 e 1. Cada dígito binário representa um bit de informação, sendo a unidade básica de dados em sistemas computacionais.

A relação entre hexadecimais e binários está na maneira como os computadores armazenam e manipulam informações. Como o sistema binário pode ser extenso e difícil de ler, o sistema hexadecimal é frequentemente utilizado para simplificar a representação de sequências binárias. Cada dígito hexadecimal equivale a quatro dígitos binários, facilitando a leitura e escrita de informações em ambientes computacionais.

Por exemplo, o número binário `1101` pode ser representado como `D` em hexadecimal, simplificando a comunicação e programação. Essa relação direta entre hexadecimais e binários é particularmente útil em programação de baixo nível, como em manipulação de memória e registros de hardware.
 
## Desvendando o 2BPP

Agora, como de fato funciona a representação utilizando dos bits por pixel? A primeira coisa que temos que entender, é que as cores do Game Boy podem ser representadas
através de uma paleta de cores (apesar do Game Boy clássico possuir apenas quatro cores, isso será importante no próximo tópico), que se fossem cores da web, poderiam
ser representadas como:


<p align="center">
  <img src="../images/game-boy-colors.png" alt="As 4 cores do game boy" />
  <br/>
  <small>As 4 cores do game boy</small>
</p>

> Assim como usamos em diversos locais `0x` para representar um número em hexa como `0xFF` também é usado `0b` para números binários como em `0b1010`    

Podemos considerar então, da esquerda para direita que a para a direita possuimos a cores de número 1, 2, 3 e 4, que em binário traduzimos como, `00`, `01`, `10` e `11`.
Note que para qualquer uma dessas cores, precisamos de 2 bits para que possam ser representadas, logo paraum tile de 8x8 (64 pixels), para cada linha (8 pixels) iremos
precisar de 16 bits para que seja feita essa representação. Logo de cara podemos achar então que um hexadecimal, que como vimos anteriormente representam 4 bits, 
pode ser utilizado para representar um quarto da linha desse tile, e 4 hexas seriam o suficiente para representar a linha complete, logo 32 hexas poderiam representar
o tile por completo. E da fato nossa conta estaria correta, precisamos de 16 pares de hexas para representar um único tile 8x8, porém, o Game Boy não usa diretamente
um hexa para representar 2 pixels, existe um "pulo do gato".

Computadores organizam dados de maneiras específicas, que são descritos por um conceito chamado de [Endianness](https://developer.mozilla.org/pt-BR/docs/Glossary/Endianness),
e isso vale principalmente para números inteiros. O Game Boy por sua vez, utiliza o Little Endian, isso significa que, o ponteiro da pilha vai apontar
para a o último bit inserido, então o valor obitido ao ler `FF 00`, seria `0x00FF` (utilizando a notação para hexadecimal). Sendo assim, precisamos definir onde está
o bit mais significativo e o menos significativo para representar a nossas cores, e uma estratégia para isso é combinar dois valores em hexa como no exemplo anterior.

Para o caso de termos os valores `9C` ou `10011100` em binário como sendo o byte menos significativo e o valor `AB` ou `10101011` como o byte mais singificativo, podemos
realizar a combinação 2 a 2 para representar a cor de cada bit na tela, assim teríamos `11 00 10 01 11 01 10 10`, um ótimo exemplo disso é o disponiblizado pelo gbdev.io para
um tile definido como `$3C $7E $42 $42 $42 $42 $42 $42 $7E $5E $7E $0A $7C $56 $38 $7C`:

> `$` é uma forma de representar os valores de hexadecimal, assim como o `0x`, e essa forma varia de maneira arbitrária entre tipos diferentes de tecnologia, nesse exemplo específico eu decidir manter o uso `$` para fazer sentido com a imagem disponibilizada.

<p align="center">
  <img src="../images/game-boy-tile-map.png" alt="TIle" />
  <br/>
  <small>Dispinível em <a href="https://gbdev.io/pandocs/Tile_Data.html">gbdev.io</a></small>
</p>

Desta maneira então, funciona a representação de cores para cada pixel dos gráficos de um Game Boy. Um ponto importante que vale mencionar aqui é que o Game Boy faz uma diferanciação
entre tiles e sprites. 

Os sprites não são nada mais do que as coisas que se movimentam em um jogo, um maneira simples de pensar nisso é que os tiles são o background, e os sprites
são os personagens. Para o caso dos sprites é comum precisarmos de trasnparência, pois eles ficam na frente do background, caso isso não fosse possível, acabaria causando
uma experiência estranha onde o sprite estaria obstruindo completamente o background em que ele esta sobreposto. Então para atingir essa transparência necessária, a solução 
adota foi implementar um mecanismo no Game Boy para que no caso de tiles que serão usados como sprites, uma das cores não é renderizada (como o `0b00` por exemplo), criando assim
o efeito de transparência e fazendo que o sprites possuam apenas 3 cores, sendo uma "cor de transparência", enquanto os tiles de background podem usar 4 cores.

> Uma curiosidade: é que é possível utilizar os valores `00`, `01`, `10` e `11` para criar representações visuais bastante fidedignas aos gráficos do formato 2BPP. Nesse contexto, o valor `11` atua como preto, devido à sua densidade, ocupando mais espaço e deixando pouco vazio entre eles. Já o valor `00` representa o branco, seguindo o raciocínio oposto. Os valores `01` e `10` são utilizados para representar tons de cinza, funcionando como uma única variação dessa cor. Para uma compreensão mais visual dessa relação, recomendo explorar o repositório disponível em [aqui](https://github.com/victorheringer/jolt).

E por fiz, a título de curiosidade, a web utiliza 24 bits por
pixel para representar as cores do sRGB, o que corresponde à 16.777.216 ou 2^24 possíveis cores. Por isso utilizamos a composição de 8 números em hexadecimal
para representar as cores da web, pois como vimos anteriormente, cada hexa representa 4 bits, e 6x4 = 24. 


## E as cores do Game Boy?

Se você prestou atenção durante a explição, deve ter percebido que as 4 cores mostradas, na primeira imagem de exemplo e na disponibilizada pelo gbdev.io possuem diferenças.
Com o avanço da tecnologia depois de algumas iterações do hardware do Game Boy, como o Game Boy Pocket e Game Boy Light, em 21 de Outubro de 1998 surgiu o irmão mais novo
e mais poderoso da família, chamado de Game Boy Color. 

Com algumas melhorias no seu hardware, este console era capaz de produzir até 56 cores de maneira simultane na tela, de um
total de 32.768 cores existentes. Isso foi possível graças ao novo hardware possuir uma pequena quantidade de memória RAM para armazenamento das paletas de cores, então, a ideia de dois bits
por pixel ainda continuava valendo, porém dessa vez, era possível indicar em qual paleta estariam representado esses valores para aquele tile. Permitindo assim, além
de criar jogos muito mais bonitos devido a um sprite composto de 4 tiles poder ter até 16 cores diferentes, também trocar a paleta de jogos antigos que foram feitos
para a primeira versão que possuia apenas 4 cores.

Para deixar mais claro, aqui vai um exemplo, enquanto o valor `0b00` para a paleta de cima representa a cor preta, na paleta de baixo o mesmo valor representa o vermelho. 

<p align="center">
  <img src="../images/game-boy-pallets.png" alt="TIle" />
  <br/>
  <small>Diferentes paletas para um tile</small>
</p>


## Considerações Finais

Em suma, explorar os detalhes dos gráficos 2BPP do Nintendo Game Boy nos leva a uma compreensão mais profunda das técnicas de representação visual em consoles clássicos. Esses conceitos não apenas iluminam a engenharia por trás dos videogames antigos, mas também proporcionam insights valiosos para o desenvolvimento contemporâneo de jogos e interfaces. Ao mergulhar na simplicidade eficaz das paletas de cores e na organização meticulosa dos pixels, descobrimos a base sólida que sustenta toda uma era de inovação em entretenimento eletrônico. À medida que avançamos para novos horizontes tecnológicos, as lições aprendidas com o Game Boy continuam a inspirar e informar os criadores de hoje, demonstrando que o conhecimento do passado é uma ferramenta poderosa para moldar o futuro.

## Referências

* [Marcus. Gameboy 2BPP Graphics Format.](https://www.huderlem.com/demos/gameboy2bpp.html) *Acesso em 15 de Dezembro de 2023.* 
* [gbdev.io](https://gbdev.io/pandocs/Tile_Data.html) *Acesso em 15 de Dezembro de 2023.* 