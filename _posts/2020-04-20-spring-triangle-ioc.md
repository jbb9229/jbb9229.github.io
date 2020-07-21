---
title: Spring Triangle (스프링의 핵심 3요소) - 1. IoC Container
date: 2020-04-20 15:45:20
lastmod: 2020-07-21 09:14:20
sitemap:
    changefreq: always
    priority: 1.0
photos:
 - https://media.vlpt.us/images/jbb9229/post/d44f9f4a-8839-494e-9f27-0bcfe859c708/1600px-Spring_Framework_logo.png
categories:
 - Spring
tags: 
 - Spring
 - Spring-IoC
 - Spring-IoC-Container
--- 

# IoC (Inversion of Control) 
**제어의 역전**

일반적인 의존성 설정과 관리는 

```java
class Controller { 

    private Repository repo = new Repository(); 

} 
``` 
와 같은 형태로 의존성을 클래스가 만들어 사용합니다. <br/>
하지만 IoC 는 **Inversion of Control** (제어의 역전)이라고 하며, <br/> 
직접 의존성을 관리하는 것이 아닌 

```java
class  Controller { 
    private Repository repo; 
    
    public Controller(Repository repo) { 
        this.repo; 
    } 

} 
``` 

위의 코드와 같이 Controller 클래스는 Repository를 사용을 하지만  
직접 객체를 생성하지는 않고, 누군가가 Controller 밖에서 생성자를 통해 받아옵니다. 

그렇다면 의존성을 생성하는 일은 더 이상 Controller의 일이 아니므로 제어권이 역전되었다고 합니다. <br/>
이렇게 의존성을 주입하는것을 **DI (Dependency Injection)**라고 하는데 DI 또한 일종의 IoC 라고 할 수 있습니다. 

## IoC Container 
### **Application Context (Bean Factory)** 
>  
IoC 컨테이너는 주로 Application Cotext를 사용하는데 그 이유는 BeanFactory를 사용해도 되지만 <br/>
Application Context가 BeanFactory를 상속받고 있으며, 더 다양한 기능을 가지고 있기 때문 

IoC Container는 Bean을 만들고 그들 사이의 의존성을 엮어주며,
Container가 가지고 있는 Bean들을 제공해주는 역할을 합니다. 

여기서 주의할 점은 
기본적으로 DI (Dependency Injection)는 <br/>
IoC Container 에 생성되어 있는 Bean 들만 서로 의존성 주입이 가능하고 <br/>
IoC Container 에 있는 Bean들을 가져오는 방법을 제공합니다. <br/>
IoC Container 밖에 있는 객체에는 의존성 주입이 가능하지만 지양하는 편입니다. 


```java
@Autowired 
ApplicationContext applicationContext; 

public OwnerController(OwnerRepository clinicService, 
        VisitRepository visits,  
            ApplicationContext applicationContext) { 
    this.owners = clinicService; 
    this.visits = visits; 
    this.applicationContext = applicationContext; 
} 

@GetMapping("/bean") 
@ResponseBody 
public String bean() { 
    return "bean : " + applicationContext.getBean(OwnerRepository.class) + "owners : " + this.owners; 
} 
``` 
  
위와 같은 코드로 IoC Container에서 OwnerRepository를 가져온 것과 <br/>
ApplicationContext가 주입해준 owners 가 같은지 비교해 볼 수 있습니다. 


해당 코드를 동작시키면 아래와 같은 결과값을 볼 수 있습니다. 

>  
bean : org.springframework.data.jpa.repository.support.SimpleJpaRepository@29031da1 <br/> 
owners : org.springframework.data.jpa.repository.support.SimpleJpaRepository@29031da1 

**두 객체는 같은 해쉬 코드를 가지고 있으며 두 객체는 같은 객체**라고 볼 수 있습니다. 
> 이러한 형태를 Singleton Scope의 객체라고 합니다. 

