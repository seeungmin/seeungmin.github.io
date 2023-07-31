---
layout: post
title:  "옵저버 패턴(Observer Pattern)"
tags: [Disign Pattern, Behavioral Pattern]
date:   2023-07-31 19:30:00 +0900
toc:  true
---

## Observer Pattern

옵저버 패턴은 어떤 클래스에 변화가 일어났을 때, 이를 감지하고 다른 클래스에 통보해주는 패턴이다. 이는 주로 분산 이벤트 핸들링 시스템을 구현하는 데 사용된다. 구독/발행 모델로도 알려져 있다.

## 옵저버 패턴 구조

![Class Diagram](https://github.com/seeungmin/seeungmin.github.io/blob/main/DesignPatternDiagram/%EC%98%B5%EC%A0%80%EB%B2%84%20%ED%8C%A8%ED%84%B4.png?raw=true "Strategy Pattern Class Diagram")



## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

해당 코드는 옵저버 패턴을 사용하여 사람이 유튜버를 구독하는 것을 나타낸 코드입니다.


### Youtuber 인터페이스

{% highlight ruby %}
public interface Youtuber {
    public void send();
}
{% endhighlight %}

알림을 보내는 대상들에 대한 인터페이스입니다.

<mark>send</mark>메서드를 통해서 구독자들에게 알림을 보내줍니다.

### YoutuberA, B 클래스
{% highlight ruby %}
public class YoutuberA implements Youtuber{
    @Override
    public void send() {
        System.out.println("YoutuberA가 알림을 보냈습니다.");
    }

    @Override
    public String toString() {
        return "YoutuberA";
    }
}
{% endhighlight %}
{% highlight ruby %}
public class YoutuberB implements Youtuber{
    @Override
    public void send() {
        System.out.println("YoutuberA가 알림을 보냈습니다.");
    }

    @Override
    public String toString() {
        return "YoutuberB";
    }
}
{% endhighlight %}
<mark>Youtuber</mark>인터페이스를 구현하고 있는 객체를 구현한 클래스입니다.


### Subscriber 인터페이스
{% highlight ruby %}
public interface Subscriber {
    public void subscribe(Youtuber youtuber);
    public void unSubscribe(Youtuber youtuber);
    public void notifyYoutuber();
}
{% endhighlight %}

해당 인터페이스는 객체의 상태 변화를 관찰하는 관찰자들에 대한 인터페이스입니다.

각각 구독, 구독취소, 알림받기 메서드를 가지고 있습니다.

### Subscriber 인터페이스
{% highlight ruby %}
public class ConcreteSubscriber implements Subscriber{

    private List<Youtuber> youtubers = new ArrayList<>();

    @Override
    public void subscribe(Youtuber youtuber) {
        youtubers.add(youtuber);
        System.out.println(youtuber.toString() + " : 구독 완료");
    }

    @Override
    public void unSubscribe(Youtuber youtuber) {
        youtubers.remove(youtuber);
        System.out.println(youtuber.toString() + " : 구독 취소");
    }

    @Override
    public void notifyYoutuber() {
        for (Youtuber youtuber : youtubers)
            youtuber.send();
    }
}
{% endhighlight %}
해당 클래스는 관찰자에 대해서 구현한 클래스입니다.

관찰자는 관찰 대상을 <mark>List</mark>구조로 모아서 관리합니다.


### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        Subscriber subscriber = new ConcreteSubscriber();

        Youtuber youtuberA = new YoutuberA();
        Youtuber youtuberB = new YoutuberB();

        subscriber.subscribe(youtuberA);
        subscriber.subscribe(youtuberB);

        subscriber.notifyYoutuber();

        subscriber.unSubscribe(youtuberB);

        subscriber.notifyYoutuber();
    }
}
    /** 출력 결과
        * YoutuberA : 구독 완료
        * YoutuberB : 구독 완료
        * YoutuberA가 알림을 보냈습니다.
        * YoutuberA가 알림을 보냈습니다.
        * YoutuberB : 구독 취소
        * YoutuberA가 알림을 보냈습니다.
        **/
{% endhighlight %}

해당 코드는 옵저버 패턴을 사용하여 구독자들이 유튜버들에 대해서 구독 관리하고, 알림을 받아주는 것을 보여주는 코드입니다.

우선 구독자 객체와 유튜버 객체들을 생성합니다. 구독자는 여러 유튜버들을 구독하거나, 해당 유튜버들로부터 알림을 받을 수 있는 것을 보여줍니다.


## 사용 이유

이와 같은 옵저버 패턴을 사용하는 이유는 여러가지 경우들이 있다.

그러한 여러가지 경우들을 지금부터 하나하나씩 살펴보도록 하곘다.

1. 어플리케이션이 한정된 시간, 특정 경우에만 어떤 객체를 관찰해야하는 경우
2. 대상 객체의 변경이 있을 때마다, 다른 객체의 동작을 발생시켜야 하는 경우

위와 같은 경우에 사용이 되어, OCP 원칙을 지키거나 느슨한 결합을 유지하는 등의 장점을 가지고 있습니다.


## 단점
- 구독자의 입장에서는 알림을 받는 순서를 제어할 수 없어, 무작위 순서로 알림을 받습니다.
- 옵저버 객체를 등록 이후에 해지하지 않는다면 메모리 손실이 발생할 수도 있습니다.


## 결론

옵저버 패턴은 관찰자들이 관찰하고 있는 대상의 상태 변화가 있을 때마다 대상자는 직접 목록의 관찰자들에게 통지하고, 관찰자들은 알림을 받아서 조치하는 패턴이다.

이 패턴은 다른 패턴들과 다르게 일대다의 관계를 가지게 되기 때문에, 주로 분산 이벤트 핸들링 시스템을 구현하는 데 사용이 된다. 이는 일방적으로 유튜브나 뉴스 피드와 같은 것들을 생각하면 이해가 빠를 것이다.

우리의 일상생활에서 자주 보이는 관계이기 때문에 크게 어려울 것 업싱 공부할 수 있는 내용이라 생각한다.