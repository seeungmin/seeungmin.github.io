---
layout: post
title:  "추상 팩토리 패턴(Abstract Factory Pattern)"
tags: [Disign Pattern, Creational Pattern]
date:   2023-07-08 17:30:00 +0900
toc:  true
---

## Abstract Factory Pattern

추상 팩토리 패턴은 구체적인 클래스를 지정하지 않고 관련성을 갖는 객체들의 집합을 생성해주는 패턴이다. 객체를 사용할 클라이언트에서 구체적인 객체 생성을 지정하는 책임을 분리하기 위해서 추상 인터페이스를 사용해 관련된 객체 집합을 생성해준다.

## 사용 이유
이와 같은 추상 팩토리 패턴을 사용하는 이유는 여러가지가 있는데 한 번 알아보도록 하겠다.


1. 객체 생성 관점
- 실제 객체가 무엇인지 알지 못하더라도 객체를 생성하고 조작할 수 있도록 해서 객체가 생성되거나 구성되는 방식과는 무관하게 시스템을 독립적으로 만들 수 있게 해준다.
2. 객체지향의 원칙 관점
- 객체 생성 코드를 한 곳으로 추출하여 단일 책임의 원칙을 지켜서 유지보수성이 높아진다.
- 수정에는 닫혀있고 확장에는 열려있어 개방 폐쇄의 원칙을 지켜서 유지보수성이 높아진다.


## 팩토리 메서드 패턴과의 차이점

<a href="https://seeungmin.github.io/2023/07/03/Factory-Method-Pattern/">팩토리 메서드 패턴</a>과 비슷하다고 느낄 수 있지만 큰 차이점이 있다.

- 팩토리 메서드 패턴은 어떤 객체를 생성 할지에 집중을 하고 있고, 추상 팩토리 패턴은 연관된 객체들의 집합을 만드는 것에 집중한다는 것이다.

또한, 사용하는 목적에 따라 조금 다르다.

- 팩토리 메서드 패턴은 구체적인 객체를 생성하는 과정을 하위 또는 구체적인 클래스로 옮기는 것이 목적이고, 추상 팩토리 패턴은 관련있는 여러 객체를 구체적인 클래스에 의존하지 않고 만들 수 있게 하는 것이 목적입니다.


## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

추상 팩토리 패턴에 대한 기본 개념을 위한 코드로 간단하게 팩토리에 따라 객체를 생성할 수 있는 코드를 작성해 보았다.

### 객체 추상화
{% highlight ruby %}
public interface Professor {
    public void explain();
}
{% endhighlight %}

{% highlight ruby %}
public interface Student {
    public void explain();
}
{% endhighlight %}
Professor와 Student 각각의 인터페이스이다.

### Professor 객체 구현체
{% highlight ruby %}
public class CEProfessor implements Professor{
    public void explain(){
        System.out.println("CE 교수입니다.");
    }
}
{% endhighlight %}

{% highlight ruby %}
public class SWProfessor implements Professor{
    public void explain(){
        System.out.println("SW 교수입니다.");
    }
}
{% endhighlight %}

두 구현체 모두 Professor 인터페이스를 상속하고 해당되는 <mark>explain()</mark>메서드를 작성한다.

### Student 객체 구현체
{% highlight ruby %}
public class CEStudent implements Student{
    public void explain(){
        System.out.println("CE 학생입니다.");
    }
}
{% endhighlight %}

{% highlight ruby %}
public class SWStudent implements Student{
    public void explain(){
        System.out.println("SW 학생입니다.");
    }
}
{% endhighlight %}

두 구현체 모두 Student 인터페이스를 상속하고 해당되는 <mark>explain()</mark>메서드를 작성한다.

### 공장 추상화
{% highlight ruby %}
public interface PersonFactory {
    Professor createProfessor();
    Student createStudent();
}
{% endhighlight %}
공장 객체를 추상화해둔 인터페이스이다.

각각의 <mark>create...()</mark> 메서드를 사용하여 Professor와 Student 객체를 생성해줄 수 있게 한다.


### 공장 객체 CEPersonFactory, SWPersonFactory
{% highlight ruby %}
public class CEPersonFactory implements PersonFactory{
    @Override
    public Professor createProfessor() {
        return new CEProfessor();
    }

    @Override
    public Student createStudent() {
        return new CEStudent();
    }
}
{% endhighlight %}

{% highlight ruby %}
public class SWPersonFactory implements PersonFactory{
    @Override
    public Professor createProfessor() {
        return new SWProfessor();
    }

    @Override
    public Student createStudent() {
        return new SWStudent();
    }
}
{% endhighlight %}

위의 두 공장 객체 모두 PersonFactory를 상속하고 있다.

또한, 같은 Professor와 Student 객체를 생성하지만, 각각의 공장에 따라서 분류별로 객체를 생성하는 것을 볼 수 있다.

### 기본코드 test
{% highlight ruby %}
public class Client {

    public static void main(String[] args) {
        create(new CEPersonFactory());
        create(new SWPersonFactory());
    }

    private static void create(PersonFactory personFactory){
        Professor professor = personFactory.createProfessor();
        Student student = personFactory.createStudent();

        professor.explain();
        student.explain();
    }
}
    /** 출력 결과
        * CE 교수입니다.
        * CE 학생입니다.
        * SW 교수입니다.
        * SW 학생입니다. 
        **/
{% endhighlight %}
추상 팩토리 패턴을 사용하여 각각의 팩토리를 넘겨받아 객체를 생성하는 것을 보여주는 코드이다.

해당 코드를 통해서 각각의 객체들은 구체 클래스가 아닌 인터페이스에 의존하여 작성되고 있음을 알 수 있다.

또한 클라이언트는 **어떤 Factory를 넘겨주는지에 관계없이** 각각의 Professor, Student를 생성하여 사용할 수 있음을 알 수 있다.

## 단점
여러가지 장점도 많지만 그와 상반되는 단점들도 존재하게 되는데 하나하나 알아보도록 하겠다.

1. 새로운 군집 추가
- 기존 군집에서 새로운 군집을 추가해서 확장을 하는 경우에 모든 서브 클래스들이 동시에 변경되어야 하기 때문에 어려움이 있다.
2. 관리 그룹의 증가
- 추상 팩토리는 팩토리 메서드보다 관리할 그룹이 많은데 계층의 크기가 커질수록 복잡한 문제가 발생하는 경우가 증가한다.

## 결론
추상 팩토리 패턴은 다양한 집합이 필요하거나 해당되는 제품의 구체 클래스들에 의존하고 싶지 않은 경우 같이 특별한 경우에 사용되는 패턴이라고 생각된다.

해당 패턴은 뚜렷한 장점이 존재하고 그에 따른 단점 또한 존재하기 때문에 패턴을 사용해야 하는 코드를 잘 파악하여 상황에 맞게 쓰는것이 가장 중요하다고 생각이 든다.