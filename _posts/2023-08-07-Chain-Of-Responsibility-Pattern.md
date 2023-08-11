---
layout: post
title:  "책임 연쇄 패턴(Chain Of Responsibility Pattern)"
tags: [Disign Pattern, Behavioral Pattern]
date:   2023-08-07 12:30:00 +0900
toc:  true
---

## Chain Of Responsibility Pattern

책임 연쇄 패턴은 책임들이 연결되어 있어 어떠한 객체가 책임을 지지 못 할 것 같으면 다음 책임자에게 책임을 자동으로 넘겨주는 패턴이다. 

이때 핸들러들의 사슬에 따라 요청을 전달할 수 있게 하고, 각 핸들러는 요청을 받으면 요청을 처리할지 다음 핸들러로 전달할지 결정합니다. 


## 책임 연쇄 패턴 구조

![Class Diagram](https://github.com/seeungmin/seeungmin.github.io/blob/main/DesignPatternDiagram/%EC%B1%85%EC%9E%84%20%EC%97%B0%EC%87%84%20%ED%8C%A8%ED%84%B4.png?raw=true"Chain Of Responsibility Pattern Class Diagram")



## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

해당 코드는 책임 연쇄 패턴을 사용해 로그인 할 때에 아이디, 비밀번호, 보안코드를 입력받아 로그인 성공을 판단해주는 코드를 나타낸 것입니다.


### Login 클래스

{% highlight ruby %}
public class Login {
    private String id;
    private String password;
    private String securityCode;

    public Login(String id, String password, String securityCode) {
        this.id = id;
        this.password = password;
        this.securityCode = securityCode;
    }

    public String getId() {
        return id;
    }

    public String getPassword() {
        return password;
    }

    public String getSecurityCode() {
        return securityCode;
    }
}
{% endhighlight %}

로그인을 요청하기 위한 정보를 나타내는 클래스입니다.


### Support 추상 클래스
{% highlight ruby %}
public abstract class Support {
    private Support next;

    public Support setNext(Support next){
        this.next = next;
        return next;
    }

    public final void support(Login login){
        if (check(login))
            return;
        else if(next != null)
            next.support(login);
        else
            System.out.println("성공적인 로그인입니다.");
    }

    public abstract boolean check(Login login);
}
{% endhighlight %}

해당 클래스는 요청을 해결할 사슬을 만들기 위한 추상 클래스 입니다.

<mark>next</mark>필드에 다음 사슬을 지정한 후, <mark>setNext()</mark>메서드를 통하여 다음 사슬을 설정해줍니다.

<mark>support()</mark>메서드는 요청이 들어왔을 때 요청의 처리 순서에 대한 것을 구현해 놓았습니다.

<mark>check()</mark>메서드는 추상 메서드로 선언하여 하위 클래스에서 요청에 대한 처리를 구현할 수 있도록 하였습니다.


### NoSupport 클래스

{% highlight ruby %}
public class NoSupport extends Support{
    @Override
    public boolean check(Login login) {
        return false;
    }
}
{% endhighlight %}

<mark>Support</mark>를 상속받아 구현한 클래스이다.

아무것도 처리하지 않는 <mark>Support</mark>로서 임시로 시작점을 두기 위해서 생성하였다.

### Id, PassWord, SecurityCode Support 클래스

{% highlight ruby %}
public class IdSupport extends Support{
    @Override
    public boolean check(Login login) {
        if(login.getId().equals("myId")){
            return false;
        }
        System.out.println("아이디가 잘못되었습니다.");
        return true;
    }
}
{% endhighlight %}
{% highlight ruby %}
public class PassWordSupport extends Support{
    @Override
    public boolean check(Login login) {
        if(login.getPassword().equals("myPassword")){
            return false;
        }
        System.out.println("비밀번호가 잘못되었습니다.");
        return true;
    }
}
{% endhighlight %}
{% highlight ruby %}
public class SecurityCodeSupport extends Support{
    @Override
    public boolean check(Login login) {
        if(login.getSecurityCode().equals("1234")){
            return false;
        }
        System.out.println("보안코드가 잘못되었습니다.");
        return true;
    }
}
{% endhighlight %}

<mark>Support</mark>를 상속받아 구현한 클래스들이다.

입력받은 아이디, 비밀번호, 보안코드를 확인하기 위한 로직이 <mark>check()</mark>메서드에 구현되어 있습니다.

입력받은 것들이 일치한다면 <mark>false</mark>를 반환하여 다음 확인할 단계로 넘어갈 수 있게 해준다.

만약 그렇지 않다면 잘못되었다고 출력해준 후에 <mark>true</mark>를 반환하여 다음 단계로 넘어가지 않고 해당 단계에서 끝낸다.


### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        Support a = new NoSupport();
        Support id = new IdSupport();
        Support passWord = new PassWordSupport();
        Support securityCode = new SecurityCodeSupport();

        a.setNext(securityCode).setNext(id).setNext(passWord);

        for(int i = 0; i<4; i++){
            System.out.print("id를 입력하세요 : ");
            String cId = sc.nextLine();
            System.out.print("password를 입력하세요 : ");
            String cPW = sc.nextLine();
            System.out.print("securitycode를 입력하세요 : ");
            String cSC = sc.nextLine();
            a.support(new Login(cId, cPW, cSC));
        }
    }
}
    /** 출력 결과
        * id를 입력하세요 : myId
        * password를 입력하세요 : myPassword
        * securitycode를 입력하세요 : 1
        * 보안코드가 잘못되었습니다.
        * 
        * id를 입력하세요 : my
        * password를 입력하세요 : myPassword
        * securitycode를 입력하세요 : 1234
        * 아이디가 잘못되었습니다.
        * 
        * id를 입력하세요 : myId
        * password를 입력하세요 : myPassword
        * securitycode를 입력하세요 : 1234
        * 성공적인 로그인입니다.
        **/
{% endhighlight %}

