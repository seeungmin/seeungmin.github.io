---
layout: post
title:  "빌더 패턴(Builder Pattern)"
tags: [Disign Pattern, Creational Pattern]
date:   2023-07-06 17:03:35 +0900
toc:  true
---

## Builder Pattern 

빌더 패턴은 생성과 표현을 분리하여 나타내는 것이다. 이것을 통해 객체 생성 및 초기화 과정을 간소화 할 수 있고, 객체 생성에 대해서 유연성과 가독성을 가지도록 할 수 있다.

## 사용 이유
 
빌더 패턴을 사용해서 왜 생성과 표현을 분리하여 나타내는지 알아보도록 하겠다.

1. 객체 생성 및 초기화 복잡성 해결
- 생성자에서 처리해야 할 매개변수를 빌더 패턴을 사용함으로써 단계적으로 처리할 수 있도록 하여, 보다 간편하게 객체 생성 및 초기화를 하도록 한다.
2. 객체 생성 유효성 보장
- 빌더패턴을 사용하여 객체 생성에 필요한 매개변수를 단계적으로 처리하여, 중간에 매개변수가 빠지거나, 유효하지 않은 값이 전달 되는 것 과 같은 것을 방지할 수 있다.
3. 가독성 향상
- 객체 생성과 초기화 과정을 분리하고, 매개변수들을 명시적으로 처리하는 것으로, 코드의 가독성을 향상시켜 유지보수성과 코드 재사용성까지 높일 수 있다.
4. 유연성 향상
- 빌더 패턴은 다양한 객체 생성 방식을 제공하기 때문에, 여러 방법을 사용하여 객체를 생성하여 유연성을 높여준다.
5. 생성자 감소
- 생성자가 많아지면 많아질수록 코드의 가독성도 떨어지고 유지보수성까지 낮아질 수 있다.
- 이러한 것을 해결하기 위해 빌더패턴을 사용하여 객체 생성 과정을 감싸고 생성자의 복잡성을 줄일 수 있다.

## 구현
구체적인 패턴 사용 방법이나 유형은 코드로 설명하도록 하겠다.

### 빌더 인터페이스
{% highlight ruby %}
public interface StudentBuilder {
    public StudentBuilder name(String name);
    public StudentBuilder age(int age);
    public StudentBuilder phone(String phone);
    public StudentBuilder email(String email);

    public Student build();

}
{% endhighlight %}
위의 코드는 객체에 기본적으로 들어가야하는 매개변수들에 대한 인터페이스이다.

빌더 구현체들은 해당 인터페이스의 메서드를 전부 구현해야 한다.

### 빌더 구현체
{% highlight ruby %}
public class DefaultStudentBuilder implements StudentBuilder{
    private String name;
    private int age;
    private String phone;
    private String email;

    @Override
    public StudentBuilder name(String name) {
        this.name = name;
        return this;
    }

    @Override
    public StudentBuilder age(int age) {
        this.age = age;
        return this;
    }

    @Override
    public StudentBuilder phone(String phone) {
        this.phone = phone;
        return this;
    }

    @Override
    public StudentBuilder email(String email) {
        this.email = email;
        return this;
    }

    @Override
    public Student build() {
        return new Student(name, age, phone, email);
    }
}

{% endhighlight %}
빌더 인터페이스의 구현체로서 합성하여 나타내고 싶은 방법에 따라 여러가지 구현체를 만들어 사용한다.

위의 코드는 간단하게 학생의 정보 전체를 입력받도록 구현한 코드이다.

### 빌더로 만들어낸 결과물

{% highlight ruby %}
public class Student{
    private String name;
    private int age;
    private String phone;
    private String email;

    public Student(String name, int age, String phone, String email) {
        this.name = name;
        this.age = age;
        this.phone = phone;
        this.email = email;
    }

    public String toString(){
        return "name: " + name + ", " + "age: " + age + ", " + "phone: " + phone + ", " + "email: " + email;
    }
}
{% endhighlight %}
빌더로 만들어낸 결과물로 학생 정보를 입력받아 저장하고 출력해주는 역할을 하고 있다.

### 기본코드 test
{% highlight ruby %}
public class Client {
    public static void main(String[] args) {
        Student student = new DefaultStudentBuilder()
                .name("신짱구")
                .age(5)
                .phone("000-0000-0000")
                .email("sinzzang@...")
                .build();

        System.out.println("student = " + student.toString());
    }
}
    /** 출력 결과
        * student = name: 신짱구, age: 5, phone: 000-0000-0000, email: sinzzang@...
         **/
{% endhighlight %}
빌더 패턴을 사용하여 새로운 객체를 생성하였고, 출력 결과를 통하여 객체가 잘 생성됨을 알 수 있다.

## 단점
1. 클래스 및 코드 추가 생성
- 빌더 패턴을 사용하게 되면 빌더 클래스를 생성해야 하기 때문에, 클래스 및 코드의 양이 늘어나게 되고, 만약 프로젝트의 규모가 커지게 된다면 관리하기 어려워질 수 있다.
2. 성능 저하
- 빌더 패턴은 객체를 생성하기 위해서 여러 개의 메서드를 호출해야 하기 때문에 프로젝트의 규모가 커지고 빌더패턴이 많이 쓰이게 되는 경우에 성능이 저하될 수 있다.
3. 의존성 증가
- 객체에 필드를 추가하고나 삭제할 때, 빌더 클래스도 수정을 해야한다.
- 이것은 빌더 클래스와 객체 클래스 간의 의존성을 높여 유지보수를 어렵게 할 수도 있다.

## 결론
간단하게 빌더 test 코드만 보더라도 코드의 가독성이 매우 높아지고, 개발자의 실수로 인한 오류가 생기지 않을 것으로 보인다. 이와 같이 여러가지 장점때문에 사용하는 것이 빌더 패턴이라고 생각된다.

빌더 패턴을 사용함에 있어 성능과 유지보수와 같은 것에 대한 양면성이 존재하게 된다. 그렇기 때문에 어느 상황에 사용할지 적절하게 계획하여 잘 사용하지 않으면 오히려 코드가 안좋아질 것이라고 생각한다.
