---
layout: post
title:  "퍼사드 패턴(Facade Pattern)"
tags: [Disign Pattern, Structural Pattern]
date:   2023-07-24 15:30:00 +0900
toc:  true
---

## Facade Pattern

퍼사드 패턴은 서브시스템에 있는 인터페이스 집합에 대해서 하나의 통합된 인터페이스를 제공해주는 패턴이다. 

그 외에도 라이브러리, 프레임워크 또는 여러 복잡한 집합의 클래스에 대해서 단순화된인터페이스를 제공해주는 것이다.

## 사용 이유

이와 같은 퍼사드 패턴을 사용하는 이유는 여러가지 경우들이 있다.

그러한 여러가지 경우들을 지금부터 하나하나씩 살펴보도록 하곘다.

1. 간단한 인터페이스가 필요한 경우
- 하위 시스템 같은 경우에는 시간이 지날수록 클래스의 수가 증가할 수 밖에 없기 때문에 복잡해질 수 밖에 없다
    - 클라이언트 요구에 부합하면서 하위 시스템에서 자주 사용되는 기능들을 묶어 제공하는 것이다.
2. 하위 시스템을 계층들로 구성하는 경우
- 하위 시스템을 묶은 퍼사드를 통해서만 통신하도록 하여 하위 시스템 간의 결합도를 줄여줄 수 있다.

위와 같은 상황들에 퍼사드 패턴을 적용하여 복잡한 하위 시스템에서 코드를 별도로 분리하는 이점이 생기게 된다.


## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

해당 코드는 상품을 구매하는 과정을 퍼사드 패턴으로 나타낸 코드이다.


### Goods 클래스
{% highlight ruby %}
public class Goods {
    private String name = "";

    public Goods(String name) {
        this.name = name;
    }

    public void selectGoods(){
        System.out.println(name + " 상품을 선택하였습니다.");
    }
}
{% endhighlight %}

상품을 나타내는 클래스이다.

상품이 선택되었다고 출력해주는 메서드가 존재한다.

### Charge 클래스
{% highlight ruby %}
public class Charge {
    private String name = "";

    public Charge(String name) {
        this.name = name;
    }

    public void chargeGoods(){
        if (!(name == "카드" || name == "현금")) {
            System.out.println(name + "-> 잘못된 결제 방식 입니다.");
            return;
        }
        System.out.println(name + "결제입니다.");
    }
}
{% endhighlight %}

결제종류를 나타내는 클래스이다.

결제 방식을 출력해주는 메서드가 존재한다.

### PickUp 클래스
{% highlight ruby %}
public class PickUp {
    private String name = "";

    public PickUp(String name) {
        this.name = name;
    }

    public void pickUp(){
        if (name == "포장" || name == "배달") {
            System.out.println(name + "입니다.");
            return;
        }
        System.out.println("잘못된 픽업입니다.");
    }
}
{% endhighlight %}

픽업방식을 나타내는 클래스이다.

픽업 방식을 출력해주는 메서드가 존재한다.

### Facade 클래스
{% highlight ruby %}
public class Facade {
    private String goodsName = "";
    private String chargeName = "";
    private String pickUpName = "";

    public Facade(String goodsName, String chargeName, String pickUpName) {
        this.goodsName = goodsName;
        this.chargeName = chargeName;
        this.pickUpName = pickUpName;
    }

    public void getGoods(){
        Goods goods = new Goods(goodsName);
        Charge charge = new Charge(chargeName);
        PickUp pickUp = new PickUp(pickUpName);

        goods.selectGoods();
        charge.chargeGoods();
        pickUp.pickUp();
    }
}
{% endhighlight %}

퍼사드 클래스로서 가장 중요한 역할을 하는 클래스이다.

여러가지 복잡한 서브 클래스들에 대해서 인스턴스를 가지고 있고, <mark>getGoods()</mark>메서드를 통해서 여러 서브 클래스의 복잡한 호출 방식을 구현해내었다.

### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        Facade facade = new Facade("사이다", "카드", "배달");
        facade.getGoods();
    }
}
    /** 출력 결과
        * 사이다 상품을 선택하였습니다.
        * 카드결제입니다.
        * 배달입니다.
        **/
{% endhighlight %}

해당 코드는 퍼사드 패턴을 사용하는 사용자 입장을 나타낸 코드입니다.

해당 코드를 보았을 때 사용자 입장에서는 해당 서브 클래스들의 내용을 알 필요 없이 매개변수를 입력해주면, <mark>Facade</mark>클래스를 통해서 복잡한기능을 수행하는 것을 볼 수 있다.

클라이언트가 복잡한 내용을 모르더라도 <mark>Facade</mark>클래스를 통해서 복잡한 기능을 구현해 놓았기 때문에 편하게 사용할 수 있음을 알 수 있다.



## 단점
- 퍼사드 패턴을 사용하면 Facade클래스는 앱의 모든 클래스에 결합된 없어서는 안될 객체가 될 수 있다.

해당 단점은 치명적일 수 있기 때문에 너무 만능인 것 처럼 사용하면 안된다.

## 결론

퍼사드 패턴은 복잡한 하위 시스템에 대한 간단한 인터페이스를 제공하는 패턴이다.

디자인 패턴을 공부한다면 꼭 반드시 기억해야하는 중요한 패턴이라고 생각이 된다. 실제로 매우 많은 경우에서 사용되는 것으로 알고 있고, 유명한 것으로 알고 있다.

실제로 공부를 해보면 알겠지만 어려운 내용이 절대 아니기 때문에 Facade 클래스를 인터페이스로 만들어 기능을 하나로 만들어주는 개념을 잘 안다면 충분히 잘 공부한 것이라고 생각든다.