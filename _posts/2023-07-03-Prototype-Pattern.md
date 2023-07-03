---
layout: post
title:  "프로토타입 패턴(Prototype Pattern)"
tags: [Disign Pattern, Creational Pattern]
date:   2023-07-03 21:03:35 +0900
toc:  true
---

## Prototype Pattern

프로토타입 패턴은 기존의 인스턴스를 복제하여 새로운 인스턴스를 만드는 방법이다. 이때 코드를 해당 클래스들에 의존시키지 않고 기존 객체들을 복제할 수 있도록 한다.

## 사용이유
이와 같은 프로토타입 패턴을 사용하는 이유는 여러가지가 있는데 한 번 알아보도록 하겠다.
1. 비용 관점
- 인스턴스를 새로 만들 때 비용이 많이 들기 때문에 비슷한 객체가 이미 있는 경우 해당 객체를 가져와 사용한다.
2. 리소스 관점
- 기존의 인스턴스를 만들 때 시간이 오래 걸린다.
- DB로부터 데이터를 읽어와 인스턴스를 생성하거나, http요청을 보내서 가져온 데이터로 인스턴스를 만드는 경우 상대적으로 오랜시간과 큰 리소스가 필요하다.
- 위와 같은 문제점들을 해결하기 위해 DB나 HTTP통신으로 가져온 데이터를 복제하여 새로운 인스턴스를 만들고 일부만 변경하여 사용한다.

## 장단점
### 장점
1. 구현 클래스에 직접 연결을 하지 않고도 객체를 복제할 수 있다.
2. 프로토타입이 미리 정의되어 있기 때문에 중복되는 초기화 코드는 제거가 가능하다.
3. 복잡한 오브젝트들을 복사를 통해 쉽게 만들 수 있다.

### 단점
1. 순환참조가 있는 복잡한 객체들 같은 경우에는 복제하기 어려울 수가 있다.

## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

해당 코드는 간단하게 db에 전공과목이 저장되어 있다고 가정하고 해당 정보를 가져와 출력하려는 간단한 코드입니다.

### 기본형
{% highlight ruby %}
public class Major implements Cloneable{

    private List<String> list;

    public Major() {
        this.list = new ArrayList<>();
    }

    public Major(List<String> list) {
        this.list = list;
    }

    public List<String> getList() {
        return list;
    }

    public void listAll() {
        this.list.add("Mobile");
        this.list.add("Network");
        this.list.add("OperatingSystem");
        this.list.add("Software");
    }

    @Override
    protected Object clone() throws CloneNotSupportedException {
        return new Major(new ArrayList<>(this.list));
    }
}
{% endhighlight %}
Java에는 인스턴스의 복사를 실행하는 도구로 <mark>clone()</mark>메서드가 정의되어 있다. 

<mark>clone()</mark>메서드를 실행할 경우에 복사 대상이 되는 클래스는<mark>java.lang.Cloneable</mark> 인터페이스를 구현해야 해서 인터페이스를 상속받아 사용하고 있다.

<mark>clone()</mark>메서드를 구현할 때 예외처리까지 해주는 것을 볼 수 있다.


### 기본형 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) throws CloneNotSupportedException{
        Major major = new Major();
        major.listAll();

        Major major1 = (Major) major.clone();
        Major major2 = (Major) major.clone();

        List<String> list1 = major1.getList();
        List<String> list2 = major2.getList();

        list1.add("Algorithm");
        list2.remove("Software");

        System.out.println("list = " + major.getList());
        System.out.println("list1 = " + list1);
        System.out.println("list2 = " + list2);
    }
}
    /** 출력 결과
        * list = [Mobile, Network, OperatingSystem, Software]
        * list1 = [Mobile, Network, OperatingSystem, Software, Algorithm]
        * list2 = [Mobile, Network, OperatingSystem]
         **/
{% endhighlight %}
위의 코드는 클라이언트의 실행 결과이다.

Major 객체를 하나 만들어 데이터를 가져와 저장을 하고, <mark>clone()</mark>메서드를 사용하여 Major1, Major2 객체에 Major로부터 복제를 하여 저장하는 것을 볼 수 있다.

복제가 잘 된 것을 보여주기 위해서 각각 객체의 리스트에 데이터를 추가 및 삭제 하여 나타내었다.

## 결론
프로토타입 패턴의 내용 자체는 크게 어려운 느낌은 없어 쉽게 이해가 가능했던 것 같다.

DB에서 빈번하게 데이터를 가져오거나 사용할 때, 매번 DB에 접근하는 것도 코드상에서는 아무 문제가 없지만 DB에 접근하는 비용이 매우 크기 때문에 복사해두고 사용하는 것이 비용 절약 측면에서 매우 중요하다고 생각을 한다.  