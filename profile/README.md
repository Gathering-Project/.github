
OTTERMEET 🦦
=======
![22조ver2_540_260.png](/profile/assets/ottermeet2.png)


<br>

# 📢 프로젝트 소개

### **OTTERMEET : JOIN, CONNECT, ENJOY !**
**현대 사회의 개인주의 속에서, 취미를 함께할 사람들과 소모임을 쉽게 만들고 참여할 수 있는 플랫폼입니다.** <br>
웹 기반으로 다양한 활동을 함께할 사람들과 자유롭게 소모임을 구성할 수 있으며,
안전하고 간편하게 소모임을 생성하고 활발한 커뮤니티를 형성할 수 있도록 직관적인 사용자 경험과 신뢰 기반의 환경을 제공합니다.

<br>

# 🏅 프로젝트 핵심 목표
### 1. 실시간 매칭을 통한 1:1 채팅 서비스 제공
   - RabbitMQ와 WebSocket-STOMP를 활용해 사용자 간 실시간 매칭과 안정적인 1:1 채팅 서비스를 제공
   - 매칭 성공 시 즉시 채팅방을 생성하며 빠르고 효율적인 커뮤니케이션 환경을 구축
### 2. 검색 고도화 및 성능 최적화
   - ElasticSearch와 Nori 형태소 분석기를 활용해 대규모 데이터에서도 빠르고 정확한 검색 서비스 제공 
   - 소모임 제목, 지역 기반 복합 검색과 연관 키워드 추천 기능을 통해 사용자 만족도 향상
### 3. 실시간 알림 시스템 구현
   - RabbitMQ와 WebSocket 기반 알림 시스템을 구축하여 사용자 맞춤형 알림 제공
   - 참가 신청 승인, 새로운 이벤트 생성 등의 주요 활동에 대한 알림을 실시간으로 전달
### 4. 위치 기반 사용자 경험 강화
   - Geocoding API와 Places API를 활용하여 사용자의 관심 지역에 맞는 장소 추천 서비스를 제공
   - Redis 캐싱으로 반복 요청 처리 성능을 최적화하여 사용자 경험을 향상
### 5. 동시성 문제 해결 및 안정적 데이터 관리
   - Redis 기반 Redisson 분산락을 활용하여 다중 요청 처리 중 데이터 무결성을 보장
   - 동시성 문제로 인한 충돌 방지와 성능 최적화를 통해 안정적인 이벤트 참가 시스템 구축
### 6. 운영 효율성 및 서비스 안정성 강화
   - Docker와 GitHub Actions 기반 CI/CD 파이프라인으로 배포 자동화 및 운영 호율성 극대화
   - Prometheus와 Grafana를 활용한 실시간 모니터링으로 시스템 성능과 안정성 확보


# 🗝️ KEY SUMMARY

## 🍁 **트러블 슈팅 : 매칭 서비스 트래픽 폭증에 따른 서버 과부하 문제**
### 1. 문제 상황
- [x] 매칭 서비스는 사용자 트래픽 폭증 상황에서도 안정적으로 작동해야 했습니다. 
- [x] 초당 1000명의 사용자가 몰리는 상황을 가정해 부하 테스트를 진행한 결과, <br>
CPU 사용량이 95%에 도달하며 서버 장애 위험이 매우 높다는 문제가 확인되었습니다.<br>
　<br>
![개선 전 CPU](/profile/assets/troubleshooting/매칭/매칭트러블1.png)  

### 2. 문제 원인
  - 매칭 로직이 메인 서버에서 모두 처리되면서 과도한 부하 발생
  - 대량의 매칭 요청이 서버에 집중되며 CPU 사용량이 급증

### 3. 해결 방안
- [x] 매칭 서버 분리
  -기존 메인 서버에서 매칭 로직을 제거하고 매칭 전용 서버를 분리하여 로직 처리
  - 메인 서버는 매칭 요청을 수신해 매칭 서버로 데이터를 전달하는 역할로 단순화
  - 매칭 서버는 매칭 처리에만 집중하여 부하를 분산
- [x] 테스트 및 결과 분석
  - 매칭 서버 분리 후 동일한 조건에서 부하 테스트를 재실행
  - CPU 사용량이 94.8%에서 50.4%로 감소하며 46.86%의 부하 감소 효과를 확인.<br>
  　 <br>
  ![개선 후 CPU](/profile/assets/troubleshooting/매칭/매칭트러블1-2.png) 

### 4. 개선 결과
- 메인 서버의 부하가 크게 감소해 장애 위험이 줄어듦
- 매칭 로직 처리 속도가 안정적으로 유지되며 사용자 경험이 향상됨

| **조건**         | **테스트 전 CPU 사용량** | **테스트 후 CPU 사용량** | **개선율**       |
|------------------|-------------------------|-------------------------|-----------------|
| 초당 1000명 요청 | 94.8%                  | 50.4%                  | **46.86% 감소** |


<br>

## 🍁 **핵심 성능 개선 : 매칭 서비스 비동기 처리 도입**

- [x] 매칭 서비스의 효율적인 운영을 위해 **RabbitMQ를 활용한 비동기 처리 방식**을 도입
- [x] 기존의 동기적 처리에서 발생한 성능 병목을 해결하고 요청 처리량을 대폭 향상시킴

### 1. 성능 개선 목표

1. **매칭 요청 처리량 증대**
   - 기존 동기 처리 방식에서 초당 요청 처리량이 낮아 성능 병목 현상이 발생
   - 비동기 처리 방식을 통해 요청 처리 속도를 최적화

2. **서버 부하 감소**
   - 메인 서버의 매칭 로직을 메시지 큐로 분리하여 부하를 분산
   - 고부하 상황에서도 안정적인 응답 시간을 유지


### 2. 적용 내용

1. **RabbitMQ 도입:**
   - 기존 동기식 API 호출 방식을 비동기 메시지 처리 방식으로 전환
   - 메인 서버는 요청 데이터를 RabbitMQ 큐에 전달, 매칭 서버는 큐의 메시지를 비동기로 소비하여 매칭 로직 실행

2. **비동기 환경 구축:**
   - 서버 간의 병렬 작업을 강화하여 요청 처리량 최적화
   - 메인 서버와 매칭 서버의 역할을 명확히 분리하여 부하 관리 용이

### 3. 개선 결과

- **처리량 향상**
  - 기존 동기 방식에서는 요청 처리량이 **초당 54.7ops/s**에 불과했으나, 비동기 방식을 도입한 후 **181ops/s**로 처리량이 **3.31배** 증가

| **조건**         | **기존 요청 처리량**       | **개선 후 요청 처리량**      | **개선율**       | **테스트 전**                                                                                         | **테스트 후**                                                    |
|------------------|---------------------------|-----------------------------|------------------|------------------------------------------------------------------------------------------------------|--------------------------------------------------------------|
| 초당 1000명 요청 | **54.7ops/s**             | **181ops/s**                | **3.31배 증가**  | ![개선 후 CPU](/profile/assets/troubleshooting/매칭/매칭트러블2.png)  | ![개선 후 CPU](/profile/assets/troubleshooting/매칭/매칭트러블2-2.png) |

- **시스템 안정성 향상**
  - 메시지 큐를 통한 비동기 처리로 요청 간 병목 현상을 해소하고, 메인 서버의 부하를 대폭 줄여 고부하 상황에서도 안정적인 성능 유지

```
비동기 처리 도입을 통해 처리량을 극대화하고, 안정적이고 확장 가능한 매칭 서비스를 구축함
```
<br>

# 🖥️ 인프라 아키텍처 & 적용기술
![infra](/profile/assets/인프라1121.png)

-  위 아키텍처는 모놀리틱(Monolithic) 기반의 서비스 구조를 AWS 환경에서의 구조를 나타냅니다. 
- 서비스는 Redis, RabbitMQ를 통해 통신하며, Docker로 컨테이너화되어 GitHub Actions 기반 CI/CD를 통해 자동 배포됩니다.

