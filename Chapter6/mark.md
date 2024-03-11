# Chapter6 예측 가능한 코드를 작성하라

다룰 내용
- 코드가 어떻게 예측을 벗어나 작동할 수 있는지
- 예측을 벗어난 코드가 어떻게 버그로 이어지는지
- 코드가 예측을 벗어나지 않도록 보장하는 방법

궁극적으로 개발자는 코드를 사용하는 방법에 대한 정신 모델 구축  
코드 계약 발견 + 사전 지식 + 적용 가능 공통 패러다임 근거 -> 정신모델  

if 코드 하는 일 != 정신모델  

	버그 가능성 증가

## 6.1 매직값을 반환하지 말아야 한다

매직값 - 함수의 정상적 반환 유형이지만 특별한 의미

### 6.1.1 매직값은 버그를 유발할 수 있다

레거시 or 신중하게 최적화 코드 <- 어느정도 합리적 이유  
일반적으로 사용하지 않는 것이 바람직

예시) 사용자 평균 연령 구할 때  
getAge() 가 -1 반환 모른다면 문제

### 6.1.2 해결책: 널, 옵셔널 또는 오류를 반환하라

코드 계약의 명백한 부분에서 확인하도록 하는 것이 좋음  
-> 널(널 안전성), 옵셔널 값을 반환 추천

널, 옵셔널의 단점 - 값이 없는 이유 명시적 전달 안함  
상황 구별 필요 -> 오류 전달 기법 사용 고려

### 6.1.3 때때로 매직값이 우연히 발생할 수 있다

예시) 초기값으로 매직값 넣고 빈 목록 반복문 돌리면 초기값 그대로 반환  
어떤 상황에서는 타당할 수 있지만 아닐 확률도 큼

>프로그래밍 언어 기능 적절한 사용  
리스트에서 최솟값 찾아주는 기능이 있다면 그것 사용

## 6.2 널 객체 패턴을 적절히 사용하라

널값 대신 유요한 값 반환되어 피해 최소화

- 빈 문자열, 빈 리스트 반환
- 모든 멤버 함수 아무일 안함 or 기본값 반환 클래스 구현 <- 더 정교한 형태

### 6.2.1 빈 컬렉션을 반환하면 코드가 개선될 수 있다

컬렉션에 값이 없을 수 있음. 두가지 방법

1. 널값 반환

서로 다른 이유로 널 반환이 가능할 때 차이를 구분하기 힘듬

2. 널 객체 패턴

예시) `func(attribute == null) -> return new Set();`  
=> return func(elem).contains("str");

코드는 간단해지고 예측 벗어나는 작동 가능성 낮아짐  
복잡한 상황에서는 예측 벗어나는 작동 가능성 커짐. 다음 하위절 이유 설명

>널 포인터 예외  
널 객체 패턴 사용 지지 <- Null 관련 예외 발생을 최소화 주장  
하지만 널 안정성 or 옵셔널을 사용 가능하면 그 주장은 의미 없음(레거시 코드 제외)

### 6.2.2 빈 문자열을 반환하는 것도 때로는 문제가 될 수 있다

문자열이 문자의 의미가 모아 놓은 것을 넘어설 때, 빈 문자열 반환 문제 가능성

#### 문자들의 모음으로서의 문자열

문자열을 입력하지 않은 것인지 빈 문자열 입력한 것인지 구분 의미 없음

#### ID로서의 문자열

문자열이 실행할 논리에 영향 미침.   
문자열 없음을 수 있음을 명시적으로 인식 중요

빈 문자열 반환시 항상 널이 되지 않으므로 오해 소지. 오히려 널 반환이 나음

### 6.2.3 더 복잡한 널 객체는 예측을 벗어날 수 있다

호출 쪽에서 빈 상자 받고 황당할 가능성 있다면, 널 객체 패턴 피하는 것이 좋음

예시) 머그잔 랜덤 반환 함수. 재고 없을 때, 크기 0 머그잔 반환  
보고서 작성시 크기 0 머그잔 때문에 틀린 보고서 만들어짐

예시에서 널을 처리하지 않아도 되기 때문에 코드 작성 간단하지만 예상 벗어날 수 있음

이 경우에도 널 반환이 더 나을 수 있음

### 6.2.4 널 객체 구현은 예상을 벗어나는 동작을 유발할 수 있다

널 객체 패턴 -> 널 객체 전용 인터페이스 or 클래스 정의

