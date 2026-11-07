# 01 — Problem Scan & Quick Cards

**Thành viên:** Nguyễn Khôi Nguyên | **Branch:** `nguyenkhoinguyen`

> **Lưu ý về số liệu:** mọi con số về thời gian, tần suất và ngưỡng metric trong file này là **ước lượng/giả định** của tôi dựa trên mô tả bài toán, chưa có dữ liệu vận hành thật của Vingroup. Metric là mục tiêu đề xuất; cần đo baseline thật trước khi cam kết.

---

## Phase 1 — SCAN

Tôi quét theo 4 lens (Lặp lại, Tốn thời gian, AI có thể tốt hơn, Pain từ người khác) trên 5 công ty thành viên.

| # | Subsidiary | Lens | Mô tả ngắn bài toán |
|---|------------|------|---------------------|
| 1 | Xanh SM | Tốn thời gian | Khi tài xế báo hết pin giữa đường, điều phối viên phải tra vị trí xe, tìm trạm sạc VinFast còn trụ trống rồi tự soạn tin chỉ dẫn. Ước lượng ~15 phút/lượt, tài xế phải chờ lâu. |
| 2 | Vinhomes | Lặp lại | Cư dân gửi phản ánh (mất nước, hỏng đèn, ồn ào) qua app; nhân viên đọc từng phản ánh rồi chuyển đúng ban quản lý tòa nhà. Khối lượng lớn mỗi ngày, dễ chuyển nhầm và chậm phản hồi. |
| 3 | VinFast | Lặp lại | Mỗi tuần kế toán đối soát dữ liệu sạc của trụ đối tác với hóa đơn gửi về hệ thống tài chính, dò từng dòng bằng Excel. Tốn nhiều giờ, dễ sót dòng lệch. |
| 4 | Vinmec | Pain từ người khác | Bác sĩ tự viết tóm tắt xuất viện từ bệnh án, xét nghiệm và ghi chú; ước lượng 20-30 phút/bệnh nhân, là việc bác sĩ hay phàn nàn vì quá tải. Sai sót ảnh hưởng an toàn người bệnh. |
| 5 | Vinpearl | AI có thể tốt hơn | Review khách sạn nằm rải rác trên Booking, Agoda, Google Maps; quản lý đọc thủ công nên phàn nàn khẩn (phòng bẩn, thái độ nhân viên) bị phát hiện muộn. |

---

## Phase 2 — QUICK-ASSESS

Tôi chọn top 3: **#2 (Vinhomes), #3 (VinFast), #4 (Vinmec).**

**Lý do chọn:** ba bài toán cho ba mức kiến trúc và ba mức rủi ro khác nhau (LLM duyệt nhẹ, Rule, LLM duyệt bắt buộc), giúp tôi thực hành phân biệt "khi nào cần AI". Bài #1 tôi đã dùng làm prototype kỹ thuật (`prompt_prototype.py`). Bài #5 có giá trị nhưng cách tiếp cận gần giống #2 (đọc văn bản tự do rồi phân loại), nên không chọn.

### Card #1 — Vinhomes: phân loại phản ánh cư dân

```text
QUICK PROBLEM CARD #1
------------------------------------------------------------
Bài toán: Phân loại và chuyển đúng ban quản lý các phản ánh
của cư dân gửi qua ứng dụng cư dân của Vinhomes.
Công ty thành viên: [ ] VinFast [ ] Xanh SM [x] Vinhomes
                    [ ] Vinmec [ ] Khác

Ai đang đau? Nhân viên CSKH (quá tải), cư dân (chờ lâu).

Workflow thủ công hiện tại:
 1. Cư dân gửi phản ánh trên app
 2. CSKH đọc nội dung
 3. Xác định loại sự việc, tòa nhà, mức khẩn
 4. Chuyển cho ban quản lý đúng tòa
 5. Theo dõi và phản hồi cư dân

Bước nào tốn nhất? Bước 2-3 (⏱ ~3 phút/phản ánh, ước lượng)
AI hỗ trợ ở bước nào? Bước 2-3. Rule bắt từ khóa an toàn
(cháy, rò gas, chập điện) và đẩy lên khẩn ngay; LLM gợi ý loại
sự việc + tòa nhà + mức khẩn cho phần còn lại; nhân viên bấm
xác nhận trước khi chuyển.

Metric: Giảm thời gian phân loại từ ~3 phút xuống dưới 30 giây
(đã gồm bước xác nhận); chuyển đúng ban quản lý ≥ 95% trên 200
phản ánh mẫu do 2 nhân viên gán nhãn; phản ánh khẩn bị bỏ sót = 0
trên mẫu đó.

Quick Architecture: [ ] No AI [ ] Rule [x] LLM [ ] Agent
------------------------------------------------------------
```

**Vì sao LLM Feature (kèm Rule làm baseline):**
- Phản ánh viết tự do bằng tiếng Việt, nhiều biến thể nên khó viết hết bằng luật; volume lặp lại lớn.
- Nhân viên duyệt trước khi chuyển, nên sai chỉ tốn công chuyển lại, không gây hại trực tiếp.
- Phần an toàn (cháy, gas, điện) **không giao cho LLM**: dùng Rule để khẳng định chắc chắn.
- Trước khi dùng LLM cần đo baseline: nếu app đã có ô chọn loại sự việc và từ khóa đơn giản đã đạt ≥ 95% thì chưa cần LLM.
- Chưa cần Agent vì chỉ có một bước quyết định, không gọi nhiều công cụ.