<br>

## ⚒️ 기술 스택

<details>
<summary><strong> 기술 스택 전체 펼쳐보기 </strong></summary>

  ### Backend
  ![Java](https://img.shields.io/badge/java-%23ED8B00.svg?style=for-the-badge&logo=openjdk&logoColor=white)
  ![Spring](https://img.shields.io/badge/Spring%20Boot-6DB33F?style=for-the-badge&logo=Spring&logoColor=white)
  ![Spring](https://img.shields.io/badge/Spring%20JPA-6DB33F?style=for-the-badge&logo=Spring&logoColor=white)
  ![Spring](https://img.shields.io/badge/springsecurity-6DB33F?style=for-the-badge&logo=springsecurity&logoColor=white)
  ![JWT](https://img.shields.io/badge/JWT-000000?style=for-the-badge&logo=jsonwebtokens&logoColor=white)
  ![Gradle](https://img.shields.io/badge/Gradle-02303A?style=for-the-badge&logo=gradle&logoColor=white)
  ![Kafka](https://img.shields.io/badge/Kafka-231F20?style=for-the-badge&logo=Apache-Kafka&logoColor=white)
  ![Redis](https://img.shields.io/badge/redis-%23DD0031.svg?style=for-the-badge&logo=redis&logoColor=white)
  ![Redisson](https://img.shields.io/badge/Redisson-FFA500?style=for-the-badge)
  ![Redis Rate Limiter](https://img.shields.io/badge/Redis%20Rate%20Limiter-DC382D?style=for-the-badge&logo=Redis&logoColor=white)
  ![RabbitMQ](https://img.shields.io/badge/RabbitMQ-FF6600?style=for-the-badge&logo=rabbitmq&logoColor=white)
  ![WebSocket](https://img.shields.io/badge/WebSocket-000000?style=for-the-badge&logo=websocket&logoColor=white)
  ![OAuth 2.0](https://img.shields.io/badge/OAuth%202.0-3C8DBC?style=for-the-badge&logo=oauth&logoColor=white)
  ![STOMP](https://img.shields.io/badge/STOMP-7952B3?style=for-the-badge&logo=stomp&logoColor=white)

  ### Databases
  ![MySQL](https://img.shields.io/badge/mysql-4479A1.svg?style=for-the-badge&logo=mysql&logoColor=white)
  ![MongoDB](https://img.shields.io/badge/MongoDB-47A248?style=for-the-badge&logo=mongodb&logoColor=white)
  
  
  ### DevOps
  
  ![EC2](https://img.shields.io/badge/amazonec2-FF9900?style=for-the-badge&logo=amazonec2&logoColor=white)
  ![AWS S3](https://img.shields.io/badge/AWS%20S3-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
  ![Docker](https://img.shields.io/badge/docker-2496ED?style=for-the-badge&logo=docker&logoColor=white)
  ![AmazonRds](https://img.shields.io/badge/amazonrds-527FFF?style=for-the-badge&logo=amazonrds&logoColor=white)
  ![GitHub Actions](https://img.shields.io/badge/GitHub%20Actions-2088FF?style=for-the-badge&logo=github-actions&logoColor=white)
  ![ECR](https://img.shields.io/badge/Amazon%20ECR-FF9900?style=for-the-badge&logo=Amazon-AWS&logoColor=white)
  
  [//]: # (![Jenkins]&#40;	https://img.shields.io/badge/Jenkins-D24939?style=for-the-badge&logo=Jenkins&logoColor=white&#41;)
  

### Monitoring
  [![Elasticsearch](https://img.shields.io/badge/elasticsearch-005571.svg?style=for-the-badge&logo=elasticsearch&logoColor=white)](https://www.elastic.co/elasticsearch/)
  [![Kibana](https://img.shields.io/badge/kibana-005571.svg?style=for-the-badge&logo=kibana&logoColor=white)](https://www.elastic.co/kibana/)
  [![Grafana](https://img.shields.io/badge/grafana-F46800.svg?style=for-the-badge&logo=grafana&logoColor=white)](https://grafana.com/)
  ![Prometheus](https://img.shields.io/badge/prometheus-E6522C.svg?style=for-the-badge&logo=prometheus&logoColor=white)

### Tools
  ![JUnit5](https://img.shields.io/badge/JUnit5-FB4F14?style=for-the-badge&logo=JUnit5&logoColor=white)
  ![GitHub](https://img.shields.io/badge/github-%23121011.svg?style=for-the-badge&logo=github&logoColor=white)
  ![IntelliJ IDEA](https://img.shields.io/badge/IntelliJIDEA-000000.svg?style=for-the-badge&logo=intellij-idea&logoColor=white)
  ![Git](https://img.shields.io/badge/git-F05032?style=for-the-badge&logo=git&logoColor=white)
  ![Jmeter](https://img.shields.io/badge/apachejmeter-D22128?style=for-the-badge&logo=apachejmeter&logoColor=white)
  ![Notion](https://img.shields.io/badge/Notion-%23000000.svg?style=for-the-badge&logo=notion&logoColor=white)
  ![Slack](https://img.shields.io/badge/Slack-4A154B?style=for-the-badge&logo=slack&logoColor=white)
  ![Google Places API](https://img.shields.io/badge/Google%20Places-4285F4?style=for-the-badge&logo=google&logoColor=white)
  ![Geocoding](https://img.shields.io/badge/Geocoding-4285F4?style=for-the-badge&logo=googlemaps&logoColor=white)
  ![Toss Payments](https://img.shields.io/badge/Toss%20Payments-0077FF?style=for-the-badge&logo=toss&logoColor=white)

</details>

<details>
<summary><strong> 📦 적용 기술 상세보기 </strong></summary>

| **분류**         | **기술 및 도구**                              | **적용 위치**                                   | **특징 / 구체적 역할**                               |
|-------------------|----------------------------------------------|------------------------------------------------|-----------------------------------------------------|
| **데이터베이스**  | **MySQL**<br>**MongoDB**<br>**Redis**        | - 메인 데이터베이스<br>- 채팅 데이터베이스<br>- 실시간 캐싱 및 동시성 제어 | - 관계형 데이터 관리<br>- 비정형 데이터 처리<br>- 빠른 응답 속도 제공 |
| **메시징 시스템** | **RabbitMQ**                                 | 매칭 서비스 및 실시간 알림                       | - 비동기 메시지 전달<br>- 트래픽 급증 시 서버 부하 분산 |
| **캐싱**         | **Redis**                                    | 이벤트 참가 동시성 제어 및 위치 데이터 캐싱      | - 높은 처리 성능<br>- 분산 락 구현으로 동시성 문제 해결 |
| **동시성 제어**   | **Redisson**<br>**Redis Sentinel**            | 이벤트 참가 및 동시 요청 제어                    | - 분산 락을 활용해 데이터 무결성 유지<br>- 고가용성을 보장하며 안정적 처리 |
| **검색 및 모니터링** | **ElasticSearch**<br>**Prometheus & Grafana**<br>**Kibana**<br>**Logstash** | - 검색 최적화<br>- 시스템 성능 모니터링 및 로그 관리 | - 빠르고 정확한 검색 지원<br>- 주요 지표 시각화로 빠른 대응 가능<br>- 로그 데이터 분석 및 문제 추적 |
| **채팅 서비스**   | **WebSocket**<br>**STOMP**<br>**RabbitMQ**   | 1:1 채팅 및 실시간 메시징                       | - 양방향 실시간 메시지 송수신<br>- 안정적인 메시지 전달 및 저장 |
| **알림 시스템**   | **RabbitMQ**<br>**WebSocket**                | 주요 활동 알림 제공                             | - 실시간 알림 전송<br>- 사용자 맞춤형 주요 소식 제공 |
| **인프라 및 배포**| **Docker**<br>**Amazon EC2**<br>**Amazon S3**<br>**GitHub Actions** | - 모든 서비스 컨테이너화<br>- 애플리케이션 서버 및 RabbitMQ, Redis 운영<br>- 이미지 및 정적 파일 저장소<br>- CI/CD 자동화 파이프라인 | - 개발 환경 간 일관성 제공<br>- 서버 리소스 탄력적 할당<br>- 대용량 정적 자산 안정적 관리<br>- 배포 효율성 증대 |

</details>

##  📑 API 명세서 ([Swagger API 바로가기]())

<details>
  <summary> 🔖 API 명세 펼쳐보기</summary>

<details>
  <summary><span style="font-size:1.2em"><strong>유저</strong></span></summary>

![유저1.png](/profile/assets/api/유저api1.png)
![유저2.png](/profile/assets/api/유저api2.png)

</details>

<details>
  <summary><span style="font-size:1.2em"><strong>멤버</strong></span></summary>

![멤버1.png](/profile/assets/api/멤버api.png)
![멤버3.png](/profile/assets/api/멤버api3.png)

</details>

<details>
  <summary><span style="font-size:1.2em"><strong>소모임</strong></span></summary>

![소모임1.png](/profile/assets/api/소모임api1.png)
![소모임3.png](/profile/assets/api/소모임api3.png)


</details>

<details>
  <summary><span style="font-size:1.2em"><strong>참여관리</strong></span></summary>

![소모임참여.png](/profile/assets/api/소모임참여api.png)

</details>

<details>
  <summary><span style="font-size:1.2em"><strong>이벤트</strong></span></summary>

![이벤트1.png](/profile/assets/api/이벤트api1.png)
![이벤트2.png](/profile/assets/api/이벤트api2.png)

</details>

<details>
  <summary><span style="font-size:1.2em"><strong>댓글</strong></span></summary>

![댓글1.png](/profile/assets/api/댓글api.png)

</details>

<details>
  <summary><span style="font-size:1.2em"><strong>첨부파일</strong></span></summary>

![첨부파일.png](/profile/assets/api/첨부파일api.png)

</details>

<details>
  <summary><span style="font-size:1.2em"><strong>채팅</strong></span></summary>

![채팅.png](/profile/assets/api/채팅.png)

</details>

<details>
  <summary><span style="font-size:1.2em"><strong>매칭</strong></span></summary>

![매칭.png](/profile/assets/api/매칭.png)

</details>

<details>
  <summary><span style="font-size:1.2em"><strong>광고</strong></span></summary>

![광고.png](/profile/assets/api/광고.png)
![광고2.png](/profile/assets/api/광고2.png)

</details>


<details>
  <summary><span style="font-size:1.2em"><strong>결제</strong></span></summary>

![결제.png](/profile/assets/api/결제1.png)
![결제2.png](/profile/assets/api/결제2.png)


</details>

<details>
  <summary><span style="font-size:1.2em"><strong>검색</strong></span></summary>

![검색.png](/profile/assets/api/검색.png)

</details>

<details>
  <summary><span style="font-size:1.2em"><strong>추천</strong></span></summary>

![장소추천.png](/profile/assets/api/장소추천.png)

</details>

</details>



## 🗯️ 주요 기능

* **소모임과 이벤트 관리**

    - [x] 소모임 생성 및 관리: 사용자 주도로 소모임을 생성하고, 필요한 정보 수정 및 관리 기능 제공
    - [x] 소모임 참가 승인 시스템 :  관리자의 승인/거절을 통해 효율적인 참여 관리 지원
    - [x] 이벤트 관리 : 소모임 내 일정을 소모임 멤버가 관리하고 참가할 수 있음
    - [x] 실시간 알림: 신청 및 승인 상태를 즉시 알림으로 전달하여 신속한 소통 가능

* **매칭 서비스**

    - [x] 관심사 및 위치 기반 매칭: 공통 관심사나 위치를 통해 사용자 간 자동 매칭을 지원하여 참여 기회 제공
    - [x] 메세지 큐를 활용해 안정적인 비동기 매칭 서비스 구현

* **1:1 실시간 채팅**

    - [x] 사용자 매칭 시 1대1 채팅이 지원
    - [x] WebSocket-STOMP 프로토콜을 활용하여 실시간 메시지 송수신을 통해 원활한 소통 지원
    - [x] QOS 메세지 처리의 안정성 보장, 메세지 전송 속도 제한, 메세지 손실 최소화
    - [x] 비동기처리: 작업 병렬화와 리소스 효율성, 처리 속도 개선

* **실시간 알림 시스템**

    - [x] Message Broker : 참가 신청 승인, 새로운 이벤트 등 주요 활동에 대한 실시간 알림 제공
    - [x] 사용자 맞춤형 주요 소식을 놓치지 않도록 지원

* **위치 기반 장소 추천 서비스**

    - [x] Geocoding API를 활용하여 사용자의 입력 주소를 기반으로 정확한 좌표 정보 제공
    - [x] Places API를 통해 사용자의 현재 위치 또는 입력된 좌표를 중심으로 주변 장소 추천
    - [x] 사용자 선호도와 반경, 장소 유형을 고려한 맞춤형 추천 결과 제공
    - [x] Redis를 활용하여 위치 정보와 추천 결과를 캐싱, 반복 요청 시 빠른 응답 제공

* **광고 및 결제 시스템**

    - [x] 소모임 배너 노출 일자별 광고 서비스 제공
    - [x] Spring Scheduler를 활용해 광고 상태 기반 관리 자동 갱신
    - [x] Toss Payments API를 활용한 결제 관리
    - [x] Redis를 활용한 멱등성 키 관리로 중복 요청 방지
    - [x] Spring Scheduler로 결제 타임아웃 자동 처리
  
* **ElasticSearch 기반 검색 기능**
    
    - [x] ElasticSearch의 역색인 구조를 활용해 대규모 데이터에서도 빠른 응답 시간 제공
    - [x] 로그 데이터에서 키워드 기반 검색, 특정 문구를 포함하는 데이터 찾기를 제공
    - [x] 고급 텍스트 검색: 소모임 제목과 지역을 포함한 전체 텍스트 검색 기능 제공

<br>



# 💭 기술적 고도화

## 기술적 의사결정  ([✅ 기술적 의사결정 WIKI 바로가기](https://github.com/Gathering-Project/Gathering/wiki))

<details>
  <summary><span style="font-size:1.2em"><strong>매칭 서비스 기술적 의사결정</strong></span></summary>

- RabbitMQ는 **설정의 간단함**, **메시지 전달 보장의 용이성**, **다양한 라우팅 방식 지원**, **소규모 트래픽 처리에 적합성** 등의 이유로 매칭 서비스의 요구사항에 가장 부합하여 최적의 선택이었음.

| **번호** | **요구사항**             | **기술 선택지**                          | **선택한 기술**            | **근거**                                                                                                                       |
|--------|----------------------|----------------------------------------|--------------------------|-------------------------------------------------------------------------------------------------------------------------------|
| 1      | **메시지 브로커의 안정성**     | Kafka, RabbitMQ       | **RabbitMQ**             | - Kafka는 데이터 영속성 보장이 강점이나, 매칭 서비스에서 매칭 이력 유실은 큰 문제가 되지 않음.<br>- RabbitMQ는 메시지 전달 보장이 용이하고 간단한 설정으로 운영 가능. |
| 2      | **초기 설정 및 학습 비용**    | Kafka, RabbitMQ  | **RabbitMQ**             | - Kafka는 초기 설정 및 학습 비용이 높음.<br>- RabbitMQ는 설정 및 학습이 상대적으로 간단하여 소규모 프로젝트에 적합.                           |
| 3      | **데이터 처리 및 라우팅 유연성** | Kafka, RabbitMQ    | **RabbitMQ**             | - RabbitMQ는 다양한 라우팅 방식 지원 (Direct, Topic, Fanout 등).<br>- 매칭 실패/성공, 채팅방 생성 등 조건별 데이터 전송에 적합.              |
| 4      | **소규모 트래픽 처리**       | Kafka, RabbitMQ      | **RabbitMQ**             | - Kafka는 대규모 트래픽 및 분산 환경에 최적화되어 소규모 프로젝트에는 과한 옵션.<br>- RabbitMQ는 소규모 트래픽에서도 안정적인 성능 발휘.        |

📍 매칭 시퀀스 다이어그램
  ![매칭](/profile/assets/sequence/매칭시퀀스.webp)

</details>

<details>
  <summary><span style="font-size:1.2em"><strong>채팅 서비스 기술적 의사결정</strong></span></summary>

- RabbitMQ와 WebSocket-STOMP의 조합을 통해 **실시간 메시지 전달**, **안정적인 메시지 전송**, **유연한 데이터 저장**을 충족시킬 수 있었으며, MongoDB와 비동기 처리로 시스템 성능을 더욱 강화함.

| **요구사항**                    | **기술 선택지**          | **선택한 기술**     | **근거**                                                                                                                                                                                                                  |
|--------------------------------|------------------------|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. 실시간 메시지 전달**        | WebSocket-STOMP       | **WebSocket-STOMP** | - WebSocket은 실시간 양방향 통신에 적합<br>- STOMP 프로토콜은 메시지 브로커(RabbitMQ)와 통합이 용이<br>- 복잡한 라우팅 및 구독 관리 가능<br>- 대규모 채팅 서비스에 적합 |
| **2. 안정적인 메시지 전송 및 저장** | RabbitMQ, Redis Pub/Sub, Kafka | **RabbitMQ (Direct Exchange)** | - Direct Exchange를 통해 지정된 라우팅 키로 정확한 큐에 메시지 전달<br>- 메시지 보장(Acknowledgment) 및 QoS 설정 지원<br>- Redis Pub/Sub은 메시지 보장에 한계가 있음<br>- Kafka는 1:1 메시징에는 과도한 선택으로 판단 |
| **3. 낮은 레이턴시와 높은 처리량** | WebSocket, RabbitMQ   | **비동기 처리**     | - Java의 CompletableFuture를 활용한 비동기 작업 처리<br>- 메시지 전송과 브로커 전달을 비동기적으로 처리하여 응답성 개선<br>- 메인 스레드의 부담을 줄여 시스템 효율성 향상 |
| **4. 대화 내용 저장 및 조회 가능** | MySQL, MongoDB        | **MongoDB**         | - NoSQL 기반으로 비정형 데이터 처리에 적합<br>- 유저, 채팅방, 타임스탬프 등의 데이터를 문서 지향 방식으로 저장<br>- 수평적 확장(horizontal scaling)이 용이<br>- MySQL은 대량의 메시지 저장 및 빠른 검색에서 비효율적 |
| **5. 안정성 및 처리량 관리**      | RabbitMQ QoS          | **RabbitMQ QoS 적용** | - Prefetch Count를 설정하여 소비자의 메시지 처리량 제한<br>- 메시지 소비 속도 제어로 리소스 안정적 관리 가능                                                                                                                                  |

📍 채팅, 매칭-채팅 시퀀스 다이어그램
![채팅](/profile/assets/sequence/채팅시퀀스.png)
![매칭](/profile/assets/sequence/채팅시퀀스.png)

</details>

<details>
  <summary><span style="font-size:1.2em"><strong>이벤트 참가 동시성 제어 기술적 의사결정</strong></span></summary>

- edisson 분산 락은 **요청 순서와 데이터 일관성 관리**, **고부하 환경에서의 성능 안정성**, **낮은 동시성 및 고부하 환경 모두에서의 테스트 결과**를 바탕으로 최적의 동시성 제어 솔루션으로 선정되었음.

| **번호** | **요구사항**           | **기술 선택지**        | **선택한 기술**             | **근거**                                                                                |
|--------|--------------------|-------------------|--------------------------|---------------------------------------------------------------------------------------|
| 1      | **동시성 제어**         | 낙관락<br>비관락<br>분산락  | **Redisson 분산 락**   | - Redis를 활용하여 요청 순서와 데이터 일관성을 효율적으로 관리.<br>- 락 획득 실패 시 재시도를 통해 작업 충돌 방지 및 데이터 무결성 유지. |
| 2      | **성능 유지**          | 낙관락<br>비관락<br>분산락  | **Redisson 분산 락**   | - 락 획득/해제 과정에서 자동 갱신을 지원해 고부하 환경에서도 안정적 성능 제공.<br>- 부하 상황에서도 성능 저하 없이 데이터 일관성 유지.     |
| 3      | **테스트 결과 기반 의사결정** | 낙관락<br>비관락<br>분산락  | **Redisson 분산 락**   | - 낮은 동시성 및 고부하 환경 모두에서 데이터 무결성과 성능을 보장하는 테스트 결과 확인.<br>- 실사용 환경에서도 안정적으로 동작 가능.       |


📍 동시성 제어 시퀀스 다이어그램
![동시성제어](/profile/assets/sequence/동시성제어시퀀스.png)


</details>

<details>
  <summary><span style="font-size:1.2em"><strong>알림 기술적 의사결정</strong></span></summary>

- RabbitMQ는 **낮은 지연 시간**, **메시지 신뢰성 보장**, **설정 및 유지 관리의 용이성**, **대용량 데이터 스트리밍 요구사항 부재** 등의 이유로 매칭 서비스의 요구사항에 가장 부합하여 최적의 선택이었음.

| **번호** | **요구사항**                          | **기술 선택지**             | **선택한 기술 및 근거**                                                                                           |
|----------|---------------------------------------|-----------------------------|-------------------------------------------------------------------------------------------------------------------|
| 1        | 실시간 매칭을 위한 낮은 지연 시간 필요 | RabbitMQ, Kafka             | **RabbitMQ**: 매우 낮은 지연 시간(수 밀리초 수준)으로 메시지를 전달하므로 실시간 매칭에 적합.                              |
| 2        | 매칭 시 알람 메시지의 신뢰성 필요      | RabbitMQ, Kafka             | **RabbitMQ**: 메시지 확인(Acknowledgement)과 재전송 메커니즘을 제공하여 메시지 신뢰성을 보장.                            |
| 3        | 설정 및 유지 관리 용이성               | RabbitMQ, Kafka             | **RabbitMQ**: GUI 기반 관리 인터페이스를 제공하며 설정이 간단하여 운영과 유지 관리가 편리함.                                |
| 4        | 대용량 데이터 스트리밍 요구사항 없음    | RabbitMQ, Kafka             | **RabbitMQ**: Kafka는 대규모 로그 처리나 데이터 스트리밍에 특화되었으나, 프로젝트에는 해당 요구사항이 없어 RabbitMQ가 적합. |

📍 알람 시퀀스 다이어그램
![알림](/profile/assets/sequence/알람시퀀스.webp)


</details>

<details>
  <summary><span style="font-size:1.2em"><strong>위치기반 장소추천 서비스 기술적 의사 결정</strong></span></summary>

- Redis는 **실시간 데이터 처리**, **높은 응답 속도 유지**, **중복 API 호출 방지 및 비용 절감**에 강점을 가지고 있어, 프로젝트 요구사항에 가장 부합하는 선택이었음.

| **번호** | **요구사항**                                        | **기술 선택지**       | **선택한 기술** | **근거**                                                                                                                                           |
|----------|----------------------------------------------------|------------------|------------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| 1        | **정확한 주변 장소 추천**                           | Redis<br>데이터베이스 캐싱 | **Redis**  | - 메모리 기반 저장소로 실시간 데이터 처리에 적합.<br>- Disk 기반 DB 캐싱보다 빠른 응답 속도를 제공.                                                |
| 2        | **높은 응답 속도 유지**                              | Redis<br>데이터베이스 캐싱 | **Redis**  | - 외부 API 호출 결과를 캐싱해 API 요청 부하를 줄이고 응답 속도를 개선.<br>- 캐시 데이터 유효기간을 설정하여 불필요한 데이터 오염 방지.             |
| 3        | **중복 API 호출 방지 및 비용 절감**                  | 낙관락<br>분산락       | **분산락**    | - Redis의 분산 락을 활용하여 다수의 동시 요청에서 동일 키 중복 호출 방지.<br>- 충돌 상황에서도 데이터 일관성을 보장하여 시스템 안정성 유지.         |

</details>

<details>
  <summary><span style="font-size:1.2em"><strong>검색 고도화 기술적 의사결정</strong></span></summary>

- ElasticSearch는 **단일 및 복합 검색 최적화**, **텍스트 기반 고급 검색 지원**, **한국어 검색 정확도 향상** 등의 강점을 통해 프로젝트의 검색 요구사항에 가장 적합한 선택이었음.

| **번호** | **요구사항**                                   | **기술 선택지**                          | **선택한 기술**               | **근거**                                                                                                                   |
|----------|---------------------------------------------|---------------------------------------|----------------------------|---------------------------------------------------------------------------------------------------------------------------|
| 1        | **단일 필드 검색 최적화**                     | ElasticSearch                         | **ElasticSearch**          | - "소모임 제목"과 "지역" 필드에 각각 단일 인덱스를 생성하여 기본 검색 성능 최적화.<br>- 빠른 검색 성능과 확장성 제공.       |
| 2        | **복합 검색 성능 고도화**                     | ElasticSearch                         | **ElasticSearch**          | - 두 필드를 복합 인덱스로 묶어 결합 검색의 효율성과 성능을 높임.<br>- 복합 조건 검색 처리에 유리.                          |
| 3        | **텍스트 기반 고급 검색 지원**                 | ElasticSearch<br>Nori 형태소 분석기     | **ElasticSearch & Nori 분석기** | - Nori 형태소 분석기를 통해 한국어 검색 정확도를 향상.<br>- 조사, 어미 제거로 핵심 단어 추출 및 텍스트 검색 정밀도 강화.<br>- 사용자 사전 및 동의어 기능 지원. |


</details>

<br> 

## 트러블 슈팅 ([✅ 트러블 슈팅 WIKI 바로가기](https://github.com/Gathering-Project/Gathering/wiki))


## 1️⃣ 매칭 서비스 고도화 트러블 슈팅

- [x] 매칭 서비스는 대량의 트래픽이 발생할 경우 서버 과부하 및 성능 저하 문제가 발생함. 
- [x] 이를 해결하기 위해 매칭 로직 분리와 비동기 처리 방식 도입 등으로 성능을 최적화함. 

| **문제**    | **문제 분석**                                                                                                                                               | **문제 해결**                                                                                                 |
|-----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------|
| **서버 과부하** | - 매칭 서비스 트래픽 폭증 시 CPU 94.8% 사용으로 서버 과부하 발생<br>- 트래픽 증가로 서비스 셧다운 우려                                                          | - 매칭 전용 서버를 분리하여 부하를 분산<br>- CPU 사용량 46.83% 감소로 성능 안정화                                                                 |
| **저조한 처리량** | - 동기 방식으로 매칭 서버 API 호출<br>- 응답 대기 시간이 길어 초당 처리량(54.7ops/s) 저하                                                                   | - RabbitMQ를 활용한 비동기 처리로 전환<br>- 초당 처리량 3.31배 증가 (54.7ops/s → 181ops/s)                                                        |
| **병목 현상** | - RabbitMQ의 컨슈머 처리량이 프로듀서 전송량을 따라가지 못함<br>- 큐에 최대 7000개의 메시지가 쌓여 병목 현상 발생                                                  | - RabbitMQ 클러스터를 구성하여 병렬 처리<br>- 큐 적재량이 7000개에서 3000개로 감소 (57.14% 개선)                                                  |

| **항목**               | **개선 전**        | **개선 후**       | **개선 비율**        | **전 이미지**                                                  | **후 이미지**                                                    |
|------------------------|-------------------|------------------|---------------------|------------------------------------------------------------|--------------------------------------------------------------|
| **CPU 사용량**          | 94.8%            | 50.4%            | **46.83% 감소**      | ![개선 전 CPU](/profile/assets/troubleshooting/매칭/매칭트러블1.png) | ![개선 후 CPU](/profile/assets/troubleshooting/매칭/매칭트러블1-2.png) |
| **초당 처리량 (ops/s)** | 54.7ops/s        | 181ops/s         | **3.31배 증가**       | ![개선 전 처리량](/profile/assets/troubleshooting/매칭/매칭트러블2.png) | ![개선 후 처리량](/profile/assets/troubleshooting/매칭/매칭트러블2-2.png) |
| **RabbitMQ 큐 적재량**  | 최대 7000개       | 최대 3000개       | **57.14% 감소**      | ![개선 전 큐](/profile/assets/troubleshooting/매칭/매칭트러블3.png)   | ![개선 후 큐](/profile/assets/troubleshooting/매칭/매칭트러블3-3.png)   |

🔎 [상세 내용 기술블로그 보러가기](https://velog.io/@kim_dg/%EB%A7%A4%EC%B9%AD-%EC%84%9C%EB%B9%84%EC%8A%A4-%EA%B3%A0%EB%8F%84%ED%99%94-%ED%8A%B8%EB%9F%AC%EB%B8%94-%EC%8A%88%ED%8C%85)


<br>

## 2️⃣ 1대1 채팅 서비스 비동기 처리 방식을 적용한 처리량 개선
- [x] 동시 메시지 전송으로 발생한 처리 속도 저하와 블로킹 문제를 해결하기 위해 비동기 처리와 백프레셔를 도입.
- [x] 이를 통해 처리 속도는 22.4% 개선, I/O 효율성은 17% 증가, 스레드 대기 상태는 42.86% 감소하여 시스템 안정성과 사용자 경험이 크게 향상됨.

| **구분**   | **내용**                                                                                                                                               |
|------------|-------------------------------------------------------------------------------------------------------------------------------------------------------|
| **문제 발생** | - **메시지 처리 병목 현상**: 동시 메시지 전송으로 처리 속도 저하 및 블로킹 발생.<br> - **백프레셔 미적용**: 과도한 요청으로 성능 저하 및 응답 지연 발생. |
| **문제 분석** | - **JMeter 부하 테스트**: 동시 100명의 사용자, 10초간 메시지 전송 테스트.<br> - **Prometheus/Grafana**: Duration, GC, I/O, 스레드 상태 및 CPU 모니터링. |
| **문제 해결** | - **비동기 처리 적용**: CompletableFuture와 메시지 큐로 동기 작업을 비동기로 변환.<br> - **백프레셔 도입**: 큐 오버플로 방지로 자원 낭비 최소화.<br> - **I/O 최적화**: 메인 스레드 부하 감소와 작업 처리 속도 향상. |

<details>
  <summary><span style="font-size:1.2em"><strong> 성능 개선 결과</strong></span></summary>

| 항목                     | 비동기 처리 전 | 비동기 처리 후 | 개선율          | 차트 이미지                                                     |
|--------------------------|----------------|----------------|----------------|------------------------------------------------------------|
| **Duration**             | 704 µs         | 575 µs         | **22.4% 감소** | ![채팅1](/profile/assets/troubleshooting/채팅/채팅트러블슈팅1.png)    |
| **I/O 처리량**           | 13.5 ops/s     | 11.1 ops/s     | **17% 감소**   | ![채팅2](/profile/assets/troubleshooting/채팅/채팅트러블슈팅2.png)    |
| **Timed-Waiting 스레드** | 42             | 24             | **42.86% 감소** | ![채팅3](/profile/assets/troubleshooting/채팅/채팅트러블슈팅3(1).png) |
| **Runnable 스레드**      | 63             | 68             | **7.94% 증가**  | ![채팅3](/profile/assets/troubleshooting/채팅/채팅트러블슈팅4(1).png) |

</details>

🔎  [상세 내용 기술블로그 보러가기](https://minjooig.tistory.com/168)

<br>

## 3️⃣  이벤트 참가 관리의 동시성제어 문제 해결
- [x] 이벤트 참가 시스템에서 다수의 사용자가 동시에 요청 시 데이터 무결성 문제와 성능 저하가 발생
- [x] 제한된 참가자 수를 초과하거나 처리 속도가 느려지는 문제가 확인

| **구분**       | **내용**                                                                                                                      |
|----------------|-----------------------------------------------------------------------------------------------------------------------------|
| **문제 발생**  | - 요청 처리 순서가 보장되지 않아 참가자 수가 초과 저장되거나 데이터 무결성이 깨짐.<br> - 락 점유 시간이 길어지며, 시스템의 병렬 처리가 제한됨. <br> - 충돌이 발생할 때마다 재시도가 이루어져 성능이 저하됨 |
| **문제 분석**  | - **테스트 환경**: 10초 동안 500명의 동시 사용자가 요청을 전송.<br> - **JMeter 부하 테스트**로 처리 속도 및 안정성 분석.<br>                                     |
| **문제 해결**  | - **Redis 기반 분산 락 도입**: 고유 키를 활용해 요청 충돌 방지.<br>- 성능 개선: 평균 응답 시간 28ms로 유지, 에러율 0%.                                          |
| **결과 이미지** | ![채팅3](/profile/assets/troubleshooting/동시성/동시성비관락2.png) ![채팅3](/profile/assets/troubleshooting/동시성/동시성분산락1.png)             |


<details>
  <summary><span><strong> 성능 개선 결과</strong></span></summary>

| **테스트 방식**       | **테스트 환경**                | **에러율 (%)** | **평균 응답 시간 (ms)** | **최대 응답 시간 (ms)** | **결과 요약**                                                                                   | **결과 이미지**                                              |
|-----------------------|-------------------------------|----------------|--------------------------|--------------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------|
| **트랜잭션 기반 제어** | 10초 동안 500명 동시 요청      | 48.2%          | 743ms                   | 2220ms                  | 데이터 무결성 보장 실패, 처리 속도 저하.<br> 동시성 환경에서 많은 요청 실패 발생.                                                     | ![동시성](/profile/assets/troubleshooting/동시성/동시성트렌젝션.png) |
| **낙관적 락**         | 10초 동안 500명 동시 요청      | 37.6%          | 42ms                    | 351ms                   | 충돌 발생 시 재시도로 인해 성능 저하.<br> 낮은 동시성에서는 안정적이나 고부하 환경에서는 성능 한계.                                   | ![동시성](/profile/assets/troubleshooting/동시성/동시성낙관락.png)  |
| **비관적 락**         | 10초 동안 500명 동시 요청      | 0%             | 942ms                   | 2110ms                  | 데이터 일관성 보장.<br> 처리 속도가 느려 고부하 환경에서는 비효율적.                                                                 | ![동시성](/profile/assets/troubleshooting/동시성/동시성비관락.png)  |
| **Redis 분산 락**     | 10초 동안 500명 동시 요청      | 0%             | 28ms                    | 145ms                   | 데이터 무결성과 성능 모두 우수.<br> 높은 동시 요청 환경에서도 안정적이며 최적의 성능 제공.                                           | ![동시성](/profile/assets/troubleshooting/동시성/동시성분산.png)   |


</details>

🔎  [상세 내용 기술블로그 보러가기](https://fargoewave.tistory.com/206)

<br>

## 4️⃣ API 호출 성능 최적화 및 캐싱 동시성 문제 해결

- [x] API 호출에서 동일한 요청이 반복적으로 외부 API를 호출함에 따라 성능 저하와 불필요한 비용이 발생
- [x] 캐싱을 도입했음에도 초기 동시 요청에서는 중복 호출 문제가 여전히 존재함
- [x] 성능 최적화와 동시성 제어 방안 마련이 필요했음

| **구분**     | **상세 내용**                                                                                                                                                         |
|--------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **문제 발생** | - 동일 요청에 대해 외부 API가 매번 호출되어 성능 저하 및 추가 비용 발생. <br> - 캐싱 적용 후에도 초당 10명 동시 접속 시 API 중복 호출 문제 발생.                           |
| **문제 분석** | - **캐시 미적용 시**: 모든 요청이 외부 API를 호출하여 100건 요청당 100회의 API 호출 발생. <br> - **캐싱 적용 후**: 초기 동시 요청이 캐싱 전에 API를 중복 호출함.         |
| **문제 해결** | 1. **Redis 캐싱 도입**: <br> - 데이터 조회 시 캐시 확인 후 API 호출 여부 결정. <br> - 데이터 저장 기간 설정: 1년(변경 없음) / 1주일(변경 가능성 있음).                  |
|              | 2. **Redis 분산 락 도입**: <br> - `setIfAbsent`로 락 설정, 락 설정된 요청만 API 호출. <br> - 락이 설정되지 않은 요청은 캐시된 데이터 반환. <br> - 락 유효 기간 5초 설정. |
| **결과**     | - 캐시만 적용 시 **초기 동시 요청 일부 중복 호출** 발생. <br> - 분산 락 적용 후 **모든 중복 호출 방지**, API 호출 횟수 및 성능 최적화 확인.                              |

<details>
  <summary><span><strong> 성능 개선 결과</strong></span></summary>

| **구분**             | **API 호출 횟수** | **동시성 문제**          | **응답 속도**          | **결과 이미지**                                                    |
|----------------------|--------------------|--------------------------|------------------------|---------------------------------------------------------------|
| **Before (캐싱x)**   | 100건 요청 → 100회 호출 | **발생**: 모든 요청 중복 호출 | 평균 응답 시간: 지연 발생  | ![채팅3](/profile/assets/troubleshooting/api/api트러블1.png)       |
| **After (캐싱o, 동시성x)**    | 초기 10건 요청만 호출   | **부분 해결**: 초기 동시 요청 중복 호출 | 초기 요청에서 지연 발생   | ![After 캐싱o](/profile/assets/troubleshooting/api/api트러블2.png) |
| **After (캐싱o, 분산 락o)** | 100건 요청 → 1회 호출   | **해결**: 모든 중복 호출 방지 | 응답 속도: 안정적 유지   | ![After 캐싱o](/profile/assets/troubleshooting/api/api트러블4.png) |

</details>

🔎 [상세 내용 기술블로그 보러가기](https://gorogorokko.tistory.com/47)

<br>



## 5️⃣ 검색 서비스 성능 최적화 및 데이터 처리 문제 해결

- [x] 기존 검색 서비스에서 대량 데이터 처리 시 응답 시간이 길고, 처리량이 낮아 사용자 경험에 부정적 영향을 미침
- [x] 요청 간 응답 시간 변동성이 커 안정성이 부족한 문제가 확인됨

| 구분             | 내용                                                                                         |
|------------------|---------------------------------------------------------------------------------------------|
| 문제 발생        | - 기존 검색 쿼리에서 응답 시간이 길어짐 (평균 12,827ms, 최대 20,636ms). <br> - 처리량이 낮고(141ops/s), 요청 응답 시간의 편차가 큼.                                         |
| 문제 분석        | - 인덱스 미적용: 데이터베이스 풀 스캔으로 성능 저하. <br> - 분석 부족: 대량 데이터에 대한 효율적인 검색 전략 부재.                                       |
| 문제 해결        | 1. 인덱싱 적용: <br> - 주요 검색 필드에 적절한 인덱스를 생성하여 쿼리 성능 개선. <br> - 인덱스를 통해 응답 시간이 평균 2,956ms로 감소. <br> 2. Elasticsearch 도입: <br> - Elasticsearch와 Nori 형태소 분석기를 사용하여 복합 검색 성능 최적화. <br> - 응답 시간이 평균 517ms로 감소하고, 처리량이 증가. <br> 3. 시각화 및 모니터링: <br> - Grafana를 통해 Throughput 및 응답 시간 시각화. <br> - 분석 결과를 기반으로 성능 병목을 제거.                                                      |

<details>
  <summary><span><strong> 성능 개선 결과</strong></span></summary>

1. **인덱싱 적용 후**
   - 기존 데이터베이스에서 주요 검색 필드에 적절한 인덱스를 생성하여 성능을 측정.
   
   ![After 캐싱o](/profile/assets/troubleshooting/인덱싱/인덱싱트러블1.png)


| 항목            | 적용 전           | 적용 후           | 개선 비율         |
|------------------|------------------|------------------|-------------------|
| 평균 응답 시간   | 12,827ms         | 2,956ms          | 76.96% 감소       |
| 중앙값 응답 시간 | 13,867ms         | 3,097ms          | 77.66% 감소       |
| 최대 응답 시간   | 20,636ms         | 5,589ms          | 72.91% 감소       |
| Throughput       | 48.3ops/s        | 141ops/s         | 191.72% 증가      |


2. **Elasticsearch 도입 후**
   - Elasticsearch와 Nori 형태소 분석기를 활용해 복합 검색 성능을 최적화한 결과를 측정.

   ![After 캐싱o](/profile/assets/troubleshooting/인덱싱/인덱싱트러블1-2.png)

| 항목            | 인덱싱 적용 후    | Elasticsearch 적용 후 | 개선 비율         |
|------------------|------------------|-----------------------|-------------------|
| 평균 응답 시간   | 2,956ms          | 517ms                | 82.52% 감소       |
| 중앙값 응답 시간 | 3,097ms          | 534ms                | 82.75% 감소       |
| 최대 응답 시간   | 5,589ms          | 2,944ms              | 47.34% 감소       |
| Throughput       | 141ops/s         | 10,867ops/s          | 7,606.38% 증가    |


</details>

🔎 [상세 내용 기술블로그 보러가기](https://velog.io/@celine_jang/%EC%9D%B8%EB%8D%B1%EC%8B%B1-%EC%A0%81%EC%9A%A9-%ED%9B%84-%EC%97%98%EB%9D%BC%EC%8A%A4%ED%8B%B1-%EC%A0%81%EC%9A%A9-%ED%9B%847)

<br>

# 💪 TEAM 같이의 가치

## 📌 Ground Rule
```
- 외출시 슬랙에 사유 공유
- 슬랙 수시로 확인하기
- 의견 적극적으로 내기  
- 절대 화내지 않기 !
- 절대 짜증내지 않기 !
- 절대 비난하지 않기 !
- 반대 의견 제기시 근거와 함께 부드럽게 이야기하기
- 자율적으로 최대한 찾아본 후 팀원들에게 질문하기
- 질문에 짜증 금지, 겁내지 말고 자유롭게 질문 하기
```

<details>
<summary><span style="font-size:1.4em"><strong> 💻 Code convention </strong></span></summary>

<details>
<summary><span style="font-size:1.1em"><strong> 네이밍 컨벤션 </strong></span></summary>

| **항목**         | **컨벤션**                   | **예시**               |
|------------------|-----------------------------|------------------------|
| **클래스명**      | 파스칼 케이스               | `IAmGoodPerson`        |
| **변수명**        | 카멜 케이스                 | `iAmGoodPerson`        |
| **상수명**        | 어퍼 케이스                 | `I_AM_GOOD_PERSON`     |
| **DB 테이블**     | 스네이크 케이스             | `i_am_good_person`     |
| **DB 컬럼명**     | 스네이크 케이스             | `i_am_good_person`     |

</details>

<details>
<summary><span style="font-size:1.1em"><strong> 주석 </strong></span></summary>

```
// <- 한칸 공백
System.out.println(”Hello World!!”)

/*
* @param userId
*......
*
*/ <- 컨트롤러 클래서에만 사용

// <- 나머지는 해당 주석으로 정리할 것!
```

</details>

<details>
<summary><span style="font-size:1.1em"><strong> 포멧팅 </strong></span></summary>

-[x] 불필요한 import문은 삭제하고 커밋
    - MAC : Ctrl + Option + O
    - Window : Ctrl + Alt + O
-[x] 코드 한 번 포맷팅하고 커밋 (중괄호 등의 형식을 맞추기 위해서!)
    - MAC : Command + option + L
    - MAC : Command + option + L
    - Window : Alt + Control + L
-[x] 생성자 주입은 Lombok으로 (@Required~ @No, @All ~)

</details>

<details>
<summary><span style="font-size:1.1em"><strong> API 명세서 </strong></span></summary>

- /api/v1 ~
- 계층구조 다 작성하기 ex) /groups/events/comments/{commentId}
```
Response 성공시(조회, 생성, 수정시 데이터 반환 / 삭제시 데이터 반환 X)
{
  "status": 200,
  "message" : "정상처리 되었습니다.",
  "data": {
    ...
  }
}
```
```
Response 실패시

{
  "status": 400,
  "message" : "",
  "data" : null
}
}
```



</details>

</details>

<details>
<summary><span style="font-size:1.4em"><strong> 🗒️ 깃허브 규칙 </strong></span></summary>

```
- default 브랜치는 dev. 최종 제출 전날 dev에서 main으로 PR.
- 브랜치 명 : `종류/도메인-기능` `feature/user-create`
- 커밋 메시지 : `깃헙커밋규칙: 내용` `feat: User 생성 기능 추가`
- PR 규칙 : 혼자서 Merge 진행 하시고 충돌 발생시 슬랙에 말씀 해 주세요
```

| **작업 타입**   | **작업 내용**                                    |
|-----------------|-------------------------------------------------|
| ✨ **update**   | 해당 파일에 새로운 기능이 생김                     |
| 🎉 **add**      | 없던 파일을 생성함, 초기 세팅                     |
| 🐛 **bugfix**   | 버그 수정                                       |
| ♻️ **refactor** | 코드 리팩토링                                    |
| 🩹 **fix**      | 코드 수정                                       |
| 🚚 **move**     | 파일 옮김/정리                                   |
| 🔥 **del**      | 기능/파일을 삭제                                 |
| 🍻 **test**     | 테스트 코드를 작성                                |
| 💄 **style**    | CSS 관련 작업                                   |
| 🙈 **gitfix**   | `.gitignore` 수정                                |
| 🔨 **script**   | `package.json` 변경 (npm 설치 등)                 |
| 🧹 **chore**    | 그 외 자잘한 수정                                 |
| 📜 **docs**     | 문서 수정                                       |


</details>

## **👪 Detail Role**

<table>
  <thead>
    <tr>
      <th align="center">팀원</th>
      <th align="center">포지션</th>
      <th align="center">담당</th>
      <th align="center">GitHub</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td align="center">
        <img src="/profile/assets/profile/profile5.png" width="100px;" alt="Profile Image"/><br/>
        <sub><b>김도균</b></sub>
      <td align="center">👑 Leader</td>
      <td align="left">
        - 담당 도메인 : User, Auth, Matching<br/>
        - 유저 관리 기능 개발 (회원가입, 로그인, 탈퇴 등)<br/>
        - Spring Security 기반 인증/인가 시스템 구축<br/>
        - Kafka / RabbitMQ 활용 서버 간 메시지 큐 기반 비동기 환경 구현<br/>
        - 필터링을 통한 1:1 유저 매칭 서비스 개발
      </td>
      <td align="center"><a href="https://github.com/gyun97">GitHub</a></td>
    </tr>
    <tr>
      <td align="center">
        <img src="/profile/assets/profile/profile6.jpg" width="100px;" alt="Profile Image"/><br/>
        <sub><b>나유화</b></sub>
      <td align="center">👑 Sub-Leader</td>
      <td align="left">
        - 담당 도메인 : Event, Payment, Ad<br/>
        - 이벤트 및 참가 관리 기능 개발<br/>
        - OAuth 2.0 소셜 로그인 (Kakao, Naver) 연동<br/>
        - Redisson 및 Redis를 활용한 동시성 제어<br/>
        - Redis master-slave 구조 및 sentinel 구축<br/>
        - Toss Payments 결제 시스템 구축<br/>
        - Spring Scheduler 날짜별 광고 생성 및 상태별 광고 노출 구현
      </td>
      <td align="center"><a href="https://github.com/fargoe">GitHub</a></td>
    </tr>
    <tr>
      <td align="center">
        <img src="/profile/assets/profile/profile1.jpeg" width="100px;" alt="Profile Image"/><br/>
        <sub><b>김민주</b></sub>
      </td>
      <td align="center">💚 Member</td>
      <td align="left">
        - 담당 도메인 : Attachment, Chat<br/>
        - S3 기반 유저 및 소모임 이미지 등록 관리 개발<br/>
        - Gathering 및 Event 목록 Redis 캐싱으로 조회 성능 최적화<br/>
        - Rate Rate Limiter 어뷰징 방지 및 부하 관리<br/>
        - RabbitMQ와 WebSocket-STOMP을 통한 실시간 채팅 구현<br>
        - CI/CD(GitHub Actions) 파이프라인 구성<br/>
        - AWS Infra(EC2, S3, RDS, ECR) 구성 및 배포
      </td>
      <td align="center"><a href="https://github.com/wanduek">GitHub</a></td>
    </tr>
    <tr>
      <td align="center">
        <img src="/profile/assets/profile/profile2.JPG" width="100px;" alt="Profile Image"/><br/>
        <sub><b>장민경</b></sub><td align="center">💚 Member</td>
      <td align="left">
        - 담당 도메인 : Comment, Serch<br/>
        - 댓글 생성, 수정, 삭제 기능 개발<br>
        - 소모임 제목과 지역 필드에 대한 단일 및 복합 인덱스 적용<br/>
        - ElasticSearch 한국어 데이터 색인 및 고급 검색, 연관 키워드 분석 제공
      </td>
      <td align="center"><a href="https://github.com/Minkyeongweb">GitHub</a></td>
    </tr>
    <tr>
      <td align="center">
        <img src="/profile/assets/profile/profile4.jpg" width="100px;" alt="Profile Image"/><br/>
        <sub><b>이시우</b></sub><td align="center">💚 Member</td>
      <td align="left">
        - 담당 도메인: Member, Notification <br/>
        - 멤버 가입, 조회, 삭제, 거절 및 단체 알림 기능 개발<br>
        - RabbitMQ 및 WebSocket을 활용한 API별 알림 기능 구현<br>
        - ELK 스택(Logstash, Kibana, Metricbeat)을 이용한 로그 수집 및 시각화<br>
      </td>
      <td align="center"><a href="https://github.com/lsy8467">GitHub</a></td>
    </tr>
    <tr>
      <td align="center">
        <img src="/profile/assets/profile/profile3.png" width="100px;" alt="Profile Image"/><br/>
        <sub><b>박용준</b></sub><td align="center">💚 Member</td>
      <td align="left">
        - 담당 도메인 : Gathering,locations<br/>
        - 소모임 생성, 조회, 수정, 삭제 기능 개발<br/>
        - Geocoding API 및 Place API 연동을 통한 위치 기반 추천 서비스 제공<br/>
        - Redis 캐싱을 활용한 API 호출 최적화<br/>
        - 분산 락 적용으로 동시성 문제 해결<br/>
      </td>
      <td align="center"><a href="https://github.com/Gorokgorokgo">GitHub</a></td>
    </tr>
  </tbody>
</table>

<br>


## 🥇 개발 프로세스 회고

### 🦦 GOOD: 참 잘했수달
```
1. 팀 내 커뮤니케이션 원활  
   - 프로젝트 진행 중 팀원 간의 소통이 원활하게 이루어졌음.
   - 의견을 자유롭게 공유하며 갈등 없이 협업을 진행.

2. 적극적인 시도와 도전  
   - 빠듯한 일정 속에서도 다양한 기술과 방법론을 시도하려는 자세가 돋보였음.
   - 새로운 접근법을 적극적으로 탐구하며 개발에 적용.

3. 수용적인 협업 태도  
   - 팀원 간 실력 차이에도 불구하고 서로의 능력을 인정하고 수용.
   - 이러한 태도가 협업 과정의 원활함과 효율성을 높였음.

4. 낯선 기술 스택 학습 노력  
   - 처음 접하는 어려운 기술 스택들을 익히기 위해 꾸준히 학습.
   - 학습 과정에서의 열정이 프로젝트 완성도를 높이는 데 기여.
```
### 😰 BAD: 조금 아쉬웠수달
```
1. 코드 리뷰 부족 
   - 제한된 일정으로 인해 팀원 간 코드 리뷰가 활발하게 이루어지지 못함.
   - 코드 품질 향상과 팀 내 코드 스타일 통일이 미흡했던 점이 아쉬웠음.

2. 기술 학습 순서에 대한 아쉬움
   - 기술을 도입하고 학습하는 순서가 비효율적이었음.
   - 쉬운 기술부터 익혔다면 개발 시간 단축 및 일정 관리가 더 수월했을 것으로 판단.
   
3. 추가 기능 구현 불발 
   - 시간 부족으로 인해 계획했던 추가 기능을 구현하지 못한 점이 아쉬웠음.
   - 완성도를 높일 수 있는 기회를 놓침.

4. 기술적 이해 부족
   - 일부 기술에 대한 이해 부족으로 인해 초반 일정이 지연.
   - 개발 효율성을 높이기 위해 학습에 더 많은 시간을 할애했어야 했음.

```

### 🏃‍♂️‍➡️ 향후 계획: 지속적인 개발 및 기술적 도전
#### **1. 서비스 추가 확장 및 MSA 구조 적용**
- **목표**: 기존 모놀리틱 구조에서 MSA로 전환하여 서비스 확장성 및 유연성 강화.
- **세부 계획**:
    - 각 도메인별로 독립적인 마이크로서비스로 분리하여 운영.
    - Redis, RabbitMQ, Kafka와 같은 분산 환경에 적합한 기술 스택을 활용.
    - 서비스 간 데이터 일관성을 보장하기 위한 Saga 패턴 또는 이벤트 소싱 도입 검토.

#### **2. EC2 → Fargate ECS 전환**
- **목표**: MSA 구조로의 전환과 함께 컨테이너 관리 효율성을 극대화.
- **세부 계획**:
    - EC2 기반 애플리케이션을 **AWS Fargate ECS**로 이전.
    - **자동 확장** 및 **클러스터 관리 간소화**를 통해 운영 효율성 강화.
    - CI/CD 파이프라인과의 연계로 컨테이너 배포 속도 및 안정성 확보.

    
#### **3. 테스트 커버리지 70% 달성**
- **목표**: 테스트 코드 작성을 통해 서비스 신뢰도와 유지 보수성을 향상.
- **세부 계획**:
    - JUnit5 및 Mockito를 활용하여 유닛 테스트와 통합 테스트 작성.
    - 테스트 커버리지 도구(JaCoCo) 도입 및 CI 과정에 통합.
    - 주요 기능 및 경계 조건에 대한 테스트를 강화하여 오류 발생 가능성 최소화.


#### **4. 향후 기대 효과**
- MSA 구조와 ECS 전환을 통해 고부하 상황에서도 안정적인 서비스 운영 가능.
- 컨테이너 관리 자동화를 통해 인프라 운영 비용 절감 및 배포 속도 향상.
- 테스트 커버리지 확대를 통해 코드 품질 및 안정성 확보, 예측 가능한 서비스 제공.  
