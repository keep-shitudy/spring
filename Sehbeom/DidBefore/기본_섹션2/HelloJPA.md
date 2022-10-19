# Hello JPA

간단한 애플리케이션을 만들어 보는 실습.

## 1. H2 Database

- JPA는 서버 단에 해당하므로 간단하게라도 Database가 있어야 함
- 따라서 H2를 활용하여 간단히 구성 후 실습 진행.
- Web용 Query를 보내볼 수 있다.
- 가볍다.
- 시퀀스, AUTO INCREMENT 기능 지원.

1) H2 설정

- 아래 링크로 접속

[H2 Database Engine](http://www.h2database.com/)

- 본인 PC에 알맞는 플랫폼으로 다운. (1.4.200 버전으로!)
    - Mac의 경우, All Platforms로 선택

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled.png)

- 다운받은 zip파일 압축 해제 후 원하는 위치로 옮기기
- 해당 위치의 터미널 실행
- 아래 명령어로 h2 실행 (Mac 기준)

- ❗️Permission Denied Error
    
    처음 실행 시, Permission Denied 에러가 발생할 수 있음. 아래 명령어 입력 후 재시도.
    
    → chmod 755 h2.sh
    

```jsx
./h2.sh
```

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%201.png)

- 그럼 아래 사진과 같이 h2 콘솔 웹사이트가 실행됨.

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%202.png)

- 연결 버튼을 누르면 콘솔로 이동한다.
- ❗️Database "~/test" not found, and IFEXISTS=true, so we cant auto-create it [90146-199]
    - 맨 처음 실행할 때 DB가 없으면 나타나는 오류. 새로 생성해주면 됨.
    
    ![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%203.png)
    
    - 위 사진과 같이 JDBC URL에 **jdbc:h2:~/test** 을 입력한 후 연결 버튼을 누르면 생성됨.
    - 이후부터는 JDBC URL에 **jdbc:h2:tcp://localhost/~/test**을 넣어야함.

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%204.png)

## 2. JAVA 프로젝트 생성

- intelliJ IDEA 실행
- new Project
- Maven 선택 후 Next

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%205.png)

- 아래 사진과 같이 설정

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%206.png)

### 2-1. Dependency 설정 (pom.xml)

- 프로젝트 생성 후, 아래 사진과 같이 pom.xml 파일에 외부 라이브러리 설정(dependencies)을 진행한다.

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%207.png)

```java
		<dependencies>
        <!-- JPA 하이버네이트 -->
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-entitymanager</artifactId>
            <version>5.3.10.Final</version>
        </dependency>
        <!-- H2 데이터베이스 -->
        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
            <version>1.4.200</version>
        </dependency>
    </dependencies>
```

- jpa는 하나의 인터페이스이고, hibernate 내에서 구현되어 있음.
- 따라서 hibernate를 가져오면 jpa의 모든 기능을 활용할 수 있음.
- 또한 본 실습에서는 H2 DB를 활용하므로 관련 dependency 또한 가져온다.
- 아래 사진과 같이 IDEA의 우측 상단 ‘Maven’ 버튼을 누르면 가져온 Dependencies를 확인할 수 있다.

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%208.png)

### 2-2. JPA 설정 (persistence.xml)

- 표준 위치가 정해져있으므로 다음 위치에 persistence.xml 파일을 생성한다.
    - src/main/resources/META-INF/persistence.xml에 위치시킨다.
- 아래 내용을 넣는다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<persistence version="2.2"
             xmlns="http://xmlns.jcp.org/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/persistence http://xmlns.jcp.org/xml/ns/persistence/persistence_2_2.xsd">
    <persistence-unit name="hello">
        <properties>
            <!-- 필수 속성 -->
            <property name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
            <property name="javax.persistence.jdbc.user" value="sa"/>
            <property name="javax.persistence.jdbc.password" value=""/>
            <property name="javax.persistence.jdbc.url" value="jdbc:h2:tcp://localhost/~/test"/>
            <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>
            <!-- 옵션 -->
            <property name="hibernate.show_sql" value="true"/>
            <property name="hibernate.format_sql" value="true"/>
            <property name="hibernate.use_sql_comments" value="true"/>
            <!--<property name="hibernate.hbm2ddl.auto" value="create" />-->
        </properties>
    </persistence-unit>
