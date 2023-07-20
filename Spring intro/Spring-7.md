# [스프링 입문] Section 7. AOP

---

## AOP 가 필요한 상황

백엔드 구현에서의 관심사항은 두 가지 사항으로 나뉜다.

**공통 관심사항(cross-cutting concern)**과 **핵심 관심사항(core concern)**으로 나뉘는데, 회원 가입, 회원 조회와 같은 비즈니스 로직과 같은 경우가 핵심 관심사항이고, 이러한 기능 외에 회원 가입이나 회원 조회의 기능을 실행하는데 걸리는 시간을 측정하는 것과 같은 기능은 공통 관심 사항으로 분류된다.

이렇게 모든 메서드에 대한 호출 시간을 측정하고 싶을 때, 우리는 기존 모든 메서드의 시작과 끝에 시간을 측정하는 코드를 추가하여 이를 구현해야 한다.

회원 가입과 회원 조회의 기능의 호출 시간을 구하기 위해서는 회원 서비스에 다음과 같은 코드를 작성해야 한다.

```java
@Transactional
public class MemberService {
    private final MemberRepository memberRepository;

    public  MemberService(MemberRepository memberRepository){
        this.memberRepository = memberRepository;
    }

    /**
     * 회원가입
     */
    public Long join(Member member){
        
        long start = System.currentTimeMillis();
        
        try {
            validateDuplicateMember(member);
            memberRepository.save(member);
            return member.getId();
        } finally{
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println("join " + timeMs + "ms");
        }
    }

    /**
     * 전체 회원 조회
     */
    public List<Member> findMembers(){
        
        long start = System.currentTimeMillis();
        
        try {
            return memberRepository.findAll();
        } finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;
            System.out.println(timeMs);
        }
    }
}
```

메서드 시작 전 start로 시간을 받고, finally 에서 finish를 받아 두 시간의 차로 , 호출에 걸리는 시간을 측정 할 수 있다.

하지만 비즈니스 로직이 1000개 이상이라면 1000개가 넘는 모든 로직에 이러한 코드를 추가해야 하기 때문에 엄청난 시간이 소요가 되고, 또한 비즈니스 로직 과 코드가 섞여있기 때문에 유지보수에도 어려움을 갖는다.

이러한 문제점을 AOP로 해결 할 수 있다.

---

# AOP

**AOP**란 **Aspect Oriendted Programming**이란 의미로 위에서 말한것처럼 시간을 측정하는 로직과 같은 **공통 관심 사항(cross-cutting concern)**과 비지니스 로직인 **핵심 관심 사항(core concern)**으로 **분리하여 개발**하는 기술이다.

우리가 위에서 각 메서드의 시간을 측정하는 코드를 작성할 때 각각의 메서드에 코드를 입력하는 방식으로 개발하였다면, AOP를 사용하면 시간을 측정하는 코드를 따로 분리하여 개발할 수 있다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/c406de9f-f9dd-4eb9-b4f5-30e6f19915ae">

```java
@Component
@Aspect
public class TimeTraceAop {

    @Around("execution(* hello.hellospring..*(..))")
    public Object execute(ProceedingJoinPoint joinPoint) throws Throwable{

        long start = System.currentTimeMillis();

        System.out.println("START: " + joinPoint.toString());

        try {
            return joinPoint.proceed();
        }finally {
            long finish = System.currentTimeMillis();
            long timeMs = finish - start;

            System.out.println("END: " + joinPoint.toString() + " " + timeMs + "ms");
        }
    }
}
```

AOP를 사용하기 위해서는 @Aspect 어노테이션이 필요하고, AOP 또한 스프링 빈으로 등록되어야 하기 때문에 @Component 을 사용해 빈으로 등록하거나 Config와 같은 설정 파일에서 직접 @Bean으로 등록할 수 있다.

@Around 어노테이션을 사용해 개발자가 어느 로직에 어떤 부분에 AOP를 적용하고 싶은지 명시 할 수있다.

## AOP 적용 후

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/3d9294ca-1300-4374-82f2-68010ed42b14">

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/8bef34b4-cacd-4361-8463-deda8699cf3c">

@Around 어노테이션으로 스프링 컨테이너에 등록된 모든 로직들에 AOP가 적용 되었기 때문에 모두 프록시가 등록된 것을 확인 할 수 있다.