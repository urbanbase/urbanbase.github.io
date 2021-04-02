---
layout: post
title: "Class vs Factory Function, 더 나은 선택은?"
date: 2021-03-28 10:00:00
categories: dev
tags: javascript ES6 API
cover: "/assets/21_03_ES6/21_03_ES6.png"
---


<img src="/assets//21_03_ES6/21_03_ES6.png" width="" alt="ES6">



안녕하세요. API 개발팀 이정준 입니다. ECMAScript6 에서 class가 추가되었습니다.
Class Function과 Factory Function의 차이를 알아보고, 어떤 Function을 사용할지 생각해 봅시다.

### 1.기본 사용 방법

기본 사용 방법은 다음과 같습니다. 

- Class Function

```javascript
class TodoModel {
  constructor(data) {
    this.todos = [];
    this.data = data;
  }
    
  addData() {
    console.log(`${data} addData`); 
  }

  add() { console.log('add'); }
}

const todoModel = new TodoModel('input');
todoModel.addData();        // input addData
```

- Factory Function

```javascript
function TodoModel(data){
  const todos = [];

  function addData() {
    console.log(`${data} addData`);
  }

  function add() { console.log('add'); }

  return Object.freeze({
    addData,
  });
}

const todoModel = TodoModel('input');
todoModel.addData();        // input addData
```

### 2. 캡슐화(Encapsulation)  

내부 변수 또는 감추고 싶은 함수에 접근이 가능 여부 입니다.  
캡슐화가 안되면 보안에 이슈가 생길 수 있습니다.  

- Class Function

```javascript
const todoModel = new TodoModel('input');
console.log(todoModel.todos);       // []
console.log(todoModel.data)         // input
todoModel.add();                    // add
```

- Factory Function

```javascript
const todoModel = TodoModel('input');
console.log(todoModel.todos);       // undefined
console.log(todoModel.data)         // undefined
todoModel.add();                    // todoModel.add is not a function
```

기본적으로 function은 캡슐화가 되지만 class는 캡슐화가 되지 않습니다.  
그러나 nodejs 12.0.0 버전부터는 Private class fields를 사용하면 class도 캡슐화를 할 수 있습니다.  

- Private class fields

```javascript
class TodoModel {
  #todos;

  constructor(data) {
    this.#todos = [];
    this.data = data;
  }

  addData() {
    console.log(`${data} addData`);
  }

  #add() { console.log('add'); }
}

const todoModel = new TodoModel('inputData');
console.log(todoModel.todos);       // undefined
todoModel.add();                    // todoModel.add is not a function
```

### 3. 불변성(Immutable)  

정의된 함수를 변경할 수 있는지를 말합니다.  
보안이나 코드 이해를 위해서는 함수가 변경되지 않는 것이 좋습니다.  
특수한 상황에 따라서는 함수 변경이 필요할 수도 있으나 권장하는 방식은 아닙니다.  

- Class Function

```javascript
todoModel.add = function() {
  console.log('a new add');
}
todoModel.add();            // a new add
```

- Factory Function

```javascript
todoModel.add = function() {
  console.log('a new add');
}
todoModel.add();            // add
```

Class Function에서는 함수를 변경할 수 있으나 Factory Function에서는 변경되지 않습니다.  
이 부분은 static을 사용하면 개선할 수 있으나 인스턴스화 되지 않으므로 사용법에 주의해야 합니다.  
그리고 static은 method를 정적으로 만드는 것이기 때문에 상황에 따라서는 static을 사용하는 것이 맞지 않을 수 있습니다.  

- static

```javascript
class TodoModel {
  #todos;

  constructor(data) {
    this.#todos = [];
    this.data = data;
  }

  addData() {
    console.log(`${data} addData`);
  }

  static add() { console.log('add'); }
}

TodoModel.add();            // add
TodoModel.add = function() {
  console.log("a new add");
}                           // Invalid left-hand side in assignment
```

### 4. 상속과 구성 (Composition and inheritance)  

