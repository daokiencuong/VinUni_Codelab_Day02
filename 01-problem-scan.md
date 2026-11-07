Phase 1 — SCAN
### 📝 List bài toán của tôi:

| # | Subsidiary (VinFast/Xanh SM...) | Lens | Mô tả ngắn bài toán |
|---|---|---|---|
| 1 | Xanh SM | Tốn thời gian | Điều phối viên phải xử lý thủ công các phản hồi khẩn cấp từ tài xế về sự cố sạc pin, va chạm và sự cố thực địa, mất khoảng 15–20 phút/lượt. |
| 2 | VinFast | Lặp lại | Nhân viên phải đối chiếu hóa đơn sạc điện và dữ liệu tiêu thụ điện từ các trạm sạc đối tác hằng tuần để phát hiện sai lệch. |
| 3 | Vinhomes | AI-upgrade | Nhân viên CSKH phải phân loại, chuyển bộ phận và xử lý thủ công các phản ánh, khiếu nại của cư dân trên ứng dụng Vinhomes Resident. |
| 4 | Vinmec | Tốn thời gian | Bác sĩ mất khoảng 20–30 phút/bệnh nhân để tổng hợp và viết tóm tắt hồ sơ xuất viện, làm tăng áp lực công việc hành chính. |
| 5 | Xanh SM | Pain từ người khác | Nhân viên phân tích vận hành phải nghe lại các cuộc gọi ghi âm và đọc ghi chú của tài xế để tổng hợp nguyên nhân khách hàng hủy chuyến, tìm ra các vấn đề lặp lại. |

PHASE 2 — QUICK-ASSESS
### QUICK PROBLEM CARD #1 Xanh SM

Bài toán (1 câu): Tự động tiếp nhận, phân loại và đề xuất điều phối các sự cố khẩn cấp từ tài xế Xanh SM.

Công ty thành viên: [ ] VinFast  [x] Xanh SM  [ ] Vinhomes  [ ] Vinmec  [ ] Khác

Ai đang đau (Actor)? Nhân viên điều phối vận hành và bộ phận hỗ trợ tài xế Xanh SM.

Workflow thủ công hiện tại (4 bước):

1. Tài xế gặp sự cố và liên hệ tổng đài hỗ trợ.
2. Nhân viên tiếp nhận, hỏi thông tin và ghi nhận sự cố.
3. Nhân viên xác định mức độ nghiêm trọng và tìm bộ phận phụ trách.
4. Chuyển thông tin đến bộ phận liên quan và theo dõi xử lý.

Bước nào tốn thời gian/lỗi nhất? Bước 2–3: Tiếp nhận thông tin, xác định loại sự cố và mức độ ưu tiên (15–20 phút/lượt theo giả định ban đầu).

AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–3: AI tự động trích xuất thông tin, phân loại sự cố, đánh giá mức độ ưu tiên theo quy tắc và đề xuất bộ phận xử lý.

Đo thành công bằng gì (Metric có số)?
- Giảm thời gian tiếp nhận và phân loại từ 15–20 phút xuống ≤ 5 phút/lượt.
- Độ chính xác phân loại sự cố ≥ 95%.
- Giảm ít nhất 50% thời gian xử lý thông tin ban đầu.

Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent

### QUICK PROBLEM CARD #2 VINHOMES

Bài toán (1 câu): Tự động phân loại và điều phối khiếu nại của cư dân đến đúng bộ phận phụ trách trên ứng dụng Vinhomes Resident.

Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  [ ] Vinmec  [ ] Khác

Ai đang đau (Actor)? Nhân viên chăm sóc khách hàng (CSKH) và ban quản lý khu đô thị Vinhomes.

Workflow thủ công hiện tại (4 bước):

1. Cư dân gửi phản ánh hoặc khiếu nại qua ứng dụng.
2. Nhân viên CSKH đọc và xác định nội dung vấn đề.
3. Nhân viên phân loại và chuyển yêu cầu đến bộ phận phụ trách.
4. Bộ phận liên quan xử lý, CSKH cập nhật kết quả cho cư dân.

Bước nào tốn thời gian/lỗi nhất? Bước 2–3: Đọc, phân loại và chuyển khiếu nại đến đúng bộ phận (thời gian phản hồi có thể kéo dài; mốc 12 giờ là giả định cần xác minh từ ví dụ ban đầu).

AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–3: LLM đọc nội dung, tự động phân loại khiếu nại, tóm tắt vấn đề và đề xuất bộ phận tiếp nhận; Rule-based xác định mức độ ưu tiên.

Đo thành công bằng gì (Metric có số)?
- Thời gian phân loại một phản ánh ≤ 1 phút.
- Độ chính xác phân loại ≥ 95%.
- Tỷ lệ điều phối đúng bộ phận ≥ 95%.
- Giảm ít nhất 50% thời gian xử lý ban đầu.

Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent

