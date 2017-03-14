---
layout: post
title: "자바의 어노테이션"
#description: ""
date: 2017-03-14
tags: [java]
comments: true
share: true
---

# 자바의 어노테이션

JDK1.5 부터 제공된 기능인 어노테이션은 @(;AT) 으로 시작하는 주석의 한 형태를 말한다. `@Override`, `@SuppressWarnings("")` 과 같은 어노테이션에 익숙할 것이다.

> 어노테이션은 메타데이터 한 형태로, 프로그램에 대한 정보를 제공하지만, 그 프로그램의 일부는 아니다. 어노테이션을 설정한 코드에 직접적인 영향을 미치지는 않는다.

Oracle의 자바 튜토리얼 문서에서 어노테이션을 위와 같이 정의하며 용도를 설명한다.

- 컴파일러를 위한 정보를 제공하기 위해.
- 컴파일 시점에 어떤 코드나 XML 파일 등을 생성하기 위해.
- 런타임 시점에 추가적인 처리를 하기 위해.



## 어노테이션의 형태

```java
@Override
public String toString() {
	// ...
}
```

@(;AT) 기호는 컴파일러에 기호 다음에 오는 것이 어노테이션임을 알린다. 위 예제에서 어노테이션명은 `Override` 이다.



## 미리 정의 된 어노테이션들

자바에서 바로 사용할 수 있도록 미리 정의 되어 있는 어노테이션들은 다음과 같다.

- `@Deprecated`
- `@Override`
- `@SuperWarnings`
- `@SafeVarargs` 
- `@FunctionalInterface`

