# Phase 3 — DEEP-DIVE: Vinhomes AI Resident Complaint Intelligence Assistant
## 3.1. Current-State Workflow
Quy trình xử lý phản ánh cư dân hiện tại của nhân viên CSKH Vinhomes:

```text
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│ Bước 1           │     │ Bước 2           │     │ Bước 3           │     │ Bước 4           │
│ Nhận phản ánh    │     │ Đọc, đối chiếu   │     │ Chọn bộ phận     │     │ 🔄 Chuyển yêu    │
│ của cư dân       │     │ danh mục, tách   │     │ & mức ưu tiên    │     │ cầu đến bộ phận  │
│                  │ ──→ │ nhiều vấn đề     │ ──→ │                  │ ──→ │                  │
│ Ai: CSKH         │     │ Ai: CSKH         │     │ Ai: CSKH         │     │ Ai: CSKH         │
│ ⏱ 1 phút         │     │ ⏱ 4 phút 🔴      │     │ ⏱ 3 phút 🔴      │     │ ⏱ 2 phút         │
│ In: App Resident │     │ In: Mô tả, DM    │     │ In: DS vấn đề    │     │ In: BP, ưu tiên  │
│ Out: DM + mô tả  │     │ Out: DS vấn đề   │     │ Out: BP, ưu tiên │     │ Out: Ticket      │
└──────────────────┘     └──────────────────┘     └──────────────────┘     └──────────────────┘
                                                                                     │
                                                                                     ▼
                                                                           ┌──────────────────┐
                                                                           │ Bước 5           │
                                                                           │ Chuyển lại khi   │
                                                                           │ nhầm bộ phận     │
                                                                           │ (nếu cần)        │
                                                                           │ Ai: CSKH         │
                                                                           │ ⏱ 3 phút         │
                                                                           │ In: Ticket sai   │
                                                                           │ Out: Ticket mới  │
                                                                           └──────────────────┘

🔴 = Bottleneck    🔄 = Handoff    DM = danh mục    BP = bộ phận
⏱ Tổng thời gian xử lý thủ công: 10 phút/lượt (13 phút nếu phải chuyển lại).
```

*Lưu ý: số phút là ước lượng giả định, cần đo trên mẫu 500–1.000 ca thực tế. Sơ đồ dừng ở bước bàn giao cho bộ phận xử lý, vì bài toán tập trung vào phân tích và điều phối.*

## 3.2. Problem Statement (6-field) 

## 3.2. Problem Statement (6-field) — Vin Smart Future Standard

