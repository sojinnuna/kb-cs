스레드 안전성(Thread Safety)이란 여러 스레드가 동시에 실행되는 환경에서 프로그램이 의도한 대로 동작하며, 데이터의 일관성이 유지되는 것을 의미합니다. 즉, 여러 스레드가 동시에 접근할 수 있는 공유 자원이 있을 때, 그 자원이 동시에 여러 스레드에 의해 변경되더라도 데이터가 손상되거나 예기치 않은 결과가 발생하지 않는 상태를 말합니다.

- synchronized 키워드
    
    메서드나 블록을 동기화하여 한 번에 하나의 스레드만 접근할 수 있도록 합니다.
    
    ```java
    public static class Item {
      private Integer item=0;
      
      public synchronized void increment() {
        item++;
      }
    
      public synchronized void decrement() {
        item--;
      }
    
      public Integer get() {
        return item;
      }
    }
    ```
    
- Lock 객체
    
    `java.util.concurrent.locks` 패키지의 Lock 객체를 사용하여 좀 더 유연한 락 제어를 할 수 있습니다.
    (Java 5 부터 추가)
    
    ```java
    public class Counter{
    
      private Lock lock = new Lock();
      private int count = 0;
    
      public int inc(){
        lock.lock();
        int newCount = ++count;
        lock.unlock();
        return newCount;
      }
    }
    ```
    

- Atomic 클래스
    
    `java.util.concurrent.atomic` 패키지의 Atomic 클래스(AtomicInteger, AtomicBoolean)를 사용하여 원자적 연산을 수행합니다.
    
    ```java
    public static class Item {
      private AtomicInteger item = new AtomicInteger(0);
      public void increment() {
        item.incrementAndGet();
      }
      public void decrement() {
          item.decrementAndGet();
      }
    
      public Integer get() {
          return item.get();
      }
    }
    ```
    
- Concurrent Collections
    - `java.util.concurrent` 패키지 하위의 클래스를 사용합니다.
    - ConcurrentHashMap, CopyOnWriteArrayList 등이 있습니다.
    
    ```java
    private ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
    ```
    
    - ConcuurentHashMap
        - ConcuurentHashMap에는 Hashtable 과는 다르게 synchronized 키워드가 메소드 전체에 붙어 있지 않습니다.
        - get() 메소드에는 아예 synchronized가 존재하지 않고, put() 메소드에는 중간에 synchronized 키워드가 존재하는 것을 볼 수 있습니다.
        - 즉, ConcurrentHashMap은 읽기 작업에는 여러 쓰레드가 동시에 읽을 수 있지만, 쓰기 작업에는 특정 세그먼트 or 버킷에 대한 Lock을 사용한다는 것입니다.

### Race Condition

**Race Condition**은 두 개 이상의 스레드가 동시에 자원에 접근할 때 발생할 수 있는 문제입니다. 여러 스레드가 동일한 자원을 변경하려고 할 때, 실행 순서에 따라 결과가 달라질 수 있습니다. 이는 예기치 않은 결과를 초래할 수 있으며, 데이터의 일관성을 해칠 수 있습니다.

예를 들어, 두 스레드가 동일한 변수의 값을 증가시키려고 할 때, 각 스레드가 값을 읽고 변경한 후 저장하는 과정에서 간섭이 발생하면 올바른 값이 저장되지 않을 수 있습니다.

### **Lock 외에 Thread Safe를 구현하기 위한 방법?**

1. **Atomic Variables (원자적 변수)**
    - `java.util.concurrent.atomic` 패키지에서 제공하는 원자적 클래스를 사용하여 락 없이 원자적 연산을 보장합니다.
2. **Immutable Objects (불변 객체)**
    - `객체를 불변`으로 설계하여 상태 변이를 방지하고, 자연스럽게 스레드 안전성을 확보합니다.
3. **Functional Programming (함수형 프로그래밍)**
    - 상태 변이를 피하고 순수 `함수`를 사용하여 스레드 안전성을 유지합니다.
    - 병렬 스트림과 람다
4. **Thread-Local Storage**
    - `ThreadLocal` 클래스를 사용하여 각 스레드마다 독립된 변수 공간을 제공함으로써 공유 자원을 피합니다.
5. **Concurrent Collections**
    - `java.util.concurrent` 패키지의 동시성 컬렉션 사용
