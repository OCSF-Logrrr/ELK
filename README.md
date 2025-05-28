# ELK Stack with TLS - Docker Compose Setup

이 리포지토리는 Elasticsearch 3노드 클러스터, Kibana, Logstash, 그리고 TLS 인증 구성을 포함한 ELK 스택을 Docker Compose로 배포하는 환경을 제공합니다.

## 📦 구성 요소

* **setup**: CA 및 노드별 인증서 자동 생성, 초기 사용자 비밀번호 설정
* **elasticsearch**: 3노드 클러스터 (es01, es02, es03), TLS 활성화
* **kibana**: TLS를 통해 Elasticsearch와 연동되는 Kibana 인터페이스
* **logstash**: Kafka/Beats 수신을 위한 포트 개방 및 Elasticsearch 출력

## ⚙️ 요구 사항

* Docker
* Docker Compose v2.2 이상
* `.env` 파일에서 환경변수 설정 필요 (`ELASTIC_PASSWORD`, `KIBANA_PASSWORD`, `CLUSTER_NAME`, `LICENSE`, `MEM_LIMIT`, `ES_PORT`, `KIBANA_PORT`)

## 📁 디렉토리 구조

```
.
├── docker-compose.yml
├── logstash/
│   └── pipeline/
│       └── logstash.conf
└── .env (사용자가 별도로 생성)
```

## 🔐 TLS 인증 흐름

`setup` 서비스에서 다음 인증 흐름이 자동으로 수행됩니다:

1. CA 생성 (없을 경우)
2. 각 노드별 인증서 발급 (`es01`, `es02`, `es03`, `logstash`)
3. 권한 설정
4. Elasticsearch 기동 대기
5. `kibana_system` 사용자 비밀번호 설정 완료

## 🚀 실행 방법

1. `.env` 파일 생성 및 변수 설정 (`.env.example` 참고)
2. Docker Compose 실행

```bash
docker compose up -d
```

## 🔎 상태 확인

```bash
docker ps

# 개별 서비스 로그 확인
docker logs es01
```

## 🔌 포트 구성

| 서비스      | 포트   | 설명                      |
| -------- | ---- | ----------------------- |
| es01     | 9200 | Elasticsearch API (TLS) |
| kibana   | 5601 | Kibana Web UI           |
| logstash | 5044 | Beats input port        |
| logstash | 5000 | TCP/UDP input           |
| logstash | 9600 | Logstash monitoring API |

## 🔐 인증서 경로

모든 인증서는 `certs` 볼륨 경로(`/usr/share/elasticsearch/config/certs`)에 위치합니다.
Logstash와 Kibana도 해당 인증서를 공유하여 TLS 기반 통신을 수행합니다.

## 📌 기타 참고 사항

* 초기 비밀번호는 `.env` 파일에서 설정하며, setup 서비스가 이를 이용해 Kibana 사용자 패스워드를 설정합니다.
* 인증서가 이미 존재하는 경우 재생성하지 않으며, 해당 zip 파일과 디렉토리가 자동 관리됩니다.

---

**작성일:** 2025-05-28
