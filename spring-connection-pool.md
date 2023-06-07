## Spring Connection Pool

요청을 처리하기 위해 스프링은 Database 와 connection 을 맺어 반환한다.

TCP/IP connection, ID/PW 인증, 세션 생성 등의 과정을 거친다.

MySQL 이러한 connection 생성이 수 ms 내로 수행된다.

Connection pool size 는 서비스의 특징, 서버 스펙, 디비 서버 스펙 등을 고려해서 성능 테스트를 해서 정해야 한다.

성능 테스트는 latency, throughput 등이 있다.

MySQL 은 대체적으로 간단한 쿼리를 빠르게 수행한다.