</persistence>
```

- IDEA에서 제공해주는 다양한 추가 기능들(우측 하단에 표시되는)은 활용하는게 좋다.

(IDEA에서 JPA를 활용한다는 것을 알아차리는게 좋음) 

- <persistence version=”2.2” ~ : JPA 2.2버전을 활용.
- <persistence-unit name=”hello” ~ : JPA를 쓰면서 활용할 “hello”라는 이름의 Database 생성.
- <properties>~
    - 필수 속성 : JPA는 Database를 활용하기 때문에 Database 관련 정보들을 적어줘야 한다.
        - javax~로 시작 : java 표준 라이브러리를 활용. hibernate가 아닌 다른 프레임워크를 써도 그대로 적용될 수 있음.
        - hibernate~로 시작 : hibernate 전용 라이브러리들. hibernate를 활용해야 쓸 수 있는 라이브러리들.
        - 본 실습에서는 H2 DB를 활용하므로 org.h2.Driver를 활용한다.
        - user, password, url 등은 앞서 설정한 H2 DB에 접근할 때 입력하는 정보들이다. (H2 DB 실행하면 켜지는 웹사이트에서 처음 로그인 할 때 입력하는 정보들)
        - hibernate.dialect는 Database 방언을 의미한다.
            - Dialect?
                - JPA는 특정 Database에 종속된 프레임워크가 아님. → 서로 다른 Database에 대해 동일하게 동작할 수 있어야 한다.
                - 다양한 Database를 활용할 때, 유사한 기능이지만 서로 다른 명령어가 쓰이는 경우가 있음.
                
                (Ex. MySQL은 VARCHAR, Oracle은 VARCHAR2 / SQL은 SUBSTRING(), Oracle은 SUBSTR() 등)
                
                - 이처럼 SQL 표준을 지키지 않고, 특정 Database만의 고유한 기능이나 명령어에 대해 자동으로 변환해주는 기능이 Dialect임.
                - 본 실습에서는 H2 Database를 활용하므로 “org.hibernate.dialect.H2Dialect” 를 활용하는 것.
                - 따라서 아래 사진과 같이 Dialect는 JPA와 DB 사이에서 번역해주는 역할을 수행하며, 필요에 맞춰 알맞은 Dialect를 활용하면 된다.
                
                ![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%209.png)
                
            

## 3. JPA 실습

### 3-1. JPA 구동 방식

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%2010.png)

- Persistence 라는 이름의 클래스에서부터 시작.
- Persistence 클래스에서 앞서 persistence.xml 파일에서 설정한 정보를 조회한 후, 내용에 맞추어 ‘EntityManagerFactory’ 클래스를 생성함.
- EntityManagerFactory 클래스에서 필요에 맞춰 EntityManager를 생성함.

### 3-2. Main 설정

- src/main/java/hellojpa Package 생성 후, 안에 JpaMain 자바 클래스 생성

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%2011.png)

- JpaMain 클래스에 아래 내용 입력

```java
package hellojpa;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.Persistence;

public class JpaMain {
    public static void main(String[] args){
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

        EntityManager em = emf.createEntityManager();

        //code~

        em.close();

        emf.close();

    }
}
```

- 앞서 언급한 것처럼 JPA는 Persistence에서부터 시작한다. Persistence 클래스가 persistence.xml 내용을 토대로 생성되었고, 우리는 이를 활용하여 EntityManagerFactory 클래스를 생성한다.
- EntityManagerFactory 클래스를 담을 변수 ‘emf’에 
Persistence의 ‘createEntityManagerFactory()’ 함수로 EntityManagerFactory를 생성하여 저장한다.
    - 파라미터로 앞서 persistence.xml 파일에서 <persistence-unit name=”hello”>~ 로 설정한 name 값을 전달한다.
- EntityManagerFactory의 createEntityManager() 함수를 통해 EntityManager를 생성한다.
- 위 코드의 //code~ 부분에 필요한 api들을 구현하게 되고, 동작이 끝나면 close() 함수를 통해 em과 emf를 닫아준다.

### 3-3. DB 설정

- api를 구현하기 위해 H2 Database에 Table을 생성하고, JPA에서 클래스를 만들어 맵핑시켜야 한다.

- H2 Database의 웹 콘솔로 접속하여 아래 명령어를 입력하고 실행한다.

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%2012.png)

```sql
create table Member(
id bigint not null,
name varchar(255),
primary key(id)
);
```

- DB에 존재하는 Table을 JPA에서도 클래스로 생성한다.
- 아래 사진과 같이 JpaMain과 동일한 위치에 Member 클래스를 생성하고, 내용을 입력한다.

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%2013.png)

```java
package hellojpa;

