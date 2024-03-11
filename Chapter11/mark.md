# Chapter11 단위 테스트의 실제

다룰 내용
- 코드 모든 동작을 효과적, 신뢰성 있게 테스트
- 이해 쉽고 실패 잘 설명되는 테스트 작성
- 의존성 주입 사용하여 테스트 용이한 코드 작성

## 11.1 기능뿐만 아니라 동작을 시험하라

코드 테스트는 할 일 목록 작업과 비슷

개발자 실수 중 하나. 함수 마다 하나의 테스트 케이스 작성  
중요한 행동을 모두 테스트 해야하는 것 중요. 함수 수행 모든 동작 작성이 좋음

### 11.1.1 함수당 하나의 테스트 케이스만 있으면 적절하지 않을 때가 많다

행동이 아닌 기능 테스트에 집중하여 실수. 하나의 케이스로 충분치 않음

### 11.1.2 해결책: 각 동작을 테스트하는데 집중하라

예시) 주담대 신청 함수. 신청 조건으로 신용 등급, 대출 이력 등이고 신청되면 계산에 맞게 최대 대출금액 정산됨.  
이러한 각 동작은 테스트되어야 함. 신뢰도 높이기 위해 여러 값과 경계 조건 테스트 타당

테스트 코드 양 > 실제 코드 양 -> 모든 동작 제대로 테스트 안될 가능성 있음.

#### 모든 동작이 테스트되었는지 거듭 확인하라

코드가 제대로 테스트 되는지 여부 측정하는 한 방법
- 코드에 버그가 있어도 테스트가 여전히 통과 가능한지 생각해보는 것

요점은 테스트 대상 코드의 각 줄, 조건문, 논리 표현식, 값 등은 존재하는 이유가 있어햐 한다는 것. 불필요한 코드라면 제거되어야 함.

중요한 동작 모두 테스트 -> 기능 변경 시 최소 하나의 테스트 케이스 실패해야 함(오류 검사 코드는 예외)

돌연변이 테스트 도구 사용하면 약간 변형된 코드 만듬. 이 코드가 통과한다면 문제 있는 것임

#### 오류 시나리오를 잊지 말라

코드가 서로 다른 오류 시나리오를 처리하고 알리는 방법은 중요한 동작.

---

궁극적으로 중요한 모든 행동을 파악 및 각각에 대한 테스트 케이스 있는지 확인하는 것 효과적


## 11.2 테스트만을 위해 퍼블릭으로 만들지 말라

구현 세부 사항과 밀접하게 연관된 테스트가 될 수있고, 궁극적으로 우리가 신경 써야 하는 코드의 동작을 테스트하지 않을 수 있기 때문

### 11.2.1 프라이빗 함수를 테스트하는 것은 바람직하지 않을 때가 많다

함수를 프라이빗 -> 퍼블릭 만든 후 테스트 문제
1. 실제로 우리가 신경 쓰는 행동을 테스트하는 것이 아님
  - 그 함수가 통과해도 중요 동작이 잘못 수정되어도 확인 못할 수 있음
2. 구현 세부 사항에 독립적이지 못함
  - 리팩터링 하더라도 실패하면 안되는데 그럴 수 있음
3. 퍼블릭 API를 변경한 효과 가짐
  - 다른 개발자가 그 함수에 의존할 수 있음

 좋은 단위 테스트는 궁극적으로 중요한 행동을 테스트해야 함.
 - 코드 문제점 정확하게 감지 가능성 극대화
 - 구현 세부 사항에 독립적

### 11.2.2 해결책: 퍼블릭 API를 통해 테스트하라

프라이빗으로 바꾸면 '퍼블릭 API만을 이용한 테스트'라는 지침 깨뜨림

>실용적으로 하라  
어떤 행동이 중요하고 궁극적으로 신경 써야 하는 것이라면, 퍼블릭 API 여부와 상관없이 테스트되어야 함

### 11.2.3 해결책: 코드를 더 작은 단위로 분할하라

클래스가 복잡하여 퍼블릭 API 만으로 모든 동작 테스트 까다로울 때 -> 코드 추상화 계층 너무 크다는 의미 -> 문제 해결하는 더 작은 클래스로 분할을 고려할 시점

## 11.3 한번에 하나의 동작만 테스트하라

### 11.3.1 여러 동작을 한꺼번에 테스트하면 테스트가 제대로 안될 수 있다

예시) 쿠폰 목록에서 유효한(사용 안된, 유효기간 이내, 자신 소유) 쿠폰만 추려 내림차순 반환하는 함수.

한 테스트에 모든 동작 테스트
- 무엇 테스트하는지 이해 어려움 -> 이해하기 쉬운 테스트 원칙 깨뜨림
- 잘 설명된 실패 원칙 깨뜨림

### 11.3.2 해결책: 각 동작은 자체 테스트 케이스에서 테스트하라

