# this

## 자바스크립트 외의 언어

자바에서의 this는 인스턴스(객체) 자신에 대한 참조이다.
파이썬에서는 이와 비슷하게 self를 사용한다.

```Java
public Class Person {

  private String name;

// 클래스 명과 같은 메서드는 생성자 함수임
  public Person(String name) {
    // this.name은 멤버 변수 클래스 Person의 변수 name이다.
    // name은 생성자 함수가 전달받은 매개변수이다
    this.naem = name;
  }
}
```

```python
class Person:
    def __init__(self, name):
      # self.name은 클래스 Person의 변수 name
      # name은 생성자 함수가 전달받은 매개변수
        self.name = name
```

## 자바스크립트

자바스크립트는 함수 호출 방식에 따라 this에 바인딩되는 객체가 달라진다.

### 함수 호출 방식

1. 함수 호출
2. 메서드 호출
3. 생성자 함수 호출
4. apply/call/bind 호출

```javascript
var foo = function () {
  console.dir(this);
};

// 1. 함수 호출
foo(); // window
// window.foo();

// 2. 메서드 호출
var obj = { foo: foo };
obj.foo(); // obj

// 3. 생성자 함수 호출
var instance = new foo(); // instance

// 4. apply/call/bind 호출
var bar = { name: "bar" };
foo.apply(bar); // bar
foo.call(bar); // bar
foo.bind(bar)(); // bar
```

### 함수 호출

기본적으로 전역 객체에 바인딩 된다.  
주의할 점으로 내부함수는 일반함수, 메서드, 콜백함수 어디에서 선언되었든 this는 전역객체를 바인딩한다.

```javascript
var value = 1;

var obj = {
  value: 100,
  foo: function () {
    console.log("foo's this: ", this); // obj
    console.log("foo's this.value: ", this.value); // 100
    function bar() {
      console.log("bar's this: ", this); // window
      console.log("bar's this.value: ", this.value); // 1
    }
    bar();
  },
};

obj.foo();
```

이 때 내부함수에서 `this`가 전역 객체를 바인딩하는 것을 회피하는 방법은 아래와 같다.

```javascript
var value = 1;

var obj = {
  value: 100,
  foo: function () {
    var that = this; // Workaround : this === obj

    console.log("foo's this: ", this); // obj
    console.log("foo's this.value: ", this.value); // 100
    function bar() {
      console.log("bar's this: ", this); // window
      console.log("bar's this.value: ", this.value); // 1

      console.log("bar's that: ", that); // obj
      console.log("bar's that.value: ", that.value); // 100
    }
    bar();
  },
};

obj.foo();
```

`foo`에 있는 `this`는 객체에 바인딩 된다. 따라서 `foo`에 있는 변수 `that`을 내부함수 `bar`에서 호출하면 객체를 가리키게 된다.

**주의사항 : 위의 코드는 브라우저에서 작동되는 자바스크립트에서 동작하는 코드이다. node는 작성된 코드를 하나의 함수로 만들어 실행하기에 전역에서 선언한 변수여도 전역 객체인 `global`에 등록되지 않는다. 따라서 위의 코드를 실행해도 `bar's this.value`는 `undefined`라고 출력된다. node에서 전역 객체인 `global`에 등록하기 위해서는 변수 선언 키워드(var, let, const)를 생략하거나 직접 `global`에 프로퍼티로 등록하는 방법을 사용한다. 이렇게 전역 객체에 등록하게 되면 내부 함수의 `this`, 즉 global 에 바인딩되어 `bar's this.value`에서 1이라고 출력된다.**

### 메서드 호출

함수가 객체의 프로퍼티 값이면 메서드로 호출된다. 이 때 메서드 내부의 `this`는 해당 메서드를 소유한 객체, 해당 메서드를 호출한 객체에 바인딩 된다.

```javascript
var obj1 = {
  name: "Lee",
  sayName: function () {
    console.log(this.name);
  },
};

var obj2 = {
  name: "Kim",
};

obj2.sayName = obj1.sayName;

obj1.sayName(); // Lee
obj2.sayName(); // Kin
```

프로토타입 객체도 메서드를 가질 수 있는데 이 또한 일반 메서드와 같이 해당 메서드를 호출한 객체에 바인딩 된다.

### 생성자 함수 호출

자바스크립트의 생성자 함수는 형식이 정해져 있지 않다. **기존 함수에 new 연산자를 붙여 호출하면 해당 함수는 생성자 함수로 동작한다.**

반대로 일반 함수에 new 연산자를 붙일 경우 생성자 함수처럼 동작할 수 있어서 혼란을 방지하기 위해 생성자 함수명은 첫문자는 대문자로 기술한다.

