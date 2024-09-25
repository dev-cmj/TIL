# Spring Data Redis

## 1. Spring Data Redis란?

**Spring Framework**에서 **Redis**와 상호작용할 수 있도록 도와주는 라이브러리입니다. 주로 **캐시**, **데이터 저장소**, **세션 관리** 등에 활용됩니다.

### 주요 기능

- **Redis 명령어 연동**
- **Redis Transaction 및 Pipeline** 지원
- **Redis Pub/Sub** 지원
- 다양한 데이터 구조 지원:
    - Key-Value
    - List
    - Set
    - ZSet
    - Hash

Spring에서 Redis와의 상호작용을 보다 쉽게 할 수 있도록 다양한 도구들을 제공합니다. **RedisTemplate**을 활용하여 위의 데이터 구조를 처리할 수 있습니다.


### Redis Transaction

레디스는 본래 RDBMS와 같은 트랜잭션을 지원하지 않지만 트랜잭션의 일부 개념을 지원하는 방식이다.
MULTI/EXEC/DISCARD/WATCH 등의 명령어를 사용하여 트랜잭션을 처리할 수 있다. 여러 명령어를 순차적으로 실행하고 중단 없이 실행되는 것을 보장한다.

- **MULTI**: 트랜잭션 시작
- **EXEC**: 트랜잭션에서 큐에 넣은 모든 명령 실행
- **DISCARD**: 큐에 있는 명령 취소 및 트랜잭션 종료
- **WATCH**: 특정 키를 감시하여 트랜잭션이 커밋되기 전에 데이터 변경이 감지되면 트랜잭션 중단

### 특징
1. 완벽한 원자성 보장 안함
    - 트랜잭션 내 명령어들이 순서대로 실행되지만, 중간에 실패한 명령어가 있어도 나머지 명령어는 계속 실행됨
2. 롤백이 불가
    - 트랜잭션에서 오류가 발생하더라도, 이미 실행된 명령은 롤백되지 않고, 오류가 발생한 명령어 이후의 명령만 무시된다.
3. 단일 스레드 처리
    - 레디스는 단일 스레드로 동작하기 때문에, 트랜잭션 내에서 여러 명령어를 실행할 때, 다른 명령어 겹치지 않고 순차적으로 처리
4. WATCH, UNWATCH
    - WATCH 명령은 Redis에서 낙관적 락의 역할을 한다. 트랜잭션을 시작하기 전에 WATCH 명령으로 특정 키를 감시하고, 트랜잭션 실행 시 해당 키가 변경되었는지 확인한다.
    - 변경되었다면 트랜잭션 실행을 중단한다. 이를 통해 경합 상황을 해결할 수 있다.

## 예제

### 1. MULTI - EXEC (트랜잭션 성공)
```redis
> MULTI
OK
> SET key1 value1
QUEUED
> INCR key2
QUEUED
> EXEC
1) OK
2) (integer) 1
```
MULTI로 트랜잭션 시작, QUEUED로 명령어 큐에 삽입, EXEC로 트랜잭션 실행

### 2. MULTI - EXEC (트랜잭션 실패)
```redis
> MULTI
OK
> SET key1 value1
QUEUED
INCR key_not_exist
QUEUED

> EXEC
1) OK
2) (error) ERR value is not an integer or out of range
```

2에서 key_not_exist는 존재하지 않는 키이므로 INCR 명령어가 실패한다. 이 경우, 트랜잭션은 실패하고, 나머지 명령어는 실행되지 않는다. 이 전의 명령어는 롤백되지 않는다.

### 3. WATCH를 사용한 트랜잭션 예제
```redis
> WATCH key1
OK

> GET key1
"10"

> MULTI
OK

> INCR key1
QUEUED

> EXEC
1) 
(integer) 11
```
WATCH가 key1을 감시하고 있으므로, 트랜잭션 실행 전에 key1이 변경되었는지 확인한다. key1이 변경되지 않았으므로 트랜잭션 실행이 성공한다.

### 4. WATCH를 사용한 트랜잭션 실패 예제
```redis
> WATCH key1
OK

> GET key1
"10"

> MULTI
OK

> INCR key1
QUEUED

> 다른 클라이언트에서 key1을 변경: SET key1 "15"

> EXEC
(nil)
```

트랜잭션 실행 전에 key1이 변경되었으므로, 트랜잭션 실행이 실패한다.