| Field | Nội dung |
|---|---|
| **1. Actor / Operator** | Nhân viên chăm sóc khách hàng (CSKH) và ban quản lý khu đô thị Vinhomes. |
| **2. Current Workflow** | Cư dân chọn danh mục và nhập mô tả phản ánh trong app Vinhomes Resident. Nhân viên CSKH đọc từng phản ánh, đối chiếu danh mục với mô tả, tách các vấn đề nếu có nhiều nội dung, chọn bộ phận và mức ưu tiên, rồi chuyển yêu cầu đi. Nếu chuyển nhầm thì phải chuyển lại. 5 bước, hoàn toàn thủ công, mất khoảng 10 phút/lượt (13 phút nếu chuyển lại). |
| **3. Bottleneck** | Bước 2 & 3 (mất khoảng 7 phút): đọc nội dung tự nhiên, kiểm tra danh mục cư dân chọn có đúng không, phát hiện phản ánh chứa nhiều vấn đề, xác định mức khẩn cấp và chọn đúng bộ phận. Dễ chuyển nhầm và dễ bỏ sót ca khẩn cấp khi khối lượng phản ánh lớn. |
| **4. Business Impact** | *(Giả định, cần kiểm chứng bằng mẫu 500–1.000 ca thực tế.)* Mỗi ngày có khoảng **300** phản ánh. Với 7 phút/lượt ở khâu bottleneck, đây là khoảng **35 giờ công CSKH/ngày**. Tỷ lệ chuyển nhầm bộ phận ước tính **15%**, mỗi ca nhầm tốn thêm khoảng 3 phút và kéo dài thời gian phản hồi cư dân. Ca khẩn cấp bị phân loại chậm có thể vi phạm SLA và ảnh hưởng trải nghiệm cư dân. |
| **5. Success Metric** | *(Mục tiêu cần kiểm chứng bằng pilot, chưa phải kết quả đã đo.)*<br>1. Giảm thời gian phân tích và điều phối từ khoảng 10 phút xuống khoảng 5 phút/lượt, tức giảm ~50% (Efficiency).<br>2. Độ chính xác phân loại ≥ 90% và giảm điều phối sai ~30% so với baseline (Quality).<br>3. Recall ca khẩn cấp ≥ 95%; 100% ca nghiêm trọng hoặc AI không chắc chắn được nhân viên xem xét (Safety). |
| **6. Operational Boundary** | **AI được phép:** đọc và phân tích nội dung phản ánh, đối chiếu danh mục cư dân đã chọn và cảnh báo khả năng chọn sai, phát hiện nhiều vấn đề trong một phản ánh, tóm tắt thông tin quan trọng, đề xuất bộ phận xử lý và mức ưu tiên (dạng đề xuất).<br>**CẤM:** AI không được tự đổi danh mục cư dân đã chọn; không tự chuyển yêu cầu chính thức; không tự gửi phản hồi cho cư dân; không tự quyết ca rủi ro cao. Mọi đề xuất điều phối phải được nhân viên CSKH phê duyệt (bắt buộc HITL). Dấu hiệu nguy hiểm phải kích hoạt quy trình khẩn cấp đã được phê duyệt, không phụ thuộc vào LLM. Dữ liệu cá nhân của cư dân được bảo vệ và xử lý theo quy định pháp luật. |

## 3.3. Future-State Flow & AI Fit

* **AI-Fit Matrix:** [x] Rule / State-Machine  [x] LLM Feature  [ ] Agentic Loop
* **AI Fit:** Chọn **LLM Feature kết hợp Rule-based** (không cần Agent tự trị vì quy trình có cấu trúc cố định: nhận phản ánh → phân tích → duyệt → điều phối). LLM đảm nhận phần hiểu ngữ nghĩa mà rule khó làm: kiểm tra danh mục cư dân chọn, tách nhiều vấn đề, tóm tắt. Rule-based đảm nhận mức ưu tiên và dấu hiệu nguy hiểm vì cần kết quả xác định, kiểm toán được. Rủi ro khi AI tự quyết (chuyển nhầm hoặc bỏ sót ca khẩn cấp như cháy, rò gas, kẹt thang máy) nên bắt buộc có HITL.
* **Quy trình tương lai (Future-State):**

```text
┌──────────────────┐   ┌──────────────────┐   ┌──────────────────┐   ┌──────────────────┐
│ Bước 1           │   │ Bước 2           │   │ Bước 3           │   │ Bước 4           │
│ Cư dân gửi phản  │   │ 🔵 Rule + LLM    │   │ 🟢 CSKH xem đề   │   │ Chuyển yêu cầu   │
│ ánh trong app    │   │ phân tích, tóm   │   │ xuất, sửa nếu    │   │ đến bộ phận      │
│ (giữ danh mục    │──→│ tắt, đối chiếu   │──→│ cần, bấm duyệt   │──→│ phụ trách        │
│ đã chọn)         │   │ danh mục, đề xuất│   │                  │   │                  │
│                  │   │ BP + ưu tiên     │   │                  │   │                  │
│ ⏱ 1 phút         │   │ ⏱ < 1 phút       │   │ ⏱ 2 phút         │   │ ⏱ 2 phút         │
└──────────────────┘   └──────────────────┘   └──────────────────┘   └──────────────────┘
                              │
              ┌───────────────┴───────────────┐
              ▼                               ▼
┌───────────────────────────┐   ┌───────────────────────────┐
│ 🚨 Nhánh khẩn cấp         │   │ 🔁 Fallback               │
│ Rule phát hiện dấu hiệu   │   │ Nếu LLM lỗi, quá thời     │
│ nguy hiểm → kích hoạt quy │   │ gian hoặc độ tin cậy thấp │
│ trình khẩn cấp đã phê     │   │ → bỏ đề xuất AI, CSKH xử  │
│ duyệt, báo CSKH ngay      │   │ lý thủ công như cũ        │
│ (không phụ thuộc LLM)     │   │                           │
└───────────────────────────┘   └───────────────────────────┘

🔵 = AI Step    🟢 = Human Step (HITL)    🔁 = Fallback    BP = bộ phận
⏱ Tổng thời gian ước tính: ~5 phút/lượt (so với 10 phút hiện tại).
```

