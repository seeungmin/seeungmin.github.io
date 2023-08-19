---
layout: post
title:  "인터프리터 패턴(Interpreter Pattern)"
tags: [Disign Pattern, Behavioral Pattern]
date:   2023-08-19 12:30:00 +0900
toc:  true
---

## Interpreter Pattern

인터프리터라는 단어는 cs에서 사람이 작성한 코드를 하드웨어가 이해할 수 있도록 변환해 주는 장치를 뜻한다.

인터프리터 패턴은 간단한 언어의 문법을 정의하고 해석에 사용되는 패턴이다. 

자주 등장하는 문제를 간단한 언어로 정의하고 재사용 해주는 것이다.

문법의 규칙을 클래스로 바꿔주는 구조로서 SQL이나 통신 프로토콜에 자주 사용된다. 



## 인터프리터 패턴 구조

![Class Diagram](https://github.com/seeungmin/seeungmin.github.io/blob/main/DesignPatternDiagram/%EC%9D%B8%ED%84%B0%ED%94%84%EB%A6%AC%ED%84%B0%20%ED%8C%A8%ED%84%B4.png?raw=true"Interpreter Pattern Class Diagram")



## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

해당 코드는 인터프리터 패턴을 사용하여 후위 계산을 할 수 있도록 문법을 정의하여 사용하는 코드이다.


### PostfixExpression 인터페이스

{% highlight ruby %}
public interface PostfixExpression {
    int interpret(Map<Character, Integer> context);
}
{% endhighlight %}

규칙을 생성하고 표현을 파싱하는 것을 보이기 위한 인터페이스이다.


### VariableExpression 클래스
{% highlight ruby %}
public class VariableExpression implements PostfixExpression{
    private final Character variable;

    public VariableExpression(Character variable){
        this.variable = variable;
    }

    @Override
    public int interpret(Map<Character, Integer> context) {
        return context.get(this.variable);
    }
}
{% endhighlight %}

해당 클래스는 변수에 대한 규칙을 정의하는 클래스이다.

### Plus, Minus Expression 클래스
{% highlight ruby %}
public class PlusExpression implements PostfixExpression{
    private final PostfixExpression left, right;

    public PlusExpression(PostfixExpression left, PostfixExpression right) {
        this.left = left;
        this.right = right;
    }

    @Override
    public int interpret(Map<Character, Integer> context) {
        return left.interpret(context) + right.interpret(context);
    }
}
{% endhighlight %}
{% highlight ruby %}
public class MinusExpression implements PostfixExpression{
    private final PostfixExpression left, right;

    public MinusExpression(PostfixExpression left, PostfixExpression right) {
        this.left = left;
        this.right = right;
    }

    @Override
    public int interpret(Map<Character, Integer> context) {
        return left.interpret(context) - right.interpret(context);
    }
}
{% endhighlight %}

해당 클래스는 +, - 연산자 대한 규칙을 정의하는 클래스이다.

<mark>left</mark>, <mark>right</mark>를 정의하여 다음 표현식인 <mark>PostfixExpression</mark>을 반환하도록 합니다.

### PostfixParser 클래스
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        Map<Character, Integer> map = new HashMap() {
            {
                put('x', 1);
                put('y', 2);
                put('z', 3);
            }
        };

        PostfixExpression postfixExpression = PostfixParser.parse("xyz+-");
        int result = postfixExpression.interpret(map);
        System.out.println(result);
    }
}
    /** 출력 결과
        * -4
        **/
{% endhighlight %}

해당 코드는 인터프리터 패턴을 사용하여 클라이언트가 후위 연산의 특정 규칙을 받아서 계산을 수행하는 코드이다.

<mark>Map</mark>을 사용하여 규칙에 값을 입력하고 있다.

해당 코드를 통해<mark>xyz+-</mark>를 해석한 후 다음으로 해석할 수 있는 Expression으로 넘기며, 해석이 끝나서 하나의 Expression만이 남게 되었을 때 트리 구조로 올라오며 기능을 수행하도록 합니다.


## 사용 이유

이와 같은 메멘토 패턴을 사용하는 이유는 여러 가지 경우들이 있다.

그러한 여러 가지 경우들을 지금부터 하나하나씩 살펴보도록 하겠다.

1. 간단한 언어를 구현해야 하는 경우
2. 효율적인 것보다 문법을 단순하고 간단하게 만드는 것이 더 중요한 경우

위와 같은 경우에 많이 사용되고 있으며 여러 장점이 존재한다.


## 장점
- 자주 등장하는 문제 패턴을 클래스로 표현하여 언어를 쉽게 구현할 수 있다.
- 언어를 쉽게 변경하거나 확장이 가능하다.
- 문법과 규칙을 계층구조로 명시적으로 모델링 하기 때문에 코드의 가독성이 높아져 문법과 요소의 관계를 이해하는 데 도움이 된다.
- DSL의 여러 인스턴스에 대해 동일한 표현식 클래스와 로직을 사용하기 때문에 재사용성이 높아진다.

## 단점
- 문법과 규칙이 어려운 경우 복잡한 별도의 클래스가 많아지기 때문에 관리 및 이해가 어려워질 수 있다.
- 인터프리터 패턴만으로 에러 핸들링, 최적화와 같은 여러 케이스의 측면을 처리하기 어려울 수 있다.
- 문법이 자주 진화하는 경우 유지 보수하는데 오히려 더 많은 비용이 들 수 있다.

## 결론

인터프리터 패턴은 자주 등장하는 문제를 간단한 언어로 정의하고 재사용 하는 패턴이다.

기존 cs 지식에서도 사용자 관점의 코드를 하드웨어가 읽기 쉽도록 해석한다는 뜻으로 사용되고 있는 인터프리터라는 단어가 그 뜻 그대로 패턴에 적용되고 있는 것 같다.

사용되는 부분이 한정되어 있는 것 같아서 코드를 작성하는 데 있어서 조금 어렵게 작성한 것 같다.

실무에서 어떤 식으로 사용될지는 잘 모르겠지만 언어를 쉽게 구현하도록 해주는 것은 기억하고 넘어가야 할 것 같다.