## 테스트 코드 작성시 주의사항

좋은 테스트 코드를 위해서는 주의사항이 존재한다

### 1. 두 개 이상을 검증하지 않기

회원가입을 검증한다 가정했을 때,

1. 회원가입 이후 데이터가 잘 저장되는지 검증

2. 가입 완료 이메일 발송을 요청하는지 검증

두 검증이 한번에 일어난다면 테스트의 결과를 확인할 때 어느 부분이 문제인지 확인하기 어렵다.

### 2. 변수나 필드를 사용해 기댓값을 표현하지 않기

```python
import datetime

dt = datetime.datetime(1996, 5, 10)
result = dt.strftime("%Y년 %m월 %d일")
assert result == f'{dt.year}년 {dt.month}월 {dt.day}일', f'{result}, {dt.year}년 {dt.month}월 {dt.day}일'


'''
위의 테스트에서 오류가 발생하는데 어디서 발생하는지 한눈에 파악하기 어려움.
dt.year이 문제인지 내가 만든 result가 문제인지 파악하기 어려움.
'''
import datetime

dt = datetime.datetime(1996, 5, 10)
result = dt.strftime("%Y년 %m월 %d일")
assert result == f'1996년 05월 10일', '문제발생'
```

### 3. 정확한 값으로 모의 객체 설정하지 않기

```python
def 암호화(word):
    code = ''
    for char in word:
        code += chr((ord(char) + 7 - 97) % 26 + 97)
    return code
# 위와 같이 간단한 암호화를 적용시킨다 생각해보자

password = 암호화('pw')
assert password == 'wd'
```

와 같은 식으로 작성하게 되면 비밀번호를 `pw`라고 할 때만 테스트에 통과한다. 나중에 다른 비밀번호를 넣어서 테스트가 통과하는지 확인하기 위해서는 암호화가 되는지가 아니라 제대로 `a~z` 사이의 문자로 바뀌는지를 확인해야 할 것이다.

### 4. 내부 구현을 검증하지 않기

테스트 코드를 작성할 때 내부 구현을 검증하는 것이 나쁜 것은 아니지만 과도하게 검증할 경우 구현을 조금만 변경하더라도 테스트가 깨질 가능성이 높아진다. 테스트 코드를 작성할 때에는 과정보다는 결과에 집중해야 한다.

### 5. 중복된 상황을 설정하지 않기

```java
@BeforeEach
void setUp(){
    changeService = new ChangeUserService(memoryRepository);
    memoryRepository.save(new User("id","pw",new Address("서울","북부")));
}

@Test
void noUser(){
    assertThrows(
    UserNotFoundException.class , ()-> changeService.changeAddress("id2",new Address("서울","남부")));
}

@Test
void changeAddress(){
    changeService.changeAddress("id",new Address("서울","남부"));

    User user=memoryRepository.findById("id");
    assertEquals("서울",user.getAddress().getCity());
}

@Test
void changePw(){
    changeService.changePw("id","pw","newpw");

    User user=memoryRepository.findById("id");
    assertTrue(user.matchPassword("newpw"));
}

@Test
void pwNotMatch(){
    assertThrows(IdPwNotMatchException.class, () -> changeService.changePw("id","pw2","newpw"));
}
```

이런 코드가 있다면 코드의 길이는 짧아졌지만 유지보수의 측면에서 나중에 문제가 발생해 이 코드를 본다면 setUp이 있기에 코드의 위치를 찾아가야 하고 모든 코드가 같은 객체를 사용하기에 이를 바꾸기 위해서는 이에 영향을 받는 다른 메서드가 있는지 확인해야 하는 상황이 온다.

### 6. 실행 환경이나 시점이 결과에 영향을 미치지 않기

##### 실행환경

실행 환경의 대표적인 예는 `파일 경로`, 절대경로를 쓸 경우 실행하는 장치에 따라 실패하기도 할 것이다.

##### 실행시점

로그인 토큰의 만료기간을 확인하는 테스트 코드를 작성했다고 생각해보자.

```java
public class Member{
    private LocalDateTime expiryDate;

    public boolean isExpired(){
        return expiryDate.isBefore(LocalDateTime.now());
    }
}
```

```java
@Test
void notExpired(){
    //테스트 코드를 작성한 시점이 2019년 1월 1일
    LocalDateTime expiry = LocalDateTime.of(2019,12,31,0,0,0);
    Member m = Member.builder().expiryDate(expiry).build();
    assertFalse(m.isExpired());
}
```

토큰이 만료일 이전이면 `false`, 즉 만료되지 않았다는 `assertFalse()`에 통과된다.
만료일이 2019년 12월 31일 0시 0분 0초로 설정했기에 작성시점에는 문제가 없다. 하지만 12월 31일 이후에 테스트하면 현재 시점 기준이면 만료된 이후이기에 문제가 발생한다.

이를 방지하기 위한 방법으로는 값을 파라미터로 넘기는 방식이 있다.

```java
public class Member{
    private LocalDateTime expiryDate;

    public boolean passedExpiryDate(LocalDateTime time){
        return expiryDate.isBefore(time);
    }
}
```

```java
@Test
void notExpired(){
    //테스트 코드를 작성한 시점이 2019년 1월 1일
    LocalDateTime expiry = LocalDateTime.of(2019,12,31,0,0,0);
    Member m = Member.builder().expiryDate(expiry).build();
    assertFalse(m.passedExpiryDate(LocalDateTime.of(2019,12,30,0,0,0)));
}
```

