# static class와 static method

<br> 

## static

### 개념
- 어떤 객체에 소속된 것이 아닌 **클래스에 고정**되어있는 변수나 메서드
- 자바에서 static 키워드
    - JVM이 시작될 때 static 영역에 한번 저장되어 프로그램이 종료될 때 해제되는 것을 의미

<br> 

### 특징
**✔️ 메모리에 고정적으로 할당 됨**
- 각 객체들에서 공통적으로 하나의 값이 유지되어야할 경우 유용하게 사용

<br>

**✔️ 객체 생성 없이도 메서드나 변수 사용 가능**
- 자동으로 메모리의 static영역에 올라가기 때문에 객체 생성할 필요없이 사용 가능

<br>

**✔️ static 영역 적재**

<img src ="https://t1.daumcdn.net/cfile/tistory/99AAAC405CEC82C032?original" width="400" heigth="500"/>

- 프로그램의 시작과 끝까지 메모리에 존재
    - 프로그램 시작 시, 메모리의 static영역에 적재
    - 프로그램 종료 시, 메모리의 static영역에서 해제
- 무분별한 static 남발 조심
    - 과도하게 많은 static을 선언할 경우 메모리에 과부하
    - 시작 시, static 영역의 메모리 크기를 넘어서는 양의 static 메서드들이 존재시 에러 발생 가능
- staic영역에 할당된 메모리는 모든 객체가 공유하는 메모리

> 일반적인 메서드는 객체 생성 시 메모리의 Heap영역에 올라감.        
> Heap영역은 GC에 의해 자동으로 메모리가 관리되나, static영역은 해당 관리를 받지 못함

<br>

**✔️ static매서드 내에서는 인스턴스 변수 사용 불가**
- `인스턴스 변수`
    - 런타임 시점에 메모리에 올라감
    - 객체를 생성해야 사용 가능

- `Static 메서드`
    - 컴파일 시점에 메모리에 올라감

<br>
<br>

### 메모리 효율

- 긍정적인 메모리 효율 측면
    - 생성할 때마다 인스턴스가 힙에 올라가는 것이 아니라 고정 메모리이므로 효율적
    - 객체 생성하지 않고 사용하기 때문에 속도가 빠름

<br>

- 부정적인 메모리 효율 측면
    - GC의 대상이 아님
        - static 할당 값이 클 경우 GC의 효율이 떨어짐
        - 과도한 static의 경우 메모리 누수 발생 가능
    - static영역의 특징
        - 프로그램이 끝날 때까지 메모리에서 내릴 수 없음
        - static영역의 차지 영역이 클 경우 메모리 부족 발생 가능

<br>
<br>

### 문제점
- 전체 프로그램과 동일한 라이프 사이클
    - static멤버는 사용유무와 상관없이 프로그램 시작과 끝까지 메모리 내 존재
- static 변수의 생성과 소멸 지시 불가
    - 개발자가 프로그램적으로 생성과 소멸에 관여 불가
    - `생성` : 프로그램 로딩 시
    - `소멸` : 프로그램 종료 혹은 JVM이 내려갈 때
- Thread safe 하지 않음 (`동시성 문제`)
    - 모든 스레드에서 static필드를 공유하며 프로그램 전역 사용
    - 한 스레드에서 값 변경 시, 다른 모든 스레드에서 영향을 받음
    - Thread safe를 위한 추가작업 필요
- static 메서드는 오버라이딩 불가
    - 인스턴스 메소드
        - 런타임 시 해당 메소드를 구현하고 있는 실제 객체를 찾아 호출 (`다형성 적용`)
    - static 메소드
        - JVM과 컴파일러 모두 실제 객체를 찾는 작업을 시행하지 않음
        - 컴파일 시점에 선언된 타입의 메서드 호출 (`다형성 적용X`)
- static 멤버는 Serialization 불가
    - 객체 직렬화는 인스턴스에 대해 적용 됨
    - 클래스 자체 정보인 static 멤버는 포함되지 않음
      * Serialization 란?
        * 자바 직렬화란 자바 시스템 내부에서 사용되는 객체 또는 데이터를 외부의 자바 시스템에서도 사용할 수 있도록 
          바이트(byte) 형태로 데이터 변환하는 기술과 바이트로 변환된 데이터를 다시 객체로 변환하는 기술(역직렬화)을 아울러 이야기함.
        * 시스템적으로 이야기하자면 JVM의 메모리에 상주(힙 또는 스택)되어 있는 객체 데이터를 
          바이트 형태로 변환하는 기술과 직렬화된 바이트 형태의 데이터를 객체로 변환해서 JVM으로 상주시키는 형태를 같이 이야기합니다.
        
        --> 즉, static은 객체(object)마다 하나씩 존재하는 것이 아닌 Class마다 하나씩 존재하므로, 인스턴스와 객체(object)를 다루는 Serialization의 영역이 아님
