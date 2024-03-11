# Chapter9 코드를 재사용하고 일반화할 수 있도록 하라

다룰 내용
- 안전하게 재사용할 수 있는 코드 작성 방법
- 다양한 문제 해결위한 일반화된 코드 작성 방법

향후 코드 재사용이 가능하도록 의도적으로 코드 작성 및 구조화 하는 것 바람직. 장기적으로 이득

간결한 추상화 계층(2장) 및 코드 모듈화(8장)과 매우 깊은 관련

## 9.1 가정을 주의하라

코드 작성 시 가정
장점
1. 코드 더 단순해짐
2. 더 효율적.
단점
1. 코드가 더 취약해짐
2. 활용도 낮아짐 -> 재사용 힘듬

### 9.1.1 가정은 코드 재사용 시 버그를 초래할 수 있다

예시) 인터넷 기사 클래스. 모든 이미지 반환 메서드. 이 코드가 이미지 포함 섹션이 하나라고 가정함

이미지 포함 반견 시 반복문 종료하므로 조금 빨라지지만 사소함.  
하지만 이미지 섹션 2개 이상이라면 예상대로 동작 안함. 버그 가능

기사 클래스가 다른 용도 재사용 or 기사 이미지 배치 변경 시 가정이 틀려버림.

### 9.1.2 해결책: 불필요한 가정을 피하라

반복문 미리 중단하지 않고 다 돌아보며 가정을 하지않음

>섣부른 최적화  
이를 피하려는 것은 소프트웨어 공학 및 컴퓨터 과학 내에 잘 정립된 개념.  
코드 최적화 시 많은 시간 노력 필요 -> 종종 가독성, 유지관리, 견고함 떨어짐. 하지만 엄청 실행 되야 이점 생김.  
최적화 보다 클린 코드 집중이 좋다. 최적화 이득 크면 그 때 해도 무방

### 9.1.3 해결책: 가정이 필요하면 강제적으로 하라 

가정을 강제적으로 시행하는 두 방법
1. 가정이 '깨지지 않게' 만들라: 가정 깨지면 컴파일 오류 발생 가능하면 가정 항상 유지 가능
2. 오류 전달 기술을 사용하라: 오류 감지 및 빠르게 실패하도록 코드 작성

#### 문제의 소지가 있는, 강제되지 않은 가정

코드가 예상대로 안굴러가도 티안나게 진행함. 큰 문제 초래

#### 가정의 강제적 확인

오류 전달 기법을 사용하여 가정을 강제로 인지, 신속 실패 코드로 변경

>오류 전달 기법  
어서션은 호출 쪽에서 복구 원치 않는 경우 적합.

---

가정 손해볼 상황 많음. 필요하다면 오류 빠지지 않게 가정을 강제로 시행하라.

## 9.2 전역 상태를 주의하라

전역변수는 프로그램 내 모든 콘텍스트에 영향 미침. 코드를 매우 취약, 재사용, 안전 하지 않음

>전역성과 가시성을 혼동하지 말라  
전역변수가 클래스의 인스턴스나 함수의 자체 버전 대신 프로그램의 모든 컨텍스트 간에 공유됨

### 9.2.1 전역 상태를 갖는 코드는 재사용 하기에 안전하지 않을 수 있다

예시) 온라인 쇼핑 앱. 항목 탐색 -> 바구니 추가 -> 체크아웃  
장바구니가 많은 부분 접근할 수 있어야 하는 상태. 전역 처리 한다면  
1. 인스턴스와 연결되지 않음
2. 어디서나 장바구니 호출 및 액세스 가능

편해보이는데 왜 안될까?

#### 누군가 이 코드를 재사용하려고 하면 어떻게 되는가?

암묵적 가정이 들어감 -> 앱 실행 중 인스턴스당 하나의 장바구니만 필요.
가정 깨지는 예시
-> 장바구니 서버에 저장한다면 다수 사용자 처리 불가능  
-> 항목 저장 기능 추가 된다면 그 장바구니까지 처리해야함

요약
최상의 경우 거의 중복된 코드로 유지 관리 비용 늘어남  
최악의 경우 악성 버그 발생

### 9.2.2 해결책: 공유 상태에 의존성 주입하라

서로 다른 클래스 간 상태를 공유하는 좋은 방법

---

프로그램 서로 다른 부분 간 상태를 공유해야할 경우, 의존성 주입을 사용해 보다 통제된 방식으로 수행하는 것이 더 안전

## 9.3 기본 반환값을 적절하게 사용하라

어떤 클래스가 10개 매개변수 사용 생성, 이 값 모두 제공하지 않아도 되면 호출 작업 쉬워짐.

기본값 제공하려면 종종 두 가지 가정 필요
- 어떤 기본값이 합리적인지
- 더 상위 계층의 코드는 기본값을 받든 명시적으로 설정된 값을 받든 상관하지 않음

가정의 비용과 이점 고려해야함. 상위 수준 코드는 특정 사례에 더 밀접하게 결합하므로 기본값 선택 쉬움.  
반면 낮은 수준 코드는 근본적 하위 문제 해결 위해 광범위 재사용 경향. 기본값 선택 어려움

