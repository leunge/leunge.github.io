---
layout: post
title: "Item05"
comment: true
date: 2018-05-15
categories:
  - programming-page
  
tags:
  - Effective JAVA 3rd
  - Effective JAVA 3rd ch2
  - JAVA
---

### 5. hard writting작업시 의존선 주입(DEPENDENCY INCECTION) 추천

많은 클래스들이 기본자원에 의존한다. 아래와 같이 사용할 경우에 유연하지 않고 테스트가 불가능하다.

Static Utility 
```
public class SpellCheker {
    private static final Lexion dictonary = ... ;
    
    private SpellChecker() {}
    
    public static boolean isValid(Stirng word) {}
}
```

Singlton
```
public class SpellCheker {
    private static final Lexion dictonary = ... ;
    
    private SpellChecker() {}
    public static INSTANCE = new SpecllChecker(...);
    
    public static boolean isValid(Stirng word) {}
}
```

위의 경우에 SpellChecker가 dictionary를 변경할 수 있을 경우, 동시성 문제등 아주 심각한 문제를 유발 할 수 있다.

Static Utility클래스와 싱글톤은 기본 자원에 의해 매개 변수화 되기에 적합하지 않다 
이런 문제를 해결하기 위한 간단한 방법은 새 인스턴스를 만들 때 리소스를 생성자로 전달하는 것 입니다.( Dependency Injection )

dictonary는 spell checker의 의존성이며, 생성시에 spell checker에 주입됩니다 

```
public class SpellCheker {
    private static final Lexion dictonary;
    
    private SpellChecker() {}
    public static INSTANCE = new SpecllChecker(Lexion dictonary) {
        this.dictonary = Objects.requireNonNull(dictionary);
    }
    
    public static boolean isValid(Stirng word) {}
}
```

### DI를 적용하는 방법 
  1. Constructor
  2. Static Factory
  3. Builders


Dependency Injection 패턴의 유용한 변형은 리소스 팩토리를 생성자에 전달하는 것입니다.
java8의 Supplier<T> interface 는 factory를 나타낸다. 이걸 이용해서 만들어 보자.

client provided factory을 사용하여 각 tile을 만드는 방법이다 
Mosaic create(Supplier<? extends Tile> tileFactory) { ... }



DI가 유연성과 테스트 기능을 향상시켰지만, 대형 프로젝트를 어수선하게 할 수 있기 때문에 framework를 이용하는것이 좋습니다 
Java 에서 DI framework 로는

  1. Dagger by google
  2. Guice by google
  3. Spring  
가 있습니다.