- 메모리 문제
    - 프로그램이 종료될 때까지 Garbage Collector로 회수되지 않음
    - 메모리 누수 문제 발생 가능
- 테스트의 어려움
    - 프로그램 전체에서 접근할 수 있는 필드를 추론하기 어려움

<br>
<br>

## 객체의 life cycle

객체의 Life-Cycle(생명주기=메모리 관점)
0. JVM 초기화 및 실행
1. main 호출
2. Class 접근하는 순간(생성 포함) static{} 블록 실행 -> 최초 한번만 실행
3. Class 생성시 {} 초기화 블록 실행
4. 생성자 호출
5. 객체 사용 (사용자 프로그래밍한 순으로 활용됨) // 여기까지가 상식선으로 알면 좋다!
6. 종료시 소멸자 - finalize() 호출 -> 실행이 불확정적임으로 언제 실행될지 모름!!
* 반드시 정리해야 하는 자원이 있다면 별도의 사용자 메소드 'clear()' 메소드를 생성시키고 수동으로 호출
* 또는 반드시 try finally 문법을 활용하자

<br>
<br>

```java
// 출처 : 전공C반 이해성 강사님 수업자료
public class StaticTest {
	public static void main(String[] args) {
		System.out.println("실행!");
		User.requiredUserNumber = 8;
		new User();
//
		int count = 0;
		while(true) {
			new User("홍길동" + count++);
		}
	}
}

class User {
	String name;
	public static final int MAX_SIZE = 10; // 최대 생성할수 있는 사람의 수 
	public static int requiredUserNumber;  // 요구되는 사용자의 수
	private static int currentUserNumber;  // 현재 생선된 사용자의 수 

	// class 사용할때 초기화 한다.
	static { // 객체 사용시 최우선 호출되는 영역
		System.out.println("Class를 초기화 하였습니다.");
		requiredUserNumber = 5;
		currentUserNumber = 0;
//		name = "홍길동"; // Cannot make a static reference to the non-static field name
	}

	// 초기화 블럭 생성자보다 먼저 호출된다.
	{ // 객체 생성시 최우선 호출되는 영역
		currentUserNumber++;
		System.out.println("유저가 생성되었습니다. Num : " + currentUserNumber);

		if (currentUserNumber > requiredUserNumber) {
			System.out.println("허용된 사용자보다 많은 유저가 생성되었습니다.");
		}
		if (currentUserNumber > MAX_SIZE) {
			System.err.println("생성된 유저 숫자가 너무 많습니다. 메모리를 정리합니다.");
			System.gc(); // Runs the garbage collector in the Java Virtual Machine.
			// 메모리 정리를 요청하는 메소드
			
			try { // 이게 없으면 소멸하지 않는다.
				Thread.sleep(1000); // 1초간 기다리는 코드
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			// System.exit(1);
		}
	}

	public User() { // 초기화문 이후에 호출 
		this("무명");
		System.out.println("User 이름 : " + name);
	}

	public User(String name) { // 초기화문 이후에 호출
		this.name = name;
		System.out.println("User 이름 : " + name);
	}
	
	@Override
	protected void finalize() throws Throwable { // 객체가 소멸될때 호출되는 메소드
		System.out.println("유저 정보를 정리합니다. " + currentUserNumber);
		currentUserNumber--;
	}
}
```

---

#### ref

[정적 메소드, 너 써도 될까?](https://tecoble.techcourse.co.kr/post/2020-07-16-static-method/)  
[07-03 스태틱](https://wikidocs.net/228)  
[☕ 내부 클래스는 static 으로 선언 안하면 큰일 난다](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%9E%90%EB%B0%94%EC%9D%98-%EB%82%B4%EB%B6%80-%ED%81%B4%EB%9E%98%EC%8A%A4%EB%8A%94-static-%EC%9C%BC%EB%A1%9C-%EC%84%A0%EC%96%B8%ED%95%98%EC%9E%90)              
[[Java] static변수와 static 메소드](https://mangkyu.tistory.com/47)             
[Static 사용을 피해야 하는 이유](https://kellis.tistory.com/127)                
[[JAVA] 정적 메소드를 오버라이드 하지 못하는 이유](https://hsik0225.github.io/java/2020/12/17/Static-Override/)