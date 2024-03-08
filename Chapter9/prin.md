# 9 코드를 재사용하고 일반화할 수 있도록 하라
## 목차
- [9.1 가정을 주의하라](#91-가정을-주의하라)
- [9.2 전역 상태를 주의하라](#92-전역-상태를-주의하라)
- [9.3 기본 반환값을 적절하게 사용하라](#93-기본-반환값을-적절하게-사용하라)
- [9.4 함수의 매개변수를 주목하라](#94-함수의-매개변수를-주목하라)
- [9.5 제네릭의 사용을 고려하라](#95-제네릭의-사용을-고려하라)

## 9.1 가정을 주의하라
가정은 코드 재사용 시 버그를 유발한다.
```java
기사의 모든 이미지를 불러온다() {
    이미지를 포함한 섹션은 하나밖에 없으니까 첫 번째 섹션의 이미지만 반환한다.
}

// 다른 곳에서 재사용
기사의 모든 이미지를 불러올 것으로 기대...
```

> 질문: 최적화 vs 가독성, 유지보수성<br>
최적화는 비용이 많이 든다. 명백히 필요한 상황이 올 때 최적화 작업을 해도 무방하다.

### 가정을 유지할 거라면 강제해라
1. 가정이 깨지지 않게 만들어라 -> 가정이 깨지면 컴파일 실패
2. 오류 전달 기술을 사용해라 -> 가정이 깨지면 오류를 전달해서 신속하게 실패

## 9.2 전역 상태를 주의하라
static 변수는 모든 인스턴스가 공유하기 떄문에 멀티 쓰레딩 환경에서 정합성이 깨질 수 있다.


## 9.3 기본 반환값을 적절하게 사용하라
기본값을 반환하는 계층을 상위 수준으로 올린다.

## 9.4 함수의 매개변수를 주목하라
```java
class TextOptions {
    private final Font font;
    private final Double fontSize;
    private final Double lineHeight;
    private final Color textColor;

    // ...
}

class TextBox {
    void setTextStyle(TextOptions options) {
        // ...
        setColor(options);
    }

    void setColor(TextOptions options) {
        textContainer.setStyleProperty(options.getTextColor().asHexRgb());
    }
}
```
`setColor()` 메서드를 재사용하려면 `TextOptions` 객체를 생성해서 호출해야 한다. 그러면 불필요한 상태를 추가해야 한다.

```java
void styleAsWarning(TextBox textBox) {
    TextOptions options = new TextOptions(Font.ARIAL, 12.0, 14.0, Color.RED); // Color 외에 불필요한 세부 사항을 알아야 함
    textBox.setColor(options);
}
```

### 필요한 매개변수만 받게 하라
```java
class TextBox {
    void setTextStyle(TextOptions options) {
        // ...
        setColor(options.getColor());
    }

    void setColor(Color color) {
        textContainer.setStyleProperty(color.asHexRgb());
    }
}

// 최상위 호출
void styleAsWarning(TextBox textBox) {
    textBox.setColor(Color.RED);
}
```

## 9.5 제네릭의 사용을 고려하라
```java
class RandomizedQueue {
    private final List<String> values;

    String getNext() {
        // ...
    }
}
```
String으로 하드 코딩되어 있어서 String만 해당 클래스를 사용할 수 있다. 다른 문제도 해결할 수 있게 일반화가 필요하다!

```java
class RandomizedQueue<T> {
    private final List<T> values;

    T getNext() {
        // ...
    }
}
```
