---
layout: post
title:  "싱글톤 패턴(Singleton Pattern)"
tags: [Disign Pattern, Creational Pattern]
date:   2023-07-02 11:03:35 +0900
toc:  true
---

## Singleton Pattern 

싱글톤 패턴을 따르는 클래스는 생성자가 여러 차례 호출이 되더라도 실제로 생성된 객체는 오직 하나이라는 것이다.
처음 호출한 이후에 호출된 생성자는 처음 생성자가 생성한 객체를 반환해준다.

## 사용 이유
 
특정 클래스의 객체가 오직 한 개만 존재하도록 보장해주는 싱글톤 패턴을 사용하는 이유를 알아보자.

1. 메모리 관점
- 하나의 인스턴스를 고정 메모리 영역에 생성하고 접근하기 때문에 추후에 접근하는 경우 메모리 낭비를 방지할 수 있다.
2. 성능 관점
- 이미 생성되어 있는 인스턴스를 접근하여 사용하기 때문에 새로 생성하는 것보다 속도 측면에서 빠르다.
3. 데이터 공유 관점
- 하나의 인스턴스를 전역으로 사용하기 때문에 다른 여러 클래스에서 데이터를 공유하며 사용할 수 있다. 그러나 이는 동시성 문제가 발생할 가능성이 있기 때문에 유의하여 설계하여야 한는데 추후에 설명하도록 하겠다.


## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

### 기본코드
{% highlight ruby %}
public class Singleton {
    // 1개만 존재해야 하는 객체의 인스턴스를 static 으로 선언
    private static Singleton instance;

    // private 으로 생성자를 선언해 외부에서 객체를 생성하는 것을 막음
    private Singleton(){ }

    // 외부에서 getInstance 함수를 통하여 instance 를 반환
    public static Singleton getInstance() {
        // instance 가 없을 때만 생성
        if (instance == null)
            instance = new Singleton();

        return instance;
    }
}
{% endhighlight %}
위의 코드는 싱글톤 패턴의 가장 기본적인 형태라고 할 수 있다.

우선적으로 <mark>private static</mark>으로 객체의 instance를 선언해준다. 이것을 바탕으로 <mark>getInstance()</mark>메서드가 처음 실행되는 경우에는 instance가 생성되고 그 후에는 기존에 생성한 instance를 반환해주는 방식으로 진행된다고 보면 된다.

기본 생성자를 <mark>private</mark>으로 선언하여 외부에서 새로운 객체를 생성하지 못 하도록 해주는 것이 가장 중요하다고 생각하면 됩니다.
### 기본코드 test
{% highlight ruby %}
public class Test{
    public static void main(String[] args) {
        Singleton singleton1 = Singleton.getInstance();
        Singleton singleton2 = Singleton.getInstance();

        System.out.println("singleton1 = " + singleton1);
        System.out.println("singleton2 = " + singleton2);
    }
}
    /** 출력 결과
         * singleton1 = CreationalPattern.Singleton.Singleton@1b6d3586
         * singleton2 = CreationalPattern.Singleton.Singleton@1b6d3586
         **/
{% endhighlight %}
싱글톤 객체를 생성하는 해당 코드를 실행해본 결과 두 객체의 주수 값이 일치하는 것을 확인 할 수 있고, 이를 통해 하나의 인스턴스를 사용한다는 것을 알 수 있다.

**그러나 위와 같은 기본형 코드는 실제로 사용하기에는 여러가지 문제점이 존재한다. 이러한 문제점을 알아보고 개선한 코드를 살펴보도록 하겠다.**

### 문제점
다중 쓰레드 환경에서 위와 같은 코드로 싱글톤 패턴을 사용하게 된다면 여러가지 문제가 발생할 수 있다.

1. 여러개의 인스턴스
- 다중 쓰레드 환경에서 instance가 없을 때 여러 쓰레드에서 동시에 <mark>getInstance()</mark>메서드를 실행하는 경우 각각의 쓰레드에서 새로운 instance를 생성할 수 있다.
2. 변수 값에 대한 일관성
- 여러개의 쓰레드가 동시에 인스턴스에 존재하는 변수의 값을 바꾸는 상황에는 서로 일관되지 않은 값들이 생길 수도 있다.

위와 같은 문제들을 해결하기 위한 방법들을 보여주도록 하겠다.


### 문제점 개선코드1
synchronzied 사용

{% highlight ruby %}
public class Singleton1 {
    private static Singleton1 instance;

    private Singleton1(){ }

    public static synchronized Singleton1 getInstance(){
        // instance 가 없을 때만 생성
        if (instance == null)
            instance = new Singleton1();

        return instance;
    }
}

