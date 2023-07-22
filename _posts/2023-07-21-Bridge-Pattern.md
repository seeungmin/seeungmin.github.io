---
layout: post
title:  "브릿지 패턴(Bridge Pattern)"
tags: [Disign Pattern, Structural Pattern]
date:   2023-07-21 15:30:00 +0900
toc:  true
---

## Bridge Pattern

브릿지 패턴은 추상화된 부분과 실제 구현 부분을 독립적으로 확장 가능하게 하는 패턴이다. 이때 객체에서 동작을 처리하는 구현부와 확장을 위한 추상부를 분리해서 코드를 작성하게 된다.


## 사용 이유

이와 같은 브릿지 패턴을 사용하는 가장 큰 이유는 **기능을 독립적으로 확장 및 변경이 가능**하다는 것이다. 이에 따른 이점들은 밑에 설명해보도록 하겠다.

1. 객체지향원칙의 이점
- 추상화의 상위 수준의 논리와 구현으로 각 세부정보에 집중할 수 있다.
- 새로운 추상화들과 구현들을 기존 코드의 수정없이 확장해 나갈 수 있다.
2. 런타임
- 브릿지 패턴을 통해 런타임 시점에 어떻게 기능을 구현할지 선택할 수 있다.
3. 정보은닉
- 기능을 독립적으로 확장할 수 있기 때문에 상세한 기능을 외부로부터 숨길 수 있다.

위와 같은 특징과 장점들 덕분에 브릿지 패턴을 사용하고 있다.


## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

해당 코드는 브릿지 패턴을 사용하여 우유를 제조할때 특정 카테고리와 지방의 종류를 선택하여 생성하도록 하는 간단한 코드를 작성해 본 것이다.


### Fat 인터페이스
{% highlight ruby %}
public interface Fat {
    public String getDensity();
}
{% endhighlight %}
우유의 지방을 나타내는 인터페이스이다.

### LowFat, HighFat 구체클래스
{% highlight ruby %}
public class LowFat implements Fat{
    @Override
    public String getDensity() {
        return "저지방";
    }
}
{% endhighlight %}
{% highlight ruby %}
public class HighFat implements Fat{
    @Override
    public String getDensity() {
        return "고지방";
    }
}
{% endhighlight %}
Fat 인터페이스를 구현하고 있는 구체 클래스들이다

고지방과 저지방으로 나뉘어 서로 다른 역할을 하고 있는 것을 볼 수 있다.

### Milk 추상클래스
{% highlight ruby %}
public abstract class Milk {
    public Fat fat;

    public Milk(Fat fat){
        this.fat = fat;
    }

    public abstract String getMilk();
}
{% endhighlight %}
기능 계층에서 최상위에 해당하는 클래스이다.

구현 부분에 해당하는 클래스인 Fat을 인스턴스로 가지고 있다. 또한 구현부의 메서드를 호출하고 있다.

### SeoulMilk, YonseiMilk 구현 클래스
{% highlight ruby %}
public class SeoulMilk extends Milk{
    private String category = "서울";

    public SeoulMilk(Fat fat){
        super(fat);
    }
    @Override
    public String getMilk() {
        return fat.getDensity() + " " + category + "우유입니다.";
    }
}
{% endhighlight %}
{% highlight ruby %}
public class YonseiMilk extends Milk{
    private String category = "연세";

    public YonseiMilk(Fat fat){
        super(fat);
    }
    @Override
    public String getMilk() {
        return fat.getDensity() + " " + category + "우유입니다.";
    }
}
{% endhighlight %}

해당 클래스들은 Milk 추상클래스를 구현한 클래스들이다.

각각 종류에 따라서 클래스의 구현을 나눠놓은 모습이다.

그러나 Milk의 Fat을 나타내는 기능에 대한 구현 부분은 따로 분리하여 코드로 작성한 것을 알 수 있다. 이를 통해서 지방과 우유종류가 각각 독립적으로 변형이 가능하게 만들어 놓았다.


### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        Milk yonseiMilk = new YonseiMilk(new LowFat());
        Milk seoulMilk = new SeoulMilk(new HighFat());

        System.out.println("yonseiMilk = " + yonseiMilk.getMilk());
        System.out.println("seoulMilk = " + seoulMilk.getMilk());
    }
}
    /** 출력 결과
        * yonseiMilk = 저지방 연세우유입니다.
        * seoulMilk = 고지방 서울우유입니다.
        **/
{% endhighlight %}

해당 코드는 브릿지 패턴을 적용한 예시로 우유의 종류와 우유의 지방의 종류를 각각 분리하여 구현한 코드입니다.

서로 다른 우유를 만들 때 각각<mark>LowFat()</mark>객체와  <mark>HighFat()</mark>객체를 생성하여 구현을 하였다.

<mark>Milk</mark>추상 클래스를 구현할 때 <mark>Fat</mark>클래스의 구현을 독립적으로 영향받고 있는 것을 확인할 수 있다.

위의 결과에서는 나오지 않았지만 YonseiMilk, SeoulMilk두 클래스를 같은 Fat으로 구현했다면 두 클래스는 같은 Fat을 가지고 있었을 것이라고 추측이 가능하다.

## 단점
- 결합도가 높은 클래스에 패턴을 적용하기 때문에 코드를 복잡하게 만들 가능성이 존재합니다.

단점이 한가지 존재하기는 하지만 당연한 말이기 때문에 한 번 읽어보면 충분히 이해 가능한 내용이다.

## 결론
브릿지 패턴은 추상화에서 끝내지 않고 두개의 계층구조 즉 추상화와 구현으로 나눈뒤에 각각을 독립적으로 개발할 수 있도록 만드는 패턴이다.

추상화나 구현과 같은 단어들이 명확하지 않아 말로만 봐서는 이해하기 어려울 수 있지만, 코드를 보면서 공부한다면 이해를 돕기에 더 수월할 것이다.

결국 해당 패턴은 기능을 확장해서 독립적으로 구현하는 것이 가장 중요하기 때문에 이 부분만 잘 생각하면 될 것이다.