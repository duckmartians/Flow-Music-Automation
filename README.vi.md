<h1 align="center">Flow Music Automation</h1>

<p align="center"><b>App desktop tạo nhạc hàng loạt trên Google Flow Music (Lyria): dán cả danh sách prompt, chia task vào hàng chờ, chạy song song trên nhiều tài khoản và tự lưu MP3 / M4A / WAV về máy.</b></p>

<p align="center">
  <a href="README.md">English</a> ·
  <b>Tiếng Việt</b>
</p>

<p align="center">
  <a href="https://github.com/duckmartians/Flow-Music-Automation/releases/latest"><img alt="Tải về cho Windows" src="https://img.shields.io/badge/T%E1%BA%A3i%20v%E1%BB%81-Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white"></a>
</p>

![Flow Music Automation](docs/screenshots/01-create.png)

---

## Cài đặt

### Bước 1 - Tải bộ cài

Mở bản phát hành mới nhất ở **[Releases](https://github.com/duckmartians/Flow-Music-Automation/releases/latest)** rồi tải tệp **`FlowMusicAutomation-<phiên bản>-win-x64.exe`** (`<phiên bản>` là số phiên bản, ví dụ `1.0.0`).

| Cần có | Ghi chú |
|---|---|
| 🪟 **Windows 10 / 11 (64-bit)** | App hiện chỉ có bản Windows |
| **Google Chrome** | App mở Chrome để bạn đăng nhập tài khoản Flow Music |
| **Tài khoản Flow Music** | Một hoặc nhiều tài khoản Google đã dùng được [flowmusic.app](https://www.flowmusic.app); nhạc tạo ra trừ credit của các tài khoản này |

### Bước 2 - Cài đặt

1. Mở tệp **`FlowMusicAutomation-<phiên bản>-win-x64.exe`** vừa tải.
2. Nếu hiện bảng **"Windows protected your PC"** (SmartScreen): bấm **More info** → **Run anyway**. *(App chưa ký chứng chỉ của Microsoft nên bị cảnh báo, không phải virus.)*
3. Chọn thư mục cài, chờ cài xong rồi mở **Flow Music Automation** từ **Start Menu**.

### Bước 3 - Đăng nhập &amp; mua gói

**Cần tài khoản G-Labs có gói Flow Music Automation còn hạn, không có bản miễn phí.** Mở app, đăng nhập bằng Google; chưa có gói thì app mở màn hình mua gói. Gói là gói riêng, tách khỏi các gói của G-Labs Studio, tính theo thời hạn và không tự gia hạn:

| Thời hạn | Chuyển khoản QR (VietQR) | PayPal / USDT |
|---|---|---|
| 1 tháng | 50.000₫ | $3 |
| 6 tháng | 250.000₫ | $15 |
| 1 năm | 500.000₫ | $30 |

![Mua gói](docs/screenshots/06-buy.png)

Gói nào cũng mở toàn bộ tính năng. Thanh toán ngay trong app bằng **QR ngân hàng (VietQR)**, **PayPal** hoặc **USDT** (USDT kích hoạt thủ công: trả xong nhắn Telegram [@duckmartians](https://t.me/duckmartians) như màn mua gói hướng dẫn). Một tài khoản dùng **một máy tại một thời điểm**: đăng nhập máy khác thì máy cũ bị đẩy ra. Ô xanh trên thanh tiêu đề hiện ngày hết hạn gói.

App **tự cập nhật**: viên phiên bản trên thanh tiêu đề kiểm tra GitHub Releases, tải bộ cài mới, **kiểm chữ ký số** rồi mới cài. Bản không có chữ ký hợp lệ sẽ không bao giờ được cài.

---

## Lần chạy đầu tiên

1. **Thêm tài khoản Flow Music** ở thẻ **Tài khoản**: bấm **Đăng nhập bằng trình duyệt**, đăng nhập Google trong cửa sổ Chrome vừa mở, app tự lấy phiên (không phải dán gì). Lặp lại cho mỗi tài khoản. Có proxy thì dán vào ô bên cạnh trước khi bấm.
2. **Nhập prompt** ở thẻ **Tạo nhạc**: mỗi dòng một prompt, hoặc **Nhập tệp** TXT / CSV / Excel. Mỗi prompt ra **2 bản nhạc**.
3. **Chọn model và cách lưu**: model, viết lời, sản xuất, số luồng, chế độ lưu, tên task, thư mục lưu.
4. Bấm **Chạy ngay**. Muốn xếp nhiều đợt thì bấm **Thêm vào hàng chờ** cho từng đợt rồi mới **Chạy ngay**: các task chạy lần lượt.
5. Bài xong hiện ngay trong danh sách: bấm ▶ để nghe thử, biểu tượng thư mục để mở file.

---

## Tính năng

- **Prompt hàng loạt**: mỗi dòng một prompt, hoặc tách bằng dòng trống để một prompt viết được nhiều dòng (ví dụ JSON). Nhập tệp TXT, CSV (tự nhận dấu `,` / `;`, cột "prompt"), Excel `.xlsx`, hoặc kéo thả tệp vào ô. Bản nháp tự lưu.
- **Hàng chờ theo task** (như G-Labs Studio): mỗi lần thêm là một task, giữ nguyên model, cài đặt và nơi lưu lúc thêm. Đổi tên, đổi thứ tự, đổi thư mục lưu, xoá task trong **Quản lý hàng chờ**.
- **Chạy song song trên nhiều tài khoản**: đặt tổng số luồng (1-20) và số luồng mỗi tài khoản (1-12). Tài khoản hết credit, bị giới hạn hay hết phiên thì app tự chuyển sang tài khoản khác.
- **Dừng không mất credit**: **Dừng** đưa bài đang tạo về hàng chờ; bấm **Chạy ngay** lại thì app nối tiếp đúng bài đã gửi, không tạo lại từ đầu. Đóng app, mở lại hàng chờ vẫn còn.
- **Model Lyria**: Lyria 3.5 / Lyria 3 Pro, viết lời Chuẩn / Pro, sản xuất Chuẩn / Nhanh.
- **Định dạng**: **M4A** (bản gốc, nhẹ), **MP3** 128-320 kbps (mã hoá ngay trên máy từ bản WAV gốc), **WAV** (không nén). Ảnh bìa lưu kèm.
- **File dễ tìm**: thư mục riêng cho mỗi task (đặt theo ngày giờ + tên task) hoặc lưu chung một thư mục; file đánh số `1-1`, `1-2`, `2-1`… theo thứ tự prompt.
- **Theo dõi kết quả**: kiểu Gọn / Chi tiết, lọc Đang chạy / Xong / Lỗi, tìm theo prompt hay tên bài, nghe thử ngay trong app, thử lại bài lỗi bằng một nút. Danh sách hàng nghìn bài vẫn mượt.
- **Báo khi xong**: thông báo Windows + nháy thanh taskbar khi cả hàng chờ chạy xong.
- **Quản lý tài khoản**: credit từng tài khoản, gói, proxy riêng, bật / tắt, đăng nhập lại tài khoản hết phiên, mở hồ sơ Chrome của tài khoản. Email được che một phần (bấm con mắt để xem).
- **6 ngôn ngữ**: English · Tiếng Việt · 简体中文 · Español · Русский · العربية (viết phải→trái); giao diện sáng / tối.

---

## Các trang

### 🎵 Tạo nhạc

![Tạo nhạc](docs/screenshots/01-create.png)

Cột trái là prompt và cài đặt, cột phải là kết quả chia theo task. Thanh trên cùng cho biết đã xong bao nhiêu prompt, bao nhiêu đang chạy, còn bao lâu.

| Ô | Ý nghĩa |
|---|---|
| **Model** | Lyria 3.5 hoặc Lyria 3 Pro |
| **Viết lời** | Chuẩn, hoặc Pro (viết kỹ hơn nhưng chậm hơn) |
| **Sản xuất** | Chuẩn, hoặc Nhanh (ra bài sớm hơn) |
| **Tổng luồng** | Số bài chạy cùng lúc trên toàn bộ tài khoản (1-20) |
| **Mỗi tài khoản** | Số bài chạy cùng lúc trên một tài khoản (1-12) |
| **Chờ tối đa** | Số phút chờ mỗi bài; quá hạn thì tự thử tài khoản khác (1-30) |
| **Chế độ lưu** | Thư mục theo task, hoặc lưu chung một thư mục |
| **Tên task** | Để trống thì đặt theo ngày giờ |

Nút **Chạy ngay**: có prompt đang nhập thì thành task mới rồi chạy; không có thì chạy tiếp hàng chờ. **Tạm dừng**: bài đang chạy chạy nốt, bài chờ giữ nguyên. **Dừng**: ngắt cả bài đang chạy và đưa về hàng chờ.

### 📋 Hàng chờ

![Hàng chờ](docs/screenshots/02-queue.png)

Mỗi dòng là một task với số prompt, cài đặt, trạng thái, tiến độ và thư mục lưu. Task chạy lần lượt từ trên xuống: dùng mũi tên để đổi thứ tự, bút chì để đổi tên, bấm vào chế độ lưu hay thư mục để đổi nơi lưu (chỉ đổi được khi task chưa bắt đầu).

### 🎧 Kết quả

![Kết quả chi tiết](docs/screenshots/03-results.png)

Kiểu **Gọn** cho danh sách dài, kiểu **Chi tiết** có thanh nghe đầy đủ và tài khoản đã tạo bài. Bài lỗi ghi rõ lý do (quá thời gian, hết credit, mạng…); **Thử lại N bài lỗi** chạy lại cả loạt. **Dọn** chỉ xoá khỏi danh sách, file nhạc trên đĩa vẫn giữ.

### 👤 Tài khoản

![Tài khoản](docs/screenshots/04-accounts.png)

Danh sách tài khoản Flow Music với credit, gói, proxy và lần làm mới gần nhất. Mở thẻ là app tự làm mới tài khoản đã lâu chưa kiểm. Tài khoản hết phiên được đánh dấu đỏ, có nút **Đăng nhập lại** (giữ nguyên proxy). Tài khoản vừa bị Flow Music giới hạn sẽ "nghỉ" vài giây rồi tự dùng lại.

Proxy nhận dạng `host:port`, `user:pass@host:port`, `host:port:user:pass` hoặc `http://` / `socks5://…`.

### ⚙️ Cài đặt

![Cài đặt](docs/screenshots/05-settings.png)

Ngôn ngữ, giao diện sáng / tối, thư mục lưu mặc định và định dạng tải:

| Chọn | File | Ghi chú |
|---|---|---|
| **M4A** | `.m4a` | Bản AAC gốc Flow Music trả về, nhẹ (~3 MB/bài) |
| **MP3** | `.mp3` | Mã hoá trên máy từ bản WAV gốc, 128 / 192 / 256 / 320 kbps; mở được ở mọi phần mềm |
| **WAV** | `.wav` | Bản không nén gốc, chất lượng cao nhất (~30 MB/bài) |

Bên dưới là thông tin bản quyền: tài khoản, mã khách hàng, ngày hết hạn.

### 🌗 Giao diện sáng &amp; 🌍 ngôn ngữ

![Giao diện sáng](docs/screenshots/07-light.png)

![Giao diện phải→trái (العربية)](docs/screenshots/08-rtl-arabic.png)

---

## Nơi lưu dữ liệu

| Thứ gì | Vị trí |
|---|---|
| Nhạc tạo ra (đổi được trong Cài đặt / trang Tạo nhạc) | `%USERPROFILE%\Music\Flow Music Automation` |
| Cài đặt, tài khoản Flow Music, hàng chờ, lịch sử | `%USERPROFILE%\.flowmusic-forge` |

Tài khoản Flow Music và hàng chờ chỉ nằm trên máy bạn. Máy chủ bản quyền chỉ dùng để kiểm tra gói, xem [Chính sách quyền riêng tư](https://duckspace.net/privacy.html).

---

## Khắc phục sự cố

**Bài báo "Chờ tài khoản"**: không còn tài khoản Flow Music nào đang bật và dùng được. Bật tài khoản ở thẻ Tài khoản, hoặc đăng nhập lại tài khoản bị đánh dấu đỏ.

**Bài báo quá thời gian**: Flow Music đang chậm. Tăng **Chờ tối đa**, hoặc giảm số luồng mỗi tài khoản. App đã tự thử tài khoản khác trước khi báo lỗi.

**Đăng nhập tài khoản không mở được Chrome**: cài [Google Chrome](https://www.google.com/chrome/) rồi thử lại.

**"Không ghi được vào thư mục lưu"**: ổ đĩa đầy hoặc thư mục không có quyền ghi. Chọn thư mục khác rồi bấm Thử lại.

**Cứ hiện màn mua gói**: gói hết hạn, hoặc tài khoản vừa đăng nhập ở máy khác. Xem ngày hết hạn và gia hạn.

**Windows chặn ở bảng "Windows protected your PC"**: bấm **More info → Run anyway**.

**Cập nhật không cài được**: tải bản mới nhất thủ công ở [Releases](https://github.com/duckmartians/Flow-Music-Automation/releases/latest).

Hỗ trợ: Telegram [@duckmartians](https://t.me/duckmartians) · [duckspace.net/support](https://duckspace.net/support/)

---

<sub>Flow Music Automation là công cụ độc lập, không liên kết hay được Google bảo trợ. "Flow Music" và "Lyria" là thương hiệu của chủ sở hữu. Bạn chịu trách nhiệm tuân thủ điều khoản của Flow Music và quyền sử dụng nhạc tạo ra.</sub>
