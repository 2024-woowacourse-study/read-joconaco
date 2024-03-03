# Ch3. 다른 개발자와 코드 계약

## 3.1 자신의 코드와 다른 개발자의 코드
- 자신의 코드와 다른 개발자의 코드는 서로 의존함
- 다른 개발자가 변경하거나 코드와 상호작용할 때 발생할 수 있는 문제는 없는지 확인 필요
  - 또, 문제가 발생한다면 그 문제를 어떻게 완화하기 위해 **선제적으로 조치**하는 것이 필요
  - 
### 자신에게 분명한 코드가 다른 개발자에게는 분명하지 않을 수 있음
   - 코드가 어떻게 사용되어야 하는지, 무엇을 하는지, 왜 그런 일을 하고 있는지 설명해야 함
     - **주석보다는 코드 자체로 설명이 되도록 하는 것이 좋은 방법**

### 다른 개발자가 무의식적으로 코드를 망가뜨릴 수 있음
- 사전 지식을 가지고 있지 않은 개발자가 코드를 오용할 수 있음
- 무언가 **문제가 있을 때 컴파일이 중지되거나, 테스트가 실패하도록 만드는 것이 좋음**
- 배경지식이 없어도 이해하기 쉬워야 하고, 잘 작동하던 코드에 버그가 발생하는 것이 어려워야 함

## 3.2 다른 사람들이 코드를 읽는 방법
- **함수, 클래스, 열거형 등의 이름**
  - 코드의 의도와 사용 방법에 대해 가장 잘 전달할 수 있음
- **매개변수 또는 반환값의 데이터 타입**
  - 컴파일 에러를 유발하기 때문에 코드를 오용하거나 오작동할 수 없도록 할 수 있음
- **문서나 주석 확인**
  - 위 두 방법에 비해 신뢰도 낮음
- **세부 구현 사항 확인**
  - 코드 사용 방법을 알기 위해 개발자가 세부 구현 사항을 읽어야 한다면 추상화 계층의 많은 이점을 부정하게 되는 것

## 3.3 코드 계약
- 코드에서 계약을 정의할 때 명확한 부분과 세부 조항이 존재
  - **명확한 부분**
    - 함수와 클래스 이름
    - 인자 유형
    - 반환 유형
    - 검사 예외 (checked exception)
  - **세부 조항**
    - 주석문과 문서
    - 비검사 예외 (unchecked exception)
- 세부 조항에 너무 의존하게 되면 오용하기 쉬운 취약한 코드가 되고, 예상과 다르게 동작할 수 있음
- 코드 계약의 **분명한 항목을 통해 명확하게 설명하는 것이 바람직**
  - **잘못된 일을 하는 것을 처음부터 불가능하게 만드는 것이 좋음**
    - 코드가 오용되거나 잘못 설정되면 컴파일조차 되지 않도록 하는 것이 목표

## 3.4 체크 및 어서션
- 컴파일러를 사용하여 코드 계약을 확인하기 어려운 경우, 대안으로 런타인 검사라도 사용
  - 컴파일 타임 확인만큼 강력하지 않음
  - 코드를 실행하는 동안 발생하는 문제에 대한 테스트(사용자)에 의존하기 때문

### 체크
- 코드 계약이 준수되었는지 확인하는 추가 로직
- 준수되지 않았을 경우 실패를 유발하는 오류를 생성
- 버그가 아무도 모르게 발생하는 것을 방지함으로써 세부 조항이 많았던 원래의 코드 개선
- 오용을 아예 불가능하게 만드는 해결책보다는 이상적이지 않음
- 코드가 배포되고 사용자가 사용하기 전까지 버그가 노출되지 않을 수 있음
- 예외가 발생했음에도 알아차리지 못할 수 있음
  - 시스템이 죽는 것을 방지하기 위해 프로그램의 상위 수준에서 예외를 처리해버리는 경우

### 어서션
- 체크와 거의 같은 방식으로 동작 
- 체크와는 다르게 배포를 위해 빌드할 때 어서션은 보통 컴파일에서 제외됨