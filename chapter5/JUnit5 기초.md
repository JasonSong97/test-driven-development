# JUnit5 기초

## 의존성 필요

- maven
- gradle

## @Test 어노테이션과 테스트 메소드

부록 IndexB 참고 필요

```java
@Test
void sum() {
    int result = 2 + 3;
    assertEquals(5, result);
}
```

## 주요 단언 메소드

Assertions 클래스가 제공하는 주요 단언 메소드

> - assertEquals(기댓값, 실제값)
> - assertNotEquals(특정값, 실제값)
> - assertSame(기댓값, 실제값)
> - assertNotSame(특정값, 실제값)
> - assertTrue(조건)
> - assertFalse(조건)
> - assertNull(실제값)
> - assertNotNull(실제값)
> - fail(): 테스트 실패 처리

Assertions가 제공하는 예외 발생 유무 메소드

> - assertThrows(기댓값 타입, Executable)
> - assertDoesNotThrows(Executable)

```java
assertThrows(IllegalArgumentException.class,
    () -> {
        AuthServuce authService = new AuthService();
        authService.authenticate(null, null);
    });
```

assertThrow()는 발생한 예외 객체를 리턴한다. 발생한 예외를 이용해서 추가로 검증이 필요하면 assertThrows() 메소드가 리턴한 예외 객체를 사용한다. 아래와 같다.

```java
IllegalArgumentException thrown = assertThrows(IllegalArgumentException.class,
    () -> {
        AuthService authService = new AuthService();
        authService.authenticate(null, null);
    });
assertTrue(thrown.getMessage().contains("id"));
```

참고로 assertThrow()와 assertDoesNotThrow()메소드에서 사용하는 Excutable 인터페이스는 다음과 같이 execute() 메소드를 가진 함수형 인터페이스다.

```java
public interface Executable {
    void execute() throws Throwable;
}
```

assert 메소드는 실패하면 다음 코드를 실행하지 않는다. 따라서 전체 테스트의 실패 또는 성공 여부를 알고 싶은 경우 상당히 부적합 하다. 그래서 assertAll()을 사용하면 된다.

```java
assertAll(
    () -> assertEquals(3, 5/2),
    () -> assertEquals(4, 2 * 2),
    () -> assertEquals(6, 11/2)    
);
```

assertAll() 메소드는 Executable 목록을 가변 인자로 받아 각 Executable을 실행한다. 실행 결과로 검증에 실패한 코드가 있으면 그 목록을 모아서 에러 메세지로 알려준다.

## 테스트 라이프사이클 어노테이션

- @BeforeEach
- @Teest
- @AfterEach
- @BeforeAll
- @AfterAll

## 테스트 메소드 간 실행 순서 의존과 필드 공유하지 않기

각 메소드는 독립적으로 실행이 되어야 하기 때문에, 한 테스트 메소드의 결과가 다른 테스트에 영향을 주어서 실행 결과가 달라지면 안된다.

따라서 필드를 서로 공유하거나 하는 행위는 절대로 하면 안 된다. 테스트간의 의존이 생기면 이는 테스트 코드의 유지보수를 어렵게 만든다.

```java
private FileOperator op = new FileOperator();
private static File file; // 두 테스트가 데이터를 공유할 목적으로 필드 사용

@Test
void fileCreationTest() {
    File createdFile = op.created();
    assertTure(created.length() > 0);
    this.file = createdFile;
}

@Test
void readFileTest() {
    long data = op.readData(file);
    assertTrue(data > 0);
}
```

## @DisplayName, @Disabled

```java
@DisplayName("class 테스트")
public class DisplayNameTest {

    @DisplayName("값 같은지 비교")
    @Test
    void assertEqualsMethod() {
        ...
    }

    @Disabled // 테스트 항목에서 제외
    @Test
    void assertEqualsMethod() {
        ...
    }
}
```

## 모든 테스트 실행

- mvn test
- gradle test