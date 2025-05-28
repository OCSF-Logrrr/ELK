## 📝 Logstash 설정 변경 이력 (logstash.conf Changelog)

이 문서는 Logstash 파이프라인 설정(`logstash.conf`)의 변경 내역을 관리합니다.  
변경된 날짜, 주요 내용, 이유를 타임라인 형식으로 정리합니다.

---

### 📅 2025-05-22

#### 1️⃣ [Beats 인덱스 분리]
- **변경 내용:**
  - '[agent][type]' 필드를 기반으로 Beats 인덱스 분기 처리
    - `filebeat → filebeat-%{+YYYY.MM.dd}`
    - `winlogbeat → winlogbeat-%{+YYYY.MM.dd}`
    - `default → logstash-%{+YYYY.MM.dd}`

- **수정 사유:**
  - 수집기 구분을 통해 인덱스 분리 및 시각화 편의성 증가
 
#### 2️⃣ [Input 설정 변경] 
- **변경 내용:**
  - input 플러그인의 TLS 비활성화
  - 수정 전:
  ```
  ssl => true
  ssl_certificate => "/usr/share/logstash/certs/logstash.crt"
  ssl_key => "/usr/share/logstash/certs/logstash.key"
  ```
  - 수정 후:
  ```
  ssl => false
  # ssl_certificate => ...
  # ssl_key => ...
  ```
  - 변경 사유:
    TLS 통신 설정 과정에서 인증서 구성 및 테스트 지연으로 인해
    프로젝트의 전체 로그 수집 파이프라인 진행 속도를 확보하기 위해 TLS 설정을 일시적으로 비활성화함.
    향후 안정화 후 다시 TLS 적용 예정.

---

### 📅 2025-05-28

#### 1️⃣ Input 플러그인 변경 (Beats → Kafka)

- **변경 내용**
  - 로그 수신 소스를 `beats` → `kafka`로 전환
  - Kafka input 설정:
    ```conf
    input {
      kafka {
        bootstrap_servers => "221.144.36.127:9092"
        topics => ["ocsf-logs"]
        group_id => "logstash-consumer-group"
        codec => "json"
        auto_offset_reset => "latest"
      }
    }
    ```

- **변경 전 설정 (Beats 기반)**  
    ```conf
    input {
      beats {
        port => 5044
        ssl => false
        # ssl_certificate => "/usr/share/logstash/certs/logstash.crt"
        # ssl_key => "/usr/share/logstash/certs/logstash.key"
      }
    }
    ```

- **수정 사유**
  - 로그 수집 구조가 Vector 및 Filebeat 등 Kafka 전송 기반으로 전환됨에 따라, Logstash 입력도 Kafka 중심으로 변경
  - 확장성과 유연성 확보 목적


#### 2️⃣ Elasticsearch 인덱스 분리 로직 유지

- **변경 내용**
  - `[agent][type]` 필드를 기반으로 수신 로그를 분리하여 Elasticsearch 인덱스를 설정:
    ```conf
    if [agent][type] == "filebeat" {
      elasticsearch {
        index => "filebeat-%{+YYYY.MM.dd}"
      }
    } else if [agent][type] == "winlogbeat" {
      elasticsearch {
        index => "winlogbeat-%{+YYYY.MM.dd}"
      }
    } else {
      elasticsearch {
        index => "logstash-%{+YYYY.MM.dd}"
      }
    }
    ```

- **수정 사유**
  - 로그 소스별 인덱스 분리를 통해 Kibana 시각화 및 분석 필터링의 편의성을 유지


#### 3️⃣ Input TLS 설정 비활성화 유지

- **설정 현황**
  - Kafka input은 TLS 설정을 사용하지 않으며, Beats 기반 TLS 설정은 주석 처리 상태 유지:
    ```conf
    # ssl => true
    # ssl_certificate => "/usr/share/logstash/certs/logstash.crt"
    # ssl_key => "/usr/share/logstash/certs/logstash.key"
    ```

- **사유**
  - 초기 환경 구성 속도를 높이기 위한 TLS 비활성화 유지
  - 향후 환경 안정화 후 TLS 적용 예정

