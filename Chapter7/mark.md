# Chapter7 코드를 오용하기 어렵게 만들라

다룰 내용
- 코드 오남용으로 버그가 발생하는 방식
- 코드 오용하기 쉬운 흔한 방식
- 코드 오용 어렵게 만드는 기술

코드를 잘못 사용할 수 있는 멸가지 일반적인 경우
- 호출하는 쪽에서 잘못된 입력 제공
- 다른 코드의 부수 효과
- 정확한 시간, 순서에 따라 함수 호출 안함
- 관련 코드가 가정에 맞지 않게 수정

>오용하기 어려움  
오용 어렵게 함으로 문제 피하려는 생각은 설계, 제조 분야에서 잘 확립된 원칙  
예시로 시게오 신고가 만든 포카 요케의 린 제조 개념. 이는 방어적 디자인 원칙의 공통적인 특징. 실제 예시는 
>- 소켓과 플러그는 모양이 다름
>- 식품 가공기는 뚜껑을 잘 부착해야만 작동.
>이 원칙은 EUHM(easy to use and hard to misuse)이라고도 함.

## 7.1 불변 객체로 만드는 것을 고려하나

객체 생성 후 상태 못바꾸면 불변.

가변 객체 문제
- 가변 객체는 추론하기 어려움
- 가변 객체는 다중 스레드에서 문제가 발생할 수 있음

기본적으로 불변 객체를 만들되 필요한 곳에서만 가변적이 되도록 하는것 바람직함

### 7.1.1 가변 클래스는 오용하기 쉽다

예시) 세터 함수 제공하는 가변 객체. 해당 객체를 사용하는 모든 코드는 오용 위험성 있음

### 7.1.2. 해결책: 객체를 생성할 때만 값을 할당하라

const, final 등 통해 생성 시에 값 할당

어떤 함수가 변수를 재정의 할 방법이 플요하다면 쓰기 시 복사(copy on write) 패턴 사용 가능

모든 옵션값이 필요한 것 아니라면 빌터 패턴이나 쓰기 시 복사 패턴 사용 좋음. 선택적 매개변수와 명명된 인수를 사용하는 것도 좋은 접근

>C++의 const 멤버 변수  
무브 시맨틱스와 관현하여 문제를 일으킬 수 있기 때문에 그다지 바람직하지 않을 수 있음

### 7.1.3 해결책: 불변성에 대한 디자인 패턴을 사용하라

세터 제거 및 final 변수 사용으로 불변적이고 버그 방지 가능

일부값 선택적 or 가변적 버전 필요 -> 클래스를 다용도로 구현  
이를 위한 두 가지 유용한 디자인 패턴
1. 빌더 패턴
2. copy on write 패턴

#### 빌터 패턴

일부 값 선택 사항 경우, 생성자로 모든 값 설정 까다로울 수 있음

빌더 패턴은 한 클래스를 두 개로 나누는 효과
1. 값을 하나씩 설정할 수 있는 빌더 클래스
2. 빌더에 의해 작성된 불변적 읽기 전용 클래스

필수값은 빌더의 생성자의 매개변수로 받음. 세터로 선택 값 받음(연속 호출 위해 this 반환)

생성 후 인스턴스 복사본을 약간 수정하는 경우 또한 빌더 패턴 사용 가능(미리 값 채워진 빌더 만드는 함수 제공을 통해)  
하지만 약간 번거로움

다음 패턴은 이 작업 쉽게 수행 가능

>빌더 패턴 구현  
빌더 패번 구현시 좋은 코드 위해 특정 기법, 언어 기능 사용 경우 많음. 몇가지 예  
>- 더 나은 네임스페이스를 위한 내부 클래스 사용
>- toBuilder() 함수를 통해 클래스와 빌더 사이 순환 의존성 생성
>- 클래스 private 생성자를 통해 빌더 사용 필수 만듬
>- 빌더의 인스턴스를 생성자의 인수로 사용하여 반복되는 코드 감소
>자바는 오토밸류로 클래스 및 빌더 정의를 자동으로 생성 도구 있음

#### 쓰기 시 복사 패턴