{% endhighlight %}
<mark>synchronized</mark>를 적용하여 다중 쓰레드에서의 동시성 문제를 해결해준다. 하지만 이러한 방법은 쓰레드의 안정성 문제를 보장하기 위해서 성능 저하가 가능하기 때문에 잘 선택하여 사용해야 한다.

그렇기 때문에 성능 저하를 개선해야하는 문제점이 존재하게 된다. 이는 다음 코드에서 설명하도록 하겠다.

### 문제점 개선코드2
객체 인스턴스를 선언과 동시에 생성

{% highlight ruby %}
public class Singleton2 {
    // 초기에 static 변수로 singleton 인스턴스를 생성
    private static Singleton2 instance = new Singleton2();

    private Singleton2(){ }

    public static Singleton2 getInstance(){
        return instance;
    }
}
{% endhighlight %}
우선적으로 <mark>private static</mark>으로 객체의 instance를 선언해줌과 동시에 생성을 해준다. 이렇게 되면 다중 쓰레드 환경에서도 다른 객체들은 <mark>getInstance()</mark>를 통해서 하나의 인스턴스를 공유가능하게 한다.

<mark>synchronized</mark>를 사용하지 않고 동시성 문제를 해결하는 코드를 완성했다. 하지만 그럼에도 불구하고 문제점이 존재한다.

위와 같이 코드를 작성하면 초반에 생성과 동시에 선언 해주기 때문에 서버 초반 로딩시간이 길어지는 문제가 발생이 가능하다. 이러한 문제를 보완한 코드를 밑에서 설명하겠다.

### 문제점 개선코드3
synchronzied 사용범위를 수정

{% highlight ruby %}
public class Singleton3 {
    private static Singleton3 instance;

    private Singleton3(){ }

    public static Singleton3 getInstance(){
        if (instance == null){
            synchronized (Singleton3.class){
                instance = new Singleton3();
            }
        }

        return instance;
    }
}

{% endhighlight %}
<mark>synchronized</mark>를 적용하여 다중 쓰레드에서의 동시성 문제를 해결해준다. 

그러나 위의 개선코드1과 다른점은 <mark>getInstance()</mark>메서드 전체에 <mark>synchronized</mark>를 적용하는 것이 아닌 호출된 후에 <mark>instance == null</mark>인 경우에만 적용하여 성능을 저하시키는 문제까지 해결한 코드이다.

### 개선코드 test
{% highlight ruby %}
public class Test{
    public static void main(String[] args) {
        Singleton1 singleton11 = Singleton1.getInstance();
        Singleton1 singleton12 = Singleton1.getInstance();
        Singleton2 singleton21 = Singleton2.getInstance();
        Singleton2 singleton22 = Singleton2.getInstance();
        Singleton3 singleton31 = Singleton3.getInstance();
        Singleton3 singleton32 = Singleton3.getInstance();

        System.out.println("singleton11 = " + singleton11);
        System.out.println("singleton12 = " + singleton12);
        System.out.println("singleton21 = " + singleton21);
        System.out.println("singleton22 = " + singleton22);
        System.out.println("singleton31 = " + singleton31);
        System.out.println("singleton32 = " + singleton32);
    }
}
    /** 출력 결과
        * singleton11 = CreationalPattern.Singleton.Singleton1@4554617c
        * singleton12 = CreationalPattern.Singleton.Singleton1@4554617c
        * singleton21 = CreationalPattern.Singleton.Singleton2@74a14482
        * singleton22 = CreationalPattern.Singleton.Singleton2@74a14482
        * singleton31 = CreationalPattern.Singleton.Singleton3@1540e19d
        * singleton32 = CreationalPattern.Singleton.Singleton3@1540e19d
         **/
{% endhighlight %}
위의 코드는 여러가지 문제점을 개선하여 싱글톤 패턴을 만족시키는 코드를 테스트하는 코드이다.

싱글톤 객체를 생성하는 해당 코드를 실행해본 결과 두 객체의 주수 값이 일치하는 것을 확인 할 수 있고, 이를 통해 하나의 인스턴스를 사용한다는 것을 알 수 있다.

## 결론
싱글톤 패턴은 해당 클래스의 인스턴스를 하나로 유지하여 성능을 개선하여 사용하자는 패턴으로 생각보다 간단한 패턴이다.

하지만 간단하다고 생각하여 코드를 작성하면 생각보다 여러가지 문제가 존재할 수 있어 여러가지 경우를 생각하여 코드를 작성을 해야할 필요가 있다.