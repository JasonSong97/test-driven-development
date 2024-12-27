# TDD 시작

기존의 방법은 비즈니스 로직을 작성하고 유지보수를 했던 방법이였다. 하지만 코드를 이해하는 과정이 생각보다 오래걸렸다. 하지만 TDD를 기반한 코드작업은 개발의 빠른 생산성과 높은 유지보수성 그리고 코드의 품질을 향상한다.

## TDD?

TDD란 기능을 검증하는 테스트 코드를 먼저 작성하고 테스트를 통과시키기 위해 개발을 진행하는 것이다.

```java
@Test
void plus() {
    int result = Calculator.plus(1, 2);
    assertEquals(3, result);
}
```

테스트 코드를 실행하면 Calculator에 대한 컴파일 에러가 발생한다. 왜냐하면 존재하지 않으니까. 이제 실제 코드를 작성한다.

```java
public class Calculator {

    public static int plus(int a1, int a2) {
        return 0;
    }
}
```

위와 같은 코드를 작성하면 컴파일 에러는 사라진다. 하지만 예측한 값은 나오지 않는다. 따라서 다음과 같이 바꿔본다.

```java
public class Calculator {

    public static int plus(int a1, int a2) {
        return 3;
    }
}
```

직접적으로 결과값을 반환한다. 하지만 이 경우는 테스트 코드가 아래와 같이 변경된 경우에는 기대값과 일치하지 않는다.

```java
@Test
void plus() {
    int result = Calculator.plus(1, 2);
    assertEquals(3, result);
    assertEquals(5, Calculator.plus(4, 1));
}
```

따라서 최종적으로 아래와 같은 형태로 비즈니스 코드가 변경이 된다.

```java
public class Calculator {

    public static int plus(int a1, int a2) {
        return a1 + a2;
    }
}
```

이 일련의 작업은 test폴더에서 작업이 되어야 한다. 왜냐하면 아직 완성돤 기능이 아니기 때문이다. 모든 테스트 코드를 전부 작성한 후에 java에 추가를 하도록 하자.

과정을 요약하면 아래와 같다.

> 1. 기능을 검증하는 테스트 코드를 작성
> 2. 테스트 대상이 될 클래스 이름, 메소드 이름, 파라미터 개수, 리턴 타입 고민
> 3. 추가로 새로운 객체를 생성 또는 정적 메소드로 구현을 고민
> 4. 컴파일 오류를 없애는 데 필요한 클래스와 메소드를 작성
> 5. 테스트 실패
> 6. 실패한 부분을 최소한의 수정을 통해 테스트 통과시키기
> 7. 이 과정을 반복하면서 점진적으로 기능 향상

## TDD 암호검사기

암호검사기는 아래와 같은 요구사항이 있다.

> - 검사할 규칙은 3가지
>   - 길이가 8글자 이상
>   - 0 부터 9 사이의 숫자를 포함
>   - 대문자 포함
> - 3가지 규칙 해당 `강함`
> - 2가지 규칙 해당 `보통`
> - 1가지 규칙 해당 `약함`

이름에 대한 고민을 해야한다.

```java
public class PasswordStrengthMeterTest {

    @Test
    void name() {
        
    }
}
```

이제 `테스트 시나리오`를 생각하자. 테스트 시나리오는 처음에는 단순한 기능을 테스트하면서 점진적으로 깊은 부분을 테스트를 해야한다. 따라서 `매우 중요한 단계`이다. 이 과정은 `TDD에서 핵심이 되는 부분`이다.

> 1. 모든 규칙을 충족하는 경우
> 2. 길이만 8글자 미만이고 나머지 조건은 충족하는 경우
> 3. 숫자를 포함하지 않고 나머지 조건은 충족하는 경우
> 4. 값이 없는 경우
> 5. 대문자를 포함하지 않고 나머지 조건을 충족하는 경우
> 6. 길이가 8글자 이상인 조건만 충족하는 경우
> 7. 숫자 포함 조건만 충족하는 경우
> 8. 대문자 포함 조건만 충족하는 경우
> 9. 아무 조건도 충족하지 않는 경우

위의 과정을 진행하면서 반드시 비즈니스 코드 또는 테스트 코드를 리팩토링을 해야한다. 무작정 리팩토링을 하기보다는 가독성이 좋은 코드로 유지보수와 코드의 품질을 변경하면 된다.

모든 테스트 시나리오가 끝나면 지금까지 작업한 클래스와 메소드들은 test폴더에 있었기 때문에 java폴더로 옮기자.

## 최종 결과물 코드

