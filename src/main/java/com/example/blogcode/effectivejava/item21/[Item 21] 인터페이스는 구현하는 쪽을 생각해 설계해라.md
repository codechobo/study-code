# [Item 21] 인터페이스는 구현하는 쪽을 생각해 설계해라

자바 8 전에는 기존 구현체를 깨뜨리지 않고는 인터페이스에 메서드를 추가할 방법이 없었다.

인터페이스에 메서드를 추가하면 기존 구현체에 이미 메서드가 존재하는 가능성이 적기에 자바 8 이후에 기존에 인터페이스에 메서드를 추가할 수 있도록 디폴트 메서드를 제공했다.

디폴트 메서드는 주로 람다를 활용하기 위해 선언하는데, 디폴트 메서드를 선언하면, 그 인터페이스를 구현한 후 디폴트 메서드를 정의 하지 않은 모든 클래스에서 사용이 가능하다.

### 불변식을 해치지 않는 디폴트 메서드를 작성하기란 어려운 법이다.

Collection 인터페이스의 removeIf() 의 디폴트 메서드를 보면 모든 Collection 구현체와 어우러지는 것은 아니다.

Collection 인터페이스를 구현한 `SynchronizedCollection` 클래스는 정적 팩터리 메서드가 반환하는 클래스와 비슷하다.

아파치 버전은 클라이언트가 제공한 객체로 락을 거능 능력을 추가로 제공한다. 즉, 모든 메서드에서 주어진 락 객체로 동기화한 후 내부 컬렉션 객체에 기능을 위임하는 래퍼 클래스다.

자바 8 이전에는 `SynchronizedCollection` 클래스는 모든 메서드를 동기화를 하는 것을 규칙으로 만들었는데 자바 8 이후에는 디폴트 메서드가 구현되며 규칙이 어겨진다.

removeIf() 메서드의 구현은 동기화에 관해 아무것도 모르므로 락 객체를 사용할 수 없다.

따라서 여러서 스레드에서 `SynchronizedCollection` 인스턴스의 removeIf() 메서드를 사용하면 `스레드 안전` 하지 않다.

이러한 문제를 예방하기 위해 구현한 인터페이스의 디폴트 메서드를 재정의하고, 다른 메서드에서는 디폴트 메서드를 호출하기 전에 동기화를 하도록 한다.

### 디폴트 메서드는 (컴파일에 성공하더라도) 기존 구현체에 런타임 오류를 일으킬 수 있다.

- 기존 인터페이스에 디폴트 메서드로 추가하는 일은 꼭 필요한 경우 아니면 피해야 한다.
- 추가하는 디폴트 메서드가 기존 구현체들과 충돌하는지도 고려해야 한다.
- 디폴트 메서드를 제거하거나 기존의 디폴트 메서드 시그니처를 수정하는 용도가 아님을 명심해야 한다.

`디폴트 메서드라는 도구가 생겼더라도 인터페이스를 설계할 때는 여전히 세심한 주의를 기울여야 한다.`

### 주의

- 새로운 인터페이스라면 릴리스 전에 반드시 테스트를 거쳐야한다.
- 인터페이스를 릴리스한 후라도 결합을 수정하는 게 가능한 경우도 있겠지만, 절대 그 가능성에 기대서는 안된다.