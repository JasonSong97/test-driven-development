# AssertJ 소개

## AssertJ 사용 이유

JUnit은 테스트 실행으로 부족한 점이 있다. 그것은 단언에 대한 표현이다.

```java
assertTrue(id.contains("a"));
```

이 코드는 실제 검사하는 내용을 포함하고 있지는 않는다. 단순히 assertTrue()를 사용하고 있는 것이다.

만약 실패하는 경우 메시지는 true와 false만 보여주고 id가 'a'를 포함해서 틀린 것은 알려주지 않는다.

AssertJ를 이용한 코드를 보자.

```java
assertThat(id).conatains("a");
```

이 코드는 정확하게 어떤 것을 체크하는지 알 수 있고 테스트가 실패하는 경우 어떤 것이 'a'를 포함하지 않아서 실패가 나왔는지 명확하게 알려준다.

따라서 AssertJ를 이용하면 테스트 코드의 표현력이 올라가고 다양한 검증 메소드를 제공하기 때문에 테스트 코드를 더욱 쉽게 작성할 수 있게 만든다.

## 의존 설정

- gradle
- maven

## AssertJ 기본 사용법

```java
assertThat(실제값).검증메소드(기댓값);
```

사용예시는 아래와 같다.

```java
@Test
void sumTest() {
    int value = sum(2, 2);
    assertThat(value).isEqualTo(4);
}

private int sum(int a, int b) {
    return a + b;
}
```

### 기본검증 메소드

모든 타입에 사용할 수 있는 메소드

> - isEqualTo(실제값)
> - isNotEqualTo(실제값)
> - isNull()
> - isNotNull()
> - isIn(실제값 목록): 값목록을 가변인자 또는 List타입(적확하게는 Iterable을 구현하는 타입)
> - isNotIn(실제값 목록): 값목록을 가변인자 또는 List타입(적확하게는 Iterable을 구현하는 타입)

Comparable 인터페이스를 구현한 타입이나 int, double과 같은 숫자 타입의 경우의 메소드

> - isLessThan(실제값)
> - isLessThanOrEqualTo(실제값)
> - isGreaterThan(실제값)
> - isGreaterThanOrEqualTo(실제값)
> - isBetween(실제값1, 실제값2)

boelean, Boolean 타입을 위한 검증 메소드

> - isTrue()
> - isFalse()

### String에 대한 추가 검증 메소드

특정 값을 포함하는지 검증하는 메소드

> - contains(CharSequence... values): 인자로 지정한 문자열을 모두 포함하는지 확인
> - containsOnlyOnce(CharSequence sequence): 해당 문자열을 딱 한 번만 포함하는지 검증
> - containsOnlyDigits(): 숫자만 포함하는지 검증
> - containsWhitespaces(): 공백문자를 포함하는지 검증
> - containsOnlyWhitespaces: 공백문자만 포함하는지 검증
> - containsPattern(CharSequence regex): 지정한 정규 표현식에 일치하는 문자를 포함하는지 검증
> - containsPattern(Pattern pattern): 위에와 동일

포함하지 않는지 여부를 확인하는 메소드

> - doesNotContain(CharSequence... values): 인자로 지정한 문자열들을 모두 포함하고 있지 않은지 검증
> - doesNotContainAnyWhitespaces(): 공백문자를 포함하고 있지 않은지를 검증
> - doesNotContainOnlyWhitespaces(): 공백문자를 포함하고 있지 않은지를 검증
> - doesNotContainPattern(Pattern pattern): 정규 표현식에 일치하는 문자를 포함하고 있지 않은지를 검증
> - doesNotContainPattern(Charsequence pattern): 위에와 동일

특정 문자열로 시작하거나 끝나는지 검증하는 메소드

> - startsWith(Charsequence prefix): 지정한 문자열로 시작하는지 검증
> - doesNotStartWith(Charsequence prefix): 지정한 문자열로 시작하지 않는지 검증
> - endsWith(Charsequence suffix): 지정한 문자열로 끝나는지 검증
> - doesNotEndWith(Charsequence suffix): 지정한 문자열로 끝나지 않는지 검증

### 숫자에 대한 추가 검증 메소드

> - isZero() / isNotZero()
> - isOne()
> - isPositive() / isNotPositive(): 양수인지 양수가 아닌지 검증
> - isNegative() / isNotNegative(): 음수인지 음수가 아닌지 검증

