# 02 — Deep-Dive Report: Phân loại & chuyển phản ánh cư dân Vinhomes

**Thành viên:** Nguyễn Khôi Nguyên | **Branch:** `nguyenkhoinguyen`
**Bài toán:** Quick Card #1 trong `01-problem-scan.md` (Vinhomes — Lặp lại)

> **Lưu ý về số liệu:** quy trình, thời gian, khối lượng và ngưỡng metric dưới đây là **giả định/ước lượng** của tôi dựa trên mô tả bài toán, chưa có dữ liệu vận hành thật của Vinhomes. Chúng được đánh dấu *(ước lượng)* và là thứ cần đo lại trong thử nghiệm tiếp theo ở Phase 5.

---

## 3.1. Current-State Workflow Mapping

Sơ đồ trực quan: xem `04-workflow-diagram.png`.

| Bước | Ai làm | Công cụ | Thời gian *(ước lượng)* | Ký hiệu |
|---|---|---|---|---|
| 1. Gửi phản ánh (mô tả, ảnh, chọn tòa/căn) | Cư dân | Ứng dụng cư dân | ~2 phút | |
| 2. Đọc nội dung phản ánh | Nhân viên CSKH | Màn hình tiếp nhận | ~1 phút | 🔄 Handoff: cư dân → hàng đợi CSKH |
| 3. Xác định loại sự việc, tòa nhà, mức khẩn | Nhân viên CSKH | Đọc tay, đối chiếu danh mục, hỏi lại cư dân nếu thiếu | ~2 phút | 🔴 **Bottleneck** (cùng bước 2: ~3 phút/phản ánh) |
| 4. Chuyển cho ban quản lý đúng tòa | Nhân viên CSKH → Ban quản lý | Hệ thống ticket / điện thoại | ~1 phút | 🔄 Handoff: CSKH → ban quản lý (dễ chuyển nhầm) |
| 5. Theo dõi và phản hồi cư dân | Nhân viên CSKH | Ticket, tin nhắn | ~2 phút | |

**Tổng cộng ≈ 8 phút/lượt *(ước lượng)*, trong đó thời gian nhân viên ≈ 6 phút/phản ánh.**

**Bottleneck:** bước 2-3. Đây là bước phải đọc văn bản tự do, suy ra loại sự việc và mức khẩn, nên vừa chậm vừa không nhất quán giữa các nhân viên.

---

## 3.2. Problem Statement (6-field) & Metrics

| Field | Nội dung chi tiết |
|---|---|
| **1. Actor / Operator** | Nhân viên CSKH tiếp nhận phản ánh của cư dân Vinhomes (làm việc này hằng ngày); ban quản lý tòa nhà là bên nhận xử lý. |
| **2. Current Workflow** | Cư dân gửi phản ánh bằng văn bản tự do trên ứng dụng; nhân viên CSKH đọc, tự xác định loại sự việc, tòa nhà và mức khẩn, rồi chuyển cho ban quản lý đúng tòa và theo dõi phản hồi (5 bước ở mục 3.1). |
| **3. Bottleneck** | Bước đọc và phân loại (~3 phút/phản ánh *(ước lượng)*): phản ánh viết tự do, nhiều biến thể, một phản ánh có thể chứa nhiều sự việc; tiêu chí "khẩn" phụ thuộc phán đoán từng người nên không nhất quán, và chuyển nhầm ban quản lý làm kéo dài thời gian xử lý. |
| **4. Business Impact** | Thời gian phân loại tiêu tốn công nhân viên CSKH: với N phản ánh/ngày thì mất khoảng 3 × N phút/ngày; ví dụ **giả định N = 300** thì ≈ 15 giờ công/ngày cho riêng bước phân loại. Ngoài ra phản ánh chuyển nhầm hoặc đánh giá thấp mức khẩn làm chậm phản hồi cư dân và có thể ảnh hưởng an toàn. *(N = 300 chỉ là giả định để minh họa; cần thay bằng số liệu thật.)* |
| **5. Success Metric** | (a) Thời gian phân loại một phản ánh từ ~3 phút xuống **dưới 30 giây** (đã gồm bước nhân viên xác nhận). (b) Chuyển đúng ban quản lý **≥ 95%** trên 200 phản ánh mẫu do 2 nhân viên gán nhãn. (c) Phản ánh khẩn bị bỏ sót **= 0** trên mẫu đó. (d) Độ trễ P95 của bước AI **dưới 5 giây**. Nếu đo được baseline thực tế khác, các ngưỡng này sẽ được điều chỉnh. |
| **6. Operational Boundary** | **AI được phép:** gợi ý loại sự việc, tòa nhà, mức khẩn kèm trích đoạn nội dung làm căn cứ. **AI TUYỆT ĐỐI không:** tự chuyển ticket, tự trả lời cư dân, tự hạ mức khẩn của ca đã bị Rule đánh dấu an toàn, hay dùng thông tin cá nhân ngoài nội dung phản ánh. **Cần duyệt:** mọi gợi ý đều do nhân viên CSKH bấm xác nhận hoặc sửa trước khi chuyển; ca có từ khóa an toàn (cháy, rò gas, chập điện) được Rule báo ngay cho trực ban, không chờ AI. |

