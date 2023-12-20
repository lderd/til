# var

ES5까지의 변수 선언 방식.

1. 함수 레벨 스코프
   
   - var는 함수 레벨 스코프이다.

2. 키워드 생략 허용
   
   - var 키워드를 사용하지 않는다면 암묵적 전역 변수를 생성해 할당한다.

3. 중복 선언 허용
   
   - 같은 이름의 변수를 선언할 수 있어 의도하지 않은 변수값의 변경이 일어날 수 있다.

4. 호이스팅
   
   - 스크립트 최상단에서 선언과 초기화를 한번에 하기 때문에 선언문 이전에 변수에 접근할 수 있다.

# let, const

ES6에서 새로 등장한 키워드.

## let

블록 레벨 스코프, 재할당 가능(값 변경 가능), 중복 선언 금지, 변수의 선언과 초기화 분리

### 스코프

- 함수 레벨 스코프 : 함수 내에서 선언된 변수는 함수 내에서만 유효하며 함수 외부에서는 참조할 수 없다. 함수 내부에서 선언한 변수는 지역 변수, 함수 외부에서 선언한 변수는 전역 변수 이다.

- 블록 레벨 스코프 : 모든 코드 블록(함수, if문, while문, try/catch문 등) 내에서 선언된 변수는 코드 블록 내에서만 유효하며 코드 블록 외부에서는 참조할 수 없다. 코드블록 내부에서 선언한 변수는 지역 변수이다.

```js
var foo = 123; // 전역변수
{
  var foo = 456; // 전역변수
}

let bar = 123; // 전역변수
{
  let bar = 456; // 지역변수
}
```

### 중복 선언 금지

```js
var foo = 123;
var foo = 456; // 중복 선언 허용, 의도와는 다르게 앞서 선언한 foo의 값이 456으로 바뀜

let bar = 123;
let bar = 456; // Uncaught SyntaxError: Identifier 'foo' has already been declared
```

### 변수의 선언과 초기화 분리

자바스크립트는 let, const를 포함한 모든 선언(var, let, const, function, function\*, class)를 호이스팅 한다. 호이스팅은 선언문을 해당 스코프의 선두로 옮긴 것처럼 동작하는 것을 말한다.

변수의 생성은 3단계에 걸쳐 이루어 진다.

1. 선언(Declaration phase)
   
   변수를 실행 컨텍스트의 변수 객체에 등록한다. 이 변수 객체는 스코프가 참조하는 대상이다.

2. 초기화(Initialization phase)
   
   변수 객체에 등록된 변수를 위한 공간을 메모리에 확보한다. 변수는 undefined로 초기화 된다.

3. 할당(Assignment phase)
   
   undefined로 초기화된 변수에 실제 값을 할당한다.

#### var의 경우

![var 변수의 선언](assets_var,let,const/var-lifecycle.png)

var 키워드로 선언한 변수는 스코프의 선두에서 선언과 초기화를 동시에 진행한다. 따라서 실제 변수 선언문이 있기 이전에 초기화된 변수가 존재한다. 이때문에 변수 선언문 이전에 변수를 참조할 수 있는 현상이 발생한다.

```js
// 스코프 선두에서 선언과 초기화 실행
// 따라서 선언문 이전에 변수를 참조 가능
console.log(foo); // undefined

var foo; // 변수 선언문
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 실행
console.log(foo); // 1
```

#### let의 경우

![let 변수의 선언](assets_var,let,const/let-lifecycle.png)

let 키워드로 선언한 변수는 선언과 초기화가 분리되어 진행된다. 스코프의 선두에서 선언은 하지만 초기화는 선언문에 도착해야 진행된다. 따라서 초기와 이전에 변수에 접근하려 하면 참조 에러가 발생한다.

```js
let foo = 1; // 전역 변수

{
  console.log(foo); // Uncaught ReferenceError: Cannot access 'foo' before initialization
  let foo = 2; // 지역 변수 초기화 및 할당
}
```

위와 같은 경우, 블록 레벨 스코프 이기 때문에 코드 블록 내에서 선언된 변수 foo가 해당 스코프에서 호이스팅되어 선언되고 초기화되는 지점까지 일시적 사각지대에 빠지기에 전역변수의 값이 출력되지 않고 에러가 발생한다.

### 전역 객체

전역 객체는 모든 객체의 유일한 최상위 객체이며 일반적으로 브라우저에서는 window객체, 서버(Node.js)에서는 global 객체를 의미한다. var 키워드로 전역 변수를 선언하면 전역 객체의 프로퍼티가 된다. 하지만 let 키워드를 사용하면 전역 객체의 프로퍼티가 아니다. 보이지 않는 개념적인 블록 내에 존재하게 된다.

```js
var foo = 123; // 전역변수
console.log(window.foo); // 123

let bar = 123; // 전역변수
console.log(window.bar); // undefined
```

## const

let은 재할당이 자유롭지만 const는 재할당이 금지되고 반드시 선언과 동시에 할당이 이루어져야 한다.

### 객체

const는 재할당이 금지된다. 이는 객체의 경우 객체에 대한 참조를 변경하지 못하는 것을 의미한다. 객체의 프로퍼티는 보호되지 않는다. 즉, 새로운 객체를 할당하는 것은 불가능하지만 할당되어있는 객체의 내용(프로퍼티의 추가, 삭제, 프로퍼티 값 등)은 변경할 수 있다.

```js
const user = { name: "Lee" };

// const 변수는 재할당이 금지된다.
// user = {}; // TypeError: Assignment to constant variable.

// 객체의 내용은 변경할 수 있다.
user.name = "Kim";

console.log(user); // { name: 'Kim' }
```
