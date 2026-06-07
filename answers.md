1. type="email" → Ô nhập text có kiểm tra định dạng email, tự báo lỗi nếu thiếu @ hoặc sai cú pháp → Dùng cho ô email đăng ký, đăng nhập, nhận hóa đơn
2. type="password" → Ô nhập text che ký tự bằng dấu chấm hoặc dấu sao → Dùng cho mật khẩu tài khoản, đổi mật khẩu, tạo tài khoản mới
3. type="tel" → Ô nhập text cho số điện thoại, không tự ép định dạng quá chặt → Dùng cho số điện thoại liên hệ, xác nhận đơn hàng, giao hàng
4. type="number" → Ô nhập số có nút tăng/giảm tùy trình duyệt → Dùng cho số lượng sản phẩm, số lượng vé, số người đặt hàng
5. type="search" → Ô tìm kiếm giống text nhưng tối ưu cho tra cứu → Dùng cho thanh tìm kiếm sản phẩm, thương hiệu, mã đơn hàng
6. type="url" → Ô nhập text kiểm tra có đúng định dạng đường dẫn web → Dùng cho link website cửa hàng, link mạng xã hội, link theo dõi đơn hàng
7. type="date" → Ô chọn ngày bằng lịch của trình duyệt → Dùng cho ngày sinh khách hàng, ngày nhận hàng, ngày đặt lịch giao
8. type="time" → Ô chọn giờ/phút theo giao diện của trình duyệt → Dùng cho chọn khung giờ giao hàng, giờ nhận hàng, lịch hẹn tư vấn
9. type="range" → Thanh trượt kéo để chọn giá trị trong một khoảng → Dùng cho bộ lọc giá sản phẩm, mức đánh giá, khoảng giá khuyến mãi
10. type="checkbox" → Ô chọn/bỏ chọn từng mục, có thể chọn nhiều → Dùng cho chọn phương thức thanh toán, đồng ý điều khoản, lọc thuộc tính sản phẩm

---

### Câu A2 — Validation Attributes (dự đoán)

1. `<input type="text" required value="">` → Khi user để trống và nhấn Submit: Trình duyệt sẽ chặn submit và hiển thị thông báo lỗi rằng trường này là bắt buộc (native required validation). Vì `required` buộc input phải có giá trị nên `checkValidity()` trả về false.

2. `<input type="email" value="abc">` → Khi user gõ "abc" và Submit: Trình duyệt sẽ chặn submit và thông báo lỗi format email không đúng (thiếu `@`/domain). `type="email"` khiến browser kiểm tra cú pháp email cơ bản.

3. `<input type="number" min="1" max="10" value="15">` → Khi user gõ 15 và Submit: Trình duyệt sẽ chặn submit vì giá trị vượt `max` (15 &gt; 10); `checkValidity()` trả về false và `validationMessage` báo giá trị lớn hơn `max`.

4. `<input type="text" pattern="[0-9]{10}" value="abc123">` → Khi user gõ "abc123" và Submit: Trình duyệt sẽ chặn submit vì chuỗi không khớp pattern (pattern yêu cầu đúng 10 chữ số). `pattern` dùng full-match nên bất kỳ ký tự chữ cái sẽ làm fail.

5. `<input type="password" minlength="8" value="123">` → Khi user gõ "123" và Submit: Trình duyệt sẽ chặn submit vì chiều dài nhỏ hơn `minlength`; `validationMessage` sẽ thông báo số ký tự tối thiểu.

Tất cả các trường trên đều sẽ ngăn form submit bởi browser-native validation (trừ khi form có attribute `novalidate` hoặc validation bị bypass bằng JS). Dự đoán: native validation sẽ ngăn submit và hiển thị thông báo tương ứng cho từng trường.

---

### Kế hoạch kiểm tra (thực tế)

Mình tạo file `validation_test.html` chứa 5 input trên trong một form. Khi nhấn Submit, một script sẽ ngăn submit mặc định, thu thập `checkValidity()` và `validationMessage` cho từng input, rồi hiển thị kết quả trên trang — điều này giúp chụp screenshot trạng thái validation dễ đọc và so sánh với dự đoán.

Kết quả kiểm tra thực tế sẽ được thêm dưới đây sau khi chạy test và chụp ảnh.

---

### Câu A3 — Accessibility

#### 1. Tại sao `<label for="email">` quan trọng cho người dùng screen reader?

`<label for="email">` kết nối nhãn với input thông qua `for + id`, tạo ra **accessible name** (tên truy cập) rõ ràng:

- **Với screen reader**: Khi người dùng focus vào ô input, trình đọc màn hình sẽ đọc nội dung của label (ví dụ: "Email"), giúp biết phải nhập gì vào ô đó.
- **Nếu thiếu label**: Screen reader chỉ nói "edit text" — không có ngữ cảnh, user sẽ bị lẫn lộn.
- **Lợi ích thêm**: Tăng vùng click (click vào label sẽ focus input), hỗ trợ autofill của password manager/browser, và tuân theo WCAG accessibility standards.

**Ví dụ đúng cách**:
```html
<label for="email">Email:</label>
<input type="email" id="email" name="email">
```

#### 2. Khi nào dùng `<fieldset>` + `<legend>`? Ví dụ cụ thể.

`<fieldset>` + `<legend>` dùng để **nhóm các form control liên quan** thành một tập có ngữ cảnh chung, đặc biệt quan trọng cho radio buttons và checkboxes:

- **Khi nào dùng**: Khi có nhóm lựa chọn có cùng chủ đề, ví dụ:
  - Nhóm lựa chọn phương thức thanh toán
  - Nhóm lựa chọn giới tính
  - Nhóm chọn địa chỉ giao hàng
  - Nhóm đồng ý/không đồng ý với các điều khoản khác nhau

- **Lợi ích**: Screen reader sẽ đọc `<legend>` trước, cung cấp ngữ cảnh cho nhóm lựa chọn, giảm nhầm lẫn.

**Ví dụ cụ thể cho e-commerce**:
```html
<fieldset>
    <legend>Phương thức thanh toán</legend>
    <label>
        <input type="radio" name="payment" value="card">
        Thẻ tín dụng
    </label>
    <label>
        <input type="radio" name="payment" value="bank">
        Chuyển khoản ngân hàng
    </label>
    <label>
        <input type="radio" name="payment" value="cash">
        Thanh toán khi nhận hàng
    </label>
</fieldset>
```

#### 3. `aria-label` dùng khi nào? Tại sao KHÔNG dùng khi đã có `<label>`?

**Khi dùng `aria-label`**:
- Khi control **không có nhãn hiển thị** (icon-only buttons, nút ×, nút menu icon).
- Khi cần cung cấp tên truy cập ngắn/mô tả cho phần tử không thể có `<label>` trực tiếp.

**Ví dụ hợp lệ**:
```html
<button type="submit" aria-label="Gửi đơn hàng">🛒</button>
<button aria-label="Đóng">×</button>
```

**Tại sao KHÔNG dùng `aria-label` khi đã có `<label>`**:
- `aria-label` **ghi đè** accessible name — nếu có cả `<label>` và `aria-label`, screen reader chỉ đọc `aria-label`, ẩn đi nhãn hiển thị.
- Tạo **mâu thuẫn**: Người nhìn thấy label nhưng screen reader nghe khác, dẫn đến nhầm lẫn.
- **Quy tắc thực tế**: Ưu tiên `<label>` (semantic, hiển thị, clickable) → sau đó mới dùng `aria-labelledby` (tham chiếu text sẵn có) → cuối cùng mới dùng `aria-label` (khi không có cách khác).

**Kết luận**: `<label for>` là giải pháp tốt nhất cho form controls; `aria-label` chỉ cho trường hợp không thể có nhãn hiển thị.

---

### Câu A4 — Media

#### 1. Thuộc tính `loading="lazy"` trên `<img>` — Cải thiện gì? Khi nào KHÔNG nên dùng?

**`loading="lazy"` là gì và cải thiện gì**:
- `loading="lazy"` yêu cầu trình duyệt chỉ tải ảnh khi ảnh sắp hiện lên trong viewport (lazy loading), không tải lên ngay lập tức.
- **Cải thiện**:
  - Giảm lưu lượng mạng ban đầu — chỉ tải ảnh người dùng thực sự nhìn thấy.
  - Tăng tốc độ tải trang (First Contentful Paint — FCP) vì ít request mạng.
  - Tiết kiệm bandwidth nếu user không scroll xuống xem hết trang.

**Ví dụ**:
```html
<img src="product.jpg" loading="lazy" alt="iPhone 16">
```

**Khi KHÔNG nên dùng `loading="lazy"`**:
- Ảnh **trên fold** (hero banner, ảnh chính ngay khi trang load) — cần tải ngay để không delay hiển thị.
- Ảnh **Above The Fold** (phần trên cùng trang mà user thấy ngay) — dùng `loading="eager"` hoặc bỏ attribute (mặc định là eager).
- Ảnh **LCP (Largest Contentful Paint)** — nếu ảnh là phần tử lớn nhất được tô màu, cần tải sớm để tối ưu LCP metric.
- Trường hợp **user chắc chắn sẽ scroll** (ví dụ: gallery sản phẩm) — vẫn có thể dùng, nhưng xem xét ảnh đầu tiên không nên lazy.