이 어노테이션들에 대한 더 자세한 정보는 [자바 튜토리얼 문서](https://docs.oracle.com/javase/tutorial/java/annotations/predefined.html)에서 확인할 수 있다.



## 어노테이션 직접(Custom) 정의

어노테이션은 필요에 따라 직접 정의할 수도 있다. 자바 튜토리얼에서 제공한 예시는 클래스에 대한 주석을 어노테이션으로 대체하는 것이다.

```java
// Author: iamkyu
// Date: 2017/03/11
// Current revisision: 2
// Last modifed: 2017/03/12
class MyClass {
	// ...
}
```

이 주석들을 어노테이션으로 전환하려면 하나의 특별한 인터페이스를 선언해야 한다.

```java
@interface ClassPreamble {
	String author();
  	String date();
  	int currentRevision() default 1;
  	String lastmodified() default "N/A";
}
```

이처럼 어노테이션을 직접 정의할 때 해당 어노테이션의 적용 가능 대상, 정보 유지 시간 등도 임의로 설정할 수 있다. 이를 위해 `@Target`, `@Retentiion`, `@Documented`, `@Inherited` 의 어노테이션을 사용하고 이것들을 메타(Meta) 어노테이션이라고 한다.

인터페이스를 선언한 후,  `@ClassPreamble` 이라는 이름의 어노테이션을 사용할 수 있다.

```java
@ClassPreamble (
   author = "iamkyu",
   date = "2017/03/11",
   currentRevision = 2,
   lastModified = "2017/03/12",
)
class MyClass {
    // ...
}
```



## 어노테이션과 리플렉션

사실 내가 궁금했던 것은 어노테이션은 어떻게 작동하는 것인가에 대해서였다. 글의 첫 부분에서 인용한 어노테이션의 정의를 다시 보자.

> 어노테이션은 메타데이터 한 형태로, 프로그램에 대한 정보를 제공하지만, 그 프로그램의 일부는 아니다. 어노테이션을 설정한 코드에 직접적인 영향을 미치지는 않는다.

어노테이션 자체가 코드에 직접적인 영향을 주지는 않는다. 그렇다면 어딘가에서 어노테이션을 인식하고 그 인식에 따라 어떤 처리 하는 코드가 또 있다는 것인데 '어디서 어떻게' 하는 것인지 궁금했다.



###  java.lang.annotation.Annotation 인터페이스

먼저, 모든 어노테이션은 `java.lang.annotation.Annotation` 인터페이스를 상속한다. 

![Annotation 상속구조](https://lh3.googleusercontent.com/pGRX4zrbpJwpUO8Alkah9kk872gjLSyMXwDlDfe6ftNGz9b_kwJBs84euw7H8zAhlCGWNZ_gkfojc8iZ0c2CK1sp5kO0IT8eRZnFUANPHzVPbGaq0otBVbLb0KUgtIJ-VjtAYFXbpZg_OD_qFiaR5yQ7PC7EAUJJb9YILiZg6B-N2W2sfpidFt52hggD_DKnytmHhCTzW7UKXjz2PVczHiMwultFD6I_mw7tCDh-buVkiZ4m4R4vj02yrfydc_VsufnRAXGctlzMngF6tZeUhdQSkZtWsMmtcpOUlKM2jSmw4A7gLrKAOyva0SDa9CAF5hvLzBcE_we5ufssmKd_N7Et_G9cl23zC9A6v0B98a0rORF3AKuVni1Tqhg5Xua-lMTcRaKCknnWiQzA-GA4IO6b90b4339fuSKdx_geI-490k6mEq0Dz5x1LwMmiWqIuivIhaD-U3CLpQEwql5GZqNQrIOjR8Mm1rLIV9mr0HkRhLQmGuCH-YZLrcbBF8G5bZEAWmzQoxHvntY1uEBiyfTCmD55CbtgmbRlFFqM4UC7ywS-tTqLDiNu3DmtJEr1OWIQPGiXmYEdkD1fIMctYhX53ycQjTnfT7I-hJvMzxwS4Ji2NhC2=w413-h371-no)

`ClassPreamble` 은 이 글의 **어노테이션 직접 정의** 부분에서 선언했던 커스텀 어노테이션인데, 직접 상속을 구현하지 않았는데 `Annotation` 인터페이스를 상속하고 있다. `@interface` 라는 메타 어노테이션을 통해 처리되는 듯하다.



### 스프링프레임워크의 커스텀 어노테이션

순수 자바 코드에서 어노테이션을 찾아서 메타데이터를 읽어 들이는 부분을 찾기가 힘들었다. 그래서 스프링 프레임워크 상에서 커스텀 어노테이션을 처리하는 방식을 추적해보기로 했다. 스프링 프레임워크에서는 `@Controller`, `@Service` 등 다양한 커스텀 어노테이션이 기본적으로 내장(Built-In) 되어 있다.

스프링프레임워크를 부트스트랩(Bootstrap) 하는 과정 중, 컨텍스트 설정을 읽어오는 부분이 있다. 이 코드를 따라 들어가다 보면 `MetaAnnotationUtils` 라는 클래스가 등장하는데 문서에 따르면 어노테이션을 찾거나 얻어올 뿐만 아니라 추가적인 기능도 지원한다고 한다.

이 유틸리티 클래스 중 `findAnnotationDescriptor` 메서드의 일부를 보면 아래와 같다.

```java
for (Annotation composedAnnotation : clazz.getDeclaredAnnotations()) {
	// ...
}
```

`clazz` 변수는 Class 타입이다. 이를 통해 추측할 수 있는 것은 자바의 리플렉션 기술을 통해 클래스에 선언 된 어노테이션을 읽어들일 수 있다는 것이다.



### 예시

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
public @interface ClassPreamble {
    String author();
    String date();
    int currentRevision() default 1;
    String lastModified() default "N/A";
    String lastModifiedBy() default "N/A";
    String[] reviewers();
}

////////////////////////////////

@ClassPreamble(
        author = "Tester",
        date = "2017/03/11",
        reviewers = {"John", "Allen"}
)
public class CustomAnnotation {
	// ...
}
```

위의 코드와 같이 커스텀 어노테이션을 정의하고 해당 어노테이션을 사용한 클래스가 있다고 했을 때, 다음과 같은 방법으로 어노테이션의 메타데이터를 읽어올 수 있다.

```java
@Test
public void getMetadataTest() {
    //given
    Class clazz = new CustomAnnotation().getClass();
    Annotation annotation = clazz.getAnnotation(ClassPreamble.class);

    //when
    String result = annotation.toString();
    // @lang.annotation.ClassPreamble(currentRevision=1, lastModified=N/A, lastModifiedBy=N/A, author=Tester, date=2017/03/11, reviewers=[John, Allen])

    //then
    assertTrue(result.contains("currentRevision=1"));
    assertTrue(result.contains("lastModified=N/A"));
    assertTrue(result.contains("lastModifiedBy=N/A"));
    assertTrue(result.contains("author=Tester"));
    assertTrue(result.contains("date=2017/03/11"));
    assertTrue(result.contains("reviewers=[John, Allen]"));
}
```



# 참고

- [The Java™ Tutorials - Lesson : Annotations](https://docs.oracle.com/javase/tutorial/java/annotations)
- [Annotation과 Reflection, 그리고 코드 속의 MetaData](http://kang594.blog.me/39704853)