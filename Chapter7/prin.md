# 7 코드를 오용하기 어렵게 만들라
## 목차
- [7.1 불변 객체로 만드는 것을 고려하라](#71-불변-객체로-만드는-것을-고려하라)
- [7.2 객체를 깊은 수준까지 불변적으로 만드는 것을 고려하라](#72-객체를-깊은-수준까지-불변적으로-만드는-것을-고려하라)
- [7.3 지나치게 일반적인 데이터 유형을 피하라](#73-지나치게-일반적인-데이터-유형을-피하라)
- [7.4 시간 처리](#74-시간-처리)
- [7.5 데이터에 대해 진실의 원천을 하나만 가져야 한다](#75-데이터에-대해-진실의-원천을-하나만-가져야-한다)
- [7.6 논리에 대한 진실의 원천을 하나만 가져야 한다](#76-논리에-대한-진실의-원천을-하나만-가져야-한다)

## 7.1 불변 객체로 만드는 것을 고려하라
> 불변: 내용물을 신뢰할 수 있는 '봉인 씰' 같은 것

### 가변 객체의 문제점
- 가변 객체는 추론하기 어렵다.
- 가변 객체는 다중 스레드에서 문제가 발생할 수 있다.

```java
class Display {
    private final MessageBox messageBox;
    
    //...

    void print() {
        TextStyle style = new TextStyle(Font.ARIAL, 12);
        messageBox.renderTitle("Hello", style);
        messageBox.renderBody("World", style); // -> 2. 변경된 style 인스턴스로 호출
    }
}

class MessageBox {
    private final TextArea area;

    // ...

    void renderTitle(String title, TextStyle style) {
        style.setFontSize(18); // -> 1. style 인스턴스 상태 변경
        area.displayTitle(title, style);
    }

    void renderBody(String body, TextStyle style) {
        area.displayBody(body, style); // -> 3. 12의 글꼴 크기를 기대했지만 18로 표시되는 버그 발생
    }
}
```
> setter를 지양하는 이유를 조금 깨달을 수 있었다

- 객체를 생성할 때만 값을 할당하라
    - 할당한 값은 final로 불변하게 유지
- 불변성에 대한 디자인 패턴을 사용하라
    - 빌더 패턴
    - 쓰기 시 복사(Copy on Write) 패턴

## 7.2 객체를 깊은 수준까지 불변적으로 만드는 것을 고려하라
리스트를 그냥 참조하면 원본이 변경됐을 때 참조하는 리스트에도 영향을 미친다.

1. 방어적 복사
```java
class Users {
    private final List<User> users;

    Users(List<User> users) {
        this.users = List.copyOf(users);
        // 또는
        this.users = new ArrayList<>(users);
    }
}
```

2. 불변적 자료구조
```java
class Users {
    private final List<User> users;

    // ...

    List<User> getUsers() {
        return Collections.unmodifiableList(users);
    }
}
```

## 7.3 지나치게 일반적인 데이터 유형을 피하라
- 원시값을 포장해서 도메인 객체의 의미 부여하기
- 일급 컬렉션으로 Collection에 의미 부여하기

## 7.4 시간 처리
정수로 시간을 나타내면 오용하기 쉽다.
- 순간적인 시간을 나타내는가? vs 시간의 양을 나타내는가?
- ns, ms, s단위의 차이를 정수로 표현할 수 없다.

### 해결책
- 적절한 자료구조 사용
- Time, Duration, ...

## 7.5 데이터에 대해 진실의 원천을 하나만 가져야 한다
- 파생되는 데이터는 필요할 때만 계산하게 한다.
    - 파생되는 데이터는 중복 정보이다.
    - ex) 계좌 잔액(파생 데이터) = 대변(원천) - 차변(원천)
- 계산의 비용이 클 경우 lazy 또는 caching 적용

> 예전에 했던 프로젝트가 생각난다...<br>
질문: 반정규화처럼 허용할 범위를 어디까지 잡아야 할까?


## 7.6 논리에 대한 진실의 원천을 하나만 가져야 한다
직렬화, 역직렬화 논리가 두 곳에 나누어져 있으면 문제가 발생한다.<br>
-> 직렬화 논리는 수정했는데 역직렬화 논리는 수정하지 못함
- 하나의 클래스에서 형식 정보를 관리하고 여기서 직렬화, 역직렬화를 모두 수행하도록 변경

> Redis 쓸 때 serializer 설정했던 것이 생각났다. 코드를 까보니까 역시 직렬화, 역직렬화를 한 곳에서 관리하고 있었다