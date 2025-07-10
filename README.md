# 모니터링 시스템

## 개요

이 리포지토리는 Spring Boot 애플리케이션의 메트릭 지표를 모니터링하기 위한 **Prometheus**와 **Grafana** 기반의 모니터링 시스템을 제공합니다.

## 🚀 서비스 구성

### 📊 Prometheus
- **포트**: 9090
- **역할**: 메트릭 수집 및 저장
- **스크레이핑 간격**: 15초 (기본), 1초 (Spring Actuator)
- **모니터링 대상**:
  - Prometheus 자체 메트릭
  - Spring Boot 애플리케이션 (`host.docker.internal:8080`)

### 📈 Grafana
- **포트**: 3000
- **역할**: 데이터 시각화 및 대시보드
- **설치된 플러그인**:
  - `grafana-exploretraces-app` - 트레이스 분석
  - `grafana-lokiexplore-app` - 로그 분석
  - `grafana-metricsdrilldown-app` - 메트릭 드릴다운
  - `grafana-pyroscope-app` - 프로파일링

## 🛠️ 설치 및 실행

### 1. 사전 요구사항
- Docker
- Docker Compose
- Spring Boot 애플리케이션 (포트 8080에서 실행, `/api/v1/actuator/prometheus` 엔드포인트 활성화)

### 2. 실행 방법

```bash
# 저장소 클론
git clone <repository-url>
cd monitoring

# 서비스 시작
docker-compose up -d

# 로그 확인
docker-compose logs -f
```

### 3. 접속 정보

| 서비스 | URL | 포트 |
|--------|-----|------|
| Prometheus | http://localhost:9090 | 9090 |
| Grafana | http://localhost:3000 | 3000 |

## 📋 모니터링 대상 설정

### Spring Boot 애플리케이션 설정

Spring Boot 애플리케이션에서 다음 의존성을 추가하세요:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
<dependency>
    <groupId>io.micrometer</groupId>
    <artifactId>micrometer-registry-prometheus</artifactId>
</dependency>
```

`application.yml` 또는 `application.properties`에 다음 설정을 추가하세요:

```yaml
management:
  endpoints:
    web:
      exposure:
        include: prometheus,health,info
      base-path: /api/v1/actuator
  endpoint:
    prometheus:
      enabled: true
  metrics:
    export:
      prometheus:
        enabled: true
```

## 🔧 설정 파일

### `prometheus.yml`
- Prometheus 스크레이핑 설정
- 타겟 서버 및 메트릭 수집 간격 정의

### `docker-compose.yml`
- 서비스 오케스트레이션
- 네트워크 및 볼륨 설정
- 포트 매핑

## 📁 디렉터리 구조

```
monitoring/
├── docker-compose.yml          # Docker Compose 설정
├── prometheus.yml              # Prometheus 설정
└── README.md                   # 리파지토리 문서
```

## 🎯 사용 방법

### 1. 메트릭 확인
- Prometheus UI에서 수집된 메트릭 확인: http://localhost:9090
- PromQL 쿼리를 사용하여 메트릭 조회

### 2. 대시보드 생성
- Grafana에서 Prometheus를 데이터 소스로 추가
- 대시보드 생성 및 커스터마이징

### 3. 알람 설정
- Grafana에서 알람 규칙 생성
- 임계값 기반 알림 설정

## 🛑 서비스 중지

```bash
# 서비스 중지
docker-compose down

# 볼륨까지 삭제
docker-compose down -v
```

## 🔍 트러블슈팅

### 자주 발생하는 문제들

1. **Spring Boot 애플리케이션 연결 실패**
   - 애플리케이션이 포트 8080에서 실행 중인지 확인
   - `/api/v1/actuator/prometheus` 엔드포인트 활성화 여부 확인

2. **Grafana 플러그인 로드 실패**
   - 권한 문제일 수 있음 (`user: root` 설정 확인)
   - 플러그인 호환성 확인

3. **데이터 수집 안됨**
   - `prometheus.yml` 설정 확인
   - 네트워크 연결 상태 확인

## 📝 참고 자료

- [Prometheus 공식 문서](https://prometheus.io/docs/)
- [Grafana 공식 문서](https://grafana.com/docs/)
- [Spring Boot Actuator 문서](https://docs.spring.io/spring-boot/docs/current/reference/html/actuator.html)

---

💡 **팁**: 프로덕션 환경에서는 보안 설정과 데이터 백업을 고려하세요!