예시) NullMug 구현. 함수들 아무일 안하고 0 반환. 이전 예와 같은 결과 가짐

널 객체 보다 널 확인이 더 편할 수 있음

---

#### 6.2. 결론

널 객체 패턴을 사용할 때, 정말 적절한지 예상 벗어난 동작 가능성 있는지 의식적으로 생각  
널 안정성과 옵셔널 인기에 따라 널 객체 패턴 설득력 떨어짐


## 6.3 예상치 못한 부수 효과를 피하라

부수 효과(side effect)는 어떤 함수의 호출이 함수 외부에 초래한 상태 변화 의미.  
함수가 반환 하는 값 외 다른 효과 있다면 부수 효과 있는 것

일반적 부수 효과 유형
1. 사용자에게 출력 표시
2. 파일 or 데이터베이스에 무언가 저장
3. 다른 시스템 호출하여 네트워크 트래픽 발생
4. 캐시 업데이트 or 무효화

부수 효과는 불가피함. 적어도 코드 일부는 부수 효과 있어야 함을 의미

부수 효과 피하는 방법
1. 애초에 일어나지 않도록 하는 것 <- 6.4절
2. 클래스를 불변으로 만듬 <- 7장

부수 효과가 원하는 기능 일부 or 피할 수 없을 때, 호출 쪽 확실하게 인지하도록 하는 것 중요

### 6.3.1 분명하고 의도적인 부수 효과는 괜찮다

예시) displayErrorMessage() <- 부수 효과: 캔버스가 업데이트 됨  
호출 쪽에서 원하고 예상하는 바

### 6.3.2 예치기 않은 부수 효과는 문제가 될 수 있다

#### 부수 효과는 비용이 많이 들 수 있다

#### 호출한 쪽의 가정을 깨트리기

함수명이 부수 효과 일으키지 않을 것 같기 때문에 오해

#### 다중 스레드 코드의 버그

데이터 레이싱 문제 가능. 스레드 관련 버그는 디버깅 및 테스트 어려움

### 6.3.3 해결책: 부수 효과를 피하거나 그 사실을 분명하게 하라

1. 확실히 부수 효과가 필요할 때 사용
2. 함수명을 부수 효과 나타나게 작성 <- 좋은 이름 중요한지 보여줌
	- 부수 효과 비용이 높이 때문에 호출 쪽에서 사용 고민
	- 함수 호출 쪽 함수도 이름에 부수 효과 포함하게 변경
	- 부수 효과 알고 스레드 위험성 인지

---

#### 6.4 요약

부수 효과 없는 코드 작성이 베스트. 피할 수 없을 때 적절하게 이름 짓기 


## 6.4 입력 매개변수를 수정하는 것에 주의하라

특정 부수 효과, 함수 내 입력 매개변수 수정하는 것 살펴봄

### 6.4.1 입력 매개변수를 수정하면 버그를 초래할 수 있다

예시) 빌려준 책을 찢고 낙서 <- 함수가 매개변수 수정 유사


### 6.4.2 해결책: 변경하기 전에 복사하라

값 복사 -> 메모리, CPU 관련 성능 영향. 하지만 버그에 비하면 큰 문제 없는 경우가 많음  
성능 상 입력 매개변수 변경시, 함수명과 문서에 분명히 하는 것이 좋음

>입력 매개변수를 변경하는 것이 때로는 일반적이다  
C++  많은 코드에서 출력 매개변수 개념 활용, 효율적이고 안전하게 반환  
최근에는 무브 시맨틱스(move semantics)를 제공

## 6.5 오해를 일으키는 함수는 작성하지 말라

코드 계약의 명백한 부분 누락되면 예기치 못함. 거기에 오해의 소지가 있다면 더 좋지 않음

### 6.5.1 중요한 입력이 누락되었을 때 아무것도 하지 않으면 놀랄 수 있다

예시) 매개변수 없다면(널 이라면) 아무것도 안하는 함수.  
명백한 부분에 아무것도 안하는 것이 전혀 나타나지 않음. 호출 쪽에서 법적 고지 사항 출력을 원했는데 안하면 법을 위반할 수 있음

### 6.5.2 해결책: 중요한 입력은 필수 항목으로 만들라

필수 매개변수는 해당 함수에 중요하다는 의미. 값 없으면 함수 호출 못하는 것이 더 안전할 수 있음

