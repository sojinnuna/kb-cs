# Equals()와 Hashcode()

> equals()와 Hashcode는 모든 Java 객체의 부모 객체인 Object 클래스에 정의되어 있다. 따라서 Java의 모든 객체는 두 함수를 상속받고 있다.

<br/>

## == 연산자

- `primitive type`에 대해선 값 비교를, `reference Type`에 대해서는 객체의 주소 값을 비교함
    - `원시 타입(Primitive type)`
        - 기본적인 데이터 타입으로, 자바에서 미리 정의되어 있는 데이터 타입
        - 빈 객체 타입으로 NULL값을 가질 수 없으며 Stack메모리 저장
        - int, char, boolean, byte, short, long, double, float
    - `참조 타입(Reference Type)`
        - 객체를 가리키는 참조(메모리 주소)를 저장하는 데이터 타입
        - 빈 객체 타입 NULL을 가질 수 있으며 Heap 메모리에 생성
        - String, Integer, ArrayList, 사용자 정의 클래스 등

<br/>
<br/>


## equals()

```java
// Object의 기본 equals 메서드
public boolean equals(Object obj) {
    return (this == obj);
}
```

- 개념
    - 어떤 `두 참조 변수 값`의 동등여부를 비교할 때 사용하는 매소드
        - 동일성(Identity) 비교
    - 어떤 두 객체가 가리키는 곳이 `동일한 메모리 주소`일 경우에만 동일한 객체로 판단

<br/>

- 재정의 시, 주의 사항 (General Contract)
  * **반사성** (Reflexive): x.equals(x)는 항상 true이어야 함
  * **대칭성** (Symmetric): x.equals(y)가 true이면 y.equals(x)도 true이어야 
  * **추이성** (Transitive): x.equals(y)가 true이고 y.equals(z)가 true이면 x.equals(z)도 true이어야 함
  * **일관성** (Consistent): x.equals(y)의 결과는 객체가 변하지 않는 한 여러 번 호출해도 동일해야 함
  * **null 비교** (Null Comparison): x.equals(null)는 항상 false이어야 함.

```java
public class Person {
    private String name;
    private int age;

    // Constructor, getters, and setters

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;   // 주소 일치
        if (obj == null || getClass() != obj.getClass()) return false;
        Person person = (Person) obj;
        return age == person.age && Objects.equals(name, person.name); // 주소 일치 x, 내부 값 비교
    }
}
```
<br/>

### equals 메소드 오버라이딩
- 프로그래밍 시 동일한 객체가 메모리 상에 여러 개 띄워져 있는 경우가 존재함
    - 해당 객체가 서로 다른 메모리에 띄워져있을 경우 동일한(Identity) 객체가 아님
- 하지만 프로그래밍 상으로 같은 값을 지님으로 같은 객체로 인식되도록 처리하고 싶은 상황이 존재할 수 있음
    - 이런 동등성(Identity)을 위해, **객체의 값을 비교하도록** equals 매소드 오버라이딩을 진행함

<br/>

### Q.  equals() 를 재정의 해야 할 때, 어떤 점을 염두에 두어야 하는지

> equals() 메서드를 재정의할 때는 **반사성, 대칭성, 추이성, 일관성, null 비교** 등의 일반 계약을 준수해야 함
> 
> equals()가 true인 두 객체는 동일한 hashCode() 값을 가져야 함
> 
> **성능 고려** : 너무 많은 필드를 비교하거나 복잡한 비교를 수행하면 성능이 저하될 수 있음. 필요한 필드만 비교하되, 동등성을 정확히 판단할 수 있도록 해야 함.
> 
> 불변 객체 (Immutable Object)와의 비교는 한 번 계산된 해시 코드를 캐싱하여 성능을 최적화할 수 있음.

<br/>
<br/>
<br/>
<br/>

## Hash code

> **native** 키워드               
> : 메소드가 JNI라는 native code를 이용해 구현되어 있음을 의미하며, OS에 C언어로 작성되어있어 그 안의 내용은 볼 수 없고 사용만 가능하다.
> `hashCode()`는 native 코드 중 하나이다.

<br/>

 ```java
public native int hashCode();
```

- 개념
    - 객체의 주소 값을 이용해서 해싱(hashing) 기법을 통해 생성한 해시 코드
    - 객체를 식별하는 하나의 정수 값
    - 객체의 메모리 번지를 이용해 해시코드를 만들어 리턴하므로 `객체마다 다른 값`을 가짐
    
<br/>

