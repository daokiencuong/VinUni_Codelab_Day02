# 02-deep-dive-report.md — Báo Cáo Phân Tích Sâu Bài Toán AI (Problem Deep-Dive)
**Dự án:** Vin Smart Future — AI Dispatcher Co-Pilot (VinFast & GSM Xanh SM)  
**Khóa học:** AI in Action (AI20K) — Day 2: AI Product Scoping  
**Người thực hiện:** Nhóm Kỹ sư AI — Vin Smart Future  

---

# 🏗️ Phase 3 — DEEP-DIVE: Phân Tích Chi Tiết Bài Toán

## 3.1. Current-State Workflow Mapping (Quy trình vận hành hiện tại)

Khi xe điện VinFast (dòng VF5 Plus, VF e34, VF8 thuộc sở hữu cá nhân hoặc đội taxi Xanh SM) rơi vào trạng thái cạn kiệt pin nguy cấp (State of Charge - SOC dưới 5%), quy trình xử lý thủ công hiện tại diễn ra qua 5 bước:

```text
┌────────────────┐     ┌────────────────┐     ┌────────────────┐     ┌────────────────┐
│ Bước 1         │     │ Bước 2         │     │ Bước 3         │     │ Bước 4         │
│ Nhận cuộc gọi  │     │ Tra cứu định vị│     │ Tra cứu trạm   │     │ Soạn thảo tin  │
│ sự cố khẩn cấp │ ──> │ GPS & SOC pin  │ ──> │ sạc V-GREEN    │ ──> │ nhắn chỉ dẫn   │
│                │     │                │     │ còn trụ trống  │     │ gửi tài xế     │
│ Actor: Dispatch│     │ Actor: Dispatch│     │ Actor: Dispatch│     │ Actor: Dispatch│
│ ⏱ 2.0 phút    │     │ ⏱ 2.5 phút    │     │ ⏱ 5.5 phút 🔴  │     │ ⏱ 4.5 phút 🔴  │
│ Input: Hotline │     │ Input: Biển số │     │ Input: Toạ độ  │     │ Input: Địa chỉ │
│ Out: Ticket sơ │     │ Out: Toạ độ GPS│     │ Out: Mã trạm   │     │ Out: Tin SMS   │
└────────────────┘     └────────────────┘     └────────────────┘     └────────────────┘
                                                                              │
                                                                              ▼
                                                                       ┌────────────────┐
                                                                       │ Bước 5         │
                                                                       │ Điều xe cứu hộ │
                                                                       │ sạc pin lưu    │
                                                                       │ động (nếu cần) │
                                                                       │ Actor: Dispatch│
                                                                       │ ⏱ 2.5 phút     │
                                                                       │ Out: Dispatch  │
                                                                       └────────────────┘
🔴 = Bottlenecks (Điểm nghẽn nghiêm trọng)
🔄 Handoffs: 4 điểm chuyển giao thông tin thủ công giữa Điện thoại -> Màn hình Fleet Telematics -> CMS Trạm Sạc -> SMS Gateway -> Đội Cứu hộ Cơ động.
⏱ Tổng thời gian xử lý thủ công: 15.0 - 17.0 phút / sự cố.
```

### Các điểm rò rỉ và nguy cơ nghiêm trọng trong quy trình hiện tại:
1. **Điểm nghẽn Bước 3 & 4 (Chiếm 10 phút / 65% thời gian):** Điều phối viên phải chuyển đổi giữa 3 màn hình nghiệp vụ khác nhau: vừa nhìn bản đồ số, vừa tra cứu ứng dụng trạm sạc V-GREEN để tìm trụ sạc có loại cổng tương thích (ví dụ cổng DC CCS2 công suất 60kW/150kW/250kW còn trống), vừa gõ tay tin nhắn chỉ đường tiếng Việt gửi cho tài xế.
2. **Sai lầm chết người (Human Error & Safety Violation):** Dưới áp lực cuộc gọi khẩn cấp của tài xế đang hoảng loạn, nhiều điều phối viên đã chỉ dẫn xe chạy đến trạm sạc cách vị trí 7-8km trong khi pin xe chỉ còn 2% (xe điện ở 2% chỉ di chuyển được tối đa 3-4km trong điều kiện bật máy lạnh và đường dốc nhẹ). Hậu quả là xe chết máy giữa đường, gây nguy hiểm tính mạng và ùn tắc giao thông nghiêm trọng.

---

## 3.2. Problem Statement (6-field) — Tiêu Chuẩn Vin Smart Future

Bảng đặc tả bài toán theo khung 6 trường chuẩn mực của Vin Smart Future:

