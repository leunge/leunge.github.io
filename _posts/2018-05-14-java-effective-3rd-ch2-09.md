---
layout: post
title: "Item09"
comment: true
date: 2018-05-19
categories:
  - programming-page
  
tags:
  - Effective JAVA 3rd
  - Effective JAVA 3rd ch2
  - JAVA
---

### 9. try-finally보단 try-with-resource 사용하기 


Java API 에는 InputStream, OuputStream, Connection 과 같이 resource 를 직접 close 해줘야 하는 API 들이 있다.

  - Java 6 방식
    ```
    InputStream stream = new MyInputStream(...);
    try {
        // ... use stream
    } catch(IOException e) {
       // handle exception
    } finally {
        try {
            if(stream != null) {
                stream.close();
            }
        } catch(IOException e) {
            // handle yet another possible exception
        }
    }
    ```
    
  - Java 7 부터는 java.lang.AutoCloseable interface 가 추가 되면서, 이 interface 를 구현한 Object 는 try-with-reources 를 사용할 수 있다.
    ```
    try (InputStream stream = new MyInputStream(...)){
        // ... use stream
    } catch(IOException e) {
       // handle exception
    }
    ```
    - 이 표기법은 이전 방법보다 확실하게 좋지만 문제가 있습니다. 리소스 (이 경우 strem )가 이전에 선언되었지만이 블록에서 올바르게 처리되었는지 확인하려면 다음과 같은 트릭이 필요합니다.
    ```
    InputStream stream = new MyInputStream(...)
    try (InputStream stream2 = stream) {
       // do something with stream being sure that is going to be closed at the end
    } catch(IOException e) {
       // handle exception
    }
    ```
  - Java 9에서는 Try-With-Resources가 개선
    ```
    InputStream stream = new MyInputStream(...)
    try (stream) {
        // do something with stream being sure that is going to be closed at the end
    } catch(IOException e) {
        // handle exception
    }
     ```
    - 이 구문을 사용하면 Java 버전 8 또는 부 버전의 컴파일 시간 오류가 발생합니다.
    - 대부분의 경우 우리가 try 블록의 범위 밖에서 자원을 필요로하지 않더라도 더 "자연스러운"작성 방법입니다. 유일한 제한은 독자 변수가 효과적으로 최종 또는 최종이어야한다는 것입니다.