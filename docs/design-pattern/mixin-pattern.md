---
layout: default
title: Mixin pattern
parent: 디자인 패턴
nav_order: 1
tags:
    - mixin
    - prototype
    - design pattern
---

# Mixin pattern
{: .no_toc }


## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

<aside>
💡 상속 없이 개체 또는 클래스에 기능 추가

</aside>

## 클래스 상속?

클래스 상속을 사용하면 클래스를 확장할 수 있다.(extends)

`Animal` Class

```jsx
class Animal {
  constructor(name) {
    this.speed = 0;
    this.name = name;
  }
  run(speed) {
    this.speed = speed;
    alert(`${this.name} 은/는 속도 ${this.speed}로 달립니다.`);
  }
  stop() {
    this.speed = 0;
    alert(`${this.name} 이/가 멈췄습니다.`);
  }
}

let animal = new Animal("동물");
```

관계도

![image](https://user-images.githubusercontent.com/43779571/183071459-777ca7fd-3fa7-44d0-952d-46721b0527d3.png)

`Rabbit` Class

```jsx
// 동물 관련 메서드가 담긴 Animal을 확장하여 Rabbit 클래스를 만든다.
class Rabbit extends Animal {
  hide() {
    alert(`${this.name} 이/가 숨었습니다!`);
  }
}

let rabbit = new Rabbit("흰 토끼");

// 확장으로 토끼도 동물이 할 수 있는 일반적인 동작 수행 가능
rabbit.run(5); // 흰 토끼 은/는 속도 5로 달립니다.
rabbit.hide(); // 흰 토끼 이/가 숨었습니다!
```

- 클래스 `Rabbit`을 사용해 만든 객체는 `rabbit.hide()` 같은 `Rabbit`에 정의된 메서드에도 접근할 수 있고, `rabbit.run()` 같은 `Animal`에 정의된 메서드에도 접근할 수 있다.

![image](https://user-images.githubusercontent.com/43779571/183071575-ea6fba28-c534-4933-9f3f-c9393c11814c.png)

- `rabbit.run`의 존재를 확인하는 과정
    - 객체 `rabbit`에 `run`이 있나 확인합니다. `run`이 없네요.
    - `rabbit`의 프로토타입인 `Rabbit.prototype`에 메서드가 있나 확인합니다. `hide`는 있는데 `run`은 없습니다.
    - `extends`를 통해 관계가 만들어진 `Rabbit.prototype`의 프로토타입, `Animal.prototype`에 메서드가 있나 확인합니다. 드디어 메서드 `run`을 찾았습니다.
- 자바스크립트의 내장 객체는 프로토타입을 기반으로 상속 관계를 맺습니다.

Class Multiple inheritance(Class 다중 상속)?

![image](https://user-images.githubusercontent.com/43779571/183071651-75687ed7-30ef-4bd8-bd56-3abe6edd01ef.png)

![image](https://user-images.githubusercontent.com/43779571/183071708-f624b2bb-bf2e-4558-bb96-b27b73adb94e.png)

## Mixin

*믹스* 인 은 특정 동작을 구현하는 메서드를 제공하지만 단독으로 사용하지 않고 다른 클래스에 동작을 추가하는 데 사용합니다.

### Attempt

1. 두 클래스를 혼합하기를 원함.
2. 클래스에 기능을 추가하고 싶음.

### 믹스인을 구현하는 가장 간단한 방법

유용한 메서드로 객체를 만들어 모든 클래스의 프로토타입으로 쉽게 병합할 수 있도록 한다.

```jsx
// 이름 속성을 갖는 Dog 클래스
class Dog {
  constructor(name) {
    this.name = name;
  }
}
// 다른 속성들도 필요할 수 있다.

--------------------------------------------------
// bark, wagTail, play 속성을 제공하는 mixin을 생성한다.
const dogFunctionality = {
  bark: () => console.log("Woof!"),
  wagTail: () => console.log("Wagging my tail!"),
  play: () => console.log("Playing!")
};

// 강아지 prototype에 dogFunctionality mixin을 추가한다.
Object.assign(Dog.prototype, dogFunctionality);
```

![image](https://user-images.githubusercontent.com/43779571/183071763-b50ec121-0673-433d-b1cf-f4ac25922163.png)

```
const pet1 = new Dog("Daisy");

pet1.name; // Daisy
pet1.bark(); // Woof!
pet1.play(); // Playing!
```

### 상속 + 믹스인

```jsx
class School {
  constructor(name){
    this.name = name;
  }
}

class Major {
  major() {
    console.log("전공을 찾아주세요")
  }
}

class Student extends School, Major {
  huhak() {
    console.log(`${this.name}에는 휴학제도가 없습니다`)
  }
}

const student1 = new Student("ㅁ대학");
console.log(student1.huhak())
console.log(Student.prototype)
```

```jsx
class School {
	constructor(name){
		this.name = name;
	}
}

const majorMixin = {
	major: () => { console.log("전공을 찾아주세요"); }
}

class Student extends School {
  huhak() {
    console.log(`${this.name}에는 휴학제도가 없습니다`)
  }
}

Object.assign(Student.prototype, majorMixin);

const student1 = new Student("ㅁ대학")

// now Student can try finding major~~
console.log(student1.huhak());
console.log(student1.major());
```

### 믹스인의 자체 내부에서 상속 사용

![image](https://user-images.githubusercontent.com/43779571/183071808-597d0dd2-992a-464e-9cc1-82acd15a798c.png)

```jsx
let sayMixin = {
  say(phrase) {
    alert(phrase);
  }
};

let sayHiMixin = {
  __proto__: sayMixin, // (or we could use Object.setPrototypeOf to set the prototype here)

  sayHi() {
    // call parent method
    super.say(`Hello ${this.name}`); // (*)
  },
  sayBye() {
    super.say(`Bye ${this.name}`); // (*)
  }
};

class User {
  constructor(name) {
    this.name = name;
  }
}

// copy the methods
Object.assign(User.prototype, sayHiMixin);

// now User can say hi
new User("Dude").sayHi(); // Hello Dude!
```

<!-- ## EventMixin -->

## 참고

[https://www.patterns.dev/posts/mixin-pattern/](https://www.patterns.dev/posts/mixin-pattern/)

[https://javascript.info/mixins](https://javascript.info/mixins)

[https://ko.javascript.info/class-inheritance](https://ko.javascript.info/class-inheritance)

[https://stackoverflow.com/questions/29879267/es6-class-multiple-inheritance](https://stackoverflow.com/questions/29879267/es6-class-multiple-inheritance)

[https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Object_prototypes](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Object_prototypes)

[https://stackoverflow.com/questions/9451881/prototype-vs-prototype-what-is-the-difference-mycons-proto-myco](https://stackoverflow.com/questions/9451881/prototype-vs-prototype-what-is-the-difference-mycons-proto-myco)