위와 같이 설정하면 경계 조건도 쉽게 테스트 할 수 있다.

##### 랜덤하게 실패

```java
public class Game{
    private int[] nums;

    public Game(){
        Random random = new Random();
        int firstNo = random.nextInt(10);
        ...
        this.nums = new int[] {firstNo, secondNo, thridNo};
    }

    public Score guess(int ... answer){
        ...생략
    }
}
```

위의 코드를 보면 게임이 만들어질 때 랜덤하게 숫자를 3개 정한다. 그럼 `Game class`를 검증하기 위해서는 랜덤한 숫자를 맞춰야 한다!!

이런 코드는 구조 자체를 바꾸어야 한다. `Game class` 내부에서 랜덤한 값을 생성하는 것이 아니라 외부에서 생성된 인자를 넘겨받도록 설계해야 한다.

### 7. 필요하지 않은 값은 설정하지 않기

```java
@Test
void dupIdExists_Then_Exception(){
    //동일 ID가 존재하는 상황
    memoryRepository.save(User.builder().id("dupid").name("이름")
                            .email("abc@abc.com")
                            .password("abcd")
                            .regDate(LocalDateTime.now())
                            .build());

    RegisterReq req = RegisterReq.builder()
        .id("dupid").name("다른이름")
        .email("dupid@abc.com")
        .password("abcde")
        .build()

    assertThrows(DupIdException.class, () -> userRegisterSvc.register(req));
}
```

중복된 ID를 가진 회원이 가입 거절되는 상황을 검증하는 테스트인데 ID 외에도 많은 값이 설정되어 있어서 어떤 상황을 테스트하는지 파악하기 어렵고 테스트에 실패했을 때 그 원인이 검증하고자 하는 상황이 맞는지(ID가 중복되어서 실패한게 맞는지)도 파악하기 어렵다.

### 8. 조건부로 검증하지 않기

```java
@Test
void canTranslateBasicWord(){
    Translator tr = new Translator();

    if(tr.contains("cat")){
        assertEquals("고양이", tr.translate("cat"));
    }
}
```

테스트는 성공 or 실패 밖에 없다. 위의 코드는 `"cat"`이 있어야만 테스트를 진행하기 때문에 `"cat"`이 없는 상황에서는 테스트를 진행하지 않기 때문에 성공도 실패도 아니다. 그렇기에 위의 코드는 잘못되었다. 테스트를 성공했지만 테스트를 성공한 것인지 테스트를 진행하지 않은 것인지 알 수 없다.
따라서 조건에 대한 테스트도 확인이 필요하다.

```java
@Test
void canTranslateBasicWorld(){
    Translator tr = new Translator();
    assertTranslationOfBasicWord(tr,"cat");
}

private void assertTranslationOfBasicWord(Translator tr, String word){
    assertTrue(tr.contains("cat"));
    assertEquals("고양이",tr.translate("cat"));
}
```

### 9. 통합 테스트는 필요한 범위만 연동하기

```java
@Component
public class MemberDao {
    private JdbcTemplate jdbcTemplate;

    public MemberDao(JdbcTemplate jdbcTemplate){
        this.jdbcTemplate= jdbcTemplate;
    }

    public List<Member> selectAll(){
        ...생략
    }
}
```

위 코드는 스프링의 `JdbcTemplate`을 이용해서 데이터를 연동하고 있다.

스프링 부트프로젝트를 사용한다면 다음과 같은 코드를 이용해서 DB연동 테스트를 진행할 수 있다.

```java
@SpringBootTest
public class MemberDaoIntTest{
    @Autowired MemberDao dao;

    @Test
    void findAll(){
        List<Member> members=dao.selectAll();
        assertTrue(members.size() > 0);
    }
}
```

위 테스트코드는 한 가지 단점이 있다.

테스트하는 대상은 DB와 연동을 처리하는 `MemberDao`인데 `@SpringBootTest` 어노테이션을 사용하면 서비스, 컨트롤러 등 모든 스프링 빈을 초기화한다는 것이다.

DB 관련된 설정 외에 나머지 설정도 처리하므로 스프링을 초기화하는 시간이 길어질 수 있다.

스프링 부트가 제공하는 `@JdbcTest` 애노테이션을 사용하면 DataSoruce, JdbcTemplate등 DB연동과 관련된 설정만 초기화 한다.

다른 빈을 생성하지 않으므로 스프링을 초기화하는 시간이 짧아진다. 다음은 `@JdbcTest` dj노테이션을 이용한 예를 보여준다

```java
@JdbcTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
public class MemberDaoJdbcTest{

    @Autowired JdbcTemplate jdbcTemplate;

    private MemberDao dao;

    @BeforeEach
    void setUp(){
        dao = new MemberDao(jdbcTemplate);
    }

    @Test
        void findAll(){
        ...생략
    }
}
```

위 코드는 MemberDao객체를 직접 생성하고 있지만 대신 확인에 필요한 스프링 설정만 초기화하고 테스트할 수 있는 장점이 있다.

DataSource와 JdbcTemplate을 테스트 코드에서 직접 생성하면 스프링 초기화 과정이 빠지므로 테스트 시간은 더 짧아질 것이다.
