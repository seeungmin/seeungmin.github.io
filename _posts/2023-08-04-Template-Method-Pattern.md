---
layout: post
title:  "템플릿 메서드 패턴(Template Method Pattern)"
tags: [Disign Pattern, Behavioral Pattern]
date:   2023-08-04 10:30:00 +0900
toc:  true
---

## Template Method Pattern

템플릿 메서드 패턴은 상위 클래스에서는 추상적으로 표현하고 그 구체적인 내용은 하위 클래스에서 결정하는 것이다. 자세히 말하자면 알고리즘의 골격을 정의한다고 할 수 있다.

알고리즘의 일부 단계를 하위 클래스에서 구현할 수 있고, 알고리즘의 구조는 그대로 유지하며 특정 단계를 서브 클래스에서 재정의 할 수도 있습니다.

## 템플릿 메서드 패턴 구조

![Class Diagram](https://github.com/seeungmin/seeungmin.github.io/blob/main/DesignPatternDiagram/%ED%85%9C%ED%94%8C%EB%A6%BF%20%EB%A9%94%EC%84%9C%EB%93%9C%20%ED%8C%A8%ED%84%B4.png?raw=true "Template Method Pattern Class Diagram")



## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

해당 코드는 템플릿 메서드 패턴을 사용하여 여러 교수님들이 수업하시는 것을 나타낸 것입니다.


### Professor 추상 클래스

{% highlight ruby %}
public abstract class Professor {
    public void startClass(){
        comeIn();
        callAttendance();
        teach();
        goOut();
    }

    public void comeIn(){
        System.out.println("교수님이 들어오신다.");
    }

    public void callAttendance(){
        System.out.println("교수님이 출석을 부르신다.");
    }

    public void goOut(){
        System.out.println("교수님이 나가신다.");
    }

    public abstract void teach();
}
{% endhighlight %}

교수님을 나타내는 추상클래스이다.

<mark>comIn()</mark>, <mark>goOut()</mark>, <mark>callAttendance()</mark>메서드는 각각 교수님이 들어오고, 출석부르고, 나가는 것을 나타낸 것이다. 이와 같은 것들은 모든 교수님들이 공통된 부분이기 때문에 구현을 해 놓았다.

<mark>teach()</mark>메서드는 추상 메서드로서 교수님들이 수업하시는 과목이 다르기 때문에 각각 하위 클래스에서 구현하도록 하였다.

<mark>startClass()</mark>는 템플릿 메서드로서 교수님이 수업을 하시는 전반적인 알고리즘을 정의해둔 것이다.


<mark>startClass()</mark>메서드에서 이미 추상 클래스에서 정의된 <mark>comIn()</mark>, <mark>goOut()</mark>, <mark>callAttendance()</mark>메서드는 공통적으로 실행이 되고, <mark>teach()</mark>메서드는 구현에 따라 나뉠 것이라는 것을 알 수 있다.



### ProfessorPark, Lee, Jung 클래스
{% highlight ruby %}
public class ProfessorPark extends Professor{
    @Override
    public void teach() {
        System.out.println("파이썬 수업을 합니다.");
    }
}
{% endhighlight %}
{% highlight ruby %}
public class ProfessorLee extends Professor{
    @Override
    public void teach() {
        System.out.println("클라우드 컴퓨팅 수업을 합니다.");
    }
}
{% endhighlight %}{% highlight ruby %}
public class ProfessorJung extends Professor{
    @Override
    public void teach() {
        System.out.println("컴퓨터 네트워크 수업을 합니다.");
    }
}

{% endhighlight %}

해당 클래스들은 <mark>Professor</mark> 추상 클래스를 상속받아 구현하는 구현 클래스들입니다.

각각 교수님들의 수업 내용에 따라 <mark>teach()</mark>메서드가 구현되어 있음을 알 수 있습니다.



### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        ProfessorJung pj = new ProfessorJung();
        ProfessorLee pl = new ProfessorLee();
        ProfessorPark pp = new ProfessorPark();

        pj.startClass();
        System.out.println("==========================================");
        pl.startClass();
        System.out.println("==========================================");
        pp.startClass();
        System.out.println("==========================================");
    }
}
    /** 출력 결과
        * 교수님이 들어오신다.
        * 교수님이 출석을 부르신다.
        * 컴퓨터 네트워크 수업을 합니다.
        * 교수님이 나가신다.
        * ==========================================
        * 교수님이 들어오신다.
        * 교수님이 출석을 부르신다.
        * 클라우드 컴퓨팅 수업을 합니다.
        * 교수님이 나가신다.
        * ==========================================
        * 교수님이 들어오신다.
        * 교수님이 출석을 부르신다.
        * 파이썬 수업을 합니다.
        * 교수님이 나가신다.
        * ==========================================
        **/
{% endhighlight %}

해당 코드는 템플릿 메서드 패턴을 사용하여 교수님들이 수업하시는 것을 나타낸 예시 코드입니다.

여러명의 교수를 생성, 정의 한 후에 <mark>startClass()</mark>메서드를 실행시켜 각각의 행동을 파악할 수 있다.

템플릿 메서드인 <mark>startClass()</mark>를 통해서 전체적인 알고리즘을 정의해주고, 해당 알고리즘에서 변경이 필요한 부분은 추상 메서드를 통해서 구현하고 있음을 알 수 있다.

## 사용 이유

이와 같은 템플릿 메서드 패턴을 사용하는 이유는 여러가지 경우들이 있다.

그러한 여러가지 경우들을 지금부터 하나하나씩 살펴보도록 하곘다.

1. 알고리즘의 특정 단계들만 확장가능해야하는 경우
- 모놀리식 알고리즘을 개별 단계들로 전환 가능함
- 부모 클래스에서 정의된 구조를 그대로 유지하며 자식 클래스에 쉽게 확장될 수 있음
2. 전체 알고리즘이나 알고리즘 구조는 확장하지 못하게 하는 경우
3. 중복되는 알고리즘을 포함하는 여러 클래스가 있는 경우
- 유사한 구현들이 있는 단계들은 부모 클래스로 묶을 수 있음
- 코드 중복을 제거 가능함

위와 같은 경우에 많이 사용되고 있으며 코드 중복을 제거하고, 알고리즘 이외의 변경에 영향을 덜 받을 수 있다는 장점이 존재한다.



## 단점
- 일부 클라이언트는 제공된 알고리즘 골격에 제한될 수 있다.
- 알고리즘 구조가 복잡할수록 형태를 유지하기 어렵고, 추상 메서드가 많아지면 클래스 관리가 어려워진다.

## 결론

템플릿 메서드 패턴은 특정 작업을 처리하는 일부를 서브 클래스로 캡슐화하여 전체 구조를 바꾸지 않고 일부만 수정하는 패턴이다.

이는 디자인 패턴이라고 하기도 과하다는 생각이 될 정도로 객체지향 언어로 개발을 진행할 때 무의식적으로 사용하고 있는 패턴입니다.

그렇기 때문에 어려운 내용이 전혀 없기 때문에 한 번 더 읽어보고 넘어간다고 생각하며 공부하면 좋을 것 같다.