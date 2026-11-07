# 01 — Problem Scan: Vin Smart Future (Vinhomes Operations Focus)

**Họ và tên Kỹ sư:** Bùi Văn Lâm  
**Đơn vị công tác:** Vin Smart Future (Vingroup)  
**Mảng vận hành trọng tâm:** **Vinhomes — Quản lý Đô thị Thông minh & Trải nghiệm Cư dân**

---

## 🔍 Phase 1 — SCAN: Quét cơ hội vận hành (Cá nhân)

Sử dụng **4 Lenses** (*Lặp lại, Tốn thời gian, AI-upgrade, Pain từ người khác*) để khảo sát các quy trình vận hành thực tế tại các công ty thành viên Vingroup:

| # | Subsidiary | Lens | Mô tả ngắn bài toán / Bottleneck |
|---|------------|------|-----------------------------------|
| 1 | **Vinhomes** | **Lặp lại** | **Phân loại và điều hướng phản ánh cư dân:** Mỗi ngày Ban Quản Lý (BQL) các đại đô thị (Ocean Park, Smart City, Grand Park) tiếp nhận hàng nghìn yêu cầu/sự cố (mất nước, hỏng đèn hành lang, ồn ào...) qua App Vinhomes Resident; nhân viên CSKH phải đọc tay và forward thủ công cho từng bộ phận kỹ thuật/an ninh. |
| 2 | **Vinhomes** | **Tốn thời gian** | **Thẩm định hồ sơ đăng ký thi công nội thất:** Cư dân gửi hồ sơ bản vẽ cải tạo, BQL mất từ 2-4 ngày để đối chiếu thủ công các quy chuẩn an toàn (PCCC, đập tường chịu lực, đường ống nước) trước khi cấp phép. |
| 3 | **Vinhomes** | **Pain từ người khác** | **Xử lý vi phạm nội quy & tranh chấp cư dân:** Khiếu nại về việc đỗ xe sai quy định, thả rông thú cưng, tiếng ồn ban đêm thường nhận phản hồi rập khuôn hoặc chậm trễ từ BQL, gây bức xúc và leo thang tranh chấp trong cộng đồng cư dân. |
| 4 | **VinFast** | **AI-upgrade** | **Sàng lọc mã lỗi kỹ thuật từ mô tả tiếng Việt:** Khách hàng mô tả hiện tượng xe qua App (ví dụ: *"xe đi qua gờ giảm tốc kêu lục cục ở bánh trước"*), chuyên viên tiếp nhận phải tra cứu thủ công bảng mã lỗi OBD-II để đặt lịch dịch vụ xưởng. |
| 5 | **Xanh SM** | **Tốn thời gian** | **Hỗ trợ xử lý sự cố sạc pin xe taxi thực địa:** Tài xế gọi tổng đài báo sắp hết pin, điều phối viên phải tra cứu bản đồ trạm sạc trống và soạn tin nhắn hướng dẫn thủ công, mất 12-15 phút/lượt. |

---

## 🃏 Phase 2 — QUICK-ASSESS: 3 Thẻ bài toán (Cá nhân)

Chọn **Top 3 bài toán** tiềm năng nhất thuộc mảng Vinhomes: **#1 (Phân loại & điều hướng phản ánh), #2 (Thẩm định hồ sơ thi công), #3 (Xử lý khiếu nại vi phạm nội quy).**

---