```javascript
// 생성자 함수
function Person(name) {
  this.name = name;
}

var me = new Person("Lee");
console.log(me); // Person {name: "Lee"}

// new 연산자와 함께 생성자 함수를 호출하지 않으면 생성자 함수로 동작하지 않는다.
var you = Person("Kim");
console.log(you); // undefined
```

#### 생성자 함수 동작 방식

1. 빈 객체 생성 및 this 바인딩

   생성자 함수의 코드가 실행되기 전에 빈 객체가 생성된다. 이후 생성자 함수 내에서 사용되는 this는 이 빈 객체를 가리킨다. 생성된 빈 객체는 생성자 함수의 `prototype` 프로퍼티가 가리키는 객체를 자신의 프로토 타입 객체로 설정한다.

2. this를 통해 프로퍼티 생성

   생성된 빈 객체에 this를 사용해 프로퍼티나 메서드를 생성할 수 있다. this는 새로 생성된 객체를 가리키므로 this를 통해 생성한 프로퍼티와 메서드는 새로 생성된 객체에 추가된다.

3. 생성된 객체 반환

   - 반환문이 없는 경우 this에 바인딩된 새로 생성한 객체가 반환된다. 명시적으로 this를 반환해도 같은 결과
   - 반환문이 this가 아닌 다른 객체를 반환하는 경우 this가 아닌 해당 객체가 반환된다. 이는 생성자 함수의 역할을 제대로 수행하지 못하기 때문에 생성자 함수는 반환문을 명시적으로 사용하지 않는다.

따라서 생성자 함수에 new를 붙이지 않거나 일반 함수에 new를 붙일 경우 문제가 발생할 수 있다. 생성자 함수의 this는 새로 생성되는 객체를 가리키지만 일반 함수의 this는 전역 객체를 가리키기 때문이다.

### apply/call/bind 호출

this에 바인딩될 객체는 함수 호출 패턴에 의해 결정되고 이는 자바스크립트 엔진이 수행한다. 이처럼 암묵적 this 바인딩 외에 this를 특정 객체에 명시적으로 바인딩하는 방법도 존재한다. `Function.prototype.apply`, `Function.prototype.call` 메서드를 사용하면 된다.

```javascript
var Person = function (name) {
  this.name = name;
};

var foo = {};

// apply 메소드는 생성자함수 Person을 호출한다. 이때 this에 객체 foo를 바인딩한다.
Person.apply(foo, ["name"]);

console.log(foo); // { name: 'name' }
```

원래라면 생성자 함수 `Person`이 실행되면서 빈 객체를 생성하고 `this`가 새로 생성된 객체에 바인딩되어야 한다. 하지만, `apply`를 사용하여 `Person`의 `this`에 `foo`를 바인딩하였기 때문에 `foo`에 `name`프로퍼티를 추가하였다.

`apply()` 메서드는 대표적으로 `arguments`객체와 같은 유사 배열 객체에 배열 메서드를 사용하는데에 사용할 수 있다.

```javascript
function convertArgsToArray() {
  console.log(arguments);

  // arguments 객체를 배열로 변환
  // slice: 배열의 특정 부분에 대한 복사본을 생성한다.
  var arr = Array.prototype.slice.apply(arguments); // arguments.slice
  // var arr = [].slice.apply(arguments);

  console.log(arr);
  return arr;
}

convertArgsToArray(1, 2, 3);
```

위 코드에서 `Array.prototype.slice.apply(arguments)`는 `Array.prototype.slice()`를 실행할건데, `Array.prototype.slice()`의 this에 `arguments` 객체를 바인딩 한다. 라는 의미이다. `arguments`는 함수 실행시에 인자와 함께 전달되는 유사 배열이다.

`call()`메서드의 경우 `apply()`와 기능은 같지만 사용 방법이 다르다. `apply()`에서는 두번째 인자를 배열형태로 넘긴 것을 `call()`에서는 각각 하나의 인자로 넘긴다.

```javascript
func.apply(thisArg, [argsArray]);

// thisArg: 함수 내부의 this에 바인딩할 객체
// argsArray: 함수에 전달할 argument의 배열

Person.apply(foo, [1, 2, 3]);

Person.call(foo, 1, 2, 3);
```

`Function.prototype.bind` 메서드는 함수에 인자로 전달한 this가 바인딩된 새로운 함수를 리턴한다. 이는 함수를 실행하지는 않기 때문에 명시적으로 함수를 호출해야 한다.

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.doSomething = function (callback) {
  if (typeof callback == "function") {
    // callback.call(this);
    // this가 바인딩된 새로운 함수를 호출
    callback.bind(this)();
  }
};

function foo() {
  console.log("#", this.name);
}

var p = new Person("Lee");
p.doSomething(foo); // 'Lee'
```