Spring은 Bean을 생성할 때 기본적으로 모든 Bean을 Singleton으로 생성하여 관리하는데 <br/>
SIngleton Bean은 Spring 컨테이너에서 한 번 생성되며, 컨테이너가 사라질 때 같이 제거되며, <br/>
객체 하나를 Application 전반에서 계속 재사용됩니다. 

특히, Multi Thread 상황에서 Singleton Scope을 구현하는 일은 굉장히 번거롭고 조심스러운 일인데, <br/>
IoC Container를 사용하면 특별한 코드를 작성하지 않아도 편하게 Singleton Scope을 달성할 수 있으며, <br/>
IoC Container를 사용하는 이유 중 하나입니다. 

## Bean 
> 객체 중에서 Spring IoC Container가 관리하는 객체를 Bean이라고 합니다. 

간단하게 비교를 하자면 
```java
OwnerController ownerController = new OwnerController(); 
OwnerController bean = applicationContext.getBean(OwnerController.class); 
``` 
해당 코드에서 위의 임의로 생성한 ownerController는 Bean이 아닙니다. 

하지만 아래의 bean은 같은 ownerController 지만,  <br/>
이 객체는 ApplicationContext에서 가져온 객체이기 때문에 Bean입니다. 

이처럼 Bean은 ApplicationContext가 담고 있는 객체 즉, IoC Container에 담겨있는 객체를 말합니다. 


Bean을 등록하는 방법은 크게 2가지가 있습니다. 

### 1. Component Scanning 

**@Component** 

**@Repository** 

**@Service** 

**@Controller** 

**@Configuration** 등 


우리가 흔히 보는 여러 가지 Annotation은 사실상 @Component라는 meta Annotation을 사용한 Annotation으로 <br/>
Component Annotation 이라고 볼 수 있습니다. 

Spring IoC Container가 사용하는 여러 가지 Interface 를 LifeCycleCallback 이라고 부릅니다 <br/>
여러가지 LifeCycleCallback 중에는 Component라는 Annotation이 붙어있는 모든 클래스를 찾아서 <br/>
그 클래스의 인스턴스를 Bean으로 등록하는 Annotation 처리가 등록되어있습니다. 

Spring Boot Project 같은 경우 @SpringBootApplication 이러한 Annotation이 적용되어 있는데 <br/>
그 Annotation 안에 @ComponentScan이라는 Annotation 이 적용되어 있습니다. <br/>
이 Annotation 자체가 행위를 처리하는 것은 아니고 주석과 비슷한 것이지만 Component 범주를 알려줍니다. <br/>  
즉, @ComponentScan 어노테이션의 설정값에 따라 ComponentScan 이 동작합니다. 

그 외에도 Spring Data JPA가 제공하는 기능에 의해서 Repository를 Bean으로 등록할 수 있습니다. <br/>
방법은 특정 Annotation 없이도 특정 Interface를 상속받은 경우에 그 Interface를 상속받은 객체를 찾아서 <br/>
그 객체의 구현체를 IoC Container 내부적으로 만들어서 Bean으로 등록해주는 복잡한 과정을 처리합니다. 


해당 객체의 빈 생성 여부를 Test 코드를 작성하여 알아볼 수 있습니다. 

```java
@RunWith(SpringRunner.class) 
@SpringBootTest 
public class SampleControllerTest { 

@Autowired 
ApplicationContext applicationContext; 

@Test 
public void testDI() { 
    SampleController bean =  
             applicationContext.getBean(SampleController.class); 
    assertThat(bean).isNotNull(); 
} 
     
} 
``` 
 


### 2. 직접 XML 또는 Java 설정 파일에 등록  
(최근 추세는 Java 설정 파일을 더 많이 사용합니다.) 

 


**Java 설정 파일 예시** 

