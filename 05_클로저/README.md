# 05 클로저

## 개요

들어가기에 앞서, 일반적인 클로저에 대해 알아보자.

사실 자바스크립트에선 모든게 클로저라고 할 수 있다. 모든 함수는 상위 스코프를 기억하므로.

```jsx
  function add1(a,b){
    return a+b;
  }

  let position = 0;

  @capture(poison) // 자바스크립트에선, 이런 데코레이터 없이 외부 변수에 접근이 가능하다...
  function add2(a,b){
    return a + b + poison;
  }
```

출처 [유튜브](https://www.youtube.com/watch?v=KmpofpqkitA&t=140s)

> 하지만, 그렇기에 일반적으로 자바스크립트에서 말하는 클로저는,  
> **1) 중첩되어 있는 함수가 상위스코프의 식별자를 참조하는데  
> 2) 그 중첩함수가 외부 함수보다 오래 유지 될 때**
> 그 중첩함수를 말한다.

출처 자바스크립트 딥다이브

<br><br>

책의 예제 5-3

```jsx
var outer = function () {
  var a = 1;
  var inner = function () {
    return ++a;
  };
  return inner;
};

var outer2 = outer();
console.log(outer2()); //2
console.log(outer2()); //3
```

위의 inner가 클로저이다.

## 클로저의 원리

어떻게 생명주기가 끝난 실행 컨텍스트의 식별자를 참조할 수 있는걸까?  
이유는,  
실행 컨텍스트가 콜스택에서 없어진다고 해서 그 렉시컬 환경이 없어지는것은 아니기 때문이다.  
가비지 컬렉터가 수집하지 않기 때문이다.  
어디선가 참조를 하고 있기 때문이다.

자바스크립트 엔진은 평가-실행 2단계를 거치며 소스코드를 평가한다.
평가 과정에서 실행컨텍스트 생성, 선언문을 실행해 스코프에 등록한다. 함수 정의가 평가될 때 함수 객체가 생성이 되고, 내부 슬롯 [[Environment]]은 실행중인 실행컨텍스트의 렉시컬 환경을 가르킨다. (함수 내부로 들어가 평가하지 않았으므로 함수의 실행컨텍스트는 생성되지 않는다)

이 내부슬롯이 가르키는 렉시컬 환경이 있기에 가비지 컬렉터가 변수를 수집하지 않으므로 클로저는 성립할 수 있다.

출처 자바스크립트 딥다이브  
(코어 자바스크립트 책에서는 선언시점에 미리 정보를 수집하는것이라고만 나와 조사했습니다.)

### **책에서 정의한** 클로저

1.  어떤 함수에서 선언한 변수를 참조하는 내부함수를 외부로 전달할 때, 어떤 함수의 실행컨텍스트가 종료 된 이후에도 그 변수가 사라지지 않는 현상
2.  이미 생명주기가 끝난 외부 함수의 변수를 참조하는 함수 (인사이드 자바스크립트)

## 클로저와 메모리 관리

메모리 누수: 개발자의 의도와 달리, 참조가 계속되어 가비지 콜렉터의 수거 대상이 되지 않음

클로저는 의도적으로 가비지 콜렉팅의 대상이 되지 않도록 하는것이지만, 대상이 되게 하고 싶다면 참조형이 아닌, 기본형 데이터를 (보통 null)입력하면 된다.

## 활용 사례

### 콜백함수 내부에서 외부 데이터 활용

1. 이벤트 리쓰너

```jsx
  var alertFruitBuilder = (fruit) =>{
    return () => {
      alert(fruit);
    }
  }

  fruits.foreach(fruit => {
    ..
      $li.addEventListener('click',alertFruitBuilder(fruit))
    ..
  })


  // 리액트에서 자주 쓰이는 패턴

jsx
  ...
  <button type="button" onClick={ ()=>setFruitBuilder(fruit)} >
  ...
```

2. 접근 제한자

다른 OOP 언어에 있는 private, public, protected는 자바스크립트에서 쓸 수 없는데 클로저를 이용해 비슷하게 구현 할 수 있다.
(접근 제한자를 이용해 클래스 내부에서만 인스턴스를 접근 할 수 있도록 해준다.)

```jsx
const Person = (name, age) => {
  this.name = name;
  let _age = age;

  this.increase = () => {
    console.log(`이름은 ${this.name}이고요 나이는 ${_age}입니다.`);
  };

  const me = Person('3fnb', 1);
  console.log(me.name);
  console.log(me._age);
  me.increase();
};
```

3. 부분 적용 함수

- 여러개의 인자를 받는 함수에 미리 몇개의 인자를 넘기고, 기억시킨 후 나중에 나머지 인자를 넘겨 실행 결과를 받는 함수

```jsx
Object.defineProperty(window, '_', {
  value: 'EMPTY_SPACE',
  writable: false,
  configurable: false,
  enumerable: false,
});

const partial = function (...rest) {
  const [func, ...originalRest] = rest;
  if (typeof func !== 'function') throw new Error('첫번째 인자가 함수가 아닙니다.');
  return (...newRest) => {
    const arrLength = originalRest.length;
    for (let i = 0; i < arrLength; i++) {
      if (originalRest[i] === _) {
        originalRest.splice(i, 1);
      }
    }
    return func.apply(this, [...originalRest, ...newRest]);
  };
};

const add = (...rest) => {
  let result = 0;
  for (el of rest) {
    result += el;
  }
  return result;
};

const addPartial = partial(add, _, 4, 5, _, 1);
console.log(addPartial(1, 2));
```

디바운스

```jsx
const debounce = function (eventName, func, wait) {
  let timeoutId;
  return function (event) {
    let self = this;
    clearTimeout(timeoutId);
    timeoutId = setTimeout(func.bind(self, event), wait);
  };
};

const moveHandler = e => console.log('move');

document.body.addEventListener('mousemove', debounce('move', moveHandler, 1000));
```

책의 예시를 es6문법으로 바꿨습니다.

1. 커링 함수

```jsx
const curry5 = func => a => b => c => func(a, b, c);
```
