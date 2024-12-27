# JUnit5 추가 내용

## 조건에 따른 테스트

JUnit5에서는 조건에 따라 테스트를 실행할지 여부를 결정하는 기능을 제공한다.(org.junit.jupiter.api.condition)

### 특정 운영제체에서만 동작

- @EnalbledOnOs
- @DisabledOnOs

```java
@Test
@EnabledOnOs(OS.WINDOW)
void windowTempPath() {
    Path tmpPath = Paths.get("C:\\Temp");
    assertTrue(Files.isDirectory(tmpPath));
}

@Test
@EnabledOnOs(OS.LINUX)
void linuxTempPath() {
    Path tmpPath = Paths.get("/tmp");
    assertTrue(Files.isDirectory(tmpPath));
}
```

### 자바 버전에 따라

- @EnabledOnJre
- @DisabledOnJre

```java
@Test
@EnabledOnJre({
    JRE.JAVA_8, JRE.JAVA_9, JRE.JAVA_10, JRE.JAVA_11
})
void testOnJre {
    assertEquals(LocalDate.of(1919, 3, 1), LocalDate.of(2019, 3, 1).minuxYears(100));
}
```

### 시스템 프로퍼티 값의 비교해 테스트 실행 여부 결정

- @EnabledIfSystemProperty
- @DisabledIfSystemProperty

```java
@Test
@EnabledIfSystemProperty(named = "java.jvm.name", matches = ".*OpenJDK.*")
void openJdk() {
    assertEquals(2, 1 + 1);
}
```

named는 시스템 프로퍼티의 이름을 지정하고 matches 속성은 값의 일치 여부를 검사한다. matches의 경우 정규 표현식을 사용한다.

## 태깅과 필터링

테스트를 할 때 특정 테스트를 포함시킬지 포함시키지 말지를 정하는게 가능하다.

- @Tag: 클래스와 테스트 메소드에 적용이 가능

```java
@Tag("integration")
public class TagTest {

    @Tag("very-slow")
    @Test
    void verySlow() {
        int result = someVerySlowop();
        assertEquals(result, 0);
    }
}
```

태그의 규칙은 아래와 같다.

> - null or 공백은 불가
> - 좌우 공백을 제거한 뒤에 공백을 포함하면 안됌
> - ISO 제어 문자를 포함하면 안됌
> - 다음 글자를 포함하면 안됌: . ( ) & | !

사용법은 아래와 같다.

- gradle

```gradle
test {
    useJUnitPlatform {
        includeTags 'integration' # 테스트 대상에 포함
        excludeTags 'slow | very-slow' # 테스트 대상에 미포함
    }
}
```

## 중첩 구성

- @Nested

```java
public class Outer {

    @BeforeEach
    void outerBefore() {}

    @Test
    void outer() {}

    @AfterEach
    void outerAfter() {}

    @Nested
    class NestedA {
        
        @BeforeEach
        void nestedBefore() {}

        @Test
        void nested1() {}

        @AfterEach
        void nestedAfter() {}
    }
}
```

> 1. Outer 객체 생성
> 2. NestedA 객체 생성
> 3. outerBefore() 실행
> 4. nestedBefore() 실행
> 5. nested1() 실행
> 6. nestedAfter() 실행
> 7. outerAfter() 실행

중첩된 클래스는 내부 클래스이기 때문에 외부 클래스에 접근이 가능합니다. 따라서 중첩 테스트 클래스를 분리해서 테스트 코드를 구성할 수 있습니다.

## 테스트 메세지

몇 번째 인자에서 값 검증에 실패했는지 알 수 있는 방법이다.

```java
List<Integer> ret = getResults();
List<Integer> expected = Arrays.asList(1, 2, 3);
for (int i = 0; i < expected.size(); i++) {
    assertEquals(expected.get(i), ret.get(i), "ret[" + i + "]"); // 이 부분
}
```

## @TempDir 이용한 임시 폴더 생성

파일 관련 테스트 코드이다. 따라서 테스트시에 임시 폴더를 생성하고 특정 파리미터를 임시 폴더 경로를 전달한다. 그리고 @TempDir은 File타입이나 Path타입에 적용할 수 있다.

```java
@TempDir
File tempFolder;

@Test
void fileTest() {
    // tempFolder에 파일 생성 등 작업
}
```

위의 경우에는 테스트 메소드를 실행하기 전에 사용을 했기 떄문에 임시 폴더를 생성하고 그 폴더 정보를 tempFilder필드에 할당한다. 그리고 각 테스트별로 tempFolder를 생성한다. 만약 특정 테스트에만 임시폴더를 생성하고 싶은 경우는 아래와 같이 사용하면 된다.

```java
@Test
void fileTest(@TempDir File tempFolder) {
    // tempFolder에 파일 생성 등 작업
}
```

해당 코드는 테스트가 끝나면 자동으로 생성한 임시 폴더를 삭제한다.

만약 특정 클래스 단위로 임시 폴더를 생성하고 싶은 경우는 static을 붙인다. 그러면 해당 전체 테스트가 실행되고 종료시 삭제된다.

```java
@TempDir
static File tempFolderPerClass;
.
.
또는
.
.
@BeforeAll
static void setUp(@TempDir File tempFolder) {
    ...
}
```

## @Timeout 이용한 테스트 실행 시간 검증

JUnit5.5 버전부터 제공하는 어노테이션입니다. 테스트가 일정 시간 내에 실행되는지 검증할 수 있습니다. 아래의 코드는 1초 측정으로 되어 있지만, 테스트 코드에는 2초로 되어있기 때문에 에러가 발생합니다.

```java
@Test
@Timeout(1)
void sleep2seconds() throws InterruptedException {
    Thread.sleep(2000);
}
```

초가 아닌 밀리초를 사용할 경우 아래와 같이 변경하면 됩니다.

```java
@Test
@Timeout(value = 500, unit = TimeUnit.MILLISECONDS)
void sleep40Mills() throws InterruptedException {
    Thread.sleep(40);
}
```