import javax.persistence.Entity;
import javax.persistence.Id;

@Entity
public class Member {

    @Id
    private Long id;
    private String name;

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

- Member 클래스를 생성하기에 앞서, ‘@Entity’ 어노테이션을 통해 JPA가 관리해야 하는 객체라는 것을 인식시킨다.
- 클래스 이름은 DB의 Table 이름과 동일하게 설정한다.
    - 만약 다르게 설정하고 싶을 경우, 클래스 선언부(public class Member{) 위에 @Table 어노테이션을 놓고 name 속성을 활용한다.
    - @Table(name = “USER”)
- H2 DB에서 id와 name으로 이루어진 Table을 생성하였으므로, Member 클래스도 동일하게 id와 name으로 설정한다.
    - 만약 DB Table의 column 명과 다르게 설정하고 싶을 경우, 해당 변수 위에 @Column 어노테이션을 놓고 name 속성을 활용한다.
    - @Column(name = “username”)
- JPA에서 Table 클래스를 생성할 때, Primary Key도 정해주어야 한다. : @Id 어노테이션
- 외부에서 Member 클래스에 접근할 때, 변수에 접근하기 위한 Getter, Setter 함수도 정의한다.

### 3-4. DB에 Query 보내기 (Insert)

- DB의 Table과 JPA의 클래스 정의도 끝났으므로, DB에 데이터를 저장하는 쿼리를 전송해 본다.

- JpaMain 클래스에 아래와 같이 내용을 추가한다.

```java
package hellojpa;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

public class JpaMain {
    public static void main(String[] args){
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

        EntityManager em = emf.createEntityManager();

        EntityTransaction tx = em.getTransaction();

        tx.begin();
        //code~
        Member member = new Member();

        member.setId(1L);
        member.setName("HelloA");

        em.persist(member);

        tx.commit();

        em.close();

        emf.close();

    }
}
```

- EntityManager 객체를 통해 쿼리를 전송하며, 데이터를 저장하는 JPA 함수는 ‘persist()’ 이다.

(영원히 저장~..)

- JPA 에서는 Transaction 단위가 매우 중요하다. 하나의 로직이 실행될 때마다 Transaction 단위를 설정해주어야 한다.
    - EntityManager의 getTransaction() 함수를 통해 EntityTransaction 객체 ‘tx’를 생성한다.
    - begin() 함수를 통해 Transaction 시작을 설정한다.
    - 로직이 마무리 된 후, commit() 함수를 통해 Transaction 종료 및 Commit을 설정한다.
- Insert 쿼리를 전송하기 전, 어느 테이블의 어떤 데이터를 Insert 할 건지에 대해 정의한다.
    - Member 객체 ‘member’를 생성하고, Setter 함수를 통해 데이터 값을 저장한다.
    - 이후 member 내용을 Insert하는 쿼리를 persist 함수로 DB에 전송한다.

- 위 코드를 실행하면 아래 사진과 같은 로그가 표시된다.

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%2014.png)

- /* insert hellojpa.Member */ : hellojpa 패키지의 Member 객체가 insert 되었다.
- insert into ~ : 실제 전송된 쿼리문이 표시된다.
- 이는 앞서 persistence.xml 의 <properties> 에 설정한 옵션 덕에 표시된다.
    - 설정했던 옵션
    
    ```java
    <property name="hibernate.show_sql" value="true"/>
    <property name="hibernate.format_sql" value="true"/>
    <property name="hibernate.use_sql_comments" value="true"/>
    ```
    
    - hibernate.show_sql : 전송된 쿼리문을 보여준다.
    - hibernate.formate_sql : 쿼리문을 보여줄 때 보기 편한 형식에 맞춰 보여준다.
    - hibernate.use_sql_comments : 쿼리문 위의 주석(/* insert hellojpa.Member */)을 통해 전송된 쿼리문에 대한 정보를 보여준다.

- 아래 사진과 같이 DB에도 저장되는 것을 확인할 수 있다.

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%2015.png)

### 3-5. 정석 코드 (예외처리, try - catch - finally)

- 3-4에서 작성한 코드는 기능은 하지만 예외처리가 되어 있지 않음.

(만약 중간에 오류가 날 경우, 후에 em.close나 emf.close 같은 코드들은 실행되지 않음.)

- 따라서 예외처리를 해주어야 한다.

