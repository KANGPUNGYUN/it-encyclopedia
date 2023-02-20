# 19. 프로토타입

## 19.1 객체지향 프로그래밍

- 객체지향 프로그래밍이란 프로그램을 여러 개의 독립적 단위 객체의 집합으로 표현하는 패러다임

- 객체지향 프로그래밍을 레고에 빗대 표현할 수 있는데, 객체가 레고의 조각이 되고, 레고의 조각을 조립해서 무언가를 만드는 방식이 객체지향 프로그래밍이라고 할 수 있다.

- 객체지향 프로그래밍은 크게 추상화, 캡슐화, 상속, 다형성이라는 특징을 가집니다.

## 19.2 상속과 프로토타입

- 상속은 어떤 객체의 프로퍼티 또는 메서드를 다른 객체가 상속받아 그대로 사용할 수 있는 것을 말한다.

- 자바스크립트에선 프로토타입을 기반으로 상속을 구현하여 불필요한 중복을 제거한다.

```js
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
  this.getArea = function () {
    return Math.PI * this.radius ** 2;
  };
}

// 반지름이 1인 인스턴스 생성
const circle1 = new Circle(1);
// 반지름이 2인 인스턴스 생성
const circle2 = new Circle(2);

// Circle 생성자 함수는 인스턴스를 생성할 때마다 동일한 동작을 하는
// getArea 메서드를 중복 생성하고 모든 인스턴스가 중복 소유한다.
// getArea 메서드는 하나만 생성하여 모든 인스턴스가 공유해서 사용하는 것이 바람직하다.
console.log(circle1.getArea === circle2.getArea); //false

console.log(circle1.getArea()); // 3.141592653589793
console.log(circle2.getArea()); // 12.566370614359172
```

