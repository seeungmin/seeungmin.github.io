---
layout: post
title:  "반복자 패턴(Iterator Pattern)"
tags: [Disign Pattern, Behavioral Pattern]
date:   2023-07-29 15:30:00 +0900
toc:  true
---

## Iterator Pattern

반복자 패턴은 반복이 필요한 자료구조를 모두 동일한 인터페이스를 통해 순차적인 접근을 제공하는 패턴이다. 이때 클라이언트는 반복이 필요한 자료구조를 모두 동일한 인터페이스를 통해서 접근을 할 수가 있다.

## 반복자 패턴 구조

![Class Diagram](https://github.com/seeungmin/seeungmin.github.io/blob/main/DesignPatternDiagram/%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0%20%ED%8C%A8%ED%84%B4.png?raw=true "Iterator Pattern Class Diagram")



## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

해당 코드는 반복자 패턴을 사용해서 장바구니에 화장품을 골라 담은 후 결제할 때 순서없이 꺼내는 것을 보여주는 코드입니다.


### Cosmetic 클래스

{% highlight ruby %}
public class Cosmetic {
    private String name;

    public Cosmetic(String name) {
        this.name = name;
    }

    public String getName() {
        return name;
    }
}
{% endhighlight %}

화장품을 나타내는 클래스이다.

화장품의 이름을 반환시키는 메서드가 존재한다.

### Aggregate 인터페이스
{% highlight ruby %}
public interface Aggregate {
    public Iterator createIterator();
}
{% endhighlight %}

여러 요소를 가지고 있는 집합체에 대한 인터페이스이다.

집합체를 생성해주는 메서드를 가지고 있다.

### Iterator 인터페이스
{% highlight ruby %}
public interface Iterator {
    public boolean hasNext();
    public Object next();
}
{% endhighlight %}

집합체의 요소들을 탐색하기 위한 인터페이스이다.

집합체를 검색하기 위한 메서드를 가지고 있다.

### ShoppingBasket 클래스
{% highlight ruby %}
public class ShoppingBasket implements Aggregate{
    public List<Cosmetic> cosmetics;

    public ShoppingBasket(){
        this.cosmetics = new ArrayList<>();
    }

    public void addCosmetic(String name){
        Cosmetic cosmetic = new Cosmetic(name);
        cosmetics.add(cosmetic);
    }

    @Override
    public Iterator createIterator() {
        return new ShoppingBasketIterator(this.cosmetics);
    }
}
{% endhighlight %}

장바구니에 대한 클래스이다.

장바구니에 화장품을 담아주는 메서드를 가지고 있다. 해당 클래스는 <mark>List</mark>를 사용하여 <mark>Cosmetic</mark>클래스를 인자로 가지고 관리하고 있다.

<mark>Aggregate</mark> 인터페이스를 구현하고 있다. <mark>ShoppingBasketIterator</mark>클래스를 만들어서 <mark>ShoppingBasket</mark>클래스에서 생성된 요소를 검색하려 하고 있다.

### ShoppingBasket 클래스
{% highlight ruby %}
public class ShoppingBasketIterator implements Iterator{
    private List<Cosmetic> cosmetics;
    int index = 0;

    public ShoppingBasketIterator(List<Cosmetic> cosmetics) {
        this.cosmetics = cosmetics;
    }

    @Override
    public boolean hasNext() {
        return index < cosmetics.size();
    }

    @Override
    public Object next() {
        if (hasNext())
            return cosmetics.get(index ++);
        return null;
    }
}
{% endhighlight %}

<mark>ShoppingBasket</mark>클래스의 검색을 위한 클래스이다.

<mark>Iterator</mark> 인터페이스를 구현하고 있다. <mark>ShoppingBasket</mark>클래스의 <mark>List</mark>의 요소의 갯수에 따라 넘어가며 검색해주는 메서드들을 구현한다.


### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        ShoppingBasket shoppingBasket = new ShoppingBasket();

        shoppingBasket.addCosmetic("토너");
        shoppingBasket.addCosmetic("로션");
        shoppingBasket.addCosmetic("크림");

        Iterator it = shoppingBasket.createIterator();
        while (it.hasNext()){
            Cosmetic cosmetic = (Cosmetic) it.next();
            System.out.println("화장품 : " + cosmetic.getName());
        }
    }
}
    /** 출력 결과
        * 화장품 : 토너
        * 화장품 : 로션
        * 화장품 : 크림
        **/
{% endhighlight %}

해당 코드는 반복자 패턴을 사용하는 사용자 입장에서의 코드입니다.

<mark>ShoppingBasket</mark>클래스를 선언하여 장바구니를 만듭니다.

해당 장바구니에 화장품을 하나씩 추가해준다.

<mark>Iterator</mark>를 선언한 후 장바구니에 대한 검색을 가능하게 한다. <mark>while</mark>문을 통해서 장바구니 안을 하나씩 확인해가며 출력해주는 것이다.

## 사용 이유

이와 같은 반복자 패턴을 사용하는 이유는 여러가지 경우들이 있다.

그러한 여러가지 경우들을 지금부터 하나하나씩 살펴보도록 하곘다.

1. 순회 코드의 중복을 줄이고 싶은 경우
2. 클라이언트가 구현 내용을 모르게 하고 싶은 경우
- 컬렉션이 복잡한 데이터 구조를 가지더라도 반복자 패턴을 통하여 숨길 수 있기 떄문에 사용된다.
3. 사전에 데이터 구조가 알려지지 않은 경우
- 데이터 구조를 미리 알 수 없을 때 반복자 패턴을 사용하면 어떤 구조든지 상관없이 받을 수 있다.

위와 같은 상황들에 반복자 패턴을 적용하여, 단일책임의 원칙, 개방폐쇄의 원칙등을 지킬 수 있으며 코드의 응집도 또한 높여주어 사용되고 있다.


## 단점
- 어떠한 특정 위치의 요소에 바로 접근해야 하는 경우에는 해당 패턴이 비효율적일 수도 있다.
- 간단한 컬렉션인 경우에는 반복자 패턴을 적용하면 오히려 성능이 감소할 수 있다.

## 결론

반복자 패턴은 반복이 필요한 자료구조에 상관없이 반복이 가능하게 하는 패턴으로, Iterator 인터페이스를 사용해서 패턴을 사용할 수 있다.

여러 언어에서 이미 규정되어 있는 메서드들을 사용해왔기 때문에, 간단하게 코드를 작성하고 이해하고 넘어간 패턴이다.
