# 클래스와 인스턴스의 개념 이해
`클래스`는 공통 요소를 지니는 집단을 분류하기 위한 개념이고, `인스턴스`는 클래스의 속성을 지니는 구체적인 사례다. 상위클래스(superclass)의 조건을 충족하면서 더 구체적인 조건이 추가된 것을 하위클래스(subclass)라고 한다. 클래스가 먼저 정의돼야만 그로부터 공통적인 요소를 지니는 개체들을 생성할 수 있으며, 한 인스턴스는 하나의 클래스만을 바탕으로 만들어진다. 
# 자바스크립트의 클래스
자바스크립트에는 클래스의 개념이 존재하지 않는다. 프로토타입에 클래스 개념을 적용해보면 아래와 같다. 
![](https://images.velog.io/images/dabin0219/post/9d39bb9b-b6b9-4812-9559-7a8502e865b6/arguments1024_1.jpg)
인스턴스에서 직접 호출할 수 있는 메서드가 프로토타입 메서드고, 인스턴스에서 직접 접근할 수 없는 메서드가 스태틱 메서드다. 스태틱 메서드는 인스턴스가 직접 호출할 수 없고 클래스(생성자 함수)에 의해서만 호출할 수 있다.
![](https://images.velog.io/images/dabin0219/post/75e57be4-5c77-4e47-a129-3db247ac57e0/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%A2%E1%86%AB%E1%84%83%E1%85%AC%E1%86%AB%20%E1%84%86%E1%85%AE%E1%86%AB%E1%84%89%E1%85%A51024_1.jpg)
# 클래스 상속
자바스크립트에서 클래스 상속을 구현했다는 것은 프로토타입 체이닝을 잘 연결한 것으로 이해하면 된다. 위에서 본 다중 프로토타입 체이닝의 Grade에서와 같이, 하위 클래스로 삼을 생성자 함수의 prototype에 상위 클래스의 인스턴스를 부여하는 것으로 기본적인 메서드 상속은 가능하다. 하지만 이는 다양한 문제를 가지며 구조적 안정성이 떨어진다. Grade에서는 length 프로퍼티가 삭제 가능하고 Grade.prototype에 빈 배열을 참조시켰다는 점이 문제가 된다. 즉, 클래스에 있는 값이 인스턴스에 영향을 주는 구조라는 문제를 지닌다. 이를 해결하는 여러가지 방법을 알아보자. 
### 클래스가 구체적인 데이터를 지니지 않게 하는 방법
일단 만들고 나서 프로퍼티들을 일일이 지우고(delete) 새로운 프로퍼티를 추가할 수 없게(freeze) 만든다. 
```javascript
delete Square.prototype.width;
delete Square.prototype.height;
Object.freeze(Square.prototype);
```
Subclass의 prototype에 직접 SuperClass의 인스턴스를 할당하는 대신, 프로퍼티를 생성하지 않는 빈 생성자 함수 Bridge를 만들어 그 prototype이 SuperClass의 prototype을 바라보게 한 다음, SubClass의 prototype에는 Bridge의 인스턴스를 할당하게 하는 방법도 있다. 이렇게 되면 인스턴스를 제외한 프로토타입 체인 경로상에 구체적인 데이터가 남아있지 않게 된다. 
```javascript
var extendClass2 = (function () {
  var Bridge = function () {};
  return function (SuperClass, SubClass, subMethods) {
    Bridge.prototype = SuperClass.prototype;
    Subclass.prototype = new Bridge();
    if (subMethods) {
      for (var method in subMethods) {
        SubClass.prototype[method] = subMethods[method];
      }
    }
    Object.freeze(subclass.prototype);
    return SubClass;
  };
})();
```
이보다 간단하게 하려면 Object.create를 이용하자.
```javascript
Square.prototype = Object.create(Rectangle.prototype);
Object.freeze(Square.prototype);
```
> 위의 방법들은 기본적인 상속에는 성공했지만, SubClass 인스턴스의 constructor는 여전히 SuperClass를 가리킨다. 원래의 Subclass를 바라보도록 만들어보자.
```javascript
var extendClass2 = (function () {
  var Bridge = function () {};
  return function (SuperClass, SubClass, subMethods) {
    Bridge.prototype = SuperClass.prototype;
    Subclass.prototype = new Bridge();
    Subclass.prototype.constructor = SubClass; //여기!!!! 여기!!!여기!!
    if (subMethods) {
      for (var method in subMethods) {
        SubClass.prototype[method] = subMethods[method];
      }
    }
    Object.freeze(subclass.prototype);
    return SubClass;
  };
})();
```
위에서 언급한 다른 방법을 사용했더라도 Subclass.prototype.constructor = SubClass; 를 통해 복구해주면 된다. 

# ES6의 클래스 및 클래스 상속
ES6에서는 본격적으로 클래스 문법이 도입됐다.
```javascript
//class 명령어 뒤 바로 {}, 내부가 클래스 본문 영역.
//class 본문에서 function 생략해도 모두 메서드로 인식.
//constuctor는 생성자 함수와 동일한 역할
//method와 method 사이는 콤마로 구분하지 않음.
var Rectangle = class {
  constructor (width, height) {
    this.width = width;
    this.height = height;
  }
  getArea () {
    return this.width * this.height;
  }
};
//Square을 Rectangle 클래스를 상속받는 SubClass로
//extends Rectangle : 이거 하나로 상속 관계 설정 끝!
var Square = class extends Rectangle {
  constructor (width) {
    super(width, width); 
  } //super을 함수처럼 사용, SuperClass의 constructor 실행
  getArea () {
    console.log(super.getArea())'
  }
};
/*
constructor 메서드를 제외한 다른 메서드에서 super 키워드를 마치 객체처럼 사용할 수 있음.
이때 객체는 SuperClass.prototype을 바라보는데, 
호출한 메서드의 this는 'super'이 아닌 원래의 this를 따름
*/
```
