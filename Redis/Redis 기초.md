### ✅ Redis 자료형

### 1. String

- 내부 인코딩
    - `int`
    - `embstr`
    - `raw`

---

### 2. List

- 내부 구조
    - `quicklist`
        - `listpack`
- 특징
    - 블로킹 연산 (`BLPOP`, `BRPOP`)을 활용하면 **메시지 큐처럼 사용 가능**

---

### 3. Hash

- 내부 구조
    - `listpack`
    - `hashtable`
- 예시 (Spring Session)
    - **key:** spring:session:sessions:e2c472f4-e396-4e2a-b182-6aff3057c565
        
        
        | field | value |
        | --- | --- |
        | sessionAttr:org.springframework.security.oauth2.client.web.HttpSessionOAuth2AuthorizationRequestRepository.AUTHORIZATION_REQUEST |  |
        | lastAccessedTime |  |
        | sessionAttr:redirectUrl | `��t<https://jumo.im/mypage`> |
        | maxInactiveInterval |  |
        | creationTime |  |
        | sessionAttr:SPRING_SECURITY_CONTEXT |  |

---

### 4. Set

- 내부 구조
    - `intset`
    - `hashtable`

---

### 5. Sorted Set (ZSet)

- 내부 구조
    - `listpack`
    - `skiplist`
- 특징
    - 평균 시간 복잡도: `O(log N)`
    - 데이터 증가 시에도 안정적인 성능
    - 균형 트리 대비 메모리 효율 우수
    - 캐시 지역성(Locality) 활용 가능
- 단점
    - `listpack`보다 메모리 사용량 큼

---

### 6. Pub/Sub

- 실시간 메시지 전달 (비영속)
- 구독자가 소비하지 못하면 데이터 유실 발생

---

### 7. Stream

- 로그 기반 메시지 큐
- consumer group 지원
- 메시지 재처리 가능

---

### ✅ Persistence (영속성)

### 1. AOF (Append Only File)

- 기록 전략
    - `always`
    - `everysec`
    - `no`
- AOF 재작성 (Redis 7, Multi-part AOF)
    1. 자식 프로세스 fork
    2. 자식 → base AOF 생성
    3. 부모 → incremental AOF 기록
    4. 매니페스트 파일 업데이트
    5. 파일 교체 및 정리

---

### 2. AOF vs Snapshot (RDB)

- AOF
    - 장점
        - 데이터 내구성 높음
    - 단점
        - 파일 크기 큼
        - 성능 영향 존재
        - 재시작 시 로딩 느림
- Snapshot (RDB)
    - 장점
        - 파일 크기 작음
        - 빠른 복구
        - 백그라운드 처리
        - LFU/LRU 정보 유지
    - 단점
        - 일부 데이터 손실 가능

---

### ✅ 캐시 전략

### 1. 읽기 전략 (Cache Aside)

Redis 조회 → miss → DB 조회 → Redis 저장

---

### 2. 쓰기 전략

- Write-Through
    - Redis + DB 동시 쓰기
    - 장점
        - 데이터 항상 최신
    - 단점
        - 쓰기 비용 증가
        - 불필요한 캐시 발생
- Write-Back
    - Redis → 비동기 DB 반영
    - 장점
        - 쓰기 성능 빠름
    - 단점
        - 데이터 유실 위험

---

### ✅ Connection & 안정성

### 1. 커넥션 풀링

- 연결 생성 비용이 크므로 Pool 사용 권장
- 기본 최대 연결 수: `10000`
- 주의: maxclients = OS file descriptor 제한 - 32

---

### 2. 재시도 전략

- 클라이언트에서 처리
- 지수 백오프 사용

---

### ✅ Redis 내부 처리 구조

### 1. Client Query Buffer

- 클라이언트 요청 임시 저장
- 점진적으로 읽어서 처리

---

### 2. Client Output Buffer

- 응답 결과 저장
- 버퍼 증가 원인
    - 큰 데이터
    - 느린 스토리지
    - 네트워크 지연
- Pub/Sub 특징
    - 소비 지연 시
    → 버퍼 초과 → 연결 종료

---

### ✅ Key 삭제 전략

- 확률 기반 근사 알고리즘
- 정책
    - LRU
    - LFU

---

### ✅ Redis 고급 개념

### 1. RESP

- Redis 통신 프로토콜
- TCP 기반

---

### 2. SDS

- 가변 길이 문자열 구조

---

### 3. ae (Event Loop)

- 이벤트 기반 처리
- Linux: `epoll`
- 장점
    - 컨텍스트 스위칭 감소
    - 메모리 효율적
    - 높은 처리량
- 단점
    - 긴 작업이 전체 지연 유발

---

### 4. RAX

- 기수 트리 기반 자료구조
- 메모리 효율적인 키 관리