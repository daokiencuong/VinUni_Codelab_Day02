# 01-problem-scan.md — Problem Scan & Quick Problem Cards
**Dự án:** Vin Smart Future (Hệ sinh thái Vingroup — VinFast & GSM Xanh SM)  
**Khóa học:** AI in Action (AI20K) — Day 2: AI Product Scoping  
**Người thực hiện:** AI Product Engineer — Vin Smart Future Team  

---

# 🔍 Phase 1 — SCAN: Quét Cơ Hội Tối Ưu Bằng AI (4 Lenses)

Sử dụng **4 Lenses** (Lặp lại, Tốn thời gian, AI có thể làm tốt hơn, Pain từ người khác) để quét toàn diện hoạt động vận hành của VinFast và các công ty thành viên Vingroup nhằm phát hiện các điểm rò rỉ hiệu suất và cơ hội áp dụng AI:

| # | Subsidiary | Tên bài toán / Nghiệp vụ | Lens | Mô tả ngắn bài toán thực tế |
|---|------------|--------------------------|------|-----------------------------|
| 1 | **VinFast / Xanh SM** | Điều phối cứu hộ pin nguy cấp & Chỉ dẫn trạm sạc khẩn cấp | **Pain từ người khác** & **Tốn thời gian** | Khi xe điện (VF5, VF e34, VF8) báo pin nguy kịch (< 5%), tài xế hoảng loạn giữa đường. Điều phối viên tổng đài mất 15-20 phút gọi điện, tra cứu thủ công vị trí GPS, kiểm tra trạm sạc V-GREEN còn trụ trống và điều xe sạc lưu động. Nguy cơ xe chết máy giữa đường gây ách tắc giao thông và giảm sút nghiêm trọng chỉ số hài lòng khách hàng (NPS). |
| 2 | **VinFast** | Tự động hóa tiếp nhận & Phân loại lỗi xe tại Xưởng Dịch Vụ (Service Bay Intake) | **AI có thể tốt hơn** | Khách hàng mang xe đến xưởng bảo dưỡng mô tả lỗi bằng ngôn ngữ tự nhiên không chuẩn hóa (*"xe đi qua gờ giảm tốc kêu cụp cụp ở bánh trước"*, *"màn hình giải trí tự tắt đen ngòm khi bật xi nhan"*). Cố vấn dịch vụ mất 20-25 phút tra cứu sổ tay mã lỗi OBD-II / DTC và lịch sử sửa chữa để tạo phiếu yêu cầu kỹ thuật ban đầu. |
| 3 | **VinFast / V-GREEN** | Đối chiếu phiên sạc điện & Phát hiện trụ sạc offline / hỏng hóc | **Lặp lại** | Hàng tuần bộ phận vận hành phải đối soát thủ công hàng chục nghìn phiên sạc điện từ các trụ sạc nhượng quyền và trạm sạc công cộng V-GREEN, phát hiện sai lệch công suất tiêu thụ hoặc trụ sạc mất kết nối 4G/CAN bus nhưng hệ thống vẫn hiển thị là "khả dụng" trên app. |
| 4 | **VinFast** | Tự động phân loại cảnh báo lỗi ảo & Đề xuất gói cập nhật phần mềm OTA | **Lặp lại** & **AI có thể tốt hơn** | Hệ thống telematics xe điện gửi về hàng triệu bản tin log CAN bus mỗi ngày. Nhiều cảnh báo là lỗi ảo thoáng qua (phantom error như cảnh báo ắc quy 12V ảo, cảm biến áp suất lốp chập chờn khi nhiệt độ thay đổi). Kỹ sư phần mềm mất nhiều công sức sàng lọc log thủ công để quyết định phát hành bản vá OTA. |
| 5 | **VinFast** | Dự báo nhu cầu tồn kho phụ tùng thay thế tại các Showroom / Trung tâm dịch vụ toàn cầu | **Tốn thời gian** | Việc cung ứng linh kiện thay thế (cản trước, cụm đèn LED, module pin, tay nắm cửa) cho các xưởng dịch vụ tại Việt Nam, Bắc Mỹ và Châu Âu bị chậm trễ do dự báo thủ công trên Excel, khiến xe của khách hàng phải nằm xưởng chờ đợi linh kiện từ 2 đến 4 tuần. |
| 6 | **Xanh SM (GSM)** | Tự động phân tích lý do hủy cuốc & Phản ánh của tài xế xe điện | **Pain từ người khác** | Mỗi ngày có hàng nghìn cuốc xe bị hủy vào giờ cao điểm hoặc trời mưa lớn. Dữ liệu ghi âm cuộc gọi và ghi chú văn bản của tài xế chứa các thông tin quan trọng (khách chờ lâu vì tài xế phải sạc pin đột xuất, trạm sạc quá tải, định vị sai ngõ hẹp) nhưng chưa được trích xuất tự động để cảnh báo điều phối. |
| 7 | **Vinhomes** | Phân loại & Tự động điều hướng phiếu khiếu nại cư dân tích hợp trạm sạc hầm chung cư | **Lặp lại** | Cư dân phản ánh qua App Vinhomes Resident về tình trạng ô tô xăng đỗ chiếm chỗ trụ sạc xe điện dưới hầm chung cư, ban quản lý xử lý thủ công qua loa nội bộ mất từ 4 đến 8 giờ. |

