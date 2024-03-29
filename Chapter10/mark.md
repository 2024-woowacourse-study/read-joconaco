# Chapter10 단위 테스트의 원칙

다룰 내용
1. 단위 테스트 기본 사항
2. 좋은 단위 테스트 되기 위한 조건
3. 테스트 더블
4. 테스트 철학

코드가 처음 작성될 때 그리고 수정될 때마다 코드가 의도한 대로 작동한다는 것을 확신할 수 있는 방법 -> 테스트

**단위 테스트**는 상대적으로 격리된 방식으로 코드의 구별되는 단위를 테스트하는 것에 관한 것.

**코드의 단위** 는 특정 클래스, 함수, 코드 파일을 의미할 때가 많다.

**상대적으로 격리된 방식**도 해석 여지 다양.  
대부분 코드는 다른 코드에 의존. 개발자에 따라 의존하는 코드를 차단 or 의존 코드를 포함하여 테스트 선호

단위 테스트의 정의보다 궁극적으로 중요한 것은 코드를 잘 테스트하고 유지보수할 수 있는 방법으로 수행하는 것임.

## 10.1 단위 테스트 기초

단위 테스트 관련 중요한 개념 및 용어
- 테스트 중인 코드: 실제 코드, 테스트 대상 코드 의미
- 테스트 코드: 단위 테스트를 구성하는 코드
- 테스트 케이스: 각 특정 동작이나 시나리오를 테스트. 보통 다음 세 개의 섹션 나뉨
  - 준비(arrange): 테스트할 특정 동작을 위한 몇 가지 설정을 수행
  - 실행(act): 테스트 중인 동작을 실제로 호출하는 코드
  - 단언(assert): 동작 실행 후 실제로 올바른지 확인
- 테스트 러너: 실제로 테스트를 실행하는 도구

>given, when, then  
일부 개발자는 AAA 보다 GWT 용어 선호. 테스트 케이스 내의 맥락에서 의미는 동일함

실제 코드가 보여주는 모든 동작에 테스트 케이스가 있을 것이라 예상하며 이것이 이상적이고 그렇게 되도록 노력해야 함

나쁜 테스트는 언젠가 사고남. 나쁜 테스트라고 말할 명백한 경우는 아예 테스트가 없는 경우지만 유일한 경우는 아님

## 10.2 좋은 단위 테스트는 어떻게 작성할 수 있는가?

좋은 단위 테스트가 가져야 할 5가지 주요 기능 정의
1. 훼손의 정확한 감지: 코드가 실제로 훼손된 경우에만 실패해야 함
2. 세부 구현 사항에 독립적: 세부 사항 변경되도 테스트 코드는 변경 않는 것 이상적
3. 잘 설명되는 실패: 테스트가 실패하면 원인과 문제접을 명확하게 설명
4. 이해할 수 있는 테스트 코드: 정확히 무엇을 테스트하기 위한 것, 어떻게 수행되는지 이해할 수 있어야 함
5. 쉽고 빠르게 실행: 테스트는 일상 작업임. 느리거나 실행 어려우면 시간 낭비

### 10.2.1 훼손의 정확한 감지

단위 테스트의 가장 명확하고 주된 목표
- 코드가 훼손되지 않았는지 확인하는 것
-> 즉, 코드가 위도대로 수행 및 버그 없음 확인.

위는 매우 중요한 두 역할 수행
1. 코드에 대한 초기 신뢰를 줌: 코드가 코드베이스에 병합 전 실수를 발견 및 수정
2. 미래의 훼손을 막아줌: 실수로 코드 훼손 가능성 존재. 테스트를 통해 효과적 방어.  
코드 변경으로 돌아가던 기능 작동않는 것을 **회귀**라고 함. 이를 탐지할 목적으로 테스트 실행하는 것을 **회귀 테스트**라고 함

**플래키**(flakey): 테스트 대상 코드가 정상임에도 때론 통과 때론 실패하는 테스트.  
이는 보통 무작위성, 타이밍 기반 레이스 조건, 외부  시스템에 의존하는 등 테스트의 비결정적 동작에 기인함.
아무것도 아닌 것인데 실패 원인 찾느라 시간 낭비

코드 일부분이 훼손될 때 and 오직 훼손된 경우에만 테스트 실패 매우 중요

### 10.2.2 세부 구현 사항에 독립적

코드베이스 변경 두 종류
1. 기능적 변화: 코드가 외부로 보이는 동작 수정. 예시) 기능 추가, 버그 수정 등
2. 리팰터링: 코드의 구조적 변화 의미. 외부 동작이 변경되면 안됨