해당 코드는 책임 연쇄 패턴을 사용해 아이디, 비밀번호, 보안코드를 받아서 로그인하는 것을 보여주고, 잘못된 입력들이 들어왔을 때 무엇이 잘못되었는지 보안코드, 아이디, 비밀번호를 우선순위로 알려주는 코드입니다.

<mark>a.setNext(securityCode).setNext(id).setNext(passWord);</mark> 

해당 코드에서 다음 사슬에 연결되어 있는 객체들을 우선적으로 정의해 주었다.

1. 보안코드
2. 아이디
3. 비밀번호

위의 순서로 확인할 수 있도록 지정해 두었다. 

이는 보안코드와 비밀번호가 제대로 입력되어도 아이디가 제대로 입력되지 않는다면 비밀번호는 확인하지 않고 아이디에서 끊긴다는 의미이다.


## 사용 이유

이와 같은 책임 연쇄 패턴을 사용하는 이유는 여러가지 경우들이 있다.

그러한 여러가지 경우들을 지금부터 하나하나씩 살펴보도록 하곘다.

1. 어플리케이션이 다양한 방식, 종류로 요청들을 처리할것이라고 생각되지만 정확한 유형들과, 순서를 미리 알 수 없는 경우
2. 특정 순서로 핸들러를 실행해야 하는 경우
- 원하는 순서로 핸들러를 연결할 수 있기 떄문에 계획한 대로 어플리케이션을 실행할 수 있다.
3. 복잡한 객체 구조의 모든 요소에 대해 작업을 수행해야 하는 경우 

위와 같은 경우에 많이 사용되고 있으며 여러 장점이 존재한다.


## 장점
- 개방 폐쇄 원칙, 단일 책임 원칙과 같은 객체지향원칙을 지킬 수 있다.
- 요청의 순서를 제어할 수 있다.
- 요청을 보낸 쪽과 받는 쪽을 분리시킬 수 있다.


## 단점
- 순서를 정해두기 때문에 일부 요청들은 처리되지 않을 수 있다.

## 결론

책임 연쇄 패턴은 받은 요청을 직접 처리하거나 사슬에 있는 다른 객체에 넘기게 되는 책임을 넘기는 구조로 된 패턴입니다.

내가 원하는 대로 순서를 정해서 요청을 처리할 수 있기 때문에 윈도우 시스템에서 마우스 클릭이나 키보드 이벤트를 처리할 때 흔하게 사용이 됩니다.

코드를 구현하는데 있어서 다른 디자인 패턴에 비하여 어려운 감이 있기는 했지만, c언어의 연결리스트와 같은 것들과 연계하여 생각하니 충분히 작성할 수 있었다.

개념자체는 크게 어려울 것 없으니 천천히 코드를 옮겨가며 확인하면 이해하는데 도움이 될 것 같다.