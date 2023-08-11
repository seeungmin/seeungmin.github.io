---
layout: post
title:  "커맨드 패턴(Command Pattern)"
tags: [Disign Pattern, Behavioral Pattern]
date:   2023-08-10 12:30:00 +0900
toc:  true
---

## Command Pattern

커맨드 패턴은 요청된 명령어를 캡슐화하여 각 명령이 들어오면 그에 맞는 서브 클래스가 선택되어 실행되는 것이다. 이는 요청을 요청에 대한 모든 정보가 포함된 독립 실행형 객체로 변환하는 패턴이다.

어떤 객체 A에서 다른 객체 B의 메서드를 실행하려면 객체 B를 참조해야 해서 의존성이 생기게 되는데 커맨드 패턴을 통해서 이 의존성을 없애주는 것이다.


## 커맨드 패턴 구조

![Class Diagram](https://github.com/seeungmin/seeungmin.github.io/blob/main/DesignPatternDiagram/%EC%BB%A4%EB%A7%A8%EB%93%9C%20%ED%8C%A8%ED%84%B4.png?raw=true"Command Pattern Class Diagram")



## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

해당 코드는 커맨드 패턴을 사용해 휴대폰의 기능을 Siri를 통해서 실행해줄 수 있게 만든 간단한 코드다.


### IPhone 클래스

{% highlight ruby %}
public class IPhone {
    public void MusicStart(){
        System.out.println("음악을 재생합니다.");
    }

    public void WeatherExplain(){
        System.out.println("날씨를 알려줍니다.");
    }
}
{% endhighlight %}

휴대폰의 기능을 수행하는 클래스입니다.

각각 음악을 재생하고, 날씨를 알려줄 수 있습니다.


### Command 인터페이스
{% highlight ruby %}
public interface Command {
    public void execute();
}
{% endhighlight %}

커맨드 인터페이스이다.

기능의 종류에 관계없이 오로지 기능을 실행하는 메서드만 존재한다.


### MusicCommand, WeatherCommand 클래스

{% highlight ruby %}
public class MusicCommand implements Command{
    private IPhone iPhone;

    public MusicCommand(IPhone iPhone) {
        this.iPhone = iPhone;
    }

    @Override
    public void execute() {
        iPhone.MusicStart();
    }
}
{% endhighlight %}
{% highlight ruby %}
public class WeatherCommand implements Command{
    private IPhone iPhone;

    public WeatherCommand(IPhone iPhone) {
        this.iPhone = iPhone;
    }

    @Override
    public void execute() {
        iPhone.WeatherExplain();
    }
}
{% endhighlight %}

<mark>Command</mark>인터페이스를 구현한 클래스들이다.

각각 서로 다른 기능을 하는 클래스다.

종류에 따라서 확장시켜 나갈 수 있다.


### Siri 클래스

{% highlight ruby %}
public class Siri {
    private Command command;

    public void setCommand(Command command) {
        this.command = command;
    }

    public void run(){
        command.execute();
    }
}
{% endhighlight %}

클라이언트가 기능을 수행하기위해 호출하는 클래스이다.

<mark>Command</mark>를 선택하여 기능을 수행하는 것을 볼 수 있다.


### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        IPhone iPhone = new IPhone();

        Command musicCommand = new MusicCommand(iPhone);
        Command weatherCommand = new WeatherCommand(iPhone);
        Siri siri = new Siri();

        siri.setCommand(musicCommand);
        siri.run();

        siri.setCommand(weatherCommand);
        siri.run();
    }
}
    /** 출력 결과
        * 음악을 재생합니다.
        * 날씨를 알려줍니다.
        **/
{% endhighlight %}

해당 코드는 커맨드 패턴을 사용하여 클라이언트가 휴대폰에 있는 시리를 통해 요청을 주고 시리가 휴대폰 기능을 실행하는 것을 보여준다.

시리에서 수행할 기능을 <mark>Command</mark>를 통하여 정하고 기능을 수행시킨다.

이때, <mark>siri.run()</mark>를 통하여 클라이언트는 시리를 실행만 시키고 구체적인 기능은 커맨드를 정해주어서 클라이언트 측에서는 그 과정을 숨기고 있다.


## 사용 이유

이와 같은 커맨드 패턴을 사용하는 이유는 여러가지 경우들이 있다.

그러한 여러가지 경우들을 지금부터 하나하나씩 살펴보도록 하곘다.

1. 작업들로 객체를 매개변수화 하려는 경우
2. 작업 실행을 예약, 대기열 삽입, 작업의 원격화를 하려는 경우
3. 되돌리기가 가능한 작업을 구현하는 경우 
- 작업을 되돌리기 위해 수행한 작업의 기록을 구현해야한다.
- 커맨드 패턴을 사용하여 기록을 남겨 실행한 모든 커맨드 객체를 포함할 수 있는 스택이 된다.

위와 같은 경우에 많이 사용되고 있으며 여러 장점이 존재한다.


## 장점
- 개방 폐쇄 원칙, 단일 책임 원칙과 같은 객체지향원칙을 지킬 수 있다.
- 각각 캡슐화가 되어 결합도가 낮아진다.
- 간단한 커맨드들로 복잡한 커맨드를 조합할 수 있다.


## 단점
- 객체의 동작이 늘어날 때 마다 커맨드 클래스가 증가하기 때문에 코드의 복잡성이 증가한다.

## 결론

커맨드 패턴은 특정 기능들을 캡슐화 시키는 패턴으로서 개방폐쇄의 원칙을 지키는 패턴이다.

우리가 게임이나 여러 분야에서 커맨드라는 단어를 들어왔을 것이다. 해당 패턴의 커맨드와 의미가 같기 때문에 실생활의 예를 생각하면 금방 이해가 될 것이다.

결국은 해당 패턴 또한 개방 폐쇄의 원칙을 지키며 여러 기능을 수행할 수 있게 하는 것이 중요한 것 같다.

또한 실행, 취소에 대한 부분을 각 커맨드 객체에 로그를 남겨 사용가능하게 한다. 이러한 부분은 실생활에서 매우 많이 사용되는 부분이기 때문에 알아둬야 할 것 같다.