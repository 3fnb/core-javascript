## 프로토타입 기본개념

- constructor: 새로운 객체 인스턴스를 어떻게 생성하는지 정의된 함수.
- new: constructor를 기반으로 새로운 객체 인스턴스를 생성하는 키워드.
- instance: constructor를 기반으로 만들어진 일시적 결과물.
- **proto**(던더프로토, double under proto) : 인스턴스에 자동으로 부여되는 프로퍼티. constructor의 prototype 프로퍼티를 참조함. 생략가능 프로퍼티로 인스턴스에서 **proto**안에 프로퍼티를 참조할 경우 생략하여도 정상적으로 참조가능하다.

<br>

<aside>
💡 결국, 생성자 함수의 prototype에 메서드나 프로퍼티가 있으면 인스턴스에서도 자신의 것처럼 해당 메서드나프로퍼티에 접근 가능하다!

</aside>

<br>

- 기본예시

```jsx
const Person = function (name) {
  this._name = name;
};

Person.prototype.getName = function () {
  return this._name;
};

const Root = new Person('Root');

Root.getName();
```

<br>

## 프로토타입 체인

### 메서드 오버라이드

프로토타입에 존재하는 메서드가 인스턴스에 존재하는 동일 이름의 메서드로 인하여 존재함에도 불구하고 가려지는 현상을 메서드 오버라이드라고 한다.

<br>

### 프로토타입체이닝

프로토타입 체인을 따라서 검색하는 것을 프로토타입 체이닝이라고 한다.

- Object.prototype에 메서드를 부여하면 프로토타입 체이닝으로 인해 number, boolean 같은 기본형 데이터타입에도 참조가능한 메서드가 되므로 객체에서만 사용할 수 없다. 따라서 Object에 직접 부여하여야 한다.
- Object.create(null)을 사용하여 객체를 생성하면 **porto**가 존재하지 않는 객체를 생성한다. 이는 프로토타입체이닝을 통한 메서드 접근이 불가능 하지만 객체 자체가 가벼워지는 성능상 이점이 존재한다.

<br>

### 다중 프로토타입 체인

생성자 함수의 prototype에 연결하고자하는 생성자함수(`Array`)의 인스턴스(`[]`)를 속성값으로 할당해주면 해당 생성자 함수의 메서드를 자유롭게 쓸 수 있게되는데 이를 다중 프로토타입 체인이라고 부른다

```jsx
const Grade = function () {
  const args = Array.prototype.slice.call(arguments);
  for (let i = 0; i < args.length; i++) {
    this[i] = args[i];
  }
  this.length = args.length;
};

Grade.prototype = [];

const g = new Grade(80, 100, 50);

console.log(g);
/*
Array {
  '0': 80,
  '1': 100,
  '2': 50,
  length: 3,
  __proto__: { length: 3 }
}
*/

g.push(70);

console.log(g);
/*
Array {
  '0': 80,
  '1': 100,
  '2': 50,
  '3': 70,
  length: 4,
  __proto__: { length: 4 }
}
*/
```