단위 테스트 작성 두 가지 접근 방식
A: 테스트가 모든 동작뿐 아니라 세부 사항도 확인함. 프라이빗 접근하여 테스트 등
B: 동작만 테스트하고 세부 사항 확인안함. 코드의 공개 API를 사용하여 동작 확인

위 접근 방식에 따른 리팩터링 후 결과
A: 테스트가 실패함. 통과하려면 코드의 많은 코드 변경 및 확인
B: 테스트 여전히 통과. 실패한다면 리팩터링이 잘못 된것임.

>기능 변경과 리팩터링을 같이 하지 말라  
두 작업을 동시에 수행하는 것은 좋지 않음.  
동시에 하면 기능적 변화로 예상되는 동작과 리팩터링 실수와 구분 힘듬.

테스트가 세부 구현에 독립적이면 리팩터링 실수를 확인하는 테스트 결과 신뢰 가능

### 10.2.3 잘 설명되는 실패

테스트 실패해도 무엇이 잘못됀지 알려주지 않으면 시간 낭비해야 함.

테스트 작성시 문제 발생 시 어떤 실패 메시지 만들지, 그것이 유용할지 생각

잘 설명된 테스트 실패
1. 하나의 테스트는 한 가지 사항만 검사
1. 테스트 케이스 이름으로 어떤 동작 테스트인지 서술적 이름 사용
2. 실패 메시지가 명확

### 10.2.4 이해 가능한 테스트 코드

새로운 요구 사항 충족 위해 코드 기능을 의도적으로 수정할 때 테스트가 실패할 수 있음  
새로운 기능 반영 위해 테스트 코드도 수정

-> 일반적인 문제 발생 두 경우
1. 한 번에 너무 많은 것을 테스트
2. 너무 많은 공유 테스트 설정을 사용

일부 개발자들은 테스트 코드를 일종의 사용 설명서로 사용함. 따라서 이해하기 쉽게 만들어야 함

### 10.2.5 쉽고 빠른 실행

1. 대부분 단위 테스트는 자주 실행. 만약 테스트 실행 한시간 걸린다면 시간 낭비
2. 테스트 할 수 있는 기회 극대화. 테스트 힘들며 하기 싫음

테스트 쉽고 빠른 실행 -> 개발자 더 효율적 작업, 테스트 광범위 및 철저해짐

## 10.3 퍼블릭 API에 집중하되 중요한 동작은 무시하지 말라

이 원칙 따르면 테스와 구현 세부 정보 결합하지 않고, 호출하는 쪽에서 실제로 신경 쓰는 사항만 테스트하는데 집중

### 10.3.1 중요한 동작이 퍼블릭 API 외부에 있을 수 있다

필자의 경험으로, 정작 중요한 동작 테스트 않고 방치하는 것을 정당화 하려고 '공용 API만 이용한 테스트'를 언급하는 상황 봄.

퍼블릭 API를 어떻게 정의하느냐에 따라 그것 만으로 모든 동작 테스트할 수 없는 경우 있음. 다음은 몇가지 예시
1. 서버와 상호작용하는 코드: 서버에 어떤 부수 효과 있는지 확인해야 할 수 있음
2. 데이터베이스에 값을 저장하거나 읽는 코드: 코드가 부수 효과로 DB에 어떤 값 저장하는지 확인해야 할 수 있음

궁극적으로 중요한 것은 코드의 모든 중요한 동작을 제대로 테스트 하는 것, 퍼블릭 API라고 생각하는 것만으로는 불가능할 경우 있음. 그러나 다른 대안이 없는 경우에만 퍼블릭 API를 벗어나 테스트.

## 10.4 테스트 더블

의존성을 실제로 사용하는 것에 대한 대안으로 **테스트 더블**이 있음.  
이는 의존성을 시뮬레이션하는 객체지만 테스트에 더 적합하게 사용할 수 있도록 만들어짐.

세 가지 유형의 테스트, 더블 목(mock), 스텁(stub), 페이크(fake)에 대해 살펴봄

### 10.4.1 테스트 더블을 사용하는 이유

테스트 더블 사용 일반적인 세 가지 이유
1. 테스트 단순화: 일부 의존성 테스트에 사용 까다롭고 힘듬.
2. 테스트로부터 외부 세계 보호: 일부 의존성은 실제 부수 효과(서버 요청, DB 값 쓰기 등) 발생. 
3. 외부로부터 테스트 보호: 외부 세계는 비결정적(다른 시스템이 쓴 값을 읽는다면 나중에 변경될 수 있음)일 수 있음.

#### 테스트 단순화

의존성 설정에 많은 노력 필요할 수 있음

테스트 단순화의 또 다른 동기는 더 빠르게 실행하는 것

오히려 테스트 더블이 복잡할 수 있으므로 사용 여부는 사례별로 고려해야함

#### 테스트로부터 외부 세계 보호