각각의 변수를 변경한 새 객체 반환하는 함수 사용

원본 객체 영향 안미치고 변경된 복사본 쉽게 얻음

---

클래스를 분변으로 만드는 것은 오용 가능성 최소화하는 좋은 방법

세터 제거 및 생성자에서만 값 제공하면 간단 구현


## 7.2 객체를 깊은 수준까지 불변적으로 만드는 것을 고려하라

클래스가 무심코 가변적으로 될 미묘한 경우 간과 쉬움. 일반적으로 깊은 가변성 때문  
이 문제는 변수 자체가 가변적 유형, 다른 코드가 변수 접근 가능 경우 발생할 수 있음

### 7.2.1 깊은 가변성은 오용을 초래할 수 있다

예시) List 변수를 완전히 제어 할 수 없기 때문에 가변적 될 수 있음

List는 목록이 아니라 목록에 대한 참조를 가지고 있다는 점 기억해야함.  
다른 코드가 해당 목록에 대한 참조를 가지고 있다면 영향 미침. 이는 깊은 가변성의 원인 될 수 있음

예시)

1. 시나리오 A : 리스트 변수 생성 후 클래스 생성자에 입력. 리스트 변수 변경 가능
2. 시나리오 B : 리스트 게터를 통해 리스트 참조를 받음. 리스트 내용 수정 가능

위 경우 찾기 힘든 버그 발생. 애초에 불변적으로 만들고 이런 문제를 피하는 것이 훨씬 바람직.

### 7.2.2 해결책: 방어적으로 복사하라

게터 함수가 객체의 복사본을 반환시키게 함. 최선의 해결책은 아니지만, 깊은 불변성 담보에 효과적인 간단 방법

단점
1. 복사하는데 비용 많이 들 수 있음 -> 성능 문제 가능
2. 클래스 내부에서 발생하는 변경을 막기 힘듬. 단순 복사만으로 깊은 불변성 완전 보장 못함

>값에 의한 전달  
C++ 등 참조, 포인터, 값에 의한 전달 사이 차이가 있음.  
값에 의한 전달은 복사본 생성. 단점으로 복사해야함.  
또한 객체 불변을 위해 상수 정확성이라는 개념 다음 절에 봄

### 7.2.3 해결책: 불변적 자료구조를 사용하라

자료구조에 대해 불변적인 버전 제공. 생성 되고 나면 아무도 내용 변경 불가능.   
방어적으로 복사본 만들 필요 없이 객체 전달 가능

Java: 구아바(Guava) 라이브러리 ImmutableList 클래스
자바스크립트 기반: 1. Immutable.js 모듈의 리스트 2. Immer 모듈 사용한 배열

불변적 자료구조 사용 -> 깊은 불변성 보장

방어적 복사 단점 피하고 실수로 변수가 변경되지 않도록 보장

>C++에서 상수 정확성  
C++은 컴파일러 수준에서 불변성을 상당히 발전된 방식 지원.  
클래스 정의 시 멤버 함수에 const 표시 가능. const 객체는 불변 멤버 함수를 호출하는 데만 사용되도록 컴파일러 보장

## 7.3 지나치게 일반적인 데이터 유형을 피하라

엄청 일반적이고, 다재다능하며, 다른 모든 것들을 대표할 수 있음  
반대로 생각하면 무언가 설명할 수 없고, 가질수 있는 값이 관대하다는 의미

설명이 부족하고 허용 범위가 넓을수록 코드 오용 쉬워짐

### 7.3.1 지나치게 일반적인 유형은 오용될 수 있다

예시) 2D 지도의 위치로 위도, 경도 두 값 필요.  
하나의 위치 -> List<Double>, 여러 개 위치 -> List<List<Double>>

다소 복잡해지고, 입력 매개변수 사용 설명 문서가 필요

이것이 코드 오용 쉽게 만드는 단점
1. 유형 자체로 아무것도 설명해주지 않음. 주석문을 읽어야 함
2. 위도와 경도 혼동 쉬움. 순서 바뀌면 버그 발생
3. 타입 안정성이 거의 없음. 경도, 위도 값이 잘못되어도 컴파일되지만 런타임에 문제 발생

