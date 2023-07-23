---
layout: post
title:  "프록시 패턴(Proxy Pattern)"
tags: [Disign Pattern, Structural Pattern]
date:   2023-07-22 15:30:00 +0900
toc:  true
---

## Proxy Pattern

프록시 패턴은 실제 객체를 감싸고 추가 기능을 제공하며 실제 객체 접근을 통제하기 위해 사용되는 패턴이다. 

이때 프록시 패턴은 원래 객체에 대하 접근을 제어하기 때문에, 클라이언트의 요청이 기존 객체에 전달되기 전과 후에 추가적인 기능을 수행할 수 있도록 한다.

## 사용 이유

이와 같은 프록시 패턴을 사용하는 이유는 여러가지 경우들이 있다.

그러한 여러가지 경우들을 지금부터 하나하나씩 살펴보도록 하곘다.

1. 가상 프록시
- 앱이 시작할 때 객체를 생성하는 대신에, 객체 초기화를 실제로 초기화가 필요한 시점까지 지연시키는 방법이다.
- 무거운 서비스 객체를 필요 시점에 생성해주기 때문에 시스템 자원의 낭비를 막을 수 있다.
2. 보호 프록시
- 클라이언트의 자격 증명이 정해진 기준과 일치하는 경우에 한해서 서비스 객체에 요청을 전달할 수 있는 방법이다.
- 서비스 측에서 특정 클라이언트만 서비스 객체를 사용할 수 있도록 하려는 경우에 사용하게 된다.
3. 원격 프록시
- 프록시가 네트워크를 통해서 클라이언트의 요청을 전달하여 네트워크와의 작업의 모든 복잡한 세부사항을 처리해주는 방법이다.
- 서비스의 객체가 원격 서버에 존재하는 경우에 사용되는 방법입니다.
4. 로깅 프록시
- 프록시가 각 요청을 서비스에 전달하기 전에 로깅을 하는 방법입니다.
- 서비스 객체에 대한 요청들의 기록을 유지하기 위해 사용됩니다.
5. 캐싱 프록시
- 프록시가 항상 같은 결과를 생성해주는 반복 요청들에 대해서 캐싱을 구현하는 방법입니다.
- 이때 요청들의 매개변수들을 프록시가 캐시 키로 사용할 수 있게 됩니다.
- 이러한 방법은 클라이언트 요청들의 결과들을 캐시하고 이 캐시들의 수명 주기를 관리해야 할 때 사용되는 방법입니다.

지금까지 설명한 5가지 말고도 수많은 경우에 사용이 되지만 가장 대표적인 경우를 설명해 드렸습니다.


### 장점

각각의 경우에 프록시 패턴을 사용하여 얻을 수 있는 이점들로는 여러가지가 있습니다.
- 서비스나 클라이언트들을 변경하지 않고 프록시를 새로 도입할 수 있다.
- 클라이언트들이 사용하지 않을 때 서비스 객체의 생명주기 관리가 가능하다.
- 서비스 객체가 준비되지 않거나 사용할 수 없는경우에도 프록시를 사용할 수 있다.

이와 같은 이점들로 여러가지 프록시를 사용하고 있는 것을 알 수 있다.


## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

해당 코드는 이미지를 프록시를 통해서 보여주는 코드를 간단하게 작성해 보았다.


### Image 인터페이스
{% highlight ruby %}
public interface Image {
    public void showImage();
}
{% endhighlight %}
이미지를 나타내는 인터페이스이다.

해당 인터페이스를 구현하여 프록시 패턴을 나타낼 것이다.

### Real Image 구현 클래스
{% highlight ruby %}
public class RealImage implements Image{

    @Override
    public void showImage() {
        System.out.println("원본 이미지 보여주기!");
    }
}
{% endhighlight %}
Image 인터페이스를 구현하고 있는 구현 클래스이다.

이미지를 보여주는 코드를 직접적으로 구현하고 있다.

### Proxy 구현 클래스
{% highlight ruby %}
public class Proxy implements Image{
    private RealImage realImage;

    public Proxy(RealImage realImage){
        this.realImage = realImage;
    }

    @Override
    public void showImage() {
        realImage.showImage();
        System.out.println("프록시 객체 실행!");
    }
}
{% endhighlight %}
Image 인터페이스를 구현하고 있는 구현 클래스로서 대상 객체를 중계할 대리자 역할을 하는 클래스이다.

프록시는 대상 객체와 같은 이름의 메서드를 호출하며, 별도의 로직 또한 수행할 수 있다.

### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        Image image = new Proxy(new RealImage());

        image.showImage();
    }
}
    /** 출력 결과
        * 원본 이미지 보여주기!
        * 프록시 객체 실행!
        **/
{% endhighlight %}

