# 가비지 컬렉션 Garbage Collection(GC)

---

## 가비지 컬렉션이란?

- Heap 영역에서 동적으로 할당했던 메모리 중 `사용하지 않는 메모리 객체(garbage)`를 모아 주기적으로 제거하는 프로세스

- 자바는 JVM에 탑재된 GC가 메모리 관리를 대행 -> 개발자 입장에서 메모리 관리, 메모리 누수(Memory Leak) 문제에 대해 완벽히 관리하지 않아도 됨
- 특정 개체가 garbage 인지 판단하기 위해서 `도달능력(Reachability)` 이라는 개념을 사용
- 객체에 레퍼런스가 있으면 **Reachable**로 구분되고, 객체에 유효한 레퍼런스가 없으면 **Unreachable**로 구분하고 GC의 대상이 됨

### 가비지 컬렉션의 장점

- 생산성 향상: C / C++ 언어에서 프로그래머가 수동으로 메모리를 할당, 해제를 했던 것에 반해 JVM의 GC가 자동으로 메모리를 관리해주기 때문에 개발자는 개발에 집중 할 수 있음
- 메모리 누수 방지: GC가 메모리 관리를 대행하기 때문에 메모리 누수를 방지하여 안정성을 높임

### 가비지 컬렉션의 단점

- 오버 헤드: GC가 실행될 때 성능 저하가 발생할 수 있음.(stop-the-world) 
- 예측 불가능: GC가 예측 불가능한 시점에 실행 될 수 있기 때문에 이 또한 성능문제로 귀결될 수 있음 

### stop-the-world

- GC을 실행하기 위해 JVM이 애플리케이션 실행을 멈추는 것
- stop-the-world가 발생하면 GC를 실행하는 스레드를 제외한 나머지 스레드는 모두 작업을 멈춘다.
- 어떤 GC 알고리즘을 사용하더라도 stop-the-world 는 발생하고, GC 튜닝이란 이 stop-the-world 시간을 줄이는 것

### weak generational hypothesis

JVM의 GC는 두 가지 가설(전제 조건)하에 만들어졌다.

- 대부분의 객체는 금방 접근 불가능 상태(unreachable)가 된다.
- 오래된 객체에서 젊은 객체로의 참조는 아주 적게 존재한다.

<br>

## Heap 영역의 구성

<img alt="Young Generation과 Old Generation" height="230" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/408f8cf3-1fce-4a14-883b-204a83eb76a0" width="700"/>

- `Young Generation 영역`

    - Eden 영역, 2개의 Survival 영역
    - 새롭게 생성한 객체의 대부분이 여기 위치
    - 대부분의 객체가 금방 접근 불가능 상태가 되기 때문에 많은 객체가 Young 영역에 생성되었다가 사라짐
    - 이 영역에서 객체가 사라질 때 **minor GC**가 발생

- `Old Generation 영역`

    - 접근 불가능 상태가 되지 않아 Young 영역에서 살아남은 객체가 여기 복사
    - 대부분 Young 영역보다 크게 할당하며, 크기가 큰 만큼 Young 영역보다 GC는 적게 발생
    - 이 영역에서 객체가 사라질 때 **Major GC** 혹은 **Full GC**가 발생

# Garbage Collection 과정

## Minor GC - Young Generation

- 새로 생성한 대부분의 객체는 Eden 영역에 위치
- Eden 영역에서 GC가 한 번 발생한 후 살아남은 객체는 Survivor 영역 중 하나로 이동
- 두 개의 Survivor 영역 중 살아남은 객체가 존재하는 Survivor 영역으로 객체가 계속 쌓임
- 하나의 Survivor 영역이 가득 차게 되면 그 중에서 살아남은 객체를 다른 Survivor 영역으로 이동
- 그리고 이전에 가득찬 Survivor 영역은 아무 데이터도 없는 상태가 됨
- 이 과정을 반복하다가 계속해서 살아남아 있는 객체는 Old 영역으로 이동

## Major GC - Old Generation

- Survivor 영역 객체의 age(객체가 살아남은 횟수) 값이 임계값에 다다르면 Old 영역 이동
  * 이 과정 `프로모션(Promotion)` 라고 함
- Major GC는 객체들이 계속 프로모션 되어 Old 영역의 메모리가 부족해지면 발생한다.
- stop-the-world 가 발생하고, Mark and Sweep 작업 수행
  * 사용 중인 객체 식별(mark)하고, 식별되지 않은 객체를 메모리에서 제거(sweep)

<img alt="img1 daumcdn" height="230" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/63e087b4-86b2-4542-a69c-a35ab4012584" width="700"/>

<br>

