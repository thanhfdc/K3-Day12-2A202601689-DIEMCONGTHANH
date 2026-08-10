# Thong Tin Deploy - Checkpoint 5

## Thong Tin Hoc Vien

| Muc | Noi dung |
|-----|----------|
| Ho va ten | Diêm Công Thành |
| Ma hoc vien / mã học viên | 2A202601689 |
| Repo | DAY12-2A202601689-DiemCongThanh |

## Service

| Muc | Noi dung |
|-----|----------|
| Public URL | Local fallback: http://localhost:8000 |
| Platform | Railway planned; local fallback used in this environment |
| Ngay deploy | 2026-08-10 |

## Bien Moi Truong Da Set Tren Cloud

Chi liet ke ten bien va nguon gia tri, khong ghi gia tri secret.

| Bien | Da set | Ghi chu |
|------|--------|---------|
| `PORT` | yes | platform tu gan; local dung 8000 |
| `AGENT_API_KEY` | yes | dat trong dashboard hoac file `.env`, khong nam trong repo |
| `REDIS_URL` | yes | Redis add-on tren Railway/Render; local fallback dung `fake://` khi thieu Docker |
| `RATE_LIMIT_PER_MINUTE` | yes | cau hinh qua bien moi truong |
| `MONTHLY_BUDGET_USD` | yes | cau hinh qua bien moi truong |
| `LOG_LEVEL` | yes | cau hinh qua bien moi truong |

## Lenh Kiem Tra

Local fallback trong moi truong nay:

```bash
curl -i http://localhost:8000/health
curl -i http://localhost:8000/ready
curl -i -X POST http://localhost:8000/ask \
  -H "Content-Type: application/json" \
  -d '{"question":"Hello"}'
```

## Ket Qua Chay That

```text
GET /health -> 200 {"status":"ok","service":"day12-agent","version":"1.0.0"}
GET /ready -> 200 {"status":"ready","redis":true}
POST /ask without X-API-Key -> 401 {"detail":"invalid or missing API key"}
```

## Anh Chup Man Hinh

Anh minh chung local fallback dat trong thu muc `screenshots/`.

## Neu Dung Phuong An Du Phong

Moi truong hien tai khong co lenh `docker` trong PATH, nen khong the chay Docker Compose truc tiep tai day. Em dung local fallback voi `REDIS_URL=fake://` de kiem tra endpoint FastAPI, va giu cau hinh Docker/Compose san sang cho may co Docker.