각 동작을 개별적 테스트
- 테스트 케이스 이름 무엇하는지 정확
- 테스트 작동 방식 이해 쉬움

단점으로 코드 중복 많아짐 <- 해결책: 매개변수화 된 테스트 사용

### 11.3.3 매개변수를 사용한 테스트

매개변수 사용해 테스트 하는 기능 프레임워크 사용

>실패가 잘 설명되도록 하라  
매개변수 테스트 작성 시 각 매개변수 세트에 이름 추가하는 것은 일반적으로 선택 사항임. 다만 이를 생략하면 실패 설명이 제대로 되지 않음. 테스트 실패시 메시지 어떻게 보일지 고민하는 것 좋음

매개변수 테스트 지원 프레임워크
- C#의 NUnit
- Java의 JUnit 
- 자바스크립트의 Jasmine

매개변수 테스트 -> 모든 동작 테스트 유용 -> 많은 코드 중복 -> 테스트 프레임워크 사용

## 11.4 공유 설정을 적절하게 사용하라

일반적으로 두 가지 시점에서 공유 설정 코드 실행 설정 가능
1. BeforeAll: OneTimeSetUp, 테스트 케이스 실행 전 단 한번 실행됨
2. BeforeEach: SetUp, 각 테스트 케이스 실행 전 매번 실행됨

해체 코드 실행 방법도 제공
1. AfterAll: OneTimeTearDown, 모든 테스트 케이스 실행 후 해체 코드 한 번 실행됨
2. AfterEach: TearDown, 각 테스트 케이스 실행 후 매번 실행됨

설정을 서로 다른 테스트 케이스간 공유. 두 가지 중요한 방식
- 상태 공유: 설정된 모든 상태가 모든 케이스 간 공유됨.
  - 실행 시간 오래 걸림 or 비용 많이 드는 경우 유용
  - 그러나 설정 상태가 가변적인 경우 다른 케이스에 악영향 미칠 위험 있음
- 설정 공유: 테스트 케이스는 모든 설정을 공유
  - 특정 값 포함 or 특정 방식으로 의존성 설정 경우 그 모든 케이스는 의존성 가지고 실행됨
  - 테스트 단순화에 유용
  - 의존성 설정 비용 크면 유용 <- 양날의 검, 취약 및 비효과적 가능

### 11.4.1 상태 공유는 문제가 될 수 있다

각 케이스가 격리되어야 하는 규칙 위반하기 쉬움

이전 케이스가 상태를 변경한 것이 다음 케이스에 영향
- 테스트가 실패할 경우도 통과하는 경우 가능

가능하다면 상태 공유하지 않는 것이 최선. 꼭 필요하다면 다른 케이스 영향 않도록 조심

### 11.4.2 해결책: 상태를 공유하지 않거나 초기화하라

분명한 해결책 -> 애초에 공유하지 않는 것, 또는 BeforeEach 사용하여 각 케이스 새 인스턴스 생성 고려(그리 느리지 않다면)

또 다른 방법 -> 테스트 더블 사용. 페이크 객체 생성 빠름 -> 각 케이스에 만들기 좋음 -> 곧 상태 공유X

만약 페이크 사용 불가능 하다면 인스턴스 공유 어쩔수 없음 -> 각 케이스 간 반드시 상태가 초기화되도록 많은 주의 <- AfterEach 사용하여 수행

>전역 상태  
전역 상태 또한 상태 공유 가능. 각 케이스 마다 이 전역 변수 확실하게 초기화해야 함.

케이스 간 가변 상태 공유 안좋음 -> 피할수 있으면 공유X -> 못 피하면 케이스간 상태 초기화 -> 다른 케이스 악영향 최소화

### 11.4.3 설정 공유는 문제가 될 수 있다

예시) 두 가지 시나리오(케이스) 필요한 상황. BeforeEach 설정 공유.  
모든 케이스 중복 코드 피하는 장점. BeforeEach 를 수정한다면 모든 케이스 영향

>테스트 상수 공유  
설정 공유로 상수 사용해도 정확히 동일한 결과 얻을 수 있지만, 여전히 논의한 문제 발생 가능  
불변 데이터 타입 사용 바람직 <- 가변 상태 공유 안된다는 것 의미.

설정 공유 -> 코드 반복 피하는데 유용하지만 -> 의존성 정확하게 추적 매우 어려움 -> 향후 변경 시 목적한 동적 테스트 안될 가능성 있음

일반적으로 중요 값 or 상태는 공유 않는 것 최선

### 11.4.4 해결책: 중요한 설정은 테스트 케이스 내에서 정의하라

테스트 케이스 결과가 설정값에 직접 영향 받는 경우 -> 케이스 내에서 설정 베스트 -> 코드 변경 인한 의도치 않은 문제 발생 예방 -> 각 케이스 의미 있는 방식으로 설정 -> 각 케이스 원인, 결과 명확  
모든 종류 설정에 맞아떨어지는 설명은 아님