---

#### 2. Tại sao nên cung cấp nhiều `<source>` trong `<video>`? Liệt kê ít nhất 3 format video web phổ biến.

**Vì sao cung cấp nhiều `<source>`**:
- Các trình duyệt hỗ trợ **codec/container video khác nhau** — Safari không hỗ trợ VP9, Firefox trên Windows có thể không hỗ trợ H.265.
- Cung cấp nhiều format đảm bảo **tương thích đa trình duyệt** — browser sẽ tự chọn format đầu tiên nó hiểu.
- **Tối ưu hóa hiệu suất** — có thể cung cấp format khác nhau cho desktop vs mobile, hoặc bitrate khác nhau.

**3 format video web phổ biến**:

1. **MP4 (video/mp4)** — Codec H.264 (AVC)
   - Hỗ trợ tốt nhất trên tất cả trình duyệt, iOS, Android.
   - File size lớn hơn nhưng độ tương thích cao.
   - Ví dụ: `<source src="video.mp4" type="video/mp4">`

2. **WebM (video/webm)** — Codec VP8/VP9
   - Codec mã nguồn mở, file size nhỏ hơn H.264 với chất lượng tương tự.
   - Hỗ trợ tốt trên Chrome, Firefox, Edge (trên Windows/Mac/Linux).
   - Không hỗ trợ tốt trên Safari và iOS.
   - Ví dụ: `<source src="video.webm" type="video/webm">`

3. **Ogg Theora (video/ogg)** — Codec Theora
   - Codec cũ hơn, mã nguồn mở.
   - Hỗ trợ trên Firefox, Chrome, Opera (nhưng dần lỗi thời).
   - File size lớn, chất lượng thấp hơn H.264/VP9.

**Ví dụ cung cấp nhiều format**:
```html
<video width="640" height="360" controls>
    <source src="video.mp4" type="video/mp4">
    <source src="video.webm" type="video/webm">
    <source src="video.ogv" type="video/ogg">
    Trình duyệt của bạn không hỗ trợ video.
</video>
```

---

#### 3. Thuộc tính `alt` trên `<img>` — Dùng để làm gì? Viết alt tốt cho 3 trường hợp.

**Mục đích của `alt`**:
- **Khi ảnh không tải được** (lỗi mạng, file bị xóa) — hiển thị text thay thế.
- **Cho screen reader** — đọc alt text để người dùng khiếm thị hiểu nội dung ảnh.
- **SEO** — search engine dùng alt text để hiểu nội dung ảnh và index.
- **Cải thiện accessibility** — WCAG yêu cầu mỗi ảnh có alt text có ý nghĩa.

**3 ví dụ viết alt tốt**:

1. **Ảnh sản phẩm iPhone 16**:
   ```html
   <img src="iphone16.jpg" alt="iPhone 16 Pro màu titanium xám, góc nhìn trước-sau">
   ```
   - ✅ Mô tả cụ thể: tên sản phẩm, màu sắc, góc nhìn.
   - ✅ Giúp screen reader người dùng và SEO hiểu rõ sản phẩm.
   - ❌ Sai: `alt="iPhone"` quá chung chung, `alt="image"` vô dụng.

2. **Ảnh trang trí (decorative)**:
   ```html
   <img src="divider.png" alt="" aria-hidden="true">
   ```
   - ✅ Dùng `alt=""` (empty) để báo ảnh là trang trí, không có nội dung.
   - ✅ Thêm `aria-hidden="true"` để screen reader hoàn toàn bỏ qua.
   - ❌ Sai: `alt="divider"` hoặc `alt="decoration"` — screen reader sẽ đọc thừa.

3. **Ảnh biểu đồ doanh thu Q1/2026**:
   ```html
   <img src="revenue-q1-2026.png" alt="Biểu đồ doanh thu Q1 2026: tháng 1 đạt 2.5M, tháng 2 đạt 3.1M, tháng 3 đạt 2.8M">
   ```
   - ✅ Mô tả đầy đủ dữ liệu chính của biểu đồ (không cần mô tả chi tiết từng cột).
   - ✅ Hoặc có thể dùng `<figcaption>` + `alt` ngắn, rồi thêm bảng data bên dưới.
   - ❌ Sai: `alt="chart"` hoặc `alt="biểu đồ"` không đủ thông tin dữ liệu.

**Quy tắc viết alt tốt**:
- Ngắn gọn (< 125 ký tự nếu có thể), nhưng đủ mô tả nội dung ảnh.
- Tránh "ảnh của", "hình ảnh", "biểu tượng" — screen reader đã nói là ảnh rồi.
- Với biểu đồ/dữ liệu: nên cung cấp dữ liệu trong bảng hoặc text phía dưới ảnh.

