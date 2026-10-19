# Flow Music Automation - Hướng dẫn tích hợp Webhook API

> Dành cho: lập trình viên, người tích hợp, và AI agent.
> Mục tiêu: đủ mọi thứ để điều khiển việc tạo nhạc qua Webhook REST API cục bộ -
> endpoint, xác thực, schema request/response, tham số, lỗi, và luồng bất đồng bộ
> (gửi → hỏi → tải).

Tài liệu này mô tả API ở tab **Webhook API** của app Flow Music Automation. Nó cho
công cụ ngoài (n8n, Make.com, script riêng, AI agent, Story Machine) tạo nhạc qua
một REST API đơn giản.

---

## 1. Khái niệm chính

- **Địa chỉ bind (mặc định chỉ máy này).** API chạy ở `http://<host>:<port>`. Mặc
  định `host` là `127.0.0.1` (chỉ máy này). Ở ô **IP** trong tab Webhook có thể gõ
  `0.0.0.0` hoặc IP LAN của máy để máy khác cùng mạng gọi được (nhớ mở cổng ở tường
  lửa). Muốn truy cập qua Internet thì cần tunnel / reverse proxy riêng.
- **Cổng mặc định:** `8799` (đổi được trong tab Webhook).
- **Phải bật máy chủ.** Mở app → tab **Webhook API** → khối **Điều khiển Server** →
  **Khởi động**. Khi đang chạy, ô IP / Cổng bị khoá. Tích **Tự động khởi động server
  khi mở app** để lần nào mở app server cũng tự chạy. Khối **Khóa API** hiện khoá
  (**Tạo** đổi khoá mới ngay, khoá cũ hết tác dụng; **Sao chép** chép khoá).
- **Lưu trữ kết quả.** Kết quả webhook lưu ở thư mục riêng (khối **Lưu trữ kết
  quả**): `<thư mục lưu>/music`, mặc định `Music/Flow Music Automation/webhook/music`.
  Khối này hiện dung lượng, có thể tự xoá file cũ hơn N ngày (kiểm lúc mở app và mỗi
  6 giờ) và có nút **Dọn dẹp**. Dọn dẹp chỉ xoá trong thư mục con `music`, bị từ chối
  khi còn task webhook đang chạy; link của file đã xoá trả 404.
- **Cần đăng nhập Flow Music Automation** thì request tạo nhạc mới chạy.
  `GET /api/health` luôn trả lời; `POST /api/music/generate` trả `403
  LICENSE_REQUIRED` khi chưa đăng nhập / hết hạn.
- **Dùng chính các tài khoản Flow Music đã đăng nhập trong app**, cùng số luồng
  ("Tổng luồng" / "Luồng/tài khoản") như tab Tạo nhạc. Không có tài khoản nào bật
  thì task lỗi `NO_ACCOUNT`.
- **Bất đồng bộ:** gửi → hỏi → tải:
  1. `POST /api/music/generate` → nhận ngay `task_id` (HTTP `202`).
  2. `GET /api/status/{task_id}` lặp lại tới khi `completed` hoặc `failed`.
  3. Khi `completed`, tải file từ các URL trong `results`.
- **Một request = một prompt = một task** cho ra **2 bản** (Flow Music trả 2 bài
  mỗi prompt), nên `results` thường có 2 link nhạc.
- **Chỉ task của webhook.** Khoá API chỉ thấy và dừng được task tạo qua webhook; bài xếp
  từ tab Tạo nhạc của app không hiện ở đây. Task webhook chạy ngay cả khi người dùng đang
  tạm dừng hàng chờ của app (chỉ chạy đúng task đó).
- **Khi app khởi động lại.** Nếu tắt app lúc task webhook còn đang chờ, task đó bị huỷ khi
  mở app lại (`status: "failed"`, `error: "CANCELED"`): hãy gửi lại.

---

## 2. Xác thực

Mọi endpoint **trừ** `GET /api/health` và `GET /api/files/{filename}` cần khoá API
ở header:

```
X-API-Key: <khoá-của-bạn>
```

Thiếu/sai khoá trả `401`. Khoá hiện trong tab Webhook; bấm **Tạo** để đổi (khoá cũ
hết tác dụng ngay).

---

## 3. Danh sách endpoint

