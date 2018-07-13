---
layout: post
title: "Item07"
comment: true
date: 2018-05-17
categories:
  - programming-page
  
tags:
  - Effective JAVA 3rd
  - Effective JAVA 3rd ch2
  - JAVA
---

### 7. 오래된 객체의 레퍼런스 삭제하기 

#### 메모리 관리의 중요성
  - 가비지 컬렉션을 자동으로 해준다고 메모리 관리를 고려할 필요가 없다는 것은 아니다!
  - 힙 프로파일러(heap profiler) 이용

#### 메모리 누수의 원인
1) 쓸모없는 참조
  - 메모리 누출이 있는 예제
    ```
    public class Stack {
    
        private Object[] elements;
        private int size = 0;
        private static final int DEFAULT_INITIAL_CAPACITY=4;
        
        public Stack(){
            elements = new Object[DEFAULT_INITIAL_CAPACITY];        
        }
        
        public void push(Object e){
            ensureCapacity();
            elements[size++]=e;
        }
        
        public Object pop(){
            if(size==0){
                throw new EmptyStackException();
            }
            return elements[--size];
        }
     
        private void ensureCapacity() {
            if (elements.length == size) {
                elements = Arrays.copyOf(elements, 2 * size + 1);
            }
        }    
     
        public void print(){
            for(int i=0; i<elements.length; i++){
                System.out.print("\t"+elements[i]);
            }
            System.out.println("");
        }
    }
    ```

  - Stack 클래스에서 메모리 누출이 생기는 이유는?
    - 자신의 메모리를 스스로 관리하기 때문에
    - 쓸모없는 참조를 스택에서 여전히 가지고 있기 때문에
    - 스택이 커졌다가 줄어들면 스택에서 꺼냈던 객체들은 그런 객체를 더 이상 참조하지 않더라도 가비지 컬렉션되지 않음

  - 수정한 예제
    - 쓸모없는 참조를 null로 만드는 것
    ```
    public Object pop(){
        if(size==0){
            throw new EmptyStackException();
        }
        Object result = elements[--size];
        elements[size] = null; //쓸모없는 참조를 제거한다.
        return result;
    }
    ```

    - 실행 결과
      - 메모리 누출이 있는 예제
      
| stack | [0]  | [1]  | [2]  | [3]  | [4]  | [5]  | [6]  | [7]  | [8]  |
|-------|------|------|------|------|------|------|------|------|------|
| push1 | 1    | null | null | null | null | null | null | null | null |
| push2 | 1    | 2    | null | null | null | null | null | null | null |
| push3 | 1    | 2    | 3    | null | null | null | null | null | null |
| push4 | 1    | 2    | 3    | 4    | null | null | null | null | null |
| push5 | 1    | 2    | 3    | 4    | 5    | null | null | null | null |
| push6 | 1    | 2    | 3    | 4    | 5    | 6    | null | null | null |
| pop6  | 1    | 2    | 3    | 4    | 5    | 6    | null | null | null |
| pop5  | 1    | 2    | 3    | 4    | 5    | 6    | null | null | null |
| push7 | 1    | 2    | 3    | 4    | 7    | 6    | null | null | null |


      - 메모리 누출이 있는 예제
      
| stack | [0]  | [1]  | [2]  | [3]  | [4]  | [5]  | [6]  | [7]  | [8]  |
|-------|------|------|------|------|------|------|------|------|------|
| push1 | 1    | null | null | null | null | null | null | null | null |
| push2 | 1    | 2    | null | null | null | null | null | null | null |
| push3 | 1    | 2    | 3    | null | null | null | null | null | null |
| push4 | 1    | 2    | 3    | 4    | null | null | null | null | null |
| push5 | 1    | 2    | 3    | 4    | 5    | null | null | null | null |
| push6 | 1    | 2    | 3    | 4    | 5    | 6    | null | null | null |
| pop6  | 1    | 2    | 3    | 4    | 5    | null | null | null | null |
| pop5  | 1    | 2    | 3    | 4    | null | null | null | null | null |
| push7 | 1    | 2    | 3    | 4    | 7    | null | null | null | null |



  - 언제 객체 참조를 null로 바꿔야 할까? 
    - 객체 참조를 null로 변경하는 것은 꼭 필요할 때만 예외적으로 행해야 함
    - 특정 배열 요소가 자유로 사용 가능할 때 요소에 저장되었던 객체 참조를 반드시 null값으로 변경

  - 쓸모없는 참조를 제거하는 가장 좋은 방법
    - 참조값을 갖는 변수가 유효 범위(scope) 밖에 있도록 하는 것

  - 메모리 직접 관리할 때
    - 메모리 누출에 항상 주의해야 함
    - 특정 배열 요소가 자유로 사용 가능할 때 그 요소에 저장되었던 객체 참조를 반드시 null로 변경해야 함

2) 캐시(cache)
  - 객체 참조를 캐시에 저장한 것을 잊어버리고 객체가 더 이상 필요없을 때까지 캐시에 내버려 두는 것
  - 캐시 외부에 캐시의 키에 대한 참조가 있을 동안만 캐시에 저장된 항목이 유효한 것만으로 충분하면 WeakHashMap 사용 키로 저장된 객체가 더 이상 참조되지 않을 때 해당 항목이 자동으로 삭제 됨
  - 캐시에 저장된 항목의 생명주기가 각 항목의 키에 대한 외부 참조에 의해 결정될 때에만 WeakHashMap이 유용

  - 약한 참조
    - 약한 참조 예 - WeakHashMap의 키로만 콜백을 저장
(http://docs.oracle.com/javase/6/docs/api/java/util/WeakHashMap.html)
    - 가비지 컬렉터가 힙을 trace할 때 객체에 대한 레퍼런스가 약한 참조이면 객체는 가비지 컬렉션의 대상이 됨
    - 일반 참조(strong reference)를 사용하여 객체 레퍼런스를 복사하면 객체의 수명이 복사된 레퍼런스의 수명만큼 되도록 제한이 되는데 약한 참조(weak reference)를 사용하면 객체의 수명을 확장하지 않는다. 살아있는 동안만 관리된다.
      > 예) 같은 객체에 A1, A2 두개의 참조가 있음
A1을 null로 변경할 경우 해당 객체는 A2의 참조(strong reference)가 살아있기 때문에 가비지 컬렉션되지 않음
하지만 A2가 약한 참조(weak reference)이면 A1을 null로 변경할 경우에 해당 객체에 대해 가비지 컬렉션이 될 수 있음

3) 리스너(listener)와 콜백(callback)
  - 콜백은 등록하고 지우지 않는 API를 구현하면 콜백은 계속 누적
  - 콜백이 신속하게 가비지 컬렉션 되도록 하는 방법은 약한 참조(weak reference) 이용