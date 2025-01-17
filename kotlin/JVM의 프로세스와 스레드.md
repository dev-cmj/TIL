일반적으로 코틀린 애플리케이션 실행 진입점은 main 함수이다.

main 함수를 실행 요청하면, JVM은 프로세스를 시작하고 메인 스레드를 생성한다. 그런 다음 main 함수를 호출한다.

프로세스 시작 -> 메인 스레드 -> 시작 -> 쓰레드 대기 -> 메인 스레드 종료 -> 프로세스 종료

단일 스레드 애플리케이션이란 스레드 하나만 사용해 실행되는 애플리케이션
=> 스레드는 한 번에 하나의 작업밖에 수행하지 못한다. 메인 스레드 또한 예외가 아님

안드로이드 애플리케이션의 경우는 메인 스레드에서 사용자의 입력을 받고 UI를 그리는 작업을 반복적으로 하는데,
이 때 메인 스레드에서 오래걸리는 작업이 점유되면 앱이 버벅이고 ANR이 발생한다. => 대부분 이 문제
서버에서 사용자의 요청을 단일 스레드만을 사용해 처리하면 요청 처리에 오랜 시간이 걸린다.

멀티 스레드 프로그래밍이란 여러개의 스레드를 사용해 작업을 처리하는 프로그래밍 기법
메인 스레드 외에 백그라운드 스레드를 사용한다. 오래 걸리는 작업 실행을 백그라운드 스레드로 분리
여러 개의 스레드를 사용해 병렬 처리한다.

자바에서는 java.lang.Thread 클래스를 사용해 스레드를 생성하고 실행한다.

```java
public class MyThread extends Thread {
    @Override
    public void run() {
        System.out.println("Hello, Kotlin!");
    }
}

public class Main {
    public static void main(String[] args) {
        MyThread().start();
    }
}
```

Thread를 사용한 멀티 스레드 프로그래밍의 한계
1. Thread의 start 함수를 호출할 때마다 새로운 스레드가 생성되고 재사용이 어려움
2. 개발자가 스레드 생성과 관리에 대한 책임을 가진다.
   - 개발자의 실수나 오류로 인해 메모리의 누수가 일어날 수 있음
   - 프로그램이 복잡해질 수록 스레드의 생성과 관리를 직접 하는 것은 불가능에 가까움
=> 이러한 문제를 해결하기 위한 방법은 한번 생성한 스레드를 간편하게 재사용하고, 관리를 미리 구축한 시스템에 넘긴다.
   - Executor 프레임워크 등장

Executor 프레임워크는 스레드의 집합인 스레드풀을 미리 생성해놓고 작업을 요청 받으면 쉬고 있는 스레드에 작업을 분배할 수 있는 시스템..
=> 개발자는 스레드의 생성에 직접 관여하지 않고, Executor 프레임워크에게 작업을 요청하면 된다. 

이 Executor 프레임워크는 많은 장점이 있지만 한계가 있다.
그 중 한개는 스레드 블로킹 (스레드가 사용될 수 없는 상태에 있는 것)이 일어난다.
이것을 극복하기 위해 나오는 것이 CompletableFuture을 사용행 콜백 형식으로 문제를 해결했다.
하지만 이 방식에도 문제가 있는데, 콜백이 너무 많아지게 되면 코드가 복잡해지고 유지보수가 어려워진다.
또한 예외처리가 어려워진다.

이 다음 RxJava를 사용해 결과값을 스트림으로 처리하는 방식이 나왔찌만 여전히 스레드 블로킹이 발생했다.

이러한 문제를 해결하기 위해 코틀린에서는 코루틴을 사용한다.

코루틴은 스레드를 블로킹하지 않고, 비동기적으로 작업을 처리할 수 있는 방법이다.

코루틴은 스레드 기반 작업애서 블로킹이 발생할 때, 해당 스레드를 블로킹하지 않고 다른 작업을 처리할 수 있게 해준다.

입출력 작업이나 네트워크 작업을 처리할 때 코루틴을 사용하면 스레드를 블로킹하지 않고 비동기적으로 작업을 처리할 수 있어 더 빠른 성능을 가지지만
CPU 바운드 작업을 처리할 때는 코루틴을 사용해도 성능이 더 좋아지지 않는다.