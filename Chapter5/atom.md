# 5. 가독성 높은 코드를 작성하라

<!-- TOC -->

* [5. 가독성 높은 코드를 작성하라](#5-가독성-높은-코드를-작성하라)
    * [5.1 서술형 명칭 사용](#51-서술형-명칭-사용)
    * [5.2 주석문의 적절한 사용](#52-주석문의-적절한-사용)
    * [5.3 코드 줄 수를 고정하지 말라](#53-코드-줄-수를-고정하지-말라)
    * [5.4 일관된 코딩 스타일을 고수하라](#54-일관된-코딩-스타일을-고수하라)
    * [5.5 깊이 중첩된 코드를 피하라](#55-깊이-중첩된-코드를-피하라)
    * [5.6 함수 호출도 가독성이 있어야 한다](#56-함수-호출도-가독성이-있어야-한다)
    * [5.7 설명되지 않은 값을 사용하지 말라](#57-설명되지-않은-값을-사용하지-말라)
    * [5.8 익명 함수를 적절하게 사용하라](#58-익명-함수를-적절하게-사용하라)
    * [5.9 프로그래밍 언어의 새로운 기능을 적절하게 사용하라](#59-프로그래밍-언어의-새로운-기능을-적절하게-사용하라)

<!-- TOC -->

**가독성의 핵심은 개발자가 코드의 기능을 빠르고 정확하게 이해할 수 있도록 하는 것이다.**
**코드의 가독성을 높이려면 다른 개발자의 입장을 공감하고, 그들이 코드를 읽을 때 어떻게 혼란스러워할지를 상상해 보는 것이 필요하다.**

## 5.1 서술형 명칭 사용

- 이름을 붙이는 것은 그것이 스스로 설명되는 방식으로 언급함으로써 읽기 쉬운 코드 작성을 위한 기회이기도 하다.
- 서술적이지 않은 이름은 코드를 읽기 어렵게 만든다.
- 주석문으로 서술적인 이름을 대체할 수 없다. 대신, 서술적인 이름을 짓자.
- 변수, 함수, 클래스가 별도로 설명할 필요가 없이 자명하여야 한다.

## 5.2 주석문의 적절한 사용

- 주석, 문서는 다음과 같은 다양한 목적을 수행할 수 있다.
    - 코드가 무엇을 하는지 설명
    - 코드가 왜 그 일을 하는지 설명
    - 사용 지침, 기타 정보 제공
- 클래스와 같이 큰 단위의 코드가 무엇을 하는지 요약하는 높은 수준에서의 주석문은 유용할 수 있다.
- 하위 수준에서 한 줄 한 줄 코드가 무엇을 하는지 설명하는 주석문은 효과적이지 않다.
- 코드가 왜 그 일을 하는지에 대한 이유, 배경을 설명하는 주석은 유용할 수 있다.
- 서술적인 이름으로 잘 작성된 코드는 그 자체로 줄 다누이에서 무엇을 하는지 설명한다.

**아래는 주석문의 사용 방법과 시기에 대한 일반적인 지침이다.**

- 중복된 주석문은 유해할 수 있다.(코드가 스스로를 설명하는데, 주석까지 추가된다면 중복)
- 주석문으로 가독성 높은 코드를 대체할 수 없다.(주석을 작성할 정도면, 코드의 가독성을 의심해 보자.)
- 코드 자체로 존재 의미를 알기 어려운 경우, 주석문은 코드의 이유를 설명하는 데 유용하다.
- 주석문은 유용한 상위 수준의 요약 정보를 제공할 수 있다.(책의 줄거리처럼)
- 예측대로 실행되고 오용을 방지하는 코드를 작성하려고 할 때 문서나 주석문에 너무 많이 의존하지 않는 것을 권장한다.
- 주석문, 문서도 함께 유지보수되어야 한다. -> 장단점 사이에서 균형 잡힌 접근법이 필요하다.

## 5.3 코드 줄 수를 고정하지 말라

- 코드 줄 수는 우리가 실제로 신경 쓰는 것들을 간접적으로 측정해 줄 뿐이다.
- 대신, 이해하기 쉽고, 오해하기 어렵고, 실수로 작동이 안 되게 만들기 어려운 코드를 작성하는 것을 확실하게 하자.
- 간결하지만 이해하기 어려운 코드는 피하라.
- 더 많은 줄이 필요하더라도 가독성 높은 코드를 작성하라.

## 5.4 일관된 코딩 스타일을 고수하라

**(결론) 일관적이지 않은 코딩 스타일은 혼동을 일으킬 수 있다. 스타일 가이드를 채택하고 따르자.**

## 5.5 깊이 중첩된 코드를 피하라

- 깊이 중첩된 코드는 읽기 어려울 수 있다.
    - 눈으로 따라가기 어렵고, 특정 값이 반환되는 시점을 파악하기 위해 밀집된 모든 if-else 논리를 탐색해야 한다.
    - 중첩이 깊어지면 가독성이 떨어진다.
- 중첩을 최소화하기 위한 구조 변경 :
    - 중첩을 피하기 위해 논리를 재배치하는 것은 일반적으로 쉽다.
    - 단, 중첩된 블록에 반환문이 없다면, 그것은 대개 함수가 너무 많은 일을 하고 있다는 신호일 수 있다.
      ㅋ
      **(결론) 중첩은 너무 많은 일을 한 결과물이다. 더 작은 함수로 분리해서 해결해보자.**

## 5.6 함수 호출도 가독성이 있어야 한다

- 함수가 무슨 일을 하는지 분명하게 이름을 잘 지었더라도 인수가 무엇을 위한 것이고, 무슨 역할을 하는지 명확하지 않다면 함수 호출 자체가 이해되지 않을 수 있다.
- 함수 호출은 인수의 개수가 늘어나면 이해하기 힘들어진다.
- 함수나 생성자가 많은 수의 매개변수를 가지고 있다면, 근본적인 문제가 있는지 의심해 보자
- 매개변수는 이해하기 어려울 수 있다. ex) `sendMessage("hello", 1, true)`
- 명명된 매개변수 사용, 혹은 객체 구조 분해를 사용해 해결 가능하다. ex) `sendMessage(message : "hello", priority: 1, allowRetry: true)`
- 혹은 서술적 유형을 사용할 수 있다. (클래스 혹은 열거형)
- 때로는 훌륭한 해결책이 없을 수 있다. ex) `new BoundingBox(10, 50, 20, 5)`
    - 인라인 주석문을 사용하면 해결은 가능하나 만족스럽지 않다.
    - setter나 builder 패턴도 대안으로 가능하지만, 불완전한 인스턴스가 만들어질 위험성이 존재한다.
- IDE에서는 매개변수 이름을 잘 보여주긴 하지만, 코드 검토, 병합, 탐색 도구에서는 보이지 않을 수 있다.

## 5.7 설명되지 않은 값을 사용하지 말라

- 하드 코드로 작성된 모든 값에는 두 가지 중요한 정보가 존재한다.
    - `값이 무엇인지` : 컴퓨터가 코드를 실행할 때 이 값을 알아야 한다.
    - `값이 무엇을 의미하는지` : 개발자가 코드를 이해하려면 값의 의미를 알아야 한다.
- 설명되지 않은 값은 혼란스러울 수 있다. 대신, `잘 명명된 상수`를 사용해라.
- 혹은, `잘 명명된 함수`를 사용하라
    - 상수를 반환하는 공급자 함수(provider function)
    - 변환을 수행하는 헬퍼 함수(helper function)
- 정의한 값, 헬퍼 함수를 다른 개발자들이 재사용할 것인지 고려해 보고 그럴 가능성이 있다면 별도의 유틸 클래스로 만드는 것이 유용하다.

## 5.8 익명 함수를 적절하게 사용하라

- 익명 함수는 간단한 로직에 좋다. 자명한 것에 명명 함수를 정의하면 보일러 플레이트 코드가 많이 필요하며, 가독성이 떨어져 보일 수 있다.
- 익명 함수는 가독성이 떨어질 수도 있다. 복잡한 논리는 명명 함수가 좋을 수 있다.
- 익명 함수가 길면 문제가 될 수 있다. 긴 익명 함수를 여러 개의 명명 함수로 나누자.
    - 익명 함수가 두세 줄 이상으로 늘어나기 시작하면, 여러 개의 명명 함수로 분리하면 코드 가독성이 좋아진다.
    - **(중요) 그전에 함수가 너무 많은 일을 하고 있는지 의심해 보자.**

**(결론) 간단하고 자명한 것에 익명 함수를 사용하면 코드의 가독성을 높여 주지만 , 복잡하거나 자명하지 않은 것 혹은 재사용해야 하는 것에 사용하면 문제가 될 수 있다.**

## 5.9 프로그래밍 언어의 새로운 기능을 적절하게 사용하라

- 새 기능은 코드를 개선할 수 있다.
    - 언어에서 제공하는 기능을 사용하면 코드가 최적화되어 효율적이고 버그가 없을 가능성이 커진다.
    - 일반적으로 코드의 품질을 개선한다면 언어가 제공하는 기능을 사용하는 것이 좋다.
- 불분명한 기능은 혼동을 일으킬 수 있다.
    - 해당 기능이 다른 개발자에게 얼마나 잘 알려져 있는지 고려할 필요 있다. (혼란을 초래하고, 학습 비용이 존재함)
    - 이를 위해서 일반적으로 현재의 상황과 궁극적으로 누가 코드를 유지보수할지에 대한 고민이 필요하다.

- **(중요, 결론) 작업에 가장 적합한 도구를 사용하자.**