```java
@Configuration 
public class SampleConfig { 

    @Bean 
    public SampleController sampleController() { 
        return new SampleController(); 
    } 

} 
``` 
설정 파일을 이용하여 직접 Bean을 등록하면 @Controller Annotation을 생성하지 않고도 Bean으로 등록하여 <br/> 
위의 Test 코드 실행 시 성공하는 모습을 볼 수 있습니다. 


## DI (Dependency Injection) 

> Spring이 제공하는 DI 의존성 주입은 다양한 방법이 있습니다. 

### @Autowired 

Autowired Annotation은 Field, Setter, Constructor 등 다양한 곳에서 사용할 수 있습니다. 

예제 코드를 보면 

```java
class  Controller { 

    private Repository repo; 

    public Controller(Repository repo) { 
        this.repo; 
    } 

} 
``` 
이처럼 생성자를 사용해서 주입을 받고 있는데 

```java
class  Controller { 
    private Repository repo; 

    @Autowired 
    public Controller(Repository repo) { 
        this.repo; 
    } 

} 
``` 
원래는 이처럼 @Autowired Annotation을 사용할 수 있습니다. <br/>
하지만 Spring version 4.3부터 어떠한 클래스에 생성자가 하나이고, <br/> 
그 생성자로 주입받는 reference 변수들이 Bean으로 등록되어 있다면  <br/>
그 Bean을 자동으로 주입해주도록 기능이 추가되었습니다. <br/>
-> @Autowired Annotation을 생략 가능합니다 


생성자가 아닌 Field에 주입받고 싶다면 

```java
class  Controller { 

    @Autowired 
    private Repository repo; 

} 
``` 
이러한 방식으로 작성이 가능합니다 

  


Setter로 주입한다면 

```java
class Controller { 

    private Repository repo; 
    
    @Autowired 
    public void setRepo(Repository repo) { 
        this.repo = repo; 
    } 

} 
``` 
Spring IoC Container가 해당 인스턴스를 생성하고 Setter를 통해서  <br/>
IoC Container의 해당 Bean의 의존성을 주입합니다. 

만약 @Autowired Annotation이 작성된 객체가 Bean으로 등록이 되어있지 않다면 Error가 발생합니다.<br/>
 (Error message : No qualifying bean) 

> 이 중 Spring Framework Reference에서 권장하는 방법은 생성자를 통한 주입입니다. 

생성자를 사용하는 주입이 좋은 이유는 필수적으로 사용해야 하는  <br/>
Reference 없이는 해당 인스턴스를 생성하지 못하도록 강제할 수 있습니다. 

Field Injection이나 Setter Injection 은 Reference 없이도  <br/>
인스턴스가 생성이 가능하기 때문에 문제가 발생할 수 있습니다. 

하지만 만약 순환 참조가 발생한다면 ( A -> B 참조 ,  B -> A 참조 )  <br/>
이러한 경우는 두 인스턴스를 둘 다 생성할 수 없기 때문에 

생성자 대신에 필드나 세터에 주입하는 방식으로 인스턴스를 만든 후  <br/>
그다음 서로의 인스턴스를 주입하는 방식으로 해당 문제를 해결할 수 있습니다. 

**순환 참조가 안 생기도록 작성하는 것이 Best** 

 


생성자로 주입 시 예제 코드에 Final이 붙은 이유 

```java
@Controller 
class OwnerController { 

    private static final String VIEWS_OWNER_CREATE_OR_UPDATE_FORM =  
         "owners/createOrUpdateOwnerForm"; 
    
    // final을 굳이 붙이지 않아도 상관은 없지만 한번 주입받은  
    // Reference가 다른 Reference로 바뀌지 않도록 보장하기 위함 
    private final OwnerRepository owners; 
    
    private VisitRepository visits; 
    
    public OwnerController(OwnerRepository clinicService, 
         VisitRepository visits) { 
        this.owners = clinicService; 
        this.visits = visits; 
    } 
} 
```

> 해당 포스트는 [백기선 님의 강의](https://www.inflearn.com/course/spring_revised_edition) 를 바탕으로 작성되었습니다.