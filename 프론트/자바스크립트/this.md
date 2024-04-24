# this

## 자바스크립트 외의 언어

자바에서의 this는 객체 자신에 대한 참조이다.
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