| Method | Path | Khoá | Mô tả |
|--------|------|------|-------|
| `GET`  | `/api/health` | ❌ | Kiểm tra máy chủ / uptime / số task |
| `POST` | `/api/music/generate` | ✅ | Xếp một task tạo nhạc |
| `GET`  | `/api/status/{task_id}` | ✅ | Hỏi trạng thái; trả kết quả hoặc lỗi |
| `GET`  | `/api/result/{task_id}` | ✅ | Lấy kết quả (khi đã `completed`) |
| `GET`  | `/api/tasks` | ✅ | 50 task webhook gần nhất |
| `GET`  | `/api/files/{filename}` | ❌ | Tải file nhạc / ảnh bìa |
| `POST` | `/api/stop` | ✅ | Dừng mọi task đang chờ/chạy - hoặc một: `/api/stop/{task_id}` hay body `{"task_id":"…"}` |

Dư dấu `/` ở cuối vẫn chấp nhận. CORS mở (`*`) để tool chạy trong trình duyệt gọi được.

---

## 4. Luồng bất đồng bộ

### Bước 1 - Gửi

```
POST /api/music/generate
X-API-Key: <khoá>
Content-Type: application/json
```

```json
{
  "prompt": "calm ambient piano, instrumental, slow tempo",
  "model": "Lyria 3.5",
  "ghostwriter": "standard",
  "producer": "standard",
  "task": "n8n batch"
}
```

Chỉ `prompt` là bắt buộc. `model` / `ghostwriter` / `producer` thiếu thì lấy mặc
định hiện tại của app. `task` là nhãn tuỳ chọn (hiện trong hàng chờ của app). Trả về:

```json
{ "task_id": "g1760000000001", "status": "pending", "message": "Music task queued", "poll_url": "/api/status/g1760000000001" }
```

### Bước 2 - Hỏi trạng thái

`GET /api/status/{task_id}` kèm header `X-API-Key`. `status` là một trong
`pending`, `running`, `completed`, `failed` (hoặc `expired` nếu task đã rơi khỏi
lịch sử).

Xong:

```json
{
  "task_id": "g176…", "type": "music", "status": "completed",
  "results": ["http://127.0.0.1:8799/api/files/Calm%20Piano_1a2b3c.m4a", "http://127.0.0.1:8799/api/files/Calm%20Piano_4d5e6f.m4a"],
  "titles": ["Calm Piano", "Calm Piano"], "completed_at": 1760000000123
}
```

### Bước 3 - Tải kết quả

`results` là mảng URL dạng `http://<host>:<port>/api/files/<tên-đã-urlencode>`.
Tải thẳng (không cần khoá cho `/api/files/`). Định dạng (M4A / WAV / MP3) theo
Cài đặt → định dạng tải của app.

---

## 5. Schema body - `POST /api/music/generate`

| Trường | Kiểu | Bắt buộc | Mặc định | Ghi chú |
|--------|------|----------|----------|---------|
| `prompt` | string | ✅ | - | Tối đa 5000 ký tự |
| `model` | string | ❌ | mặc định app | vd `"Lyria 3.5"`, `"Lyria 3 Pro"` |
| `ghostwriter` | string | ❌ | `"standard"` | `"standard"` hoặc `"pro"` |
| `producer` | string | ❌ | `"standard"` | `"standard"` hoặc `"tiny"` (nhanh) |
| `timeoutSec` | number | ❌ | mặc định app | Timeout mỗi giai đoạn (giây) |
| `task` | string | ❌ | `"Webhook"` | Nhãn hiện trong hàng chờ |

Giá trị `model` hợp lệ là những gì tab Tạo nhạc hiện cho tài khoản của bạn.

---

## 6. Lỗi

| HTTP | Nghĩa |
|------|-------|
| `400` | Sai request (thiếu/sai `prompt`, body không phải JSON) |
| `401` | Thiếu/sai khoá API |
| `403` | `LICENSE_REQUIRED` - đăng nhập hoặc gia hạn |
| `400` | `NO_ACCOUNT` - không có tài khoản Flow Music nào bật |
| `404` | Không thấy task hoặc file |
| `413` | Body quá lớn (> 1 MB) |

Task lỗi khi đang tạo sẽ trả `status: "failed"` kèm chuỗi `error` ở
`GET /api/status/{task_id}`.

---

## 7. Ví dụ nhanh (bash)

```bash
BASE=http://127.0.0.1:8799
KEY=KHOA_API_CUA_BAN

TID=$(curl -s -X POST $BASE/api/music/generate \
  -H "X-API-Key: $KEY" -H "Content-Type: application/json" \
  -d '{"prompt":"upbeat lofi hip hop, 80 bpm, jazzy chords"}' | jq -r .task_id)

while :; do
  S=$(curl -s $BASE/api/status/$TID -H "X-API-Key: $KEY")
  echo "$S" | jq -r .status | grep -qE 'completed|failed' && { echo "$S" | jq .; break; }
  sleep 5
done
```
