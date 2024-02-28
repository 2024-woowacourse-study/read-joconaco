# 4. 오류
## 목차
- [4.1 복구 가능성](#41-복구-가능성)
- [4.2 견고성 vs 실패](#42-견고성-vs-실패)
- [4.3 오류 전달 방법](#43-오류-전달-방법)
- [4.4 복구할 수 없는 오류 전달](#44-복구할-수-없는-오류-전달)
- [4.5 복구하기를 원할 수도 있는 오류 전달](#45-복구하기를-원할-수도-있는-오류-전달)

## 4.1 복구 가능성
### 복구 가능한 오류
- 치명적이지 않은 것 -> 사용자가 전화번호를 잘못 입력
- 네트워크 오류 -> 네트워크 연결에 실패하면 time out을 설정해서 재연결 시도할 수 있음
- 중요하지 않은 작업 오류 -> batch 작업

### 복구할 수 없는 오류
- 잘못된 입력 인수 호출
- 초기화하지 않음

-> **신속한 실패, 요란한 실패** 필요

## 4.2 견고성 vs 실패
오류가 발생했을 때 다음 중 하나를 선택해야 한다.

1. 더 높은 코드 계층에게 처리 위임 또는 프로그램 셧다운
2. 오류를 처리하고 계속 진행

### 신속하게 실패
문제가 실제로 발생한 지점으로부터 가까운 곳에서 오류를 나타내는 것

### 요란하게 실패
오류가 발생했다는 사실을 명백하게 알리는 것<br>
ex) 서버 중단, 에러 메시지 로깅

> 신속하고 요란하게 실패하면 개발 도중에, 또는 테스트할 때 버그를 발견할 가능성이 크다

### 오류 숨기지 않기
오류를 왜 숨기면 안될까?<br>
호출하는 쪽에서 오류를 처리할 기회를 없앰 -> 소프트웨어가 의도한 대로 작동하지 않을 수 있음
<br>

오류를 숨기는 방법은 여러가지가 있다

#### 1. 기본값
```java
public class AccountManager {
    private final AccountRepository accountRepository;

    // ...

    public double getAccountBalance(int customerId) {
        Account account = accountRepository.lookup(customerid);
        if (account.fail()) {
            return 0.0;
        }
        return account.getBalance();
    }
}
```
계정 가져오기에 실패하면 기본값 `0.0`을 리턴한다.
사용자의 잔액이 0원이 될 수 있다.

#### 2. 널 객체
```java
public class InvoiceManager {
    private final InvoiceRepository invoiceRepository;

    // ...

    public List<Invoice> getUnpaidInvoices(int customerId) {
        InvoiceResult result = invoiceRepository.findById(customerId);
        if (result.fail()) {
            return new ArrayList<>();
        }
        return result.getInvoices()
            .filter(invoice -> !invoice.isPaid())
            .toList();
    }
}
```
송장 결과 가져오기에 실패하면 빈 리스트를 리턴한다.
미지불된 송장이 없다고 판단될 수 있다.

#### 3. 아무것도 하지 않음
어떤 오류도 반환하지 않는다. 버그가 발생할 가능성이 매우 높다.

```java
if (유효성 검증 실패) {
    return; // 아무것도 하지 않음
}
// logic ...
```
<br>

``` java
try {
    // logic ...
} catch (xxException e) {
    // 오류 억제
}
```

추가 로직이 실행되었을 거라고 판단할 수 있다.

로그를 찍는 것 또한 개발자가 로그를 확인해야지만 알아차릴 수 있기 때문에 적절하지 못하다.

## 4.3 오류 전달 방법
오류를 더 높은 계층으로 알려서 정상적으로 처리할 수 있도록 해야 한다.

### 명시적 방법
호출한 곳에서 오류가 발생할 수 있음을 알려서 **오류 처리를 강제**할 것인가?

#### 1. Checked Exception
```java
public double getSquareRoot(double value) throws NevigativeNumberException {
    if (value < 0.0) {
        throw new nevigativeNumberException(value);
    }
    return Math.sqrt(value);
}
```
`throws` 시그니처를 사용한다. 해당 메서드를 호출하는 쪽에서 에러를 처리하지 않으면 **컴파일되지 않는다.**
> 에러 처리를 상위 계층에게 위임한다.<br>
상위 계층에게 계속 떠넘길 수 있지만 언젠가는 예외를 포착해서 처리하도록 구현해야 한다.

#### 2. null 반환
```java
public Double getSquareRoot(double value) {
    if (value < 0.0) {
        return null;
    }
    return Math.sqrt(value);
} 
```
> java에서 Double은 wrapper class이므로 null 가능

호출하는 쪽에서 null인지 반드시 확인해야 한다. <br>
널 안정성을 지원하지 않는 언어(ex. java)는 Optional 객체를 사용해야 한다.

#### 3. Optional 반환
```java
public Optional<Double> getSquareRoot(double value) {
    if (value < 0.0) {
        return Optional.empty();
    }
    return Optional.of(Math.sqrt(value));
} 
```
호출하는 쪽에서 `isPresent()`, `isEmpty()` 등으로 값을 사용하기 전에 null 유무를 확인하도록 한다.

#### 4. Result 반환
```java
public class Result<V, E> {
    private final Optional<V> value;
    private final Optional<E> error;

    // ...

    public static Result<V, E> ofValue(V value) {
        return new Result(Optional.of(value), Optional.empty());
    }

    public static Result<V, E> ofError(E error) {
        return new Result(Optional.empty(), Optional.of(error));
    }
}
```
> 정적 팩토리 메서드로 메서드명에 의미 부여

value를 꺼내기전에 error가 존재하는지 확인해야 하기 때문에 프로덕션 코드에서는 더 정교하게 구성해야 한다. 

#### 5. OutCome 반환
```java
Boolean sendMessage(Channel channel, String message) {
    if (channel.isOpen()) {
        channel.send(message);
        return true;
    }
    return false;
}
```
불리언 값을 리턴해서 호출한 쪽에서 오류 발생 여부를 알도록 한다.

> 결과 상태가 2개 이상이거나 boolean에 의미가 명확하지 않을 경우 **enum 사용**

호출하는 쪽에서 리턴 값을 무시하거나 아웃컴을 리턴한다는 사실을 인지하지 못할 수 있다는 한계점이 존재한다. <br>
-> `@CheckReturnValue` 어노테이션 사용하기

#### 6. 스위프트 오류
...

### 암시적 방법
호출한 곳에 오류를 알리지만 **오류 처리를 강제하지 않고 세부 조항으로 남겨둘 것**인가?

#### 1. Unchecked Exception
```java
/**
* @throws NevigateNumberException 값이 음수일 경우
*/
public double getSquareRoot(double value) {
    if (value < 0.0) {
        throw new nevigativeNumberException(value);
    }
    return Math.sqrt(value);
}
```
`throws` 시그니처가 없고 호출하는 쪽에서 에러 처리를 강제하지 않는다.<br>
에러가 발생하면 에러를 처리하는 코드를 만날 때까지 올라가다가 끝까지 없으면 종료된다.

#### 2. 매직값 반환
```java
public double getSquareRoot(double value) {
    if (value < 0.0) {
        return -1.0;
    }
    return Math.sqrt(value);
}
```
호출하는 쪽에서 매직값 -1.0을 그대로 사용하면 버그로 이어진다.

#### 3. Promise or Future
비동기적으로 실행하는 코드를 작성할 떄 Promise 또는 Future 객체를 리턴한다.

#### 4. Assertion
...

#### 5. Check
...

#### 6. Panic
...

## 4.4 복구할 수 없는 오류 전달

복구 가능성이 없는 오류가 발생하면 **신속하고 요란하게 실패하기!**

- Unchecked Exception 사용
- 프로그램을 panic 상태로 만들기
- Assertion 또는 Check 사용

## 4.5 복구하기를 원할 수도 있는 오류 전달
모범사례는 없다. **여러 방법이 있으니 팀 내부적으로 정해라**

### 주장 1: Unchecked Exception을 사용해야 한다
- 코드 구조 개선: 오류를 처리할 수 있는 계층을 둔다<br>
    -> Spring에서 `@ControllerAdvice` + `@ExceptionHandler`로 예외 처리 계층 구현하는 느낌
- 실용적이어야 함: 너무 많은 Checked Exception은 에러 처리를 제대로 안하고 넘기는 행동을 유도할 수 있다.


### 주장 2: Checked Exception을 사용해야 한다.
- 매끄러운 오류 처리: 호출하는 쪽에서 오류를 강제적으로 인식하게 해서 오류를 매끄럽게 처리할 수 있다.
- 실수로 오류를 무시할 수 없음: Checked Exception은 예외가 명백하게 드러난다.
- Unchecked Exception은 실용적이지 않음: 문서화된다는 보장이 없고 문서를 읽지 않을 가능성도 크다.