### 📇 QUICK PROBLEM CARD #1: Tự động phân loại và điều hướng phản ánh cư dân trên App Vinhomes Resident

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #1                                                   │
│                                                                         │
│ Bài toán (1 câu): Tiếp nhận, phân loại mức độ khẩn cấp và điều hướng     │
│ tự động phản ánh từ App Vinhomes Resident đến đúng đội kỹ thuật/an ninh.│
│ Công ty thành viên: [x] Vinhomes                                        │
│                                                                         │
│ Ai đang đau (Actor)? Chuyên viên CSKH BQL (quá tải), Cư dân (chờ đợi)   │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Cư dân gửi phản ánh text/ảnh qua App Vinhomes Resident             │
│   → 2. Nhân viên CSKH đọc nội dung, xem ảnh, phân loại thủ công        │
│   → 3. Tạo ticket và chuyển giao (assign) đến đội Kỹ thuật / An ninh    │
│   → 4. Soạn tin nhắn phản hồi báo cư dân đã tiếp nhận yêu cầu          │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 8-12 phút/ticket)        │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3 & 4                    │
│ (Trích xuất intent, gán mức độ khẩn cấp, route ticket & draft phản hồi) │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│ - Giảm thời gian định tuyến ticket từ 12 phút ──> dưới 30 giây.        │
│ - Tỉ lệ phân loại đúng phòng ban đạt >= 95%.                            │
│ - 100% sự cố khẩn cấp (rò rỉ khí gas, chập điện) được cảnh báo < 10s.  │
│                                                                         │
│ Quick Architecture: [x] LLM Feature (Kết hợp Rule router & LLM Extract)│
└─────────────────────────────────────────────────────────────────────────┘
```

---

### 📇 QUICK PROBLEM CARD #2: Trợ lý AI thẩm định sơ bộ hồ sơ đăng ký thi công nội thất

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #2                                                   │
│                                                                         │
│ Bài toán (1 câu): Đọc bản vẽ/hồ sơ xin phép sửa chữa nội thất và tự     │
│ động đối chiếu các quy chuẩn an toàn, chỉ ra các điểm vi phạm sơ bộ.   │
│ Công ty thành viên: [x] Vinhomes                                        │
│                                                                         │
│ Ai đang đau (Actor)? Kỹ sư thẩm định BQL Vinhomes, Chủ hộ căn hộ       │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Chủ hộ nộp file PDF mô tả bản vẽ cải tạo nội thất                  │
│   → 2. Kỹ sư BQL đọc từng trang, rà soát danh mục tường/điện/nước     │
│   → 3. Đối chiếu sổ tay quy chuẩn Vinhomes (cấm khoan tường chịu lực...)│
│   → 4. Soạn công văn phản hồi yêu cầu sửa đổi hoặc phê duyệt           │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 2 & 3 (⏱ 45-60 phút/hồ sơ)       │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2 & 3 & 4                    │
│ (Trích xuất danh mục thi công, đối chiếu quy chuẩn, draft checklist)   │
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│ - Rút ngắn thời gian sơ duyệt từ 2 ngày ──> dưới 2 giờ.                 │
│ - Phát hiện 100% hạng mục nhạy cảm cấm can thiệp (PCCC, tường chịu lực).│
│                                                                         │
│ Quick Architecture: [x] LLM Feature (Vision + Document Extraction)      │
└─────────────────────────────────────────────────────────────────────────┘
```

---

### 📇 QUICK PROBLEM CARD #3: Trợ lý soạn thảo phản hồi khiếu nại vi phạm nội quy & tranh chấp cư dân

```text
┌─────────────────────────────────────────────────────────────────────────┐
│ QUICK PROBLEM CARD #3                                                   │
│                                                                         │
│ Bài toán (1 câu): Phân tích tình huống vi phạm (đỗ xe, tiếng ồn, nuôi   │
│ chó mèo) và soạn thảo văn bản giải quyết thấu tình đạt lý, đúng nội quy.│
│ Công ty thành viên: [x] Vinhomes                                        │
│                                                                         │
│ Ai đang đau (Actor)? Trưởng ban quản lý tòa nhà, Bộ phận CSKH          │
│                                                                         │
│ Workflow thủ công hiện tại (4 bước):                                    │
│   1. Tiếp nhận đơn thư/khiếu nại tranh chấp giữa các căn hộ             │
│   → 2. Tra cứu lịch sử vi phạm của căn hộ liên quan                     │
│   → 3. Soạn thư nhắc nhở/xử phạt viện dẫn điều khoản nội quy Vinhomes   │
│   → 4. BQL ký duyệt và gửi thư thông báo đến cư dân                    │
│                                                                         │
│ Bước nào tốn thời gian/lỗi nhất? Bước 3 (⏱ 25-35 phút/văn bản)         │
│ AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3                            │
│ (Tự động tra cứu điều khoản và draft thư phản hồi chuẩn mực, lịch thiệp)│
│                                                                         │
│ Đo thành công bằng gì (Metric có số)?                                   │
│ - Giảm thời gian soạn thảo thư phản hồi từ 30 phút ──> dưới 3 phút.     │
│ - 100% thư nháp đảm bảo chuẩn phong cách giao tiếp văn minh Vinhomes.   │
│                                                                         │
│ Quick Architecture: [x] LLM Feature                                     │
└─────────────────────────────────────────────────────────────────────────┘
```

---

## 🎯 Bài toán đề xuất chọn làm Deep-Dive:
**Card #1 — Tự động phân loại và điều hướng phản ánh cư dân trên App Vinhomes Resident.**
* **Lý do lựa chọn:** Tần suất xảy ra cực lớn (hàng nghìn lượt/ngày), ảnh hưởng trực tiếp đến SLA và sự hài lòng của cư dân, dễ tích hợp giải pháp LLM Feature kết hợp HITL (Human-in-the-loop), rủi ro được kiểm soát tốt thông qua Fallback router.

