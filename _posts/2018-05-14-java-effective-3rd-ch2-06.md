---
layout: post
title: "Item06"
comment: true
date: 2018-05-16
categories:
  - programming-page
  
tags:
  - Effective JAVA 3rd
  - Effective JAVA 3rd ch2
  - JAVA
---

### 6. 필요없는 객체 생성 비추천

 당연히 한상 new로 새로 만드는것보다 재사용하는게 좋기 때문에 객체가 immutable하다면 항상 다시쓸 수 있다 

  1. String instance를 계속 생성  -> 하나만 사용   
    ```
    String s = new String("bikini");  ->  String s = "bikini";
    ```
  2. static factory method 사용  
    ```
    Boolean(String) -> Boolena.valueof(String) 
    ```
  3. library 잘 알고 사용하기   
     - String.matches 를 보면 Pattern.comile 을 호출하면서 새로운 instance를 생성해 사용한다. 
    당연히 재사용하지 않고 매번 새로운 instance 를 생성하게 된다.
그래서 Strign.matche는 반복되는 경우 사용하는게 좋지 않다.
    ```
     public class RomanNumerals {
         static boolean isRomanNumeral(String s) {
             return s.matches("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
         }
     }
    ```
    
     - 수정 
     ```
      public class RomanNumerals {
         private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|D?C{0,3})(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
       
         static boolean isRomanNumeral(String s) {
             return ROMAN.matcher(s).matches();
         }
      }
     ```

