# [스프링 입문] Section 6. 스프링 DB 접근 기술

---

강의에서는 H2 데이터베이스, 순수 Jdbc 를 다루지만, 현재는 잘 쓰이지 않는 기술이라 따로 정리는 안한다.

---

## 스프링 JDBC Template

스프링 ****JdbcTemplate, MyBatis와 같은 라이브러리는 순수 Jdbc API 코드에서의 반복되는 부분을 대부분 제거해 준다는 장점이 있다. (**단 SQL문은 직접 작성해야 한다)**

```java
public class JdbcTemplateMemberRepository implements MemberRepository{

    private final JdbcTemplate jdbcTemplate;

    @Autowired (생략 가능)
    public JdbcTemplateMemberRepository(DataSource dataSource){
        jdbcTemplate = new JdbcTemplate(dataSource);
    }

    @Override
    public Member save(Member member) {
        SimpleJdbcInsert jdbcInsert = new SimpleJdbcInsert(jdbcTemplate);
        jdbcInsert.withTableName("member").usingGeneratedKeyColumns("id");
        Map<String, Object> parameters = new HashMap<>();
        parameters.put("name", member.getName());
        Number key = jdbcInsert.executeAndReturnKey(new
                MapSqlParameterSource(parameters));
        member.setId(key.longValue());
        return member;
    }

    @Override
    public Optional<Member> findById(Long id) {
        List<Member> result = jdbcTemplate.query("select * from where id = ?",memberRowMapper(),id);
        return result.stream().findAny();
    }

    @Override
    public Optional<Member> findByName(String name) {
        List<Member> result = jdbcTemplate.query("select * from where name = ?",memberRowMapper(),name);
        return result.stream().findAny();
    }

    @Override
    public List<Member> findAll() {
        return jdbcTemplate.query("select * from member",memberRowMapper());
    }

    private RowMapper<Member> memberRowMapper() {
        return (rs, rowNum) -> {
            Member member = new Member();
            member.setId(rs.getLong("id"));
            member.setName(rs.getString("name"));
            return member;
        };
    }
}
```

jdbcTemplate을 사용하기 위해 생성자에서 **dataSource**를 받아 jdbcTemplate을 생성할 때 이를 인자로 넘겨주고 jdbcTemplate 객체를 생성한다.

나머지 함수에서는 중복되는 코드를 memberRowMapper() 함수로 작성하고 jdbcTemplate라이브러리를 사용하여 이를 sql과 함께 넘겨주어 sql의 결과를 List형태로 받았다.

memberRowMapper() 함수는 이전에 중복되었던 코드 부분으로 회원 객체를 생성하고 반환된 결과 값을 세팅해 반환한다.

```java
@Bean
    public MemberRepository memberRepository(){
        //return new MemoryMemberRepository();
        //return new JdbcMemberRepository(dataSource);
        return new JdbcTemplateMemberRepository(dataSource);
    }
```

---

# JPA

**JPA**를 사용하면 기존의 반복 코드를 줄일 뿐 아니라 기본적인 SQL도 JPA가 직접 만들어서 DB에 접근할 수 있다는 장점이 있다.

이렇게 JPA를 사용하면 SQL과 데이터 중심 설계에서 **객체 중심의 설계**로 패러다임을 전환할 수 있고, **개발 생산성을 크게 향상**시킬 수 있다.

JPA 를 사용하기 위해서는 라이브러리를 추가해야 한다.

```java
dependencies {
		implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
}

resources/application.properties 에 추가해야 할 코드

spring.jpa.show-sql=true
spring.jpa.hibernate.ddl-auto=none //create 가능
```

- show-sql: JPA가 SQL을 생성할 때 출력.
- ddl-auto: JPA가 테이블을 자동으로 생성. none 은 테이블을 생성하지 않겠다는 의미이며 create를 쓰면 JPA가 자동으로 테이블을 생성해준다.

JPA 는 인터페이스로, hibernate와 같은 구현체를 사용한다.

JPA는 ORM ( Object Relational Mapping)기술로, 객체와 관계형 데이터를 mapping 하는 기술이다.

```java
import javax.persistence.*;

@Entity
public class Member {

    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    //@Column(name="username")
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

객체(Member)와 DB를 연동하기 위해 **@Entity** 애노테이션을 추가하였다.

pk를 설정하기 위해 id에 **@Id** 애노테이션으로 pk 설정을 하고, **@GeneratedValue(strategy = GenerationType.IDENTITY)**를 사용하면 기존에 우리가 설정하였던 것처럼 id값을 자동으로 생성하여 pk값으로 지정해 준다.

주석 처리된 **@Column** 애노테이션을 사용하면 name 멤버 변수를 DB의 'username'과 mapping 할 수도 있다.

이처럼 각 애노테이션을 사용하여 DB와 mapping을 시도하고 이를 바탕으로 자동으로 SQL문을 작성한다.

```java
public class JpaMemberRepository implements MemberRepository{
    
    private final EntityManager em;
    
    public JpaMemberRepository(EntityManager em){
        this.em = em;
    }
    
    @Override
    public Member save(Member member) {
        em.persist(member);
        return member;
    }

    @Override
    public Optional<Member> findById(Long id) {
        Member member = em.find(Member.class,id);
        return Optional.ofNullable(member);
    }