---

### Câu A5 — So sánh `<figure>` vs `<img>`

#### Khi nào dùng Cách 1 (`<img>` alone)?

**`<img>` đơn lẻ** dùng khi ảnh là một phần của content flow mà **không cần caption hay mô tả kèm theo**. Ảnh tự giải thích bản thân nó hoặc ngữ cảnh đã rõ ràng.

**2 ví dụ thực tế**:

1. **Ảnh sản phẩm trong danh sách (product listing)**:
   ```html
   <div class="product-item">
       <img src="iphone-thumbnail.jpg" alt="iPhone 16 Pro Max 256GB">
       <h3>iPhone 16 Pro Max</h3>
       <p class="price">25.990.000đ</p>
   </div>
   ```
   - Lý do: Ảnh chỉ là thumbnail, thông tin chi tiết (tên, giá) đã ở phía dưới. Không cần `<figure>` vì nó không "self-contained".
   - Accessibility: alt text đủ để mô tả sản phẩm khi ảnh không tải.

2. **Avatar/hình đại diện người dùng**:
   ```html
   <div class="user-card">
       <img src="avatar.jpg" alt="Avatar của Nguyễn Văn Minh">
       <p>Nguyễn Văn Minh</p>
   </div>
   ```
   - Lý do: Ảnh chỉ là đại diện, không cần caption hay giải thích thêm.
   - Semantic: `<img>` đơn giản, không cần grouping vì không phải "figure" độc lập.

---

#### Khi nào dùng Cách 2 (`<figure>` + `<figcaption>`)?

**`<figure>` + `<figcaption>`** dùng khi ảnh là một **self-contained unit of content** có caption/mô tả **liên quan trực tiếp**. Nó tạo mối liên hệ semantic giữa ảnh và caption, cải thiện accessibility và SEO.

**2 ví dụ thực tế**:

1. **Sản phẩm chính trên trang chi tiết (product detail page)**:
   ```html
   <figure>
       <img src="iphone-hero.jpg" alt="iPhone 16 Pro Max 256GB Titan màu đen bóng">
       <figcaption>iPhone 16 Pro Max — 25.990.000đ | Bảo hành 12 tháng</figcaption>
   </figure>
   ```
   - Lý do: Ảnh chính và caption (giá, bảo hành) liên kết chặt với nhau — user cần biết cả ảnh + info này để hiểu sản phẩm.
   - Semantic: `<figure>` báo signal với search engine rằng đây là "main visual" của page.
   - Accessibility: Screen reader biết caption là mô tả chính của ảnh, không phải text riêng lẻ.

2. **Infographic/biểu đồ trong bài viết (article)**:
   ```html
   <article>
       <h2>Xu hướng bán hàng 2026</h2>
       <figure>
           <img src="sales-chart.png" alt="Biểu đồ doanh số tháng 1-12/2026: tăng trưởng từ 2.5M lên 8.7M">
           <figcaption>Hình 1: Doanh số bán hàng tăng liên tục qua các quý. Quý 4 đạt đỉnh 8.7M nhờ kampanye Black Friday.</figcaption>
       </figure>
       <p>Như biểu đồ trên cho thấy...</p>
   </article>
   ```
   - Lý do: Biểu đồ không đứng một mình — nó cần caption giải thích trend, lý do tăng/giảm.
   - Semantic: `<figure>` giúp search engine và screen reader hiểu đây là "illustration/chart" với mô tả chính thức.
   - Accessibility: Người dùng khiếm thị vừa nghe alt (dữ liệu cơ bản) vừa nghe figcaption (insight/phân tích).

---

#### So sánh nhanh

| Tiêu chí | `<img>` đơn | `<figure>` + `<figcaption>` |
|---------|---------|---------|
| **Khi nào dùng** | Ảnh là phần content flow, không cần caption | Ảnh self-contained + caption liên quan chặt |
| **Semantic** | Không tạo grouping | Báo signal: ảnh + caption là một unit |
| **Accessibility** | alt text là chủ yếu | alt + figcaption = hai lớp mô tả |
| **SEO** | Ảnh là content thường | Ảnh được đánh giá là "main figure" quan trọng |
| **Ví dụ** | Thumbnail, avatar, icon | Product hero, infographic, screenshot với giải thích |

**Quy tắc nhanh**: Nếu bạn muốn viết caption/mô tả dưới ảnh → dùng `<figure>`. Nếu ảnh tự nói lên được → dùng `<img>` đơn.
