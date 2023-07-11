---
layout: post
title:  "어댑터 패턴(Adapter Pattern)"
tags: [Disign Pattern, Structural Pattern]
date:   2023-07-10 00:05:00 +0900
toc:  true
---

## Adapter Pattern

어댑터 패턴은 기존 클래스를 재사용할 수 있도록 중간에서 맞춰주는 어댑터를 만들어주는 패턴이다. 호환되지 않는 인터페이스를 가진 객체들을 호환 가능하도록 만들어주는 패턴이다.

## 사용 이유
이와 같은 어댑터 패턴을 사용하는 이유는 여러가지가 있는데 한 번 알아보도록 하겠다.


1. 인터페이스 호환 관점
- 기존의 클래스를 사용하고는 싶지만 해당되는 인터페이스가 나머지 코드들과 호환되지 않는 경우에 중간 역할을 하는 어댑터를 통해서 호환이 가능하도록 만들어줄 수 있다.
2. 객체지향의 원칙 관점
- 기존 코드에서 인터페이스와 각각의 구현의 역할들을 분리할 수 있다.
- 기존 클라이언트의 코드를 수정하지 않고 새로운 유형의 어댑터들을 추가하여 기능을 확장할 수 있다.



## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

간단하게 어댑터 패턴을 사용하여 휴대폰을 아이폰과 갤럭시로 나누어 충전할 수 있는 코드를 작성하였다.

### 제품 객체 인터페이스
{% highlight ruby %}
public interface PhoneCharger {
    public void phoneCharger();
}
{% endhighlight %}


{% highlight ruby %}
public interface GalaxyCharger {
    public void galaxyCharge();
}
{% endhighlight %}

휴대폰 충전기와 갤럭시 충전기 인터페이스이다.

### 제품 객체
{% highlight ruby %}
public class Phone implements PhoneCharger{
    @Override
    public void phoneCharger() {
        System.out.println("휴대폰을 충전중입니다.");
    }
}
{% endhighlight %}

{% highlight ruby %}
public class Galaxy implements GalaxyCharger{
    @Override
    public void galaxyCharge() {
        System.out.println("갤럭시를 충전중입니다.");
    }
}
{% endhighlight %}

각각의 휴대폰은 각각 맞는 충전기 인터페이스를 상속받아 구현하고 있다.

이때 간단하게 상속받는 메서드는 <mark>...Charge()</mark>로 어떤 휴대폰을 충전하고 있는지 출력만 하게 해주었다.

### 제품 어댑터

{% highlight ruby %}
public class GalaxyAdapter implements PhoneCharger{
    Galaxy galaxy;

    public GalaxyAdapter(Galaxy galaxy){
        this.galaxy = galaxy;
    }

    @Override
    public void phoneCharger() {
        galaxy.galaxyCharge();
    }
}
{% endhighlight %}

해당하는 어댑터는 PhoneCharger 인터페이스를 상속받고 있다.

휴대폰 충전기와 갤럭시 충전기는 각각의 충전 타입이 다르기 때문에 기본 충전기로 갤럭시를 충전하려면 중간에 타입을 변환시켜주는 어댑터가 필요하고 위의 코드들이 갤럭시로 타입을 바꿔주는 어댑터 역할을 해준다.


### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        Phone phone = new Phone();
        Galaxy galaxy = new Galaxy();

        PhoneCharger galaxyAdapter = new GalaxyAdapter(galaxy);

        phone.phoneCharger();
        galaxy.galaxyCharge();

        galaxyAdapter.phoneCharger();
    }
}
    /** 출력 결과
        * 휴대폰을 충전중입니다.
        * 갤럭시를 충전중입니다.

        * 갤럭시를 충전중입니다.
        **/
{% endhighlight %}
해당 코드는 어댑터 패턴을 사용하여 서로 호환되지 않는 코드들 중간에서 번역기역할을 해서 호환 가능하도록 만들어주는 코드입니다.

각기 호환되지 않는 Phone, Galaxy 객체를 만들어줍니다.

Phone 충전기로 Galaxy를 충전하기 위해서 각각의 어댑터를 통해 객체를 생성해주었고, 어댑터를 통해서 호환되지 않던 것들을 호환되게 출력하는 것을 보여주고 있습니다.

## 단점
- 기존 코드를 재사용하기 위해서 새로운 인터페이스와 클래스들을 새로 생성해주어야 하기 때문에 코드의 전반적인 복잡성이 증가할 수 있습니다.
- 또한, 경우에 따라서 기존 코드에 추가를 하는 것이 기존 클래스를 변경하는 것보다 어렵고 복잡한 경우가 있어, 상황에 맞게 잘 사용해야 합니다.

## 결론
어댑터 패턴에서 중요하다고 생각되는 부분은 객체지향원칙 중 개방 폐쇄의 원칙이라고 생각한다.

클라이언트는 수정하는 것이 없고, 어댑터를 추가하거나 수정함으로써 확장되는 효과를 기대하기 때문에 이 패턴을 사용한 것이라고 생각한다.

또한, 유지보수성과 재사용성에 좋은 효과를 보이기 때문에 사용한다고 생각한다. 그러나 단점에서 말한것처럼 언제나 상황을 잘 살펴보고 사용하는 것 또한 중요하다.