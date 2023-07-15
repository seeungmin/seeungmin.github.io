---
layout: post
title:  "플라이웨이트 패턴(Flyweight Pattern)"
tags: [Disign Pattern, Structural Pattern]
date:   2023-07-15 15:30:00 +0900
toc:  true
---

## Flyweight Pattern

플라이웨이트 패턴은 여러 유사한 객체를 생성, 관리하는 비용을 절감하는 패턴이다. 이것은 각 객체에 모든 데이터를 유지하는 대신에 여러 객체들의 공통된 부분을 공유하여 RAM에 더 많은 객체를 포함 할 수 있도록 하는 것이다.


## 사용 이유
이와 같은 플라이웨이트 패턴을 사용하는 이유는 여러가지가 있는데 한 번 알아보도록 하겠다.


### 비용 절약
- 생성되는 객체의 수가 많아 저장 비용이 높아질 때 사용한다.
- 생성된 객체가 오랜시간동안 메모리에 상주하고, 자주 사용되는 경우 사용한다.
- 공통적인 인스턴스를 많이 생성하는 로직이 존재하는 경우에 사용한다.

위와 같은 경우에 플라이웨이트 패턴을 사용함으로서 애플리케이션에서 사용하는 메모리를 절약할 수 있고, 메모리를 절약하기 때문에 프로그램 속도를 개선할 수 있다. 


## 싱글톤 패턴과의 차이점
<a href="https://seeungmin.github.io/2023/07/02/Singleton-Pattern/">싱글톤 패턴</a>과 플라이웨이트 패턴은 객체를 하나만 생성하고 활용하는 패턴이라는 공통점이 있다.

그러나 이러한 두 개의 패턴은 무엇이 다른것일까?

싱글톤 패턴은 클래스 자체가 오직 1개의 인스턴스만을 허용한다.
플라이웨이트 패턴은 클래스를 팩토리에서 제어한다.

그렇기 때문에 **싱글톤패턴은 하나의 인스턴스를 생성하여 해당 변수를 변경해가며 사용하는 것**이고, **플라이웨이트 패턴은 여러종류를 가지는 대신에 각각의 인스턴스가 하나씩만 있도록 하는 것**이다.

이는 인스턴스 생성에 대한 제한을 어디에서 제어하는지에 대해서 차이가 있다고 말할 수 있다.


## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

플라이웨이트 패턴을 사용하여 색을 입력받아 해당 색의 공을 만드는 코드를 작성해보았다.

### Color 객체
{% highlight ruby %}
public class Color {
    private String color;

    public Color(String color){
        this.color = color;
    }

    @Override
    public String toString() {
        return color;
    }
}
{% endhighlight %}

공의 색을 결정해주는 객체이다.

해당 객체는 공유되는 객체로서 바뀌면 안되기 때문에 <mark>final</mark>로 선언해주었다.

### Ball 객체
{% highlight ruby %}
public class Ball {
    private Color color;
    private int diameter;

    public Ball(Color color, int diameter) {
        this.color = color;
        this.diameter = diameter;
    }

    public void setDiameter(int diameter) {
        this.diameter = diameter;
    }

    public void make(){
        System.out.println("지름이 " + diameter + " 인 " + color.toString() + "색 공을 만들었습니다.");
    }
}
{% endhighlight %}

가장 큰 범위의 객체로 Color객체를 변수로 가지고 있다.

간단한 코드이기 때문에 <mark>make()</mark>메서드를 통해서 생성된 공에 대한 정보를 출력만 해주고 있다.


### Color객체를 제공하는 Factory

{% highlight ruby %}
public class ColorFactory {
    public static final HashMap<String, Color> map = new HashMap<>();

    public static Color getColor(String ballColor){
        Color color = (Color)map.get(ballColor);

        if(color == null){
            color = new Color(ballColor);
            map.put(ballColor, color);
            System.out.println("새 Color 객체 생성");
        }
        return color;
    }
}
{% endhighlight %}

Color 객체를 제공하는 Factory로 <mark>static final</mark>로 <mark>HashMap<String, Color></mark>을 선언해서 Color객체를 공유하며 관리할 수 있도록 해주고 있다.

<mark>getColor</mark>메서드를 통해서는 기존의 HashMap에 존재하는 데이터인지 아닌지를 판단하여 메모리를 절약하고 있는 것을 보여준다.
1. 기존 Color가 존재하는 경우
- 기존에 있는 Color를 찾아 반환해준다.
2. Color가 기존에 존재하지 않는 경우
- 새로운 Color 객체를 생성해주고 Map에 추가한뒤 반환해준다.


### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        ColorFactory colorFactory = new ColorFactory();
        Scanner sc = new Scanner(System.in);

        System.out.println("원하는 색을 입력해주세요: ");
        while (true){
            String input = sc.nextLine();

            Color color = (Color) ColorFactory.getColor(input);
            Ball ball = new Ball(color, (int)(Math.random()*10));

            ball.make();
        }
    }
}
    /** 출력 결과
        * 원하는 색을 입력해주세요: 빨강
        * 새 Color 객체 생성
        * 지름이 3 인 빨강색 공을 만들었습니다.
        * ------------------------------------------
        * 원하는 색을 입력해주세요: 노랑
        * 새 Color 객체 생성
        * 지름이 5 인 노랑색 공을 만들었습니다.
        * ------------------------------------------
        * 원하는 색을 입력해주세요: 노랑
        * 지름이 1 인 노랑색 공을 만들었습니다.
        * ------------------------------------------
        * 원하는 색을 입력해주세요: 빨강
        * 지름이 8 인 빨강색 공을 만들었습니다.
        * ------------------------------------------
        **/
{% endhighlight %}

해당 코드는 플라이웨이트 패턴을 적용한 예시로 색을 입력받아 공을 생성해주는 것을 보여주고 있다.

<mark>getColor</mark>메서드를 통해서 입력받은 색의 데이터 존재여부를 판단한 후 존재하지 않으면 새롭게 Color객체를 생성하는 것을 출력문 **<mark>새 Color 객체 생성</mark>**를 통해서 알 수 있다.

기존에 존재하는 것들을 재사용하면서 메모리 절약을 이루고 있는 것을 볼 수 있다.

## 단점
- 플라이웨이트 패턴을 사용하면 특정 인스턴스만 다르게 처리하는 것이 어려워질 것이다.
- 플라이웨이트 메서드가 매우 많이 호출될 때, 해당되는 데이터의 일부를 다시 계산해야 되는 경우에는 RAM은 절약되지만 CPU주기는 생각하지 않는 것일수도 있다.

## Java에서의 플라이웨이트 패턴 예시
### Java의 String 클래스
String 클래스는 플라이웨이트 패턴을 사용하기 때문에 같은 문자열이 입력될 때 기존에 있는 것을 사용하기 때문에 여러가지 이점이 있다.

- Java에서 String은 immutable하다.
- JAVA Runtime시에 Heap영역을 절약할 수 있다.
- 보안상 안전하다.

## 결론
플라이웨이트 패턴은 여러 객체들의 공통된 부분을 하나의 객체로 묶어 공유하며 비용을 절약하는 패턴이다.

싱글톤 패턴과 유사한 부분이 많은 패턴으로 이 두개를 상황에 맞게 선택하여 사용하면 될 것 같다.

내용적으로 어려운 부분은 크게 없고 기존 객체를 재사용한다라는 개념을 중요하게 생각하며 공부하면 잘 이해할 수 있을 것 같다.