class에서는 상속을 사용하지만 factory에서는 구성을 만들어 사용합니다.  
예시를 통해 비교해 보면 다음과 같습니다.  

- Class Function

```javascript
class Person {
  eat() {
    console.log('I am eating');
  }
  breathe() {
    console.log('I am breathing');
  }    
  swim() {
    console.log('I am swimming');
  } 
}
class Wizard extends Person {
  trick() {
    console.log('I am doing a trick');
  }
}
const harry = new Wizard();
const ron = new Wizard();

// Harry can:
harry.eat();          // I am eating
harry.breathe();      // I am breathing
harry.swim();         // I am swimming
harry.trick();        // I am doing a trick

// Ron can:
ron.eat();        // I am eating
ron.breathe();    // I am breathing
ron.swim();       // I am swimming
ron.trick();      // I am doing a trick
```

- Factory Function

```javascript
const Person = () => {
  return {
    eat: () => {
      console.log('I am eating');
    },
    breathe: () => {
      console.log('I am breathing');
    },
    swim: () => {
      console.log('I am swimming');
    },
  };
};
const Trick = () => {
  return {
    trick: () => {
      console.log('I am doing a trick');
    },
  };
};

const Wizard = () => {
  return {
    eat: Person().eat,
    breathe: Person().breathe,
    trick: Trick().trick,
  };
};
const Muggle = () => {
  return {
    eat: Person().eat,
    breathe: Person().breathe,
    swim: Person().swim,
  };
};

// Harry can:
const harry = Wizard();
harry.eat();            // I am eating
harry.breathe();        // I am breathing
harry.trick();          // I am doing a trick

// Ron can:
const ron = Muggle();
ron.eat();            // I am eating
ron.breathe();        // I am breathing
ron.swim();           // I am swimming
```

class는 상속을 받기 때문에 상속 받은 모든 method를 사용해야 하지만, factory는 구성을 하기 때문에 선별적으로 사용할 수 있습니다.  factory와 같이 사용하려면 새로운 class를 생성하거나 class person에서 swim()을 제거한 뒤 새로운 class로 상속 받아야 합니다.  

### 5. this  

class에서는 this 문법을 사용할 수 있으나 factory function에서는 this 문법을 사용할 수 없습니다.  
this 문법을 사용할 때에는 컨텍스트 손실 문제가 발생할 수 있기 때문에 사용할 때 유의해서 사용해야 합니다.  

- 컨텍스트 손실 문제란?  
context가 손실되는 문제로 아래 예시를 참고하시기 바랍니다.

```javascript
class TodoModel {
    constructor(){
        this.todos = [];
    }
    
    reload(){ 
        setTimeout(function log() { 
           console.log(this.todos);
        }, 0);
    }
}
todoModel.reload();                   //undefined
```

### 6. 메모리 

class가 factory 보다 효율적입니다.  

```
The memory cost (in Chrome)
+-----------+------------+------------+
| Instances | 10 methods | 20 methods |
+-----------+---------------+---------+
| 10        | 0          |  0         |
| 100       | 0.1Mb      |  0.1Mb     |
| 1000      | 0.7Mb      |  1.4Mb     |
| 10000     | 7.3Mb      | 14.2Mb     |
+-----------+------------+------------+
```

### 7. 결론 

메모리 사용에 있어서는 Class Function이 효율적입니다.  
그러나 개발하는 내용에 따라 Factory Function이 보다 안전할 수도 있기 때문에 Class Function을 사용할지, Factory Function을 사용할 지는 사용 용도에 맞춰서 결정할 수밖에 없을 듯 합니다. 

- 참고 사이트   
    - [Class vs Factory function: exploring the way forward](https://medium.com/programming-essentials/class-vs-factory-function-exploring-the-way-forward-73258b6a8d15)
    - [Inheritance vs. Composition in JavaScript](https://js.plainenglish.io/inheritance-is-a-vs-composition-has-a-in-javascript-98fb96dfa0e6)
    - [MDN Web Docs Private class fileds](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Classes/Private_class_fields)