---

### 6.5 결론

널 확인 코드 호출하는 쪽에 코드 줄 수 증가하지만, 잘못 해석 및 예상과 다른 동작 가능성 줄어듦

버그 수정 시간 보다 널 여부 확인이 낫기 때문에 코드를 명확하게 작성하는 것이 코드 몇 줄 추가보다 훨씬 좋다

## 6.6 미래를 대비한 열거형 처리

열거형 찬반 주장
- 찬성
  1. 형 안정성 제공
  2. 시스템에 유효하지 않은 입력 방지
- 반대
  1. 열거형의 특정 값 처리 논리가 코드 전반에 퍼짐 -> 간결한 추상화 계층 막음
  2. 다형성이 더 나은 방식

찬반 상관없이 열거형 다뤄야 하는 이유
1. 다른 사람의 코드를 사용해야 하며 그들이 열거형 즐겨 사용할 수 있음
2. 다른 시스템에서 제공하는 결과 사용 시, 열거형은 종종 데이터 형식에서 유일하게 실용적 옵션일 수 있음

열거형 사용 경우 나중에 더 많은 값 추가 가능을 기억하는 것 중요

### 6.6.1 미래에 추가될 수 있는 열것값을 암묵적으로 처리하는 것은 문제가 될 수 있다

예시) 미래 예측 결과 열거형(회사_망함, 회사_이득봄) 2 가지. if 망함 -> return false;  
열거형에 세계종말 추가된다면 true 반환되고 세계종말함

### 6.6.2 해결책: 모든 경우를 처리하는 스위치 문을 사용하라

열것값을 명시적으로 처리, 처리 되지 않은 열거값 추가 시 컴파일 실패 or 테스트 실패하게 함

예시) switch 문에서 return, 바깥에 비검사 예외. 새 열거값 추가 시 예외 발생함으로 명시적 처리

>컴파일 타임 안정성  
일부 언어의 컴파일러는 모든 열것값 처리 안하면 경고 생성할 수 있음

### 6.6.3 기본 케이스를 주의하라

기본(default) 케이스는 향후 열거값이 암시적 처리될 수 있고 잠재적 버그 발생 가능

#### 기본 케이스에서 예외 발생

6.6.2절과 다르지 않아보이지만, 일부 언어에서 미묘하게 오류 발생 가능성 높음

모든 열거값 처리 안하면 컴파일러 경고를 통해 보호받음.  기본 케이스 사용 시, 보호 못받음

### 6.6.4 주의 사항: 다른 프로젝트의 열거형에 의존

내 코드가 다른 조직 열거형에 의존한다면, 추가될 열거값 처리를 위해 허용 범위가 넓어질 수밖에 없음. 스스로 판단해야함

## 6.7 이 모든 것을 테스트로 해결할 수는 없는가?

예상 벗어난 코드 방지 위한 노력에 반대 주장  
-> 테스트가 모든 문제 잡아낸다 주장

다른 개발자가 내 코드를 사용할 때 올바르게 작동하도록 하기 위한 작업임. 다음 이유로 테스트만으로는 보증하기 충분치 않음

- 테스트를 대충 작성할 수 있음. 특정, 큰 문제에서만 드러남
- 테스트가 항상 실제 상황 시뮬레이션 하는 것은 아님. 목 객체를 통해 테스트 한다면 생각하는 바대로 작성함.
- 어떤 것들은 테스트하기 매우 어려움. 멀티 스레딩 등 큰 규모에서만 문제 나타날 수 있기 때문.

다시 강조하자면 테스트 매우 중요. 좋은 코드도 고품질 테스트 대체 불가. 하지만 반대도 마찬가지, 테스트도 예상 벗어나는 코드 방지 어려움.

## 요약

- 다른 사람 작성 코드는 종종 내가 작성 코드에 의존
  - 코드 기능 잘못 해석 경우, 버그 발생 가능 큼
  - 코드 호출쪽에서 예상 동작 방법, 중요한 세부 사항을 명백한 부분에 포함시킴

- 우리가 사용하는 코드를 허술하게 가정 하면 예상을 벗어난 결과 가능
  - 예시로 열거형에 추가되는 새 값 예상 못한 경우
  - 의존한 코드가 가정 벗어날 경우, 컴파일 or 테스트 실패하는 것 중요

- 테스트만으로 예측 벗어난 코드 문제 해결 불가능

