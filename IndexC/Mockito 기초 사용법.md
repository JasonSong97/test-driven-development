# Mockito 기초 사용법

Mockito는 모의 객체 생성, 검증, 스텁을 지원하는 프레임워크이다.

## 의존설정

- maven
- gradle

## 모의 객체 생성

Mockito.mock()을 사용하면 특정 타입(class, interface, abstract class)의 모의 객체를 생성합니다.

```java
public interface GameNumGen {
    String generate(GameLevel leve);
}
.
.
@Test
void mockTest() {
    GameNumGen genMock = mock(GameNumGen.class);
}
```

## 스텁 설정

모의 객체를 생성하면 BDDMockito 클래스를 이용해서 모의 객체에 스텁을 구성할 수 있다.

```java
@Test
void mockTest() {
    GameNumGen genMock = mock(GameNumGen.class);
    given(genMock.generate(GameLevel.EASY)).willReturn("123"); // BDDMockito 클래스

    String num = genMock.generate(GameLevel.EASY);
    assertEquals("123", num);
}
```

만약 예외를 발생하려는 경우 다음과 같이 작성하면 된다.

```java
@Test
void mockTest() {
    GameNumGen genMock = mock(GameNumGen.class);
    given(genMock.generate(GameLevel.EASY)).willThrow(IllegalArgumentException.class); // BDDMockito 클래스

    assertThrows(
        IllegalArgumentException.class,
        () -> getMock.generate(null)
    );
}
```

리턴 타입이 void인 경우는 아래와 같이 작성할 수 있다.

```java
@Test
void voidMethodWillThrowTest() {
    List<String> mockList = mock(List.class);
    willThrow(UnsupportedfOperationException.class) // 발생할 익셉션 타입 또는 익셉션 객체
        .given(mockList) // 모의 객체 받기
        .clear();

    assertThrows(
        UnsupportedfOperationException.class,
        () -> mockList.clear()
    );
}
```

## 인자 매칭 처리

Mockito는 일치하는 스텁 설정이 없는 경우 리턴 타입의 기본 값을 리턴한다. 만약 int면 0을 boolean이면 false를 리턴한다. 그리고 참조타입의 경우 null을 반환한다.

```java
given(genMock.generate(GameLevel.EASY)).willReturn("123");
String num = genMock.generate(GameLevel.NORMAL; // null 리턴
```

따라서 org.mockito.ArgumentMathcers 클래스를 사용하면 정확하게 일치한는 값 대신 임의의 값에 일치하게 설정이 가능하다.

```java
@Test
void anyMatchTest() {
    GameNumGen genMock = mock(GameNumGen.class);
    given(genMock.generate(any())).willReturn("456"); // 어떤 값을 넣오도 "456" 반환

    String num = genMock.generate(GameLevel.EASY);
    assertEquals("456", num);

    String num2 = genMock.generate(GameLevel.NORMAL);
    assertEquals("456", num2);
}
```

Mockito 클래스와 BDDMockito클래스는 ArgumentMatchers 클래스를 상속하고 있다. 따라서 ArgumentMatchers.any() 대신에 Mockito.any() 아니면 BDDMockito.any()를 사용해도 좋다.

- anyInt(), anyShort(), anyLong(), anyByte(), anyChar(), anyDouble(), anyFloat(), anyBoolean()
- anyString()
- any()
- anyList(), anySet(), anyMap(), anyCollection()
- matches(String), matches(Pattern): 정규표현식을 이용한 String 값 일치 여부
- eq(값)

단, Mockito에서 ArgumentMatchers 클래스를 사용하는 경우 주의사항이 있다. 만약 1개의 인자를 사용하는 것이 아닌 2개 이상의 ArgumentMatchers를 사용하는 경우 모든 인자가 ArgumentMatchers 클래스를 사용해야 한다.

왜냐하면 Mockito는 한 인자라도 ArgumentMatchers를 사용해서 설정한 경우 모든 인자를 ArgumentMatchers를 이용해서 설정하도록 구성되어 있다.

만약 임의의 값과 일치하는지 확인하고 싶은 경우 eq()를 사용하면 된다.

```java
@Test
void mixAnyAndEq() {
    List<String> mockList = mock(List.class);

    given(mockList.set(anyInt(), eq("123"))).willReturn("456"); // eq를 사용

    String old = mockList.set(5, "123");
    assertEquals("456", old);
}
```

## 행위 검증

모의 객체 역할은 실제로 모의 객체가 불렸는지 검증을 해야한다.

```java
public class GameTest {

    @Test
    void init() {
        GameNumGen genMock = mock(GameNumGen.class);
        Game game = new Game(genMock);
        game.init(GameLevel.EASY);

        then.(genMock).should().generate(GameLevel.EASY); // 행위검증
    }
}
```

BDDMockito.then()은 메소드 호출 여부를 검증할 모의 객체를 받는다.

정확한 값이 아닌 메소드 호출여부가 중요한 경우 any(), anyInt()를 사용하면 된다.

```java
then(genMock).should().generate(any());
```

정확하게 1번만 호출된 것을 검증하고 싶은 경우, should() 메소드에 Mockito.only()를 인자로 전달한다.

```java
then(genMock).should(only()).generate(any());
```

메소드 호출 횟수를 검증하기 위한 Mockito 클래스 메소드는 아래와 같다.

- only()
- times(int)
- never()
- atLeast(int)
- atLeastOnce(): atLeast(1)과 동일
- atMost(int)

## 인자 캡쳐

모의 객체를 호출할 때 사용한 인자를 검증해야 하는 경우가 있다. Sting과 int는 쉽게 검증이 되지만, 복잡한 경우 검증하기 힘들다.

따라서 Mockito ArgumentCaptor를 사용하면 메소드 호출 여부를 검증하는 과정에서 실제 호출할 때 전달한 인자를 검증하기 위해 보관할 수 있다.

```java
private UserRegister userRegister;
private EmailNotifier mockEnailNotifier = mock(EmailNotifier.class);

@Test
void whenRegisterThenSendMail() {
    userRegister.register("id", "pw", "email@email.com");

    // String 타입을 저장할 수 있는 ArgumentCaptor 생성
    ArgumentCaptor<String> captor = ArgumentCaptor.forClass(String.class); 
    then(mockEmailNotifier).should().sendRegisterEmail(captor.capture());

    String realEmail = captor.getValue(); // 모의 객체에 사용한 값 가져오기
    assertEquals("email@email.com", realEmail);
}
```

## JUnit 확장 설정

Mockito JUnit5의 확장 기능을 사용하기 위해서는 특정 어노테이션을 추가해야 한다.

```java
@ExtendWith(MockitoExtension.class)
public class JUnit5ExtentionTest {

    @Mock
    private GameNumGen genMock;
}
```

@ExtendWith(MockitoExtension.class)를 적용하면 @Mock 어노테이션 1개로 모의객체를 생성한다. 이전과 같이 mock()을 사용할 필요가 없다.