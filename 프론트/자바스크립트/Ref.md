# react, vue3에서 쓰면서 잘 몰랐던 Ref

## Ref란 무엇인가

###### Ref는 render메서드에서 생성된 DOM노드나 React엘리먼트에 접근하는 방법을 제공한다.

일반적인 React의 데이터 플로우에서 `props`는 부모 컴포넌트가 자식과 상호작용할 수 있는 유일한 수단입니다. 자식을 수정하려면 새로운 `props`를 전달하여 자식을 다시 렌더링해야 합니다. 그러나, 일반적인 데이터 플로우에서 벗어나 직접적으로 자식을 수정해야 하는 경우도 가끔씩 있습니다. 수정할 자식은 React 컴포넌트의 인스턴스일 수도 있고, DOM 엘리먼트일 수도 있습니다. React는 두 경우 모두를 위한 해결책을 제공합니다. [공식문서](https://ko.reactjs.org/docs/refs-and-the-dom.html)

### Ref를 사용해야 할 때

Ref의 바람직한 사용 사례는 다음과 같습니다.

- 포커스, 텍스트 선택영역, 혹은 미디어의 재생을 관리할 때.
- 애니메이션을 직접적으로 실행시킬 때.
- 서드 파티 DOM 라이브러리를 React와 같이 사용할 때.

다양한 곳에서 찾은 몇 개의 예

React에서 `state`로만 해결할 수 없고, DOM을 반드시 직접 건드려야 할 때 사용하게 됩니다.

- 예: 특정 input에 focus 주기, 스크롤 박스 조작, Canvas 요소에 그림 그리기 등…

React 시스템 안에서 제어하지 않고, 순수 JS를 이용해 제어하는 컴포넌트를 제어할 때

대표적인 focus가 대표적인 예시인 이유 -> 가상DOM이 아닌 실제DOM의 `input`태그에 focus해야 하기 때문에 실제DOM을 조작하는 행위이다. 따라서 Ref를 써야한다.

등등

###### 그 외에 Ref의 작동방식을 보았을 때 사용될 수 있는 예

컴포넌트가 마운트될 때 React는 `current` 프로퍼티에 DOM 엘리먼트를 대입하고, 컴포넌트의 마운트가 해제될 때 `current` 프로퍼티를 `null`로 돌려 놓습니다. `ref`를 수정하는 작업은 `componentDidMount` 또는 `componentDidUpdate` 생명주기 메서드가 호출되기 전에 이루어진다.

즉, 컴포넌트가 마운드 된 뒤에 ref는 값을 가지게 된다. 이는 라이프사이클의 흐름을 예측하지 못하고 DOM요소를 잘못 가져왔을 경우에 발생하는오류를 막을 수 있다.

또한 Ref의 값의 변경은 `state`나 `props`의 변경과 달리 re-rendering을 일으키지 않는다.

따라서 변화할때마다 rendering이 일어나지 않았으면 하는 컴포넌트에도 쓸 수 있겠다.

### Ref에 접근하기

`render` 메서드 안에서 ref가 엘리먼트에게 전달되었을 때, 그 노드를 향한 참조는 ref의 `current` 어트리뷰트에 담기게 됩니다.

```
const node = this.myRef.current
```

ref의 값은 노드의 유형에 따라 다르다

- `ref` 어트리뷰트가 HTML 엘리먼트에 쓰였다면, 생성자에서 `React.createRef()`로 생성된 `ref`는 자신을 전달받은 DOM 엘리먼트를 `current` 프로퍼티의 값으로서 받습니다.
- `ref` 어트리뷰트가 커스텀 클래스 컴포넌트에 쓰였다면, `ref` 객체는 마운트된 컴포넌트의 인스턴스를 `current` 프로퍼티의 값으로서 받습니다.
- **함수 컴포넌트는 인스턴스가 없기 때문에 함수 컴포넌트에 ref 어트리뷰트를 사용할 수 없습니다**.

##### 클래스 컴포넌트에서 ref사용

```js
class CustomTextInput extends React.Component {
  // ...
}

class AutoFocusTextInput extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }

  componentDidMount() {
    this.textInput.current.focusTextInput();
  }

  render() {
    return (
      <CustomTextInput ref={this.textInput} />
    );
  }
}
```

위와 같은 코드는 클래스형 컴포넌트에서만 작동한다.

함수 컴포넌트는 인스턴스가 없어서 __ref 어트리뷰트를 사용할 수 없다.__

아래와 같은 코드는 작동하지 않는다.

```javascript
function MyFunctionComponent() {
  return <input />;
}

class Parent extends React.Component {
  constructor(props) {
    super(props);
    this.textInput = React.createRef();
  }
  render() {
    // 이 코드는 동작하지 않습니다.
    return (
      <MyFunctionComponent ref={this.textInput} />
    );
  }
}
```
