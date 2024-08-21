## Reflection 왜 쓸까?
**정의**
  * 구체적인 클래스 타입을 알지 못해도 그 클래스의 메소드, 타입, 변수들에 접근할 수 있도록 해주는 자바 API
  * 런타임에 객체의 타입을 몰라도 특정 클래스의 필드 또는 메서드를 호출할 수 있도록 해주는 자바 API

**왜 쓸까?**
  * 프로그램의 유연성을 높여줌. 런타임에 클래스의 정보를 얻거나, 객체를 조작할 수 있기 때문에, 동적으로 코드를 변경하거나 확장하는 것이 가능
  * 직접 작성한 코드가 아닌 프레임워크나 라이브러리에서는 사용자가 어떤 클래스를 만들지, 객체의 타입이 뭔지 컴파일 시점에 알 수 없음 -> 이를 동적으로 해결하기 위해 사용

**리플렉션은 `클래스 메타데이터(클래스의 이름, 메서드, 필드, 인터페이스, 상속관계)`를 어디서 불러올까?**
  - Java8 이전에는 클래스 메타데이터가 퍼머넌트 제네레이션(PermGen) 영역에 저장됨.
    PermGen은 힙(heap) 메모리의 한 부분으로, 클래스 메타데이터, 상수 풀, 메서드 데이터 등이 저장됨.
  - Java 8부터는 PermGen 영역이 제거되고, 메타스페이스(Metaspace)가 도입됨. 메타스페이스는 힙 영역이 아닌 네이티브 메모리(native memory)에서 관리.
    - 메타스페이스는 PermGen의 단점(예: 메모리 크기의 고정)을 보완하고, 클래스 메타데이터의 동적 할당을 더 유연하게 처리함.


## Class 클래스

- 리플렉션의 가장 핵심은 `Class` 클래스로, 리플렉션을 사용하라면 `Class<T>` 타입을 가져와야 함
- `Class<T>` 타입을 통해 `Constructor`와 `Method` 그리고 `Field` 정보를 가져올 수 있음

<br/>

### Class 객체 획득 방법

```Java
Class<Member> aClass = Member.class; // (1) {클래스 타입}.class

Member member1 = new Member();
Class<? extends Member> bClass = member1.getClass(); // (2) {인스턴스}.getClass()

Class<?> cClass = Class.forName("org.example.Member"); // (3) Class.forName("{전체 도메인 네임}")
```

- `{클래스 타입}.class`: 클래스의 `class` 프로퍼티를 통해 획득하는 방법
- `{인스턴스}.getClass()`: 인스턴스 변수의 메서드인 `getClass()` 사용
- `Class.forName("{전체 도메인 네임}")`: `Class` 클래스의 `forName()` 정적 메소드에 **FQCN**를 전달하여 해당 경로와 대응하는 클래스에 대한 `Class` 클래스의 인스턴스를 얻는 방법
    - **FQCN(Fully Qualified Class Name)**: 해당 클래스가 속한 패키지명을 모두 포함한 이름


<br/>

#### getXXX() vs getDelcaredXXX()

- Class 객체의 메서드 중에서 `getFields()`, `getMethods()`와 같은 형태와 `getDeclaredFields()`, `getDeclaredMethods()`와 같은 형태로 정의된 메서들이 존재함
- `getXXX()`: 상속받은 클래스와 인터페이스를 포함하여 모든 public인 요소들을 가져옴
- `getDelcaredXXX()`: 상속받은 클래스와 인터페이스를 제외하고 해당 클레스에 직접 정의된 메서드들을 모두 가져옴
    - 접근 제어자와 상관없이 요소에 접근할 수 있음

<br/>

### Constructor_생성자 획득 방법

- `Constructor`는 `java.lang.reflect` 패키지에서 제공하는 클래스이며, 클래스 생성자에 대한 정보와 접근을 제공함
  ```Java
  Constructor<?> constructor = aClass.getDeclaredConstructor(); // 생성자 가져오기
  Object object = constructor.newInstance(); // 인스턴스 생성
  Member member = (Member) constructor.newInstance(); // 타입 캐스팅
  
  // 파라미터가 존재하는 생성자의 경우
  Constructor<?> allArgsConstructor = getDeclaredConstructor(String.class, int.class);
  noArgsConstructor.setAccessible(true);
  Member member = (Member) noArgsConstructor.newInstance();
  ```

<br/>

### Method 획득 방법

- 리플렉션을 사용하여 Method 타입의 오브젝트를 획득하여 객체 메소드에 직접 접근할 수 있음
  ```Java
  Class<Member> aClass = Member.class;
  Member member = new Member("길동", 25);
  
  Method sayMyName = aClass.getDeclaredMethod("sayMyName");
  sayMyName.invoke(member);
  // 내 이름은 길동
  ```
    - `Method` 타입의 `invoke()`를 사용하여 메소드를 직접 호출할 수 있음


<br/>

### Field 획득 방법

- 리플렉션을 사용하여 Field 타입의 오브젝트를 획득하여 객체 필드에 직접 접근할 수 있음
  ```Java
  Class<Member> aClass = Member.class;
  Member member = new Member("길동", 25);
  
  for (Field field : aClass.getDeclaredFields()) {
      field.setAccessible(true);
      String fieldInfo = field.getType() + ", " + field.getName() + " = " + field.get(member);
      System.out.println(fieldInfo);
  }
  /*
      class java.lang.String, name = 길동
      int, age = 25
  */
  ```

<br/>
<br/>

### + 리플렉션은 무조건 기본 생성자가 필요할까?

- 모든 클래스에 기본 생성자가 필요한 이유는 Java Reflection API로 가져올 수 없는 정보 중 하나가 바로 생성자의 인자 정보이기 때문이다.
    - 따라서 기본 생성자 없이 파라미터가 있는 생성자만 존재한다면 Reflection이 객체를 생성할 수 없게 되는 것이다.
- 하지만 지금은 생성자의 파라미터 정보를 가져올 수 있다. Java7까지는 파라미터 정보를 가져올 수 없었지만, java8에서 리플렉션의 Parameter가 추가되었다.
- 그럼에도 불구하고 기본생성자를 요구하는 이유는 기본 생성자로 객체를 생성하고 필드를 통해 값을 넣어주는 것이 가장 간단한 방법이기 때문이다.
    - 다음 글을 참고해보자. [기본 생성자가 필요한 '진짜' 이유 (리플렉션 오해 바로 잡기!!!)](https://colour-my-memories-blue.tistory.com/16)


<br/>
<br/>