### 날짜/시간에 대한 추가 검증 메소드

LocalDateTime, LocalDate, Date 등 날짜와 시간 관련된 타입에 대해 비교할 값

> - isBefore(비교값)
> - isBeforeOrEqualTo(비교값)
> - isAfter(비교값)
> - isAfterOrEqualTo(비교값)

LocalDateTime, OffsetDateTime, ZonedDateTime 검증 메소드

> - isEqualToIgnoringNanos(비교값)
> - isEqualToIgnoringSeconds(비교값)
> - isEqualToIgnoringMinutes(비교값)
> - isEqualToIgnoringHours(비교값)

### 컬렌션에 대한 추가 검증 메소드

List와 Set에 해당하는 검증 메소드

> - hasSize(int expected)
> - contains(E values): 콜렉션이 저장한 값을 포함하는지 검증
> - containsOnly(E values): 콜렉션이 지정한 값만 포함하는지 검증
> - containsAnyOf(E values): 콜렉션이 지정한 값 중 일부를 포함하는지 검증
> - containsOnlyOnce(E values): 콜렉션이 지정한 값을 한 번만 포함하는지 검증

Map에 해당하는 검증 메소드

> - containsKey(K key)
> - containsKeys(K keys)
> - containsOnlyKeys(K keys): Map이 지정한 키만 포함하는지 검증
> - doesNotContainKeys(K, keys): Map이 지정한 키들을 포함하지 않는지 검증
> - containsValues(Value values): Map이 지정한 값들을 포함하는지 검증
> - contains(Entry<K, V> valeus): Map이 지정한 Entry<K, V>를 포함하는지 검증

### 예외에 대한 검증 메소드

익셉션 발생 여부 검증 시, assertThatThrownBy()를 사용한다.

```java
assertThatThrownBy(() -> readFile(new File("nofile.txt")));
```

만약 발생한 예외의 타입을 검증하고 싶은 경우는 아래와 같이 변경하면 된다.

```java
assertThatThrownBy(() -> readFile(new File("nofile.txt")))
    .isIstanceOf(IOException.class);
```

특정 타입의 예외가 발생하는지 검증하는 또 다른 방법은 assertThatExceptionOfType()이다.

```java
assertThatExceptionOfType(IOException.class)
    .isThrownBy(() -> {
        readFile(new File("noFile.txt"))
    });
```

만약 IOException이 발생하는 것을 검증하고 싶은 경우는 아래와 같은 코드를 이용한다.

```java
assertThatIOException()
    .isThrownBy(() -> {
        readFile(new File("nofile.txt"));
    });
```

> - assertThatNullPointerException()
> - assertThatIllegalArgumentException()
> - assertThatIllegalStateException()

만약 예외가 발생히자 않는 것을 검증하고 싶은 경우 아래과 같은 코드를 작성한다.

```java
assertThatCode(() -> {
    readFile(new File("pom.xml"));
}).doesNotThrowAnyExceltion();
```

### SoftAssertions 모아서 검증

SoftAssertions는 JUnit5의 assertAll()과 유사하다. 즉 여러 검증을 할 때 사용한다.

```java
SoftAssertions soft = new SoftAssertions();
soft.assertThat(1).isBetween(0, 2);
soft.assertThat(1).isGreaterThan(2);
soft.assertThat(1).isLessThan(0);
soft.assertAll(); // 이 때 실행
```

다른 방법으로도 작성할 수 있다. 아래의 코드는 assertAll()을 따로 실행할 필요가 없다.

```java
SoftAssertions.assertSoftly(soft -> {
    soft.assertThat(1).isBetween(0, 2);
    soft.assertThat(1).isGreaterThan(2);
    soft.assertThat(1).isLessThan(0);
});
```

### as()와 describedAs()로 설명 달기

as와 describedAs는 비슷한 역할을 하기 때문에, 그냥 서로 바꿔가면서 사용하면 된다.

```java
assertThat(id).as("ID 검사").isEqualTo("abc");
assertThat(id).as("ID 검사: %s", "abc").isEqualTo("abc");

soft.assertThat(ret.get(i)).as("ret[%d]", i).isEqualTo(expectedf.get(0));
```