예시로 고객 은행 계좌에 돈 인출하는 코드 테스트. 실제 실행되면 실제 돈 인출될 것.  

폭넓게 보면, 실제 서버로 요청 전송 or 실제 DB에 값쓰는 부수 효과 유발 가능.  
치명적이진 않지만 다음 문제 발생 가능
- 사용자는 이상하고 혼란스러운 값을 볼 수 있다: '테스트' 레코드가 사용자게에 표시된다면 혼란
- 모니터링 및 로깅에 영향을 미칠 수 있다: 오류 응답이 올바른지 테스트위해 서버레 일부러 잘못된 요청 전송 -> 서버 오류율 증가 -> 모니터링 영향


#### 외부로부터 테스트 보호

실제 의존성은 비결정적인 동작 가능. 

예시) 난수 생성기 사용한 ID 생성하는 실제 의존성. 테스트 결과를 신뢰하기 어려울 때 있음. 

---

실제 의존성 사용이 나쁘거나 현실적으로 불가능한 경우 -> 테스트 더블 사용 판단 -> 어떤 테스트 더블 사용할지 결졍

### 10.4.2 목

목은 클래스나 인터페이스를 시뮬하는데 멤버 함수에 대한 호출될 때 인수에 제공되는 값을 기록하는 것 외에는 어떠한 일도 수행하지 않음. 

테스트 대상 코드가 의존성 통해 제공되는 함수를 호출하는지 검증위해 사용.  
따라서 테스트 대상 코드에서 부수 효과를 일이키는 의존성 시뮬레이션에 가장 유용

예시) 은행계좌에서 고객계좌와 청구서 금액을 입력받아 금액 만큼 인출하는 함수.  
고객 계정에서 정확한 금액 인출을 테스트 해야함. 하지만 실제 계좌 사용 못함

계좌 인터페이스에 대한 목 객체 만들고 확인하는 방법
계좌 목 객체 생성 -> 테스트 대상 코드 함수에 전달 -> 객체 상태 확인

단점 -> 테스트가 비현실적, 중요한 버그를 잡지 못할 위험

### 10.4.3 스텁

스텁은 함수가 호출되면 미리 정해 놓은 값을 반환함으로써 함수를 시뮬레이션.  
-> 테스트 대상 코드가 특정 멤버 함수를 호출하여 특정 값 반환하도록 의존성 시뮬레이션  
따라서 테스트 대상 코드가 의존하는 코드로부터 어떤 값을 받아야 하는 경우 유용

목과 스텁 차이 있지만 일상적으로 목이 둘 다 지칭함

예시) 인출 전 계좌 잔액 확인 로직 추가.  
추가해야 하는 테스트 케이스
1. 잔액 부족 경우 이를 나타낼 결과 반환
2. 잔액 부족 경우 계좌로 부터 인출안되야 함
3. 잔액이 충분할 때 계좌 인출되야 함

잔액 확인 함수에 스텁을 사용하면 됨. 미리 정해진 값을 반환하도록 하여 결정적이고 테스트 결과 신뢰 가능

테스트 도구에서 스텁을 만드는 경우에도 목 객체를 만들어야 함.

### 10.4.4 목과 스텁은 문제가 될 수 있다

목과 스텁의 두 가지 주요 단점
- 목 or 스텁이 실제 의존성과 다른 방식으로 동작이 설정되면 테스트는 실제적이지 않음
- 구현 세부 사항과 테스트가 밀접하게 결합하여 리팩터링 어려워질 수 잇음

#### 목과 스텁은 실제적이지 않은 테스트를 만들 수 있다

목 예시) 마이너스 잔액이될 것이라 암묵적으로 가정 및 테스트. 실제는 예외가 발생하는 상황. 버그가 명확하지만 목을 사용하여 버그 안드러남 <- 주요 단점 중 하나

스텁 예시) 코드 계약을 제대로 고려 안해 의존성 코드가 반환하지 못하는 값 반환 가능

#### 목과 스텁을 사용하면 테스트가 구현 세부 정보에 유착될 수 있다

예시) 마이너스 잔액 버그 발견하여 조건문 도입하여 둘 중 하나의 함수 호출.  
둘 중 하나의 함수를 호출하는지 확인하는 테스트 작성 -> 이는 구현 세부 사항에 속함.  
리팩터링(세부 사항만 변경) 후 테스트가 안돌아감

---

필자는 목, 스텁 사용에 대해 최소한으로 사용하는 것이 최선이라 주장.  
대안 없다면 아예 테스트 코드를 작성하지 않는 것보다 사용하는 것이 나음  
개인적 의견으로 실제 의존성 or 페이크 사용이 가능하다면 그것이 보통 더 바람직

### 10.4.5 페이크

