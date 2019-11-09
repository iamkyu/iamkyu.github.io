---
layout: post
title: "Java, Call By Reference 에 대한 오해"
#description: ""
date: 2019-11-09
tags: [java]
comments: true
share: true
---

# Java, Call By Reference 에 대한 오해
이제까지 기본형은 Call By Value, 참조형은 Call By Reference로 막연히 알고 있었다. 그런데 생성자나 메서드를 통해 전달된 값을 사용할 때 오해하고 있는 부분이 있어 이를 정리한다. 

결론부터 얘기하자면 
> Reference data type parameters, such as objects, are also passed into methods by value. 

오라클의 Java Tutorials 문서 중 Passing Information to a Method or a Constructor를 살펴보면 위와 같이 서술되어 있다. 즉, 참조형 데이터 타입 역시 값으로 메소드에 전달된다는 것. 몇 가지 예제와 함께 살펴보자. 먼저, 예제에 사용할 Message 클래스를 정의한다.

```java
public class Message {
    private String contents;

    public Message(String contents) {
        this.contents = contents;
    }

    public void changeContents(String contents) {
        this.contents = contents;
    }

    public String getContents() {
        return contents;
    }
}
```

첫 번째로, Call By Reference 예제에 많이 사용되는 인스턴스 참조를 전달하고 해당 인스턴스의 속성을 변경하는 코드.

```java
@Test
public void caller() {
    Message message = new Message("hello");
    callee(message);
    assertThat(message.getContents()).isEqualTo("bye");
    // Test passed
}

private void callee(Message message) {
    message.changeContents("bye");
}
```

앞서 인용한 오라클 문서에 이와 관련해 언급되어 있다.

> the values of the object's fields can be changed in the method, if they have the proper access level.

즉, callee 에서 전달받은 참조형의 속성에 직접 접근할 수 있거나 속성을 변경할 수 있는 메서드가 제공된다면 속성을 변경할 수 있다. 이때, caller 나 callee 모든 같음 참조 주소를 가지고 있기 때문에 이 속성의 변경에 양쪽 모두 영향을 받는다.

반면, 아래 두 번째 예제는 callee 에서 전달받은 message에 새로운 인스턴스를 할당해버렸다. 그 후 caller에서 `message.getContents()` 로 속성을 확인하면 “hello” 일까 “bye” 일까?

```java
@Test
public void caller() {
    Message message = new Message("hello");
    callee(message);
    // message.getContents() 는 hello? bye?
}

private void callee(Message message) {
    message = new Message("bye");
}
```

정답은 “hello” 다. 사실 조금만 생각해보면 간단한 문제인데, 나를 헷갈리게 했던 포인트다.
앞서 오라클 문서에서 언급했듯 참조형 데이터 타입 역시 값으로 전달된다고 했다. 이때 이 값은 “참조 주소” 이다.
근데 이 값을 사용하지 않고 않고 다른 “참조 주소”가 할당되어 버렸기 때문에 caller  와 callee 는 서로 다른 message 인스턴스에 접근하고 있다.


이를 명확하게 구분하자면 자바에서 생성자나 메서드는 항상 Pass By Value, 객체나 배열의 경우 이 값이 참조 주소이고 이를 통해 Call By Reference가 일어난다가 정확한 설명이 아닐까?


## 참고
- [Oracle - The Java™ Tutorials : Passing Information to a Method or a Constructor](https://docs.oracle.com/javase/tutorial/java/javaOO/arguments.html)
