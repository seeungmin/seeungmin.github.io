---
layout: post
title:  "컴포지트 패턴(Composite Pattern)"
tags: [Disign Pattern, Structural Pattern]
date:   2023-07-17 15:30:00 +0900
toc:  true
---

## Composite Pattern

컴포지트 패턴은 여러 개의 객체들로 구성된 복합 객체와 단일 객체를 구별 없이 다룰 수 있게 해주는 패턴이다. 이를 만족시키기 위해 트리 구조로 작성을 하여, 전체와 부분의 관계를 표현해 준다.


## 사용 이유

이와 같은 컴포지트 패턴을 사용하는 가장 큰 이유는 전체와 부분의 관계를 효율적으로 정의할 수 있다는 것이고, 여러가지 이점들은 밑에 설명해보도록 하겠다.


### 전체-부분 관계를 효율적으로 정의
1. 유연성
- 객체의 구조를 변경하는데에 있어서 편하다.
- 개별 객체와 복합 객체를 새롭게 추가하더라도 기존 클라이언트 코드를 수정할 필요가 없다.
- 객체를 다 나눠놓기 때문에 결합도를 낮춰서 유연성을 증가시킬 수 있다.
- 복합 객체 내의 개별 객체를 제거하더라도, 다른 객체 간의 관계는 수정될 필요가 없다.
2. 단순성
- 개별 객체와 복합 객체를 동일한 방식으로 다룰 수 있기 때문에, 클라이언트 측의 코드가 단순해질 수 있다.
3. 재사용성
- 객체를 구성하는 방식이 일관적이기 때문에, 재사용성이 높아질 수 있다.


위와 같은 이점 때문에 디렉토리와 파일을 구성하는 구조와 같은 곳에서 컴포지트 패턴이 사용되고 있다.



## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

컴포지트 패턴을 사용하여 파일과 폴더를 생성하고 삭제해주는 간단한 코드를 작성해보았다.

### 파일 컴포넌트 인터페이스
{% highlight ruby %}
public interface FileComponent {
    public int getSize();
    public void remove();
}
{% endhighlight %}

전체 시스템이 상속받는 인터페이스이다.

파일이나 폴더의 size를 반환하거나, 삭제해주는 메서드가 존재한다.

### File 객체
{% highlight ruby %}
public class FileLeaf implements FileComponent{
    private String name;
    private int size;

    public FileLeaf(String name, int size) {
        this.name = name;
        this.size = size;
    }

    @Override
    public int getSize() {
        System.out.println("파일 이름: " + name + ", 파일 크기: " + size);
        return size;
    }

    @Override
    public void remove() {
        System.out.println(name + "파일을 삭제합니다.");
    }
}
{% endhighlight %}

해당 클래스는 File 컴포넌트를 구현하고, 구체 클래스를 나타낸다.

컴포지트 패턴 구조의 말단으로서 자식 클래스가 존재하지 않는다.
트리 구조에서의 말단과 같다고 생각하면 된다.


### Folder 객체

{% highlight ruby %}
public class FolderComposite implements FileComponent{
    private String name;
    private List<FileComponent> list = new ArrayList<>();

    public FolderComposite(String name) {
        this.name = name;
    }

    public void add(FileComponent fileComponent){
        list.add(fileComponent);
    }

    @Override
    public int getSize() {
        int sum = 0;
        for (FileComponent fileComponent : list)
            sum += fileComponent.getSize();
        System.out.println("폴더이름: " + name + ", 전체 파일 크기: " + sum);
        return sum;
    }

    @Override
    public void remove() {
        for (FileComponent fileComponent : list)
            fileComponent.remove();
        System.out.println(name + "폴더를 삭제합니다.");
    }
}
{% endhighlight %}

해당 클래스는 File 컴포넌트를 구현한 것으로, 자식 클래스가 존재하는 객체의 행동을 정의해준다.

또한 File 컴포넌트를 자식 클래스로 가지게 된다.
이는, 폴더안에 파일이 존재하는 관계와 똑같이 생각하면 된다.

해당 코드는 폴더에 있는 파일 전체의 크기를 알거나, 폴더와 해당 폴더에 존재하는 파일들을 삭제해줄 수 있다.

### 클라이언트 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        FolderComposite folder = new FolderComposite("1번");

        FileComponent file = new FileLeaf("최종본", 1);
        FileComponent file1 = new FileLeaf("최종최종본", 2);

        folder.add(file);
        folder.add(file1);

        folder.getSize();
        folder.remove();

    }
}
    /** 출력 결과
        * 파일 이름: 최종본, 파일 크기: 1
        * 파일 이름: 최종최종본, 파일 크기: 2
        * 폴더이름: 1번, 전체 파일 크기: 3
        * 최종본파일을 삭제합니다.
        * 최종최종본파일을 삭제합니다.
        * 1번폴더를 삭제합니다.
        **/
{% endhighlight %}

해당 코드는 컴포지트 패턴을 적용한 예시로 <mark>FileLeaf</mark>클래스로 파일을 만들어주고, <mark>FolderComposite</mark>클래스로 폴더를 만들어주어 파일을 삽입하는 것을 보여주고 있다.

<mark>FileLeaf</mark>클래스는 자식 클래스가 존재하지 않기 때문에 하나의 단위로서 사용되고 있음을 알 수 있다.

<mark>FolderComposite</mark>클래스는 <mark>FileLeaf</mark>클래스를 자식 클래스로 가지고 있어 자식 클래스까지 관리하는 것을 알 수 있다.


## 단점
- 복합 객체 내부의 모든 개별 객체를 각각 처리해주어야 하기 때문에 처리 비용이 증가할 수 있다.
- 객체의 구조가 복잡한 경우 사용하면 매우 효과적이지만, 구조가 단순한 경우 사용하게 된다면 오히려 복잡해질 수 있다.


## 결론
컴포지트 패턴은 개별 객체와 복합 객체가 동일한 인터페이스를 가지는 경우 사용해서 코드의 전체적인 복잡성을 줄여주고 유연성을 높여주는 패턴이다.

컴포지트 패턴은 각가의 타입의 안정성보다, 개별 객체와 복합 객체의 일관성에 좀 더 중점을 준 패턴이기 떄문에 트리 구조와 같은 특별한 경우에 사용하면 좋은 패턴인 것 같다.

대표적인 예시인 디렉토리-파일 구조를 생각하며 공부한다면 쉽게 이해하고 넘어갈 수 있는 패턴이라고 생각한다.
