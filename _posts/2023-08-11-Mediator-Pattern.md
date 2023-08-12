---
layout: post
title:  "중재자 패턴(Mediator Pattern)"
tags: [Disign Pattern, Behavioral Pattern]
date:   2023-08-11 12:30:00 +0900
toc:  true
---

## Mediator Pattern

중재자 패턴은 클래스 간의 복잡한 상호작용을 캡슐화하여 한 클래스에 위임하여 중재 처리해 주는 패턴입니다. 

이때 중재자라는 객체 안에서 서로 다른 객체들을 캡슐화하여 객체들이 더 이상 직접적으로 상호작용하지 않고, 중재자를 통해서 커뮤니케이션이 가능하도록 합니다.

이를 통해 객체 간의 의존성을 줄이면서 클래스 간의 느슨한 결합을 만들 수 있습니다.


## 중재자 패턴 구조

![Class Diagram](https://github.com/seeungmin/seeungmin.github.io/blob/main/DesignPatternDiagram/%EC%A4%91%EC%9E%AC%EC%9E%90%20%ED%8C%A8%ED%84%B4.png?raw=true"Mediator Pattern Class Diagram")



## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

해당 코드는 중재자 패턴을 사용하여 톡 방에 사람이 들어왔을 때 톡 방이 중재자 역할을 하고, 사람들이 메시지를 보내고 받는 것을 보여주는 코드입니다.


### Mediator 인터페이스

{% highlight ruby %}
public interface Mediator {
    public void addUser(User user);
    public void deleteUser(User user);
    public void sendMessage(User user, String message);
}
{% endhighlight %}

중재자 역할을 하는 인터페이스이다.

회원을 추가, 삭제, 메시지 전송의 기능을 가지고 있다.


### User 추상 클래스
{% highlight ruby %}
public abstract class User {
    public Mediator mediator;
    public String name;

    public User(Mediator mediator, String name) {
        this.mediator = mediator;
        this.name = name;
    }

    public abstract void send(String message);
    public abstract void receive(String message);
}
{% endhighlight %}

유저 추상 클래스이다.

각각 중재자를 가지고 있을 수 있다. 이는 톡 방을 선택할 수 있다는 것과 같다.

각 유저는 이름이 존재하고 <mark>send()</mark>, <mark>receive()</mark>메서드를 통해서 메시지를 전송, 수신할 수 있다.


### MemberUser 클래스

{% highlight ruby %}
public class MemberUser extends User{
    public MemberUser(Mediator mediator, String name) {
        super(mediator, name);
    }

    @Override
    public void send(String message) {
        System.out.println(this.name + "님이 보낸 메세지 입니다 : " + message);
        mediator.sendMessage(this, message);
    }

    @Override
    public void receive(String message) {
        System.out.println(this.name + "님이 받은 메세지 입니다 : " + message);
    }
}
{% endhighlight %}

톡 방에 들어온 유저를 나타내는 구체 클래스입니다.

각각 회원에 따라 이름을 받아 메시지를 보내고 받는 것을 나타내고 있다.


### UserManagerMediator 클래스

{% highlight ruby %}
public class UserManagerMediator implements Mediator{

    private List<User> users = new ArrayList<>();

    @Override
    public void addUser(User user) {
        this.users.add(user);
    }

    @Override
    public void deleteUser(User user) {
        this.users.remove(user);
    }

    @Override
    public void sendMessage(User user, String message) {
        for (User user1 : this.users){
            if (user1 != user)
                user1.receive(message);
        }
    }
}
{% endhighlight %}

유저를 중재자에서 관리하기 위한 클래스입니다.

<mark>List</mark> 로 유저를 받아 관리한다.

각각 <mark>addUser()</mark>, <mark>deleteUser()</mark> 메서드를 통해서 중재자에서 관리할 유저를 추가 삭제할 수 있다.

<mark>sendMessage()</mark> 메서드를 통해서 메시지를 보낸 유저를 제외한 톡 방에 존재하는 모든 유저가 메시지를 받도록 한다.


### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        Mediator mediator = new UserManagerMediator();

        User user1 = new MemberUser(mediator, "철수");
        User user2 = new MemberUser(mediator, "영희");
        User user3 = new MemberUser(mediator, "가영");
        User user4 = new MemberUser(mediator, "진수");

        mediator.addUser(user1);
        mediator.addUser(user2);
        mediator.addUser(user3);
        mediator.addUser(user4);

        user1.send("하윙");

        mediator.deleteUser(user2);

        user3.send("반가워");

    }
}
    /** 출력 결과
        * 철수님이 보낸 메세지 입니다 : 하윙
        * 영희님이 받은 메세지 입니다 : 하윙
        * 가영님이 받은 메세지 입니다 : 하윙
        * 진수님이 받은 메세지 입니다 : 하윙
        * 가영님이 보낸 메세지 입니다 : 반가워
        * 철수님이 받은 메세지 입니다 : 반가워
        * 진수님이 받은 메세지 입니다 : 반가워
        **/
{% endhighlight %}