# Q. finalize() 메서드를 수동으로 호출하는 것의 문제점

- `객체의 상태가 불완전`할 수 있음
  *	finalize()는 객체가 더 이상 사용되지 않을 때 GC에 의해 호출되는 메서드로, 객체 자원 해제 등을 수행
  * 문제
    * 수동으로 호출하면 객체가 여전히 유효한 상태에서 finalize()가 실행되어, 객체가 불완전한 상태가 될 수 있음
    * 이는 예기치 않은 동작을 초래할 수 있음
    
- GC의 `관리 흐름을 방해` 및 `자원 관리의 혼란`
  * finalize()는 GC가 객체를 수집하기 전에 호출하는 메서드
  * 문제
    * 수동으로 finalize()를 호출하면, GC가 객체를 적절히 수집하고 관리하는 흐름을 방해할 수 있음
    * 이는 메모리 누수와 같은 문제를 야기할 수 있음

- `중복 호출 위험`
  * finalize()는 한 번만 호출되도록 설계됨
  •	문제
    * 수동으로 호출하면, GC가 객체를 수집할 때 다시 호출될 위험이 있음
    * 이는 자원 해제 등의 작업이 중복으로 실행되어 불필요한 동작이나 에러를 초래

- `성능 저하`
  * 수동으로 호출하면, 불필요한 동작이 추가되어 성능 저하를 초래할 수 있음


### 해결 방법
- try-with-resources 구문
  * AutoCloseable 인터페이스를 구현하고, try-with-resources 구문을 사용하여 자원을 명시적으로 관리

- 명시적인 자원 해제 메서드
  * close()와 같은 명시적인 자원 해제 메서드를 제공하여, 자원을 명시적으로 해제.

```java
try (MyResource resource = new MyResource()) {
    // 자원 사용
} catch (Exception e) {
    // 예외 처리
} 
// try-with-resources 블록을 벗어나면 자동으로 close() 호출
```


> 객체 상태 불완전, GC 흐름 방해, 중복 호출, 성능 저하, 자원 관리 혼란 등의 문제를 일으킬 수 있음. 대신, try-with-resources 구문이나 명시적인 자원 해제 메서드를 사용하는 것이 바람직함.

<br>

## Q. 어떤 변수의 값이 null이 되었다면, 이 값은 GC의 대상이 될 가능성이 있을까?

>변수의 값이 null이 되면, 해당 변수에 참조되었던 객체는 더 이상 사용되지 않으므로 GC의 대상이 될 가능성이 있음. 
> 그러나 GC는 JVM에 의해 주기적으로 실행되므로, 객체가 즉시 수집되는 것은 아님. 강한 참조는 null이 되면 수집 대상이 되지만, 
> `약한 참조, 유약 참조, 유령 참조` 등 특별한 참조 유형에 따라 GC 동작이 달라질 수 있음.


* 강한 참조(Strong Reference): 일반적인 객체 참조. null로 설정되면 수집 대상이 됨
```java
public class GCExample {
    public static void main(String[] args) {
        MyObject obj = new MyObject();
        // obj가 MyObject 인스턴스를 참조함
        obj = null;
        // 이제 MyObject 인스턴스는 참조되지 않음 -> GC의 대상이 될 수 있음
    }
}
```

* 약한 참조 (Weak Reference) : GC가 수집할 수 있음
```java
WeakReference<Object> weakRef = new WeakReference<>(new Object());
```
* 유약 참조: 메모리가 부족할 때 GC가 수집할 수 있음
```java
SoftReference<Object> softRef = new SoftReference<>(new Object());
```
* 유령 참조: 객체가 메모리에서 완전히 제거된 후에도 GC가 수집하기 전에 알림을 받을 수 있도록 함
```java
PhantomReference<Object> phantomRef = new PhantomReference<>(new Object(), referenceQueue);
```
<br>

# Garbage Collection의 종류(알고리즘)

### **Serial GC**
<img height="200" src="https://velog.velcdn.com/images/erinleeme/post/97004aa9-6950-488f-b79f-a8487abbe5c7/image.png"/>

- 서버의 CPU 코어가 1개일 때 사용하기 위해 개발된 가장 단순한 GC
- GC를 처리하는 **스레드가 1개**이기에 **stop-the-world 시간이 가장 김.**
- `mark-sweep-compact` 알고리즘 사용

    <img height="250" src="https://velog.velcdn.com/images/erinleeme/post/96b32eaf-37bd-47ba-982e-e1e2733e1bf1/image.png"/>
  
    - 이 알고리즘의 첫 단계는 Old 영역에 살아 있는 객체를 식별(Mark)하는 것
    - 그 다음 heap의 앞 부분부터 확인하여 살아 있는 것만 남김 (Sweep)
    - 마지막 단계에서 살아남은 객체를 힙의 시작 부분으로 이동 -> 이동된 객체의 새로운 위치에 따라 참조를 업데이트 -> 메모리 공간 확보 (Compaction)

