# Kotlin에서 null을 다루는 방법


1. Kotlin 에서의 null 체크
2. Safe Call과 Elvis 연산자
3. 널 아님 단언
4. 플랫폼 타입

## 1. Kotlin 에서의 null 체크

```java
public boolean startsWithA1(String s) {
    if (s == null) {
        throw new IllegalArgumentException("s is null");
    }
    return s.startsWith("A");
}

public Boolean startsWithA2(String s) {
    if (s == null) {
        return null;
    }
    return s.startsWith("A");
}

public boolean startsWithA3(String s) {
    if (s == null) {
        return false;
    }
    return s.startsWith("A");
}
```

```kotlin
fun main() {
}

fun startsWithA1(s: String?): Boolean {
    if (s == null) {
        throw IllegalArgumentException("s is null")
    }
    return s.startsWith("A")
}

fun startsWithA2(s: String?): Boolean? {
    if (s == null) {
        return null
    }
    return s.startsWith("A")
}

fun startsWithA3(s: String): Boolean {
    return s.startsWith("A")
}
```

## 2. Safe Call과 Elvis 연산자

```kotlin
// Safe Call
fun main() {
    var str: String? = "ABC"
    println(str?.length) // 3
}

// Elvis 연산자
fun main() {
    var str: String? = null
    println(str?.length ?: -1) // -1
}
```

## 3. 널 아님 단언

```kotlin
fun main() {
    var str: String? = null
    println(str!!.length) // NPE
}
```

혹시나 null이 들어오면 NPE가 나오기 때문에
정말 null이 아닌게 확실한 경우에만 사용하는 것이 좋다.

## 4. 플랫폼 타입

코틀린에서 자바 코드를 가져다 쓸 떄, 자바 코드에서 null이 들어올 수 있는 경우에는
코틀린에서는 자바 코드의 타입을 플랫폼 타입으로 인식한다.

```java
public class Person {
    private String name;

    public Person(String name) {
        this.name = name;
    }

    @Nullable
    public String getName() {
        return name;
    }
}
```

```kotlin

fun main() {
    val person = Person("홍길동")
    val name = person.name // String!
    println(name.length) // NPE
}
```

코틀린에서 자바 코드를 가져다 쓸 때, 자바 코드에서 null이 들어올 수 있는 경우에는
자바 코드를 읽으며 널 가능성을 확인해야 한다.



