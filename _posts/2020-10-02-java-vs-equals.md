---
title: Java == vs equals
tags:
- java
---

## ==
1. If any of the comparable target is primitive type, it compares by value
2. If none of the comparable target is primitive type, it compares by reference

That is:
```
int a = 6;
int b = 6;

Integer c = new Integer(6);
Integer d = new Integer(6);

System.out.println(a == b); // true
System.out.println(c == d); // false
```

## equals
java.lang.Object.equals can be overrided.
For example, with attaching lombok's @EqualsAndHashCode annotation, below code
```java
import lombok.EqualsAndHashCode;

@EqualsAndHashCode
public class EqualsAndHashCodeExample {
  private transient int transientVar = 10;
  private String name;
  private double score;
  private String[] tags;
  @EqualsAndHashCode.Exclude private int id;
  
  public String getName() {
    return this.name;
  }
}
```

is converted into

```java
import java.util.Arrays;

public class EqualsAndHashCodeExample {
  private transient int transientVar = 10;
  private String name;
  private double score;
  private String[] tags;
  private int id;
  
  public String getName() {
    return this.name;
  }
  
  @Override public boolean equals(Object o) {
    if (o == this) return true;
    if (!(o instanceof EqualsAndHashCodeExample)) return false;
    EqualsAndHashCodeExample other = (EqualsAndHashCodeExample) o;
    if (!other.canEqual((Object)this)) return false;
    if (this.getName() == null ? other.getName() != null : !this.getName().equals(other.getName())) return false;
    if (Double.compare(this.score, other.score) != 0) return false;
    if (!Arrays.deepEquals(this.tags, other.tags)) return false;
    return true;
  }
  
  @Override public int hashCode() {
    final int PRIME = 59;
    int result = 1;
    final long temp1 = Double.doubleToLongBits(this.score);
    result = (result*PRIME) + (this.name == null ? 43 : this.name.hashCode());
    result = (result*PRIME) + (int)(temp1 ^ (temp1 >>> 32));
    result = (result*PRIME) + Arrays.deepHashCode(this.tags);
    return result;
  }
  
  protected boolean canEqual(Object other) {
    return other instanceof EqualsAndHashCodeExample;
  }
} 
```

## References
[https://marobiana.tistory.com/130](https://marobiana.tistory.com/130)
[https://stackoverflow.com/questions/7520432/what-is-the-difference-between-and-equals-in-java](https://stackoverflow.com/questions/7520432/what-is-the-difference-between-and-equals-in-java)
[https://projectlombok.org/features/EqualsAndHashCode](https://projectlombok.org/features/EqualsAndHashCode)