<br>

### **Parallel GC**

- Java 8의 디폴트 GC, Java 9에서부터 `deprecated`
- Serial GC와 기본적인 알고리즘은 같지만, Young 영역의 Minor GC과 Old 영역의 Marjor GC가 **병렬처리** 함
- 여러 스레드를 사용하기 때문에 **CPU 사용량이 높음**
- Serial GC에 비해 **stop-the-world 시간 감소**
  * 지연 시간이 일관되지 않을 수 있음. 
  * 실시간 시스템이나 지연 시간이 중요한 애플리케이션에서는 부적합할 수 있음.

Serial GC와 Parallel GC의 스레드 비교한 그림


<img alt="serial GC and Parallel GC" height="250" src="https://velog.velcdn.com/images/erinleeme/post/4970d3bc-b666-4031-8994-1b56768b20be/image.png"/>

<br>
<br>

### **Parallel Old GC**

- Parallel GC를 개선한 버전
- `Mark-Summary-Compaction` 사용
  * Summary 목적 : 객체 이동을 준비하고 메모리 재배치 계획 수립
    * 마크된 객체들을 기반으로 살아남은 객체들의 위치를 계산
    * 각 객체가 이동할 새로운 위치를 결정
    * 메모리 재배치 테이블 생성
    * 이 단계는 Compaction 단계에서 객체를 이동시키기 위한 정보를 제공

<br>
<br>

### **CMS GC**

아래의 그림은 Serial GC와 CMS GC의 절차를 비교한 그림


<img alt="helloworld-1329-5" height="300" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/10f94c30-0af2-4723-8a81-a16468e119fe"/>

-  주로 애플리케이션의 응답 시간을 줄이기 위해 설계됨
- `Initial Mark 단계`에서 GC 루트에서 **가장 가까운 접근 가능한** 모든 객체를 마크
    - 따라서 멈추는 시간이 매우 짧다.
- `Concurrent Mark 단계`에서는  **초기 마크 단계(Initial Mark)에서 마크된 객체들이 참조하는 모든 객체**를 마크
    - _애플리케이션 스레드와 동시에 실행_
    - 힙 전체를 스캔하여 접근 가능한 모든 객체를 마크
- `Remark 단계`에서는 Concurrent Mark 단계에서 **새로 추가되거나 참조가 끊긴 객체**를 확인
- `Concurrent Sweep 단계`에서는 힙을 스캔하여 **마크되지 않은 객체를 메모리에서 제거**
    - _애플리케이션 스레드와 동시에 실행_

**CMS GC의 장단점**

- 장점

    - 이런 단계로 진행되는 GC 방식이기에 stop-the-world 시간이 매우 짧다.
    - 모든 애플리케이션의 응답 속도가 매우 중요할 때 CMS GC를 사용한다.

- 단점

    - 다른 GC 방식보다 메모리와 CPU를 더 많이 사용한다.
    - Compaction 단계가 없어서 메모리 단편화 문제가 발생할 수 있음
      * 메모리 단편화는 메모리 공간이 작은 조각들로 나뉘어져서 효율적으로 사용할 수 없게 되는 현상을 말함


<br>
<br>

### **G1 GC**

- CMS GC를 대체하기 위해 jdk 7 버전에서 최초로 release된 GC
- Java 9+ 버전의 디폴트 GC로 지정
- 구조는 Young 영역과 Old 영역과는 사뭇 다른 Region 이라는 개념을 사용한다.
- 전체 Heap 영역을 Region이라는 영역으로 체스같이 분할하여 상황에 따라 Eden, Survivor, Old 등 역할을 고정이 아닌 동적으로 부여
- Garbage로 가득찬 영역을 빠르게 회수하여 빈 공간을 확보하므로, 결국 GC 빈도가 줄어드는 효과를 얻게 되는 원리


<img alt="img1 daumcdn" height="300" src="https://github.com/reddevilmidzy/CS-Study/assets/78539407/fee6b60c-2140-4f06-a2df-d53c5e62c223" width="300"/>


---


#### ref

[Java Garbage Collection](https://d2.naver.com/helloworld/1329)  
[가비지 컬렉션 동작 원리 & GC 종류 💯 총정리](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%EC%85%98GC-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC)  
[[10분 테코톡] 🐥엘리의 GC](https://youtu.be/Fe3TVCEJhzo)