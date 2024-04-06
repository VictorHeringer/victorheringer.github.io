---
title: "Não useImperativeHandle"
date: 2024-02-10T21:04:53-03:00
categories:
- Development
tags:
- React
- Typescript
- Javascript 
- Hook
# bookComments: false
# bookSearchExclude: false
---

Recentemente, ao explorar aleatoriamente o LinkedIn por alguns minutos sem encontrar nada de interesse, deparei-me finalmente com uma postagem que capturou minha atenção. Tratava-se de uma sugestão para utilizar o useImperativeHandle no gerenciamento da abertura e fechamento de modais. Ao continuar a leitura para compreender como esse hook abordava esse desafio específico, refleti sobre a proposta apresentada.

O que imediatamente veio à minha mente foram as prudentes palavras da documentação do React, que enfatiza a necessidade de evitar, na maioria dos casos, código imperativo usando refs.

A questão que surgiu foi: como um hook tão especializado poderia solucionar um problema aparentemente trivial, como lidar com a exibição de um modal? Dediquei alguns minutos para contemplar essa questão, revisitar os comentários na postagem e reler a documentação. Concluí, de maneira bastante evidente, que não seria a solução ideal para esse cenário específico.


Ao continuar a reflexão, revisitei os comentários e outro aspecto também capturou minha atenção: a quantidade significativa de pessoas que considerou a solução proposta como excelente. Parando para ponderar mais profundamente, cheguei à conclusão de que muitos indivíduos que ingressam no mercado de tecnologia optam por trajetórias de aprendizado que proporcionam uma inserção mais rápida no mercado de trabalho, o que, vale ressaltar, não é um problema. No entanto, como contrapartida, alguns conceitos fundamentais acabam sendo relegados a segundo plano.

Diante dessa observação, decidi registrar aqui um pouco mais sobre o useImperativeHandle, com o intuito de auxiliar aqueles que desejam aprofundar seus conhecimentos. Além disso, servirá como uma referência para mim mesmo no futuro, quando precisar relembrar exatamente a finalidade desse recurso.

## O que é o useImperativeHandle ?

A função deste hook é notavelmente simples e claramente definida: ele possibilita a personalização de uma ref, sendo que, no contexto do React, uma ref faz referência (sem trocadilhos) a um elemento no DOM.

Em JavaScript puro, realizar essa tarefa seria semelhante a utilizar getElementById para obter um elemento do DOM. No entanto, ao lidar com refs, a abordagem envolve a definição de uma variável e a atribuição a ela do elemento representado através da sintaxe JSX.

```ts
const ref = document.getElementById("meuInput");
```

Seria entre algumas aspas equivalente à:

```tsx
const ref = useRef(null);

return <input ref={ref}>
```

Em ambas as situações, seria possível utilizar a função focus() desse input, mas é crucial ter em mente que, no React, uma referência deve ser utilizada a partir de seu valor atual, acessado através de .current.

Agora, você pode estar se perguntando por que não simplesmente utilizar getElementById em vez de useRef. Vamos parar por aqui para evitar uma discussão extensa sobre o assunto e simplificar as coisas, afirmando que useRef ou createRef representam a abordagem "React" para lidar com elementos do DOM.

Prosseguindo, essas referências são responsáveis por possibilitar algo bastante importante: permitir que o componente pai invoque funções de um componente filho. Isso vai de encontro à intuição do React, onde o fluxo de dados é sempre "de cima para baixo", com o pai definindo funções e props e repassando-os para que os filhos os utilizem. Em outras palavras, o fluxo de dados no React segue uma direção bem definida, mas as refs permitem que um componente filho seja modificado fora desse fluxo "natural".

Neste ponto, é crucial compreender que o React se baseia em um paradigma declarativo, onde os componentes e hooks devem saber como reagir a mudanças de estado. Ou seja, ao modificar um estado, você desencadeia um novo ciclo de renderização, e todos os componentes respondem a essa mudança de maneira coordenada.

Por outro lado, as refs possibilitam a modificação imperativa de um componente, seguindo um paradigma diferente do estabelecido pela biblioteca. Portanto, quando você utiliza:

```ts
ref.current.focus();
```

Você está instruindo de maneira imperativa para que o foco seja direcionado ao seu elemento, sem aguardar uma reação a uma mudança de estado.

A distinção entre código imperativo e declarativo pode parecer um tanto confusa inicialmente, especialmente quando o exemplo em questão envolve apenas uma linha de código. Entretanto, o que gostaria que você compreendesse inicialmente é que, se for necessário descrever passo a passo como algo deve ser feito, esse algo é considerado imperativo.

