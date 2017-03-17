Javascript
===

스크립트 삽입
---
```html
<script>
...
</script>
```



Type
---

- `typeof` 사용하기
http://devdocs.io/javascript/operators/typeof
  - Primitives와 Object만 활용
  https://developer.mozilla.org/en-US/docs/Glossary/Primitive

```javascript
typeof undefined             // undefined
typeof null                  // object
null === undefined           // false
null == undefined            // true
```

- .toString 이용하기

```javascript
Object.prototype.toString.call({}); //"[object Object]"
Object.prototype.toString.call([1,2,3]); //"[object Array]"
Object.prototype.toString.call(new Date); //"[object Date]"
Object.prototype.toString.call(/a-z/); //"[object RegExp]"
```

Falsey values
---

- https://developer.mozilla.org/en-US/docs/Glossary/Falsy
- Boolean 컨텍스트에서 `if()` 사용시 `false`로 평가
```javascript
/* falsy values*/
false
0
""
null
undefined
NaN
```

NaN
---
http://devdocs.io/javascript/global_objects/nan

```javascript
NaN === NaN;        // false
Number.NaN === NaN; // false
isNaN(NaN);         // true
isNaN(Number.NaN);  // true
isNaN(undefined); // true
isNaN({});        // true
isNaN(true);      // false
isNaN(null);      // false
```

Dynamic Typing
---
- javascript is a *loosely typed* language.
- 실행 시runtime에 타입을 알 수 있다.  

```java
int num = 5;
float val  = 3.14;
boolean isActive = false;
String text ="Javascript는 Java와 아무 상관이 없다.";
```

```javascript
var num = 5;
var val  = 3.14;
var isActive = false;
var text ="Javascript는 Java와 아무 상관이 없다.";

typeof num; //number
num = 'five';
typeof num; //string
```



Hoisting
---
- javascript는 같은 scope 안에서 변수를 나중에 선언declaration 해도 된다. => 끌어올림 Hoisting 되었다.


```javascript
x = 5; //할당 assignment
console.log(x);
var x; //선언 declaration
```

- declaration은 hoisting 되지만, 초기화initializations는 그렇지 않다.

```javascript
var x = 5; // 초기화 Initialize x
console.log(x,y);
var y = 7; // 초기화 Initialize y
```


Function-level Scoping
---
- C나 Java와 같은 block scope({} 밖에서는 안을 알 수 없음)대신 function-level scope을 사용
- function을 기준으로 scope이 결정됨

```javascript
var foo = function() {
  var a = 3, b = 5;
  var bar = function() {
    var b = 7, c = 11;
    a += b+ c;
  }
  bar();
  console.log(a, b, c);
}
foo();
```

```javascript
function foo() {
  var x = 4;
  if(x == 4)
  {
    var y = 15;
    for (var i = 0; i <= x; i++)
    {
      var j = i;
    }
  }

  console.log(y, i, j);

}
foo();
```

Hoisting
---
https://developer.mozilla.org/en-US/docs/Glossary/Hoisting
- 코드의 실행 전, 컴파일 단계에서 scope 안에 있는 `function, var`가 미리 메모리에 선언
- `function`의 경우 정의까지 됨

```javascript
catName("Chloe");

function catName(name) {
  console.log("My cat's name is " + name);
}
```

```javascript
console.log(typeof fun);
var fun = 3;
console.log(typeof fun);
function fun(){}
console.log(typeof fun);
```

Closure
---
- [클로저](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Closures)는 독립적인 (자유) 변수를 가리키는 함수이다. 또는, 클로저 안에 정의된 함수는 만들어진 환경을 '기억한다'.
- 함수가 메모리 상에 남아있는 한 환경도 계속 기억

```javascript
function init() {
  var name = "홍길동"; // init에 있는 지역 변수 name
  function displayName() { // 내부 함수, 즉 클로저인 displayName()
    console.log(name); // 부모 함수에 정의된 변수를 사용한다
  }
  displayName();
}
init();
```

```javascript
function makeFunc() {
  var name = "홍길동";
  function displayName() {
    console.log(name);
  }
  return displayName;
}

var myFunc = makeFunc();
myFunc();
```

```javascript
var person = function(name) {
  return {
    showName: function() {
      return name;
    }
  }
}
var gilDong = person('홍길동');
console.log(gilDong.showName());
```

```javascript
function makeAdder(x) {
  return function(y) {
    return x + y;
  };
}

var add5 = makeAdder(5);
var add10 = makeAdder(10);

console.log(add5(2));  // 7
console.log(add10(2)); // 12
```

*[클로져 응용 퀴즈](./00_quiz.md)*


function context
---
- `this` 키워드는 함수가 실행 될때의 `function context`를 가리킨다.

- 전역 환경에서 실행될 때
```javascript
function foo() {
  return this;
}
console.log(window === foo());
'use strict';
function foo2() {
  return this;
}
console.log(undefined === foo2());
```

- 객체 속성method으로 실행될 때
```javascript
var a = {
  foo: function() {
    return this;
  }
}

console.log (a === a.foo());
```


- 생성자constructor 에서 사용될 때
```javascript
function Ninja() {
  this.foo = function () {
    return this;
  };
}
var ninja1 = new Ninja();
console.log(ninja1 === ninja1.foo());
```


- `apply,call` 통해 사용될 때

```javascript
function foo() {
  return this.name;
}
console.log(foo());
var a= {
  name: 'foo'
}
console.log(foo.apply(a));
```