![image](https://user-images.githubusercontent.com/71264780/220138179-8f587b23-80cb-4831-aff4-8500d89b621d.png)

```js
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
}

// Circle 생성자 함수가 생성한 모든 인스턴스가 getArea 메서드를
// 공유해서 사용할 수 있도록 프로토타입에 추가한다.
// 프로토타입은 Circle 생성자 함수의 prototype 프로퍼티에 바인딩되어 있다.
Circle.prototype.getArea = function () {
  return Math.PI * this.radius ** 2;
};

// 인스턴스 생성
const circle1 = new Circle(1);
const circle2 = new Circle(2);

// Circle 생성자 함수가 생성한 모든 인스턴스는 부모 객체의 역할을 하는
// 프로토타입 Circle.prototype으로부터 getArea 메서드를 상속받는다.
// 즉, Circle 생성자 함수가 생성하는 모든 인스턴스는 하나의 getArea 메서드를 공유한다.
console.log(circle1.getArea === circle2.getArea); // true

console.log(circle1.getArea()); // 3.141592653589793
console.log(circle2.getArea()); // 12.566370614359172
```

![image](https://user-images.githubusercontent.com/71264780/220138303-3f08bb85-9f0e-496f-b040-9ff5f378a791.png)

## 19.3 프로토타입 객체

- 프로토타입은 어떤 객체의 상위 객체의 역할을 하는 객체로서 다른 객체에 공유 프로퍼티를 제공한다.

- 프로토타입을 상속받은 하위 객체는 상위 객체의 프로퍼티를 자신의 프로퍼티처럼 사용할 수 있다.

- 모든 객체는 [[Prototype]]이라는 내부 슬롯을 가지며, 이 내부 슬롯의 값은 프로토타입의 참조다.

- [[Prototype]]에 직접 접근할 수 없지만 `__proto__` 접근자 프로퍼티를 통해 자신의 프로토타입에 간접적으로 접근할 수 있다.

<img width="376" alt="console" src="https://user-images.githubusercontent.com/71264780/220143322-b211ca37-4964-45ae-9e7a-f5387ce36c8d.png">

- 자바스크립트는 원칙적으로 내부 슬롯과 내부 메서드를 직접적으로 접근하거나 호출 할 수 있는 방법을 제공하지 않는다.

- Object.prototype의 접근자 프로퍼티인 `__proto__`는 getter/setter 함수락고 부르는 접근자 함수를 통해 [[Prototype]] 내부 슬롯의 값, 즉 프로토타입을 취득하거나 할당한다.

```js
const obj = {};
const parent = { x: 1 };

// getter 함수인 get __proto__가 호출되어 obj 객체의 프로토타입을 취득
obj.__proto__;
// setter 함수인 set __proto__가 호출되어 obj 객체의 프로토타입을 교체
obj.__proto__ = parent;

console.log(obj.x); // 1
```

- `__proto__`접근자 프로퍼티는 상속을 통해 사용된다.

- 프로토타입에 접근하기 위해 전근자 프로퍼티를 사용하는 이유는 상호 참조에 의해 프로토타입 체인이 생성되는 것을 방지하기 위해서이다.

![image](https://user-images.githubusercontent.com/71264780/220154160-1f4f7ca1-b229-417f-a9a3-0ed22c107f2c.png)

- 프로토타입 체인은 단방향 링크드 리스트로 구현되어야 한다.

- 서로가 자신의 프로토타입이 되는 비정상적인 프로토타입 체인이 만들어지면 프로토타입의 종점이 존재하지 않기 때문에 프로토타입 체인에서 프로퍼티를 검색할 때 무한 루프에 빠집니다.

- 모든 객체가 `__proto__` 접근자 프로퍼티를 사용할 수 있는 것은 아니라서 `__proto__` 접근자 프로퍼티를 코드 내에서 직접 사용하는 것을 권장하지 않습니다.

- `__proto__` 접근자 프로퍼티 대신 프로토타입의 참조를 취득하고 싶은 경우에는 Object.getPrototypeOf 메서드를 사용하고, 프로토타입의 참조를 교체하고 싶은 경우에는 Object.setPrototypeOf 메서드를 사용할 것을 권장한다.

```js
const obj = {};
const parent = { x: 1 };

// obj 객체의 프로토타입을 취득
Object.getPrototypeOf(obj); // obj.__proto__;

// obj 객체의 프로토타입을 취득
Object.setPrototypeOf(obj, parent); // obj.__proto__ = parent;

console.log(obj.x); // 1
```

- 함수 객체만이 소유하는 prototype 프로퍼티는 생성자 함수가 생성한 인스턴스의 프로토타입을 가리킨다.

| 구분                        | 소유        | 값                | 사용주체    | 사용목적                                                           |
| --------------------------- | ----------- | ----------------- | ----------- | ------------------------------------------------------------------ |
| `__proto__` 접근자 프로퍼티 | 모든 객체   | 프로토타입의 참조 | 모든 객체   | 객체가 자신의 프로토타입에 접근 또는 교체하기 위해 사용            |
| prototype 프로퍼티          | constructor | 프로토타입의 참조 | 생성자 함수 | 생성자 함수가 자신이 생성할 객체의 프로토타입을 할당하기 위해 사용 |

- 모든 객체가 가지고 있는 `__proto__` 접근자 프로퍼티와 함수 객체만이 가지고 있는 prototype 프로퍼티는 결국 동일한 프로토타입을 가리킨다. 하지만 이들 프로퍼티를 사용하는 주체가 다르다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person("Lee");

// 결국 Person.prototype과 me.__proto__는 동일한 프로토타입을 가리킨다.
console.log(Person.prototype === me.__proto__); // true
```

![image](https://user-images.githubusercontent.com/71264780/220162082-f3b00d93-c9ac-4125-8967-82730897a693.png)

- 모든 프로토타입은 constructor 프로퍼티를 갖는다. 이는 생성자 함수가 생성될 때 함께 연결된다.

- me 객체에는 constructor 프로퍼티가 없지만 me 객체의 프로토타입인 Person.prototype에는 constructor 프로퍼티가 있다. 따라서 me 객체는 프로토타입의 constructor 프로퍼티를 상속받아 사용할 수 있다.

## 19.4 리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입

- 프로토타입은 생성자 함수와 더불어 생성되며 prototype, constructor 프로퍼티에 의해 연결되어 있다.

- 프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍으로 존재한다.

| 리터럴 표기법      | 생성자 함수 | 프로토타입         |
| ------------------ | ----------- | ------------------ |
| 객체 리터럴        | Object      | Object.prototype   |
| 함수 리터럴        | Function    | Function.prototype |
| 배열 리터럴        | Array       | Array.prototype    |
| 정규 표현식 리터럴 | RegExp      | RegExp.prototype   |

## 19.5 프로토타입의 생성 시점

- 프로토타입은 생성자 함수가 생성되는 시점에 더불어 생성된다.

- 함수 호이스팅에 의해 함수 선언문은 런타임 이전에 자바스크립트 엔진에 의해 먼저 실행된다. 이때 프로토타입도 더불어 생성된다.

<img width="348" alt="image" src="https://user-images.githubusercontent.com/71264780/220168062-0abc96cf-7234-4ee6-99ae-d9e2c22a9eef.png">

- 빌트인 생성자 함수도 일반 함수와 마찬가지로 빌트인 생성자 함수가 생성되는 시점에 프로토타입이 생성된다.

- 모든 빌트인 생성자 함수는 전역 객체가 생성되는 시점에 생성된다.

- 생성된 프로토타입은 빌트인 생성자 함수의 prototype 프로퍼티에 바인딩된다.

## 19.6 객체 생성 방식과 프로토타입의 결정

- 객체를 생성하는 방법에는 객체 리터럴, Object 생성자 함수, 생성자 함수, Object.create 메서드, 클래스를 이용하여 생성하는 방법이 있다.

- 프로토타입은 추상 연산 OrdinaryObjectCreate에 전달되는 인수에 의해 결정된다.

- 이 인수는 객체가 생성되는 시점에 객체 생성 방식에 의해 결정된다.

- 객체 리터럴에 의해 생성된 객체 프로토타입 : OrdinaryObjectCreate에 전달되는 프로토타입은 Object.prototype이다.

- Object 생성자 함수에 의해 생성된 객체 프로토타입 : OrdinaryObjectCreate에 전달되는 프로토타입은 Object.prototype이다.

- 생성자 함수에 의해 생성된 객체 프로토타입 : OrdinaryObjectCreate에 전달되는 프로토타입은 생성자 함수의 prototype 프로퍼티에 바인딩되어 있는 객체다.(Object.prototype가 아닌 생성자함수이름.prototype인 것이다)

## 19.7 프로토타입 체인

- 스코프 체인(scope chain)과 유사하게 상속받은 최상위 프로토타입을 종점으로 하여 부모 프로토타입의 프로퍼티를 찾아 가는 것을 프로토타입 체인이라고 한다.

![image](https://user-images.githubusercontent.com/71264780/220171478-6165f6ac-99ef-4c93-b1bd-c40d33259b60.png)

```js
// hasOwnProperty는 Object.prototye의 매서드다.
// me 객체는 프로토타입 체인을 따라 hasOwnProperty 메서드를 검색하여 사용한다.
me.hasOwnProperty("name"); // true
```

- Object.prototype을 프로토타입 체인의 종점이라하고, Object.prototype의 프로토타입은 null이다.

- 프로토타입 체인은 식별자과 프로퍼티 검색을 위한 메커니즘이다.

## 19.8 오버라이딩과 프로퍼티 섀도잉

- 오버라이딩 : 상위 클래스가 가지고 있는 메서드를 하위 클래스가 재정의하여 사용하는 방식

- 상속 관계에 의해 부모 프로퍼티 메서드가 가려지는 것을 프로퍼티 섀도잉(Property Shadowing)이라고 한다.

```js
const Person = (function () {
  // 생성자 함수
  function Person(name) {
    this.name = name;
  }

  // 프로토타입 메서드
  Person.prototype.sayHello = function () {
    console.log(`Hi~ My name is ${this.name}`);
  };

  // 생성자 함수 반환
  return Person;
})();

const me = new Person("Lee");

// 인스턴스 메서드
me.sayHello = function () {
  console.log(`Hello World i'm ${this.name}`);
};

// 인스턴스 메서드가 호출된다. 프로토타입 메서드는 인스턴스 메서드에 의해 가려진다.
me.sayHello(); // Hello World i'm Lee
```

![image](https://user-images.githubusercontent.com/71264780/220172878-020502d8-0eec-48b2-8f13-262d5a8bfbd5.png)

- 인스턴스 메서드 sayHello는 프로토타입 메서드 sayHello를 오버라이딩했고 프로토타입 메서드 sayHello는 가려진다.

```js
// 인스턴스 메서드를 삭제한다.
delete me.sayHello;

// 인스턴스에는 sayHello 메서드가 없으므로 프로토타입 메서드가 호출된다.
me.sayHello(); // Hi ~ My name is Lee

// 이번에는 프로토타입 메서드를 삭제한다.
delete me.sayHello; // 하위 객체를 통한 상위 프로퍼티의 set 액세스는 혀용 불가하다.

// 삭제 되지 않았다.
me.sayHello(); // Hi ~ My name is Lee
```

- 하위 객체를 통한(프로토타입 체인을 통한) 상위 프로퍼티의 set 액세스는 불가능하다.

- 상위 프로퍼티의 set 액세스를 위해서는 프로토타입에 직접 접근해야한다.

```js
// 프로토타입 메서드 변경
Person.prototype.sayHello = function () {
  console.log(`Hello World i'm ${this.name}`);
};

me.sayHello(); // Hello World i'm Lee

// 프로토타입 메서드 삭제
delete Person.prototype.sayHello;
me.sayHello(); // TypeError: me.sayHello is not a function
```

## 19.9 프로토타입 교체

- 프로토타입은 임의의 다른 객체로 변경할 수 있다. 즉, 부모 객체인 프로토타입을 동적으로 변경할 수 있다.

- 프로토타입 교체는 생성자 함수 또는 인스턴스에 의해 교체할 수 있다.

```js
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  // 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  Person.prototype = {
    sayHello() {
      console.log(`Hi~ My name is ${this.name}`);
    },
  };

  return Person;
})();

