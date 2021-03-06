# 01 데이터 타입
---

## 데이터 타입의 종류

- **기본형** : 숫자, 문자열, 불리언, null, undefined 등이 있다. ES6에서 symbol이 추가되었다.
- **참조형** : 객체, 배열, 함수, 날짜, 정규표현식 등이 있다. ES6에서 Map, WeakMap, Set, WeakSet 등이 추가되었고, 객체의 하위 분류에 속한다.

기본형은 주솟값을 바로 복제, 참조형은 주솟값들의 묶음을 가리키는 주솟값을 복제한다는 점에서 차이가 있다.

## 메모리와 데이터

모든 데이터는 메모리 주솟값을 통해 서로 구분하고 연결한다.

## 식별자와 변수

변수는 변할 수 있는 데이터이며 식별자는 변수명이다.

## 변수 선언과 데이터 할당

변수를 선언하고 데이터를 할당할 때, 데이터를 저장하기 위한 별도의 메모리 공간에 저장하고, 그 주소를 변수영역에 저장한다. 이처럼 변수와 데이터를 별도의 공간에 저장하는 것은 데이터 변환을 자유롭게 하게 하고 메모리를 효율적으로 관리하기 위함이다. 이렇게 되면 중복된 데이터에 대한 처리 효율이 높아진다.

## 기본형 데이터와 참조형 데이터

- **불변값** : 기본형 데이터 숫자, 문자열, 불리언, null, undefined 등은 모두 불변값이다. 변경은 새로 만드는 동작을 통해서만 이루어진다. 가비지 컬렉팅을 하지 않는 이상 변하지 않는다. 데이터 영역에 저장된 값은 모두 불변값이다.
- **가변값** : 참조형 데이터는 가변값이다. 객체의 변수 영역이 별도로 존재하기 때문에 가변값이다. 참조형 데이터의 메모리는 변수영역 - 데이터영역 - 객체의 변수 영역으로 이루어진다.
- **참조 카운트** : 어떤 데이터에 대해 자신의 주소를 참조하는 변수의 개수이다. 0이 되면 가비지 컬렉터가 수거해 메모리는 빈 공간이 된다.

변수 복사 이후 객체의 프로퍼티를 변경했을 때 기본형은 값이 달라지고 참조형은 값이 달라지지 않는다. 참조형 데이터는 수정된 값이 객체의 변수영역에 담겨있기 때문이다. 엄밀히 말하면 모든 데이터 타입은 참조형 데이터일 수 밖에 없다. 기본형도 주솟값을 복사하는 과정이 한 번 이루어지기 때문이다. 참조형 데이터도 객체의 프로퍼티가 아닌 새로운 객체를 할당하면 값이 달라진다.

## 불변 객체

데이터 자체를 변경하고자 하면 기본형과 참조형 모두 기존 데이터는 변하지 않는다. 원본 객체가 변하지 않아야 하는 경우 불변 객체가 필요하다.

- 얕은 복사 : 바로 아래 단계 값만 복사
- 깊은 복사 : 내부 모든 값 하나하나 찾아 전부 복사

참조형 데이터를 가변값으로 여겨야 하는 경우 깊은 복사를 하거나 해당 기능을 제공하는 라이브러리를 사용하면 된다.

## undefined와 null

undefined와 null은 모두 없음을 나타내지만, 의미와 사용하는 목적이 다르다.

- undefined : 값을 대입하지 않은 변수, 객체 내부의 존재하지 않는 프로퍼티에 접근, return문이 없거나 호출되지 않은 함수의 실행 결과에 자바스크립트 엔진이 자동으로 부여한다.
- null : 비어있음을 명시적으로 나타내고 싶을 때 사용한다.

혼란을 막기 위해 명시적으로 undefined를 대입하는 것을 지양해야한다. 또한, typeof null은 object이다.(자바스크립트 자체 버그) 따라서 null과 undefined 비교시 ==(동등 연산자)가 아닌 ===(일치 연산자)를 사용해야 한다.
