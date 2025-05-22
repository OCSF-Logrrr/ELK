## 📝 Logstash 설정 변경 이력 (logstash.conf Changelog)

이 문서는 Logstash 파이프라인 설정(`logstash.conf`)의 변경 내역을 관리합니다.  
변경된 날짜, 주요 내용, 이유를 타임라인 형식으로 정리합니다.

---

### 📅 2025-05-22

#### 1) [Beats 인덱스 분리]
- **변경 내용:**
  - '[agent][type]' 필드를 기반으로 Beats 인덱스 분기 처리
    - `filebeat → filebeat-%{+YYYY.MM.dd}`
    - `winlogbeat → winlogbeat-%{+YYYY.MM.dd}`
    - `default → logstash-%{+YYYY.MM.dd}`

- **수정 사유:**
  - 수집기 구분을 통해 인덱스 분리 및 시각화 편의성 증가
 
#### 2) [Input 설정 변경] 
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


  
