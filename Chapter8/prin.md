# 8 코드를 모듈화하라
## 목차
- [8.1 의존성 주입의 사용을 고려하라](#81-의존성-주입의-사용을-고려하라)
- [8.2 인터페이스에 의존하라](#82-인터페이스에-의존하라)
- [8.3 클래스 상속에 주의하라](#83-클래스-상속에-주의하라)
- [8.4 클래스는 자신의 기능에만 집중해야 한다](#84-클래스는-자신의-기능에만-집중해야-한다)
- [8.5 관련 있는 데이터는 함께 캡슐화하라](#85-관련-있는-데이터는-함께-캡슐화하라)
- [8.6 반환 유형에 구현 세부 정보가 유출되지 않도록 주의하라](#86-반환-유형에-구현-세부-정보가-유출되지-않도록-주의하라)
- [8.7 예외 처리 시 구현 세부 사항이 유출되지 않도록 주의하라](#87-예외-처리-시-구현-세부-사항이-유출되지-않도록-주의하라)

## 8.1 의존성 주입의 사용을 고려하라
```java
class RoutePlanner {
    private final RoadMap roadMap;

    public RoutePlanner() {
        this.roadMap = new NorthAmericaRoadMap();
    }
}
```
특정 구현에 대한 의존성을 하드 코딩하면 다른 구현으로 재설정할 수 없다. <br>
여기서 `NorthAmericaRoadMap` 생성자에 파라미터가 추가된다면?

```java
class NorthAmericaRoadMap implements RoadMap {
    private final boolean useOnlineVersion;
    private final boolean includeSeasonalRoads;

    public NorthAmericaRoadMap(boolean useOnlineVersion, boolean includeSeasonalRoads) {
        this.useOnlineVersion = useOnlineVersion;
        this.includeSeasonalRoads = includeSeasonalRoads;
    }
}

class RoutePlanner {
    private static final boolean USE_ONLINE_MAP = true;
    private static final boolean INCLUDE_SEASONAL_ROADS = false;

    private final RoadMap roadMap;

    public RoutePlanner() {
        this.roadMap = new NorthAmericaRoadMap(USE_ONLINE_MAP, INCLUDE_SEASONAL_ROADS);
    }
}
```
- 몰라도 될 특정 구현 내용을 알아야 한다.(`USE_ONLINE_MAP`, `INCLUDE_SEASONAL_ROADS`)
- 추상화 계층이 지저분해지고 코드의 적응성이 한층 더 제한된다.
- 모듈화되어 있지 않고 다용도로 사용할 수 없다.
    - 다른 도로 지도로 변경(`NorthAmericaRoadMap`를 다른 RoadMap 구현체로)
    - 온라인 버전 지도에 연결하지 않음(`USE_ONLINE_MAP` = false)
    - 계절 도로 포함 (`INCLUDE_SEASONAL_ROADS` = true)

### 의존성 주입의 필요성
```java
class RoutePlanner {
    private final RoadMap roadMap;

    public RoutePlanner(RoadMap roadMap) {
        this.roadMap = roadMap;
    }
}

// 다양한 객체 생성
RoutePlanner europeRoutePlanner = new RoutePlanner(new EuropeRoadMap());
RoutePlanner northAmericaRoutePlanner = new RoutePlanner(new NorthAmericaRoadMap(true, false));
```
`RoutePlanner`의 생성자가 복잡해지고 `RoadMap` 구현체의 인스턴스를 먼저 생성해야 한다는 단점이 있다.
<br>-> 팩토리로 해결하기
```java
class RoutePlannerFactory {
    static RoutePlanner createEuropeRoutePlanner() {
        return new RoutePlanner(new EuropeRoadMap());
    }

    static RoutePlanner createDefaultNorthAmericaRoutePlanner() {
        return new RoutePlanner(new NorthAmericaRoadMap(true, false));
    }
}
```

## 8.2 인터페이스에 의존하라
```java
class RoutePlanner {
    private final NorthAmericaRoadMap roadMap;

    public RoutePlanner(NorthAmericaRoadMap roadMap) {
        this.roadMap = roadMap;
    }
}
```
`NorthAmericaRoadMap`라는 특정한 객체에 의존한다. 다른 구현체를 사용할 수 없게 된다.

### 인터페이스의 필요성
```java
class RoutePlanner {
    private final RoadMap roadMap;

    public RoutePlanner(RoadMap roadMap) {
        this.roadMap = roadMap;
    }
}
```
인터페이스에 의존하면 다양한 추상화 계층을 제공할 수 있다.

## 8.3 클래스 상속에 주의하라
1. 추상화 계층에 방해됨<br>
상속하면 부모가 노출하고 있는 메서드를 자식도 모두 노출하게 된다. 즉, 원하는 것보다 더 많은 기능을 노출하게 된다.
2. 적응성 높은 코드의 작성을 어렵게 만듬<br>
`IntFileReader`가 `CsvFileHandler`를 상속하고 있다가 `SemicolonFileHandler`로 바꿔야 한다면 코드 변경이 어려울 수 있다.

### 상속이 아닌 구성 사용하기
```java
// 상속
class IntFileReader extends CsvFileHandler {
    // ...
}

// 구성으로 변경
class IntFileReader {
    private final FileValueReader valueReader;
    // ...
}
```

### is-a 관계라도 상속은 문제될 수 있다
1. 취약한 베이스 클래스 문제: 슈퍼클래스를 수정하면 서브클래스가 작동하지 않을 수 있다.
2. 다이아몬드 문제: 다중 상속 시 누구의 메서드를 상속해야 하는지 알 수 없다.
3. 문제가 있는 계층 구조: 대부분의 언어는 단일 상속만 지원하기 때문에 논리적으로 둘 이상의 클래스에 속할 경우 문제가 된다.

## 8.4 클래스는 자신의 기능에만 집중해야 한다
1. 다른 객체의 구현 내용을 꺼내서 쓰지 않는다. 다른 객체에게 메시지를 보내도록 구현한다.
2. 디미터의 법칙을 지킨다.

## 8.5 관련 있는 데이터는 함께 캡슐화하라
```java
class TextBox {
    // ...
    void renderText(String text, Font font, Double fontSize, Double lineHeight, Color textColor) {
        // ...
    }
}

class UserInterface {
    private final Textbox textbox;
    private final UiSetting uiSetting;

    void displayMessage(String message) {
        textbox.renderText(mesasge, uiSetting.getFont(), uiSetting.getFontSize(), uiSetting.getLineHeight(), uiSetting.getTextColor());
    }
}
```
- `UiSetting`의 모든 내용을 꺼내서 전달하고 있다. `UserInterface`가 이 내용을 모두 알아야 하는가?
- `renderText()`의 시그니처가 변경되면(ex. fontStyle 추가) `displayMessage()`도 변경되어야 한다.

택배기사는 소포 안에 무엇이 들었는지 신경쓰지 않는다!

### 캡슐화 필요
```java
class TextBox {
    // ...
    void renderText(String text, TextOptions textStyle) {
        // ...
    }
}

class UserInterface {
    private final Textbox textbox;
    private final UiSetting uiSetting;

    void displayMessage(String message) {
        textbox.renderText(mesasge, uiSetting.getTextStyle());
    }
}
```
텍스트 스타일관련 정보를 캡슐화해서 `TextOptions`만 서로 알게 한다.

## 8.6 반환 유형에 구현 세부 정보가 유출되지 않도록 주의하라
- HTTP응답 객체를 그대로 반환하면 구현 세부 사항이 외부로 노출된다.
- 다른 개발자는 이를 처리하기 위해 HTTP 응답 코드를 찾아봐야 한다.
- 리턴 유형을 변경하려고 하면 이미 많은 곳에서 HTTP응답 객체로 참조하고 있기 때문에 변경하기 매우 어렵다.
- 추상화 계층에 적합한 유형을 반환해야 한다.

## 8.7 예외 처리 시 구현 세부 사항이 유출되지 않도록 주의하라
각 계층은 주어진 추상화 계층을 반영하는 오류 유형만 드러내도록 한다.

```java
class ModelBasedScorer implements TextImportanceScorer {
    // 비검사 예외 발생 PredictionModelException
    @Override
    boolean isImportant(String text) {
        return model.predict(text) >= MODEL_THRESHOLD;
    }
}

class TextSummarizer {
    private final TextImportanceScorer importanceScorer;

    // ...

    String summarizeText(String text) {
        return finder.find(text)
                   .filter(importanceScorer::isImportant)
                   .join();
    }
}

// 최상위 호출
void updateTextSummary() {
    try {
        String summary = textSummarizer.summarizeText(userText);
    } catch (PredictionModelException e) {
        // ... error 처리
    }
}
```
1. `PredictionModelException`은 구현체인 `ModelBasedScorer`에서만 발생하는 예외이다.
2. 다른 구현체가 `PredictionModelException` 예외를 던지지 않으면 예외 처리가 안된다.

### 적절한 예외 만들기
```java
interface TextImportanceScorer {
    boolean isImportant(String text) throws TextImportanceScorerException;
}

class ModelBasedScorer implements TextImportanceScorer {
    @Override
    boolean isImportant(String text) throws TextImportanceScorerException {
        try {
            return model.predict(text) >= MODEL_THRESHOLD;    
        } catch (PredictionModelException e) {
            throw new TextImportanceScorerException(e); // -> 예외 유형 변경해서 던짐
        }
        
    }
}

class TextSummarizer {
    private final TextImportanceScorer importanceScorer;

    // ...

    String summarizeText(String text) throws TextSummarizerException {
        try {
            return finder.find(text)
                   .filter(importanceScorer::isImportant)
                   .join();
        } catch (TextImportanceScorerException e) {
            throw new TextSummarizerException(e); // -> 예외 유형 변경해서 던짐
        }
        
    }
}

// 최상위 호출
void updateTextSummary() {
    try {
        String summary = textSummarizer.summarizeText(userText);
    } catch (TextSummarizerException e) {
        // ... error 처리
    }
}
```

> 비검사 예외를 사용할 때 ArgumentException, StateException과 같은 표준 예외 유형 사용<br>
장점: 다른 개발자도 예외를 예측해서 적절히 처리할 가능성이 크다<br>
단점: 서로 다른 오류를 구별하기 어렵다
