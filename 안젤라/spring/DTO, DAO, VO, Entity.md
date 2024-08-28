![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/8bd2ac1f-1618-484c-8027-2b799474753f/307b8856-df5f-48fc-aaeb-a7d5cfb01bab/image.png)

# **1. DTO (Data Transfer Object)**

- **한줄정리 : 데이터 전달만을 위할때 사용**

### 1) 개념

- **보내는 쪽에서(서비스레이어) Setter**를 사용해 **데이터를 DTO에 담아 보내고**, **받는쪽에서(웹레이어) Getter**를 사용해서 **전달받은** **DTO로부터 데이터를 꺼내는** 방식
- 일반적으로 DB에서 가져온 데이터를 다른 계층에서 사용하기 적합한 형식으로 변환하여 전송하는데 사용
- DTO 속성은 getter / setter 메서드로 구성

### 2) 역할

- DB에서 데이터를 얻어서 Service나 Controller 등으로 보낼 때 사용한다.
- \*\*\*\*엔티티를 DTO 형태로 변환한 후 사용한다.

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/8bd2ac1f-1618-484c-8027-2b799474753f/a00b140b-7aa6-41ae-afa9-5f94cf4384cf/image.png)

### 3) 특징

- _가변 객체 DTO_

```java
// 가변 객체 DTO
// 기본생성자로 생성 후 값을 유동적으로 변경
public class DtoEx {
    private String name;
    private int age;

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

- _불변 객체 DTO_
  - DTO가 setter매서드를 가질 경우 해당 DTO는 데이터가 가변적
  - setter매서드를 삭제하고, 생성자를 통해 속성값을 초기화하여 불변객체로 만들 수 있음
    - 전달과정 중 데이터 불변성을 보장함

```java
// 불변 객체 DTO
// 생성시 지정했던 값이 변하지 않고 getter() 메소드만 사용 가능
public class DtoEx {
    private final String name;
    private final int age;

    public DtoEx(String name, int age) {
        this.name = name;
        this.age = age;
    }

    public String getName() {
        return name;
    }

