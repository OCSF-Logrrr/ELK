# 🔧 Simple ELK Stack with Docker Compose

## 📌 구성 요소
- **Elasticsearch** 8.6.2 (3-node cluster)
- **Kibana** 8.6.2
- **Logstash** 8.6.2
- **도커 컴포즈 기반의 간편한 배포**

---

## 📁 디렉토리 구조
```
ELK/
├── .env
├── bulk.template
├── docker-compose.yml
└── logstash/
    └── pipeline/
        └── logstash.conf
```

---

## 🚀 시작하기

### 1. 환경 변수 설정
`.env` 파일에 다음 항목을 설정하세요:
```env
ELASTIC_USERNAME=elastic

ELASTIC_PASSWORD=p@ssw0rd1234

KIBANA_PASSWORD=p@ssw0rd1234

CLUSTER_NAME=docker-cluster

STACK_VERSION=8.6.2

LICENSE=basic

ES_PORT=9200

KIBANA_PORT=5601

MEM_LIMIT=1073741824
```

### 2. 실행
```bash
docker compose up -d
```

첫 실행 시 인증서가 자동 생성되며, `setup` 컨테이너가 종료된 뒤 클러스터가 구성됩니다.


![image](https://github.com/user-attachments/assets/9a316fba-462e-4fb2-9eba-2b6f8a8ee5cc)


---

## 📌 주요 포인트

- 인증서는 `setup` 컨테이너에서 자동 생성되며, Docker volume(`certs`)에 보관됩니다.
- `Logstash`는 `https://es01:9200` 주소로 Elasticsearch에 안전하게 연결됩니다.
- Kibana도 SSL을 통해 Elasticsearch와 연결됩니다.
