### Câu C1 (10đ) — Debug Form

Lỗi 1: Dòng 1 — Thẻ `<form>` không có `action` và `method`, chưa xác định nơi gửi dữ liệu và best practice cho form đăng nhập/đặt hàng.
Sửa: `<form action="#" method="POST">`

Lỗi 2: Dòng 2 — Trường "Tên" không có `<label for="...">` và input cũng thiếu `id`, vi phạm accessibility.
Sửa: `<label for="name">Tên:</label> <input type="text" id="name" name="name" required placeholder="Nhập họ và tên">`

Lỗi 3: Dòng 4 — Input email chỉ có `placeholder`, thiếu `label`, thiếu `required`, và thiếu `name` để gửi dữ liệu.
Sửa: `<label for="email">Email:</label> <input type="email" id="email" name="email" required placeholder="Email của bạn">`

Lỗi 4: Dòng 6 — Hai trường mật khẩu đều thiếu `label`, thiếu `name`, và chưa có ràng buộc validation cơ bản như `required`/`minlength`.
Sửa: `<label for="password">Mật khẩu:</label> <input type="password" id="password" name="password" required minlength="8" placeholder="Mật khẩu">` và `<label for="confirm_password">Nhập lại mật khẩu:</label> <input type="password" id="confirm_password" name="confirm_password" required minlength="8" placeholder="Nhập lại mật khẩu">`

Lỗi 5: Dòng 9 — Trường Phone dùng `type="text"` với giá trị mặc định, không có `label`, không có `name`, và không có validation kiểm tra số điện thoại.
Sửa: `<label for="phone">Phone:</label> <input type="tel" id="phone" name="phone" pattern="[0-9]{10}" placeholder="0901234567" required>`

Lỗi 6: Dòng 11–14 — Thẻ `<select>` thiếu `label`, thiếu `name`, và các `<option>` không có `value` rõ ràng.
Sửa: `<label for="city">Tỉnh/Thành phố:</label> <select id="city" name="city" required><option value="">-- Chọn tỉnh/thành --</option><option value="hanoi">Hà Nội</option><option value="hcm">TP.HCM</option></select>`

Lỗi 7: Dòng 16–18 — Câu đồng ý điều khoản chỉ đặt trong `<label>` rỗng, không gắn với input checkbox nên người dùng không thể thao tác đúng, và thiếu control thực sự.
Sửa: `<label for="agree"><input type="checkbox" id="agree" name="agree" required> Tôi đồng ý điều khoản</label>`

Lỗi 8: Dòng 20 — Nút gửi dùng `<input type="submit">` thay vì `<button type="submit">`; dùng button linh hoạt hơn và dễ mở rộng nội dung/aria.
Sửa: `<button type="submit" aria-label="Gửi">Gửi</button>`

### Form đã sửa mẫu

```html
<form action="#" method="POST">
	<label for="name">Tên:</label>
	<input type="text" id="name" name="name" required placeholder="Nhập họ và tên">

	<label for="email">Email:</label>
	<input type="email" id="email" name="email" required placeholder="Email của bạn">

	<label for="password">Mật khẩu:</label>
	<input type="password" id="password" name="password" required minlength="8" placeholder="Mật khẩu">

	<label for="confirm_password">Nhập lại mật khẩu:</label>
	<input type="password" id="confirm_password" name="confirm_password" required minlength="8" placeholder="Nhập lại mật khẩu">

	<label for="phone">Phone:</label>
	<input type="tel" id="phone" name="phone" pattern="[0-9]{10}" placeholder="0901234567" required>

	<label for="city">Tỉnh/Thành phố:</label>
	<select id="city" name="city" required>
		<option value="">-- Chọn tỉnh/thành --</option>
		<option value="hanoi">Hà Nội</option>
		<option value="hcm">TP.HCM</option>
	</select>

	<label for="agree">
		<input type="checkbox" id="agree" name="agree" required>
		Tôi đồng ý điều khoản
	</label>

	<button type="submit" aria-label="Gửi">Gửi</button>
</form>
```

### Câu C2 (10đ) — Thiết kế chiến lược Validation

#### 1. Pattern regex cho CMND/CCCD và Số tài khoản

- **CMND/CCCD: đúng 12 chữ số**  
  Regex: `^[0-9]{12}$`

- **Số tài khoản: 10-15 chữ số**  
  Regex: `^[0-9]{10,15}$`

- **Email: bắt buộc, đúng format**  
  Dùng `type="email"` + `required`

- **PIN: đúng 6 chữ số, KHÔNG hiển thị**  
  Dùng `type="password"` + `inputmode="numeric"` + `pattern="^[0-9]{6}$"` + `required`

#### 2. HTML5 validation đủ an toàn cho ứng dụng ngân hàng chưa? Tại sao?

**Chưa đủ an toàn.** HTML5 validation chỉ là lớp kiểm tra đầu vào ở phía trình duyệt để cải thiện UX. Nó có thể bị vô hiệu hóa bằng DevTools, request thủ công, script, hoặc gửi dữ liệu trực tiếp tới API mà không đi qua giao diện form. Ứng dụng ngân hàng bắt buộc phải có validation và kiểm tra bảo mật ở **backend**.

#### 3. Ba loại validation mà HTML5 không thể làm được, phải dùng JavaScript

1. **Kiểm tra dữ liệu phụ thuộc lẫn nhau giữa 2 trường**  
   Ví dụ: xác nhận PIN phải khớp PIN gốc, ngày kết thúc phải sau ngày bắt đầu.

2. **Kiểm tra theo logic động từ API/hệ thống**  
   Ví dụ: tên người dùng đã tồn tại chưa, tài khoản có bị khóa không, hạn mức giao dịch còn bao nhiêu.

3. **Kiểm tra phức tạp theo ngữ cảnh nghiệp vụ**  
   Ví dụ: số tiền chuyển phải nhỏ hơn hạn mức theo vai trò người dùng, OTP còn hiệu lực hay không.

#### 4. Hai rủi ro bảo mật nếu chỉ validate trên Frontend mà không validate Backend

1. **Bypass validation và gửi dữ liệu bẩn/độc hại**  
   Kẻ tấn công có thể gửi request trực tiếp với giá trị sai định dạng, dữ liệu giả mạo, hoặc payload cố tình gây lỗi.

2. **Injection và sửa dữ liệu trái phép**  
   Nếu backend tin vào dữ liệu frontend, hệ thống có thể bị SQL injection, XSS, hoặc thay đổi thông tin giao dịch sai quy tắc nghiệp vụ.

**Kết luận**: Frontend validation chỉ là lớp tiện ích cho người dùng. Với ngân hàng số, phải luôn validate lại ở backend, kết hợp whitelist, kiểm tra nghiệp vụ và logging/audit.