> Planejo abordar mais profundamente o tema da programação imperativa versus declarativa em um futuro post e fornecerei um link para aprimorar essa explicação.

Em resumo, o uso de referências está associado à escrita de um código imperativo, que não está alinhado com o paradigma que o React promove. Com essa compreensão, torna-se claro por que o useImperativeHandle deve ser utilizado apenas em situações estritamente necessárias, pois sua única finalidade, conforme mencionado anteriormente, é permitir a modificação do valor de uma referência. Para ilustrar:

Suponha que seja necessário alterar o comportamento padrão da função de foco de um input, de modo que, sempre que o foco seja acionado, a cor de fundo do elemento seja modificada. Nesse caso, você poderia empregar o useImperativeHandle para alcançar esse objetivo:

```tsx
function ComponenteFilho(props, ref) {
  const inputRef = useRef();
  const [color, setColor] = useState("#fff");

  useImperativeHandle(ref, () => ({
    focusWithColor: (nextColor: string) => {
      inputRef.current.focus();
      setColor(nextColor);
    },
  }));

  return <input style={{ backgroundColor: color }} ref={inputRef} />;
}

ComponenteFilho = forwardRef(ComponenteFilho);

function ComponentePai() {
  const ref = useRef(null);

  return (
    <>
      <button onClick={() => ref.current.focusWithColor('#f00')}>
      <ComponenteFilho ref={ref} />
    </>
  );
}
```

Este exemplo ilustra a utilização deste hook, embora seja apenas uma demonstração didática de seu funcionamento, não representando um caso de uso apropriado para sua aplicação.

Ao examinar o código acima, pode ser tentador usar o useImperativeHandle para lidar com modais. Afinal, se é possível modificar um estado do filho sem declará-lo no pai, não seria necessário ter o estado de isVisible e as funções de abrir e fechar o modal em cada um dos componentes pais que os utilizam. Seria suficiente concentrar toda essa lógica no componente modal (que é o filho), modificar a ref para adicionar as funções de abrir e fechar o modal. Dessa forma, os componentes pais só precisariam passar uma ref e poderiam chamar os métodos quando necessário. Vamos, então, implementar esta abordagem:

```tsx
function ComponenteFilhoModal(props, ref) {
  const inputRef = useRef();
  const [isVisible, setIsVisible] = useState(false);

  useImperativeHandle(ref, () => ({
    open: () => {
      setIsVisible(true);
    },
    close: () => {
      setIsVisible(false);
    }
  }));

  return isVisible <p>Hello modal</p>;
}

ComponenteFilhoModal = forwardRef(ComponenteFilhoModal);

function ComponentePai() {
  const ref = useRef(null);

  return (
    <>
      <button onClick={() => ref.current.open()}>
      <ComponenteFilhoModal ref={ref} />
    </>
  );
}
```

Ao examinar o código, percebe-se que a lógica foi abstraída para o componente filho, aparentando resolver um problema de duplicação de código desnecessária. Afinal, se a lógica estivesse no pai, cada componente pai que precisa usar um modal também teria que incorporar a lógica de abrir e fechar, resultando em duplicação. Entretanto, na realidade, criou-se outro problema: o uso do React de maneira contrária ao seu design original.

Como o React foi concebido para ser utilizado? Bem, esse desafio de implementar e abstrair a lógica de abrir e fechar um modal, assim como vários outros, é conhecido como problemas de compartilhamento de lógica de estado. E esses podem ser solucionados através da feature que o React criou para esse propósito, os famosos... hooks! Conforme podemos observar na documentação:

> Dois componentes usando o mesmo Hook compartilham estado (state)? Não. Hooks customizados são um mecanismo para reutilizar lógica com estado (como configurar uma subscrição ou lembrar de um valor atual), mas sempre que você usa um Hook customizado, todo o estado (state) e os efeitos dentro dele são completamente isolados.

Portanto, os hooks são a abordagem que o React propõe para o compartilhamento de lógica de estado. Criar um hook, como um useModal, resolveria o problema de duplicação de lógica, mantendo a característica do fluxo natural de dados, onde o pai invoca os hooks e repassa o estado de aberto ou fechado para o filho. Problema resolvido!