### Card #2 — VinFast: đối soát hóa đơn sạc điện

```text
QUICK PROBLEM CARD #2
------------------------------------------------------------
Bài toán: Đối chiếu dữ liệu sạc điện từ trụ đối tác với hóa
đơn gửi về hệ thống tài chính mỗi tuần.
Công ty thành viên: [x] VinFast [ ] Xanh SM [ ] Vinhomes
                    [ ] Vinmec [ ] Khác

Ai đang đau? Nhân viên kế toán/đối soát.

Workflow thủ công hiện tại:
 1. Tải file dữ liệu sạc từ đối tác
 2. Xuất hóa đơn từ hệ thống tài chính
 3. Dò khớp từng dòng bằng Excel
 4. Đánh dấu các dòng lệch
 5. Gửi đối tác xác nhận

Bước nào tốn nhất? Bước 3 (⏱ ~4 giờ/tuần, ước lượng)
AI hỗ trợ ở bước nào? Không cần LLM. Script so khớp theo mã
trạm + khung thời gian + số kWh, có bảng ánh xạ tên trạm giữa
hai nguồn và dung sai kWh cấu hình được.

Metric: Giảm thời gian đối soát từ ~4 giờ xuống dưới 20 phút/
tuần; phát hiện ≥ 99% dòng lệch trên bộ dữ liệu kiểm thử có
nhãn sẵn.

Quick Architecture: [ ] No AI [x] Rule [ ] LLM [ ] Agent
------------------------------------------------------------
```

**Vì sao Rule:**
- Dữ liệu có cấu trúc, logic so khớp rõ ràng; đối soát tài chính cần kết quả lặp lại được và giải thích được từng dòng lệch.
- LLM có thể cho kết quả khác nhau giữa các lần chạy và khó giải thích khi lệch, nên dùng sẽ là overkill.
- Có thể đơn giản hơn nữa: một truy vấn SQL join hoặc Power Query cũng đủ; chọn Rule vì cần cấu hình dung sai và nhật ký các dòng lệch.
- Đây là ví dụ cho nguyên tắc "bắt đầu từ Rule, chỉ lên LLM khi giá trị tăng hơn độ phức tạp".

### Card #3 — Vinmec: tóm tắt hồ sơ xuất viện

```text
QUICK PROBLEM CARD #3
------------------------------------------------------------
Bài toán: Soạn bản tóm tắt xuất viện từ bệnh án điện tử, kết
quả xét nghiệm và ghi chú của bác sĩ.
Công ty thành viên: [ ] VinFast [ ] Xanh SM [ ] Vinhomes
                    [x] Vinmec [ ] Khác

Ai đang đau? Bác sĩ điều trị (quá tải giấy tờ).

Workflow thủ công hiện tại:
 1. Bác sĩ mở bệnh án điện tử
 2. Đọc lại xét nghiệm, ghi chú, đơn thuốc
 3. Tự viết bản tóm tắt xuất viện
 4. Chỉnh sửa và ký duyệt
 5. Giao cho bệnh nhân

Bước nào tốn nhất? Bước 2-3 (⏱ ~20-30 phút/bệnh nhân, ước lượng)
AI hỗ trợ ở bước nào? Bước 2-3. Các trường có cấu trúc (tên
thuốc, liều, kết quả xét nghiệm) được Rule chép nguyên từ bản
ghi, không để LLM viết lại; LLM chỉ soạn phần diễn giải, mỗi
câu gắn nguồn trong bệnh án. Bác sĩ rà soát và ký.

Metric: Giảm thời gian từ ~20-30 phút xuống 5-8 phút (đã gồm
rà soát); 0 lỗi nghiêm trọng (sai thuốc, sai liều) lọt qua rà
soát trên 50 hồ sơ thử nghiệm đã ẩn danh.

Quick Architecture: [ ] No AI [ ] Rule [x] LLM [ ] Agent
------------------------------------------------------------
```

**Vì sao LLM Feature kèm HITL bắt buộc:**
- Phần diễn giải cần tổng hợp nhiều nguồn nên Rule khó đủ; nhưng đây là lĩnh vực rủi ro cao, nên AI chỉ soạn **nháp**, bác sĩ duyệt và ký mới có giá trị, AI không bao giờ tự ghi vào hồ sơ chính thức.
- Rủi ro riêng: bác sĩ có thể ký vội vì tin vào bản nháp. 50 hồ sơ chỉ là ngưỡng loại bỏ ở giai đoạn thử nghiệm, **không đủ để chứng minh an toàn**.
- Dữ liệu bệnh án là dữ liệu cá nhân nhạy cảm; nếu chưa có dữ liệu ẩn danh và phê duyệt nội bộ để thử nghiệm thì quyết định hợp lý hơn là **NOT YET**, bắt đầu bằng việc thu thập dữ liệu và đo baseline.

### Tổng kết 3 card

| Card | Công ty | Kiến trúc | Mức rủi ro nếu sai | Mức duyệt của người |
|---|---|---|---|---|
| #1 | Vinhomes | LLM (Rule làm baseline và bắt ca an toàn) | Thấp-trung bình | Nhân viên bấm xác nhận |
| #2 | VinFast | Rule | Trung bình (tiền) | Kế toán rà các dòng lệch |
| #3 | Vinmec | LLM + HITL | Cao (an toàn người bệnh) | Bác sĩ rà soát và ký bắt buộc |