const me = new Person("Lee");
```

![image](https://user-images.githubusercontent.com/71264780/220174691-35e99c8a-b63f-4479-99e5-7f1964ae61ac.png)

- 교체한 객체 리터럴에는 constructor 프로퍼티가 없다. me 객체의 생성자 함수를 검색하면 Person이 아닌 Object가 나온다.

- 위와 같은 문제를 해결하기 위해서는 객체 리터럴에 constructor 프로퍼티를 추가하고, prototype 프로퍼티를 재설정 해야한다.

```js
function Person(name) {
  this.name = name;
}

const me = new Person("Lee");

// 프로토타입으로 교체할 객체
const parent = {
  sayHello() {
    console.log(`Hi~ My name is ${this.name}`);
  },
};

// me의 객체 프로토타입을 parent로 교체한다.
Object.setPrototypeOf(me, parent);

me.sayHello();
```

- 인스턴스에 의한 프로토타입으로 교체한 객체에는 constructor 프로퍼티가 없으므로 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴된다.

- 따라서 프로토타입의 constructor 프로퍼티로 me 객체의 생성자 함수를 검색하면 Person이 아닌 Object가 나온다.

- 생성자 함수에 의한 프로토타입 교체와 인스턴스에 의한 프로토타입 교체에 차이가 있다면 생성자 함수에 의한 프로토타입 교체에선 Person 생성자 함수의 prototype 프로퍼티가 교체된 프로토타입을 가리키지만, 인스턴스에 의한 프로토타입 교체에선 Person 생성자 함수의 prototype 프로퍼티가 교체된 프로토타입을 가리키지 않는다.

## 19.10 instanceof 연산자

- instanceof 연산자는 이항 연산자로서 좌변에 객체를 가리키는 식별자, 우변에 생성자 함수를 가리키는 식별자를 피연산자로 받는다.

- 우변의 생성자 함수의 prototype에 바인딩된 객체가 좌변의 객체의 프로토타입 상에 존재하면 true 로 평가, 반대의 경우에는 TypeError 발생.

```js
function Person(name) {
  this.name = name;
}

