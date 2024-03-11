# 11
## 목차
- [11.1 기능뿐만 아니라 동작을 시험하라](#111-기능뿐만-아니라-동작을-시험하라)
- [11.2 테스트만을 위해 퍼블릭으로 만들지 말라](#112-테스트만을-위해-퍼블릭으로-만들지-말라)
- [11.3 한 번에 하나의 동작만 테스트하라](#113-한-번에-하나의-동작만-테스트하라)
- [11.4 공유 설정을 적절하게 사용하라](#114-공유-설정을-적절하게-사용하라)
- [11.5 적절한 어서션 확인자를 사용하라](#115-적절한-어서션-확인자를-사용하라)
- [11.6 테스트 용이성을 위해 의존성 주입을 사용하라](#116-테스트-용이성을-위해-의존성-주입을-사용하라)

## 11.1 기능뿐만 아니라 동작을 시험하라
`하나의 함수 != 하나의 테스트 케이스`<br>
하나의 함수에서 시행할 수 있는 동작이 여러 개라면 모두 테스트해야 한다.
> 분기 커버리지 만족하기

테스트 코드의 양이 실제 코드의 양보다 **적다면** 모든 동작이 제대로 테스트되고 있지 않음을 나타내는 경고 표시일 수 있다.

### 모든 동작을 검증하지 않고 있다는 신호
- 어떤 구문(코드 라인)을 삭제해도 여전히 컴파일되거나 테스트가 통과된다.
- if문 논리를 반대로 해도 테스트가 통과된다.
- 논리 또는 산술 연산자를 다른 것으로 대체해도 테스트가 통과된다.
- 상숫값이나 하드 코딩된 값을 변경해도 테스트가 통과된다.

### 오류 시나리오도 테스트
예외가 발생하는 시나리오도 테스트해야 한다. 오류 메시지도 검증한다.

## 11.2 테스트만을 위해 퍼블릭으로 만들지 말라
- private 함수는 구현 세부 사항이다. 퍼블릭으로 만들면 구현을 외부에 노출하게 되고 리팩토링했을 때 실패하는 케이스가 발생한다.
- 프로덕션 코드에서 퍼블릭이기 때문에 다른 개발자가 이 기능에 의존할 수 있다.

코드를 더 작은 단위로 분할하라 ⭐️
- 테스트할 동작/시나리오가 많다는 것은 해당 클래스가 많은 책임을 가지고 있다는 것이다.

## 11.3 한 번에 하나의 동작만 테스트하라
하나의 테스트 케이스로 모든 행동을 테스트하면 테스트 코드가 이해하기 어려워진다.

- 각 동작을 개별적으로 테스트하고, 각 테스트 케이스에 적절한 이름을 사용하면 테스트가 실패했을 때 어떤 이유로 실패했는지 잘 알 수 있다.
- `@ParameterizedTest`

## 11.4 공유 설정을 적절하게 사용하라
`@BeforeAll`, `@BeforeEach`, `@AfterAll`, `@AfterEach`

특정 상태나 의존성을 설정하는데 비용이 많이 드는 경우 설정 공유가 필요할 수 있다.<br>
그러나 테스트 케이스가 서로 격리되어 있지 않다면 문제가 될 수 있다.

### 상태 공유 문제
- 같은 데이터베이스에 쓰기 작업을 할 경우 이전 테스트 케이스에서 쓰여진 값이 현재 테스트 케이스에 영향을 준다.
```java
class OrderTest {
    private Database database;

    @BeforAll
    static void oneTimeSetUp() {
        database = Database.createInstance();
        database.waitForReady();
    }
}
```
-> 상태를 공유하지 않거나 초기화한다.
```java
@AfterEach
void tearDown() {
    database.reset();
}
```
### 설정 공유 문제
- 설정이 변경되면 다른 테스트 케이스에 영향을 준다.
```java
class OrderPostTest {
    private Order order;

    @BeforeEach
    void setUp() {
        order = new Order(new Customer(), List.of(new Item(1), new Item(2), new Item(3)));
    }

    @Test
    void 아이템이_3개_일_때_우편_라벨은_대형이다() {
        PostageManager postageManager = new PostageManager();

        Label label = postageManager.getPostageLabel(order);

        assert(label.isLargePackage()).isTrue();
    }
}
```
여기서 다른 테스트를 위해 order 개수가 4개가 되면 이 테스트는 효용이 없어진다.

-> 중요한 설정은 테스트 케이스 내에서 정의한다.
```java
class OrderPostTest {
    @Test
    void 아이템이_3개_일_때_우편_라벨은_대형이다() {
        Order order = new Order(new Customer(), List.of(new Item(1), new Item(2), new Item(3)));
        PostageManager postageManager = new PostageManager();

        Label label = postageManager.getPostageLabel(order);

        assert(label.isLargePackage()).isTrue();
    }
}
```

## 11.5 적절한 어서션 확인자를 사용하라
- 반환하는 리스트가 순서를 보장하지 않는데 어서션에서 순서를 강제하면 테스트에 실패한다.
```java
void test() {
    List<String> names = 테스트할_메서드("프린", "포비", "토미"); // 순서 보장 x

    assertThat(names).containsExactly("프린", "포비", "토미"); // -> 이 순서로 저장되어 있어야 테스트를 통과한다.
}
```
-> 적절한 어서션 사용
```java
void test() {
    List<String> names = 테스트할_메서드("프린", "포비", "토미");

    assertThat(names).contains("토미", "프린", "포비"); // -> 요소의 포함 여부만 확인한다.
}
```
<br>

- 테스트가 실패했을 때 이유를 제대로 설명해주어야 한다.
```java
void test() {
    List<String> names = 테스트할_메서드("프린", "포비", "토미");

    assertThat(names.contains("네오")).isTrue();
}
```
```
Expecting value to be true but was false
Expected :true
Actual   :false
```
-> 적절한 어서션 사용
```java
void test() {
    List<String> names = 테스트할_메서드("프린", "포비", "토미");

    assertThat(names).contains("네오");
}
```
```
Expecting ListN:
  ["프린", "포비", "토미"]
to contain:
  ["네오"]
but could not find the following element(s):
  ["네오"]
```

## 11.6 테스트 용이성을 위해 의존성 주입을 사용하라
```java
class InvoiceReminder {
    private final AddressBook addressBook;
    private final EmailSender emailSender;

    InvoiceReminder() {
        this.addressBook = DataStore.getAddressBook();
        this.emailSender = new EmailSenderImpl();
    }

    boolean sendReminder(Invoice invoice) {
        // ...
    }
}
```
- `AddressBook`은 데이터가 변경될 수 있는 실제 DB이다.
- `EmailSenderImpl`은 실제로 이메일은 보내는 부수 효과가 있다.

-> 테스트할 때 다른 의존성을 주입해야 한다.
```java
class InvoiceReminder {
    private final AddressBook addressBook;
    private final EmailSender emailSender;

    InvoiceReminder(AddressBook addressBook, EmailSender emailSender) {
        this.addressBook = addressBook;
        this.emailSender = emailSender;
    }

    static InvoiceReminder create() {
        return new InvoiceReminder(DataStore.getAddressBook(), new EmailSenderImpl());
    }

    boolean sendReminder(Invoice invoice) {
        // ...
    }
}
```
- 테스트할 때 테스트 더블 사용가능

> 질문: 생성자를 private으로 제한하지 않으면 테스트를 위한 코드일텐데 테스트가 프로덕션 코드에 영향을 주는 것 아닌가?<br>
다른 크루들 PR에 이와 관련한 리뷰가 많은데 어떻게 리뷰어를 설득할 것인가?