한줄 요약, 세부 조항 지식 없이 올바른 사용 불가능, 세부 조항 의지 신뢰X -> 버그 발생

#### 패러다임은 퍼지기 쉽다

예시) 누가 위도, 경도를 저장하는 다른 클래스를 작성. 진실의 원천이 둘이 되는 일례, 이로 인해 버그 발생

위치를 표현하기 위해 List<Double>을 사용하는 것은 임시변통의 방법. 이런 코드는 다른 코드 전반에 퍼지는 경향이 있음. 다른 개발자들도 같은 방식 사용 및 확산. 바로잡기 매우 어려워짐


### 7.3.2 페어 유형은 오용하기 쉽다

예시) Pair<Double, Double> 로 위치 표현.  
입력 매개변수 설명 문서 필요. List<Pair<D, D>> 도 의미 파악 힘듬  
여전히 위도, 경도 순서 혼동

### 7.3.3 해결책: 전용 유형 사용

무언가 나타내기 위해 새로운 클래스 정의는 불필요해 보이지만, 보기보다 노력이 덜 들어가고 이해하기 쉽고 버그 가능성도 줄여줌

예시) 위도, 경도 나타내는 클래스 정의

>데이터 객체  
데이터를 묶기만 하는 간단한 객체 정의는 상당히 일반적인 작업이며, 많은 언어에서 이것을 쉽게 해주는 기능 존재  
>- 코틀린의 데이터 클래스, 한 줄 코드로 클래스 정의
>- 자바 최신 버전의 레코드(이전 버전은 오토밸류 대안)
>- 다양한 언어의 구조체
>- 타입스크립트는 인터페이스 정의 다음 사용 객체가 반드시 포함하는 속성에 대래 컴파일 안정성 제공

## 7.4 시간 처리

실제 시간 나타내기 까다로움
- 어떤 때는 절대적인 시간(2024년3월3일) 지칭, 다른 때는 상대적 시간(5분 이내) 표현
- 시간의 양(30분 굽기)을 언급. 다양한 단위로 표시 가능
- 표준 시간대, 윤년, 윤초 개념이 있어 상황이 복잡

### 7.4.1 정수로 시간을 나타내는 것은 문제가 될 수 있다

일반적으로 정수, 큰 정수 사용. 시각과 시간의 양 둘다 표현
1. 순간 시간 : 유닉스 시간 이후 몇초
2. 양 시간 : 초 or 밀리초 단위 일반적

#### 한순간의 시간인가, 아니면 시간의 양인가?

예시) deadline 매개변수. 시각인지 시간인지 모름

#### 일치하지 않는 단위

일반적으로 밀리초, 초 를 사용함. 정수 타입은 어떤 단위인지 나타내는데 전혀 도움 안됨

#### 시간대 처리 오류

순간으로서의 시간은 일반적으로 유닉스 시간 이후 지나간 초로 나타냄. 보통 타임스탬프로 불림

날짜와 순간의 차이는 미묘하지만 문제가 될 수있음.  
다른 표준시의 사용자는 시간이 다르게 보임


### 7.4.2 해결책: 적절한 자료구조를 사용하라

시간 관련 라이브러리,  오픈소스 라이브러리
- 자바 : java.time
- C# : Noda 시간 라이브러리
- C++ :  chrono 라이브러리

다음 하위 절에서 위 라이브러리 사용하여 코드 개선 방법 설명

#### 양으로서의 시간과 순간으로서의 시간의 구분

예시) 많은 언어 Duration 클래스 제공. 순간인지 시간의 양인지 알 수 있음

#### 더 이상 단위에 대한 혼동이 없다

단위가 타입 내에 캡슐화되어 있음. 실수로 잘못된 단위 제공 불가능

예시) `Duration.ofSecond(5)`, `.ofMinutes` 등 팩토리 함수 사용

#### 시간대 처리 개선

예시) LocalDateTime 클래스 제공. 정확한 순간 날짜 나타낼 수 있음

---