해당 코드는 중재자 패턴을 사용하여 톡 방에 사람을 추가하고 메시지를 보내고 받고, 톡 방을 나가는 것을 구현한 코드입니다.

중재자를 통해서 유저를 추가, 삭제하는 것을 볼 수 있다. 이를 통해 직접적으로 접근하는 것을 막을 수 있다.

자신이 메시지를 보내면 자신을 제외한 다른 모든 유저는 메시지를 받을 수 있다.


## 사용 이유

이와 같은 중재자 패턴을 사용하는 이유는 여러 가지 경우들이 있다.

그러한 여러 가지 경우들을 지금부터 하나하나씩 살펴보도록 하겠다.

1. 느슨한 결합을 유지해야 하는 경우
- 서로 상호작용하는 객체들을 캡슐화한다.
2. 객체 사이의 관계가 M : N 관계일 경우
- 복잡한 상호작용하는 객체들을 1 : M 관계로 바꾸어 준다.

위와 같은 경우에 많이 사용되고 있으며 여러 장점이 존재한다.


## 장점
- 개방 폐쇄 원칙, 단일 책임 원칙과 같은 객체지향 원칙을 지킬 수 있다.
- 프로그램의 객체 간의 결합도를 낮출 수 있다.
- 개별 객체들의 재사용성이 증가한다.


## 단점
- 코드의 양이 증가함에 따라 중재자 패턴의 역할이 너무나 커질 수 있다.

## 옵저버 패턴, 퍼사드 패턴과의 차이점
### 옵저버 패턴 차이

옵저버 패턴과의 차이는 간단히 말하면 객체들의 관계의 수에서 차이가 있다.

- 옵저버 패턴
    - 1개의 publisher에 대해 M개의 subscriber가 존재한다.
- 중재자 패턴
    - M개의 publisher와 N개의 subscriber 사이에서 1개의 mediator를 통해 통신한다.

### 퍼사드 패턴 차이

퍼사드 패턴과의 차이는 간단히 말하면 통신 방향에서의 차이가 있다.

- 퍼사드 패턴
    - 단방향 통신만 가능하다.
- 중재자 패턴
    - 양방향 통신을 지원한다.


## 결론

중재자 패턴은 M : N 관계를 M : 1 관계로 만들어 복잡도를 줄여 느슨한 결합을 유지하고 유지 보수 및 확장성에서 유리함을 가지는 패턴입니다.

여러 패턴들과의 유사점이 많기 때문에 앞선 패턴들에 대해 열심히 공부했다면 연관 시켜서 공부하면 어렵지 않은 패턴이다.

위에서 말했듯이 퍼사드, 옵저버 패턴과의 혼동 가능성이 존재하기 때문에 각각의 특징을 확실하게 하고 넘어가야 할 것 같다.