### QUICK PROBLEM CARD #3 VINMEC

Bài toán (1 câu): Sử dụng AI để tự động tổng hợp bản nháp tóm tắt hồ sơ xuất viện từ hồ sơ bệnh án, hỗ trợ giảm công việc hành chính cho bác sĩ.

Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [ ] Vinhomes  [x] Vinmec  [ ] Khác

Ai đang đau (Actor)? Bác sĩ điều trị và nhân viên phụ trách quản lý hồ sơ bệnh án.

Workflow thủ công hiện tại (4 bước):

1. Bệnh nhân hoàn thành quá trình điều trị nội trú.
2. Bác sĩ kiểm tra hồ sơ bệnh án, kết quả xét nghiệm và quá trình điều trị.
3. Bác sĩ tổng hợp thông tin và tự viết tóm tắt hồ sơ xuất viện.
4. Bác sĩ kiểm tra, chỉnh sửa và xác nhận hồ sơ.

Bước nào tốn thời gian/lỗi nhất? Bước 2–3: Đọc, tổng hợp thông tin từ nhiều phần của hồ sơ bệnh án và viết tóm tắt (20–30 phút/bệnh nhân theo giả định ban đầu).

AI có thể nhảy vào hỗ trợ ở bước nào? Bước 2–3: LLM kết hợp RAG truy xuất thông tin từ hồ sơ bệnh án, tổng hợp chẩn đoán, quá trình điều trị, thuốc và kết quả xét nghiệm để tạo bản nháp tóm tắt xuất viện.

Đo thành công bằng gì (Metric có số)?

- Giảm thời gian soạn tóm tắt từ 20–30 phút xuống ≤ 10 phút/bệnh nhân.
- Độ chính xác trích xuất thông tin ≥ 95%.
- Tỷ lệ hồ sơ có đầy đủ trường thông tin bắt buộc ≥ 98%.
- 100% hồ sơ được bác sĩ kiểm tra và phê duyệt trước khi sử dụng.

Quick Architecture: [ ] No AI  [ ] Rule  [x] LLM  [ ] Agent

**Operational Boundary:** AI chỉ tạo bản nháp và đánh dấu thông tin cần xác minh. Không tự chẩn đoán, kê đơn hoặc ký xác nhận hồ sơ. Bác sĩ chịu trách nhiệm kiểm tra và phê duyệt toàn bộ nội dung.

**🤖 AI Prompts — Stress-Test thẻ bài toán:**
### AI Stress-Test – Critical Review

Bài toán được phản biện: Vinhomes – AI Resident Complaint Intelligence Assistant.

Vai trò LLM phản biện: CFO kiêm Trưởng phòng Vận hành.

Kết quả phản biện:

1. Ba điểm yếu lớn nhất

- Logic nghiệp vụ: Chưa chứng minh việc phân loại khiếu nại là nút thắt chính. Thời gian xử lý có thể nằm ở khâu phối hợp và xử lý sự cố thực tế.
- Metrics: Chưa có baseline về thời gian xử lý, tỷ lệ chuyển sai bộ phận, chi phí trên mỗi khiếu nại và mức độ hài lòng của cư dân.
- Giải pháp AI: Chưa chứng minh LLM tốt hơn Rule-based. Việc bắt nhân viên phê duyệt 100% kết quả có thể làm giảm hiệu quả tiết kiệm thời gian.

2. Vì sao Rule-based có thể tốt hơn AI?

- Các trường hợp phổ biến có thể được xử lý bằng danh mục và quy tắc định tuyến.
- Rule-based có chi phí thấp, tốc độ nhanh và dễ kiểm toán.
- LLM chỉ thực sự cần thiết đối với nội dung phức tạp, mơ hồ, nhiều vấn đề hoặc cần tóm tắt ngữ nghĩa.

3. Các giả định cần xác minh

- Khối lượng khiếu nại và thời gian xử lý trung bình.
- Tỷ lệ sai lệch giữa danh mục cư dân lựa chọn và nội dung mô tả.
- Tỷ lệ chuyển sai bộ phận.
- Khả năng truy cập dữ liệu lịch sử đã được gán nhãn.
- Khả năng tích hợp AI và yêu cầu bảo vệ dữ liệu cá nhân.

4. Hướng cải tiến

- Giữ nguyên hệ thống danh mục hiện có trên Vinhomes Resident.
- Sử dụng Rule-based cho các trường hợp rõ ràng.
- Sử dụng LLM để phân tích nội dung tự do, phát hiện sai lệch, tóm tắt và đề xuất điều phối.
- Nhân viên CSKH kiểm tra và phê duyệt các đề xuất.
- Thực hiện thử nghiệm trên dữ liệu đã ẩn danh trước khi triển khai thực tế.

5. Kết luận: REVISE.

Bài toán có tiềm năng nhưng cần xác minh bottleneck, baseline và giá trị kinh doanh trước khi đầu tư xây dựng hệ thống AI hoàn chỉnh.