| Trường thông tin (Field) | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Điều phối viên (Dispatcher) thuộc Trung tâm Điều vận Vin Smart Future / Khối Vận hành Xanh SM và Chăm sóc khách hàng VinFast. |
| **2. Current Workflow** | Khi tài xế/chủ xe VinFast gọi hotline báo pin nguy kịch (< 5%), điều phối viên tiếp nhận cuộc gọi, mở phần mềm Fleet Tracking để tra cứu tọa độ GPS, mở tiếp Dashboard V-GREEN để tìm trụ sạc trống có cổng tương thích gần nhất, tự soạn văn bản chỉ đường gửi qua SMS/App, hoặc nhấc máy gọi sang Đội Cứu hộ Pin Di Động (Mobile Charging Van) nếu không thể tới trạm. Quy trình gồm 5 bước thủ công, mất trung bình 15.0 - 17.0 phút/lượt. |
| **3. Bottleneck** | **Bước 3 & Bước 4:** Tra cứu thủ công trụ sạc trống phù hợp với dòng xe và soạn thảo văn bản chỉ đường chi tiết bằng tiếng Việt thân thiện; độ trễ lớn trong việc ra quyết định chuyển hướng sang xe cứu hộ khi dung lượng pin đã rơi vào vùng nguy hiểm (< 5%). |
| **4. Business Impact** | Mỗi ngày ghi nhận ~120 sự cố pin nguy cấp trên toàn quốc (tập trung tại Hà Nội, TP.HCM, Đà Nẵng). Gây lãng phí ~32 giờ làm việc/ngày của đội ngũ điều phối viên. Khoảng 8% trường hợp bị hướng dẫn sai dẫn đến xe chết máy giữa đường, phát sinh chi phí xe kéo cứu hộ tốn kém (700.000 VNĐ - 1.200.000 VNĐ/lượt) và làm suy giảm nghiêm trọng chỉ số hài lòng thương hiệu VinFast (NPS giảm 12 điểm ở nhóm khách hàng gặp sự cố). |
| **5. Success Metric** | **Hiệu suất vận hành (Efficiency):** Giảm tổng thời gian xử lý sự cố từ 15 phút xuống dưới 2.5 phút/lượt (giảm 83% thời gian).<br>**Độ an toàn & Tuân thủ ranh giới (Safety & Boundary):** 100% trường hợp pin < 5% bị chặn tuyệt đối không cho gợi ý trạm sạc xa > 5km, và tự động kích hoạt đề xuất điều xe cứu hộ sạc di động trong vòng 10 giây.<br>**Độ chính xác nội dung (Quality):** Tỉ lệ đề xuất đúng loại cổng sạc và trạm khả dụng đạt >= 98%. |
| **6. Operational Boundary** | **AI ĐƯỢC PHÉP:** Truy xuất dữ liệu telemetry (SOC, GPS), tra cứu API trạng thái trạm sạc V-GREEN theo thời gian thực, tổng hợp thông tin và tự động soạn thảo tin nhắn hướng dẫn hoặc tạo lệnh điều xe cứu hộ dưới dạng DỰ THẢO (DRAFT).<br>**AI TUYỆT ĐỐI CẤM (Hard Boundary):**<br>1. CẤM tự ý gửi tin nhắn ra ngoài cho tài xế/khách hàng mà không có sự click phê duyệt của Điều phối viên (Bắt buộc Human-in-the-Loop qua tiền tố `[DRAFT_ONLY]`).<br>2. CẤM đề xuất trạm sạc cách xa quá 5km khi dung lượng pin dưới 5%; bắt buộc phải kích hoạt `dispatch_mobile_charger`.<br>3. CẤM chỉ định cổng sạc không tương thích với dòng xe (ví dụ đề xuất cổng sạc AC chậm cho xe đang cấp cứu pin gấp). |

---

## 3.3. Future-State Flow & AI Fit (Quy trình tương lai & Mức độ ứng dụng AI)

### Phân tích AI-Fit Matrix (Chọn mức độ công nghệ):
* **Không dùng Rule thuần túy:** Bởi vì thông tin từ người dùng và tài xế rất đa dạng về ngữ cảnh (mô tả vị trí theo mốc đường, tình trạng tâm lý, trạng thái xe, giao thông thực địa).
* **Không dùng Autonomous Multi-Agent:** Vì mức độ rủi ro an toàn giao thông rất cao. Một quyết định tự động gửi sai lầm từ AI có thể khiến xe chết máy trên đường cao tốc.
* **LỰA CHỌN TỐI ƯU: LLM Feature with Deterministic Boundary Guardrails (Augment human dispatcher, Human-in-the-Loop):**
  - AI đóng vai trò Co-pilot: Đọc telemetry, lọc trạm sạc, kiểm tra ranh giới an toàn, soạn thảo văn bản phản hồi chuẩn hóa kèm thẻ `[DRAFT_ONLY]` hoặc sinh JSON kích hoạt cứu hộ `{"action": "dispatch_mobile_charger"}`.
  - Con người (Điều phối viên) giữ quyền quyết định cuối cùng (1-click Approve hoặc Chỉnh sửa).

### Sơ đồ quy trình tương lai (Future-State Workflow):

