# 이벤트 기반 아키텍처(Event-Driven Architecture) 정의 및 활용

1. 이벤트 기반 아키텍처 정의

이벤트 기반 아키텍처는 시스템 내에서 발생하는 **이벤트** 를 중심으로 구성된 소프트웨어 아키텍처
패턴이다. 여기서 이벤트는 상태의 변화나 특정 액션을 의미한다. 시스템의 각 컴포넌트는 이러한
이벤트를 생성, 발행, 소비, 반응한다. 이 아키텍처는 컴포넌트 간의 느슨한 결합을 가능하게 하여
시스템의 유연성과 확장성을 높인다.

2. 이벤트 기반 아키텍처가 대두된 이유

기존의 모놀리식(monolithic) 또는 동기적(synchronous) 시스템에서는 컴포넌트 간의 결합도가 높아 변화에 유연하게 대응하기 어려웠다.
특히 대규모 데이터 처리나 실시간 반응이 필요한 애플리케이션에서는 이러한 구조가 한계를 드러냈다.
이벤트 기반 아키텍처는 이러한 문제를 해결하기 위해 등장했으며,
비동기적(asynchronous) 이벤트 처리를 통해 시스템의 성능과 확장성을 개선할 수 있게 되었다.

## Spring Framework Application Event

1. 개요
   스프링 프레임워크는 애플리케이션 내에서 컴포넌트 간의 통신을 위해 Application Event라는 이벤트 기반의 프로그래밍 모델을 제공한다.
   이를 통해 컴포넌트들은 이벤트를 **발행(publish)** 하고 **수신(listen)** 함으로써 느슨한 결합(loose coupling)을 유지하면서 상호작용할 수 있다.
   이러한 이벤트 시스템은 관찰자 패턴(Observer Pattern)을 기반으로 하며, 애플리케이션의 모듈화를 촉진하고 유지보수성을 향상시킨다.

2. Application Event의 주요 구성 요소
    1. 이벤트(Event): 이벤트는 ApplicationEvent 클래스를 상속하여 정의된다. 이벤트 객체는 발생한 이벤트에 대한 정보를 담고 있다. (e.g., OrderCreatedEvent)
    2. 이벤트 발행자(Event Publisher): ApplicationEventPublisher 인터페이스를 구현하거나 이를 주입받아 이벤트를 발행하는 역할을 한다. 
    3. 이벤트 리스너(Event Listener): @EventListener 어노테이션이나 ApplicationListener 인터페이스를 구현하여 특정 이벤트를 수신하고 처리하는 역할을 한다. 

3. 이벤트 사용 예시
   (동기적 이벤트 처리)
```java
// 이벤트 정의
public class UserRegisteredEvent {
    private final User user;

    public UserRegisteredEvent(User user) {
        this.user = user;
    }

    public User getUser() {
        return user;
    }
}
```

```java
// 이벤트 발행자
@Service
public class UserService {
    private final ApplicationEventPublisher eventPublisher;

    public UserService(ApplicationEventPublisher eventPublisher) {
        this.eventPublisher = eventPublisher;
    }

    public void registerUser(User user) {
        // 사용자 등록 로직
        eventPublisher.publishEvent(new UserRegisteredEvent(user));
    }
}
```

```java
// 이벤트 리스너

@Component
public class NotificationService {
    @EventListener
    public void handleUserRegisteredEvent(UserRegisteredEvent event) {
        // 사용자 등록 이벤트 처리 로직
        User user = event.getUser();
        System.out.println(user.getUsername() + "님이 가입하였습니다.");
    }
}
```

기본적으로 스프링의 이벤트 처리는 **동기적(synchronous)** 으로 이루어진다.
이벤트를 발행하면 해당 이벤트를 처리하는 모든 리스너가 순서대로 실행되며, 이벤트 발행 메서드는 모든 리스너의 처리가 완료될 때까지 반환되지 않는다.
이러한 방식은 이벤트 처리 순서를 보장하고, 특정 이벤트에 대한 처리 결과를 다른 리스너에게 전달할 때 유용하다.

### 특징
이벤트 발행자와 리스너가 같은 스레드에서 실행된다.
리스너 중 하나에서 예외가 발생하면 이후의 리스너는 실행되지 않는다.
트랜잭션 전파가 가능하다.

(비동기적 이벤트 처리)
비동기적 이벤트 처리의 경우 @Async 어노테이션을 사용하여 별도의 스레드에서 이벤트를 처리할 수 있다.
```java
@Component
public class AnalyticsListener {

    @Async
    @EventListener
    public void handleUserRegisteredEvent(UserRegisteredEvent event) {
        User user = event.getUser();
        // 분석 시스템에 데이터 전송
        // ...
    }
}
```

비동기 리스너는 별도의 스레드에서 실행되므로 트랜잭션 전파에 유의해야 한다! => @TransactionalEventListener 사용
비동기 메서드에서 발생한 예외는 기본적으로 로깅되지만 호출자에게 전달되지 않는다. => CompletableFuture 사용
적절한 스레드 풀 설정이 필요합니다 => @EnableAsync 및 TaskExecutor 빈 설정

이 외에도 스프링 이벤트 시스템은 특정 조건에 따라 이벤트를 필터링하거나, 이벤트 처리 순서를 제어하는 기능을 제공한다.
이를 통해 다양한 비즈니스 요구사항에 맞게 이벤트 시스템을 활용할 수 있다.

4. 참고 자료
   - [Spring Framework Reference Documentation: Application Events](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#context-functionality-events)
```