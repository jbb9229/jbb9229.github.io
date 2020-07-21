---
title: Design Patterns - Strategy Pattern
date: 2020-04-22 13:44:20
lastmod: 2020-07-21 09:14:20
sitemap:
changefreq: always
priority: 1.0
photos:
 - https://media.vlpt.us/images/jbb9229/post/12a0bea4-69d1-47a2-ab1d-ae428d11f16e/wordcloud.png
categories:
 - DesignPattenrs
tags: 
 - Strategy
 - StrategyPattern
 - DesignPatterns
 - 디자인패턴
 - 전략패턴
--- 
# 📚&nbsp;Design Pattern?

## 🤔&nbsp;Design Patterns이란 무엇일까요?

>✍🏼&nbsp; 디자인 패턴의 정의는 소프트웨어 공학 소프트웨어 디자인의 특정 문맥에서 <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;공통적으로 발생하는 문제에 대해 재사용 가능한 해결책입니다. <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;소스나 기계 코드로 바로 전환될 수 있는 완성된 디자인은 아니며, <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;다른 상황에 맞게 사용될 수 있는 문제들을 해결하는데에 쓰이는 서술이나 템플릿입니다. <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;디자인 패턴은 프로그래머가 어플리케이션이나 시스템을 디자인할 때 <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;공통된 문제들을 해결하는데에 쓰이는 형식화 된 가장 좋은 관행입니다.<br/><br/>
😨&nbsp;정의를 읽어보면 사실 그 뜻이 정확하게 이해가 되지 않습니다.<br/><br/>
🔍&nbsp;좀 더 쉽게 풀어보자면, 우리가 개발을 하면서 접하는 문제들 중 <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;대다수는 누군가 이미 같은 문제를 겪었을 확률이 높고, <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;디자인 패턴은 많은 이들이 접하는 문제를 뛰어난 많은 개발자들이 제시한 해결책이라고 볼 수 있습니다. <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;지금부터 여러가지 디자인 패턴과 해당 패턴의 예시 그리고 왜 해당 패턴을 사용하는지 알아봅시다.<br/>
<br/>

# 📚&nbsp;Strategy Pattern (전략 패턴)

우리가 처음 알아볼 패턴은 **Strategy Pattern(전략 패턴)** 입니다.
<br/>

