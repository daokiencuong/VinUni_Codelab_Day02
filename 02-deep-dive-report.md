# 02 — Deep-Dive Report: Vin Smart Future (Vinhomes Operations)

> **Báo cáo phân tích chuyên sâu giải pháp AI cho bài toán vận hành đô thị Vinhomes**  
> **Đơn vị thực hiện:** Nhóm Kỹ sư AI Product — Vin Smart Future  
> **Thành viên đóng góp:** Bùi Văn Lâm và các cộng sự  
> **Công ty thành viên:** **Vinhomes — Quản lý Vận hành Đô thị Thông minh**

---

## 🏛️ 1. Bối cảnh bài toán & Khảo sát thực địa

Tại các đại đô thị Vinhomes (Vinhomes Ocean Park 1-2-3, Vinhomes Smart City, Vinhomes Grand Park), mỗi ngày hệ thống tiếp nhận từ **1.500 đến 3.000 phản ánh/yêu cầu** từ cư dân thông qua ứng dụng **Vinhomes Resident**.

Các phản ánh bao gồm nhiều lĩnh vực khác nhau:
* Sự cố kỹ thuật hạ tầng (hỏng đèn chiếu sáng, mất nước cục bộ, thang máy kêu, chập điện hành lang).
* Vấn đề vệ sinh và cảnh quan (rác thải chưa thu gom, cây xanh gãy đổ).
* An ninh và trật tự (đỗ xe sai quy định dưới lòng đường, tiếng ồn từ việc sửa nhà quá giờ, nuôi thú cưng không rọ mõm).
* Thủ tục hành chính (đăng ký gửi xe, thẻ cư dân, đăng ký vận chuyển đồ).

Hiện tại, toàn bộ khâu đọc hiểu, phân loại mức độ khẩn cấp, tạo ticket và chuyển giao (assign) công việc đến đúng tổ kỹ thuật/an ninh của từng tòa nhà đang được **xử lý thủ công bởi các nhân viên CSKH tại Ban Quản Lý (BQL)**. Điều này gây nên sự chậm trễ nghiêm trọng trong việc phản hồi và xử lý các sự cố khẩn cấp.

---

## 🏗️ 2. Phase 3 — DEEP-DIVE

### 2.1. Current-State Workflow Mapping (Quy trình hiện tại)

Sơ đồ quy trình thủ công 5 bước hiện tại của Ban Quản Lý Vinhomes:

```text
┌────────────────┐     ┌────────────────┐     ┌────────────────┐     ┌────────────────┐
│ Bước 1         │     │ Bước 2         │     │ Bước 3         │     │ Bước 4         │
│ Cư dân gửi     │     │ CSKH đọc &     │     │ CSKH tạo ticket│     │ Gửi thông báo  │
│ phản ánh qua   │ ──→ │ phân loại nội  │ ──→ │ & forward tới  │ ──→ │ xác nhận tiếp  │
│ App Resident   │     │ dung phản ánh  │     │ tổ chuyên trách│     │ nhận tới cư dân│
│                │     │                │     │                │     │                │
│ Ai: Cư dân     │     │ Ai: CSKH BQL   │     │ Ai: CSKH BQL   │     │ Ai: CSKH BQL   │
│ ⏱ 1 phút       │     │ ⏱ 5 phút 🔴    │     │ ⏱ 4 phút 🔴    │     │ ⏱ 3 phút       │
│ In: Text / Ảnh │     │ In: Text / Ảnh │     │ In: Phân loại  │     │ In: Số ticket  │
│ Out: Raw data  │     │ Out: Danh mục  │     │ Out: Task CRM  │     │ Out: Push noti │
└────────────────┘     └────────────────┘     └────────────────┘     └────────────────┘
                                                                              │
                                                                              ▼
                                                                       ┌────────────────┐
                                                                       │ Bước 5         │
                                                                       │ Kỹ thuật viên  │
                                                                       │ nhận task và   │
                                                                       │ đi kiểm tra    │
                                                                       │                │
                                                                       │ Ai: Kỹ thuật   │
                                                                       │ ⏱ 15-30 phút   │
                                                                       └────────────────┘

🔴 = Điểm nghẽn nghiêm trọng (Bottleneck tại Bước 2 & Bước 3)
🔄 = Điểm chuyển giao (Handoff giữa CSKH và Đội Kỹ thuật/An ninh tòa nhà)
⏱ Tổng thời gian chuyển giao trung bình từ khi cư dân gửi đến khi kỹ thuật nhận việc: 12-15 phút/ticket.
```

