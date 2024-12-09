# Thread-safety
2024-12-09
`#Java`
---

## Thread-safety 란 
하나 이상의 스레드(`Thread`)가 동일한 함수나 변수 혹은 객체에 대해서 동시에 접근하더라도, 프로그램 실행에 문제가 없거나 프로그래머가 의도한 결과를 도출할 수 있도록 보장하는 성질 
> 멀티 스레드 환경에서 데이터의 무결성과 일관성을 보장하는 개념
> - 무결성(`Data Integrity`) : 동일한 데이터에 여러 스레드가 동시에 접근하더라도 변경되거나 삭제 또는 손상되지 않도록 보장하는 성질
> - 일관성(`State Consistency`) : 데이터가 여러 스레드에 의해서 변경되더라도 일관된 상태를 유지할 수 있는 성질  

### 프로그래밍에서 본 성질이 중요한 이유
하나 이상의 스레드가 동시에 데이터를 읽거나 수정될 경우 무결성과 일관성일 보장되지 않으면, 예상하지 못한 비정상적인 동작이 발생하는 문제가 발생할 수 있다.

## 예시
`SimpleCounter`가 가지고 있는 필드 `value`값은 여러 스레드가 동시에 접근할 경우, 각 스레드에 의해서 변경되어 의도한 결과를 도출 할 수 없음.
따라서 이 클래스는 전혀 `Thread-safety` 하지 않다고 말할 수 있음
```java
public class SimpleCounter{
    private int value = 0; // 스레드 간 경합 발생
    
    public void increment() {
        value++;
    }
    
    public int getValue() {
        return value;
    }
}
```
더 자세한 이해를 위해서 테스트케이스를 작성해보도록 하자, 클래스 `SimpleCounter`을 통해서 하나의 객체 `counter`를 생성한 합니다. 
그리고, `1,000`번의 복문을 통해서 단순히 `value` 값을 1씩 증가시키는 `increment()` 메소드를 호출하도록 하는 `task`를 작성해 두 스레드 `t1`과 `t2` 를 생성합니다.
본 케이스를 실행하게 될 경우 기대되는 결과는 두 개의 스레드를 통해서 반복해 증가된 값 `value`의 값은 `2,000` 이나, 실제로 실행할 게 될 경우 만나게되는 결과는 `1967`, `1994` 등 예상치 못한 값이 반환 됩니다.
```java
import static org.junit.jupiter.api.Assertions.*;

class SimpleCounterTest{

    @Test
    void simpleCounterWithMultiThreadTest() {
        // Given
        SimpleCounter counter = new SimpleCounter();

        Runnable task = () -> {
            for (int i = 0; i < 1000; i++) {
                counter.increment();
            }
        };

        // When
        Thread t1 = new Thread(task);
        Thread t2 = new Thread(task);

        t1.start();
        t2.start();

        t1.join();
        t2.join();

        // Then
        assertNotEquals(2000, counter.getCounter()); 
    }
}

```
### 왜 이런 결과가 도출될까?

**결론적으로는 해당 코드에서는 `Race Condtion(경쟁 상태)` 가 발생해, 두 스레드가 `value`필드에 접근하고 변경하려는 행위를 할 때 세르데 스케쥴링에 의해서 스레드가 언제든 변경될 수 있기 때문**이다.
본 과정 중 스레드가 동시에 접근하게 될 경우 데이터에 대한 무결성과 일관성이 깨지게 된다.

예를 들어서 선언된 스레드 `t1`와 `t2`가 동시에 `value`필드에 접근했다고 가정하자
- 실행 중 `t1` 가 `value`값을 읽어서 `4`를 가져가고, `t2`도 동시에 접근해 `4` 가져감
- `Thread_A` 에서 `4`에서 `5`로 계산되어 `value`에 저장
- 하지만, `Thread_B`는 `4`를 가지고 있기에 반영되지 않고 동일하게 다시 `5`를 `value`에 저장

결론적으로 `getValue()` 할 경우 반환되는 값은 `5` 로, 스레드를 두 개를 실행하더라도 값은 1만 증가하는 결과가 발생

## 그렇다면 어떻게 성질을 보장할 수 있을까?
### 상호 배제(Mutual Exclusion)
한 번에 하나의 스레드만 공유 자원에 접근할 수 있도록 보장함, 위에서 발생한 `Race Condition(경쟁 상태)`를 방지할 수 있는 가장 기본적인 방안
- `synchronized` 키워드를 사용해 스레드를 동기화 시킴

```java
public synchronized void increment() {
    value++;  // 한 번에 하나의 스레드만 접근 가능
}
```

### 원자성(Atomicity)
실행될 작업에 대해서 정상적으로 완전히 실행되거나, 실패하여 실행이 되지 않거나 하도록 하는 방식을 채택하는 방식. 만약 방식에 대해서 원자성이 보장되지 않는다면 중간에 다른 스레드에 의해서 값이 변경되거나 수정될 수 있음
- `AtomicInteger`, `AtomicLong` 와 같은 `Atomic-Variables`를 사용해 작업에 대한 원자성을 확보

```java
AtomicInteger value = new AtomicInteger(0);

public void increment() {
    value.incrementAndGet();  // 원자적으로 증가
}
```

### 가시성(Visibility)
`Java Memmory Model(JMM)`에 따라서 값이 불일치 되는 문제가 발생할 수 있어, 변경된 값을 다른 스레드에서 즉시 볼 수 있도록 보장하도록 하는 방식.  
- `volatile ` 키워드를 사용해 변수의 값을 `Main Memory`에 즉시 쓰고 읽도록 변경
- 
```java
private volatile boolean flag = false;

public void setFlagTrue() {
    flag = true;  // 다른 스레드가 즉시 변경된 값을 읽을 수 있음
}
```

## Reference
- [스레드 세이프와 동시성 프로그래밍 이해하기](https://f-lab.kr/insight/understanding-thread-safety-and-concurrency-programming)
- [자바 메모리 모델 이해하기](https://f-lab.kr/insight/understanding-java-memory-model)
- [Java에서 Thread-Safe하게 구현하기](https://velog.io/@mangoo/java-thread-safety)