# Thong Tin Deploy - Checkpoint 5

> File nay dung cho ban deploy cloud that. Sau khi deploy xong, thay dong
> Public URL bang URL HTTPS that cua service tren Railway hoac Render.
> Chi ghi TEN bien moi truong, khong ghi gia tri secret.

## Thong Tin Hoc Vien

| Muc | Noi dung |
|-----|----------|
| Ho va ten | Diem Cong Thanh |
| Ma hoc vien / mã học viên | 2A202601689 |
| Repo | DAY12-2A202601689-DiemCongThanh |

## Service

| Muc | Noi dung |
|-----|----------|
| Public URL | https://day12-agent-a5ma.onrender.com |
| Platform | Render |
| Ngay deploy | 2026-08-10 |

## Bien Moi Truong Da Set Tren Cloud

| Bien | Da set | Ghi chu |
|------|--------|---------|
| `PORT` | yes | Railway/Render tu gan, khong hardcode trong image |
| `AGENT_API_KEY` | yes | Dat trong dashboard, khong nam trong repo |
| `REDIS_URL` | yes | Lay tu Redis add-on cua platform |
| `RATE_LIMIT_PER_MINUTE` | yes | Dat qua dashboard, vi du 10 |
| `MONTHLY_BUDGET_USD` | yes | Dat qua dashboard, vi du 10.0 |
| `LOG_LEVEL` | yes | Dat qua dashboard, vi du INFO |

## Lenh Kiem Tra

Thay URL ben duoi bang Public URL that o tren:

```bash
URL=https://day12-agent-a5ma.onrender.com

# 1. Liveness - mong doi 200 {"status":"ok"}
curl -i "$URL/health"

# 2. Readiness - mong doi 200 {"status":"ready"} neu Redis cloud dung
curl -i "$URL/ready"

# 3. Khong co API key - mong doi 401
curl -i -X POST "$URL/ask" \
  -H "Content-Type: application/json" \
  -d '{"question":"Hello"}'

# 4. Co API key - mong doi 200
curl -i -X POST "$URL/ask" \
  -H "Content-Type: application/json" \
  -H "X-API-Key: $AGENT_API_KEY" \
  -H "X-User-Id: sv-test" \
  -d '{"question":"Deploy la gi?"}'

# 5. Rate limit - goi 15 lan, cac lan cuoi phai tra 429
for i in $(seq 1 15); do
  curl -s -o /dev/null -w "%{http_code} " -X POST "$URL/ask" \
    -H "Content-Type: application/json" \
    -H "X-API-Key: $AGENT_API_KEY" \
    -H "X-User-Id: sv-test" \
    -d '{"question":"test"}'
done; echo
```

## Ket Qua Chay That

Sau khi deploy, dan output that vao day. Khong dan gia tri `AGENT_API_KEY`.

```text
GET /health -> 200 {"status":"ok","service":"day12-agent","version":"1.0.0"}
GET /ready -> 200 {"status":"ready","redis":true}
POST /ask without X-API-Key -> 401 {"detail":"invalid or missing API key"}
POST /ask with X-API-Key -> 200, response co answer, user_id, history_length, cost_usd, tokens
Rate limit -> mot so request dau 200, cac request vuot nguong 429
```

## Anh Chup Man Hinh

Dat anh minh chung trong thu muc `screenshots/`:

- `screenshots/dashboard.png` - dashboard service tren Railway/Render
- `screenshots/health.png` - ket qua goi `/health`

## Ghi Chu Deploy

Neu dung Railway:

```bash
railway login
railway init
railway add --database redis
railway variables --set AGENT_API_KEY=<set-tren-terminal-khong-commit> \
                  --set RATE_LIMIT_PER_MINUTE=10 \
                  --set MONTHLY_BUDGET_USD=10.0 \
                  --set LOG_LEVEL=INFO
railway up
railway domain
```

Neu dung Render, tao Blueprint tu repo va set `AGENT_API_KEY` trong dashboard khi Render hoi. `REDIS_URL` phai tro toi Redis/Key Value add-on cua Render.