```text
┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐       ┌─────────────────┐
│ Bước 1          │       │ Bước 2          │       │ Bước 3          │       │ Bước 4          │
│ Tiếp nhận tín   │       │ 🔵 AI Context   │       │ 🔵 AI Guardrail │       │ 🟢 Dispatcher   │
│ hiệu sự cố /    │ ────> │ Engine:         │ ────> │ & Draft Engine: │ ────> │ 1-Click Duyệt   │
│ Cuộc gọi từ app │       │ Auto-pull GPS,  │       │ Kiểm tra pin,   │       │ & Bắn tin nhắn  │
│                 │       │ SOC, trạm V-GRN │       │ sinh DRAFT_ONLY │       │ hoặc lệnh cứu hộ│
│ ⏱ 0.5 phút      │       │ ⏱ 0.5 phút      │       │ ⏱ 0.5 phút      │       │ ⏱ 0.5 phút      │
└─────────────────┘       └─────────────────┘       └─────────────────┘       └─────────────────┘
                                                                                       │
                                                                                       ▼
                                                                                ↩️ Fallback Mechanism:
                                                                                Nếu API LLM timeout (>5s)
                                                                                hoặc output thiếu thẻ
                                                                                [DRAFT_ONLY], hệ thống tự
                                                                                chuyển về Rule-based fallback
                                                                                và cảnh báo Dispatcher làm tay.
```

* 🔵 **AI Step:** Tự động tổng hợp dữ liệu, kiểm tra ngưỡng pin 5%, sinh đề xuất kèm ranh giới nghiêm ngặt.
* 🟢 **Human Step (HITL):** Điều phối viên chỉ cần nhìn bản nháp đã được điền sẵn thông tin, kiểm tra nhanh và bấm nút "Gửi" (giảm thời gian từ 15 phút xuống dưới 2 phút).
* ↩️ **Fallback Plan:** Cơ chế dự phòng khi API lỗi hoặc vi phạm cấu trúc an toàn.

---

# 🏁 Phase 5 — EVALUATE: Đánh Giá Khả Thi & Quyết Định Gate 0

### Bảng Đánh Giá Độ Sẵn Sàng (AI Readiness Checklist):

| Tiêu chí sẵn sàng | Đánh giá hiện trạng | Trạng thái |
|---|---|:---:|
| **1. Dữ liệu sẵn có & Chất lượng (Data Readiness)** | Hệ sinh thái VinFast & Xanh SM đã có sẵn API Telematics (GPS, SOC pin truyền về qua CAN-bus mỗi 10 giây) và API trạng thái trụ sạc V-GREEN (thông tin số trụ trống, công suất kW, trạng thái online/offline). Dữ liệu sạch, có cấu trúc và có độ trễ thấp. | ✅ **ĐẠT** |
| **2. Kiểm soát rủi ro an toàn (Risk Containment via HITL & Fallback)** | Mô hình luôn ép tiền tố `[DRAFT_ONLY]` đảm bảo không tin nhắn nào tự ý lọt ra ngoài. Bộ lọc ranh giới pin < 5% ngăn chặn 100% nguy cơ điều xe đi xa. Nếu LLM lỗi, cơ chế Rule-based Fallback kích hoạt ngay lập tức. | ✅ **ĐẠT** |
| **3. Mức độ sẵn sàng của Stakeholders (Change Management)** | Đội ngũ điều phối viên Xanh SM và CSKH VinFast đang chịu áp lực quá tải lớn trong giờ cao điểm. Họ cực kỳ hào hứng với công cụ trợ lý gợi ý tự động (Co-pilot) giúp giảm bớt việc gõ phím thủ công và tra cứu qua lại nhiều phần mềm. | ✅ **ĐẠT** |

---

### 🏆 Quyết Định Cuối Cùng của Ban Giám Đốc Vin Smart Future:

# 👉 **QUYẾT ĐỊNH: GO (Bắt đầu xây dựng Prototype)**

### Lý giải quyết định (Justification):
1. **Giá trị Kinh tế & Hiệu quả (ROI Rõ Ràng):**
   - Với ~120 sự cố/ngày, việc giảm thời gian xử lý từ 15 phút xuống 2 phút giúp giải phóng **~26 giờ lao động mỗi ngày** của đội ngũ điều vận, tương đương tiết kiệm ngân sách vận hành hơn 450 triệu VNĐ/tháng.
   - Giảm thiểu 90% số vụ xe chết máy giữa đường do cạn pin, tiết kiệm hơn 80 triệu VNĐ/tháng chi phí xe cẩu cứu hộ khẩn cấp và bảo vệ giá trị thương hiệu VinFast.
2. **Độ phức tạp Kỹ thuật ở mức vừa phải, khả thi cao:**
   - Không đòi hỏi huấn luyện lại mô hình nền tảng phức tạp. Giải pháp chỉ yêu cầu **Prompt Engineering có ranh giới nghiêm ngặt (Strict Boundary Guardrails)** kết hợp API Retrieval qua mô hình Gemini 2.5 Flash (tốc độ cao, chi phí API cực thấp: chỉ ~0.0001 USD/lần gọi).
3. **Độ an toàn kiểm soát tuyệt đối:**
   - Bằng việc kết hợp chặt chẽ giữa thẻ bắt buộc `[DRAFT_ONLY]`, ngưỡng pin khẩn cấp < 5% kích hoạt `dispatch_mobile_charger`, và cơ chế Human-in-the-Loop, rủi ro ảo giác (hallucination) của LLM bị triệt tiêu hoàn toàn trước khi tiếp cận người dùng cuối.
