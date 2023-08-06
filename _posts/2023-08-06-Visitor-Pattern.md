---
layout: post
title:  "비지터 패턴(Visitor Pattern)"
tags: [Disign Pattern, Behavioral Pattern]
date:   2023-08-04 11:30:00 +0900
toc:  true
---

## Visitor Pattern

비지터 패턴은 각 클래스의 데이터 구조로부터 처리 기능을 분리하여 별도의 클래스로 만들어내는 패턴이다. 즉 알고리즘들을 대상 객체들로부터 분리할 수 있도록 해주는 것이다.

즉, 데이터 구조와 연산을 분리하여 구조의 변경 없이 새로운 연산을 추가할 수 있게 되는 것이다. 새로운 연산을 추가할때는 방문자를 추가하면 된다.

## 비지터 패턴 구조

![Class Diagram](https://github.com/seeungmin/seeungmin.github.io/blob/main/DesignPatternDiagram/%EB%B9%84%EC%A7%80%ED%84%B0%20%ED%8C%A8%ED%84%B4.png?raw=true "Visitor Pattern Class Diagram")



## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

해당 코드는 비지터 패턴을 사용하여 대리점안에 있는 여러 통신사를 방문자들이 방문하는 것을 나타낸 것이다.


### Agency 인터페이스

{% highlight ruby %}
public interface Agency {
    public void accept(Visitor visitor);
}
{% endhighlight %}

visitor가 방문하는 대상에 대한 인터페이스이다.

<mark>accept()</mark>메서드를 통해서 visitor의 방문을 받아준다.


### Skt, Lg 클래스
{% highlight ruby %}
public class Skt implements Agency{
    private String name = "Skt";

    public String getName() {
        return name;
    }

    @Override
    public void accept(Visitor visitor) {
        visitor.visitSkt(this);
    }
}
{% endhighlight %}
{% highlight ruby %}
public class Lg implements Agency{
    private String name = "Lg";

    public String getName() {
        return name;
    }

    @Override
    public void accept(Visitor visitor) {
        visitor.visitLg(this);
    }
}
{% endhighlight %}

해당 클래스들은 <mark>Agency</mark>인터페이스를 구현하고 있는 구현 클래스입니다.

<mark>accept()</mark>메서드를 통하여 각각의 방문자가 해당 구역으로의 방문을 받고 있는 것을 알 수 있다.

### Visitor 인터페이스

{% highlight ruby %}
public interface Visitor {
    public void visitSkt(Skt skt);

    public void visitLg(Lg lg);
}
{% endhighlight %}

visitor가 방문하는 대상에 대한 인터페이스이다.

방문하는 곳에 따라서 <mark>visit()</mark>메서드를 정의한다.
- 현재는 코드의 가독성을 높이기 위해서 <mark>visitSkt()</mark>와 <mark>visitLg()</mark>로 메서드를 나타내었지만 보통 <mark>visit()</mark>으로 통일한다.


### VisitorA, B 클래스
{% highlight ruby %}
public class VisitorA implements Visitor{
    @Override
    public void visitSkt(Skt skt) {
        System.out.println(skt.getName() + " 통신사 입니다.");
    }

    @Override
    public void visitLg(Lg lg) {
        System.out.println(lg.getName() + " 통신사 입니다.");
    }
}
{% endhighlight %}
{% highlight ruby %}
public class VisitorB implements Visitor{
    @Override
    public void visitSkt(Skt skt) {
        System.out.println(skt.getName() + " 통신사 입니다.");
    }

    @Override
    public void visitLg(Lg lg) {
        System.out.println(lg.getName() + " 통신사 입니다.");
    }
}
{% endhighlight %}

<mark>Visitor</mark> 인터페이스를 구현한 클래스이다.

<mark>visit()</mark>메서드를 구현함으로써 각각 통신사를 방문하였다는 것을 출력하고 있습니다.

### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        Agency[] agencies = new Agency[]{
                new Lg(), new Skt()
        };

        Visitor a = new VisitorA();
        for(Agency agency : agencies)
            agency.accept(a);
        System.out.println("=========================");
        
        // 방문자 B 추가
        Visitor b = new VisitorB();
        for(Agency agency : agencies)
            agency.accept(b);
    }
}
    /** 출력 결과
        * Lg 통신사 입니다.
        * Skt 통신사 입니다.
        * =========================
        * Lg 통신사 입니다.
        * Skt 통신사 입니다.
        **/
{% endhighlight %}

해당 코드는 비지터 패턴을 사용하여 방문자가 여러개의 대리점을 방문한 것을 각각 보여주고 있습니다.

방문자에 따라서 각각의 연산을 수행하고 있는것을 알 수 있습니다.


## 사용 이유

이와 같은 비지터 패턴을 사용하는 이유는 여러가지 경우들이 있다.

그러한 여러가지 경우들을 지금부터 하나하나씩 살펴보도록 하곘다.

1. 자료구조와 자료구조를 처리하는 로직을 분리해야 하는 경우
2. 데이터 구조보다 알고리즘이 더 자주 바뀌는 경우
3. 복잡한 객체 구조의 모든 요소에 대해 작업을 수행해야 하는 경우 

위와 같은 경우에 많이 사용되고 있으며 개방 폐쇄 원칙, 단일 책임 원칙과 같은 객체지향원칙을 지킬 수 있고, 다양한 객체들과 작업하며 유용한 정보를 축적할 수 있는 장점을 가지고 있습니다.


## 단점
- 요소 계층구조에 클래스가 추가, 제거될 때마다 모든 Visitor를 수정해야한다.
- <mark>visit()</mark>메서드와 <mark>accept()</mark>메서드를 통하여 서로 의존하기 때문에 결합도가 높아진다.

## 결론

비지터 패턴은 간단하게 말해서 로직과 구조를 분리하는 패턴이라고 할 수 있다.

앞서 공부한 여러 디자인 패턴들은 블로그를 쓰면서 공부할 때 이해가 수월했지만 이 패턴은 조금 달랐다.

뭔가 어느 시점에 쓰는지 결합도가 높아지는 것을 왜 써야하는지 등 여러 의문이 남는 것 같다. 이는 추후에 추가 공부를 통하여 알아가야 할 것 같다.

우선 간단하게 앞서 말했듯 로직과 구조를 분리한다라고 이해하고 넘어가야할 것 같다.