해당 코드는 기본형 프록시 패턴을 보여주는 코드이다.

Image 객체의 <mark>showImage()</mark>메서드를 호출할 때 <mark>RealImage</mark>객체를 직접 호출하는 것이 아니라 <mark>Proxy</mark>객체의 <mark>showImage()</mark>메서드를 호출하여 <mark>RealImage</mark>객체의 <mark>showImage()</mark>를 호출하고 있다.

이를 통해 Proxy가 대리자 역할을 수행하고 있음을 알 수 있고, 또한 Proxy에서 별도의 로직을 실행하고 있음을 알 수 있다.

## 여러가지 Proxy

위에서 가장 기본적인 Proxy 패턴을 알아보았다.

지금부터는 추가적으로 위에서 말한 여러 경우들을 한 번 씩 살펴볼 것이다.

### 가상 Proxy 구현 클래스
{% highlight ruby %}
public class VirtualProxy implements Image{
    private RealImage realImage;

    public VirtualProxy(){}

    @Override
    public void showImage() {
        if(realImage == null){
            realImage = new RealImage();
        }
        realImage.showImage();

        System.out.println("프록시 객체 실행!");
    }
}
{% endhighlight %}

해당 코드는 기존의 프록시 패턴에서 지연 초기화 방식을 사용한 방법이다.

기존에는 생성자를 통해 선언 및 정의를 했지만, 해당 방법은 실제로 요청이 들어왔을때 실제객체를 생성한다.

<mark>realImage == null</mark>코드를 통해서 판단을 한 후 객체를 생성하는 것을 볼 수 있다.

### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        Image image1 = new VirtualProxy();

        image1.showImage();
    }
}
    /** 출력 결과
        * 원본 이미지 보여주기!
        * 프록시 객체 실행!
        **/
{% endhighlight %}

해당 코드는 가상 프록시를 보여주는 코드이다.

기존 프록시와 다른점은 객체를 생성하는 시점이다.

가상 프록시를 사용하는 이유를 알아보겠다.

1. 객체 생성에 많은 자원이 소모 되지만 사용빈도는 낮을 때
2. 가끔 쓰지만 항상 메모리에 적재되어 있는 경우
3. 서비스를 시작할 때 객체를 전부 생성하는 것이 아닌 필요한 시점에 초기화하여 로딩을 줄일 때

이와 같은 경우 가상 프록시를 사용해준다.

### 보호 Proxy 구현 클래스
{% highlight ruby %}
public class ProtectionProxy implements Image{
    private RealImage realImage;
    boolean access;

    public ProtectionProxy(RealImage realImage, boolean access){
        this.realImage = realImage;
        this.access = access;
    }

    @Override
    public void showImage() {
        if(access){
            realImage.showImage();
            System.out.println("프록시 객체 실행!");
        }
    }
}
{% endhighlight %}

해당 코드는 기본형 Proxy패턴에서 접근 권한을 추가하여 객체를 생성해주는 방법이다.

객체를 생성하거나 메서드를 실행할 때, 접근 권한이 없으면 실행하지 못 하도록 한다.

<mark>if(access)</mark>를 통해서 판단을 해준다.

### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        Image image2 = new ProtectionProxy(new RealImage(), true);

        image3.showImage();
    }
}
    /** 출력 결과
        * 원본 이미지 보여주기!
        * 프록시 객체 실행!
        **/
{% endhighlight %}

해당 코드는 보호 프록시를 보여주는 코드이다.

기존 프록시와 다른점은 객체 생성 방법과 메서드를 실행할 때 접근권한이 존재한다는 것이다.

보호 프록시를 사용하는 이유를 알아보겠다.

1. 특정 클라이언트만 서비스의 객체를 사용할 수 있도록 하는 경우
2. 프록시 객체를 통해서 클라이언트의 자격이 기준과 일치하는 경우에만 요청하는 경우

이와 같은 경우 보호 프록시를 사용해준다



## 단점
- 실제 객체를 실행하는 것 보다 프록시를 통해서 실행하기 때문에 서비스의 응답이 약간 늦어질 수 있다.
- 새로운 클래스들을 많이 도입해야 해서 코드의 복잡성이 증가할 수 있다.

## 결론
프록시 패턴은 여러가지 앞에서 말한 이유들 때문에 실제 객체를 직접 실행하지 않고 프록시를 통해서 실행하는 방법이다.

사용되는 경우의 예가 많은것만 확인해보더라도 해당 패턴이 매우 많이 사용되고 있음을 알 수 있다.

코드나 내용 전부 이해하기 쉬운 내용이라고 생각이 되고, 실제로도 많이 쓰이기 때문에 꼭 완벽하게 공부하고 넘어가야 할 것 같다.