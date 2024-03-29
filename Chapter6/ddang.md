# Ch6. 예측 가능한 코드를 작성하라

## 6.1 매직값을 반환하지 말아야 한다

- 매직값은 정상적인 반환 유형
- 이 값의 특별한 의미를 인지하지 못하고 정상 반환값으로 오해하기 쉬움
- 사용하지 않는 것이 바람직

### 널, 옵셔널 또는 오류를 반환하라

- 매직값을 반환하는 경우 문제점은 호출하는 쪽에서 함수 계약의 세부사항을 알아야 한다는 점
- 값이 없을 수 있는 경우 코드 계약의 명백한 부분에서 확인할 수 있도록 하는 것이 좋음
- 옵셔널 값 반환
  - 호출하는 쪽에서 값이 없을 수 있음을 인지하고 적절한 방법으로 처리할 수 있게 해야 함
    - 호출하는 쪽에 부담을 주지만, 중장기적으로 발생할 수 있는 버그를 고치는 노력보다 훨씬 가벼움

## 6.2 널 객체 패턴을 적절히 사용하라

- 의도는 널값을 반환하는 대신 유효한 값을 반환하여 그 이후에 실행되는 로직에 널값으로 인한 피해를 회피하기 위함
- 값이 없을 때 빈 컬렉션을 반환하면 함수를 간결히 표현할 수 있음
- 그러나 잘못 사용하는 경우, 문제가 될 수 있음
  - 문자열의 경우, 별다른 의미가 없다면 널 대신 빈 문자열을 반환하는 것은 문제되지 않음
  - 그러나 코드에서 특별한 의미를 가지는 문자열의 경우, 문자열이 없을 수 있음을 호출하는 쪽에서 명시적으로 인식하도록 하는 것이 중요함
    - 해당 필드는 특정한 의미를 가지며, 널을 갖는다는 것은 중요한 무언가를 의미하기 때문
  - 널 객체 패턴은 가게 점원이 물건이 품절됐음을 알려주지 않고 아무 말 없이 빈 박스를 판 것과 같음
  - 예상을 벗어난 동작 유발

## 6.3 예상치 못한 부수 효과를 피하라

- 부수 효과는 어떤 함수의 호출이 외부에 초래한 상태 변화를 의미함
- 부수 효과는 불가피하며 예상된 부수 효과는 괜찮지만, 예상되지 않을 경우 버그로 이어질 수 있음
  - 부수 효과는 비용이 많이 들 수 있음
    - 자신도 모르게 부수 효과를 일으키는 코드를 반복적으로 호출하는 경우, 성능 문제 발생 가능
  - 부수 효과는 호출한 쪽의 가정을 깨뜨림
    - 함수의 목적이 값을 가져오거나 읽기 위한 경우, 다른 개발자는 일반적으로 함수 호출이 부수 효과를 일으키지 않는다고 가정함
    - 이 가정은 잘못됐기 때문에 버그를 일으킬 가능성 존재
  - 다중 스레드 코드의 버그
    - 서로 다른 스레드가 종종 동일한 데이터에 엑세스할 수 있기 때문에 한 스레드로 인한 부수 효과는 다른 스레드에 문제를 일으킬 수 있음
      - 어떤 스레드가 접근중인 데이터를 다른 스레드에서 수정하거나 삭제하는 경우

### 부수 효과를 피하거나 그 사실을 분명하게 하라

- 예상치 못한 부수 효과를 피하는 방법 중 하나는 부수 효과가 일어나지 않도록 하는 것
  - 클래스를 불가변으로 만드는 것
- 부수 효과가 원하는 기능의 일부이거나, 피할 수 없는 경우에는 호출하는 쪽에서 확실하게 인지하도록 해야 함
  - 부수 효과가 일어나는 함수의 이름을 명확히 할 것

## 6.4 입력 매개변수를 수정하는 것에 주의하라

- 어떤 객체를 다른 함수에 입력으로 넘기는 것은 친구에게 책을 빌려주는 것과 유사함
  - 함수가 입력 매개변수를 수정하는 것은 페이지를 찢고 여백 위에 낙서하는 등의 일을 코드에서 하는 것
  - 해당 객체를 빌려준 쪽은 함수에게 객체를 빌려준 후에도 다른 용도로 사용해야 하는데, 함수가 입력 매개변수를 수정해버릴 수 있음

### 변경하기 전에 복사하라

- 입력 매개변수 내의 값을 어쩔 수 없이 변경해야 하는 경우에는 변경 전에 새 자료구조에 복사해야 함
  - 이렇게 하면 원래의 객체가 변경되지 않음
  - 단 값을 복사하는 경우, 메모리나 CPU와 관련해 성능에 영향을 미칠 수 있음 (trade-off)

## 6.5 오해를 일으키는 함수는 작성하지 말라

- 코드 계약의 명백한 부분에 오해의 소지가 있을 때 더 상황이 안좋음

### 중요한 입력은 필수 항목으로 만들라

- 필수 매개변수에 대해 값을 사용할 수 없는 경우, 함수를 호출할 수 없도록 널을 허용하지 않는 것이 안전함
  - 널을 확인하는 코드를 호출하는 쪽에 추가
  - 코드 라인 수가 증가할 수 있지만, 코드가 잘못 해석되거나 예상과 다른 동작을 할 가능성은 줄어듦

## 6.6 미래를 대비한 열거형 처리

- 미래에 추가될 수 있는 열거값을 암묵적으로 처리하는 것은 문제가 될 수 있음
- 모든 경우를 처리하는 스위치문을 사용
  - 스위치 문을 사용하는 경우, default 케이스 주의. 암묵적으로 처리될 수 있음
