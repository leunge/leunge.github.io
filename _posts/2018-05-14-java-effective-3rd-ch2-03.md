---
layout: post
title: "Item03"
comment: true
date: 2018-05-13
categories:
  - programming-page
  
tags:
  - Effective JAVA 3rd
  - Effective JAVA 3rd ch2
  - JAVA
---

### 3. private 생성자나 enum에 싱글톤 property 강제하기 
싱글톤 - 객체를 하나만 만들 수 있는 클래스   
클래스를 싱글톤으로 만드는건 타입으로 인터페이스를 implement 받지 않는한 mock 객체로 만들수 없기 때문에 테스트하기 힘들어 질 수 있다.

#### implement singleton하는 방법

  1. singleton with public final field  
     * 예외는 허가된 클라이언트가 AccessibleObject.setAccessible 메소드를 사용해서 private 생성자를 재귀적으로 호출 이런 호출을 막을 필요가 있으면 두번째 인스턴스 생성 요청이 오면 예외 발생하도록 생성자 수정
     * 멤버 필드만 봐도 싱글톤 클래스인지 명쾌하게 알 수 있음
     * static 팩토리 메소드를 사용하는 것에 비해 성능의 이점은 없음  
      (최근 자바 가상 머신은 static 팩토리 메소드를 인라인 코드로 호출함)
      
     ```
     public class Evis {
         public static final Evis INSTANCE = new EVis();
         private Evis() { ... }
        
         public void leaveTheBuilding() { ... }
     }
     ```  
  2. singleton with static factory
     * AccessibleObject.setAccessible 메소드를 사용해도 생성안됨
     * 클래스의 API를 변경하지 않고 클래스에서 반환하는 싱글톤 인스턴스 형태를 바꿀 수 있는 유연성 제공
     ```
     public class Elvis {
         private static final Elvis INSTANCDE = new Elvis();
         private Elvis() { ... }
         public static Elvis getInstance() { return INSTANCE; }
        
         public void leaveTheBuilding() { ... }
     }
     ```

  3. 열거형(enum) 타입
     * 싱글톤을 구현하는 가장 좋은 방법
     * public 필드 방법과 기능적으로 동일하지만 더 간단
     * 복잡한 직렬화나 리플렉션(reflection) 상황에서도 직렬화 자동으로 지원, 인스턴스가 여러개 생기지 않도록 보장

     ```
     public enum Elvis {
          INSTANCE;
          public void leaveTheBuilding(){
              //...
          }
     }
     ```

  4. 1), 2)번 방법으로 구현된 싱글톤 클래스를 직렬화(serializable) 가능하도록 하려면 어떻게?
     * 클래스 선언부에 implements Serializable을 추가
     * 싱글톤을 보장하기 위하여 모든 인스턴스 필드를 transient로 선언
     * readResolve 메소드를 추가해야 함  
      -> 이렇게 하지 않으면 직렬화된 인스턴스가 역직렬화(deserialized)될 때마다 새로운 인스턴스가 생성
     ```
     private Object readResolve(){
        //하나의 진짜 Elvis를 반환하고 가비지 컬렉터가 가짜 Elvis 처리        
          return INSTANCE;
     }
     ```
          