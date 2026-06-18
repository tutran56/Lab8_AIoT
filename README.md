```html
<div align="center">

<h1>Lab 8 v3 - LLM Reasoning & Context-aware Decision for AIoT</h1>

<p>
  <b>AIoT Deployment Pipeline</b><br>
  Sensor only → AI Models → Local/Mock LLM → Safety Gate → Final Decision
</p>

<p>
  <img src="https://img.shields.io/badge/Python-3.12-blue" />
  <img src="https://img.shields.io/badge/FastAPI-Backend-green" />
  <img src="https://img.shields.io/badge/Ollama-Local%20LLM-purple" />
  <img src="https://img.shields.io/badge/Model-qwen3:0.6b-orange" />
  <img src="https://img.shields.io/badge/Mode-Mock%20%7C%20Local-lightgrey" />
</p>

</div>

<hr>

<h2>1. Giới thiệu</h2>

<p>
Lab 8 v3 mô phỏng một hệ thống <b>AIoT có khả năng reasoning theo ngữ cảnh</b>.
Hệ thống không chỉ đọc dữ liệu cảm biến thô, mà còn kết hợp kết quả từ các model AI trước đó
như anomaly detection, forecasting, motion/camera metadata và vision AI.
Sau đó, LLM sẽ đọc toàn bộ <b>context packet</b>, trả về JSON decision và đi qua
<b>safety gate</b> trước khi hiển thị quyết định cuối cùng trên dashboard.
</p>

<p>
Mục tiêu chính của lab là so sánh trực tiếp 3 tầng xử lý:
</p>

<ol>
  <li><b>Sensor only:</b> chỉ đọc cảm biến và so sánh theo ngưỡng.</li>
  <li><b>Sensor + AI models:</b> dùng thêm anomaly, forecasting, motion và vision evidence.</li>
  <li><b>Sensor + AI models + LLM:</b> LLM tổng hợp ngữ cảnh, giải thích tình huống và đề xuất hành động an toàn.</li>
</ol>

<hr>

<h2>2. Kiến trúc hệ thống</h2>

<pre>
Sensor Data
   ↓
AI Models Evidence
   ↓
Context Packet
   ↓
LLM Reasoning
   ↓
JSON Validation
   ↓
Safety Gate
   ↓
Dashboard + Logs
</pre>

<table>
  <thead>
    <tr>
      <th>Thành phần</th>
      <th>Vai trò</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Sensor Simulator</td>
      <td>Tạo dữ liệu cảm biến theo timeline hoặc do người dùng chỉnh bằng slider.</td>
    </tr>
    <tr>
      <td>AI Models</td>
      <td>Mô phỏng evidence từ các lab trước: anomaly, forecasting, motion, vision.</td>
    </tr>
    <tr>
      <td>LLM Reasoning</td>
      <td>Đọc context packet, giải thích tình huống và đề xuất hành động.</td>
    </tr>
    <tr>
      <td>JSON Validation</td>
      <td>Kiểm tra output của LLM có đúng schema không.</td>
    </tr>
    <tr>
      <td>Safety Gate</td>
      <td>Chặn điều khiển nguy hiểm, yêu cầu human review nếu cần.</td>
    </tr>
    <tr>
      <td>Dashboard</td>
      <td>Hiển thị live sensor, so sánh 3 tầng, thời gian suy luận và quyết định cuối.</td>
    </tr>
  </tbody>
</table>

<hr>

<h2>3. Các kịch bản có sẵn</h2>

<table>
  <thead>
    <tr>
      <th>Kịch bản</th>
      <th>Mục tiêu quan sát</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Smart classroom</td>
      <td>CO2 tăng nhanh, phòng đông người, quạt tắt, cửa sổ đóng.</td>
    </tr>
    <tr>
      <td>Fire alarm conflict</td>
      <td>Vision nghi cháy nhưng smoke/gas bình thường, có thể do projector.</td>
    </tr>
    <tr>
      <td>Fall ambiguity</td>
      <td>Phân biệt người bị ngã thật hay chỉ cúi nhặt đồ.</td>
    </tr>
    <tr>
      <td>PPE danger zone</td>
      <td>Người vào vùng nguy hiểm nhưng thiếu thiết bị bảo hộ.</td>
    </tr>
    <tr>
      <td>Greenhouse leaf disease</td>
      <td>Lá nghi bệnh, độ ẩm cao, cần kiểm tra thêm trước khi kết luận.</td>
    </tr>
  </tbody>
</table>

<hr>

<h2>4. Cài đặt môi trường</h2>

<h3>4.1. Tạo virtual environment</h3>

<pre><code>python3.12 -m venv .venv
source .venv/bin/activate</code></pre>

<h3>4.2. Cài thư viện</h3>

<pre><code>python -m pip install --upgrade pip
pip install -r requirements.txt</code></pre>

<h3>4.3. Chạy smoke test</h3>

<pre><code>python run_lab8_demo.py</code></pre>

<p>Kết quả đúng:</p>

<pre><code>LOCAL_PIPELINE_TEST_PASS</code></pre>

<hr>

<h2>5. Chạy dashboard</h2>

<pre><code>source .venv/bin/activate
uvicorn app:app --reload --host 127.0.0.1 --port 8000</code></pre>

<p>Mở trình duyệt tại:</p>

<pre><code>http://127.0.0.1:8000/</code></pre>

<p>API docs:</p>

<pre><code>http://127.0.0.1:8000/docs</code></pre>

<hr>

<h2>6. Chạy Local LLM bằng Ollama</h2>

<p>
Lab có thể chạy bằng <b>mock mode</b> mà không cần model thật.
Tuy nhiên, trong bài này em đã thử chạy local LLM bằng Ollama với model nhẹ
<b>qwen3:0.6b</b>, phù hợp hơn với MacBook Pro 2015.
</p>

<h3>6.1. Kiểm tra Ollama server</h3>

<pre><code>curl http://localhost:11434/api/tags</code></pre>

<p>Kết quả mong đợi:</p>

<pre><code>{"models":[...]}</code></pre>

<h3>6.2. Tải model</h3>

<pre><code>ollama pull qwen3:0.6b</code></pre>

<h3>6.3. Chạy thử model</h3>

<pre><code>ollama run qwen3:0.6b --think=false</code></pre>

<h3>6.4. Chạy dashboard với local LLM</h3>

<pre><code>source .venv/bin/activate
export OLLAMA_MODEL=qwen3:0.6b
uvicorn app:app --reload --host 127.0.0.1 --port 8000</code></pre>

<p>
Trên dashboard, chọn mode:
</p>

<pre><code>local - Ollama</code></pre>

<hr>

<h2>7. Context Packet</h2>

<p>
LLM không nhận một câu hỏi tự do, mà nhận một <b>context packet</b> có cấu trúc.
Context packet gồm dữ liệu cảm biến, evidence từ các lab trước, safety rules và output schema.
</p>

<pre><code>{
  "telemetry": {
    "co2_ppm": 1280,
    "temperature_c": 29.6,
    "humidity_percent": 67,
    "person_count": 28,
    "fan": "OFF",
    "window": "CLOSED"
  },
  "evidence_from_previous_labs": {
    "lab3_anomaly_detection": {},
    "lab4_forecasting": {},
    "lab6_camera_motion_metadata": {},
    "lab7_vision_ai": {}
  },
  "safety_rules": [],
  "output_schema": {}
}</code></pre>

<hr>

<h2>8. Output JSON của LLM</h2>

<p>
LLM phải trả về JSON đúng schema để backend có thể parse, validate, ghi log và đưa qua safety gate.
</p>

<pre><code>{
  "situation_summary": "Phòng học có CO2 cao và xu hướng tiếp tục tăng.",
  "risk_level": "HIGH",
  "recommended_action": "Khuyến nghị tăng thông gió và tiếp tục theo dõi CO2.",
  "control_allowed": false,
  "need_human_review": true,
  "blocked_reason": "Lab mode chặn điều khiển trực tiếp actuator.",
  "evidence_used": [
    "telemetry",
    "lab3_anomaly_detection",
    "lab4_forecasting",
    "lab7_vision_ai",
    "safety_rules"
  ]
}</code></pre>

<hr>

<h2>9. Safety Gate</h2>

<p>
Safety gate là lớp kiểm tra cuối cùng sau khi LLM trả kết quả.
Trong lab mode, hệ thống không cho phép LLM điều khiển actuator trực tiếp.
LLM chỉ đóng vai trò hỗ trợ ra quyết định.
</p>

<ul>
  <li>Nếu confidence thấp → yêu cầu human review.</li>
  <li>Nếu evidence mâu thuẫn → yêu cầu human review.</li>
  <li>Nếu LLM đề xuất điều khiển trực tiếp → safety gate chặn.</li>
  <li>Nếu lab mode đang bật → control_allowed luôn là false.</li>
</ul>

<hr>

<h2>10. Dashboard mới</h2>

<p>
Giao diện dashboard đã được chỉnh để hiển thị rõ hơn quá trình suy luận và quyết định:
</p>

<ul>
  <li>Live telemetry timeline.</li>
  <li>Sensor control panel.</li>
  <li>So sánh 3 tầng: Sensor only, AI models, LLM reasoning.</li>
  <li>Thời gian suy luận LLM.</li>
  <li>Tổng thời gian request từ trình duyệt.</li>
  <li>Validation status.</li>
  <li>Final decision sau safety gate.</li>
  <li>Raw LLM output, context packet và full compare JSON.</li>
</ul>

<p>Ảnh minh họa dashboard:</p>

<pre><code>./screenshots/dashboard_compare_three_levels.png
./screenshots/final_decision_local_ollama.png</code></pre>

<hr>

<h2>11. File log đầu ra</h2>

<p>
Sau khi chạy hệ thống, các log được lưu trong thư mục <code>outputs/</code>.
</p>

<table>
  <thead>
    <tr>
      <th>File</th>
      <th>Ý nghĩa</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>telemetry_timeseries.csv</td>
      <td>Lưu lịch sử dữ liệu cảm biến theo thời gian.</td>
    </tr>
    <tr>
      <td>context_packet_log.jsonl</td>
      <td>Lưu context packet được gửi cho LLM.</td>
    </tr>
    <tr>
      <td>comparison_log.csv</td>
      <td>Lưu kết quả so sánh 3 tầng.</td>
    </tr>
    <tr>
      <td>llm_decision_log.csv</td>
      <td>Lưu thông tin quyết định của LLM.</td>
    </tr>
    <tr>
      <td>safety_audit_log.csv</td>
      <td>Lưu kết quả kiểm tra của safety gate.</td>
    </tr>
    <tr>
      <td>latency_report.csv</td>
      <td>Lưu thời gian suy luận và phản hồi.</td>
    </tr>
  </tbody>
</table>

<hr>

<h2>12. Kết quả đạt được</h2>

<ul>
  <li>Chạy được FastAPI backend và dashboard tại <code>127.0.0.1:8000</code>.</li>
  <li>Chạy được smoke test với kết quả <code>LOCAL_PIPELINE_TEST_PASS</code>.</li>
  <li>Chạy được live sensor timeline và manual sensor control.</li>
  <li>So sánh được 3 tầng xử lý.</li>
  <li>Tích hợp được local LLM bằng Ollama với model <code>qwen3:0.6b</code>.</li>
  <li>Hiển thị được thời gian suy luận LLM và quyết định cuối.</li>
  <li>Ghi được các file log trong thư mục <code>outputs/</code>.</li>
</ul>

<hr>

<h2>13. Kết luận</h2>

<p>
Lab 8 giúp em hiểu rằng LLM trong hệ thống AIoT không thay thế sensor hoặc các model AI chuyên biệt.
Sensor cung cấp dữ liệu thô, các AI models tạo bằng chứng như anomaly score, forecast trend và vision event.
LLM đóng vai trò reasoning layer để tổng hợp bằng chứng, hiểu ngữ cảnh, giải thích tình huống và đề xuất hành động.
Sau đó, safety gate kiểm tra kết quả để đảm bảo hệ thống không đưa ra hành động nguy hiểm hoặc thiếu kiểm soát.
</p>

<p>
Pipeline cuối cùng của hệ thống là:
</p>

<pre><code>Sensor → AI Models → Context Packet → LLM JSON Output → Validation → Safety Gate → Final Decision</code></pre>
```