```java
public class Calculator {

    public static int plus(int a1, int a2) {
        return a1 + a2;
    }
}
```

```java
public enum PasswordStrength {
    WEAK, INVALID, NORMAL, STRONG
}
```

```java
public class PasswordStrengthMeter {

    public PasswordStrength meter(String s) {
        if (s == null || s.isEmpty()) return PasswordStrength.INVALID;
        int metCounts = getMetCriteriaCounts(s);
        if (metCounts == 1) return PasswordStrength.WEAK;
        if (metCounts == 2) return PasswordStrength.NORMAL;

        return PasswordStrength.STRONG;
    }

    private int getMetCriteriaCounts(String s) {
        int metCounts = 0;
        if (s.length() >= 8) metCounts++;
        if (meetsContainingNumberCriteria(s)) metCounts++;
        if (meetsContainingUppercaseCriteria(s)) metCounts++;
        return metCounts;
    }

    private boolean meetsContainingUppercaseCriteria(String s) {
        for (char ch: s.toCharArray()) {
            if (Character.isUpperCase(ch)) {
                return true;
            }
        }

        return false;
    }

    private boolean meetsContainingNumberCriteria(String s) {
        for (char ch: s.toCharArray()) {
            if (ch >= '0' && ch <= '9') {
                return true;
            }
        }
        return false;
    }
}
```

```java
public class PasswordStrengthMeterTest {

    private PasswordStrengthMeter meter = new PasswordStrengthMeter();

    private void assertStrength(String password, PasswordStrength expStr) {
        PasswordStrength result = meter.meter(password);
        assertEquals(expStr, result);
    }

    @Test
    void meetsAllCriteria_Then_Strong() {
        assertStrength("ab12!@AB", PasswordStrength.STRONG);
        assertStrength("abc1!Add", PasswordStrength.STRONG);
    }

    @Test
    void meetsOtherCriteria_except_for_Length_Then_Normal() {
        assertStrength("ab12!@A", PasswordStrength.NORMAL);
        assertStrength("Ab12!c", PasswordStrength.NORMAL);
    }

    @Test
    void meetsOtherCriteria_except_for_number_Then_Normal() {
        assertStrength("ab!@ABqwer", PasswordStrength.NORMAL);
    }

    @Test
    void nullInput_Then_Invalid() {
        assertStrength(null, PasswordStrength.INVALID);
    }

    @Test
    void emptyInput_Then_Invalid() {
        assertStrength("", PasswordStrength.INVALID);
    }

    @Test
    void meetsOtherCriteria_except_for_Uppercase_Then_Normal() {
        assertStrength("ab12!@df", PasswordStrength.NORMAL);
    }

    @Test
    void meetsOnlyLengthCriteria_Then_Weak() {
        assertStrength("abdefghi", PasswordStrength.WEAK);
    }

    @Test
    void meetsOnlyNumCriteria_Then_Weak() {
        assertStrength("12345", PasswordStrength.WEAK);
    }

    @Test
    void meetsOnlyUpperCriteria_Then_Weak() {
        assertStrength("ABZEF", PasswordStrength.WEAK);
    }
}
```

## TDD 흐름

![alt text](<tdd-pic.png>)

> 1. TDD는 기능을 검증하는 테스트를 먼저 작성
> 2. 작성한 테스트를 통과하지 못하면 테스트를 통과할 만큼만 코드를 작성(최소한의 코드)
> 3. 테스트가 통과한 후 개선할 코드가 있으면 리펙토링 진행
> 4. 리펙토링 이후 전체 테스트를 체크하며 기존 기능에 에러가 발생하는지 확인

TDD 사이클을 Red-Green-Refactor로 부른다. 왜냐하면 먼저 Red로 에러를 터트리고 최소한의 코드로 통과(Green)시키며 리펙토링을 진행하기 떄문이다.

## 테스트가 개발을 주도

테스트 코드를 먼저 작성하면 테스트가 개발을 주도하게 된다. 테스트를 추가한 뒤에는 테스트를 통과시킬 만큼 기능을 구현한다. 이렇게 점진적으로 테스트 코드를 만들면 다음 개발 범위가 정해지고 테스트 코드가 추가되면서 구현도 완성이 되어간다.

## 지속적인 코드 정리

TDD를 통해서 지속적으로 비즈니스 코드와 테스트 코드를 리펙토링하기 때문에 코드의 품질이 점진적으로 올라간다.

## 빠른 피드백

TDD의 이점은 코드 수정에 대한 피드백이 빠르다. 새로운 코드를 추가하거나 수정하는 경우 테스트를 통해 에러를 빠르게 잡는다.