시간을 다루는 것은 까다롭고 오용 쉽고 버그 가능성 큼. 라이브러리를 잘 사용하여 코드 개선

## 7.5 데이터에 대해 진실의 원천을 하나만 가져야 한다

데이터는 종종 두 가지 형태 제공
1. 기본 데이터(primary data): 코드에 제공할 데이터.  
데이터 안알려주면 코드가 처리할 방법이 없음
2. 파생 데이터(derived data): 주어진 기본 데이터에 기반해 코드가 계산할 수 있는 데이터

예시) 은행계좌 상태 설명 데이터. 기본 데이터(대변 금액, 차변 금액), 파생 데이터(계좌 잔액)

기본 데이터는 일반적으로 프로그램에서 진실의 원천이 됨. 상태를 완전히 설명

### 7.5.1 또 다른 진실의 원천은 유효하지 않은 상태를 초래할 수 있다

예시) 계좌 잔액은 두 기본 데이터에 완전히 제한됨. 서로 일치하지 않는 두 진실 원천을 가지면(잔액을 기본 데이터로 사용하면) 5원 입금에 2원 출금해도 잔액 10원 될 수 있음.

### 7.5.2 해결책: 기본 데이터를 유일한 진실의 원천으로 사용하라

계좌 예시는 매우 간단하기 때문에 잔액이 대변과 차변으로 계산하여 필드 중복 발견 쉬움.  
복잡한 상황에는 발견 힘듬.

정의할 데이터 모델과 잘못된 상태를 허용하는지 시간 들여 숙고해볼 가치 있음

#### 데이터 계산에 비용이 많이 드는 경우

때로 파생 데이터 계산에 많은 비용 들 수 있음

그런 경우 그 값을 **지연**(lazily) 계산 후 결과를 캐싱하는 것이 좋음.  
그 값이 정말 필요할 때 까지 계산 미루는 것 의미
예시) 캐시가 널이라면 계산

## 7.6 논리에 대한 진실의 원천을 하나만 가져야 한다

진실의 원천은 논리에도 적용됨. 코드 한 부분 수행되는 일이 다른 부분 수행과 일치해야 하는 경우 많음

### 7.6.1 논리에 대한 진실의 원천이 여러개 있으면 버그를 유발할 수 있다

예시) 정수값 기록 후 파일 저장 클래스. 저장되는 방식 대한 두 가지 세부 정보  
1. 각 값은 10진수 문자열 변환
2. 그 다음 각 값 문자열을 쉼표 구분하여 결합

반대 과정 수행하는 코드도 위 논리가 일치해야함

>오류 처리  
파일 입출력 시, 오류 처리 관해 고려할 사항 있음. 파일 입출력 실패하거나 정수 변환 실패시 오류 전달 등

위 예시에서 한 클래스만 수정된다면 문제 발생
예시로 16진수로 저장하거나 쉼표 대신 줄 바꿈 사용 등

### 7.6.2 해결책: 진실의 원천은 단 하나만 있어야 한다

어떤 형식을 사용하는지에 대한 하위 문제 해결해야 함

직렬화와 역직렬화를 재사용 가능한 하나의 코드 계층으로 구현하면 단 하나의 진실 원천 가짐

예시) IntListFormat 클래스 정의. 직렬화와 역직렬화 논리 하나. 추가로 십진법 구분자 등을 상수로 한번 저장하기 때문에 이 정보에 대한 진실 원천 하나

파일 쓰기와 파일 읽기의 상위 수준 문제와 직렬화 방식에 대한 하위 문제를 나누어 계층 분리.

## 요약

- 코드가 오용 쉽게 작성되면 버그 가능성 커짐
- 일반적인 코드 오용 사례
  1. 호출 쪽에서 잘못된 입력 제공
  2. 다른 코드에서 일어나는 부수 효과
  3. 함수 호출 시점 잘못되거나 올바른 순서 호출 안됨
  4. 기존 코드 연관 코드 수정 시, 기존 코드 가정과 어긋나게 수정
- 오용 어렵거나 불가능하도록 코드 설계 및 구조화 종종 가능.
  - 이를 통해 버그 가능성 줄고 중장기적 시간 절약

