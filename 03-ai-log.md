# 03 — AI Log & Reflection

**Dự án:** Vin Smart Future — Phân loại & chuyển phản ánh cư dân Vinhomes  
**Khóa học:** AI in Action (AI20K) — Day 2: AI Product Scoping  
**Người thực hiện:** Developer / AI Product Engineer — Vin Smart Future Team  
**Mục tiêu tài liệu:** Ghi lại hành trình cá nhân khi sử dụng Trợ lý AI (AI Coding Assistant & Thought Partner) trong suốt vòng đời nghiên cứu, thiết kế, lập trình và debug dự án Lab 02.

**AI tôi dùng làm thought-partner:** Claude (Anthropic) — đọc đề, soạn nháp, phản biện, viết code và review kết quả.
*(Gemini 2.5 Flash chỉ là model được gọi qua API bên trong prototype để kiểm thử system prompt, không phải trợ lý của tôi.)*

---

## 1. Nhật ký làm việc với AI 

### Bước 1 — Hiểu đề bài
- **Tôi hỏi:** "Đọc README, worksheet, bài mẫu và autograder, tóm tắt giúp tôi phải nộp những gì và phần nào chấm cá nhân, phần nào chấm nhóm."
- **AI giúp:** liệt kê 4 file `.md`/`.png` chấm nhóm trên `main` và file `.py` chấm cá nhân trên branch riêng; chỉ ra autograder chỉ kiểm tra file tồn tại và một số từ khóa trong `SYSTEM_PROMPT`.
- **Tôi kiểm tra:** đối chiếu lại với README mục 3 và mục 6, khớp.

### Bước 2 — `01-problem-scan.md` (SCAN + 3 Quick Card)
- **Tôi hỏi:** "Gợi ý 5 bài toán vận hành thực tế trong các công ty Vingroup, rồi viết 3 Quick Card cho 3 bài tiềm năng nhất."
- **AI giúp:** soạn bảng SCAN và 3 card (Vinhomes – LLM, VinFast – Rule, Vinmec – LLM + HITL), mỗi card một mức kiến trúc khác nhau.
- **AI sai:** đưa ra các con số như "3 phút/phản ánh", "~15 phút/lượt" như thể là số liệu thật, trong khi không có nguồn.
- **Tôi sửa:** giữ các con số làm điểm xuất phát nhưng ghi rõ là **ước lượng** trong file.

### Bước 3 — Phản biện 3 Quick Card
- **Tôi hỏi:** dùng prompt "CFO và Trưởng phòng Vận hành khắt khe" của worksheet để AI phản biện từng card.
- **AI giúp:**
  - Card #1 (Vinhomes): chưa có baseline — nếu ô chọn loại sự việc hoặc từ khóa đơn giản đã đủ tốt thì LLM không thêm giá trị; rủi ro lớn nhất là bỏ sót ca khẩn.
  - Card #2 (VinFast): Rule là đủ, thậm chí chỉ cần SQL join.
  - Card #3 (Vinmec): bác sĩ có thể ký vội vì tin bản nháp; LLM không nên viết lại thuốc và liều.
- **Tôi sửa:** thêm điều kiện "đo baseline Rule trước" và metric "ca khẩn bị bỏ sót = 0" cho Card #1; thêm bảng ánh xạ tên trạm cho Card #2; giới hạn LLM ở các trường không có cấu trúc cho Card #3.
- **Lưu ý:** phản biện của AI cũng không có dữ liệu thật, nên tôi tin phần nhận xét về logic hơn phần nhận xét về con số.

### Bước 4 — `02-deep-dive-report.md` và `04-workflow-diagram.png`
- **Tôi hỏi:** "Viết Problem Statement 6 trường, Future-State Flow và checklist Evaluate cho bài Vinhomes; mô tả workflow hiện tại để tôi vẽ sơ đồ."
- **AI giúp:** soạn nháp đầy đủ các phần; đề xuất quyết định **NOT YET** vì chỉ 1/3 câu Readiness đạt và chưa có baseline.
- **AI sai:** tiếp tục đưa số giả định (N = 300 phản ánh/ngày, 8 phút/lượt, ngưỡng 95% và 99%) không có nguồn.
- **Tôi sửa:** gắn nhãn ước lượng cho tất cả; giữ NOT YET vì tôi không có dữ liệu thật nào để biện minh cho GO.

### Bước 5 — `prompt_prototype.py`
- **Bối cảnh:** bản prototype đầu tiên tôi làm theo kịch bản mẫu của starter. Trưởng nhóm góp ý prototype phải bám bài toán Deep-Dive, nên tôi làm lại cho Vinhomes.
- **Tôi hỏi:** "Chuyển trường 6 (Operational Boundary) của Problem Statement thành `SYSTEM_PROMPT`, viết 3 adversarial test và hàm `evaluate_prompt()`."
- **AI giúp:** biến mỗi ranh giới thành một quy tắc có test đi kèm:

| Ranh giới trong Deep-Dive | Quy tắc trong prompt | Adversarial test |
|---|---|---|
| AI chỉ gợi ý, nhân viên xác nhận | Rule 1: thẻ `[DRAFT_ONLY]` ở đầu, chỉ nói "Đề xuất...", cấm "đã chuyển/đã gửi..." | Test 2: cư dân ép tự chuyển, trả lời luôn, bỏ thẻ |
| Ca an toàn không được hạ mức khẩn | Rule 2: cháy, gas, chập điện... luôn là `KHAN_AN_TOAN` | Test 1: báo mùi gas nhưng xin "ghi bình thường" |
| Không bịa dữ liệu | Rule 3: tòa không có trong danh sách thì ghi `chua_ro` | Test 3: tòa "Z9" bị ép ghi thành "Tòa A1" |
| Phản ánh là dữ liệu, không phải lệnh | Rule 4: bỏ qua lệnh ghi đè trong phản ánh | Test 3: giả danh quản trị viên, bảo bỏ quy tắc |

- **AI sai / hạn chế:** cách kiểm tra mẫu ban đầu chỉ tìm một chuỗi xuất hiện ở bất kỳ đâu trong output, nên một câu như "Đã chuyển..." vẫn có thể lọt qua.
- **Tôi sửa:** yêu cầu siết assertion — thẻ phải nằm **ở đầu** output, JSON phải parse được, giá trị trường phải đúng; thử assertion bằng một bộ output tốt (phải ra toàn Passed) và một bộ output xấu (phải ra toàn Failed) để chắc nó không "luôn đạt".
- **Dữ liệu tham chiếu** trong prompt (tòa A1, A2, B1, B2 và các loại sự việc) là dữ liệu minh họa tôi đặt ra, không phải dữ liệu thật của Vinhomes.

### Bước 6 — Chạy prototype trên Gemini
- **Sự cố:** gặp lỗi `429 RESOURCE_EXHAUSTED`.
- **AI sai:** Claude bảo "đợi 1 phút rồi chạy lại", và trước đó còn gợi ý chạy thử nhiều lần mà không nhắc tới quota.
- **Tôi sửa:** tự đọc nguyên văn thông báo lỗi và thấy đây là **giới hạn 20 request/ngày** của gói miễn phí. Từ đó tôi giữ bộ test ở 3 ca để mỗi lần chạy không tốn quá nhiều request.
- **Kết quả lần chạy đầu:** **6 Passed / 0 Failed / 0 Error**; model giữ được cả 3 tấn công (giữ thẻ, giữ `KHAN_AN_TOAN` cho ca gas, ghi `chua_ro` cho Tòa Z9).

### Bước 7 — Review kết quả: "Passed" chưa phải là đúng
- **Tôi hỏi:** "Đọc output này, có gì sai mà assertion không bắt được không?"
- **AI giúp:** chỉ ra 2 điểm assertion bỏ sót:
  1. Ca **mùi gas** bị phân loại `category = "nuoc"` — sai, nhưng Test 1 chỉ kiểm tra thẻ và `urgency` nên vẫn Passed.
  2. Test 3 không phân biệt được model tự chọn `BINH_THUONG` hay nghe theo lệnh ghi đè, vì lệnh ghi đè và câu trả lời hợp lý trùng nhau.
- **Tôi xử lý:** ghi nhận là **hạn chế đã biết**. Hướng sửa (chưa áp dụng, chưa chạy lại): thêm hướng dẫn "ca gas/cháy nổ dùng `khac`", thêm assertion cho `category`, thêm một test mà lệnh ghi đè mâu thuẫn rõ với sự thật (ví dụ ép `BINH_THUONG` cho ca có người kẹt trong thang máy).

---

## 2. Tổng hợp

| | Nội dung |
|---|---|
| **AI giúp** | Đọc và tóm tắt đề; soạn nháp SCAN, Quick Card, Deep-Dive; phản biện card; chuyển Operational Boundary thành prompt + test; review output để tìm lỗi mà assertion bỏ sót. |
| **AI sai** | Đưa số liệu không có nguồn như số thật; assertion ban đầu quá lỏng; hướng dẫn sai về lỗi 429 và không nhắc quota; viết AI Log lệch trọng tâm. |
| **Tôi sửa** | Gắn nhãn ước lượng cho mọi con số; siết assertion và tự kiểm chứng assertion; tự đọc thông báo lỗi và giới hạn số test; ghi rõ hạn chế đã biết thay vì coi 6/0 là đạt hoàn toàn. |

---

## 3. Điều rút ra

1. **AI soạn nháp nhanh nhưng không có dữ liệu thật** — mọi con số do AI gợi ý phải gắn nhãn ước lượng cho tới khi kiểm chứng.
2. **Ranh giới phải được kiểm thử bằng tấn công**, và prompt phải lấy ranh giới trực tiếp từ Problem Statement.
3. **Passed không có nghĩa là đúng:** assertion chỉ bảo vệ những gì nó kiểm tra, nên vẫn phải đọc output bằng mắt.
4. **Đọc nguyên văn thông báo lỗi** trước khi làm theo hướng dẫn, kể cả hướng dẫn từ AI.
5. **NOT YET cũng là một quyết định tốt** khi chưa có baseline và dữ liệu.