const me = new Person("Lee");

const parent = {};

Object.setPrototypeOf(me, parent);

console.log(Person.prototype === parent); // false
console.log(parent.constructor); // Object

console.log(me instanceof Person); // false

console.log(me instanceof Object); // true

console.log(parent instanceof Person); // false

Person.prototype = parent; // parent 객체를 Person 생성자 함수의 prototype 프로퍼티에 바인딩한다.

console.log(me instanceof Person); // true
console.log(Person.prototype === parent); // true
```

- instanceof는 생성자 함수의 prototype에 바인딩 된 객체가 프로토타입 체인 상에 존재하는지 확인한다.

## 19.11 직접 상속

- Object.create 메서드는 명시적으로 프로토타입을 지정하여 새로운 객체를 생성한다. 즉, 객체를 생성하면서 직접적으로 상속을 구현하는 것이다.

```js
// Object.create 직접 상속
// 프로토타입이 null인 객체를 생성한다. 생성된 객체는 프로토타입 체인의 종점에 위치한다.
// obj -> null
let obj = Object.create(null);
console.log(Object.getPrototypeOf(obj) === null); // true
// Object.prototype을 상속받지 못한다.
//console.log(obj.toString()); // TypeError: obj.toString is not a function

// obj -> Object.prototype -> null
// obj = {}; 와 동일하다.
obj = Object.create(Object.prototype);
// Objet.prototype를 상속받는다.
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