---

# 🃏 Phase 2 — QUICK-ASSESS: 3 Quick Problem Cards

Từ danh sách quét trên, chọn ra **Top 3 bài toán tiềm năng nhất** để phân tích nhanh:

---

### 📇 QUICK PROBLEM CARD #1: Điều phối cứu hộ pin nguy cấp & Chỉ dẫn trạm sạc thông minh (VinFast / Xanh SM)

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                   │
│                                                                         │
│ Bài toán (1 câu): Xe điện VinFast/Xanh SM cạn pin nguy cấp (< 5%)       │
│ cần hệ thống tự động xác định toạ độ, khóa trạm sạc gần nhất hoặc       │
│ kích hoạt điều xe cứu hộ pin lưu động (Mobile Charger) lập tức.         │
│ Công ty thành viên: [x] VinFast   [x] Xanh SM (GSM)   [ ] Vinhomes      │
│                                                                         │
│ Ai đang đau (Actor)? Tài xế/Khách hàng (hoảng loạn, nguy cơ chết máy),  │
│                      Điều phối viên tổng đài (quá tải, thao tác chậm).   │
│                                                                         │
│ Workflow thủ công hiện tại (5 bước):                                    │
│   1. Tài xế gọi tổng đài khẩn cấp báo pin nguy kịch (< 5%)              │
│   ──> 2. Điều phối viên tra cứu toạ độ GPS xe trên bản đồ Fleet         │
│   ──> 3. Tra cứu Dashboard trạm sạc V-GREEN để tìm trụ sạc trống        │
│   ──> 4. Soạn thảo tin nhắn SMS/In-App chỉ đường gửi tài xế             │
│   ──> 5. Nếu xe quá xa trạm sạc (> 5km), gọi điện điều xe sạc cứu hộ    │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (⏱ 12 - 15 phút/lượt)       │
│   Lỗi nguy hiểm: Điều phối viên hướng dẫn tài xế đến trạm sạc cách 8km  │
│   trong khi pin xe chỉ còn 2%, dẫn đến chết máy giữa cầu/cao tốc!       │
│                                                                         │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3, 4 & 5                     │
│   (Tự động tiếp nhận dữ liệu telemetry SOC pin, vị trí GPS; kiểm tra    │
│    ranh giới an toàn; draft tin nhắn chỉ dẫn hoặc trigger điều xe cứu hộ)│
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│   - Giảm thời gian xử lý sự cố từ 15 phút ──> dưới 2.5 phút.            │
│   - 100% trường hợp pin < 5% bị chặn không được chỉ trạm xa > 5km.     │
│   - Tỉ lệ điều xe cứu hộ chính xác và kịp thời đạt >= 98%.              │
│                                                                         │
│ Quick Architecture: [x] LLM Feature with Strict Operational Boundaries  │
└─────────────────────────────────────────────────────────────────────────┘
```

---

### 📇 QUICK PROBLEM CARD #2: Tiếp nhận & Phân loại lỗi xe thông minh tại Xưởng Dịch Vụ (VinFast Service Bay Intake)

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                   │
│                                                                         │
│ Bài toán (1 câu): Tự động phân tích mô tả tiếng Việt phi cấu trúc của    │
│ chủ xe VinFast để trích xuất hệ thống hỏng hóc, gợi ý mã lỗi DTC        │
│ và tạo phiếu chẩn đoán kỹ thuật ban đầu cho Cố vấn Dịch vụ.             │
│ Công ty thành viên: [x] VinFast   [ ] Xanh SM   [ ] Khác: Showroom/Bay   │
│                                                                         │
│ Ai đang đau (Actor)? Khách hàng mang xe đến bảo dưỡng (chờ đợi lâu),     │
│                      Cố vấn dịch vụ Service Advisor (tra cứu mệt mỏi).  │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Cố vấn dịch vụ phỏng vấn khách hàng về triệu chứng lỗi             │
│   ──> 2. Ghi chép tay triệu chứng vào phiếu biên nhận thô               │
│   ──> 3. Tra cứu danh mục mã lỗi OBD-II / DTC và sơ đồ linh kiện        │
│   ──> 4. Đánh máy nhập thông tin vào phần mềm DMS/SAP nội bộ             │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 & 4 (⏱ 18 - 25 phút/xe)         │
│                                                                         │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3                        │
│   (Dùng LLM phân tích văn bản mô tả triệu chứng, trích xuất cấu trúc    │
│    JSON gồm: Bộ phận nghi ngờ, Mức độ ưu tiên, Danh sách mã DTC gợi ý)  │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│   - Rút ngắn thời gian lập phiếu tiếp nhận từ 20 phút ──> dưới 5 phút.  │
│   - Độ chính xác ánh xạ triệu chứng sang nhóm linh kiện đạt >= 92%.     │
│                                                                         │
│ Quick Architecture: [x] LLM Feature (Structured Output JSON)            │
└─────────────────────────────────────────────────────────────────────────┘
```

