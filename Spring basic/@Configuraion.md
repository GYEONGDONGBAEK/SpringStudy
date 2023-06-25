# @Bean,@Configuration,@Component 에 대해

---

# **1. Spring Bean이란?**

### **Spring Bean**

Spring에서는 Spring의 DI Container에 의해 관리되는 POJO(Plain Old Java Object)를 Bean이라고 부르며, 이러한 Bean들은 Spring을 구성하는 핵심 요소이다. Spring의 Bean을 정리하면 아래와 같다.

- POJO(Plain Old Java Object)로써 Spring 애플리케이션을 구성하는 핵심 객체이다.
- Spring IoC 컨테이너(또는 DI 컨테이너)에 의해 생성 및 관리된다.
- class, id, scope, constructor-arg 등을 주요 속성으로 지닌다.

<img src="https://github.com/GYEONGDONGBAEK/SpringStudy/assets/122242439/c43020da-9fd1-4c6b-b6b9-a8943f264a0c">

### **Spring Bean 구성 요소**

- class: Bean으로 등록할 Java 클래스
- id: Bean의 고유 식별자
- scope: Bean을 생성하기 위한 방법(singleton, prototype 등)
- constructor-arg: Bean 생성 시 생성자에 전달할 파라미터
- property: Bean 생성 시 setter에 전달할 인수

---

# **2. Spring Bean 등록 방법(@Bean, @Configuration, @Component)**

### **@Bean 어노테이션과 @Configuration 어노테이션**

명시적으로 설정 클래스에서 @Bean 어노테이션을 사용해 수동으로 스프링 컨테이너에 빈을 등록하는 방법이 있다. 

설정 클래스는 다음과 같이 @Configuration 어노테이션을 클래스에 붙여주면 되는데, @Bean을 사용해 수동으로 빈을 등록해줄 때에는 메소드 이름으로 빈 이름이 결정된다. 그러므로 중복된 빈 이름이 존재하지 않도록 주의해야 한다.

스프링 컨테이너는 @Configuration이 붙어있는 클래스를 자동으로 빈으로 등록해두고, 해당 클래스를 파싱해서 @Bean이 있는 메소드를 찾아서 빈을 생성해준다.

@Bean을 사용하는 클래스에는 반드시 @Configuration 어노테이션을 활용하여 해당 클래스에서 Bean을 등록하고자 함을 명시해주어야 한다.

스프링 빈으로 등록된 다른 클래스 안에서 @Bean으로 직접 빈을 등록해주는 것도 동작은 한다. 하지만 @Configuration 안에서 @Bean을 사용해야 싱글톤을 보장받을 수 있으므로 @Bean 어노테이션은 반드시 @Configuration과 함께 사용해주어야 한다.

@Bean 어노테이션의 경우는 수동으로 빈을 직접 등록해줘야만 하는 상황인데, 주로 다음과 같을 때 사용한다.

- 개발자가 직접 제어가 불가능한 라이브러리를 활용할 때
- 애플리케이션 전범위적으로 사용되는 클래스를 등록할 때
- 다형성을 활용하여 여러 구현체를 등록해주어야 할 때

****@Component 어노테이션****

스프링은 컴포넌트 스캔(Component Scan)을 사용해 @Component 어노테이션이 있는 클래스들을 찾아서 자동으로 빈 등록을 해준다. 그래서 우리가 직접 개발한 클래스를 빈으로 편리하게 등록하고자 하는 경우에는 @Component 어노테이션을 활용하면 된다.

@Component를 갖는 어노테이션으로 @Controller, @Service, @Repository 등이 있으며 앞서 살펴봤던 @Configuration 역시 안에 @Component를 가지고 있다.

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Configuration {

    @AliasFor(annotation = Component.class)
    String value() default "";

    boolean proxyBeanMethods() default true;

}
```

@Configuration 안에 있는 @Component에 의해 설정 클래스 역시 자동으로 빈으로 등록이 되고, 그래서 @Bean이 있는 메소드를 통해 빈을 등록해줄 수 있었던 것이다.

스프링은 기본적으로 컴포넌트 스캔을 이용한 자동 빈 등록 방식을 권장한다. 또한 직접 개발한 클래스는 @Component를 통해 해당 클래스를 빈으로 등록함을 노출하는 것이 좋다. 왜냐하면 해당 클래스에 있는 @Component만 보면 해당 빈이 등록되도록 잘 설정되었는지를 바로 파악할 수 있기 때문이다. 

@Bean을 이용하면 빈으로 등록하는 작업도 번거로우며 빈이 등록되었는지 파악하는 과정도 번거로워질 수 있다. 그러므로 위에서 @Bean으로 해야하는 경우를 제외한 대부분의 경우에 자동 등록 방식을 사용하면 된다.

---

## ****@Configuration 안에 @Bean을 사용해야 하는 이유, proxyBeanMethods****

### **@Configuration에 적용되는 프록시 패턴**

@Configuration 어노테이션 안에는 @Component 어노테이션이 붙어있어서 @Configuration이 붙어있는 클래스 역시 스프링의 빈으로 등록이 된다. 그럼에도 불구하고 스프링이 @Configuration을 따로 만든 이유는 CGLib으로 프록시 패턴을 적용해 수동으로 등록하는 스프링 빈이 반드시 싱글톤으로 생성됨을 보장하기 위해서이다.

```java
public class People {

}
```

위의 클래스를 @Component를 이용해 자동으로 빈 등록을 한다면 스프링이 해당 클래스의 객체의 생성을 제어하게 되고(제어의 역전, IoC) 1개의 객체만 생성되도록 컨트롤할 수 있다.

하지만 @Bean 으로 등록 한다면 빈 등록 메소드를 여러번 호출할 수 있다.

```java
@Configuration
public class BeanConfiguration { 

    @Bean 
    public People people() {
        return new people(); 
    } 
    
    @Bean 
    public MyFirstBean myFirstBean() { 
        return new MyFirstBean(people()); 
    } 
    
    @Bean 
    public MySecondBean mySecondBean() { 
        return new MySecondBean(people()); 
    } 
}
```

스프링은 @Configuration이 있는 클래스를 객체로 생성할 때 CGLib 라이브러리를 사용해 프록시 패턴을 적용한다. 그래서 @Bean이 있는 메소드를 여러 번 호출하여도 항상 동일한 객체를 반환하여 싱글톤을 보장한다.

---

### ****proxyBeanMethods****

@Bean 메소드를 호출할 때 의도적으로 매번 다른 객체가 생성되기를 원할 수 있다. 그럴 때에는 @Configuration 어노테이션이 갖고 있는 proxyBeanMethods를 false로 주면 된다.

```java
@Configuration(proxyBeanMethods = false)
public class BeanConfiguration { 

    @Bean 
    public People people() {
        return new people(); 
    } 
    
    @Bean 
    public MyFirstBean myFirstBean() { 
        return new MyFirstBean(people()); 
    } 
    
    @Bean 
    public MySecondBean mySecondBean() { 
        return new MySecondBean(people()); 
    } 
}
```