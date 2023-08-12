---
layout: post
title:  "상태 패턴(State Pattern)"
tags: [Disign Pattern, Behavioral Pattern]
date:   2023-08-12 12:30:00 +0900
toc:  true
---

## State Pattern

상태 패턴은 동일한 동작을 객체의 상태에 따라 다르게 처리해야 할 때 사용하는 패턴이다. 

이때 상태를 직접 체크하는 것이 아닌 상태를 객체화하여 필요에 따라 다르게 행동하도록 위임하는 것이다.


## 상태 패턴 구조

![Class Diagram](https://github.com/seeungmin/seeungmin.github.io/blob/main/DesignPatternDiagram/%EC%83%81%ED%83%9C%20%ED%8C%A8%ED%84%B4.png?raw=true"State Pattern Class Diagram")



## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

해당 코드는 상태 패턴을 사용하여 선풍기의 상태를 객체화 시켜서 선풍기의 상태를 조절하는 코드입니다.


### AirVolumeState 인터페이스

{% highlight ruby %}
public interface AirVolumeState {
    public void push();
}
{% endhighlight %}

선풍기 바람세기를 나타내는 인터페이스이다.

<mark>push()</mark>메서드를 통해서 세기를 조절할 수 있게 한다. 현실에서 선풍기 버튼 누르는 것을 생각해 보았다.


### Off 클래스
{% highlight ruby %}
public class Off implements AirVolumeState{
    @Override
    public void push() {
        System.out.println("전원을 끕니다.");
    }
}
{% endhighlight %}

### Low 클래스
{% highlight ruby %}
public class Low implements AirVolumeState{
    @Override
    public void push() {
        System.out.println("약풍입니다.");
    }
}
{% endhighlight %}

### Middle 클래스
{% highlight ruby %}
public class Middle implements AirVolumeState{
    @Override
    public void push() {
        System.out.println("중풍입니다.");
    }
}
{% endhighlight %}

### High 클래스
{% highlight ruby %}
public class High implements AirVolumeState{
    @Override
    public void push() {
        System.out.println("강풍입니다.");
    }
}
{% endhighlight %}

위의 4개의 클래스는 <mark>AirVolumeState</mark> 인터페이스를 구현하는 클래스이다.

각각의 클래스는 선풍기 바람을 정지, 약풍, 중풍, 강풍으로 선택할 수 있다.

### Fan 클래스

{% highlight ruby %}
public class Fan {
    private AirVolumeState airVolumeState;

    public Fan(){
        this.airVolumeState = new Off();
    }

    public void setAirVolumeState(AirVolumeState airVolumeState) {
        this.airVolumeState = airVolumeState;
    }

    public void pushButton(){
        airVolumeState.push();
    }
}
{% endhighlight %}

상태 변화에 대상이 되는 클래스이다.

상태 객체들을 바꿀 수 있도록 <mark>setAirVolumeState()</mark> 메서드를 선언하여 나타냈다.

상태 객체의 기능을 수행하기 위해 <mark>pushButton()</mark> 메서드를 선언하여 나타냈다.


### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        Fan fan = new Fan();
        Low low = new Low();
        Middle middle = new Middle();
        High high = new High();
        Off off = new Off();

        fan.pushButton();
        fan.setAirVolumeState(low);
        fan.pushButton();
        fan.setAirVolumeState(middle);
        fan.pushButton();
        fan.setAirVolumeState(high);
        fan.pushButton();
        fan.setAirVolumeState(off);
        fan.pushButton();
    }
}
    /** 출력 결과
        * 전원을 끕니다.
        * 약풍입니다.
        * 중풍입니다.
        * 강풍입니다.
        * 전원을 끕니다.
        **/
{% endhighlight %}

해당 코드는 상태 패턴을 사용하여 선풍기의 강도를 조절해 주는 것을 보여주는 코드입니다.

대상 객체와 상태 객체들을 생성 한 후에 상태를 변환시켜가며 기능들을 수행하는 것을 알 수 있다.


## 사용 이유

이와 같은 상태 패턴을 사용하는 이유는 여러 가지 경우들이 있다.

그러한 여러 가지 경우들을 지금부터 하나하나씩 살펴보도록 하겠다.

1. 유사한 상태들에 대해서 중복 코드와 조건문 기반의 코드가 많은 경우
- 조건문들의 브랜치들을 상태 클래스들의 메서드들로 추출하여 해결한다.
2. 상태에 따라 다르게 행동하는 객체가 존재하는 경우
3. 상태의 수가 많은 경우
4. 상태별 코드가 자주 변경되는 경우

위와 같은 경우에 많이 사용되고 있으며 여러 장점이 존재한다.


## 장점
- 개방 폐쇄 원칙, 단일 책임 원칙과 같은 객체지향 원칙을 지킬 수 있다.
- 거대한 조건문들을 제거하여 코드를 단순화하는 것이 가능하다.
- 기존의 상태들을 추가하거나 기존 상태들을 독립적으로 변경하여 유지 보수 비용을 절감 가능하다.



## 단점
- 상태가 많지 않거나 상태가 거의 변경되지 않는 경우에는 오히려 독이 될 수 있다.

## 전략 패턴과의 차이

두 패턴은 객체화를 통한 클래스 묶음을 만들어내기 때문에, 전략 패턴과 상태 패턴의 클래스 다이어그램은 매우 유사하다고 생각할 수 있다.

이러한 공통점도 존재하지만 다른 점 또한 알아보겠다.

- 전략 패턴
    - 전략을 객체화
- 상태 패턴
    - 상태를 객체화


## 결론

상태 패턴은 객체가 특정 상태에 따라 행위를 달리하는 경우에 상태를 조건문으로 검사하는 것이 아닌, 상태를 객체화하여 상태가 행동을 할 수 있도록 위임하는 패턴이다.

크게 어려울 것 없는 패턴이고, 실생활의 예들도 많기 때문에 가볍게 공부하고 넘어가면 될 것 같은 패턴이다.