페이크는 클래스의 대체 구현체로 테스트에서 안전하게 사용 가능.

실제 의존성의 공개 API를 정확하게 시뮬레이션

일반적으로 단순한 구현
- 외부 시스템과 통신하는 대신 페이크 내의 맴버 변수에 상태를 저장

페이크의 요점
- 실제 의존성과 동일 -> 실제 클래스처럼 동작
- 따라서 실제 의존성 코드와 페이크 코드를 같은 팀이 유지보수 <- 코드 계약이 동일하기 때문

예시) 은행계좌 인터페이스를 구현하는 페이크 클래스. 
- 실제 은행 대신 멤버 변수를 사용하여 계좌 잔고 추적
- 마이너스 금액 호출의 경우 실제 코드와 마찬가지로 예외 발생
  - 이런 세부 사항 때문에 페이크가 유용

#### 페이크로 인해 보다 실질적인 테스트가 이루어질 수 있다

세부 구현 사항 까지 테스트가 잘 확인할 수 있음

#### 페이크를 사용하면 구현 세부 정보로 부터 테스트를 분리할 수 있다

페이크 이점 중 하나
- 구현 세부 사항에 덜 밀접하게 결함됨

페이크 구현 좋은 상황
1. 코드를 작성했는지 여부
2. 코드를 계속 유지보수할 의지
3. 실제 코드를 테스트에 사용하는 것 부적합

### 10.4.6 목에 대한 의견 

목과 스텁 사용. 두 가지 의견
- 목 찬성론자: '런던 학파'. 의존성 코드로 부터 값을 받는 경우 스텁 사용
- 고전주의자: '디트로이트 학파'. 목 최소한 사용, 의존성 실제로 사용 최우선. 불가능할 때 페이스 사용 선호. 목은 페이크도 불가능할 때 최후 수단

두 가지 방법 테스트 차이점
- 목 사용 -> 상호작용 테스트 -> 대상 코드가 **어떻게**
- 고전주의 방법 -> 코드의 결과 상태와 의존성을 테스트 -> 코드 실행 최종 결과가 **무엇인지**

목 사용 지지 주장
- 단위 테스트 더욱 격리
- 테스트 코드 작성 쉬워짐

고전주의적 지지 주장
- 목은 실제 호출이 유효한지 검증X. 문제있어도 통과 가능
- 목 안쓰면 구현 세부 사항에 더 독립적인 테스트 가능.

필자는 목 사용이 동작 제대로 테스트 않고, 리팩터링 매우 어렵게 만든다고 생각

### 10.5 테스트 철학으로부터 신중하게 선택하라

테스트 철학 예시
- 테스트 주도 개발(TDD)
  - 코드 구현 전 테스트 코드 먼저 작성
  - 실제 코드는 테스트만 통과하도록 최소한 작성, 이후 리팩터링
- 행동 주도 개발(BDD)
  - 사용자, 고객, 비즈니스 관점에서 소프트웨어가 보여야 할 행동(or 기능)을 식별하는데 집중하는 것
- 수용 테스트 주도 개발(ATDD)
  - 고객 관점에서 소프트웨어가 보여줘야 할 동작(기능)을 식별하고 소프트웨어가 전체적으로 필요에 따라 동작하는지 검증하기 위해 자동화된 수락 테스트(cceptance test)를 만드는 것을 수반
  - TDD와 같이 코드 구현 전 테스트 생성
  - 이론적으로 테스트가 모두 통과 -> 소프트웨어는 완성, 고객 수락 준비 됨

---

궁극적으로 달성하고자 하는 목표가 그 목표에 도달하기 위해 선택한 작업 방식보다 더 중요함. 중요한 것은 좋은 테스트 코드, 고품질 소프트웨어 생산.

## 요약

- 코드베이스에 제출된 거의 모든 코드는 단위 테스트 동반되야 함
- 실제 코드의 모든 동작을 실행 및 결과를 확인하는 테스트 케이스 작성되야함
  - 준비, 실행, 단언 세 파트로 나누는 것 일반적
- 바람직한 단위 테스트 주요 특징
  1. 문제가 생긴 코드의 정확한 탐지
  2. 구현 세부 정보에 구애받지 않음
  3. 실패가 잘 설명됨
  4. 이해하기 쉬운 테스트 코드
  5. 쉽고 빠르게 실행
- 테스트 더블은 실제 의존성 사용 불가능 or 힘들 때 사용 가능.
  - 목, 스텁, 페이크 가 있음
- 목, 스텁 테스트 코드는 비현실적, 세부 정보에 밀접 연결 가능
- 목 사용에 대한 필자의 의견
  - 가능한 실제 의존성이 테스트 사용. 페이크 차선책. 목과 스텁은 최후의 수단으로만 사용


