# Phieu Phan Anh - K3 Ngay 12

Ho va ten: Diêm Công Thành 
Ma hoc vien: 2A202601689

---

### Cau 1 - Fail fast (CP1)

Neu deploy len cloud ma quen set `AGENT_API_KEY`, app dung ngay luc khoi dong se giup minh phat hien loi trong dashboard/log deploy. Neu de mac dinh `"changeme"`, service van chay cong khai va bat ky ai biet key mau do co the goi `/ask`, lam ton chi phi va kho phat hien hon.

---

### Cau 2 - Log cho may doc (CP1)

Vi du mot dong log:

```json
{"event":"ask_completed","level":"info","timestamp":"2026-08-10T02:30:00+00:00","user_id":"sv01","tokens_in":12,"tokens_out":32,"cost_usd":0.0001}
```

Voi log JSON nay minh co the loc theo `event` hoac `user_id` de xem ai dang goi API, va co the cong `cost_usd` de theo doi chi phi. `print("da tra loi xong")` khong co cau truc nen may kho dem, kho loc va kho canh bao.

---

### Cau 3 - Kich thuoc image (CP2)

| Ban | Dung luong |
|-----|-----------|
| 1 stage ban dau | chua do duoc tren may nay vi khong co Docker |
| Multi-stage | chua do duoc tren may nay vi khong co Docker |

Phan chenh lech thuong den tu compiler, cache build, file tam va cac layer chi can cho qua trinh cai thu vien. Multi-stage chi copy ket qua cai dat sang runtime nen image cuoi nho va gon hon.

---

### Cau 4 - Thu tu lenh trong Dockerfile (CP2)

Dockerfile hien tai copy `requirements.txt` va cai dependency truoc, sau do moi copy `app` va `utils`. Khi sua mot ky tu trong `app/main.py`, layer cai dependency van duoc dung lai tu cache, chi cac layer copy source va sau do phai chay lai. Neu dat `COPY . .` truoc `RUN pip install`, moi lan sua code Docker se mat cache tu som va cai lai toan bo thu vien.

---

### Cau 5 - Vi sao khong chay bang root (CP2)

Neu code Python co lo hong cho phep ghi file hoac chay lenh trong container, attacker se thuc thi lenh voi user dang chay process. Neu process la root, tac dong trong container lon hon va khi co them loi cau hinh volume/runtime thi co the anh huong toi host. Lenh `USER appuser` cat chuoi do bang cach ha quyen process xuong user thuong.

---

### Cau 6 - Cua so truot (CP3)

Neu dem theo phut dong ho voi han muc 10 request/phut, user co the gui 20 request trong 2 giay: 10 request o 10:00:59 va 10 request tiep theo o 10:01:01. Vi bo dem reset o giay 00, ca hai cum deu hop le tren giay to, nhung thuc te la burst rat manh.

---

### Cau 7 - Rate limit va cost guard (CP3)

Rate limit gioi han toc do goi request, con cost guard gioi han tong chi phi trong thang. Mot user gui it request nhung moi request rat dai co the qua rate limit nhung bi cost guard chan. Nguoc lai, mot user chua ton nhieu tien nhung spam nhieu request nho trong 1 phut se bi rate limit chan truoc.

---

### Cau 8 - /health khac /ready (CP4)

Neu gop `/health` va `/ready` thanh mot endpoint co kiem tra Redis, khi Redis mat ket noi 30 giay thi ca 3 container deu tra health 503. Orchestrator hieu nham process chet va restart ca 3 container. Restart khong sua duoc Redis, nen cum co the lap lai viec restart va lam su co nho thanh downtime lon hon.

---

### Cau 9 - Stateless (CP4)

Khi lich su luu trong Redis, `history_length` tang deu qua nhieu request cung `X-User-Id`, du request vao instance nao. Neu luu bang dict Python trong RAM, moi container co mot dict rieng nen so nay se nhay lung tung: co luc 0, co luc 2, co luc 4 tuy request roi vao container nao.

---

### Cau 10 - Deploy that (CP5)

Loi gap trong moi truong nay la khong co lenh `docker` trong PATH, nen khong the chay `docker compose up` truc tiep. Minh kiem tra bang PowerShell `docker --version` va thay command not found. Cach xu ly tam thoi la dung `REDIS_URL=fake://` de chay local fallback cho FastAPI, dong thoi van hoan thien Dockerfile va docker-compose de khi co Docker thi build/chay duoc.
