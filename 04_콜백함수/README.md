# 04 콜백 함수

### 콜백 함수란?

* 콜백 함수는 다른 코드(함수 또는 메서드)에게 인자로 넘겨줌으로써 그 제어권도 위임한 함수다.

### 제어권

* 콜백 함수의 제어권을 넘겨받은 코드는 콜백 함수 호출 시점에 대한 제어권을 가진다.

### 호출시점

* setInterval
```
var intervalID = scope.setInterval(func, delay[, param1, param2, ...]);
```

setInterval 콜백 함수의 제어권을 살펴보자.

```
var count = 0;
var cbFunc = function () {
  console.log(count);
  if(++count > 4) clearInterval(timer);
};
var timer = setInterval(cbFunc, 300);
```

코드를 실행하면 0.3초에 한 번씩 숫자가 0부터 1씩 증가하다 4가 출력된 이후 종료된다. 
cbFunc가 아닌 setInterval에 cbFunc 함수를 넘겼고, setInterval은 콜백 함수 호출 시점에 대한 제어권을 갖게 되었다.

- 인자: 콜백 함수를 호출할 때 인자로 넘겨줄 값들 및 순서는 정해져있으며, 순서를 따라야 한다.
- this: 콜백 함수도 함수니까 this는 전역객체를 참조하지만, 제어권을 넘겨받을 코드에서 콜백 함수에 this가 될 대상을 지정한 경우 그 대상을 참조한다.
---

콜백 함수로 어떤 함수의 인자에 객체의 메서드를 전달해도 그 메서드는 함수로서 호출된다.
```
var obj = {
  vals: [1, 2, 3],
  logvalues: function(v,i) {
    console.log(this, v, i);
  }
};

obj.logvalues(1, 2); //메서드로 호출, this는 obj를 가리킴

[4, 5, 6].forEach(obj.logValues); //this는 전역객체

// logValues메서드를 forEach 함수의 콜백 함수로 전달.
// obj.logvalues가 가리키는 함수만 전달. 
// forEach에 의해 콜백이 함수로 호출됨.
```
--- 
위와 같이, 메서드를 콜백 함수로 전달하면 this는 전역객체를 바라본다. this가 객체를 바라보게 하고 싶다면 ES5에서 등장한 bind 메서드를 이용할 수 있다.

```
var obj1 = {
  name: 'obj1',
  func: function () {
    console.log(this.name);
  }
};
setTimeout(obj1.func.bind(obj1), 1000); //"obj1"
```

## 콜백 지옥과 비동기 제어
### 콜백 지옥
콜백 함수를 익명 함수로 전달하는 과정이 반복되어 코드의 들여쓰기 수준이 감당하기 힘들 정도로 깊어지는 현상이다. 자바스크립트에서 흔히 발생하며 가독성이 떨어지고 코드 수정이 어렵다.
### 동기적인 코드
현재 실행 중인 코드가 완료된 후에 다음 코드 실행. 즉시 처리가 가능한 대부분의 코드.
### 비동기적인 코드 
현재 실행 중인 코드의 완료 여부와 무관하게 즉시 다음 코드로 넘어감. setTimeout, addEventListener 등과 같이 별도의 요청/실행대기/보류 등과 관련된 코드.

#### callback hell
```
setTimeout(function (name) {
  var gimbapList = name;
  console.log(gimbapList);
  
  setTimeout(function (name) {
  	gimbapList += ',' + name;
  	console.log(gimbapList);
    
    setTimeout(function (name) {
  		gimbapList += ',' + name;
  		console.log(gimbapList);
      
      	setTimeout(function (name) {
  			gimbapList += ',' + name;
  			console.log(gimbapList);
        }, 500, '치즈김밥');
     }, 500, '참치김밥');
  }, 500, '떡갈비김밥');
}, 500, '야채김밥');
/*
"야채김밥"
"야채김밥,떡갈비김밥"
"야채김밥,떡갈비김밥,참치김밥"
"야채김밥,떡갈비김밥,참치김밥,치즈김밥"
*/
```

#### 익명의 콜백함수를 기명함수로 전환
```
var gimbapList = '';

var add1 = function(name) {
  gimbapList = name;
  console.log(gimbapList);
  setTimeout(add2, 500, '떡갈비김밥')
};
var add2 = function(name) {
  gimbapList += ',' + name;
  console.log(gimbapList);
  setTimeout(add3, 500, '참치김밥')
};
var add3 = function(name) {
  gimbapList += ',' + name;
  console.log(gimbapList);
  setTimeout(add4, 500, '치즈김밥')
};
var add4 = function(name) {
  gimbapList += ',' + name;
  console.log(gimbapList);
};

setTimeout(add1, 500, '야채김밥')
/*
"야채김밥"
"야채김밥,떡갈비김밥"
"야채김밥,떡갈비김밥,참치김밥"
"야채김밥,떡갈비김밥,참치김밥,치즈김밥"
*/
```