    public int getAge() {
        return age;
    }
}
```

# **2. DAO (Data Access Object)**

- **한줄정리 : DB에 접근할때 사용**

### 1) 개념

- DB에서 데이터를 가져오거나 DB에 데이터를 저장하는 등의 작업을 수행하는 객체
- 비즈니스 계층과 DB 간의 인터페이스 역할

### 2) 역할

- **실제로 DB에 접근하여 data를 삽입, 삭제, 조회, 수정 등 CRUD 기능을 수행**
- **Service와 DB를 연결하는 고리 역할**

### 3) 특징

- `Mapper`**,** `Repository`**가 dao역할을 한다.**
- DAO에 DB Connection이 설정되어있는지 여부로 DAO의 형태가 나뉨
- `MyBatis`
  - DB Connection정보를 root-context.xml파일에 저장
  - `@Mapper`
    - MyBatis에서 사용되는 인터페이스로, SQL 매퍼 XML 파일과 연동되어 SQL 쿼리 실행
    - 해당 인터페이스를 기반으로 DAO 구현체 생성
- `JPA`(ORM)
  - applicaiton.yml(properties)파일에 설정해 사용
  - `@Repository`
    - Spring Data JPA에서 DAO 역할을 하는 인터페이스나 클래스에 붙이는 어노테이션
    - Spring이 자동으로 DAO 구현체 생성
      - `@Repository`는 해당 클래스가 데이터베이스와의 상호작용을 담당하는 데이터 액세스 객체(DAO)임을 Spring에 알린다. 이 클래스는 주로 CRUD(Create, Read, Update, Delete) 작업을 수행한다.
      - Spring 컨텍스트에서 `@Repository`로 표시된 클래스는 자동으로 빈(bean)으로 등록된다. 이 빈은 다른 서비스나 컴포넌트에서 의존성 주입(Dependency Injection)을 통해 사용할 수 있다.
      - 예를 들어, `@Autowired`를 통해 `@Repository`로 등록된 DAO 클래스의 인스턴스를 주입받을 수 있다.
- 데이터베이스와 상호작용하는 모든 클래스에 이 애노테이션을 붙이는 것이 권장된다.

```java
@Repository
@RequiredArgsConstructor
public class MemberRepository {

```

private final EntityManager em;

public void save(Member member) {
em.persist(member);
}

public Member findOne(Long id) {
return em.find(Member.class, id);
}

public List<Member> findAll() {
return em.createQuery("select m from Member m", Member.class).getResultList();
}

public List<Member> findByName(String name) {
return em.createQuery("select m from Member m where m.name =:name", Member.class)
.setParameter("name", name)
.getResultList();
}
}

```

# 3. **VO (Value Object)**

- **한줄정리 : 데이터의 불변성을 보장해야할 때 사용**

### 1) 개념

- 값 객체
- 데이터의 불변성을 보장하고 상태를 가지지 않음
- 보통 DB의 한 행 또는 여러 행을 나타내는데 사용

### 2) 역할

- Entity를 자바의 개념으로 해석한것
- **Entity를 자바의 객체로 대입하자면 VO가 된다.**

### 3) 특징

- 동등성
    - 값 객체의 모든 속성이 같은 `값`을 가질 시 동일한 객체로 판단
    - 이때, [equals(), hashCode() 오버라이딩](https://github.com/jmxx219/CS-Study/blob/main/Java-Spring/equals%EC%99%80%20hashCode.md#equals%EC%99%80-hashcode-1)을 했다는 전제 조건 필요
    - 해당 속성들은 `primitive 타입`
- 불변성
    - 수정자(setter)를 포함하지 않고, 생성자를 통해서 값 초기화
    - 새로운 값 사용을 위해선 새로운 객체 생성
        - 중간에 데이터를 조작할 수 없음
        - Read Only
- 자가 유효성 검사(Self-Validation)
    - 유효성 검사 진행 후 VO 생성
    - VO 사용 시 유효성 검사가 보장되어있으므로 안전하게 사용 가능
- getter 이외의 로직 포함 가능
- 값 타입을 여러 엔티티에서 공유 시 `Side effect` 발생가능

# 4. **Entity**

- **한줄정리 : 데이터베이스와 매핑되어 있는 핵심 클래스** , 절대 요청이나 응답 값을 전달하는 클래스로 사용하면 안됨

### 1) 개념

- JPA와 같은 ORM프레임워크에서 사용되는 개념으로 `@Entity`어노테이션을 붙여 엔티티를 나타냄
- DB에 있는 개념

### 2) 역할

- Entity 클래스를 기준으로 테이블이 생성되고, 스키마가 변경됨
- 실제 DB의 테이블과 정확하게 매칭되는 속성들을 갖도록 디자인 된 클래스

### 3) 특징

- 보통 식별자를 가짐(Primary key)
    - ID로 구분 되며 로직 포함 가능
- 비즈니스 로직을 엔티티로 모음으로 응집도가 높아짐
- 데이터베이스의 테이블과 직접 연관
    - ORM 프레임워크에서 데이터베이스 테이블과 매핑되어 CRUD작업 수행

# 5. **각 개념간 차이점**

- **DTO와 VO**
    - `DTO`는 데이터 전송을 위한 객체로 가변적일 수 있음
    - `VO`는 불변객체로 값 자체를 표현
- **DAO와 Repository**
    - `DAO`는 데이터 접근 로직을 캡슐화
    - `Repository`는 좀 더 도메인 중심의 접근 방식을 사용해 데이터 계층 관리
- **Entity와 DTO**
    - `Entity`는 데이터베이스 테이블과 직접 매핑되는 객체로 비즈니스 로직 포함 할 수 있음
    - `DTO`는 단순히 데이터 전송을 위한 객체

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/8bd2ac1f-1618-484c-8027-2b799474753f/b55df018-f4c3-4fc8-968d-4a7861c5d7dd/image.png)

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/8bd2ac1f-1618-484c-8027-2b799474753f/eb3048f8-7e5d-416d-acc7-f20ddbc2a919/image.png)

# 6. **DTO와 Entity의 분리**

- **뷰의 결과 값을 전달해주는 DTO와 Entity는 반드시 분리해서 사용해야한다.**
- Entity는 요청/응답 값을 사용하는 클래스로 사용하면 안됨
- Entity클래스를 요청/응답 값을 전달하는 클래스로 사용할 경우 뷰 변경시 마다 Entity클래스를 변경해야 함
    - REST API 설계시 엔티티의 속성이 변화(추가, 삭제)될때마다 API명세가 바뀌게 됨
    - API명세가 테이블에 의존하는 현상이 발생
- 요청/응답 값을 사용하는 클래스로 DTO의 사용
    - 뷰의 변경에 따라 다른 클래스에 영향을 끼치지 않고 자유롭게 변경 가능
    - 여러 테이블들을 Join한 결과값을 응답값으로 표현하기 편리함

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/8bd2ac1f-1618-484c-8027-2b799474753f/7756832e-cd26-41e1-a756-9d69f989f578/image.png)

# 7. **디자인 패턴에서 이용**

- `DTO`: 여러 데이터 소스를 결합해 한 번에 전송할 수 있도록 함
- `DAO`: 데이터소스와 상호작용하는 로직 캡슐화
- `VO`: 두 VO객체가 동일한 값을 가지면 동일하다고 간주
- `Repository`: 데이터 계층을 추상화해 데이터 접근로직을 캡슐화하고, 비즈니스 도메인과 데이터 매핑 관리
```
