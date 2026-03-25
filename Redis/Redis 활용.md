### ✅ Redis 특징

- **In-Memory**
    
    모든 데이터를 RAM에 저장 (백업 / 스냅샷 제외)
    
- **Single Threaded**
    
    단일 thread에서 모든 task 처리
    
- **Cluster Mode**
    
    다중 노드에 데이터를 분산 저장하여 안정성 & 고가용성 제공
    
- **Persistence**
    
    RDB(Redis Database) + AOF(Append only file) 통해 영속성 옵션 제공
    
- **Pub/Sub**
    
    Pub/Sub 패턴을 지원하여 손쉬운 어플리케이션 개발 (e.g 채팅, 알림 등)
    

---

### ✅ Redis 장점

- 높은 성능 ⭐⭐
    
    모든 데이터를 메모리에 저장하기 때문에 매우 빠른 읽기/쓰기 속도
    
- Data Type 지원
    
    Redis에서 지원하는 Data type을 잘 활용하여 다양한 기능 구현
    
- 클라이언트 라이브러리
    
    Python, Java, JavaScript 등 다양한 언어로 작성된 라이브러리 지원
    

---

### ✅ Redis 사용 사례

- **Caching**
    - 임시 비밀번호 (One-Time Password)
    - 로그인 세션 (Session)
- **Rate Limiter**
    - Fixed-Window / Sliding-Window Rate Limiter(비율 계산기)
- **Message Broker**
    - 메시지 큐(Message Queue)
- **실시간 분석 / 계산**
    - 순위표(Rank / Leaderboard)
    - 반경 탐색(Geofencing)
    - 방문자 수 계산(Visitors Count)
- **실시간 채팅**
    - Pub/Sub 패턴

---

### ✅ Persistence

- **Persistence(영속성)**
    - Redis는 주로 캐시로 사용되지만 데이터 영속성을 위한 옵션 제공
    - SSD와 같은 영구적인 저장 장치에 데이터 저장
- **RDB(Redis Database)**
    - Point-in-time Snapshot -> 재난 복구(Disaster Recovery) 또는 복제에 주로 사용
    - 일부 데이터 유실의 위험이 있고, 스냅샷 생성 중 클라이언트 요청 지연 발생
- **AOF(Append Only File)**
    - Redis에 적용되는 Write 작업을 모두 log로 저장
    - 데이터 유실의 위험이 적지만, 재난 복구시 Write 작업을 다시 적용하기 때문에 RDB 보다 느림
- **RDB + AOF 함께 사용하는 옵션 제공**

---

### ✅ Caching

- Cache-Aside pattern

---

### ✅ DataType

- **Strings**
    - 문자열, 숫자, serialized object(JSON string) 등
    
    ```bash
    $ SET lecture inflearn-redis
    $ MSET price 100 language ko
    $ MGET lecture price language
    $ INCR price
    $ INCRBY price 10
    $ SET ‘{“lecture”: “inflearn-redis”, “language”: “en”}’
    $ SET inflearn-redis:ko:price 200
    ```
    
- **Lists**
    - String을 Linked List로 저장 -> push / pop에 최적화 O(1)
    - Queue(FIFO) / Stack(FILO) 구현에 사용
    
    ```bash
    $ LPUSH queue job1 job2 job3
    $ RPOP queue
    $ LPUSH stack job1 job2 job3
    $ LPOP stack
    $ LPUSH queue job1 job2 job3
    $ LRANGE queue -2 -1
    $ LTRIM queue 0 0
    ```
    
- **Sets**
    - Unique string을 저장하는 정렬되지 않은 집합
    - Set Operation 사용 가능(e.g. intersection, union, difference)
    
    ```bash
    $ SADD user:1:fruits apple banana orange orange
    $ SMEMBERS user:1:fruits
    $ SCARD user:1:fruits
    $ SISMEMBER user:1:fruits banana
    
    $ SADD user:2:fruits apple lemon
    
    $ SINTER user:1:fruits user:2:fruits
    $ SDIFF user:1:fruits user:2:fruits
    $ SUNION user:1:fruits user:2:fruits
    ```
    
- **Hashes**
    - field-value 구조를 갖는 데이터 타입
    - 다양한 속성을 갖는 객체를 저장할 때 유용 (Session 정보)
    
    ```bash
    $ HSET lecture name inflearn-redis price 100 language ko
    $ HGET lecture name
    $ HMGET lecture price language invalid
    $ HINCRBY lecture price 10
    ```
    
- **Sorted Sets**
    - Unique string을 연관된 score를 통해 정렬된 집합
        - Set의 기능 + 추가로 score 속성 저장
    - 내부적으로 Skip List + Hash Table로 이루어져 있고, score 값에 따라 정렬 유지
    - score가 동일하면 lexicographically(사전 편찬 순) 정렬
    
    ```bash
    $ ZADD points 10 TeamA 10 TeamB 50 TeamC
    $ ZRANGE points 0 -1
    $ ZRANGE points 0 -1 REV WITHSCORES
    $ ZRANK points TeamA
    ```
    
- **Streams**
    - append-only log에 consumer groups과 같은 기능을 더한 자료 구조
    - unique id를 통해 하나의 entry를 읽을 때, O(1) 시간 복잡도
    - Consumer Group을 통해 분산 시스템에서 다수의 consumer가 event 처리
    
    ```bash
    $ XADD events * action like user_id 1 product_id 1
    $ XADD events * action like user_id 2 product_id 1
    $ XRANGE events - +
    $ XDEL events ID
    ```
    

---

### ✅ 분산 락

- **SET NX/XX**
    - NX: 해당 Key가 존재하지 않는 경우에만 SET
    - XX: 해당 Key가 이미 존재하는 경우에만 SET
    - Null Reply SET이 동작하지 않은 경우 (nil) 응답
    
    ```bash
    $ SET greeting hello NX
    $ SET greeting hello XX
    ```
    

---

### ✅ Pub/Sub

- Publisher와 Subscriber가 서로 알지 못해도 통신이 가능하도록 decoupling 된 패턴
- Publisher는 Subscriber에게 직접 메시지를 보내지 않고, Channel에 Publish
- Subscriber는 관심이 있는 Channel을 필요에 따라 Subscribe하며 메시지 수신

→ vs. Stream 메시지가 보관되는 Stream과 달리 Pub/Sub은 Subscribe 하지 않을 때 발행된 메시지 수신 불가

```bash
$ SUBSCRIBE ch:order ch:payment
$ PUBLISH ch:order new-order
$ PUBLISH ch:payment new-payment
```