---

### 📇 QUICK PROBLEM CARD #3: Đối chiếu phiên sạc điện & Phát hiện bất thường trụ sạc V-GREEN

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                   │
│                                                                         │
│ Bài toán (1 câu): Đối soát tự động hóa dữ liệu phiên sạc hàng ngày      │
│ giữa đồng hồ công tơ điện lực, phần mềm trụ sạc và hóa đơn thanh toán;   │
│ phát hiện tức thì các trụ sạc offline hoặc rò rỉ điện năng.             │
│ Công ty thành viên: [x] VinFast / V-GREEN   [ ] Xanh SM                 │
│                                                                         │
│ Ai đang đau (Actor)? Chuyên viên vận hành hạ tầng V-GREEN, Kế toán.     │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Tải log các phiên sạc từ hệ thống quản lý trạm sạc CMS            │
│   ──> 2. Tải hóa đơn tiền điện từ EVN và dữ liệu ví thanh toán VinFast   │
│   ──> 3. Dùng bảng tính Excel đối chiếu VLOOKUP mã phiên và số kWh      │
│   ──> 4. Ghi nhận danh sách sai lệch và gửi email cho kỹ sư bảo trì     │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 (⏱ 3 - 4 giờ mỗi ngày)          │
│                                                                         │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3 & 4 (Rule + ML Anomaly)    │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│   - Giảm thời gian đối soát từ 4 giờ/ngày xuống dưới 15 phút.           │
│   - Phát hiện trụ sạc hỏng trong vòng 10 phút thay vì 24 giờ.           │
│                                                                         │
│ Quick Architecture: [x] Rule / State-Machine + ML Anomaly Detection     │
└─────────────────────────────────────────────────────────────────────────┘
```

---

# 🗳️ Quyết định Lựa chọn Bài toán Deep-Dive của Nhóm

* **Bài toán được lựa chọn:** **Quick Problem Card #1 — Điều phối cứu hộ pin nguy cấp & Chỉ dẫn trạm sạc thông minh (VinFast / Xanh SM Co-Pilot).**
* **Lý do lựa chọn:**
  1. **Tác động trực tiếp đến an toàn và thương hiệu:** Sự cố xe điện cạn kiệt pin giữa đường là nỗi ám ảnh lớn nhất của người dùng xe điện (Range Anxiety). Giải quyết bài toán này bảo vệ an toàn tính mạng, tránh tắc nghẽn hạ tầng giao thông và giữ vững uy tín cho VinFast và đội xe taxi Xanh SM.
  2. **Giá trị vận hành thời gian thực (Real-time Value):** Khác với bài toán đối soát tài chính hay dự báo kho (back-office), bài toán này diễn ra trực tiếp tại tiền tuyến điều vận hàng ngày với tần suất cao (~120 sự cố/ngày tại Hà Nội và TP.HCM).
  3. **Ranh giới an toàn (Operational Boundary) cực kỳ rõ ràng và cấp bách:** Ranh giới kỹ thuật được xác lập rõ ràng (Pin < 5% cấm hướng dẫn trạm > 5km; Bắt buộc có Human-in-the-loop duyệt tin trước khi gửi). Điều này cho phép nhóm xây dựng và kiểm thử bản mẫu kỹ thuật (Prompt Prototype) hoàn chỉnh với độ tin cậy cao nhất.