---

### 2.2. Problem Statement (6-field) — Vin Smart Future Standard

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Chuyên viên CSKH và Điều phối viên dịch vụ thuộc Ban Quản Lý đô thị Vinhomes. |
| **2. Current Workflow** | Cư dân gửi phản ánh bằng văn bản tự do kèm hình ảnh qua App Vinhomes Resident. Nhân viên CSKH đọc từng yêu cầu, xác định tính chất sự cố, tra cứu tòa nhà/căn hộ, mở hệ thống quản lý nội bộ (BMS/CRM) để tạo ticket và chỉ định kỹ thuật viên trực ca tương ứng, sau đó gửi tin nhắn thông báo cho cư dân. Quy trình 5 bước thủ công hoàn toàn, tốn trung bình 12 phút/ticket. |
| **3. Bottleneck** | Bước 2 & 3: Đọc hiểu văn bản tiếng Việt đa dạng (có teencode, từ địa phương, cảm xúc bức xúc) và xem ảnh để phân loại chính xác mức độ khẩn cấp (P1: Khẩn cấp, P2: Cao, P3: Bình thường) và chuyển đúng phòng ban chuyên trách. |
| **4. Business Impact** | Với ~2.000 phản ánh/ngày tại một đại đô thị, BQL tiêu tốn ~400 giờ làm việc/ngày chỉ để phân loại ticket. Tỷ lệ chuyển giao nhầm phòng ban chiếm khoảng 12% (khiến thời gian xử lý bị đội thêm 2-4 tiếng). Đáng ngại nhất, các sự cố rủi ro cao (cháy chập điện, rò rỉ nước) bị trễ do nghẽn hàng đợi xử lý. |
| **5. Success Metric** | 1. **Thời gian điều phối:** Giảm thời gian từ khi nhận phản ánh đến khi tạo ticket giao việc từ **12 phút xuống dưới 30 giây** (Đạt >= 95% số vé).<br>2. **Độ chính xác:** Tỉ lệ phân loại đúng phòng ban và đúng mức độ khẩn cấp đạt **>= 96%**.<br>3. **An toàn khẩn cấp:** 100% sự cố có dấu hiệu nguy hiểm (P1 - cháy nổ, kẹt thang máy) được kích hoạt báo động trong vòng **10 giây**. |
| **6. Operational Boundary** | **ĐƯỢC PHÉP:** Trích xuất thông tin, tóm tắt nội dung phản ánh, phân loại danh mục (Kỹ thuật/An ninh/Cảnh quan), đề xuất mức độ khẩn cấp và draft sẵn tin nhắn xác nhận cho cư dân.<br>**CẤM TUYỆT ĐỐI:** AI không được tự ý quyết định xử phạt vi phạm quy chế cư dân, không được tự ý đóng (close) ticket khi chưa có kỹ thuật viên xác nhận nghiệm thu, không được đưa ra cam kết đền bù thiệt hại tài chính cho cư dân mà không có sự duyệt của Trưởng ban quản lý (bắt buộc Human-in-the-loop). |

---

### 2.3. Future-State Flow & AI Fit

#### Phân tích AI-Fit:
* **Lựa chọn:** **LLM Feature** tích hợp bộ lọc **Rule-based Router**.
* **Lý do:** Quy trình xử lý ticket có cấu trúc rõ ràng, không cần agent tự trị tương tác tự do (Agentic Loop) vì rủi ro tạo ảo giác hoặc tự ý đóng ticket là quá nguy hiểm. Sự kết hợp giữa Rule (bắt từ khóa khẩn cấp: cháy nổ, rò rỉ ga) và LLM (trích xuất ngữ cảnh, tóm tắt và gán nhãn JSON) mang lại độ ổn định và chi phí tối ưu nhất.

#### Sơ đồ Future-State Flow:

```text
┌────────────────┐     ┌────────────────┐     ┌────────────────┐     ┌────────────────┐
│ Bước 1         │     │ Bước 2         │     │ Bước 3         │     │ Bước 4         │
│ Cư dân gửi     │ ──→ │ 🔵 AI Trích    │ ──→ │ 🔵 AI Draft    │ ──→ │ 🟢 Điều phối   │
│ phản ánh qua   │     │ xuất Intent &  │     │ Ticket &       │     │ viên kiểm tra &│
│ App Resident   │     │ Mức khẩn cấp   │     │ Lời phản hồi   │     │ Click phê duyệt│
└────────────────┘     └────────────────┘     └────────────────┘     └────────────────┘
                                                       │                      │
                                                       │                      ▼
                                                       │               ┌────────────────┐
                                                       │               │ Kỹ thuật viên  │
                                                       │               │ nhận việc ngay │
                                                       │               │ trên App NV    │
                                                       │               └────────────────┘
                                                       │
                                                       ▼
                                                ↩️ Fallback Plan:
                                                Nếu độ tự tin của AI < 80%
                                                hoặc ảnh không đọc được:
                                                Hệ thống tự động đẩy về hàng đợi
                                                CSKH xử lý thủ công như cũ.
```

