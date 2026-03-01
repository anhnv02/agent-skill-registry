---
description: Hướng dẫn sử dụng các Agent Skills chuyên dụng
---

Để kích hoạt và sử dụng hiệu quả các kỹ năng phân tích code, hãy thực hiện theo các quy tắc sau:

### 1. Phân loại tác vụ
Dựa trên yêu cầu của người dùng, hãy xác định Skill phù hợp nhất:

- **Khám phá & Tìm hiểu**: Dùng [code-explorer](file:///Users/anhnguyen/Projects/workspace/skills/code-explorer/SKILL.md) khi cần hiểu luồng code hiện có.
- **Thiết kế & Lập kế hoạch**: Dùng [code-architect](file:///Users/anhnguyen/Projects/workspace/skills/code-architect/SKILL.md) cho kiến trúc hệ thống.
- **Thiết kế Giao diện**: Dùng [frontend-designer](file:///Users/anhnguyen/Projects/workspace/skills/frontend-designer/SKILL.md) cho Web/UI chất lượng cao.
- **Kiểm tra & Soát lỗi**: Dùng [code-reviewer](file:///Users/anhnguyen/Projects/workspace/skills/code-reviewer/SKILL.md) cho file đơn lẻ hoặc [pr-reviewer](file:///Users/anhnguyen/Projects/workspace/skills/pr-reviewer/SKILL.md) cho toàn bộ Pull Request.

### 2. Cách kích hoạt
Khi nhận thấy yêu cầu thuộc một trong các nhóm trên:
1. Thông báo cho người dùng rằng bạn sẽ sử dụng skill tương ứng.
2. Đọc file `SKILL.md` trong thư mục của skill đó để nắm rõ quy trình (Analysis Approach, Core Process, hoặc Review Scope).
3. Thực hiện các bước theo hướng dẫn trong skill và trình bày kết quả theo đúng phần **Output Guidance**.

### 3. Ví dụ
- "Giải thích cho tôi luồng đăng ký user" -> Sử dụng **code-explorer**.
- "Tôi muốn thêm tính năng thanh toán bằng QR code" -> Sử dụng **code-architect**.
- "Review các thay đổi tôi vừa thực hiện" -> Sử dụng **code-reviewer**.
