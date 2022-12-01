---
title: Lamba and Closure
tags:
- lambda
---

- Lambda ⊂ Closure
- Lambda uses outer variable -> Closure
- Closure "captures" outer variable, Which means that variables in function defined when closure created, and function itself is copied and separated from outer environment.

## Lambda Examples
**Javascript**
```javascript
[0, 1, 2].map(n => n + 1);
```

## Closure Examples
**Javascript**
```javascript
function multiplier(a) {
  return function(b) {
    return a * b;
  }
}
var mult = multiplier(2);
mult(3); // 6
```

**Java**
```java
public class TwoMultiplier {
  private Integer a = 2;
  
  private Stream<Integer> multiply(Stream<Integer> stream) {
    return stream.map(t -> t * a);
  }

  public static void main(String... args) {    
    List<Integer> result = new TwoMultiplier()
      .multiply(Arrays.asList(1, 2, 3).stream)
      .collect(Collectors.toList()); // [2, 4, 6]
  }
}
```

## References
[https://hyunseob.github.io/2016/09/17/lambda-anonymous-function-closure/](https://hyunseob.github.io/2016/09/17/lambda-anonymous-function-closure/)
[https://futurecreator.github.io/2018/08/09/java-lambda-and-closure/](https://futurecreator.github.io/2018/08/09/java-lambda-and-closure/)