**Kiểm tra độ chặt:** từ Problem Statement này có thể suy ra test cases (200 phản ánh gán nhãn, nhóm ca an toàn riêng), eval metric (đúng ban, bỏ sót khẩn, latency) và ranh giới kiến trúc (AI chỉ gợi ý, người xác nhận).

---

## 3.3. Future-State Flow & AI Fit

### So sánh Rule vs LLM vs Agent cho bài toán này

| Tiêu chí | Rule / State-Machine | LLM Feature | Agentic Loop |
|---|---|---|---|
| Phù hợp với phản ánh viết tự do nhiều biến thể? | Hạn chế: khó liệt kê hết cách diễn đạt | Tốt | Tốt nhưng thừa |
| Cần gọi nhiều công cụ / thay đổi trạng thái? | Không | Không | Có (không cần ở đây) |
| Kết quả có chắc chắn, giải thích được? | Rất cao | Trung bình, cần kiểm tra | Thấp hơn |
| Rủi ro nếu sai | Thấp | Thấp-trung bình khi có người duyệt | Cao hơn (hành động tự động) |
| Chi phí / độ phức tạp | Thấp | Trung bình | Cao |

### Mức AI Fit được chọn
- [ ] Rule / State-Machine
- [x] **LLM Feature** (kèm lớp Rule cho ca an toàn và để làm baseline)
- [ ] Agentic Loop

**Lý lẽ:** chỉ có một bước quyết định (phân loại) và không cần gọi công cụ nào, nên Agent là thừa (*"nếu không trả lời được vì sao cần planning và tool autonomy, khả năng cao là chưa cần agent"*). Rule một mình khó xử lý văn bản tự do, nhưng Rule vẫn cần cho ca an toàn vì cần kết quả chắc chắn. LLM chỉ có giá trị nếu thử nghiệm cho thấy Rule/dropdown không đạt metric (xem Phase 5).

### Future-State Flow

```text
Cư dân gửi phản ánh
        |
        v
[RULE] Quét từ khóa an toàn (cháy, rò gas, chập điện...)
   |-- có từ khóa --> đánh dấu KHẨN + báo ngay trực ban (người xử lý)
   |
   v
[🔵 AI STEP] LLM trả JSON: {loại sự việc, tòa nhà, mức khẩn, trích đoạn căn cứ}
        |
        v
[RULE] Kiểm tra JSON hợp lệ; tòa nhà có trong danh sách hợp lệ
   |-- lỗi / không hợp lệ / AI báo "không chắc" --> ↩️ FALLBACK
   |
   v
[🟢 HUMAN STEP - HITL] CSKH xem gợi ý, bấm xác nhận hoặc sửa
        |
        v
Chuyển ban quản lý đúng tòa --> theo dõi phản hồi
```

### Human-in-the-loop và Fallback
- **HITL:** nhân viên CSKH xác nhận hoặc sửa mọi gợi ý trước khi chuyển. Các lần sửa được ghi lại để làm dữ liệu đánh giá.
- **↩️ Fallback:** khi LLM lỗi, quá thời gian, trả JSON sai định dạng, tòa nhà ngoài danh sách, hoặc tự báo "không chắc", phản ánh quay về hàng đợi xử lý thủ công như quy trình hiện tại; không có phản ánh nào bị kẹt vì AI. (Không dựa vào "điểm tự tin" do LLM tự báo như một ngưỡng cứng vì chỉ số này không đáng tin; thay vào đó dùng kiểm tra bằng Rule ở trên.)
- **Ca an toàn không phụ thuộc AI:** Rule báo trực ban ngay cả khi LLM sập.