### Problem Selection – Final Decision

Bài toán được chọn: Vinhomes – AI Resident Complaint Intelligence Assistant.

Lý do lựa chọn:

1. Quy trình tiếp nhận phản ánh có đầu vào số hóa và quy trình nghiệp vụ tương đối rõ ràng.
2. Có thể xây dựng prototype bằng dữ liệu khiếu nại giả lập hoặc dữ liệu đã ẩn danh.
3. Có thể kết hợp Rule-based và LLM để giải quyết các trường hợp có độ phức tạp khác nhau.
4. Có các chỉ số đo lường được như thời gian xử lý, tỷ lệ phát hiện sai lệch danh mục và tỷ lệ điều phối sai.
5. Ranh giới AI rõ ràng: AI hỗ trợ phân tích và đề xuất, nhân viên CSKH chịu trách nhiệm phê duyệt.

Kết quả đánh giá: REVISE.

Điều kiện trước khi triển khai:
- Khảo sát quy trình thực tế với nhân viên CSKH.
- Xác định baseline vận hành.
- Kiểm tra khả năng sử dụng dữ liệu.
- So sánh hiệu quả giữa Rule-based và Rule-based kết hợp LLM.

### Revised Problem Card – Vinhomes V2
### QUICK PROBLEM CARD #2  VINHOMES (REVISED V2)

Bài toán (1 câu): Ứng dụng AI để phân tích nội dung mô tả tự do, phát hiện sai lệch giữa danh mục khiếu nại cư dân lựa chọn và vấn đề thực tế, đồng thời hỗ trợ tóm tắt và đề xuất điều phối chính xác hơn.

Công ty thành viên: [ ] VinFast  [ ] Xanh SM  [x] Vinhomes  [ ] Vinmec

Ai đang đau (Actor)? Nhân viên chăm sóc khách hàng (CSKH) và nhân viên điều phối thuộc ban quản lý khu đô thị Vinhomes.

Workflow thủ công hiện tại (5 bước):

1. Cư dân chọn danh mục phản ánh có sẵn trên Vinhomes Resident.
2. Cư dân nhập nội dung mô tả chi tiết sự cố.
3. Nhân viên CSKH đọc nội dung và đối chiếu với danh mục đã chọn.
4. Nhân viên xác minh vấn đề, xác định bộ phận phụ trách và chuyển yêu cầu.
5. Bộ phận liên quan xử lý, CSKH theo dõi và phản hồi cư dân.

Bước nào tốn thời gian/lỗi nhất? Bước 3–4: Đọc và phân tích nội dung tự do, phát hiện trường hợp chọn sai danh mục, phản ánh nhiều vấn đề hoặc thiếu thông tin để xác định bộ phận phụ trách.

*Baseline thời gian và tỷ lệ sai lệch cần được khảo sát thực tế.*

AI có thể nhảy vào hỗ trợ ở bước nào? Bước 3–4:

- LLM phân tích ngữ nghĩa nội dung mô tả tự do.
- Đối chiếu nội dung với danh mục cư dân đã chọn.
- Phát hiện sự không nhất quán, nhiều vấn đề hoặc thông tin còn thiếu.
- Tóm tắt phản ánh và trích xuất thông tin quan trọng.
- Đề xuất bộ phận xử lý và mức độ ưu tiên.
- Rule-based xử lý các trường hợp có quy tắc rõ ràng và cảnh báo an toàn.

Đo thành công bằng gì (Metric có số)?

1. Tỷ lệ phát hiện đúng các trường hợp sai lệch danh mục ≥ 90% trên bộ dữ liệu kiểm thử.
2. Giảm ít nhất 30% thời gian CSKH đọc và xử lý thông tin ban đầu so với baseline.
3. Giảm ít nhất 30% tỷ lệ yêu cầu phải chuyển lại bộ phận so với baseline.
4. Recall nhận diện các tình huống khẩn cấp ≥ 99% trên bộ kiểm thử chuyên biệt.
5. Đo chi phí xử lý trung bình trên mỗi phản ánh trước và sau thử nghiệm.

*Các con số là mục tiêu thử nghiệm đề xuất, chưa phải kết quả thực tế.*

Quick Architecture: [ ] No AI  [x] Rule  [x] LLM  [ ] Agent

Operational Boundary:

- AI không thay đổi danh mục do cư dân lựa chọn hoặc tự động chuyển yêu cầu chính thức.
- AI chỉ phân tích, phát hiện sai lệch và đề xuất xử lý.
- Nhân viên CSKH xác nhận các đề xuất trước khi điều phối.
- Các dấu hiệu nguy hiểm phải kích hoạt quy trình khẩn cấp đã được phê duyệt, không phụ thuộc vào LLM.
- Dữ liệu cá nhân cư dân phải được bảo vệ và xử lý theo quy định pháp luật.


