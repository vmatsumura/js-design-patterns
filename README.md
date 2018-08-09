# Javascript Design Patterns

Todo desenvolvedor deveria se esforçar para escrever um código sustentável, legível e reutilizável. A estruturação do código se torna mais importante à medida que as aplicações se tornam maiores. 

Os padrões de projeto (Design Patterns) são cruciais para solucionar esse desafio, fornecendo uma estrutura padronizada e organizada para que se tenha um entendimento mais rápido para resolução de problemas.

Muita das vezes acabamos utilizando mais de um padrão de projeto dependendo do tamanho e complexidade do projeto. Para sermos mais assertivos na escolha do uso de um ou mais padrões de projetos temos que entender:
* O que queremos resolver?
* Como resolvemos com esse padrão?
* Como e onde implementamos?

Para respondermos essas perguntas, vamos conhecer alguns padrões de projeto Javascript:

* [Module Design Pattern](#module-design-pattern)
* [Prototype Design Pattern](#prototype-design-pattern)
* [Observer Design Pattern](#observer-design-pattern)
* [Singleton Design Pattern](#singleton-design-pattern)
* [Provider Design Pattern](#provider-design-pattern)

## Module Design Pattern

Este padrão de projeto é um dos mais utilizados pelos desenvolvedores. Com este padrão podemos manter códigos indenpendentes de outros componentes.

Para quem esta familiarizado com linguagem OO, o módulo é como se fosse uma classe do Javascript. A vantagem tirada disso é o encapsulamento que protege propriedades e funções de outros módulos.

Mas para que o escopo privado funcione, é necessário a invocação imediata de uma função que envolve o módule, também conhecida como IIFE (Immediately-Invoked-Function-Expressions).

```javascript
const ModuleClass = (function() {
  // declaração privada de variaveis e/ou funções
  const text = 'Olá Module Design Pattern'
  
  const changeText = () => {
    const element = document.getElementById('hello-world')
    element.innerHTML = text
  }
  
  return {
    // declaração pública de variaveis e/ou funções
    publicChangeText: function() {
      changeText()
    }
  }
})()

ModuleClass.publicChangeText() // Vai conseguir executar o método changeText
ModuleClass.text // undefined
ModuleClass.changeText() // undefined
```
### Revealing Module Pattern
O Module Design Pattern tem uma variação para que possamos revelar alguma variável ou função mas mantendo o encapsulamento de outros métodos

```javascript
const RevealingModuleClass = (function() {
  // declaração privada de variaveis e/ou funções
  const counter = 8
  
  const privateMethod = () => {
    counter++
    console.log('inside private method')
  }
  
  const exposedMethod = () => {
    privateMethod()
  }
  
  return {
    // declaração pública de variaveis e/ou funções
    increment: exposedMethod
  }
})()

RevealingModuleClass.increment() // Output: inside private method
RevealingModuleClass.counter // undefined
RevealingModuleClass.privateMethod() // undefined
```

## Prototype Design Pattern

## Observer Design Pattern

## Singleton Design Pattern

## Provider Design Pattern

## Conclusão
