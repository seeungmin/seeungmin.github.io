---
layout: post
title:  "데코레이터 패턴(Decorator Pattern)"
tags: [Disign Pattern, Structural Pattern]
date:   2023-07-14 15:30:00 +0900
toc:  true
---

## Decorator Pattern

데코레이터 패턴이란 집합 관계와 위임을 사용하여 기능을 동적으로 유연하게 확장 시킬 수 있는 패턴이다.

간단하게 말하면 데코레이터란 말 그대로 장식으로 받아들이면된다. 기존 클래스에 변경 없이 장식들을 만들어 붙이는 방법이라고 생각하면 된다.


## 사용 이유
이와 같은 데코레이터 패턴을 사용하는 이유는 여러가지가 있는데 한 번 알아보도록 하겠다.


1. 객체 관점
- 새로운 자식 클래스를 생성하지 않고 객체의 기능을 확장 가능하다.
- 객체를 여러개의 데코레이터로 래핑을 함으로써 여러 행동들을 합성하여 나타낼 수 있다.
- 실행중인 프로그램에서 객체들로부터 책임을 추가, 제거가 가능하다.
2. 객체지향의 원칙 관점
- 다양한 행동들을 여러 클래스로 분리하여 나타낼 수 있다.
- 클래스의 기존 기능을 건드리지 않고, 기능등을 확장시킬 수 있다.


## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

간단하게 데코레이터 패턴을 사용하여 햄버거를 치즈버거와 그냥 햄버거를 만드는 코드를 작성해 보았다.

### 재료 추가 인터페이스
{% highlight ruby %}
public interface Component {
    public String add();
}
{% endhighlight %}
햄버거를 제조할 때 여러가지 재료를 추가해야합니다. 해당 인터페이스를 상속함으로써 <mark>add()</mark>메서드를 통해 재료를 추가할 수 있도록 해줍니다.

### 기본 제품 클래스
{% highlight ruby %}
public class BaseComponent implements Component{
    @Override
    public String add() {
        return "빵 패티";
    }
}
{% endhighlight %}
햄버거의 가장 기본이 되는 재료들을 추가해주는 클래스이다. 가장 기초되는 것들을 추가해주는 단계로서 여러가지 데코레이터를 통해 해당 객체로부터 늘려나갈 수 있다.

### 데코레이터 추상 클래스

{% highlight ruby %}
abstract public class Decorator implements Component{
    private Component component;

    public Decorator(Component component){
        this.component = component;
    }

    @Override
    public String add() {
        return component.add();
    }
}

{% endhighlight %}

해당 데코레이터는 Component 인터페이스를 상속받고 있다.

데코레이터 추상 클래스는 앞으로 추가될 재료 즉 데코레이터들이 상속을 받는 클래스로, 해당 데코레이터를 통해 재료를 추가해주는 것이다.

### 데코레이터 클래스
{% highlight ruby %}
public class CheeseDecorator extends Decorator{
    public CheeseDecorator(Component hambergerComponent){
        super(hambergerComponent);
    }

    @Override
    public String add() {
        return super.add() + " 치즈 ";
    }
}
{% endhighlight %}

{% highlight ruby %}
public class LettuceDecorator extends Decorator{
    public LettuceDecorator(Component hambergerComponent){
        super(hambergerComponent);
    }

    @Override
    public String add() {
        return super.add() + " 양배추 ";
    }
}
{% endhighlight %}

각각의 코드는 치즈, 양배추를 추가해주는 데코레이터 클래스이다. 각각 Decorator 추상클래스를 상속받아 <mark>add()</mark>메서드를 구현하고 있다.


### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        Component hamberger = new LettuceDecorator(new BaseComponent());
        Component cheeseberger = new CheeseDecorator(new BaseComponent());

        System.out.println("cheeseberger = " + cheeseberger.add());
        System.out.println("hamberger = " + hamberger.add());
    }
}
    /** 출력 결과
        * cheeseberger = 빵 패티 치즈 
        * hamberger = 빵 패티 양배추
        **/
{% endhighlight %}

해당코드는 데코레이터 패턴을 사용하여 기본 Component에 재료를 추가해주는 것을 나타낸 코드이다.

각각 치즈, 양배추를 추가해주는 데코레이터 패턴을 생성해준다.

같은 Component를 구현하고 있지만, 구현한 데코레이터에 따라 추가되는 것이 다른것을 볼 수 있다.

## 단점
- 데코레이터 패턴을 사용할 때 데코레이터 객체와 실제 구현 객체를 구분하기 어렵기 때문에, 코드만으로 기능이 어떻게 동작하는지 이해하기 어려울 수 있다.
- 어떤 데코레이터를 통해서 먼저 데코레이팅 하느냐에 따라 스택 순서가 결정되기 때문에, 순서에 의존하지 않도록 코드를 작성해야 하는 경우 매우 어려울 것이다.

## 결론
데코레이터 패턴의 가장 중요하다고 생각되는 부분은 객체지향의 원칙에서 개방폐쇄의 원칙을 지키는 것이라고 생각한다.

기존의 기능을 건드리지 않고 기능을 언제나 확장이 가능하다는 장점과 모놀리식하게 코드를 작성할 수 있다는 것 때문에 사용한다고 생각된다.

크게 어려운 부분은 없다고 생각이 들고 패턴의 이름처럼 장식이라고 생각하면 좋을 것 같다.