---
layout: post
title:  "전략 패턴(Strategy Pattern)"
tags: [Disign Pattern, Behavioral Pattern]
date:   2023-07-31 15:30:00 +0900
toc:  true
---

## Strategy Pattern

전략 패턴은 객체들의 행위 각각에 대해서 전략 클래스를 생성하고, 유사한 행위들을 캡슐화 하는 인터페이스를 정의하여 객체의 행위를 동적으로 바꾸어 행위를 유연하게 확장하는 방법입니다.

## 전략 패턴 구조

![Class Diagram](https://github.com/seeungmin/seeungmin.github.io/blob/main/DesignPatternDiagram/%EC%A0%84%EB%9E%B5%20%ED%8C%A8%ED%84%B4.png?raw=true "Strategy Pattern Class Diagram")



## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

해당 코드는 전략 패턴을 사용하여 학교가는 방법에 대해서 동적으로 처리하게한 코드입니다.


### Strategy 인터페이스

{% highlight ruby %}
public interface Strategy {
    public void goSchool();
}
{% endhighlight %}

행위를 동적으로 처리하기 위한 인터페이스이다.

학교가는 방법에 대한 메서드가 존재한다.

### Bus, Bike, WalkStrategy 클래스
{% highlight ruby %}
public class BikeStrategy implements Strategy{
    @Override
    public void goSchool() {
        System.out.println("자전거를 타고 갑니다.");
    }
}
{% endhighlight %}
{% highlight ruby %}
public class BusStrategy implements Strategy{

    @Override
    public void goSchool() {
        System.out.println("버스를 타고 갑니다.");
    }
}
{% endhighlight %}{% highlight ruby %}
public class WalkStrategy implements Strategy{

    @Override
    public void goSchool() {
        System.out.println("걸어 갑니다.");
    }
}
{% endhighlight %}

<mark>Strategy</mark>인터페이스를 구현하고 있는 행위들을 구현한 클래스입니다.


### Context 클래스
{% highlight ruby %}
public class Context {
    private Strategy strategy;

    public void setStrategy(Strategy strategy){
        this.strategy = strategy;
    }

    public void goSchool(){
        this.strategy.goSchool();
    }
}
{% endhighlight %}

전략을 등록하여 사용할 수 있도록 해주는 클래스이다.

### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        Context context = new Context();

        context.setStrategy(new WalkStrategy());
        context.goSchool();

        context.setStrategy(new BikeStrategy());
        context.goSchool();

        context.setStrategy(new BusStrategy());
        context.goSchool();
    }
}
    /** 출력 결과
        * 걸어 갑니다.
        * 자전거를 타고 갑니다.
        * 버스를 타고 갑니다.
        **/
{% endhighlight %}

해당 코드는 전략 패턴을 사용하여 학교 가는 방법을 정할 수 있는 간단한 코드입니다.

전략을 결정해주는 <mark>Context</mark>클래스를 선언한다. 그 후 <mark>setStrategy</mark>메서드를 통해서 여러가지 전략 중 한 가지를 선택해준다.

해당 코드로 보았을 때, 기존 코드의 변경 없이 행위에 대한 전략을 추가가 가능한 것을 알 수 있다.

## 사용 이유

이와 같은 전략 패턴을 사용하는 이유는 여러가지 경우들이 있다.

그러한 여러가지 경우들을 지금부터 하나하나씩 살펴보도록 하곘다.

1. 전략 알고리즘의 여러 버전 또는 변형이 필요한 경우
2. 캡슐화가 필요할 때
- 알고리즘 코드가 노출되어서는 안되는 데이터에 엑세스를 하거나 활용하기 위해서 캡슐화 할 때 사용된다.
3. 런타임에서의 교체가 필요할 때
- 알고리즘의 동작을 실시간으로 교체 가능해야하는 경우에 사용한다.

위와 같은 경우에 사용이 되고 있다.

또한 전략 패턴과 같은 경우에는 OOP의 집합체로서도 정의한다고 한다.

### OOP의 집합체

1. 동일 계열의 알고리즘군을 전략 구현체로 정의한다.
2. 각 알고리즘을 인터페이스로 추상화하여 캡슐화 시켜준다.
3. 합성을 통하여 상호 교환이 가능하도록 한다.
4. 컨텍스트 객체의 수정 없이 알고리즘을 변경 가능하다.
5. 알고리즘을 메서드를 통해서 전략 객체를 실시간으로 변경하여 다양하게 변경 가능하게 한다.

전략 패턴은 해당 내용과 같이 여러가지로 정의가 된다.


## 단점
- 알고리즘이 많아질수록 관리가 힘들어진다.
- 어플리케이션의 알고리즘이 적고, 변경률이 작다면 오히려 복잡해질 수 있다.

## 결론

전략 패턴은 런타임 중에 알고리즘 전략을 선택하고, 객체의 동작을 실시간으로 바뀔 수 있게 하는 패턴이다.

해당 패턴은 OOP의 특징을 전부 가지고 있기 때문에 패턴의 공부와 같이 OOP원칙도 한 번 보고 가면 좋을 것 같다.

구조는 어렵지 않으나 코드의 크기가 증가하면 복잡해 질 수 있기 때문에 실제 사용된 패턴을 보게 되면 구조 파악하는 것이 매우 중요할 수 있을 것 같다.