### 11.4.5 설정 공유가 적절한 경우

설정 공유하면서 각 케이스 결과에 직접적인 영향 미치지 않는 설정 있음

예시) 그다지 중요하지 않지만 객체 생성에 필요한 메타 데이터 필요. 공유 설정 통해 한 번만 정의 아주 타당.

>함수 매개변수는 꼭 필요한 것만 갖는 것이 이상적이다  
코드 동작에 별로 관련없는 설정 필요하다면 매개변수 목적이 명확하지 않다는 신호일 수 있음.

적절하게 사용 심사 숙고  
코드 반복 및 비용 많은 설정 vs 테스트 파악 어려움, 효과적이지 못함

## 11.5 적절한 어서션 확인자를 사용하라

어서션 확인자는 보통 테스트 통과 여부를 최종적으로 결정하기 위한 코드임.

실패하면 이유 설명하는 메시지 생성

가장 적절한 어서션 확인자 선택 중요

예제 코드)
- `assertThat(someValue).isEqualTo("expected value");`
- `assertThat(someList).contains(“expected value");`


### 11.5.1 부적합한 확인자는 테스트 실패를 잘 설명하지 못할 수 있다

예시) 순서 없는 리스트 반환 함수 테스트. 
- `isEqualTo` 로 테스트 하면 순서 때문에 실패 가능
- `contains` 로 바꿔 순서 문제 해결. 그래도 `isTrue` 를 사용하므로 실패 메시지가 문제 설명 못함

### 11.5.2 해결책: 적절한 확인자를 사용하라

대부분 최신 어서션 도구는 다양한 확인자 가짐. 

예시) 리스트 순서 상관없이 특정 할목 포함하는지 검증하는 확인자  
- 자바의 Truth 라이브러리의 `containsAtLeast()` 확인자

이전 예시에서 위 확인자를 사용하면 순서와 상관없이 테스트하며 실패 메시지가 이유 잘 설명함

## 11.6 테스트 용이성을 위해 의존성 주입을 사용하라

의존성 주입 -> 테스트 용이성 크게 향상

### 11.6.1 하드 코딩된 의존성은 테스트를 불가능하게 할 수 있다

예시) 의존성 주입 않고 생성자에서 의존 객체를 만듬  
- 실제 DB 데이터 사용하면 안되므로 테스트 안됨. 권한도 없음
- 데이터를 실제로 보내기까지 함. 테스트로 부터 외부 세계 보호 규칙 깨짐

손쉬운 해결책은 테스트 더블 사용. 하지만 내부에서 의존 객체 생성하기에 불가능

### 11.6.2 해결책: 의존성 주입을 사용하라

의존성 주입으로 페이크 객체를 사용하여 클래스 생성 쉬워짐

테스트 용이성은 모듈화와 밀접한 관련. 의존성 주입은 모듈화에 효과적인 기술. 곧 테스트 용이성에 효과적

## 11.7 테스트에 대한 몇 가지 결론

자주 접할 다른 두 가지 테스트 수준
1. 통합 테스트(integration test): 한 시스템 = 여러 구성요소 + 모듈 + 하위 시스템(통합). 이러한 통합이 제대로 작동하는지 확인하기 위한 테스트
2. 종단 간 테스트(e2e test): 처음부터 끝까지 전체 소프트웨어 시스템 통과하는 프로세스 테스트.

알고 있으면 좋을 몇 가지 유형의 테스트
1. 회귀 테스트: 소프트웨어의 동작이나 기능이 잘못 변경되지 않았는지 확인위해 정기적으로 수행하는 테스트
2. 골든 테스트: 특성화 테스트라고도 함. 일반적으로 주어진 입력 집합에 대해 코드가 생성한 출력을 스냅샷으로 저장한 것을 기반으로 함.
3. 퍼즈 테스트: 많은 무작위 값이나 흥미로운 값으로 코드를 호출 후 동작 멈추지 않는지 점검

테스트는 방대한 주제임. 단위 테스트는 빙산의 일각. 단위 테스트만으로 모든 테스트 요구 사항 충족 못함. 그래서 다양한 테스트 유형과 수준 알고 새로운 도구, 기술 등 최신 정보 유지 좋음.

## 요약

- 모든 중요한 행동 파악 후 각각의 테스트 케이스 작성하는 것이 효과적
- 결과적으로 중요한 동작을 테스트해야 함
- 한 번에 한 가지만 테스트 -> 테스트 실패 이유 잘 드러나고 이해하기 쉬움
- 테스트 설정 공유는 양날의 검. 코드 반복 및 비용 vs 잘못 사용하면 신뢰 불가 및 안좋음
- 의존성 주입 -> 테스트 용이성 향상
- 높은 품질의 소프트웨어 작성 및 유지 위해 여러 가지 테스트 기술 함께 사용해야 함