```tsx
function useModal() {
  const [isVisible, setIsVisible] = useState(false);

  function open() {
    setIsVisible(true);
  }

  function close() {
    setIsVisible(false);
  }

  return { isVisible, show, hide };
}

function ComponenteFilhoModal({ isVisible }) {
  return isVisible <p>Hello modal</p>;
}

function ComponentePai() {
  const { isVisible, open, close } =  useModal()

  return (
    <>
      <button onClick={() => open()}>
      <ComponenteFilhoModal isVisible={isVisible} />
    </>
  );
}
```

Se todas essas explicações ainda não foram suficientes para evidenciar que o uso do useImperativeHandle para modais é um "anti-pattern", o React, em sua [documentação](https://pt-br.reactjs.org/docs/hooks-reference.html#useimperativehandle), oferece um exemplo bastante específico:

> Evite usar refs para qualquer coisa que possa ser feita de forma declarativa. Por exemplo, ao invés de expor os métodos open() e close() em um componente Dialog, passe a propriedade isOpen para ele.

## Quando devo utilizar o useImperativeHandle ?

Agora que compreendeu a finalidade do useImperativeHandle e por que não deveria ser utilizado, já deve estar se questionando se este hook é um capricho coletivo ou uma pegadinha do time de desenvolvimento do React, certo?

Não é para menos. A documentação do React, ao mencionar refs, geralmente é acompanhada por avisos para evitá-las. Contudo, tanto as refs quanto o useImperativeHandle têm sua relevância, uma vez que a implementação dos elementos do DOM não é declarativa. Portanto, há momentos em que é necessário, de maneira consciente, quebrar algumas boas práticas e padrões na área de tecnologia. Isso se deve ao fato de que nem sempre as coisas funcionarão exatamente como precisamos, tornando essencial compreender plenamente o que estamos fazendo e por que estamos fazendo.

Pessoalmente, utilizei esse hook muito raramente. Uma das situações em que se fez necessário foi devido a particularidades do React Native e a forma como as posições funcionam nele. Nesse contexto, precisei que um componente filho alterasse outro componente filho, sem a possibilidade de inserir essa lógica no componente pai e compartilhá-la entre eles.

Essa é uma das principais razões pelas quais é desafiador determinar quando esse hook deve ser usado. São casos específicos que variam de projeto para projeto, de negócio para negócio e de arquitetura para arquitetura. O crucial é compreender sua função e as compensações que traz, para que, quando necessário, seja possível tomar a melhor decisão.

Encerraria o post por aqui, mas há mais um ponto que considero bastante relevante para concluir este assunto.

## Por que utilizamos o forwardRef com useImperativeHandle?

A doc do React simplesmente diz:

> O useImperativeHandle deve ser usado com forwardRef

A documentação do React simplifica a utilização do useImperativeHandle com a seguinte afirmação:

> O useImperativeHandle deve ser usado com forwardRef.

Entretanto, será que é só isso? Bem, parece que não. É fundamental compreender por que precisamos dessa função. Então, por que, de fato, a utilizamos?

O forwardRef tem a finalidade de encaminhar uma referência. Voltando ao nosso exemplo, necessitamos que a ref declarada no ComponentePai seja repassada. Dessa forma, podemos enviá-la como parâmetro para o useImperativeHandle. Isso ocorre porque não desejamos que a ref declarada no pai seja uma ref para o ComponenteFilho, como aconteceria se não a repassássemos usando o forwardRef. Em suma, nosso objetivo final não é que o ComponentePai tenha uma referência para o ComponenteFilho, mas sim que essa referência seja encaminhada para que possamos criar, nela, o método focusWithColor. Assim, a ref que estamos definindo com o useImperativeHandle terá acesso à referência do input, que é declarada dentro do próprio filho.

E agora sim, é isso.

## Considerações finais

Explorar o useImperativeHandle abrange uma série de detalhes e pontos específicos, e se você chegou até aqui meio atordoado pela quantidade de informações, saiba que não está sozinho. Solucionar problemas mais específicos sempre demanda um entendimento mais profundo, devido às suas particularidades. O React, embora tenha uma API concisa, está repleto de detalhes cruciais, e, por isso, até a escolha de utilizar um simples hook ou não implica nessa carga de informação.

Planejo aprofundar alguns dos temas mencionados aqui em futuros conteúdos, como mencionado anteriormente. Assim, se houver algum ponto que ainda não ficou claro para você, talvez eu possa esclarecer em breve ou, quem sabe, até mesmo complicar um pouco mais. Portanto, continue estudando e praticando!

## Referências

* [React, useImperativeHandle.](https://react.dev/reference/react/useImperativeHandle) *Acesso em 14 de Dezembro de 2023.*