// obj -> Object.prototype -> null
// obj = {x: 1}; 와 동일하다.
obj = Object.create(Object.prototype, {
  x: { value: 1, writable: true, enumerable: true, configurable: true },
});
// 위 코드는 아래와 동일
// obj = Object.create(Object.prototype);
// obj.x = 1;
console.log(obj.x);
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

const myProto = { x: 10 };
// obj -> myProto -> Object.prototype -> null
obj = Object.create(myProto);
console.log(obj.x);
console.log(Object.getPrototypeOf(obj) === myProto); // true

// 생성자 함수
function Person(name) {
  this.name = name;
}

// obj -> Person.prototype -> Object.prototype -> null
// obj = new Person('Lee')과 동일
obj = Object.create(Person.prototype);
obj.name = "Lee";
console.log(obj.name); // Lee
console.log(Object.getPrototypeOf(obj) === Person.prototype); // true
```

- Object.create 메서드의 장점으로 new 연산자 없이 객체 생성 가능하며, 프로토타입을 지정하며 객체 생성 가능하고, 객체 리터럴로 생성된 객체도 상속 가능하다는 점이 있다.

- 하지만, Object.create에서 Object.prototype 메서드를 직접 호출하면 프로토타입 체인 종점에 위치하는 객체를 생성할 수 있으므로 Object.prototype 메서드의 직접적인 사용을 권장하지 않는다.

- Object.create 메서드의 단점으로 두 번째 인자로 프로퍼티를 정의하는 것이 번거롭다.

- ES6에서는 객체 리터럴 내부에서 proto접근자 프로퍼티를 사용해 직접 상속 구현이 가능하다.

```js
const myProto = { x: 10 };

// 객체 리터럴에 의해 객체를 생성하면서 프로토타입을 지정하여 직접 상속받을 수 있다.
const obj = {
  y: 20,
  // 객체 직접 상속받는다
  // obj -> myProto -> Object.prototype -> null
  __proto__: myProto,
};

/* 위 코드는 아래와 동일하다
const obj = Object.create(myProtom {
  y: {value: 20, writable: true, ...}
})
*/

