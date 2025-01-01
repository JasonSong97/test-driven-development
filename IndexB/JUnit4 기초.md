# JUnit4 기초

## 의존 설정

JUnit5는 자바 8이상에서, JUnit은 자바 1.5 이상에서 동작한다.

- gradle

- maven

## 기본 테스트 어노테이션

- @BeforeEach
- @Test
- @AfterEach

## 단언 메소드

- assertEquals(기대하는 값, 실제 값)
- assertNotEquals(기대하지 않는 값, 실제 값)
- assertSame(기대하는 객체, 실제 객체)
- assertNotSame(기대하지 않는 객체, 실제 객체)
- aasertTrue(조건)
- assertFalse(조건)
- assertNull(실제 객체)
- assertNotNull(실제 객체)
- fail(): 테스트를 실패처리