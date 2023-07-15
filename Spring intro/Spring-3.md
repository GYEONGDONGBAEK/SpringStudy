# [스프링 입문] Section 3. 회원 관리 예제 - 백엔드 개발

---

## 비즈니스 요구사항 정리

- 데이터: 회원ID, 이름
- 기능: 회원 등록, 조회
- 아직 데이터 저장소가 선정되지 않음(가상의 시나리오)

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/46831357-2ab9-4396-9280-ca0cde30c91b">

- 컨트롤러: 웹 MVC의 컨트롤러 역할
- 서비스: 핵심 비즈니스 로직 구현
- 리포지토리: 데이터베이스에 접근, 도메인 객체를 DB에 저장하고 관리
- 도메인: 비즈니스 도메인 객체, 예) 회원, 주문, 쿠폰 등등 주로 데이터베이스에 저장하고 관리됨

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/0f708a08-e061-4cc7-9197-f4f729d2e40d">

회원 정보를 저장하는 MemberRepository는 인터페이스로 설계되어있음

=> 왜? 아직 데이터 저장소가 선정되지 않았기 때문에 초기 개발 단계에서는 구현체로 가벼운 메모리 기반의 데이터 저장소를 사용하고, 추후에 DB가 정해지는 경우 DB에 맞춰서 바꿔서 사용할 수 있도록

## **회원 도메인과 리포지토리 만들기**

### **회원 객체(Member.java)**

```java
package hi.hispring.domain;

public class Member {

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

domain package를 생성해서 회원 정보를 담는 객체를 생성해준다.

### **회원 리포지토리 인터페이스 (MemberRepository.java)**

```java
package hi.hispring.repository;

import hi.hispring.domain.Member;

import java.util.List;
import java.util.Optional;

public interface MemberRepository {
    Member save(Member member);
    Optional<Member> findById(Long id);
    Optional<Member> findByName(String name);
    List<Member> findAll();
}
```

repository 패키지를 생성하고 회원의 정보를 저장할 리포지토리 인터페이스를 생성해준다. 4가지 기능 선언해준다.

같은 패키지 안에 구현체를 생성해준다.

### **회원 리포지토리 메모리 구현체 (MemoryMemberRepository)**

```java
package hi.hispring.repository;

import hi.hispring.domain.Member;
import org.springframework.stereotype.Repository;
import java.util.*;

public class MemoryMemberRepository implements MemberRepository{
 
    private static Map<Long, Member> store = new HashMap<>(); //회원를 저장할 하나의 데이터베이스라고 생각하면 됨.
    private static long sequence = 0L; //일련 번호
 
    @Override
    public Member save(Member member) {
        member.setId(++sequence); //멤버를 저장할 때 일련번호 값을 1 증가 시켜주기. id setting
        store.put(member.getId(), member);
        return member;
    }
 
    @Override
    public Optional<Member> findById(Long id) {
        return Optional.ofNullable(store.get(id)); //만약 null을 반환할 경우를 대비해서 Optional을 사용해서 감싸준다.
    }
 
    @Override
    public Optional<Member> findByName(String name) {
        return store.values().stream()
                .filter(member -> member.getName().equals(name)) //같은 name을 가지고 있는 객체를 찾으면 반환. 없으면 null반환
                .findAny();
    }
 
    @Override
    public List<Member> findAll() {
        return new ArrayList<>(store.values());
    }
 
    public void clearStore(){
        store.clear();
    }
}
```

---

## 회원 리포지토리 테스트 케이스 작성

내가 구현한 코드가 정상적으로 동작하는지 확인하기 위해서 테스트케이스를 작성한다. 자바는 JUnit이라는 프레임 워크로 테스트 코드를 실행한다.

기존에 작업했던 main 폴더가 아닌 test 폴더에 새로운 패키지를 선언해준다.

테스트 파일은 보통 테스트하고자 하는 파일명+Test를 붙여서 생성하는 것이 일반적이다.

테스트는 @Test 라는 어노테이션을 사용하면 된다. 그리고 테스트하고자 하는 내용을 입력해준다.

```java
class MemoryMemberRepositoryTest {

    MemoryMemberRepository repository= new MemoryMemberRepository();

    @AfterEach
    public void afterEach(){
        repository.clearStore();
    }

    @Test
    public void save(){
        //given
        Member member= new Member();
        member.setName("spring");
        //when
        repository.save(member);
        
        //then
        Member result= repository.findById(member.getId()).get();
        Assertions.assertThat(member).isEqualTo(result);

    }

    @Test
    public void findByName(){
        //given
        Member member1= new Member();
        member1.setName("spring1");
        repository.save(member1);
    
        Member member2= new Member();
        member2.setName("spring2");
        repository.save(member2);
        
        //when
        Member result = repository.findByName("spring1").get();
        //then
        assertThat(result).isEqualTo(member1);

    }
    @Test
    public void findAll(){
 
```

- @AfterEach : 한번에 여러 테스트를 실행하면 메모리 DB에 직전 테스트의 결과가 남을 수 있다. 이렇게 되면 다음 이전 테스트 때문에 다음 테스트가 실패할 가능성이 있다. @AfterEach 를 사용하면 각 테스트가 종료될 때 마다 이 기능을 실행한다. 여기서는 메모리 DB에 저장된 데이터를 삭제한다.
- 테스트는 각각 독립적으로 실행되어야 한다. 테스트 순서에 의존관계가 있는 것은 좋은 테스트가 아니다

---

## 회원 서비스 개발

```java
public class MemberService {

    private final MemberRepository memberRepository= new MemoryMemberRepository();
    /**
     * 회원가입
     */
    public Long join(Member member){
        //같은 이름이 있는 중복 회원 x

        validateDuplicateMember(member);//중복회원검증
        memberRepository.save(member);
        return member.getId();
    }

    private void validateDuplicateMember(Member member) {
        memberRepository.findByName(member.getName())
                .ifPresent(m ->{
                    throw new IllegalStateException("이미 존재하는 회원입니다");
                });
    }
    /**
     * 전체 회원 조회
     * */
    public List<Member> findMember(){
        return memberRepository.findAll();
    }

    public Optional<Member> findOne(Long memberId){
        return memberRepository.findById(memberId);
    }
}
```

### **회원 서비스 테스트**

아까와 같은 방법으로 테스트를 하려고 한다. 이번에는 테스트 클래스를 단축키를 사용해서 만들어보려고 한다.

단축키는 window 기준으로 ctr+shift+t  를 누르면 테스트 클래스를 만들 수 있는 창이 생긴다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/8ba5bbcd-2b24-4c6d-9023-9c11526492ca">

Create New Test를 눌러서 창이 나오면 이름을 입력하고 테스트 클래스를 만들어준다.

테스트 코드는 한글로 작성해도 된다.

```java
class MemberServiceTest {

    MemberService memberService= new MemberService();
    @Test
    void 회원가입() {
        //given
        Member member=new Member();
        member.setName("hello");

        //when
        Long saveId=memberService.join(member);
        //then
        Member findMember=memberService.findOne(saveId).get();
        Assertions.assertThat(member.getName()).isEqualTo(findMember.getName());
    }

    @Test
    public void 중복_회원_예외(){
        //given
        Member member1 = new Member();
        member1.setName("spring");

        Member member2 = new Member();
        member2.setName("spring");
        //when
        memberService.join(member1);
        assertThrows(IllegalStateException.class,()-> memberService.join(member2));
//        try{
//            memberService.join(member2);
//            fail();
//        }catch (IllegalStateException e){
//            Assertions.assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다");
//        }

        //then
    }
```