console.log(obj.x, obj.y); // 10, 20
console.log(Object.getPrototypeOf(obj) === myProto); // true
```

## 19.12 정적 프로퍼티/메서드

- 정적(static) 프로퍼티/메서드는 생성자 함수로 인스턴스를 생성하지 않아도 참조/호출할 수 있는 프로퍼티/메서드이다.

```js
/ 생성자 함수
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi My name is ${this.name}`);
};

// 정적 프로퍼티
Person.staticProp = 'static prop';

// 정적 메서드
Person.staticMethod = function () {
  console.log('static method');
}

const me = new Person('Lee');

// 생성자 함수에 추가한 정적 프로퍼티/메서드는 생성자 함수로 참조/호출한다.
Person.staticMethod(); // static method

let a = Person.staticProp;

console.log(a);

// 정적 메서드, 프로퍼티는 생성자 함수가 생성한 인스턴스로 호출이 불가하다.
// 인스턴스로 참조/호출할 수 있는 프로퍼티/메서드는 프로토타입 체인 상에 존재해야 한다.
me.staticMethod(); // TypeError: me.staticMethod is not a function
```

- 정적 프로퍼티/메서드는 Person 생성자 함수가 소유한다. 생성자 함수가 생성한 인스턴스(me)로는 참조하거나 호출할 수 없다.

- 인스턴스/ 프로토타입 메서드에서 this로 바인딩 되는 프로퍼티가 없다면 그 메서드는 정적 메서드로 변경이 가능하다.

## 19.13 프로토타입 존재 확인

### 19.13.1 in 연산자

- in 연산자는 객체 내에 특정 프로퍼티가 존재하는지 여부를 확인한다.

```js
const person = { name: "Lee" };

console.log("name" in person); // true

console.log("toString" in person); // true
```

### 19.13.2 Object.prototype.hasOwnProperty 메서드

- Object.prototype.hasOwnProperty 메서드 또한 객체에 특정 프로퍼티가 있는지 확인할 수 있다.

```js
console.log(person.hasOwnProperty("name")); // true
console.log(person.hasOwnProperty("toString")); // false
```

- in 연산자와 Object.prototype.hasOwnProperty 메서드와의 차이점은 in 연산자에서 person 객체가 속한 프로토타입 체인을 따라 검색하기 때문에 toString도 true가 나오지만, hasOwnProperty 메서드에서는 고유의 프로퍼티 외에 상속받은 프로퍼티나 메서드는 false로 처리한다.

## 19.14 프로토타입 열거

### 19.14.1 for ... in 문

- 객체의 모든 프로퍼티를 순회하며 열거하려면 for in 문을 사용한다.

```js
const person = {
  name: "Lee",
  address: "Seoul",
};

// for...in 문의 변수 prop에 person 객체의 프로퍼티 키가 할당된다.
for (const key in person) {
  console.log(key + ": " + person[key]);
}
// name: Lee
// address: Seoul
```

- for in 문은 상속받은 프로토타입의 프로퍼티까지 열거하는데, Object.prototype의 프로퍼티가 열거되지 않는 이유는, 프로퍼티 속성 값에 열거할 수 없도록 정의되어 있기 때문이다.(enumable : false)

- for in 문은 [[Enumable]]의 값이 true인 프로퍼티를 순회하며 열거한다.

- for in 문은 프로퍼티 키가 심벌인 프로퍼티는 열거하지 않는다.

- for in 문은 열거 순서를 보장하지 않는다. (대부분 모던 브라우저들은 보장한다)

### 19.14.2 Object.keys/values/entries 메서드

- for in 문에 hasOwnProperty 메서드를 사용하여 자기 자신 객체인지 확인하는 방법도 있지만.
  되도록이면 Object.key / values / entries 메서드를 사용하는 것을 권장한다.

```js
const person = {
  name: "Lee",
  address: "Seoul",
  __proto__: { age: 20 },
};
```

- Object.keys : 객체 자신이 열거 가능한 프로퍼티 키를 배열로 반환한다.

```js
console.log(Object.keys(person)); // ["name", "address"]
```

- Object.values : 객체 자신이 열거 가능한 프로퍼티 값을 배열로 반환한다.

```js
console.log(Object.value(person)); // ["Lee", "Seoul"]
```

- Object.entries : 객체 자신이 열거 가능한 프로퍼티 키와 값의 쌍을 배열로 반환한다.

```js
console.log(Object.entries(person)); // [["name", "address"], ["Lee", "Seoul"]]

Object.entries(person).forEach(([key, value]) => console.log(key, value));
/*
name Lee
address Seoul
*/
```
