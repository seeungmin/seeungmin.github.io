---
layout: post
title:  "팩토리 메서드 패턴(Factory Method Pattern)"
tags: [Disign Pattern, Creational Pattern]
date:   2023-07-03 11:03:35 +0900
toc:  true
---

## Factory Method Pattern

팩토리 메서드 패턴은 클라이언트에서 사용할 클래스의 객체를 생성하는 책임을 분리하여 객체 생성에 변화를 대비하는 것이다. 팩토리 메서드를 포함하는 인터페이스를 만들어 하위 클래스에서 인스턴스를 생성해주는 방식이다.

클라이언트에서 직접 **new**연산자를 통해 제품의 객체를 생성하는 것이 아니라 제품 객체들을 맡아서 생성하는 공장 클래스를 생성하고, 이를 상속하고 있는 서브 공장 클래스의 메서드에서 여러가지 제품을 객체를 생성하는 책임을 지고 있다.

## 사용 이유
이와 같은 팩토리 메서드 패턴을 사용하는 이유는 여러가지가 있는데 한 번 알아보도록 하겠다.


1. 캡슐화, 추상화
- 팩토리 메서드 패턴을 사용하여 생성자와 구현 객체간의 강한 결합을 피할 수 있고, 코드가 동작해야 하는 객체의 유형과 종속성을 정보 은닉 처리하게 하여 구체적인 타입을 감출 수 있다.
2. 객체지향의 원칙 관점
- 객체를 생성하는 코드를 하나의 클래스 또는 패키지로 이동하여 작성하기 때문에 유지보수성이 높아진다.
- 기존 코드를 수정하지 않고 새로운 제품 인스턴스를 프로그램에 원활하게 추가가 가능하다.
3. 리소스 관점
- 객체를 생성하는 코드는 자주 중복될 가능성이 존재한다. 또한 객체를 생성하는 방식의 변화에 해당하는 모든 코드를 변경해야 하는 문제가 발생가능하다.
- 이러한 문제를 해결하기 위해 객체를 생성하는 코드를 클래스 또는 메서드로 분리함으로써 유지보수성을 높이기 위해 이 패턴을 사용한다.

## 단점
여러가지 장점이 존재하지만 단점 또한 존재한다.

1. 복잡성
- 추상화와 캡슐화를 많이 사용하기 때문에 코드의 복잡도가 증가할 수 밖에 없다.
2. 서브 클래스의 수
- 각 제품의 구현체마다 팩토리 객체들을 모두 구현해주어야 하기 때문에, 제품이 증가할 때마다 팩토리 클래스가 증가해 서브 클래스의 수가 매우 많아진다.

## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

팩토리 메서드 패턴에 대한 기본 개념을 위한 코드로 간단하게 객체를 생성하면 해당 객체에 대한 설명을 출력하도록 하였다.

### 제품 객체 추상화
{% highlight ruby %}
public interface Book {
    void explain();
}
{% endhighlight %}
제품 객체를 추상화 해둔 인터페이스이다.

### 제품 구현체 GEBook, MajorBook
{% highlight ruby %}
public class GEBook implements Book{
    @Override
    public void explain() {
        System.out.println("교양 책입니다.");
    }
}
{% endhighlight %}

{% highlight ruby %}
public class MajorBook implements Book{
    @Override
    public void explain() {
        System.out.println("전공 책입니다.");
    }
}
{% endhighlight %}

위의 두 제품 구현체는 모두 Book 인터페이스를 상속하고 해당되는 <mark>explain()</mark>메서드를 작성한다.

### 공장 객체 추상화
{% highlight ruby %}
public interface BookFactory {
    default Book explainBook(){
        Book book = createBook();
        book.explain();
        return book;
    }

    Book createBook();
}
{% endhighlight %}
공장 객체를 추상화해둔 인터페이스이다.

<mark>createBook()</mark>메서드는 팩토리 메서드로 구체적인 객체 생성 종류는 각 서브 클래스에 위임하는 것을 볼 수 있다.

<mark>explainBook()</mark>메서드는 객체 생성 전처리, 후처리를 해주는 메서드이다. 제품 객체를 생성하고 추가 설정하고 완성된 제품을 반환시켜준다. 또한 해당 메서드에서<mark>createBook()</mark>을 호출하여 제품 객체를 생성해주는 것을 볼 수 있다.

### 공장 객체 GEBook, MajorBook
{% highlight ruby %}
public class GEBookFactory implements BookFactory{
    @Override
    public Book createBook() {
        return new GEBook();
    }
}
{% endhighlight %}

{% highlight ruby %}
public class MajorBookFactory implements BookFactory{
    @Override
    public Book createBook() {
        return new MajorBook();
    }
}
{% endhighlight %}

위의 두 공장 객체 모두 BookFactory를 상속하고 있고, 각각의 맞는 객체를 생성하여 반환시켜주고 있다.

### 기본형 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        BookFactory[] bookFactories = { new MajorBookFactory(), new GEBookFactory() };

        Book majorBook = bookFactories[0].explainBook();
        Book gEBook = bookFactories[1].explainBook();
    }
}
    /** 출력 결과
        * 전공 책입니다.
        * 교양 책입니다.
        **/
{% endhighlight %}

사용자의 입장에서 각각의 제품을 생성하는 코드이다.

우선 공장 객체 배열을 생성하여 각각의 공장 객체를 넣어주는 것을 볼 수 있다. 그후 공장객체 배열에서 각각에 해당하는 제품을 생성한다.

전체적인 코드의 흐름이 어려울 수 있으니 하나씩 확인해가며 넘어가도록 하겠다.

1. 제품을 생성할때를 보면 <mark>bookFactories[0].explainBook()</mark>해당 코드에서 <mark>createBook()</mark>메서드와 생성 후처리를 실행해준다.
2. 공장 객체를 추상화한 인터페이스의 default 메서드로 넘어간다.
3. <mark>createBook()</mark>메서드를 호출하고 원하는 공장 객체로 넘어간다.
4. 공장 객체에선 해당되는 제품을 반환시켜 준 후에 default메서드로 돌아가 추상화한 Book 객체를 채워준다.
5. <mark> book.explain()</mark>코드의 book은 공장 객체로 부터 반환받은 객체이기 때문에 <mark>MajorBook</mark>이 된다.
6. <mark>MajorBook</mark>의 <mark>explain()</mark>메서드를 호출하고 해당 객체를 반환시켜준다.

코드를 보고 말로 설명한 것이기 때문에 이해를 돕기에는 어려울 수 있지만 천천히 코드를 따라가 본다면 충분히 이해할 수 있는 내용들이다.

## 결론
해당 패턴에 대해서는 초반에 이해가 되지 않는 내용들이 매우 많았다.
그렇지만 패턴에 대하여 공부하고 코드를 작성해 본 결과, 어렵게 생각하지 않고 쉽게 생각하여 결합도를 낮추고 유지보수를 편하게 하기 위해서 <mark>new</mark>연산자를 직접 사용하지 않고 객체를 만들어 관리한다고 생각하니 이해하기가 좀 수월해진 것 같다.

위의 작성된 코드는 매우 간단한 코드이기 때문에 추후 실력 향상을 위해 해당 코드의 기능들을 몇가지 추가해 볼 예정이다.