자바스크립트 진영은 비동기적인 작업을 동기적으로, 혹은 동기적인 것처럼 보이게 처리해주는 장치를 마련하고자 노력하고 있다. ES6에서는 Promise, Generator 등이 도입됐고, ES2017에서는 async/await가 도입됐다.

#### Promise
promise는 비동기 작업이 맞이할 미래의 완료 또는 실패와 그 결과 값을 나타낸다. 비동기 작업이 완료될 때 resolve/reject를 호출하는 방법으로 동기적 표현이 가능하며, 둘 중 하나가 실행되기 전까지는 다음 또는 오류 구문으로 넘어가지 않는다.

- resolve : 주어진 값으로 이행하는 .then 반환
- reject : 거부된 promise를 반환
- then() : promise를 리턴하고 두 개의 콜백 함수를 인자로 받음(이행했을 때, 거부했을 때)

```
new Promise(function (resolve) {
  setTimeout(function () {
    var name = '야채김밥';
    console.log(name);
    resolve(name);
  }, 500);
}).then(function (prevName) {
  return new Promise(function (resolve) {
  	setTimeout(function () {
    	var name = '떡갈비김밥';
    	console.log(name);
    	resolve(name);
  	}, 500);
  });
}).then(function (prevName) {
  return new Promise(function (resolve) {
  	setTimeout(function () {
    	var name = '참치김밥';
    	console.log(name);
    	resolve(name);
  	}, 500);
  });
}).then(function (prevName) {
  return new Promise(function (resolve) {
  	setTimeout(function () {
    	var name = '치즈김밥';
    	console.log(name);
    	resolve(name);
  	}, 500);
  });
});
/*
"야채김밥"
"떡갈비김밥"
"참치김밥"
"치즈김밥"
*/
```

### Generator
generator함수를 실행하면 iterator를 반환한다. iterator는 next메서드를 가지는데, 이 메서드를 호출하면 generator함수 내부에서 가장 먼저 등장하는 yield에서의 함수 실행을 멈춘다.
```
var addGimbap = function (prevName, name) {
  setTimeout(function () {
    gimbapMaker.next(prevName ? prevName + ',' + name : name);
  }, 500);
};
var gimbapGenerator = function* () {
  var vegGimbap = yield addGimbap('', '야채김밥');
  console.log(vegGimbap);
  var galbiGimbap = yield addGimbap('', '떡갈비김밥');
  console.log(galbiGimbap);
  var tunaGimbap = yield addGimbap('', '참치김밥');
  console.log(tunaGimbap);
  var cheeseGimbap = yield addGimbap('', '치즈김밥');
  console.log(cheeseGimbap);
};
var gimbapMaker = gimbapGenerator();
gimbapMaker.next();
/*
"야채김밥"
"떡갈비김밥"
"참치김밥"
"치즈김밥"
*/
```

#### promise + Async/await
- async : 비동기 작업을 수행하고자 하는 함수 앞에 표기
- await : 함수 내부에서 실질적인 비동기 작업이 필요한 위치마다 표기
이 작업으로 뒤의 내용을 promise로 자동 전환할 수 있다. 즉, promise의 then같은 효과를 얻을 수 있다.

```
var addGimbap = function (name) {
  return new Promise(function (resolve) {
    setTimeout(function () {
      resolve(name);
    }, 500);
  });
};
var gimbapMaker = async function () {
  var gimbapList = '';
  var _addGimbap = async function (name) {
    gimbapList += (gimbapList ? ',' : '') + await addGimbap(name);
  };
  await _addGimbap('야채김밥');
  console.log(gimbapList);
  await _addGimbap('떡갈비김밥');
  console.log(gimbapList);
  await _addGimbap('참치김밥');
  console.log(gimbapList);
  await _addGimbap('치즈김밥');
  console.log(gimbapList);
};
gimbapMaker();
/*
"야채김밥"
"야채김밥,떡갈비김밥"
"야채김밥,떡갈비김밥,참치김밥"
"야채김밥,떡갈비김밥,참치김밥,치즈김밥"
*/
```
