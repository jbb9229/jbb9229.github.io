---
title: Design Patterns - Observer Pattern
date: 2020-05-15 13:44:20
lastmod: 2020-07-28 09:14:20
sitemap:
    changefreq: weekly
    priority: 1.0
photos:
 - https://media.vlpt.us/images/jbb9229/post/12a0bea4-69d1-47a2-ab1d-ae428d11f16e/wordcloud.png
categories:
 - DesignPattenrs
tags: 
 - Strategy
 - ObserverPattern
 - DesignPatterns
 - 디자인패턴
 - 옵저버패턴
--- 

# 📚&nbsp;Observer Pattern (옵저버 패턴)<br/>

지난 시간 우리는 전략 패턴에 대해 알아보았습니다.<br/>
이번 시간엔 옵저버 패턴에 대해 알아볼 시간입니다.<br/>

[Code Example](https://github.com/jbb9229/DesignPatterns/tree/master/src/observer)

## 🤔&nbsp;Observer Pattern(옵저버 패턴)이란 무엇일까요?

> 📑&nbsp;[위키백과](https://ko.wikipedia.org/wiki/%EC%98%B5%EC%84%9C%EB%B2%84_%ED%8C%A8%ED%84%B4) <br/>
✍&nbsp;옵저버 패턴은 객체의 상태 변화를 관찰하는 관찰자들,<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;즉 옵저버들의 목록을 객체에 등록하여 상태 변화가 있을 때마다 메서드 등을 통해<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;객체가 직접 목록의 각 옵저버들에게 통지하도록 하는 디자인 패턴입니다.<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;주로 분산 이벤트 핸들링 시스템을 구현하는데 사용됩니다.<br/><br/>
🔍&nbsp;뭔가 중요한 일이 일어났을 때, 객체들한테 소식을 알려 줄 수 있는 패턴입니다.<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;객체 쪽에서는 계속해서 정보를 받을지 여부를 실행중에 결졍할 수 있습니다.<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;옵저버 패턴은 JDK에서 가장 많이 쓰이는 패턴 가운데 하나이기도 합니다.<br/>

## 📋&nbsp;예제를 보며 알아봅시다

### 📡&nbsp;기상 모니터링 애플리케이션 개요

이 시스템은 기상청과 WeatherData(기상청으로부터 오는 데이터를 추적하는 객체), <br/>
그리고 사용자에게 현재 기상 조건을 보여주는 디스플레이, 이렇게 세 요소로 이루어집니다.

![](https://images.velog.io/images/jbb9229/post/47d208c6-7638-49fa-a929-1db0b406a978/Design%20Patterns%20Images_0.png)

### 💻&nbsp;WeatherData 클래스를 열어봅시다
<br/>

![](https://images.velog.io/images/jbb9229/post/6be96c5e-ca85-4dd0-8718-2fa682ee45ab/Untitled%20Diagram%20(2).png)

<br/>
WeatherData 클래스에는 세 가지 측정값(온도, 습도, 기압)을 알아내기 위한 메소드가 있습니다.<br/>
새로운 기상 측정 데이터가 나올 때마다 WeatherData 클래스의 <br/>
measurementsChanged( ) 메소드가 호출됩니다.<br/>
기상 데이터를 사용하는 여러가지 디스플레이 항목을 구현해야 합니다.<br/>
또한, WeatherData에서 새로운 측정값이 들어올 때마다 디스플레이를 갱신해야 합니다.<br/>
추가로 시스템이 확장 가능해야 합니다.<br/>
다른 개발자들이 별도의 디스플레이 항목을 만들 수 있도록 해야 하고,<br/>
사용자들이 애플리케이션에서 마음대로 디스플레이 항목을 추가/제거 할 수 있도록 해야합니다.<br/>

### 📖&nbsp;옵저버 패턴에 대해 좀 더 자세히 알아봅시다.

> 🔍&nbsp;옵저버 패턴에서는 한 객체의 상태가 바뀌면 <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;그 객체에 의존하는 다른 객체들한테 연락이 가고,<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;자동으로 내용이 갱신되는 방식으로<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;일대다 (one-to-many) 의존성을 정의합니다.<br/>

![](https://images.velog.io/images/jbb9229/post/872e122d-fff5-4ea0-98a5-ff45b4b8fbd4/Untitled%20Diagram%20(4).png)

일대다 관계는 주제와 옵저버에 의해 정의됩니다. 옵저버는 주제에 의존하고,<br/>
주제의 상태가 바뀌면 옵저버한테 연락을 합니다.<br/>
연락 방법에 따라 옵저버에 있는 값이 새로운 값으로 갱신될 수 도 있습니다.<br/>

옵저버 패턴을 구현하는 방법에는 여러가지가 있지만,<br/>
대부분 주제(Subject) 인터페이스 와 옵저버(Observer) 인터페이스가<br/>
들어있는 클래스 디자인을 바탕으로 합니다.<br/>

### 🕎&nbsp;옵저버 패턴: 클래스 다이어그램

![](https://images.velog.io/images/jbb9229/post/d3822490-f750-498a-aa7b-6ff9ea2cc81d/Untitled%20Diagram%20(6).png)

1. 주제를 나타내는 Subject 인터페이스 입니다.<br/>
객체에서 옵저버로 등록하거나 옵저버 목록에서 탈퇴하고 싶을 때는<br/>
이 인터페이스에 있는 메소드를 사용합니다.<br/>

2. 각 주제마다 여러개의 옵저버가 있을 수 있습니다.<br/>

3. 옵저버가 될 가능성이 있는 객체에서는 반드시 Observer 인터페이스를 구현해야 합니다.<br/>
이 인터페이스에는 주제의 상태가 바뀌었을 때 호출되는 update( ) 메소드 밖에 없습니다.<br/>

4. Observer 인터페이스만 구현한다면 무엇이든 옵저버 클래스가 될 수 있습니다.<br/>
각 옵저버는 특정 주제  객체에 등록을 해서 연락을 받을 수 있습니다.<br/>

5. 주제 역할을 하는 구상 클래스에서는 항상 Subject 인터페이스를 구현해야 합니다.<br/>
주제 클래스에서는 등록 및 해지를 위한 메소드 외에 상태가 바뀔때마다 <br/>
모든 옵저버들에게 연락을 하기 위한 notifyObservers( ) 메소드도 구현해야 합니다.<br/>

6. 주제 클래스에는 상태를 설정하고 알아내기 위한 <br/>
Gettet/Setter 메소드가 들어있을 수 도 있습니다.<br/>

> - 🤔&nbsp;이거랑 일대다 관계랑 무슨 관계가 있는거죠?<br/><br/>
🔍&nbsp;옵저버 패턴에서 상태를 저장하고 지배하는 것은 주제 객체 입니다.<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;따라서 상태가 들어있는 객체는 하나만 있을 수 있습니다.<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;옵저버를 사용하긴 하지만 반드시 상태를 가지고 있어야 하는 것은 아닙니다.<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;따라서, 옵저버는 여러 개가 있을 수 있으며,<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;주제 객체에서 상태가 바뀌었다는 것을 알려주기를 기다리는,<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;주제에 의존적인 성질을 가지게 되죠.<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;그러므로 하나의 주제와 여러 개의 옵저버가 연관된,<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;일대다 (One-To-Many) 관계가 성립됩니다.<br/><br/>
>- 🤔&nbsp;의존성이 이 내용이랑 무슨 상관이 있어요?<br/><br/>
🔍&nbsp;데이터의 주인은 주제입니다.<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;옵저버는 데이터가 변경되었을 때 주제에서 갱신해주기를<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;기다리는 입장이기 때문에 의존성을 가진다고 할 수 있습니다.<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;이런 방법을 사용하면 여러 객체에서 동일한 데이터를 제어하도록<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;하는 것에 비해 더 깔끔한 객체지향 디자인을 만들 수 있습니다.
<br/>


### 🕎&nbsp;옵저버 패턴: 예제 클래스 다이어그램

![](https://images.velog.io/images/jbb9229/post/d2f53f0e-3615-4a2c-adbb-d410ae011159/Untitled%20Diagram%20(7).png)

위의 다이어그램과 실제 예제를 구현할 클래스 다이어그램을 비교해봅시다.<br/>
구현한 코드는 [Code Example](https://github.com/jbb9229/DesignPatterns/tree/master/src/observer) 깃허브 저장소에서 보실 수 있습니다.

## 💡&nbsp;느슨한 결합 (Loose Coupling)의 위력

> 두 객체가 느슨하게 결합되어 있다는 것은, 그 둘이 상호작용을 하긴 하지만<br/>
서로에 대해 잘 모른다는 것을 의미합니다.<br/><br/>
옵저버 패턴에서는 주제와 옵저버가 느슨하게 결합되어 있는 객체 디자인을 제공합니다.<br/>
그 이유에 대해 알아봅시다.<br/>

Subject(주제)가 Observer(옵저버)에 대해서 아는 것은 <br/>
**Observer가 특정 인터페이스(Observer 인터페이스)를 구현한다는 것 뿐입니다.** <br/>
Observer의 구상 클래스가 무엇인지, <br/>
Observer가 무엇을 하는지 등에 대해서는 알 필요가 없습니다.<br/>

**Observer는 언제든지 새로 추가할 수 있습니다.** <br/>
Subject는 Observer 인터페이스를 구현하는 객체의 목록에만 의존하기 때문에<br/>
언제든지 새로운 Observer를 추가할 수 있습니다. 사실 실행중에 한 Observer를 바꿔도  되고,<br/>
그렇게 해도 Subject 객체는 계속해서 데이터를 보낼 수 있습니다.<br/>
마찬가지로 Observer를 아무때나 제거해도 됩니다.<br/>

**새로운 형식의 Observer를 추가하려고 할 때도 Subject를 전혀 변경할 필요가 없습니다.** <br/>
Observer가 되어야 하는 새로운 구상 클래스가 생겼다고 가정해 봅시다.<br/>
이 때도 새로운 클래스 형식을 받아들일 수 있도록 Subject를 바꿔야 할 필요는 없습니다.<br/>
새로운 클래스에서 Observer 인터페이스를 구현하고, Observer로 등록하기만 하면 됩니다.<br/>
Subject 객체는 전혀 신경도 쓰지 않아도 됩니다.<br/>

**Subject와 Observer는 서로 독립적으로 재사용할 수 있습니다.** <br/>
Subject나 Observer를 다른 용도로 활용 할 일이 있다고 해도 손쉽게 재사용할 수 있습니다. <br/>
그 둘이 서로 단단하게 결합되어 있지 않기 때문입니다. <br/>

**Subject나 Observer가 바뀌더라도 서로한테 영향을 미치지 않습니다.** <br/>
둘이 서로 느슨하게 결합되어 있기 때문에 Subject 혹은 Observer 인터페이스를 구현한다는 <br/>
조건만 만족된다면 어떻게 바꿔도 문제가 생기는 일은 없습니다. <br/>

>✍&nbsp;디자인 패턴의 새로운 원칙을 알아봅시다. <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;서로 상호작용을 하는 객체 사이에서는 가능하면 <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;느슨하게 결합하는 디자인을 사용해야 한다. <br/>

**느슨하게 결합하는 디자인을 사용하면 변경 사항이 생겨도  <br/>
무난히 처리할 수 있는 유연한 객체지향 시스템을 구축할 수 있습니다. <br/>
객체 사이의 상호의존성을 최소화 할 수 있기 때문입니다.**

## 끝내며,
> 이렇게 옵저버 패턴의 1부 포스팅이 끝났습니다.<br/>
2부에서는 자바의 java.util.Observable과 <br/>
JDK에서의 Observer Pattern에 대해서 알아보겠습니다.