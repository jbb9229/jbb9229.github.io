---
title: Spring Triangle - 2.AOP
date: 2020-04-20 17:45:20
photos:
 - https://media.vlpt.us/images/jbb9229/post/d44f9f4a-8839-494e-9f27-0bcfe859c708/1600px-Spring_Framework_logo.png
categories:
 - Spring
tags: 
 - Spring
 - Spring-AOP
--- 

# AOP (Aspect Oriented Programming)
**관점 지향 프로그래밍**

<br/>

흩어진 코드 
```java
Class A { 

    method a ( ) { 
        AAAA 
        오늘은 2020년 1월 4일 입니다. 
        BBBB 
    } 
    
    method b ( ) { 
        AAAA 
        지금 시간은 18시 45분 입니다. 
        BBBB 
    } 

} 

Class B { 

    method c ( ) { 
        AAAA 
        저는 배가 고픕니다. 
        BBBB 
    } 

} 
``` 
흩어져 있는 코드들은 AAAA 혹은 BBBB를 수정하려면 <br/>
AAAA와 BBBB가 있는 모든 클래스의 메서드를 찾아가서 변경해야 합니다. 

<br/>

모아 놓은 코드
```java
Class A { 

    method a ( ) { 
        오늘은 2020년 1월 4일 입니다. 
    } 
    
    method b ( ) { 
        지금 시간은 18시 45분 입니다. 
    } 
    
} 
    
Class B { 
    
    method c ( ) { 
        저는 배가 고픕니다. 
    } 
    
} 
    
Class AAAABBBB { 
    
    method aaaabbbb (JoinPoint point) { 
        AAAA 
        point.execute( ); 
        BBBB 
    } 

} 
``` 
하지만 코드를 모아서 메소드로 정의를 하면  
원래의 메소드에는 기존에 가지고 있던 기능을 가지고 

부가적으로 공통적으로 하던 기능인 AAAA와 BBBB를 모아서  
별도의 클래스에서 가지고 있는 것을 AOP라고 할 수 있습니다. 



## 다양한 AOP 구현 방법 

### 컴파일 

A.java ---(AOP)---> A.class 

AspectJ 가 제공하는 기능 

  

### 바이트코드 조작 

A.java --> A.class ---(AOP)---> Memory 

AspectJ 가 제공하는 기능 

  

### 프록시 패턴 

Spring AOP가 사용하는 방법이며, 

Design Pattern을 이용하여 AOP를 구현하는 방법입니다. 

  

#### 예제 - 기존 코드 건드리지 않고 새 기능 추가하기 

```java
public interface Payment { 

    void pay(int amount); 

} 

public class Cash implements Payment { 

    @Override 
    public void pay(int amount) { 
        System.out.println(amount + " 현금 결제"); 
    } 

} 

public class CashPerf implements Payment { 

    Payment cash = new Cash(); 

    @Override 
    public void pay(int amount) { 
        if (amount > 100) { 
            System.out.println(amount + " 신용 카드"); 
        } else { 
            cash.pay(amount); 
        } 
        cash.pay(amount); 
    } 

} 

public class Store { 

    Payment payment; 

    public Store(Payment payment) { 
        this.payment = payment; 
    } 

    public void buySomething(int amount) { 
        payment.pay(amount); 
    } 

} 

public class StoreTest { 

    @Test 
    public void testPay() { 
        Payment cashPerf = new CashPerf(); 
        Store store = new Store(cashPerf); 
        store.buySomething(101); 
    } 

} 
  
``` 

  

#### 예제 - Custom Annotation 을 사용한 Stopwatch 사용 

@LogExcuetionTime이라는 태그를 만들어서  
예제 코드의 OwnerController 여러 메서드에 작성 

Annotation은 주석같은 코멘트이므로 기능이 동작하지는 않습니다  
이 어노테이션을 읽어서 실행하는 Aspect를 생성해야 합니다. 

```java
@GetMapping("/owners/new") 
@LogExecutionTime 
public String initCreationForm(Map<String, Object> model) { 

    Owner owner = new Owner(); 
    model.put("owner", owner); 
    return VIEWS_OWNER_CREATE_OR_UPDATE_FORM; 

} 
``` 
  

실제 기능이 동작할 Aspect 생성 

```java
@Component 
@Aspect 
public class LogAspect { 

    Logger logger = LoggerFactory.getLogger(LogAspect.class); 

    // Around Annotation 을 사용하면 joinPoint라는 Parameter를 받을 수 있습니다. 
    // 여기서 JoinPoint 는 해당 Annotation 이 적용된 Target Method를 가지고 있습니다. 
    @Around("@annotation(LogExecutionTime)") 
    public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable { 
        StopWatch stopWatch = new StopWatch(); 
        stopWatch.start(); 

        // JoinPoint 의 해당 Method의 본 기능을 수행합니다. 
        Object proceed = joinPoint.proceed(); 
        stopWatch.stop(); 
        logger.info(stopWatch.prettyPrint()); 

        return proceed; 
    } 

} 
``` 
이러한 코드가 Aspect 이며, Spring이 제공해주는 Annotation 기반의 AOP입니다. 

안의 내부는 Proxy Pattern 기반으로 동작합니다. 

 


이렇게 작성한 상태에서 Application을 실행해서 해당 메소드를 동작시켜보면 

```
2020-01-04 22:22:31.659  INFO 999 --- [nio-8080-exec-8] o.s.samples.petclinic.owner.LogAspect    : StopWatch '': running time = 5072556 ns 

--------------------------------------------- 

ns         %     Task name 

--------------------------------------------- 

005072556  100%  
``` 
  

이렇게 위의 작성한 stopWatch를 볼 수 있습니다. 


  
#### 위 두가지 예제에 대한 해설 

OwnerController와 Cash라는 클래스는 Aspect를 적용할 Target이며, 

CashPerf와 LogAspect는 같은 역할을 합니다. 

다만 Annotation을 사용하면 Bean으로 등록할 때 무언가를 상속받고 

Cash 대신에 CashPerf를 주입받는 일들이 자동으로 이루어집니다. 

 


Spring AOP를 적용하는 방법은 여러가지가 있고, 

Annotation 없이도 적용을 하는 방법이 있습니다. 

Annotation을 작성하여 적용하는 방법이 좀 더 명시적입니다.

> 해당 포스트는 [백기선 님의 강의](https://www.inflearn.com/course/spring_revised_edition) 를 바탕으로 작성되었습니다. 