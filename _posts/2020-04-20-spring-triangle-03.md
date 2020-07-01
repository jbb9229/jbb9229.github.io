---
title: Spring Triangle (스프링의 핵심 3요소) - 3. PSA
date: 2020-04-20 17:45:20
photos:
 - https://media.vlpt.us/images/jbb9229/post/d44f9f4a-8839-494e-9f27-0bcfe859c708/1600px-Spring_Framework_logo.png
categories:
 - Spring
tags: 
 - Spring
 - Spring-PSA
---

> 해당 포스트는 [백기선 님의 강의](https://www.inflearn.com/course/spring_revised_edition)를 바탕으로 작성되었습니다. 

# PSA (Portable Service Abstraction) 
**환경의 변화와 관계없이 일관된 방식의 기술 접근 환경을 제공하려는 추상화 구조** 


  

Spring 은 다양한 기술에 Service Abstraction을 제공하고 있는데 그 예시로는 

  

## Web MVC와 관련된 Service Abstraction 

@Controller라는 Annotation을 사용하면 요청을  
Mapping 할 수 있는 Controller 역할을 수행할 수 있습니다. 
해당 클래스 안에 @GetMapping, @PostMapping 등과 같은  
Annotation으로 요청을 Mapping 합니다

Mapping은 Mapping Annotation 안의 괄호에 해당하는 요청이 왔을 때  
그 요청을 해당 메소드가 처리하게끔 Mapping 한다는 뜻입니다.

Mapping 에는 여러가지 많은 속성을 가지고 있는데  
전부 요청과 관련된 속성으로 해당 속성에 해당할 때만  
해당 요청을 처리하는 Controller 역할을 수행할 수 있습니다. 

 


PSA에서 Portable은 기술 스택을 여러가지 기술 스택으로 바꿀 수 있다는 뜻인데 
PSA를 사용하면 코드를 일부만 변경하고 완전히 다른 기술로 동작이 가능합니다 

그 예시로는 기본적으로 Spring Boot 기반의 Application은 tomcat을 기반으로 동작하는 걸 알 수 있습니다. 

완전히 다른 기술 스택의 예시로는 Spring5에 적용된 WebFlux라는 기술이 있는데 

기존의 Spring Web MVC와 거의 흡사한 방법으로 코딩을 하지만 기술이 완전히 다릅니다. 

기존의 Servlet 처럼 하나의 요청당 하나의 Thread를 사용하는 구조가 아니라  
CPU 갯수만큼의 Thread를 유지하면서 Thread Pool을 사용하며 최소한의 Thread를 이용하여  
가용성을 높이는 프로그래밍 방법 중 하나로 해당 기술을 적용하면  
tomcat이 아닌 Netty 기반으로 실행할 수 있습니다  -> 100% 호환이 되지는 않습니다. 

 


원래 Netty의 서버를 동작하는 코드는 Tomcat의 방식과는 굉장히 다르지만 Spring이 제공해주는 WebMVC 추상화 계층을 사용하여 Netty를 사용할 수 있습니다. 

  


  

## Spring Transaction Manager 

**Transaction** : Database와 data를 주고받을 때  
A, B, C 쿼리가 있다면 A, B, C 쿼리가 모두 정상 동작했을 때 작동합니다. 
-> All or Notion : 동작한다면 모두가 동작하고 실패한다면 모두가 실패합니다 


  

기존의 jdbc 의 Transaction 처리는 AutoCommit을 해제하는 것부터  
시작해서 굉장히 Low Level의 처리가 필요합니다 

  

하지만 Spring 이 제공하는 추상화 계층에서는 @Transactional 이라는 Annotation을 사용하면 Annotation이 적용된 Method는 Transaction 처리가 되기 때문에  
Try, Catch, AutoCommit, execute 와 같은 여러 가지 처리가 없이 Transaction을 적용할 수 있습니다. 

  

Spring의 Transaction Manager 또한 PSA를 이용해 
위의 적용된 Service Abstraction을 여러가지 기술을 바꾸어 사용할 수 있습니다. 

기술의 예시로는 jdbc 와 같은 Datasource Transaction Manager를 사용할 수 도 있으며, 
Hibernate와 Jpa를 사용하는 경우에는 Jpa Transaction Manager를 사용할 수도 있습니다. 
이것 또한 위의 Service Abstraction의 코드는 변경하지 않고 가능합니다. 

 


## Spring Cache Manager 

Cache 또한 @Cacheable | @CacheEvict와 같은 Annotation을 이용하여 구현체는 신경 쓰지 않고 

javax.cache, ehcache 등의 기술을 바꾸어 사용할 수 있습니다. 

 


이처럼 Spring이 제공해주는 다양한 편의성 인터페이스 Service Abstraction 덕분에 

기술이 바뀌더라도 우리의 코드가 바뀔일은 거의 없습니다.