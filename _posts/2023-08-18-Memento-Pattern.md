---
layout: post
title:  "메멘토 패턴(Memento Pattern)"
tags: [Disign Pattern, Behavioral Pattern]
date:   2023-08-18 12:30:00 +0900
toc:  true
---

## Memento Pattern

메멘토 패턴은 클래스 설계 관점에서 객체의 정보를 저장해두어 undo 기능을 유용하게 사용할 수 있는 패턴이다.

이를 통해 객체의 상태 정보를 저장하고 사용자의 필요에 의하여 원하는 시점의 데이터를 복원할 수 있는 패턴이다.


## 메멘토 패턴 구조

![Class Diagram](https://github.com/seeungmin/seeungmin.github.io/blob/main/DesignPatternDiagram/%EB%A9%94%EB%A9%98%ED%86%A0%20%ED%8C%A8%ED%84%B4.png?raw=true"Memento Pattern Class Diagram")



## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

해당 코드는 메멘토 패턴을 사용하여 게임의 레벨과 단계를 세이브하는 코드입니다.


### OriginatorGame 클래스

{% highlight ruby %}
public class OriginatorGame {
    private int level;
    private int stage;

    public Memento save(){
        return new Memento(this.level, this.stage);
    }

    public int getLevel() {
        return level;
    }

    public int getStage() {
        return stage;
    }

    public void setLevel(int level) {
        this.level = level;
    }

    public void setStage(int stage) {
        this.stage = stage;
    }
}
{% endhighlight %}

게임에서 특정 레벨과 단계를 나타내주는 클래스이다.

해당 게임에서 <mark>save()</mark>메서드를 통해서 현재의 레벨과 단계를 저장할 수 있다.


### Memento 클래스
{% highlight ruby %}
public final class Memento {
    private int level;
    private int stage;

    public Memento(int level, int stage) {
        this.level = level;
        this.stage = stage;
    }

    public int getLevel() {
        return level;
    }

    public int getStage() {
        return stage;
    }
}
{% endhighlight %}

해당 클래스는 특정 시점의 레벨과 단계를 저장하는 클래스이다.

<mark>Setter</mark>를 사용하지 않아서 정보를 바꿀 수 없도록 해주었다.

### CareTaker 클래스
{% highlight ruby %}
public class CareTaker {
    private List<Memento> mementos = new ArrayList<>();

    public void add(Memento memento){
        mementos.add(memento);
    }

    public Memento get(int index){
        return mementos.get(index);
    }
}
{% endhighlight %}

해당 클래스는 상태 정보가 저장되어 있는 <mark>Memento</mark>들을 관리하는 클래스이다.

<mark>List</mark>를 통하여 <mark>Memento</mark>객체들을 관리하고 있다.

<mark>add()</mark> 메서드를 통해서 <mark>Memento</mark> 객체를 추가합니다.

<mark>get()</mark> 메서드를 통해서 <mark>Memento</mark> 객체를 가져올 수 있다.


### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        OriginatorGame game = new OriginatorGame();
        CareTaker careTaker = new CareTaker();

        game.setLevel(1);
        game.setStage(4);

        careTaker.add(game.save());

        game.setLevel(2);
        game.setStage(6);

        careTaker.add(game.save());

        game.setLevel(3);
        game.setStage(10);

        System.out.println("현재 상황 -> Level: " + game.getLevel() + ", Stage: " + game.getStage());
        System.out.println("1번 save -> Level: " + careTaker.get(0).getLevel() + ", Stage: " + careTaker.get(0).getStage());
        System.out.println("2번 save -> Level: " + careTaker.get(1).getLevel() + ", Stage: " + careTaker.get(1).getStage());

    }
}
    /** 출력 결과
        * 현재 상황 -> Level: 3, Stage: 10
        * 1번 save -> Level: 1, Stage: 4
        * 2번 save -> Level: 2, Stage: 6
        **/
{% endhighlight %}

해당 코드는 메멘토 패턴을 사용하여 정보를 저장하는 <mark>Memento</mark>들을 관리하고 객체들의 상태를 확인하는 코드이다.

<mark>CareTaker</mark> 객체를 통해서 <mark>Memento</mark> 객체들을 관리하는 것을 볼 수 있다.

현재 객체의 상태를 갱신하며, 저장하고 있는 것을 볼 수 있다.


## 사용 이유

이와 같은 메멘토 패턴을 사용하는 이유는 여러 가지 경우들이 있다.

그러한 여러 가지 경우들을 지금부터 하나하나씩 살펴보도록 하겠다.

1. 실행 취소 기능을 구현해야 하는 경우
2. 오류가 발생한 경우 특정 객체를 복구해야 하는 경우
- 비공개 필드들을 포함하여 객체의 상태 전체 복사본들을 만들 수 있다.
- 복사본들을 객체와 별도로 저장할 수 있다.
3. 객체의 필드, Getter, Setter들을 직접 접근하는 것이 캡슐화를 위반하는 경우
- 객체가 스스로 자신의 상태의 스냅샷을 생성하도록 담당하기 때문에 다른 객체에서 스냅샷을 읽을 수 없어서 객체의 상태가 안전하다.

위와 같은 경우에 많이 사용되고 있으며 여러 장점이 존재한다.


## 장점
- 저장된 상태를 중요한 객체와 다른 별도의 객체에 보관하기 때문에 안전하다.
- 복구 기능을 좀 더 쉽게 구현할 수 있다.
- 캡슐화를 위반하지 않고 객체 상태의 스냅샷을 생성할 수 있다.

## 단점
- 상태를 너무 자주 저장하여 Memento 객체를 생성하면 많은 RAM을 소모할 수 있다.
- 쓸모없는 Memento들을 삭제할 수 있도록 관리해 주는 것이 어렵다.
- 동적인 프로그래밍 언어 같은 경우에는 Memento의 상태가 그대로 유지되는 것이 보장될 수 없다.

## 결론

메멘토 패턴은 객체 구현의 세부 사항을 공개하지 않으면서도 해당 객체의 이전 상태를 저장하고 복원할 수 있도록 하는 패턴이다.

위의 코드에서 예를 들어 설명한 것처럼, 간단하게 게임에서 세이브 기능이라고 생각하면 좋을 것 같다. 세이브를 어떻게 하는지 모르지만 우리는 세이브를 할 수 있다고 생각하면 이해가 쉬울 것 같다.

클래스 다이어그램을 보면 알 수 있듯이, 간단한 패턴으로 가볍게 공부하면 좋을 것 같다.