# Kotlin에서 변수를 다루는 방법

```kotlin
fun main() {

    var number1 = 10L
    number1 = 20L
    val number2 = 20L
    // number2 = 30L // 에러 발생
    
    var number3: Long = 10L
    
}
```

var = variable </br>
val = final value </br>
var number3: Long = 10L // 변수의 타입을 지정할 수 있다. </br>

## 간단한 팁 
모든 변수는 우선 val로 선언하고, 꼭 필요한 경우 var로 변경하는 것이 좋다. </br>

## 타입 추론
코틀린은 primitive type과 reference type을 구분하지 않는다. </br>

```kotlin
fun main() {
    val number1: Long = 10L
    val number2: long = 10L
    
}
```
코틀린은 숫자, boolean, 문자에 대해서는 겉으로는 reference type이여도 알아서 primitive type으로 변환한다. </br>
코틀린에서는 자바와 다르게 null을 허용하지 않는다. </br>
null을 허용하려면 ?를 붙여야 한다. </br>

```kotlin
fun main() {
    var number: Int? = null
    
//    var number2: Int = null // 에러 발생
}
```

kotlin에서 객체 인스턴스화를 할 때 new 키워드를 사용하지 않는다. </br>

```kotlin
fun main() {
var person = Person("홍길동", 20)
}
```