---

## Phase 5 — EVALUATE

### AI Readiness Checklist

1. [ ] **Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test?** *Chưa xác nhận được:* hiện chưa có 200 phản ánh đã ẩn danh và gán nhãn. Đây là điều kiện cần có trước khi build.
2. [x] **Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)?** *Có:* nhân viên duyệt mọi gợi ý, ca an toàn do Rule xử lý, Fallback về quy trình thủ công.
3. [ ] **Stakeholders sẵn sàng thay đổi quy trình làm việc cũ?** *Chưa biết:* chưa phỏng vấn nhân viên CSKH và ban quản lý để biết họ có chấp nhận bước xác nhận gợi ý hay không.

**Kết quả: 1/3.** Theo quy tắc của bài giảng, dưới 3 câu YES thì dừng lại và làm rõ problem/workflow trước khi đầu tư AI.

### RACI-lite (giai đoạn đề xuất đến pilot)

| Hoạt động | Biz/Ops (CSKH, ban quản lý) | AI Eng | Platform/Data | Risk/Legal |
|---|---|---|---|---|
| Xác định problem + metric | A/R | C | I | I |
| Chốt baseline + bộ mẫu đánh giá | C | A/R | R | I |
| Chọn kiến trúc | C | A/R | C | C |
| Chốt HITL / quy tắc ca khẩn | A | C | C | R |
| Go-live / rollback | A | C | R | R |

### Quyết định cuối cùng

- [ ] **GO (Bắt đầu xây dựng Prototype)**
- [x] **NOT YET (Cần tích lũy thêm dữ liệu/xác lập baseline)**
- [ ] **NO-GO (Không khả thi / Rule-based tốt hơn)**

**Justification:**

> Pain có vẻ thật (phân loại thủ công lặp lại, ước lượng ~3 phút/phản ánh) và rủi ro khi AI sai nằm trong tầm kiểm soát nhờ HITL và Fallback, nhưng hiện **chưa đủ bằng chứng để build**: (1) chưa có dữ liệu mẫu đã gán nhãn, nên chưa thể đo bất kỳ metric nào; (2) chưa có baseline, nên không biết AI cần tốt hơn cái gì (một Rule dựa trên ô chọn loại và từ khóa có thể đã đạt ≥ 95%); (3) chưa biết nhân viên CSKH có chấp nhận quy trình mới hay không; (4) các con số thời gian và khối lượng đều là ước lượng. Đầu tư xây LLM lúc này vi phạm nguyên tắc "không có baseline thì không có lợi ích rõ ràng". Quyết định hợp lý là **không build ngay mà đo baseline trước**.

### Next experiment nhỏ nhất (1-2 tuần)

1. Xin 200-300 phản ánh đã ẩn danh; 2 nhân viên gán nhãn độc lập (loại, tòa, mức khẩn) để có "đáp án chuẩn" và đo mức đồng thuận giữa hai người.
2. Đo baseline thủ công: thời gian phân loại trung bình và tỉ lệ chuyển nhầm hiện tại.
3. Thử baseline Rule: danh sách từ khóa và ô chọn loại sự việc, đo độ đúng trên cùng bộ mẫu.
4. Phỏng vấn 3-5 nhân viên CSKH và 1-2 ban quản lý theo 5 câu hỏi discovery (pain, workflow, cost, nếu AI sai thì sao, ai nói YES).

### Điều kiện chuyển trạng thái
- **→ NO-GO cho LLM** nếu baseline Rule đạt các metric (đúng ban ≥ 95%, bỏ sót khẩn = 0): dùng Rule là đủ.
- **→ GO (scope hẹp)** nếu Rule thấp rõ rệt (ví dụ dưới 90%) ở phần phản ánh mơ hồ, đã có dữ liệu gán nhãn, và nhân viên đồng ý quy trình xác nhận: khi đó xây prototype LLM chỉ cho phần Rule không xử lý được.
- **Giữ NOT YET** nếu chưa có dữ liệu hoặc stakeholders chưa đồng ý.
