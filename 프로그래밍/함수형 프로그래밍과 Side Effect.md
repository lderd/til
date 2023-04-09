## 왜?

![](assets_함수형%20프로그래밍과%20Side%20Effect/2023-04-05-23-47-02-image.png)

문제없는 코드인데 밑줄이 보인다

![](assets_함수형%20프로그래밍과%20Side%20Effect/2023-04-05-23-47-29-image.png)

지역변수에 재할당이 일어났단다.

별 문제 없어보이는 내용인데 왜 밑줄을 그어 알려주는 것일까

## 함수형 프로그래밍

'순수 함수'들을 쌓아 로직을 구현하고 '고차 함수'를 통해 재사용성을 높인 프로그래밍 패러다임

순수 함수란 : 출력이 입력에만 의존하는 것

이라고 전에 설명했던 적이 있음

#### 순수 함수

- 같은 입력에 항상 같은 값을 반환
- side effect가 발생하지 않음

#### Side Effect

함수가 결과값 이외에 다른 상태를 변경시킬 때 side effect가 발생한다고 한다. 예를 들어, 함수가 전역변수나 정적변수를 수정하거나, 인자로 넘어온 것들 중 하나를 변경하거나 화면이나 파일에 데이터를 쓰거나, 다른 부작용이 있는 함수에서 데이터를 읽어오는 경우가 있다.

#### 참조 투명성

흔히 side effect가 발생하는 함수는 참조 불투명하고 그렇지 않은 함수는 참조투명하다고 생각할 수 있지만 side effect가 발생하지 않지만 참조에 불투명한 경우도 있다.

프로그래밍 동작의 변경 없이 관련된 값을 대체할 수 있으면 해당 표현식을 참조상 투명하다고 한다. 참조상 투명한 함수를 실행시키면 같은 인자에 대해 항상 같은 값을 반환한다. 이러한 함수들이 순수 함수이다.

```javascript
function sqr1(x) {
  return x * x;
}

sqr1(5); // sqr1(5) 를 25로 대체 가능

function sqr2(x) {
  console.log("sqr " + x + " = " + x * x);
  return x * x;
}

sqr2(5); // sqr2(5)는 25로 대체 불가능
```

### 특징

side effect로 예상치 못한 문제를 예방하고 유지관리를 쉽게 할 수 있다

```python
def big1(x):
    global a
    if a < x:
        a = x
    return a


a = 7
print(big1(10))

'''
==========================
위와 같이 작성하면 나중에 a가 왜 10이 되었는지 알기 어렵다
'''

def big2(x, y):
    if y < x:
        return x
    else:
        return y


a = 7
print(big2(a, 10))
```

아래와 같이 작성하면 변수 a의 값이 변하지 않기에 다른 사람이 보거나 나중에 가서도 a는 7이고 유지보수가 쉽다.

```javascript
let queryValues = {
  sortBy: sortField,
  orderDesc: sortDirection === SORT_DESCENDING,
  words: query,
};
if (dateRangeFrom && dateRangeTo) {
  queryValues = {
    ...queryValues,
    from: format(dateRangeFrom.setHours(0, 0, 0, 0), DATE_FORMAT),
    to: format(dateRangeTo.setHours(23, 59, 59), DATE_FORMAT),
  };
}

// 위와같이 변수에 재할당을 하는 것보다 아래와 같이 적는 것이 전체 객체를 한번에 빌드하기 때문에 더 명확하고 알기 쉽다.

const hasDateRange = dateRangeFrom && dateRangeTo;
const queryValues = {
  sortBy: sortField,
  orderDesc: sortDirection === SORT_DESCENDING,
  words: query,
  from: hasDateRange && format(dateRangeFrom.setHours(0, 0, 0, 0), DATE_FORMAT),
  to: hasDateRange && format(dateRangeTo.setHours(23, 59, 59), DATE_FORMAT),
};
```

## 그래서 왜 인텔리제이에서 밑줄을 그어주냐

side effect가 발생했다는 것을 알려주고 함수형 프로그래밍 적인 측면에서 문제가 발생했다고 알려줌. 코드를 더 깔끔하고 유지보수하기 쉽게 작성할 수 있게 도와주는 기능임.

#### 기타

react와 redux에서 immutable에 관한 이야기를 하면서 객체에 상태를 저장하는데에 해당 객체를 변화시키는 것이 아니라 매번 새로운 객체를 다시 생성해서 저장했는데 이게 함수형 프로그래밍과 관련되어 상태를 더 잘 관리하기 위한 내용이었다.

~~그래도 반복문 i에도 밑줄은 좀 아니지 않나~~

![](assets_함수형%20프로그래밍과%20Side%20Effect/2023-04-06-00-58-13-image.png)