* 🔵 **AI Step:** Tự động phân tích ngôn ngữ tự nhiên từ tin nhắn cư dân, phân loại phòng ban (Điện/Nước/Thang máy/An ninh) và trích xuất JSON có cấu trúc.
* 🟢 **HITL (Human-in-the-loop):** Điều phối viên chỉ cần nhìn màn hình pop-up xem nhanh đề xuất của AI và bấm 1 chạm "Duyệt" (mất 5-10 giây thay vì 10 phút gõ tay).
* ↩️ **Fallback:** Khi văn bản quá mờ ám hoặc AI trả về độ tin cậy thấp, ticket lập tức được đưa vào hàng đợi thủ công của nhân viên để tránh phân bổ sai.

---

## 🏁 3. Phase 5 — EVALUATE & DECISION

### 3.1. AI Readiness Checklist

| Tiêu chí đánh giá | Trạng thái | Đánh giá thực tế tại Vinhomes |
|---|:---:|---|
| **1. Dữ liệu mẫu/logs sạch?** | ✅ Đạt | Ban Quản Lý Vinhomes đã lưu trữ hơn 200.000 tickets lịch sử trên hệ thống CRM kèm nhãn phân loại của kỹ thuật viên. |
| **2. Rủi ro sai sót kiểm soát được?** | ✅ Đạt | Có ranh giới Operational Boundary rõ ràng, 100% tin nhắn gửi đi và phân công việc đều qua bước kiểm duyệt của Điều phối viên (HITL); có Fallback về luồng thủ công khi tự tin thấp. |
| **3. Stakeholders sẵn sàng thay đổi?** | ✅ Đạt | Ban Giám Đốc Vinhomes và đội ngũ BQL đang phải chịu áp lực giảm chi phí vận hành và tăng chỉ số hài lòng (CSAT) của cư dân, rất ủng hộ áp dụng giải pháp. |

---

### 3.2. Quyết định của Ban Giám Đốc Vin Smart Future

* [x] **GO (Bắt đầu xây dựng Prototype và thử nghiệm tại Vinhomes Ocean Park)**
* [ ] **NOT YET (Cần tích lũy thêm dữ liệu / xác lập baseline)**
* [ ] **NO-GO (Không khả thi / Rule-based tốt hơn)**

### 3.3. Justification (Lý giải quyết định dựa trên Bằng chứng Kỹ thuật & Chi phí)

1. **Hiệu quả kinh tế & ROI rõ ràng:**
   * Giúp tiết kiệm hơn 300 giờ làm việc thủ công mỗi ngày của đội ngũ CSKH trên một đại đô thị, giảm chi phí nhân sự vận hành ước tính ~2,4 tỷ VNĐ/năm cho mỗi phân khu.
   * Rút ngắn thời gian tiếp nhận sự cố kỹ thuật từ 15 phút xuống dưới 30 giây, giúp bảo vệ an toàn tính mạng cư dân trong các tình huống khẩn cấp (chập điện, ngập tầng hầm).

2. **Tính khả thi kỹ thuật vượt trội:**
   * Bài toán hoàn toàn phù hợp với năng lực của mô hình ngôn ngữ lớn (Gemini 2.5 Flash) về khả năng đọc hiểu tiếng Việt phong phú và trả về JSON chuẩn xác.
   * Chi phí API cực kỳ thấp (sử dụng Gemini Flash với chi phí chỉ vài nghìn đồng cho hàng nghìn tickets/ngày), hoàn toàn áp đảo chi phí nhân công thủ công.

3. **Rủi ro vận hành bằng 0 nhờ cơ chế Fallback:**
   * Toàn bộ hệ thống được thiết kế theo nguyên tắc "AI co-pilot": AI đề xuất, con người duyệt 1 chạm. Nếu AI gặp sự cố, hệ thống tự động quay lại quy trình thủ công mà không làm gián đoạn việc tiếp nhận phản ánh của cư dân.