```java
package hellojpa;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

public class JpaMain {
    public static void main(String[] args){
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

        EntityManager em = emf.createEntityManager();

        EntityTransaction tx = em.getTransaction();

        tx.begin();
        
        try{
            Member member = new Member();

            member.setId(2L);
            member.setName("HelloB");

            em.persist(member);

            tx.commit();
        } catch(Exception e){
            tx.rollback();
        } finally{
            em.close();
        }
        
        emf.close();

    }
}
```

- Member 객체를 생성하고 persist 한 후 commit 하는 과정까지 try한다.
- 만약 try 과정에 문제가 생길 경우, rollback() 한다.
- 문제 여부와 상관없이 해당 과정이 모두 끝나면 em.close()는 꼭 진행한다.
    - EntityManager는 DB와의 커넥션을 담당하므로, 매우 중요하다. 따라서 close 과정이 꼭 이루어져야 한다.
    

### 3-6. DB에 쿼리 보내기 (Select)

- Select 쿼리를 보낼 때는 EntityManager의 find() 함수를 활용한다.
    - 파라미터로 **클래스 정보**와 **PK** 값을 전달한다.
    - 클래스 정보 : select 하려는 table과 맵핑된 클래스 (Member.class)
    - PK : 해당 Table에서 select 하려는 데이터의 PK 값

```java
package hellojpa;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

public class JpaMain {
    public static void main(String[] args){
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

        EntityManager em = emf.createEntityManager();

        EntityTransaction tx = em.getTransaction();

        tx.begin();

        try{
            Member findMember = em.find(Member.class, 1L);
            System.out.println("findMember.id = " + findMember.getId());
            System.out.println("findMember.name = " + findMember.getName());

            tx.commit();
        } catch(Exception e){
            tx.rollback();
        } finally{
            em.close();
        }

        emf.close();

    }
}
```

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%2016.png)

- 위 사진과 같이 Select 쿼리가 전송되는 것을 확인할 수 있고, 그 결과로 앞서 Insert한 1, HelloA 데이터를 받아오는 것을 확인할 수 있다.

### 3-7. DB에 쿼리 보내기 (Delete)

- Delete 쿼리는 EntityManager의 remove() 함수를 활용한다.
- 파라미터로는 제거하고자 하는 데이터 정보를 담고 있는 객체를 전달해야 한다.
- 따라서 먼저 Select를 통해 제거하고자 하는 데이터를 받아 객체를 생성한 후,
    
    (Member findMember = em.find(Member.class, 1L);
    
    해당 객체를 remove 함수의 인자로 전달해야한다.
    
    (em.remove(findMember);)
    

```java
package hellojpa;

import javax.persistence.EntityManager;
import javax.persistence.EntityManagerFactory;
import javax.persistence.EntityTransaction;
import javax.persistence.Persistence;

public class JpaMain {
    public static void main(String[] args){
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("hello");

        EntityManager em = emf.createEntityManager();

        EntityTransaction tx = em.getTransaction();

        tx.begin();

        try{
            Member findMember = em.find(Member.class, 1L);

            em.remove(findMember);

            tx.commit();
        } catch(Exception e){
            tx.rollback();
        } finally{
            em.close();
        }

        emf.close();

    }
}
```

### 3-8. DB에 쿼리 보내기 (Update)

- Update 쿼리는 매우 간단하게 이루어 질 수 있다.
- 보통 생각하기에는 아래와 같이 로직이 이루어 져야 한다고 생각한다.
    - find() 함수로 데이터를 받아와 객체에 저장시킨 후, Setter 함수로 내용을 수정하고 다시 persist() 한다.
    - 하지만 이렇게 하지 않는다.
        
        ```java
        			try{
                    Member findMember = em.find(Member.class, 2L);
        
                    findMember.setName("HelloJPA");
        						em.persist(findMember);
        
                    tx.commit();
                } catch(Exception e){
                    tx.rollback();
                } finally{
                    em.close();
                }
        ```
        
- JPA 에서는 persist() 함수를 생략한다. persist() 하지 않고 Setter 함수를 써서 객체 내용을 수정하기만 해도 DB에 반영된다.

```java
				try{
            Member findMember = em.find(Member.class, 2L);

            findMember.setName("HelloJPA");

            tx.commit();
        } catch(Exception e){
            tx.rollback();
        } finally{
            em.close();
        }
```

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%2017.png)

- 위 사진과 같이 update 쿼리 관련 함수 없이, 그저 findMember 객체의 내용을 Setter 함수로 수정했을 뿐인데 update 쿼리가 전송되는 것을 확인할 수 있다.

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%2018.png)