*Lưu ý: số phút là ước lượng giả định, cần kiểm chứng bằng pilot trên dữ liệu thực tế.*

# Phase 5 — EVALUATE

### AI Readiness Checklist:
1. [ ] Chúng tôi có sẵn dữ liệu mẫu/logs sạch để test?
2. [x] Rủi ro khi AI sai có nằm trong tầm kiểm soát (qua HITL hoặc Fallback)?
3. [ ] Stakeholders sẵn sàng thay đổi quy trình làm việc cũ?

### Quyết định cuối cùng của Ban Giám Đốc Vin Smart Future:
[ ] **GO (Bắt đầu xây dựng Prototype):** Bắt đầu phát triển với scope hẹp.
[x] **NOT YET (Cần tích lũy thêm dữ liệu/xác lập baseline):** Trì hoãn để chuẩn bị thêm.
[ ] **NO-GO (Không khả thi / Rule-based tốt hơn):** Hủy bỏ dự án AI này.

**Justification (Lý giải quyết định dựa trên bằng chứng kỹ thuật và chi phí):**
> **Vì sao không GO:** Hiện chưa có dữ liệu thực tế để chứng minh bài toán. Các số trong Problem Statement (khoảng 300 phản ánh/ngày, 10 phút/lượt, tỷ lệ chuyển nhầm 15%, giảm ~50% thời gian, accuracy ≥ 90%, recall khẩn cấp ≥ 95%) đều là giả định hoặc mục tiêu cần kiểm chứng, chưa phải kết quả đo. Chưa có baseline thực tế về số phản ánh/ngày, thời gian xử lý mỗi lượt, tỷ lệ chuyển nhầm và tỷ lệ vi phạm SLA. Chưa có dữ liệu phản ánh đã gán nhãn để đánh giá mô hình, và chưa có xác nhận từ CSKH, ban quản lý về việc thay đổi quy trình.
>
> **Vì sao không NO-GO:** Rủi ro AI sai nằm trong tầm kiểm soát nhờ thiết kế an toàn: AI chỉ đề xuất, CSKH duyệt trước khi điều phối, ca khẩn cấp do Rule xử lý không phụ thuộc LLM, và có Fallback về quy trình thủ công khi LLM lỗi hoặc không chắc chắn. Quy trình có khối lượng lớn, lặp lại, có đầu ra đo được, nên đáng để chuẩn bị thử nghiệm. Nếu các giả định được xác nhận, khâu phân tích và điều phối chiếm khoảng 35 giờ công CSKH/ngày và khoảng 45 ca chuyển nhầm/ngày, đủ lớn để đáng đo baseline. Ứng dụng Vinhomes Resident đã có chọn danh mục, vì vậy giá trị của AI nằm ở phần form và rule-based khó làm (hiểu ngữ nghĩa, kiểm tra danh mục chọn sai, tách nhiều vấn đề trong một phản ánh), và cần chứng minh bằng thử nghiệm.
>
> **Điều kiện để chuyển sang GO:**
> 1. Thu thập mẫu 500–1.000 phản ánh thực tế và đo baseline (số phản ánh/ngày, thời gian xử lý, tỷ lệ chuyển nhầm, SLA).
> 2. Thử rule-based/từ khóa trên cùng mẫu và chứng minh còn một phần đáng kể mà rule không xử lý được.
> 3. Hai người cùng gán nhãn độc lập để có baseline độ chính xác của con người.
> 4. Lập business case: giờ công tiết kiệm so với chi phí LLM, hạ tầng, vận hành.
> 5. Xác nhận pháp lý về xử lý dữ liệu cá nhân của cư dân khi dùng LLM, và có sự đồng ý của CSKH, ban quản lý.