    @Override
    public Optional<Member> findByName(String name) {
        List<Member> result = em.createQuery("select m from Member m where m.name = :name",Member.class)
                .setParameter("name",name)
                .getResultList();
        return result.stream().findAny();
    }

    @Override
    public List<Member> findAll() {
        return em.createQuery("select m from Member m",Member.class)
                .getResultList();
    }
}
```

JPA는 작동시 **EntityManager**로 동작합니다. 이는 스프링 부트가 JPA 라이브러리를 받으면 생성하는 것으로, 이전 강의에서 dataSource를 주입받은 것처럼 생성된 manager를 주입받으면 된다.

**save() 함수는** 다음과 같이 **em.persist()**로 간단하게 구현할 수 있다. 인자로 회원 객체를 넘겨주기만 하면 JPA가 insert query를 자동으로 작성해서 db에 넣고 id를 생성, 이를 받아 pk로 세팅한다. id를 부여받아 DB에 저장된 회원 객체를 반환하기만 하면 된다.

**findById() 함수는** **em.find() 함수로** 구현할 수 있다. 인자로 조회할 type, 조회 시 식별할 pk값이 필요하며, 반환된 회원 객체를 Optional로 감싸 반환한다. 위와 같은 메서드를 사용 시 자동으로 select query를 작성, 실행한다.

**findByName() 함수는** 위의 findById()와 같이 pk기반으로 검색하는 함수가 아니기 때문에 다른 방법을 사용한다.

**em.createQuery()** 메서드를 사용해 안에 **JPQL**을 작성하여 검색한다. 이때 기존에 작성했던 SQL처럼 테이블 대상으로 회원의 id, name을 검색하는 것이 아니고, 회원 객체(엔티티)를 대상으로 쿼리를 보내 해당하는 객체 자체를 검색한다.

기존에는 id, name을 select 하여 새롭게 회원 객체를 생성해 이를 세팅하고 세팅된 객체를 반환하였다고 한다면, 이제는 검색하여 반환된 회원 객체 자체를 반환할 수 있다.

**findAll()** 함수 또한 **em.createQuery()**를 사용하여 검색된 모든 결과를 리스트 형태로 받아 바로 반환한다.

추가적으로 **JPA의 모든 데이터 변경은 트랜잭션 안에서 수행**되어야 한다.

```java
import org.springframework.transaction.annotation.Transactional;

@Transactional
public class MemberService {}
```

회원 서비스 안의 join()함수는 데이터를 추가하는 기능이므로 데이터 변경 시 트랜잭션이 필요하고, 이를 **@Transational** 애노테이션을 추가하여 해결할 수 있다.

이때 스프링은 해당 클래스 메서드를 실행할 때 트랜잭션을 시작하고, 메서드가 정상 종료되면 트랜잭션을 커밋, 런타임 예외가 발생한다면 롤백을 수행한다.

```java
@Bean
    public MemberRepository memberRepository(){
        //return new MemoryMemberRepository();
        //return new JdbcMemberRepository(dataSource);
        //return new JdbcTemplateMemberRepository(dataSource);
        return new JpaMemberRepository(em);
    }
```

---

# 스프링 데이터 JPA

JPA 만 사용해도 개발해야할 코드가 줄고, 생산성 또한 증가하지만, 스프링 JPA를 사용하면 repository를 구현 클래스 없이 인터페이스만을 작성하여 개발을 할 수 있다. CRUD 또한 스프링 데이터 JPA에서 기본으로 제공한다.

따라서 개발자는 핵심적인 비즈니스 로직 개발에만 집중할 수 있다.

```java
import hello.hellospring.domain.Member;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Optional;

public interface SpringDataJpaMemberRepository extends JpaRepository<Member,Long> ,MemberRepository{
    @Override
    Optional<Member> findByName(String name);
}
```

SpringDataJpaMemberRepository 인터페이스는 스프링 데이터 JPA에서 제공하는 JpaRepository를 받는다. 이때 저장되는 Member 클래스와 pk의 type인 Long을 명시해주고, JpaRepository와 같이 MemberRepository 또한 받는다.

```java
@Configuration
public class SpringConfig {

    private final MemberRepository memberRepository;

    public SpringConfig(MemberRepository memberRepository){
        this.memberRepository = memberRepository;
    }

    @Bean
    public MemberService memberService(){
        return new MemberService(memberRepository);
    }
```

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/a0d18d73-b7d6-4a9a-9ee4-60eaf7241062">

우리가 인터페이스를 생성할 때 상속받은 JpaRepository는 PagingAndSortingRepository인터페이스를 상속받고, 이는 CrudRepository, Repository를 상속받는다. 해당 인터페이스들에는 save(), findOne(), findAll(), findById()과 같이 우리가 이전에 직접 구현했던 기능들 뿐 아니라, 이 외에도 기본적인 CRUD를 수행하거나 단순 조회를 수행하는 기능과 count(), delete()와 같은 기능 또한 구현되어있고, 페이징 기능 또한 제공하고 있다.

만약 findByName 처럼 공통으로 사용되지 않는 메서드 들은 인터페이스에  Optional<Member> findByName(String name); 처럼 적어두면 스프링 데이터 JPA에서 자동으로 쿼리를 생성해준다.