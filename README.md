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
    console.log('Trocando o texto de um elemento')
  }
  
  return {
    // declaração pública de variaveis e/ou funções
    publicChangeText: function() {
      changeText()
    }
  }
})()

ModuleClass.publicChangeText() // Output: Trocando o texto de um elemento
ModuleClass.text // undefined
ModuleClass.changeText() // undefined
```
### Revealing Module Pattern
O Module Design Pattern tem uma variação para que possamos revelar alguma variável ou função mas mantendo o encapsulamento de outros métodos

```javascript
const RevealingModuleClass = (function() {
  let counter = 8
  
  const privateMethod = function () {
    counter++
    console.log('inside private method')
  }
  
  const exposedMethod = function () {
    privateMethod()
  }

  const getCounter = function () {
	  console.log(counter)
  }

  return {
    counter,
    getCounter,
    increment: exposedMethod
  }
})()

RevealingModuleClass.increment() // Output: inside private method
RevealingModuleClass.counter // undefined
RevealingModuleClass.privateMethod() // undefined
```

## Prototype Design Pattern
O Prototype Design Pattern se baseia na herança prototípica do Javascript. 

É criado inicialmente um objeto original, com suas propriedades, para que se possar fazer clones deste objetos e reutilizar essas propriedades. 

Este padrão de projeto também pode ser utilizado para tarefas de alta performance replicando também uma funcionalidade, criando quantos clones precisar para fazer tarefas em paralelo.

```javascript
const Car = function () {
  this.wheels = 4
}

Car.prototype.go = function () {
  console.log('Push acelerate pedal')
}

const Celta = new Car()
Celta.wheels // 4
Celta.go() // Output: Push acelerate pedal

```

### Revealing Prototype Design Pattern

O padrão de Prototype também tem uma variação. Ela é utilizada para encapsular para deixar propriedades/funções públicas ou privadas

```javascript
const Car = function () {
  this.wheels = 4
}

Car.prototype = function () {

  const output = function (status) {
    console.log('The car is: ' + status)
  }
  
  const go = function () {
    output('running')
  }
  
  const stop = function () {
    output('stopped')
  }
  
  return {
    go,
    stop
  }
  
}()

const Celta = new Car()
Celta.wheels // 4
Celta.go() // Output: The car is: running
Celta.stop() // Output: The car is: stopped
Celta.output() // undefined

```


## Observer Design Pattern

Imagine que você precise executar ações simultaneas dada a mudança de um valor. Com este padrão de projeto podemos fazer isso.

Para isso precisaremos criar uma classe observadora, onde persistiremos uma lista de ações (observers) que desejamos executar.

Então teremos que criar uma forma de incrementar essa lista fazendo um "subscribe" ou remover uma ação da lista com "unsubscribe".

Com os "observers" criados, podemos fazer um "notify" quando tivermos um novo valor para fazer as ações simultaneamente.

```javascript
class Observable {
  // cada instância da classe Observer
  // começa com um array vazio de observadores/observers
  // que reagem a uma mudança de estado
  constructor() {
    this.observers = [];
  }

  // adicione a capacidade de inscrever uma nova ação
  // essencialmente, adicione algo ao array de observadores
  subscribe(f) {
    this.observers.push(f);
  }

  // adicione a capacidade de cancelar a inscrição de uma ação em particular
  // essencilamente, remove algum item do array de observadores
  unsubscribe(f) {
    this.observers = this.observers.filter(subscriber => subscriber !== f);
  }

  // atualiza todas as ações inscritas
  // e passa alguns dados para cada um deles
  notify(data) {
    this.observers.forEach(observer => observer(data));
  }
}
```

Criada uma classe podemos criar um Observer e utilizar para nosso objetivo

```javascript
const observer = new Observable()

const action1 = data => console.log('Ação 01: ' + data)
const action2 = data => console.log('Ação 02: ' + data)
const action3 = data => console.log('Ação 03: ' + data)

observer.subscribe(action1)
observer.subscribe(action2)
observer.subscribe(action3)

observer.notify('Hello Observer Design Pattern!')

observer.unsubscribe(action2)

observer.notify('Bye Action 02')

```

## Singleton Design Pattern

## Provider Design Pattern

## Conclusão
