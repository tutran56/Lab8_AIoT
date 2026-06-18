# Lab 8 v3 - LLM Reasoning cho hệ thống AIoT

Bài lab này mô phỏng một hệ thống AIoT có khả năng ra quyết định theo ngữ cảnh.

Thay vì chỉ đọc cảm biến rồi cảnh báo theo ngưỡng, hệ thống sẽ kết hợp:

- Dữ liệu cảm biến
- Kết quả từ các model AI
- LLM để giải thích tình huống
- Safety gate để kiểm tra an toàn trước khi đưa ra quyết định

Mục tiêu là hiểu vai trò của LLM trong AIoT: LLM không thay thế cảm biến hoặc các model AI, mà dùng các bằng chứng đó để suy luận, giải thích và đề xuất hành động phù hợp.

## Công nghệ sử dụng

- Python
- FastAPI
- Uvicorn
- HTML, CSS, JavaScript
- Pydantic
- Ollama
- Local LLM: qwen3:0.6b
- JSON schema
- CSV / JSONL logs

## Kiến trúc hệ thống

```text
Sensor Data
    ↓
AI Models
    ↓
Context Packet
    ↓
LLM Reasoning
    ↓
JSON Validation
    ↓
Safety Gate
    ↓
Final Decision