[Code Example](https://github.com/jbb9229/DesignPatterns/tree/master/src/strategy)

## 🤔&nbsp;Strategy Pattern(전략 패턴)이란 무엇일까요?
>✍🏼&nbsp; 전략 패턴은 알고리즘을 정의하고 각각을 캡슐화하여 교환해서 사용할 수 있도록 만듭니다. <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;전략 패턴을 활용하면 알고리즘을 사용하는 클라이언트와는 독립적으로 알고리즘을 변경할 수 있습니다.<br/><br/>
🔍&nbsp;어떤 객체의 행위를 각각 캡슐화하여 클라이언트가 각자의 전략에 따라 쉽게 행위를 바꿀 수 있도록 해줍니다.<br/>
<br/>

## 📋&nbsp;예제를 보며 알아봅시다

### 🕹&nbsp;간단한 SimuDuck
![](https://images.velog.io/images/jbb9229/post/63898461-082d-4e8a-ac85-b065cce2d990/duck1%20(2).png)

Duck 클래스를 만들어 꽥꽥 소리를 내는 quack( ) 메소드와 수영을 하는 swim( ) 메소드, <br/>
오리가 날도록 fly( ) 메소드, 오리들이 모양마다 다르기 때문에 display( ) 메소드를 추상메소드로 작성하였습니다.<br/>
그리고 Duck 클래스를 상속받은 MallardDuck 클래스와 RedheadDuck 클래스를 만들고<br/>
Duck 클래스를 상속받은 두 클래스에 display( ) 메소드를 별도로 구현했습니다.<br/>

### 💣&nbsp;그런데 문제가 발생했습니다.

![](https://images.velog.io/images/jbb9229/post/98b30c7e-b76c-4115-b542-2e0adc91173e/duck1%20(3).png)
<br/>

Duck의 모든 서브클래스가 날 수 있는것은 아니라는 점을 깜빡하고 넘어갔습니다.<br/>
Duck이라는 수퍼클래스에 fly( ) 메소드가 추가되면서 러버덕과 같은 일부 서브클래스에는<br/>
적합하지 않은 행위가 전부 추가된 것이죠.<br/>
SimuDuck 프로그램에 러버덕이 날아다니는 불상사가 생기고 말았습니다.<br/>

quack( ) 메소드와 같이 러버덕의 fly( ) 메소드를 오버라이드 할 생각을 했습니다.<br/>
하지만 러버덕 외에도 나무오리와 같이 소리도 못내고, 날지도 못하는 오리가 있었습니다.<br/>

>🔍&nbsp;&nbsp;상속을 잘 활용하여 코드를 재사용 할 수 있다고 생각했지만,<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;실제 코드를 정비하는데는 별 도움이 안되는 상황이 발생했습니다.<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;상속은 올바른 해결책이 아니였습니다.


### ❗️&nbsp;그렇다면 인터페이스는 어떨까요?
![](https://images.velog.io/images/jbb9229/post/a43ade2c-850a-436f-90bd-308a29827632/duck1%20(4).png)

서브클래스에서 인터페이스 Quackable, Flyable을 구현하도록 함으로써 일부 문제점은 해결할 수 있지만,<br/>
그렇게 하면 그러한 행위에 대한 코드 재사용을 전혀 기대할 수 없게 되므로,<br/>
코드 관리 면에 있어서 또 다른 커다란 문제점이 생기게 됩니다.<br/>
물론, 날 수 있는 오리 중에서도 날아다니는 방식이 서로 다를 수도 있습니다.

### 🔍&nbsp;문제를 명확하게 파악하기

1. 상속을 사용하는 것은 서브클래스마다 오리의 행위가 바뀔 수 있는데,<br/>
모든 서브클래스에서 한 행위를 사용하도록 하는 것이 올바르지 못했습니다.

2. 인터페이스를 사용하는 방법은 괜찮아 보일 수 있지만,<br/>
자바 인터페이스에는 구현된 코드가 전혀 들어가지 않기 때문에 <br/>
코드를 재사용 할 수 없다는 문제점이 있습니다.<br/>
한가지 행위를 바꿀 때마다 모든 서브클래스들을 찾아서 일일이 고쳐야 하고,<br/>
그 과정에 새로운 버그가 생길 가능성도 있습니다.

이제 디자인 패턴이 등장할 때가 아닐까요?

먼저, Duck 클래스는 fly( )와 quack( ) 메소드를 제외하면 잘 작동하고 있으며, <br/>
나머지 부분은 자주 달라지거나 바뀌지 않습니다.<br/>

>✍🏼&nbsp; **디자인 패턴의 첫번째 원칙을 알아봅시다.** <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;애플리케이션에서 달라지는 부분을 찾아내고, 달라지지 않는 부분으로부터 분리시킨다.
<br/>

그렇다면 **변화하는 부분과 그대로 있는 부분** 을 분리해봅시다.<br/>
그 둘을 분리하려면 Duck 클래스와는 별개로 현재 변화하는 부분인 <br/>
fly( )와, quack( )에 대한 두 개의 클래스 집합을 만들어야 합니다.<br/>

### 📝&nbsp;행위 디자인

>✍🏼&nbsp; **디자인 패턴의 두번째 원칙을 알아봅시다.** <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;구현이 아닌 인터페이스에 맞춰서 프로그래밍 한다.

각 행위는 인터페이스로 표현하고 행위를 구현할 때 이런 인터페이스를 구현하도록 하겠습니다.

나는 행위 fly( )와 꽥꽥거리는 행위 quack( )은 이제 Duck 클래스에서 구현하지 않습니다.<br/>
대신 특정 행위만을 목적으로 하는 클래스의 집합을 만들도록 하겠습니다.<br/>
Behavior 인터페이스는 Duck 클래스가 아닌, fly( ) quack( ) 을 클래스에서 구현합니다.

아까 사용했던 두 방법에서는 특정 구현에 의존했습니다.<br/>
특정 구현을 써야만 했기 때문에 행위를 변경할 여지가 없었죠.

![](https://images.velog.io/images/jbb9229/post/bb83216c-e60e-453e-8d08-0ad665e662f0/duck2.png)

새로운 디자인을 사용하면 Duck의 서브클래스에서는 인터페이스로 표현되는 행위를 사용하게 됩니다.<br/>
따라서 행위를 실제로 구현한 것(FlyBehavior의 구현체)은 Duck 서브클래스에 국한되지 않습니다.

> **"인터페이스에 맞춰서 프로그래밍 한다"** 는 것은 **"상위 형식에 맞춰서 프로그래밍 한다"** 는 것을 뜻합니다.<br/><br/>
이 말에는 인터페이스라는 단어가 중의적으로 쓰였습니다.<br/>
인터페이스는 자바의 인터페이스라는 구조를 지칭하는 용도로 쓰이기도 하고,<br/>
인터페이스라는 개념을 지칭하는 용도로도 쓰입니다.<br/><br/>
인터페이스에 맞춰서 프로그래밍하라고 했다고 <br/>
반드시 자바의 인터페이스를 사용하라는 것은 아닙니다.<br/><br/>
가장 핵심적인 것은 실제 실행시에 쓰이는 객체가 코드에 의해서 고정되지 않도록,<br/>
어떤 상위 형식에 맞춰서 프로그래밍함으로써 **다형성**을 활용해야 한다는 것입니다.<br/>

### 🔧&nbsp;행위을 구현하는 방법
![](https://images.velog.io/images/jbb9229/post/e01db390-4277-4362-8397-ca3e3251858c/duck2%20(1).png)

FlyBehavior, QuackBehavior라는 두 인터페이스를 사용합니다.<br/>
그리고 구체적인 행위를 구현한 클래스들이 있습니다.

>이렇게 디자인하면 다른 형식의 객체에서도 나는 행위와 꽥꽥거리는 행위를 재사용할 수 있습니다.<br/>
또한, 기존의 행위 클래스를 수정하거나 Duck 클래스를 전혀 건드리지 않고도 새로운 행위를 추가 할 수 있습니다.

### 🛒&nbsp;행위 통합하기
행위를 통합하는데 가장 중요한 점은 Duck에서 나는 행위와 꽥꽥 소리를 내는 행위를<br/>
Duck 클래스 또는 서브클래스에서 구현하지 않고, 다른 클래스에 위임한다는 것입니다.

1. 우선, Duck 클래스에 flyBehavior, quackBehavior 두 개의 인터페이스 형식 인스턴스 변수를 추가합니다.<br/>
각 오리 객체는 실행 시 이 변수에 특정 행동 형식에 대한 레퍼런스를 다형적으로 설정합니다.<br/><br/>
그리고 나는 행동과 꽥꽥거리는 행동은 FlyBehavior, QuackBehavior 인터페이스로 옮겨놨기 때문에<br/>
Duck클래스 및 모든 서브클래스에서 fly( )와 quack( ) 메소드를 제거해야 합니다.<br/>
fly( )와 quack( ) 대신 performFly( )와 performQuack( ) 메소드를 집어넣습니다.<br/>

2. performFly( )와 performQuack( ) 메소드를 구현해 봅시다.

```java
    public class Duck {
        
        QuackBehavior quackBehavior;
        FlyBehavior flyBehavior;
        ...
        // 기타 코드
        ...
        public void performQuack() {
            quackBehavior.quack();
        }

        public void performFly() {
            flyBehavior.fly();
        }
        
    }    
```

3. 이제 인스턴스 변수를 설정하는 방법에 대해 생각해 볼 차례입니다.<br/>
(예시로 MallardDuck 클래스를 한 번 살펴봅시다.)

```java
    public class MallardDuck extends Duck {
    	
        public MallardDuck() {
       	    // MallardDuck이 수행할 행위 클래스의 의존성을 추가하면
            // 해당 행동을 했을때 perform 메서드가 호출됐을 때,
            // 각 클래스에 위임됩니다.
            quackBehavior = new Quack();
            flyBehavior = new FlyWithWings();
        }
        
        public void display() {
        	System.out.println("Mallard Duck");
        }
        
    }
```


### 📌&nbsp;동적으로 행동을 지정하기

지금 형식의 프로그래밍은 인스턴스 변수를 유연한 방법으로 초기화 하는 방법을 사용하여, 생성자를 사용하여 행위를 쉽게 변경할 수 있습니다. <br/>
하지만 오리의 행위를 생성자에서 인스턴스를 만드는 방법이 아닌 즉석으로 바꾸는 setter 메소드를 만들어봐야겠죠?

1. Duck 클래스에 setter 메소드 두 개를 새로 추가합니다.

```java
    public class Duck {
    	
        ...
        
        public void setFlyBehavior(FlyBehavior fb) {
       	    flyBehavior = fb;
        }
        
        public void setQuackBehavior(QuackBehavior qb) {
       	    quackBehavior = qb;
        }
        
        ...
        
    }
```

2. 아까 전 MallardDuck이 날개를 다쳐 못날게 되었다고 가정하고 setter를 사용해봅시다.

```java
    ...
    
        Duck mallard = new MallardDuck();
        
        // MallardDuck을 처음 생성하게되면 
        // 생성자에 설정된 FlyWithWings 인스턴스의 fly()가 실행됩니다.
        mallard.performFly();
        
        // MallardDuck이 다쳤다고 가정하고 setter 메소드를 호출합니다.
        mallard.setFlyBehavior(new FlyNoWay());
        
        // setter메소드에 의해 mallard는 더이상 날 수 없습니다.
        mallard.performFly();
    
    ...
```

### 🌎&nbsp;캡슐화된 행위를 큰 그림으로 봅시다.

![](https://images.velog.io/images/jbb9229/post/05f30dd7-e060-4c8e-8bd2-1de28d1d759f/duck2%20(2).png)

>✍🏼&nbsp; **디자인 패턴의 세번째 원칙을 알아봅시다.** <br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;상속보다는 조합을 활용한다.<br/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Composition over inheritance](https://en.wikipedia.org/wiki/Composition_over_inheritance)
<br/>

이 말은 **Effective Java** 에서도 나오는 말입니다.

"A는 B이다"보다 "A에는 B가 있다"가 나을 수 있습니다. A와 B의 관계에 대해 생각해 봅시다.<br/>
각 오리에는 FlyBehavior, QuackBehavior가 있으며, 각각의 행위를 위임 받습니다.<br/>

두 클래스를 이런 식으로 합치는 것을 조합(composition)을 이용한다 합니다.<br/>
오리 클래스에서는 행동을 상속받는 대신, 올바른 행동 객체로 조합됨으로써 행동을 부여받게 됩니다.<br/>

위의 SimuDuck처럼 조합을 이용하여 시스템을 만들면 유연성을 크게 향상시킬 수 있습니다.<br/>

# 📚&nbsp;참고자료
[HeadFirst DesignPatterns](http://www.yes24.com/Product/Goods/1778966)