- 즉, find() 함수로 데이터를 받아와 findMember 객체에 넣어두면, 해당 객체와 DB Table의 데이터가 연결되어 있는 듯한 느낌이다.
- 이는 JPA가 Commit 하기 전,
    
    JPA에서 관리하는 클래스의 객체들 중 수정된 내용이 있는지 파악하는데, 
    
    수정된 내용이 있을 경우 알아서 update 쿼리를 만들어 전송하므로 가능한 것이다.
    

## 4. JPA 유의사항

### 4-1. EntityManagerFactory & EntityManager

- EntityManagerFactory는 하나만 생성해서 공유하고, EntityManager는 요청마다 생성하고 제거한다.
- EntityManagerFactory : 하나만 생성해서 애플리케이션 전체에서 이를 공유해서 활용한다.
- EntityManager : 고객의 요청이 올 때마다 생성해서 활용하고, 각 요청이 끝나면 반드시 버려야 한다.
- **EntityManager는 쓰레드 간에 절대 공유되어서는 안된다. (안그럼 에러난다)**
    - 하나의 EntityManager가 여기저기서 쓰이면 안된다. EntityManager 기능이 필요하면 그때 그때 새로 생성해서 활용하고 버려야 한다.

### 4-2. Transaction

- **JPA의 모든 데이터 변경은 Transaction 안에서 실행되어야 한다.**

## 5. JPQL (객체 지향 SQL)

- 단순한 쿼리문은 find(), persist() 등의 함수를 활용하면 된다.
    - EntityManager.find()
    - 객체 그래프 탐색 (a.getB().getC())
- JPQL : 쿼리문이 단순하지 않은 경우
    - 쿼리문에 특정 조건이 추가되거나, join 시킨 결과를 조회해야 하거나..
    - 나이가 18살 이상인 회원을 모두 검색
- 서버 개발 과정에서 쿼리문을 디테일하게 작성하는 과정은 필요할 수 밖에 없다.
- JPA의 개념을 유지하여 엔티티 객체를 대상으로 검색하는 JPQL이 활용된다.
- JPQL로 쿼리문을 개발해두면, 여기에도 방언이 적용되어 DB가 바껴도 수정할 필요가 없다. (세세한건 수정이 필요할 수 있지만)
- JPQL은 후에 자세히 다룬다.

### 5-1. JPQL vs SQL

- SQL : DB의 Table을 대상으로 개발
- JPQL : DB의 Table이 아닌, 클래스 객체를 대상으로 개발
- 또한, JPQL은 Java 내에서 함수 형태로 작성되며, 이를 리스트, 해시맵 등과 같은 자료구조와 병합되어 활용될 수 있어 개발 과정에서 좀 더 직관적이고 편하게 활용될 수 있다.

### 5-2. JPQL 예시

- JPQL은 EntityManager의 createQuery() 함수로 구현될 수 있다.

```java
				try{
            List<Member> result = em.createQuery("select m from Member as m", Member.class).getResultList();
                
						for (Member member : result){
                System.out.println("member.name = " + member.getName());
            }    

            tx.commit();
        } catch(Exception e){
            tx.rollback();
        } finally{
            em.close();
        }
```

- select m from Member as m 이라는 형태로 쿼리가 작성되었고, Member 테이블의 모든 필드의 데이터를 받아오게 된다.
- createQuery() 함수에 추가적으로 getResultList() 함수가 붙어 List 형태로 반환되어진다.
- 이는 자바의 List 자료구조와 함께 사용되어 질 수 있으며, 후에 다른 형태로 개발될 때도 훨씬 편리해진다.

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%2019.png)

- 또한 JPQL을 활용할 경우, 페이지내이션과 같은 추가 기능이 적용될 때도 훨씬 편리해진다.

```java
				try{
            List<Member> result = em.createQuery("select m from Member as m", Member.class)
                    .setFirstResult(5)
                    .setMaxResults(8)
                    .getResultList();

            for (Member member : result){
                System.out.println("member.name = " + member.getName());
            }

            tx.commit();
        } catch(Exception e){
            tx.rollback();
        } finally{
            em.close();
        }
```

![Untitled](Hello%20JPA%20267caf08749c4d6981dddfe6b0d9762e/Untitled%2020.png)

- setFirstResult(5) setMaxResult(8) : 5~8번째 데이터를 가져온다.
- 위와 같이 함수 형태로 구현될 수 있으며, 전송된 쿼리문을 확인해보면 limit, offset 옵션이 설정된 것을 확인할 수 있다.