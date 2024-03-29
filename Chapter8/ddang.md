# 8. 코드를 모듈화 하라

### 핵심 목표

- 요구사항 중 하나가 변경된다면, 그 요구사항이나 기능과 관련된 부분만 수정하는 것

## 8.1 의존성 주입의 사용을 고려하라

### 하드 코드화된 의존성은 문제가 될 수 있다

- 특정 구현에 의존해서 코드를 구현하면 다른 구현으로 코드를 재설정할 수 없음
- 하드 코드화 되어있는 경우, 특정 구현에 의존하게 되고 해당 클래스가 사용될 수 있는 경우를 훨씬 더 제한함

### 의존성 주입을 사용하라

- 생성자의 매개변수를 통해 필요한 인스턴스를 주입하면 훨씬 더 모듈화되고 다용도로 쓰일 수 있음
- 의존성을 주입하게 되면 생성자가 더 복잡해지고, 생성자를 호출하는 코드에서 미리 주입해줄 객체를 생성해야 한다는 단점 존재
  - 팩토리 함수를 제공하거나, 의존성 주입 프레임워크 사용

### 의존성 주입을 염두에 두고 코드를 설계하라

- 특정 인스턴스에 종속되지 않더라도 정적 함수에 직접 의존하는 경우, 의존성 주입을 사용하도록 변경할 수 없게 됨
- 하나의 해결책만 있는 하위 문제라면 일반적으로 문제가 없지만, 상위 코드 계층에서 하위 문제에 대해 설정을 달리 하고자 한다면 문제가 될 수 있음
  - 정적 함수(또는 변수)에 과도하게 의존하는 것을 정적 메달림이라고 함
- 하위 문제에 대한 해결책이 두 가지 이상 가능한 경우, 인터페이스를 정의하는 것을 고려
  - 해당 인터페이스를 구현하는 어떤 클래스라도 주입할 수 있도록 해줌

## 8.2. 인터페이스에 의존하라

- **의존성 역전 원칙(DIP)**: 구체적인 구현보다 추상화(인터페이스)에 의존하는 것이 나음
- 구체적인 구현에 의존하면 적응성이 제한됨
- 가능한 경우 인터페이스에 의존
  - 더 간결한 추상화 계층과 더 나은 모듈화 달성 가능

## 8.3. 클래스 상속을 주의하라

- 상속은 추상화 계층에 방해가 될 수 있음
  - 원하는 것보다 더 많은 기능을 노출할 수 있음
  - 추상화 계층이 복잡해지고 구현 세부 정보가 드러날 수 있음
- 바뀐 요구 사항을 반영해야 할 때 코드 변경이 간단치 않을 수 있음
  - 코드 중복 발생

### 구성을 사용하라

- 클래스를 확장하기 보다는 해당 클래스의 인스턴스를 필드로 가지고 있음으로써 하나의 클래스를 다른 클래스로부터 구성
- 상속이 슈퍼 클래스의 모든 기능을 상속하여 외부로 노출하는 반면, 상속 대신 구성을 사용하면 명시적으로 노출하지 않는 한 필드로 포함된 클래스의 기능이 노출되지 않음
- 인터페이스에 의존하며 의존성 주입을 사용할 수 있음
- 클래스 상속에는 경계해야 할 함정이 많기 때문에 구성과 인터페이스를 사용하면 상속의 단점은 피하면서 이점은 얻을 수 있음
- 상속의 단점
  - 취약한 베이스 클래스 문제: 슈퍼클래스가 수정되면 서브클래스가 작동하지 않을 수 있음
  - 다이아몬드 문제: 다중상속을 지원하는 경우, 여러 슈퍼 클래스가 동일한 함수의 각각 다른 버전을 지원할 때, 둘 중 어느 것을 상속 받아야 하는지 모호함
  - 단일 상속만 가능한 경우, 클래스가 논리적을 둘 이상의 클래스에 속할 때 문제가 발생할 수 있음

## 8.4. 클래스는 자신의 기능에만 집중해야 함

- 단일 개념이 단일 클래스 내에 완전히 포함된 경우, "요구 사항이 변경되면 그 변경과 직접 관련된 코드만 수정한다" 라는 핵심 목표를 달성할 수 있음
- 하나의 개념이 여러 클래스에 분산되는 경우, 해당 개념과 관련된 요구 사항을 변경하려면 관련된 클래스를 모두 수정해야 함

### 다른 클래스와 지나치게 연관되어 있으면 문제가 될 수 있음

- 다른 클래스에 대한 많은 세부 사항이 클래스 내부에 하드 코딩되어 있는 경우, 다른 클래스에 대한 변경 사항이 해당 클래스 뿐만 아니라 해당 클래스를 사용하는 클래스에도 영향을 미침

### 자신의 기능에만 충실한 클래스를 만들라

- 어떤 클래스에서 사용하는 논리를 그 클래스 내부로 옮기면 클래스들이 서로의 세부 사항에 대해 다루는 것을 최소화 할 수 있음
- 디미터의 법칙: 한 객체가 다른 객체의 내용이나 구조에 대해 가능한 한 최대한으로 가정하지 않아야 한다는 소프트웨어 공학 원칙
  - 한 객체는 직접 관련된 객체와만 상호작용 해야 함
- 클래스는 서로에 대한 어느정도의 지식을 필요로 할 때도 있지만, 가능한 한 이것을 최소화 하는 것이 좋을 때가 많음

## 8.5. 관련있는 데이터는 함께 캡슐화 하라

- 서로 다른 데이터가 서로 밀접하게 연관되어 있어 항상 함께 움직여야 하는 경우, 클래스로 그룹화하는 것이 합리적
- 코드는 여러 항목의 세부 사항을 다루는 대신, 그 항목들이 묶여있는 단일한 클래스가 제공하는 상위 수준의 개념을 다룰 수 있게 됨
  - 코드는 더욱 모듈화 되고, 변경된 요구 사항을 해당 클래스에서만 처리할 수 있음

## 8.6. 반환 유형에 세부 정보가 유출되지 않도록 주의

- 간결한 추상화 계층을 가지려면 각 계층의 구현 세부 정보가 유출되지 않아야 함
- 구현 세부 정보가 유출되면 코드의 하위 계층에 대한 정보가 노출되어 향후 수정이나 재설정이 매우 어려워질 수 있음
- 해당 세부 정보와 밀접하게 연결된 유형을 반환할 때 정보가 유출됨
  - 호출하는 쪽에서는 구현 세부 정보에 대한 추가적인 지식이 필요해짐
  - 구현이 변경되는 경우, 기존의 반환 유형을 처리하는 코드를 모두 수정해야 함
  - 추상화 계층에 적합한 유형을 반환해야 함

## 8.7. 예외 처리 시 구현 세부 사항이 유출되지 않도록 주의

- 예외를 처리하는 코드를 작성하는 과정에서 구현 세부 사항을 알 수 있게 됨
- 추상화 계층에 적절한 예외를 만들어야 함
