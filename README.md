# Javascript Design Patterns

Todo desenvolvedor deveria se esforçar para escrever um código sustentável, legível, organizado, perfomático e reutilizável. A estruturação do código se torna mais importante à medida que as aplicações se tornam maiores. 

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
## Pub/Sub Design Pattern
O Pub/Sub é muito semelhante ao Observer, mas existe pequenas diferenças entre eles. Uma delas é que existe um orquestrador de "subscribers" pois eles são gerenciados como tópicos, ou seja, vocês pode querer afetar apenas uma série de elementos.

Para criarmos este orquestrador podemos fazer desta forma:

```javascript
var pubsub = {}
(function(q) {
    var topics = {}, subUid = -1
    q.subscribe = function(topic, func) {
        if (!topics[topic]) {
            topics[topic] = []
        }
        var token = (++subUid).toString()
        topics[topic].push({
            token: token,
            func: func
        })
        return token
    }

    q.publish = function(topic, args) {
        if (!topics[topic]) {
            return false
        }
        setTimeout(function() {
            var subscribers = topics[topic],
                len = subscribers ? subscribers.length : 0

            while (len--) {
                subscribers[len].func(topic, args)
            }
        }, 0)
        return true

    }

    q.unsubscribe = function(token) {
        for (var m in topics) {
            if (topics[m]) {
                for (var i = 0, j = topics[m].length; i < j; i++) {
                    if (topics[m][i].token === token) {
                        topics[m].splice(i, 1)
                        return token
                    }
                }
            }
        }
        return false
    }
}(pubsub))
```
Depois de criado você pode criar ínumeros "subscribers" com o mesmo tópico ou não, e fazer a publicação quando precisar

## Singleton Design Pattern

A idéia de padrão é fazer com que utilizemos apenas uma instâcia de um objeto para executar suas funções. Para mostrar isso no código, imagine que vários desenvolvedores precisam utilizar a mesma instância de um objeto para executar um console.log

Criaremos o objeto:
```javascript
const Printer = (function () {

  let printerInstance

  function createInstance () {

    function print (dev, text) {
      console.log(`O desenvolvedor ${dev} disse ${text}`)
    }

    return {
      print
    }
  }

  return {
    getInstance: function () {
      if (!printerInstance) {
        printerInstance = createInstance()
      }
      return printerInstance
    }
  }
})()
```
Se criarmos dois outros objetos, podemos pegar a mesma instância do objeto criado acima:
```javascript
const Printer1 = Printer.getInstance()
const Printer2 = Printer.getInstance()

Printer1.print('Vitinho', 'Olá desenvolvedores')
Printer2.print('Kuwai', 'Olá Vitinho!')

console.log('Printer1 tem a mesma instância de Printer2? ' + Printer1 === Printer2)
```

## Provider Design Pattern

A idéia do Provider Design Pattern é persistir um estado em um componente pai. Esse estado tem que ser acessado de qualquer outro componente filho ou neto, sem a necessidade de ficar repassando como props de filho para filho até chegar no componentes netos ou bisnetos. Este padrão é utilizado na biblioteca de Redux, por exemplo.

Pensando em componentes React, podemos ter um exemplo de Provider utilizando o React.Component.context, apesar de não ser muito recomendado a grande utilização desta feature.

Criamos um componente Provider para criar o context:
```javascript
class ThemeProvider extends React.Component {
  getChildContext() {
    return {
      coloredTheme: this.props.coloredTheme
    }
  }
  render() {
    return this.props.children
  }
}

ThemeProvider.childContextTypes = {
  coloredTheme: PropTypes.string
}

```

Envolvemos essa classe Provider na aplicação, passando uma prop de cor do tema:
```javascript
const coloredTheme = "green";
// tema de exemplo
// imagine que a configuração esteja localizada em outro lugar
// precisaria ser obtido primeiro (do servidor, por exemplo)
// e seria diferente para cada usuário de sua aplicação
ReactDOM.render(
  <ThemeProvider coloredTheme={coloredTheme}>
    <App />
  </ThemeProvider>,
  document.getElementById('app')
)
```

Agora em qualquer componente dentro da aplicação consegue pegar a cor do tema requisitando o context criado:

```javascript
class App extends React.Component {
  render() {
    return (
      <div>
        <Paragraph>
          That's how you would use children in React
        </Paragraph>
      </div>
    )
  }
}
class Paragraph extends React.Component {
  render() {
    const { coloredTheme } = this.context;
    return (
      <p style={{ color: coloredTheme }}>
        {this.props.children}
      </p>
    )
  }
}
Paragraph.contextTypes = {
  coloredTheme: PropTypes.string
}
```

## Conclusão

Implementaremos inumeras aplicações com tamanhos, complexidades e regras diferentes. 

Acabaremos usando um ou mais padrões de projetos para conquistarmos um código top da galáxia. 

Mas para isso precisamos ter em mente todas essas ferramentas em mãos para decidir nos momentos certos a utilização de cada uma delas e aproveitando o que elas tem de bom à oferecer. 

Espero ter ajudado um pouco com o esse resumo de alguns padrões de projetos Javascript.
