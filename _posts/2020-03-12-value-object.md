---
title: VO - Value Object? DTO?
date: 2020-03-12 09:44:20
lastmod: 2020-07-21 09:14:20
sitemap:
changefreq: weekly
priority: 1.0
photos:
 - https://media.vlpt.us/images/jbb9229/post/acb7245d-e894-43ce-b639-7f80ce5d48d7/question-mark-2318030_1280.jpg
categories:
 - DDD
tags: 
 - VO
 - ValueObject
 - DTO
 - Entity
 - DateTransferObject
--- 

# 😃 들어가며
프로그래밍을 하며, VO(Value Object)라는 단어를 들어보셨을겁니다.<br/>
하지만 VO라는 단어가 실무에서 혼란스럽게 쓰이고 있습니다.<br/>
구글에 VO를 검색해보면 아직도 여러 글들이 Getter/Setter만 있는값을 실어나르는 객체를 <br/>
VO라 칭하고 있는데 이는 DTO로 칭하는게 혼란의 여지가 적습니다.<br/>
그렇다면, VO에 대해서 알아봅시다

## **Martin Fowler의 [Value-Object](https://martinfowler.com/bliki/ValueObject.html)**

>**Martin Fowler의 글에 의하면**<br/>
✍🏼&#160;I find it useful to think of two classes of object: <br/>
&#160;&#160;&#160;&#160;&#160;value objects and reference objects, depending on how I tell them apart<br/><br/>
🔍&#160;나는 구별하는 방법에 따라 값 객체와 참조 객체, <br/>
&#160;&#160;&#160;&#160;&#160;두 가지 클래스의 객체를 고려하는 것이 유용하다는 것을 알게되었다.<br/><br/>
✍🏼&#160;One source of terminological confusion is that around the turn of the century <br/>
&#160;&#160;&#160;&#160;&#160;some J2EE literature used "value object" for Data Transfer Object. <br/>
&#160;&#160;&#160;&#160;&#160;That usage has mostly disappeared by now, but you might run into it.<br/><br/>
🔍&#160;용어의 혼란이 온것은 세기가 바뀔 무렵 J2EE문헌에서 <br/>
&#160;&#160;&#160;&#160;&#160;Date Tranfer Object를 Value Object라고 사용했었기 때문이다.<br/>
&#160;&#160;&#160;&#160;&#160;그러한 사용은 지금으로서는 대부분 사라졌지만, 너는 여전히 우연히 접할 수 있을 것이다

그렇다면, Value-Object와 Reference-Object를 구분하는 방법을 알아봅시다.

# 🧐 VO (Value-Object)?
Martin Fowler가 언급한 VO의 개요는 다음과 같습니다.
>✍🏼&#160;When programming, I often find it's useful to represent things as a compound.<br/>
&#160;&#160;&#160;&#160;&#160;A 2D coordinate consists of an x value and y value. <br/>
&#160;&#160;&#160;&#160;&#160;An amount of money consists of a number and a currency. <br/>
&#160;&#160;&#160;&#160;&#160;A date range consists of start and end dates, <br/>
&#160;&#160;&#160;&#160;&#160;which themselves can be compounds of year, month, and day.<br/><br/>
🔍&#160;프로그래밍할 때, 사물을 복합물로 표현하는 것이 유용한 경우가 종종 있다.<br/>
&#160;&#160;&#160;&#160;&#160;예를 들어, 2차원 좌표는 x, y로 이루어져 있고,<br/>
&#160;&#160;&#160;&#160;&#160;돈이나 통화 같은 경우 숫자로 이루어져 있다.<br/>
&#160;&#160;&#160;&#160;&#160;날짜의 범위는 시작 날짜와 종료날짜로 구성될 수 있고,<br/>
&#160;&#160;&#160;&#160;&#160;연도와 월, 일의 복합물일 수  도 있다.

즉, Value-Object 란, 한개 혹은 그 이상의 속성들을 묶어서 특정 값을 나타내는 객체를 말합니다.<br/>
VO는 도메인 객체의 일종이며, 보통 기본키로 식별 값을 갖는 Entity와 구별해서 사용합니다.<br/>
그렇다면 VO는 어떤 조건들에 의해 구분해서 사용하는지 알아봅시다.<br/>

## 1. equals & hash code 메서드 재정의
일반적으로 타입이 같고, 내부의 속성 값도 같은 두 객체가 있다면, 두 객체는 같은 객체라고 취급할 것 입니다.

**_오히려 다르다고 하는게 더 이상하다고 생각 할 수 도 있습니다. <br/>
예를들면, 빨간색이 빨간색과 다르다고 하는것과 같습니다._**

