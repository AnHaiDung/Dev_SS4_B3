# Báo Cáo Thực Hành: Cấu Hình Xác Thực SSH và Đẩy Dự Án Lên GitHub

- **Học viên:** An Hải Dũng
- **Email:** anhaidung2k6@gmail.com
- **Tài khoản GitHub:** AnHaiDung
- **Bài tập:** Session 04 - Bài 3 (homework/session_04/ex3/)

---

## 1. Mục tiêu
- Khởi tạo cặp khóa SSH bảo mật sử dụng thuật toán hiện đại **Ed25519** để phục vụ xác thực an toàn không cần mật khẩu với GitHub.
- Cấu hình liên kết an toàn giữa kho lưu trữ cục bộ (Local Repository) với máy chủ GitHub qua giao thức **SSH**.
- Đẩy (push) mã nguồn và lịch sử commit lên GitHub thành công.
- Tuân thủ nguyên tắc bảo mật thông tin (không chia sẻ hoặc nộp khóa bí mật Private Key).

---

## 2. Quá trình thực hiện chi tiết

### Bước 1: Khởi tạo cặp khóa SSH với thuật toán Ed25519
Sử dụng công cụ `ssh-keygen` tích hợp sẵn trong hệ thống để tạo cặp khóa:
```bash
ssh-keygen -t ed25519 -C "anhaidung2k6@gmail.com"
```

**Giải thích tham số:**
- `-t ed25519`: Chỉ định sử dụng thuật toán khóa mã hóa công khai Ed25519 (Elliptic Curve25519) - an toàn hơn, tốc độ ký/xác thực nhanh hơn và độ dài khóa gọn hơn nhiều so với RSA cổ điển.
- `-C "anhaidung2k6@gmail.com"`: Gắn nhãn nhận diện (comment) vào khóa, giúp phân biệt giữa các máy/tài khoản.

**Kết quả sinh khóa tại thư mục `~/.ssh/`:**
- `id_ed25519`: Khóa riêng tư (**Private Key**) - Lưu trữ bí mật trên máy cá nhân, không chia sẻ với bất kỳ ai.
- `id_ed25519.pub`: Khóa công khai (**Public Key**) - Dùng để cấu hình trên GitHub.

---

### Bước 2: Thêm SSH Public Key vào tài khoản GitHub
1. Lấy nội dung của khóa công khai (Public Key):
   - Trên PowerShell / Command Prompt:
     ```powershell
     Get-Content $HOME\.ssh\id_ed25519.pub
     # Hoặc copy trực tiếp vào clipboard:
     clip < $HOME\.ssh\id_ed25519.pub
     ```
2. Truy cập GitHub:
   - Vào **Settings** -> mục **SSH and GPG keys** -> chọn **New SSH key**.
   - **Title:** Đặt tên gợi nhớ máy làm việc (ví dụ: `Laptop-Workstation-Ed25519`).
   - **Key type:** Chọn `Authentication Key`.
   - **Key:** Dán toàn bộ nội dung từ tệp `id_ed25519.pub` (bắt đầu bằng `ssh-ed25519 AAAAC3...`).
   - Nhấn **Add SSH key** để lưu.

---

### Bước 3: Kiểm tra xác thực SSH tới máy chủ GitHub
Chạy lệnh kiểm tra kết nối từ terminal:
```bash
ssh -T git@github.com
```

**Kết quả mong đợi:**
```text
Hi AnHaiDung! You've successfully authenticated, but GitHub does not provide shell access.
```
*(Nếu nhận được thông báo trên, máy cục bộ đã xác thực danh tính với GitHub thành công qua khóa Ed25519).*

---

### Bước 4: Khởi tạo và thiết lập Git Repository cục bộ
Tại thư mục dự án cục bộ:
```bash
# Khởi tạo repository với nhánh chính mặc định là main
git init -b main

# Thêm tất cả các tệp vào staging area
git add .

# Tạo commit đầu tiên
git commit -m "feat: complete exercise 3 - configure SSH authentication and push to GitHub"
```

---

### Bước 5: Cấu hình liên kết Remote Repository qua SSH
Liên kết kho lưu trữ cục bộ với remote repository trên GitHub sử dụng định dạng URL giao thức SSH:
```bash
git remote add origin git@github.com:AnHaiDung/<ten-repository>.git
```
*(Lưu ý: Thay thế `<ten-repository>` bằng tên repository bạn đã tạo trên GitHub).*

Kiểm tra cấu hình remote URL:
```bash
git remote -v
```

**Kết quả mong đợi:**
```text
origin  git@github.com:AnHaiDung/<ten-repository>.git (fetch)
origin  git@github.com:AnHaiDung/<ten-repository>.git (push)
```
> URL có dạng `git@github.com:...` chứng minh giao thức SSH đã được áp dụng, thay vì giao thức HTTPS `https://github.com/...`.

---

### Bước 6: Đẩy (Push) mã nguồn lên GitHub
Thực hiện push mã nguồn lên nhánh `main` của remote `origin`:
```bash
git push -u origin main
```

**Kết quả mong đợi:**
```text
Enumerating objects: ... done.
Counting objects: 100% ... done.
Writing objects: 100% ... done.
Total ... (delta 0), reused 0 (delta 0)
To github.com:AnHaiDung/<ten-repository>.git
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```

---

## 3. Thông tin kho lưu trữ (Repository URL)
- **Đường dẫn Remote SSH:** `git@github.com:AnHaiDung/<ten-repository>.git`
- **Đường dẫn Web GitHub:** `https://github.com/AnHaiDung/<ten-repository>`

*(Học viên chỉ cần cập nhật `<ten-repository>` tương ứng với repo thực tế đã tạo).*

---

## 4. Tổng kết & Đánh giá bảo mật
- Đã tạo thành công cặp khóa mã hóa Ed25519 với độ an toàn cao.
- Toàn bộ quá trình push/pull diễn ra tự động thông qua khóa bất đối xứng SSH mà không cần phải nhập thông tin đăng nhập thủ công hay lưu token dạng plaintext.
- Tệp khóa bí mật `id_ed25519` được giữ tuyệt đối an toàn trên máy cục bộ và không được đẩy lên kho lưu trữ.
