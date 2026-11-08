# 03 — AI Log & Reflection

**Thành viên:** [Lê Quang Huy] | **Branch:** `lequanghuy`

**Công cụ AI đã dùng:**
* **Claude (Anthropic):** Đóng vai trò trợ lý kỹ thuật và cố vấn chiến lược chính: hỗ trợ phản biện bài toán, soạn thảo báo cáo, **trực tiếp viết toàn bộ mã nguồn file `prompt_prototype.py`**, thiết kế bộ kịch bản test ranh giới an toàn, và hỗ trợ đọc/phân tích sâu các tệp kết quả output sau khi chạy.
* **Gemini (API/Model):** Đóng vai trò là **đối tượng thử nghiệm (Target Model)** trong đoạn mã prototype để thực thi kiểm tra các kịch bản prompt ranh giới an toàn (Operational Boundary).

---

## 1. AI đã giúp tôi những gì
1. **Phản biện thẻ bài toán và vận hành:** Claude giúp soi chiếu quy trình xử lý phản ánh cư dân Vinhomes, gợi ý định lượng các con số vận hành giả định (N = 300 phản ánh/ngày, X = 15 chuyển nhầm bộ phận) và xây dựng ranh giới an toàn bắt buộc con người phê duyệt (HITL).
2. **Trực tiếp lập trình và cấu hình file `prompt_prototype.py`:** Do hạn chế về thời gian và kỹ năng lập trình Python cá nhân, tôi đã giao nhiệm vụ cho Claude viết trọn vẹn mã nguồn file `prompt_prototype.py`. Claude giúp thiết lập sẵn cấu trúc gọi API, cơ chế xử lý ngoại lệ (như thử lại khi gặp lỗi kết nối/hạn mức) và bộ 6 kịch bản kiểm thử (baseline và các ca tấn công đối kháng).
3. **Phân tích kết quả Output:** Sau khi chạy các kịch bản test, Claude hỗ trợ đọc các tệp kết quả (`00_test.txt` đến `05_test.txt`), chỉ ra các điểm AI giữ vững ranh giới an toàn cũng như các lỗi nội dung mà script kiểm tra tự động chưa bắt được (ví dụ: gợi ý sai phòng ban, ép phản hồi không phải khiếu nại thành vấn đề cần xử lý).
4. **Hỗ trợ thiết kế sơ đồ quy trình (`04-workflow-diagram.png`):** Claude gợi ý cấu trúc visual để thể hiện rõ 5 bước thực hiện thủ công, điểm bottleneck (bước 2 & 3: 7 phút) và nhánh phát sinh khi điều phối sai.

## 2. Tôi đã điều chỉnh, kiểm soát và vận hành AI như thế nào
1. **Đóng vai trò Product Owner (Chủ trì nghiệp vụ):** Tôi là người đưa ra bài toán thực tế (Vinhomes Resident), ấn định các con số giả định ($300$ phản ánh/ngày, $15\%$ chuyển nhầm), định nghĩa các quy tắc bất biến trong `SYSTEM_PROMPT` (thẻ `[DRAFT_ONLY]`, ca khẩn cấp không được hạ mức, bắt buộc HITL).
2. **Kiểm duyệt và định hướng kết quả:** 
   * Ban đầu Claude đề xuất giải pháp bắt cư dân chọn lại danh mục, tôi đã chủ động đính chính vì ứng dụng thực tế đã có sẵn tính năng này, yêu cầu AI tập trung vào việc đọc hiểu ngữ nghĩa và đề xuất điều phối.
   * Tôi trực tiếp kiểm tra tínhlogic của các đoạn code do Claude viết, yêu cầu tối ưu hóa và chỉnh sửa khi phát sinh lỗi thực tế trong quá trình gọi API.
3. **Quản lý mã nguồn cơ bản:** Tôi thực hiện các thao tác quản lý Git cơ bản, tạo branch `lequanghuy`, commit và đẩy code lên kho chứa chung theo đúng yêu cầu kỹ thuật của dự án.

## 3. AI đã sai hoặc suy đoán ở đâu
1. **Suy đoán về hiện trạng hệ thống:** Claude ban đầu có xu hướng thiết kế giải pháp như xây mới ứng dụng, tôi phải liên tục kéo phạm vi về đúng bản chất là bổ sung tính năng LLM hỗ trợ tầng trung gian cho hệ thống sẵn có của Vinhomes.
2. **Hạn chế của mô hình thử nghiệm (Gemini):** Khi chạy thử nghiệm các kịch bản test, mô hình đôi khi tự sinh ra các lỗi chất lượng (ví dụ: đưa ra lời cảm ơn đèn hành lang nhưng lại gợi ý chuyển cho phòng "Gửi xe", hoặc coi một câu prompt injection thành một vấn đề cần giải quyết). Điều này cho thấy kiểm tra tự động bằng code chỉ check được hình thức (JSON, thẻ bảo mật), còn kiểm duyệt nội dung bắt buộc phải có con người (Human-in-the-Loop).

## 4. Bài học rút ra
Việc giao toàn bộ phần viết mã nguồn `.py` cho Claude giúp tôi tiết kiệm được rất nhiều thời gian và tập trung hoàn toàn vào tư duy thiết kế sản phẩm, nghiệp vụ vận hành và kiểm soát rủi ro. Tuy nhiên, AI không thể thay thế con người trong việc thấu hiểu bối cảnh thực tế và kiểm duyệt chất lượng đầu ra. Sự kết hợp giữa tư duy định hướng của Product Owner và khả năng lập trình, phân tích của AI chính là chìa khóa để hoàn thành dự án một cách hiệu quả nhất.