- 재정의 시, 주의 사항 (General Contract)
    - **일관성** : 동일한 객체에 대해 여러 번 호출해도, 객체의 상태가 변하지 않는 한 항상 동일한 해시 코드를 반환해야 함
    - **equals()와의 관계** 
      - x.equals(y)가 true이면 x.hashCode() == y.hashCode()도 true이어야 함
      - 하지만 x.hashCode() == y.hashCode()가 true라고 해서 x.equals(y)가 반드시 true일 필요는 없음

```java
public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (obj == null || getClass() != obj.getClass()) return false;
        Person person = (Person) obj;
        return age == person.age && Objects.equals(name, person.name);
    }

    @Override
    public int hashCode() { // hashcode 재정의
        return Objects.hash(name, age);
    }
}
```
<br/>

### equals와 hashCode

- equals() 메서드는 두 객체가 의미적으로 같은지 비교하며, hashCode() 메서드는 객체의 해시 코드를 생성함
- equals()를 재정의할 때는 반드시 hashCode()도 재정의해야 함
  * equals()가 논리적으로 같은 객체로 간주하면, hashCode()도 동일한 값을 반환하여 해시 기반 컬렉션에서 올바르게 동작하도록 보장해야 함
- equals()와 hashCode()의 구현은 반사성, 대칭성, 추이성, 일관성, null 비교 등 일반 계약을 준수해야 함
- 해시 기반 컬렉션에서 올바르게 동작하려면 두 메서드가 일관성 있게 구현되어야 함

<br/>

**Hash값을 재정의 해야하는 이유**
- hash값을 사용하는 Collection Framework(HashSet, HashMap, HashTable)에서 객체 비교를 진행
- 기본 hashCode() 메서드: Object 클래스의 기본 hashCode() 메서드는 객체의 메모리 주소를 기반으로 해시 코드를 반환
- 다른 해시 코드: 동일한 논리적 객체라도 새로운 인스턴스를 생성하면 메모리 주소가 다르므로 서로 다른 해시 코드를 반환


```java
/*             잘못된 케이스               */

Person p1 = new Person("Alice", 30);
Person p2 = new Person("Alice", 30);

// 같은 논리적 객체지만, hashCode()를 재정의하지 않으면 서로 다른 해시 코드를 가짐
System.out.println(p1.hashCode()); // 예: 12345678
System.out.println(p2.hashCode()); // 예: 87654321

// HashSet에 저장
HashSet<Person> set = new HashSet<>();
set.add(p1);
set.add(p2);

// 논리적으로 같은 객체이지만 해시 코드가 다르므로 다른 객체로 인식됨
System.out.println(set.size()); // 2 출력
```

```java
/*             객체 내부에서 재정의한 올바른 케이스               */
// 위의 Parson 객체 참고

Person p1 = new Person("Alice", 30);
Person p2 = new Person("Alice", 30);

// 재정의된 hashCode() 메서드를 통해 동일한 해시 코드를 가짐
System.out.println(p1.hashCode()); // 예: 12345678
System.out.println(p2.hashCode()); // 예: 12345678

// HashSet에 저장
HashSet<Person> set = new HashSet<>();
set.add(p1);
set.add(p2);

// 논리적으로 같은 객체로 인식되어 하나의 객체로 저장됨
System.out.println(set.size()); // 1 출력
```


<br/>
<br/>

### identity HashCode 메소드

```java
System.identityHashCode(p1);
```

- 개념
    - hashCode()를 오버라이딩 해서 쓰되, 오버라이딩 하기 전의 원조 기능이 필요할 때 사용 하는 메서드
- 배경
    - HashCode의 본래 목적은 `객체의 주소값을 기반으로 해싱한 고유 숫자 값`으로 객체가 동등한지 여부를 판단하는 것
    - 하지만 equals()와 hashCode() 메서드를 오버라이딩할 경우, 본래 목적인 객체 자체의 해시코드를 얻을 수 없음
    - 따라서 자바에서는 똑같이 해시 코드를 반환해주는 identityHashCode() 코드를 만들게 되었음

<br/>

### Q. 본인이 hashcode() 를 정의해야 한다면, 어떤 점을 염두에 두고 구현할 것 같은지?

> hashCode() 메서드는 equals() 메서드와 일관성 있게 정의되어야 함
> 
> 중요한 필드들을 사용하여 해시 코드를 계산해야 함
> 
> 해시 코드 충돌을 최소화하고, 코드 계산이 너무 복잡하거나 시간이 오래 걸리면 성능이 저하될 수 있으므로 성능을 고려해야 함 
> 
> 불변 객체의 경우 해시 코드를 캐싱하여 성능을 향상시킬 수 있음
> 
> null 처리에 유의하여 일관된 해시 코드를 생성해야 함


<br/>
<br/>
<br/>