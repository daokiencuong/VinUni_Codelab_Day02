# 03 — AI Log & Reflection

**Thành viên:** [Lê Quang Huy] | **Branch:** `huylq2002-art`

**Công cụ AI đã dùng:**
* **Claude (Anthropic):** Đóng vai trò cố vấn chiến lược, phản biện logic bài toán, soạn nháp báo cáo Deep-Dive và **trực tiếp tạo/vẽ sơ đồ quy trình hiện tại (`04-workflow-diagram.png`)** bám sát các thông số thời gian, điểm thắt nút (bottleneck) và handoff.
* **Gemini 2.5 Flash:** Đóng vai trò động cơ chạy thử nghiệm code nguyên mẫu (`prompt_prototype.py`). Sử dụng để kiểm thử prompt phân loại tự động, test các trường hợp tấn công đối kháng (Adversarial Test Input) nhằm đảm bảo ranh giới an toàn (Operational Boundary) và cơ chế Human-in-the-Loop (HITL).

**Bằng chứng:** Nằm trong các file đã lưu (`01-problem-scan.md`, `02-deep-dive-report.md`, `04-workflow-diagram.png`, `prompt_prototype.py`) cùng các file output kiểm thử ranh giới an toàn.

---

## 1. AI đã giúp tôi những gì
1. **Phản biện bài toán và vận hành:** Claude đóng vai trò phản biện chuyên sâu, chỉ ra các điểm cần định lượng rõ như: thời gian bottleneck (7 phút/lượt), khối lượng công việc ($N = 300$ phản ánh/ngày), tỷ lệ chuyển nhầm bộ phận ($X = 15\%$), và thiết lập ranh giới an toàn bắt buộc có con người phê duyệt (HITL).
2. **Tạo và tinh chỉnh sơ đồ quy trình (`04-workflow-diagram.png`):** Tôi đưa các thông số quy trình cho Claude để **tạo và vẽ trực quan sơ đồ workflow hiện tại**. Claude hỗ trợ xuất file ảnh sơ đồ thể hiện rõ 5 bước tuần tự, các điểm giao tiếp (Handoff), điểm nghẽn Bottleneck (bước 2 & 3), thời gian xử lý chuẩn (10 phút) và thời gian phát sinh khi điều phối sai (13 phút).
3. **Kiểm thử tự động hóa với Gemini 2.5 Flash:** Tôi tự viết script Python gọi API Gemini 2.5 Flash để chạy prompt nguyên mẫu. Gemini giúp xử lý ngôn ngữ tự nhiên của cư dân, đối chiếu danh mục, nhận diện các phản ánh đa vấn đề và xuất ra cấu trúc JSON đề xuất điều phối chuẩn xác.
4. **Stress-test ranh giới an toàn (Boundary Testing):** Phối hợp cùng Gemini 2.5 chạy các kịch bản tấn công prompt (Adversarial Test Input). Qua đó xác thực hệ thống luôn giữ vững ranh giới: cảnh báo ca khẩn cấp, từ chối tự động chuyển giao khi chưa có sự xác nhận của nhân viên CSKH.

## 2. AI đã sai hoặc suy đoán ở đâu
1. **Suy đoán về hiện trạng ứng dụng:** Ở giai đoạn đầu, Claude đề xuất "bắt cư dân chọn danh mục chi tiết trên app" như một giải pháp thay thế LLM. Tôi đã can thiệp và làm rõ: app Vinhomes Resident đã có sẵn tính năng chọn danh mục, vấn đề nằm ở chỗ cư dân chọn sai/mô tả gộp nhiều nội dung, do đó AI chỉ đóng vai trò trợ lý đọc hiểu và đề xuất (LLM Feature).
2. **Sai biệt chi tiết trình bày trên sơ đồ:** Bản vẽ ban đầu của Claude thiếu dòng ghi chú phạm vi (Scope Boundary). Tôi đã yêu cầu Claude bổ sung rõ chú thích chân trang: *Phạm vi chỉ tính từ lúc CSKH tiếp nhận đến khi điều phối cho bộ phận chuyên trách, chưa bao gồm khâu xử lý kỹ thuật và phản hồi cư dân*.
3. **Độ ổn định định dạng JSON của LLM:** Khi test prompt với Gemini 2.5 Flash, đôi khi mô hình tự thêm các ký tự markdown bao quanh chuỗi JSON. Tôi đã tự tinh chỉnh lại System Prompt để ép định dạng đầu ra thuần túy JSON (Raw Output).

## 3. Tôi đã điều chỉnh và kiểm soát AI như thế nào
1. **Làm chủ kỹ thuật và quản lý mã nguồn:** Tôi tự thực hiện toàn bộ các thao tác Git (tạo branch `lequanghuy`, kiểm tra `git status`, commit và push code lên remote repository) mà không cần AI hướng dẫn từng lệnh cơ bản.
2. **Quản trị số liệu giả định:** Các thông số vận hành ($300$ phản ánh/ngày, $15\%$ điều phối nhầm) do tôi tự tính toán dựa trên quy mô đại đô thị và ấn định cho AI tuân thủ xuyên suốt từ file báo cáo đến sơ đồ hình ảnh.
3. **Kiểm soát quy trình tạo ảnh của Claude:** Tôi đối chiếu kỹ file sơ đồ `04-workflow-diagram.png` do Claude tạo với nội dung mục 3.1 và 3.2 trong report, đảm bảo từng con số (1, 4, 3, 2, 3 phút) khớp chính xác 100%.

## 4. Bài học rút ra
Sự kết hợp giữa **Claude** (mạnh về phân tích cấu trúc, phản biện logic và tạo visual diagram) cùng **Gemini 2.5 Flash** (mạnh về thực thi prompt prototype, stress-test ranh giới) giúp tối ưu hóa đáng kể tốc độ hoàn thành dự án. Tuy nhiên, AI chỉ đóng vai trò tạo bản thảo và công cụ trực quan hóa, người làm dự án bắt buộc phải kiểm soát tính logic, đối chiếu số liệu thực tế và thiết lập ranh giới vận hành an toàn.