하지만 실제로, 값이 같은 두 객체를 생성하고 객체를 비교해보면 둘은 서로 다른 객체로 비교됩니다.<br/>
좌표를 예시로 한 코드를 보면,

```java
    public class Point {
        private int x;
        private int y;

        public Point(int x, int y) {
            this.x = x;
            this.y = y;
        }
    }

    @Test
    public void isPointEquals() {
        Point point1 = new Point(1, 2);
        Point point2 = new Point(1, 2);

        // point1 != point2
        Assertions.assertThat(point1 == point2).isFalse();
    }
```

![](https://images.velog.io/images/jbb9229/post/9d829f51-78d4-48b6-8765-22dae8cf803b/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202020-07-10%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%201.48.45.png)

테스트 실행 시 같은 값을 가진 좌표 1과 좌표2는 다른 객체라는걸 알 수 있습니다.<br/>
분명 사람이 보기엔 같은 값을 가진 좌표니 같은 좌표인데 다른 좌표라고 합니다.<br/>
이러한 점은 우리가 현실과 같은 도메인을 설계하고 객체를 사용하는 점에 있어 혼란을 줍니다.

이 문제를 해결하기 위해서는 동일성 비교와 동등성 비교의 차이에 대해 알아야합니다.
>**동일성 비교**<br/>
👉&#160;동일성 비교는 해당 객체가 참조하고 있는 주소 값을 확인합니다.<br/>
&#160;&#160;&#160;&#160;&#160;&#160;하지만 현재 상태에서 Point1과 Point2가 참조하고 있는 메모리의 주소 값은 다르며,<br/>
&#160;&#160;&#160;&#160;&#160;&#160;이 주소 값은 임의로 바꿀 수 없습니다.<br/><br/>
**동등성 비교**<br/>
👉&#160;동등성 비교는 객체가 포함하고 있는 속성 값을 기준으로 객체를 비교합니다.<br/>
&#160;&#160;&#160;&#160;&#160;&#160;이러한 동등성 비교를 구현하기 위해서는 equals 메소드를 재정의함으로써 가능해집니다.<br/>
&#160;&#160;&#160;&#160;&#160;&#160;equals 메소드 재정의 시에는 어떠한 속성 값들을 기준으로 동등성을 비교할 것인지 정해야합니다.<br/>

위의 Point 객체의 equals 메소드와 hashCode 메소드를 재정의 해봅시다.

```java
...
        // equals & hashcode 재정의
        @Override
        public boolean equals(Object o) {
            if (this == o) {
                return true;
            }
            if (o == null || getClass() != o.getClass()) {
                return false;
            }
            final Point point = (Point) o;
            return x == point.x &&
                y == point.y;
        }

        @Override
        public int hashCode() {
            return Objects.hash(x, y);
        }
...
```

>🔑&#160;객체의 hash code는 객체를 식별할 하나의 정수 값을 가리키고, <br/>
&#160;&#160;&#160;&#160;&#160;&#160;재정의 하지 않으면 메모리 주소 값을 사용해서 해쉬 값을 만듭니다.<br/>
&#160;&#160;&#160;&#160;&#160;&#160;객체의 동등성을 비교할 때는 hashCode 메소드를 재정의하여,<br/>
&#160;&#160;&#160;&#160;&#160;&#160;특정 값을 기준으로 같은 해쉬 코드를 얻을 수 있고,<br/>
&#160;&#160;&#160;&#160;&#160;&#160;이는 해쉬 값을 사용하는 컬렉션 등에서 객체를 비교하는 용도로 사용됩니다.<br/>

재정의 한 equals 메소드와 hashCode 메소드를 Test 해봅시다.

```java
...
    @Test
    public void isPointAttributesEquals() {
        Point point1 = new Point(1, 2);
        Point point2 = new Point(1, 2);

        // point1 == point2
        Assertions.assertThat(point1.equals(point2)).isTrue();
        // point1.hashCode() == point2.hashCode()
        Assertions.assertThat(point1.hashCode() == point2.hashCode()).isTrue();
    }
...
```

![](https://images.velog.io/images/jbb9229/post/22033bc0-76bb-4802-96fe-dc947896cc5f/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202020-07-10%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%203.43.50.png)

이처럼 equals 메소드와 hashCode 메소드를 재정의하면,<br/>
VO를 사용할 때 속성값이 같은 객체는 같은 객체임을 보장하면서 VO를 사용할 수 있습니다.

## 2. 수정자(Setter)가 없는 불변(Immutable) 객체
>Entity와 같은 경우 별도의 식별 값을 가지고 있기 때문에 <br/>
내부 속성 값이 변경된다고 하더라도 같은 객체로 계속 인식하고 추적할 수 있습니다. <br/>
하지만 속성 값 자체가 식별 값인 VO는 속성 값이 바뀌게 되면 식별 값도 바뀌게 되어 추적이 불가능하고, <br/>
복사 될 때는 의도치 않은 객체들이 함께 변경되는 문제를 유발합니다. <br/>
따라서 VO는 값을 변경할 수 없는 불변 객체로 만들어야합니다.<br/>

예시를 통해 불변 객체로 만들어야하는 이유를 알아봅시다.

```java
    @Getter
    @Setter
    @NoArgsConstructor
    @EqualsAndHashCode
    @ToString
    public class Subsidy {
        private String country;
        private String category;
        private int familyCount;

    }

    @Test
    public void 보조금지급() {

        Subsidy 첫번째가구 = new Subsidy();
        첫번째가구.setCountry("한국");
        첫번째가구.setCategory("근로장려금");
        첫번째가구.setFamilyCount(1);

        System.out.println("첫번째가구 : " + 첫번째가구);
        // 첫번째가구 = (country=한국, category=근로장려금, familyCount=1)

        Subsidy 두번째가구 = 첫번째가구;

        System.out.println("두번째가구 : " + 두번째가구);
        // 두번째가구 = (country=한국, category=근로장려금, familyCount=1)

        두번째가구.setCategory("자녀장려금");
        두번째가구.setFamilyCount(4);

        System.out.println("첫번째가구 : " + 첫번째가구);
        System.out.println("두번째가구 : " + 두번째가구);
        // 첫번째가구 = (country=한국, category=자녀장려금, familyCount=4)
        // 두번째가구 = (country=한국, category=자녀장려금, familyCount=4)

    }
```
~~Lombok 라이브러리를 사용했습니다.~~

>⚙️&#160;국가의 보조금을 정하는 국가이름, 보조금 범주, 가족 수를 값으로 갖는 Subsidy 라는 VO를 만들었습니다.<br/>
&#160;&#160;&#160;&#160;&#160;두 가구의 보조금 신청이 들어왔고, 같은 1인가구 근로장려금을 신청하여 객체를 복사해서 사용했습니다.<br/>
&#160;&#160;&#160;&#160;&#160;어차피 같은 보조금이고 가족 수가 같으니 문제될 게 없다고 생각했습니다.<br/><br/>
🧨&#160;그런데 이 때, 두 번째 가구의 심사 결과가 4인가구의 자녀장려금 대상자로 나왔습니다.<br/>
&#160;&#160;&#160;&#160;&#160;분명 두 번째 가구의 내용만 변경했을 뿐인데, 출력된 결과를 살펴보면, <br/>
&#160;&#160;&#160;&#160;&#160;첫 번째 가구까지 4인 가구 자녀장려금으로 보조금이 변경되었습니다.<br/><br/>
🔍&#160;문제의 시작은 사용할 값이 같다고 해서 첫 번째 가구 값을 두 번째 가구에 그대로 복사한 곳 입니다. <br/>
&#160;&#160;&#160;&#160;&#160;현재 두 번째 가구는 첫 번째 가구 값을 복사한 것이 아닌 참조하고 있는 메모리 주소를 복사했기 때문에 <br/>
&#160;&#160;&#160;&#160;&#160;보조금 내용이 바뀌면 메모리 안에 저장된 실제 값이 변경됩니다. <br/>
&#160;&#160;&#160;&#160;&#160;당연히 같은 메모리를 참조하고 있는 첫 번째 가구의 내용도 변경된 값을 가리키게 되는 것 입니다.<br/>

이러한 치명적인 오류를 방지하기 위해서 VO는 한번 설정된 값이 변하지 않도록 해야 합니다.<br/>
즉, 값을 변경할 수 있는 수정자(Setter)가 없어야 합니다.

그럼 수정자 없이 어떻게 VO에 값을 설정할까요?<br/>
생성자를 통해 객체가 생성될 때, 값이 한 번만 할당되고 이후로는 변경되지 않도록 만들 수 있습니다.<br/>
생성자를 통해서 Subsidy 객체를 불변(Immutable)로 만들면 위와 같은 문제를 해결할 수 있습니다.

그렇다면 불변 객체를 만들어 봅시다.

```java
    @Getter
    @AllArgsConstructor
    @EqualsAndHashCode
    @ToString
    public class Subsidy {
        private String country;
        private String category;
        private int familyCount;

    }

    @Test
    public void 보조금지급() {

        Subsidy 첫번째가구 = new Subsidy("한국", "근로장려금", 1);
        System.out.println("첫번째 가구 : " + 첫번째가구);
        // 첫번째가구 = (country=한국, category=근로장려금, familyCount=1)

        Subsidy 두번째가구 = new Subsidy("한국", "근로장려금", 1);
        System.out.println("두번째 가구 : " + 두번째가구);
        // 두번째가구 = (country=한국, category=근로장려금, familyCount=1)

        // 심사로 인한 두번째가구 보조금 변경
        두번째가구 = new Subsidy("한국", "자녀장려금", 4);
        System.out.println("첫번째 가구 : " + 첫번째가구);
        System.out.println("두번째 가구 : " + 두번째가구);
        // 첫번째가구 = (country=한국, category=근로장려금, familyCount=1)
        // 두번째가구 = (country=한국, category=자녀장려금, familyCount=4)

    }
```

수정자(Setter)를 제거했으므로 심사로 인해 보조금이 변경된다면,<br/>
생성자를 통해 객체를 새로 생성하고 재할당 해주어야합니다.

이렇게 하면 속성 값 자체가 식별 값의 역할을 하는 VO의 정체성을 지키면서,<br/>
무엇보다도 의도치 않은 변경을 막을 수 있기 때문에 유지보수에도 효과적입니다.

## 💡 VO를 사용함으로 얻을 수 있는 이점
VO라는 객체를 사용하지 않고도 충분히 원시 타입의 값만 가지고 프로그래밍을 할 수 있습니다.<br/>
하지만 VO를 통해 도메인을 설계한다면, 객체가 생성될 때 해당 객체안에 제약사항을 추가할 수 있습니다.<br/>
또한 생성될 인스턴스가 정해져 있는 경우에는 미리 인스턴스를 생성해놓고 <br/>
캐싱하여 성능을 높이는 방법도 고려해볼 수 있습니다.

그뿐만 아니라, Entity의 원시 값들을 VO로 포장하면 <br/>
Entity가 지나치게 거대해지는 것을 막을 수 있어서, <br/>
테이블 관점이 아닌 객체 지향적인 관점으로 프로그래밍할 수 있습니다.

컬렉션도 VO의 역할을 한다면, 일급 컬렉션과 같은 불변객체로 만들어서 사용할 수 있습니다. <br/>

# 🤔 DTO?
그렇다면 DTO는 무엇일까요?<br/>
Martin Fowler의 글을 읽어보겠습니다.

## **Martin Fowler의 [Data-Transfer-Object](https://martinfowler.com/eaaCatalog/dataTransferObject.html)**

>✍🏼&#160;An object that carries data between processes in order to reduce the number of method calls.<br/><br/>
🔍&#160;메서드 호출 수를 줄이기 위해 프로세스 간에 데이터를 전송하는 개체<br/><br/>
✍🏼&#160;Although the main reason for using a Data Transfer Object is to batch up what would be multiple <br/>
&#160;&#160;&#160;&#160;&#160;remote calls into a single call, it's worth mentioning that another advantage is to encapsulate the <br/>
&#160;&#160;&#160;&#160;&#160;serialization mechanism for transferring data over the wire. By encapsulating the serialization <br/>
&#160;&#160;&#160;&#160;&#160;like this, the DTOs keep this logic out of the rest of the code and also provide a clear point to <br/>
&#160;&#160;&#160;&#160;&#160;change serialization should you wish.<br/><br/>
🔍&#160;데이터 전송 개체를 사용하는 주된 이유는 여러 원격 호출을 단일 호출로 일괄 처리하는 것이므로 <br/>
&#160;&#160;&#160;&#160;&#160;유선을 통해 데이터를 전송하기위한 직렬화 메커니즘을 캡슐화하는 것이 또 다른 장점입니다. <br/>
&#160;&#160;&#160;&#160;&#160;이와 같이 직렬화를 캡슐화함으로써 DTO는 이 논리를 나머지 코드에서 제외하고 원하는 경우 <br/>
&#160;&#160;&#160;&#160;&#160;직렬화를 변경할 수있는 명확한 지점을 제공합니다.<br/>

<br/>

# 📚 참고자료
- https://woowacourse.github.io/javable/2020-06-11/value-object
- https://d2.naver.com/news/3435170
- https://docs.microsoft.com/ko-kr/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/implement-value-objects
- https://martinfowler.com/bliki/ValueObject.html
- https://martinfowler.com/eaaCatalog/dataTransferObject.html
