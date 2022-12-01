---
title: Java Type Erasure and Generics
tags:
- java
---

Java generics is implemented by type erasure. <br>
Type erasure is the way to remove type information on compile time and not forcing type on runtime. <br>
For example,
```java
public static <E> boolean containsElement(E [] elements, E element){
    for (E e : elements){
        if(e.equals(element)){
            return true;
        }
    }
    return false;
}
```
is transformed into
```java
public static boolean containsElement(Object [] elements, Object element){
    for (Object e : elements){
        if(e.equals(element)){
            return true;
        }
    }
    return false;
}
```
on compile time. <br>
Type erasure can be applied to class, method and variable.

### Class Type Erasure
```java
public class Stack<E> {
    private E[] stackContent;
 
    public Stack(int capacity) {
        this.stackContent = (E[]) new Object[capacity];
    }
 
    public void push(E data) {
        // ..
    }
 
    public E pop() {
        // ..
    }
}
```
is transformed into
```java
public class Stack {
    private Object[] stackContent;
 
    public Stack(int capacity) {
        this.stackContent = (Object[]) new Object[capacity];
    }
 
    public void push(Object data) {
        // ..
    }
 
    public Object pop() {
        // ..
    }
}
```
on compile time. <br>
If Generic E is bounded:
```java
public class BoundStack<E extends Comparable<E>> {
    private E[] stackContent;
 
    public BoundStack(int capacity) {
        this.stackContent = (E[]) new Object[capacity];
    }
 
    public void push(E data) {
        // ..
    }
 
    public E pop() {
        // ..
    }
}
```
is transformed into
```java
public class BoundStack {
    private Comparable [] stackContent;
 
    public BoundStack(int capacity) {
        this.stackContent = (Comparable[]) new Object[capacity];
    }
 
    public void push(Comparable data) {
        // ..
    }
 
    public Comparable pop() {
        // ..
    }
}
```
on compile time. That is, E is converted into its first bound class: Comparable.

## Method Type Erasure
```java
public static <E extends Comparable<E>> void printArray(E[] array) {
    for (E element : array) {
        System.out.printf("%s ", element);
    }
}
```
is converted into
```java
public static void printArray(Comparable[] array) {
    for (Comparable element : array) {
        System.out.printf("%s ", element);
    }
}
```
on compile time.

## References
[https://www.baeldung.com/java-type-erasure](https://www.baeldung.com/java-type-erasure)
[https://stackoverflow.com/questions/27606449/how-exactly-do-generics-work](https://stackoverflow.com/questions/27606449/how-exactly-do-generics-work)