### 9.3.1 낮은 층위의 코드의 기본 반환값은 재사용성을 해칠 수 있다

예시) 워드에서 글 스타일링 기본값 설정. 사용자 재정의 가능. 환경설정 에서 글꼴 저장. 글꼴 지정 안하면 기본 글꼴 반환.

적응성에 해가 될 수 있음. 기본값 정의 코드의 계층이 낮을수록 가정이 적용되는 상위 계층 더 많아짐

사용자 환경 설정 검색과 프로그램 기본값 정의는 별개의 하위 문제 -> 별개의 하위 문제 분리 및 상위 계층 코드 적절한 기본값 처리하도록


### 9.3.2 해결책: 상위 수준의 코드에서 기본값을 제공하라

가장 간단한 방법은 기본 글꼴 대신 널 반환 -> 기본값 제공은 사용자 처리와 별개 하위 문제됨 -> 호출 쪽에서 원하는 방식으로 하위 문제 해결 가능 의미 -> 코드 재사용성 향상 -> 상위 수준 코드에서 하위 문제 해결 위한 전용 클래스 정의 가능

기본값, 사용자 제공값 구현 세부 정보 은닉, 동시에 의존성 주입 통해 재설정 가능 <- 재사용성, 적응성 보장

조건문 널 처리가 투박하면 *널 병합 연산자*(?? 연산자) 사용하면 가독성 향상.

>기본 반환값 매개변수  
널 병합 연산자 지원안하면 널 처리 코드 반복 작성해야함.  
한 가지 방법은 기본 반환값 매개변수 사용하는 것.
>- 예시로 자바의 Map.getOrDefault() 함수. 키값 있으면 반환, 없으면 기본값 반환
> `String value = map.getOrDefault(key, "default value");`

---

기본값은 코드 및 소프트웨어를 쉽게 사용할 수 있으므로 활용 가치 충분

낮은 층위 코드에서 기본값 반환은 문제 가능성 큼. 단순 널 반환 후 더 높은 층위에서 기본값 구현이 나을 수 있음 <- 상위 수준에서 상정한 가정은 유요할 가능성 큼

## 9.4 함수의 매개변수를 주목하라

데이터 객체 or 클래스 내에 포함된 모든 정보가 있어야 하는 경우 그 객체 인스턴스를 매개변수로 받는 것이 타당.  
그러나 함수가 한두 가지 정보만 필요할 때 객체나 클래스의 인스턴스를 매개변수 사용하는 것은 코드의 재사용성을 해칠 수 있음

### 9.4.1 필요 이상으로 매개변수를 받는 함수는 재사용하기 어려울 수 있다

예시) 텍스트 스타일링 옵션 캡슐화 했던 예제. setColor 함수는 데이터 객체의 색상 정보만 필요로 함. 이 함수 재사용 시 어려움 봉착. 색상 설정 때마다 데이터 객체 생성해야만 함.

### 9.4.2 해결책: 함수는 필요한 것만 매개변수로 받도록하라

일반적으로 함수가 필요한 것만 받으면 코드 재사용성, 가독성 향상.  
관련하여 어떻게 할지 자신이 판단해야 함. 정답은 없고, 취하는 방법의 장단점 및 초래 결과 고려하는 것 좋음

## 9.5 제네릭의 사용을 고려하라

제네릭 통해 참조하는 모든 타입을 구체적 명시 없이 클래스 작성 가능. 작은 추가 작업으로 일반화 크게 향상

### 9.5.1 특정 유형에 의존하면 일반화를 제한한다

예시) 단어 맞히기 게임 개발. 모음 저장 클래스를 문자열로만 구현.  
특정 유형 문제는 해결, 다른 유형의 동일 하위 문제는 해결 못하는 일반화되지 못한 코드. 사진 맞히기 게임에 재사용 불가

### 9.5.2 해결책: 제네릭을 사용하라

제네릭 사용시 일반화 증가

---

높은 수준 문제 -> 하위 문제로 세분화 -> 다양한 사례 적용 가능한 매우 근본적 문제 접함.  

하위 문제 해결책이 모든 데이터 유형 적용된다면 제네릭 사용 바람직 -> 코드 일반화 및 재사용 가능

## 요약

- 동일한 하위 문제 자주 발생 -> 코드 재사용시 시간, 노력 절약
- 특정 하위 문제에 대해 코드 재사용 가능하게 근본적 하위 문제 식별 및 코드 구성 노력해야함
- 간결한 추상화 계층, 모듈식 콛, -> 재사용, 일반화 쉽고 안전
- 가정하면 코드 종종 더 취약 및 재사용 어려움
  - 가정할 경우 이점 vs 비용
  - 가정이 코드의 적절한 계층에 대해 이루어지는지 확인. 가능하면 가정을 강제적으로 적용
- 전역 상태 사용 <- 비용 많이 발생하는 가정하는 것. 재사용 및 안전 저하. 대부분 전역 상태 피하는 것이 가장 바람직