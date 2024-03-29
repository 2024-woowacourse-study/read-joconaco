### 💭 자신의 코드와 다른 개발자의 코드

코드베이스는 계속 변하고 일반적으로 여러 개발자에 의해 변경된다. 코드 품질이 유지되려면 튼튼하고 사용하기 편해야 한다. 중요한 고려 사항 중 하나로 다른 개발자가 변경하거나 코드와 상호작용할 때 발생할 수 있는 문제는 없는지, 또 발생한다면 그 문제를 어떻게 완화할 수 있는지를 이해하고 선체적으로 조치하는 것이다.

- 자신에게 명백하다고 해서 다른사람에게도 명백한 것은 아니다.
- 다른 개발자는 무의식 중에 여러분의 코드를 망가뜨릴 수 있다.
- 시간이 지남에 따라 자신의 코드를 기억하지 못한다.

### 💭 내가 작성한 코드의 사용법을 다른 사람들은 어떻게 아는가?

다른 개발자가 나의 코드를 사용하거나 의존하는 코드를 수정하는 경우에 다음을 고려해서 나의 코드를 파악한다.

- 여러 가지 상황에서 어떤 함수를 호출해야 하는지
- 클래스가 무엇을 나타내는지 그리고 언제 사용되어야 하는지
- 어떤 값을 인수로 사용해야 하는지
- 코드가 수행하는 동작이 무엇인지
- 어떤 값을 반환하는지

구체적인 방법은 다음과 같다.

- 함수, 클래스, 열거형 등의 이름을 살펴본다.
- 함수와 생성자의 매개변수 유형 또는 반환값의 유형 같은 데이터 유형을 살펴본다.
- 함수/클래스 수준의 문서나 주석문을 읽어본다.

### 💭 코드 계약이란?

다른 사람들이 어떻게 코드를 사용할지, 그리고 코드가 무엇을 할 것으로 기대할 수 있는지에 대한 것이다.

**계약의 명확한 부분 → 컴파일 타임에 확인되는 부분**

- 함수와 클래스 이름: 호출하는 쪽에서 이것을 모르면 코드를 사용할 수 없다.
- 인자 유형: 호출하는 쪽에서 인자의 유형을 잘못 사용하면 코드는 컴파일조차 되지 않는다.
- 반환 유형: 호출하는 쪽에서 함수의 반환 유형을 알아야 한다. 이 유형과 일치하지 않는 유형을 사용하면 코드는 컴파일되지 않는다.
- 검사 예외(checked exception): 호출하는 코드가 이것을 처리하지 않으면 코드는 컴파일되지 않는다.

**계약의 세부 사항 → 런타임에 확인될 수 있는 부분**

- 주석문과 문서: 실제로 잘 읽지 않는다.
- 비검사 예외(uncheckedexception): 주석문에 이 예외가 나열되어 있다면 이것은 세부조항이다. 어떤 때는 심지어 세부조항에도 나와 있지 않을 수도 있다.

계약의 세부 사항은 "지양"하는 것이 좋다. 세부 사항을 읽지 않는 개발자가 많고, 문서가 최신화되지 않을 가능성이 있기 때문이다.

### 💭 체크 및 어서션으로 런타임에 코드 계약 확인하기

컴파일러를 사용하여 컴파일 타임에 코드 계약을 확인하는 것이 가장 신뢰할만하다. 하지만 불가능한 경우, 런타임 검사도 방법이 될 수 있다.

**체크**

코드 계약이 준수되었는지 확인하기 위한 추가적인 로직이다. 준수되지 않을 경우 체크는 실패를 유발하여 Fast-Fail을 유도한다. 체크는 계약 조건에 따라 다음 범주로 구분된다.

- 전제 조건 검사

  입력 인수가 올바르거나, 초기화가 수행되었거나, 일부 코드를 실행하기 전에 시스템이 유효한 상태인지 확인하는 경우

- 사후 상태 검사

  반환값이 올바르거나 일부 코드를 실행한 후 시스템이 유효한 상태인지 확인하는 경우


체크를 사용하면 런타임에 발견하기 어려운 버그를 방지할 수 있다. 하지만 근본적으로 오용을 불가능하게 만든 해결책보다 이상적이지 않다.

**어서션**

체크과 유사하지만 빌드할 때 컴파일 과정에서 제외된다. 조건 확인을 위한 로직을 수행하지 않는기에 성능이 향상된다. 하지만 근본적인 해결책은 아니다.

### 💭 요약

- 코드베이스는 계속 변하고 일반적으로 여러 개발자에 의해 변경된다.
- 다른 개발자가 어떻게 코드를 해석하고 오용할 수 있는지 생각해보고, 이러한 가능성을 최소화하거나 오용이 불가능하게 만드는 방식으로 코드를 작성하는 것이 유리하다.
- 코드를 작성할 때 일종의 코드 계약이 항상 만들어진다. 여기에는 명백한 항목이나 세부 조항과 같은 내용이 포함될 수 있다.
- 코드 계약의 세부 조항은 다른 개발자가 계약을 준수하도록 하기 위한 방법이지만 신뢰할만한 방법은 아니다. 보통 더 나은 접근법은 명백한 항목으로 계약의 내용을 전달하는 것이다.
- 일반적으로 컴파일러를 사용하여 계약을 확인하는 것이 가장 신뢰할 수 있는 방법이다. 이것이 가능하지 않을 때, 체크나 어서션을 사용하여 실행 시간에 계약을 확인할 수 있다.
