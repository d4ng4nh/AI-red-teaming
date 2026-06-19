# **TOOL VÀ FRAMEWORK TRONG AI RED TEAMING**

## **1\. Các Tool Và Framework phổ biến**

| Tên Tool / Framework | Phân loại | Khi nào nên sử dụng? | Mục đích & phạm vi kiểm thử   |
| :---- | :---- | :---- | :---- |
| **NVIDIA garak** | Open-source CLI-based Scanner | Giai đoạn đầu của dự án; khi cần quét nhanh các lỗ hổng cơ bản của mô hình nền tảng trước khi triển khai sâu hơn. \-\> giống nmap trong Red Team truyền thống | Tự động hóa việc gửi hàng nghìn probe-payloads để phát hiện Prompt Injection, Jailbreak, Data Leakage và sinh nội dung độc hại. Bản đồ hóa kết quả trực tiếp theo OWASP Top 10 LLM. |
| **Microsoft PyRIT** | Open-source Python Automation Framework | Khi cần thực hiện các chiến dịch tấn công tùy biến phức tạp, Multi-turn attacks và kiểm thử các hệ thống AI có kết nối công cụ bên ngoài. | Đóng vai trò lớp điều phối. Sử dụng một LLM tấn công để tự động hóa việc lừa gạt, khai thác lỗ hổng của LLM phòng thủ thông qua các kỹ thuật như chuỗi tấn công tăng dần (Crescendo pattern) hoặc Jailbreak đa vòng. |
| **UK AISI Inspect** | Open-source Advanced Evaluation Suite | Khi cần đánh giá chuyên sâu mức độ an toàn của các Frontier Models về rủi ro nghiêm trọng hoặc tuân thủ các chuẩn an toàn quốc gia. | Kiểm thử khả năng tự trị nguy hiểm, xu hướng hỗ trợ các tác vụ độc hại cấp độ cao (như cyberattacks quy mô lớn, chế tạo vũ khí hóa học/sinh học) và đánh giá năng lực nhận thức rủi ro của AI. |
| **Tencent AI-Infra-Guard** | Open-source Full-stack Red Teaming Platform | Khi muốn quét toàn diện cả hệ sinh thái hạ tầng AI bao gồm các framework máy chủ (vLLM, Ollama), Agent Scan và MCP protocol scan. | Phát hiện các lỗ hổng CVE/GHSA của các thành phần hạ tầng AI, kiểm thử rủi ro mã độc chuỗi cung ứng, lỗi cấu hình lộ lọt file cấu hình của Agent và rủi ro từ giao thức kết nối vạn năng MCP. |
| **Mindgard / Repello ARTEMIS** | Commercial Enterprise Platforms | Trong môi trường doanh nghiệp lớn, tích hợp trực tiếp vào đường ống CI/CD dưới dạng Release Gates để kiểm thử liên tục trước khi đẩy code lên Production. | Cung cấp giải pháp trọn gói, giảm thiểu False Positives, tự động đánh giá các lớp RAG (Document Injection), thao túng lời gọi hàm của Agent (Tool-call manipulation) và xuất báo cáo tuân thủ đạo luật EU AI Act, NIST. |

## **3\. Phân tích chi tiết**

### **3.1. NVIDIA garak**

Garak hoạt động tương tự như công cụ Nmap trong bảo mật mạng truyền thống. Khi nhóm Red Team tiếp cận một mô hình LLM mới và cần một bức tranh tổng thể về các cấu hình an toàn mặc định của nhà phát triển, Garak là lựa chọn tối ưu nhất nhờ kho payload tĩnh vô cùng đồ sộ.  
**Mục đích:** Xác định nhanh xem mô hình có dễ bị tổn thương trước các kỹ thuật encode bypass (Base64, Rot13) hay các prompt injection kinh điển hay không. Garak giúp thiết lập một "baseline" an toàn tối thiểu.

### **3.2. Microsoft PyRIT**

Điểm yếu của các công cụ quét tĩnh như Garak là không thể mô phỏng tư duy của một hacker thực thụ khi đối đầu với các hệ thống AI có bộ lọc động (Dynamic Guardrails). PyRIT giải quyết bài toán này bằng cách áp dụng phương pháp tự động hóa tương tác dựa trên tác tử (Agent-based testing).  
**Mục đích:** Giả lập các cuộc tấn công phức tạp đòi hỏi phải "nuôi" ngữ cảnh. Ví dụ, kỹ thuật Crescendo yêu cầu hạ gục bộ lọc an toàn của AI qua 4-5 bước trò chuyện bằng cách chuyển đổi dần từ các chủ đề trung lập sang chủ đề độc hại độc lập. PyRIT điều phối việc này một cách tự động và ghi nhận toàn bộ lịch sử hội thoại để phân tích.

### **3.3. Tencent AI-Infra-Guard**

Một sai lầm phổ biến của các chuyên gia Red Team là chỉ tập trung tấn công vào tầng Prompt mà quên mất rằng AI chạy trên một hạ tầng phần mềm truyền thống. AI-Infra-Guard lấp đầy khoảng trống này bằng việc quét an ninh toàn diện cho hệ sinh thái hạ tầng.  
**Mục đích:** Quét và phát hiện hơn 1600 lỗi CVE đã biết trên các thư viện máy chủ LLM (như vLLM, LiteLLM), phát hiện mã độc nhúng trong chuỗi cung ứng, và kiểm thử bảo mật cho các cổng kết nối MCP nhằm ngăn chặn việc tin tặc chiếm quyền điều khiển hệ thống máy chủ thông qua lỗ hổng của AI Agent.

## **4\. Quy trình phối hợp các công cụ trong một chiến dịch thực tế**

1. **Bước 1 \- Threat Modeling:** Xác định ranh giới niềm tin (Trust Boundaries), luồng dữ liệu của hệ thống RAG và các quyền hạn (Permissions) mà AI Agent được ủy thác.  
2. **Bước 2 \- Static Probing:** Sử dụng **NVIDIA garak** để kiểm tra các lỗ hổng bảo mật bề mặt và cấu hình sai của mô hình cốt lõi.  
3. **Bước 3 \- Infrastructure Assessment:** Triển khai **AI-Infra-Guard** nhằm rà soát toàn bộ các lỗ hổng của server hosting mô hình và các lỗ hổng MCP kết nối API.  
4. **Bước 4 \- Dynamic AI Orchestration:** Vận hành **Microsoft PyRIT** để thực hiện các kịch bản Jailbreak tinh vi, Indirect Prompt Injection trong hệ thống RAG và tTool-calling manipulation.  
5. **Bước 5 \- Đánh giá rủi ro cận biên & Nghiệm thu:** Ứng dụng các bộ công cụ như **UK AISI Inspect** hoặc các nền tảng như **Mindgard** để kiểm thử các rủi ro hệ thống nghiêm trọng và đối chiếu với khung pháp lý tuân thủ (EU AI Act, NIST AI RMF).