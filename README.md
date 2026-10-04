# BÁO CÁO THỰC HÀNH - BÀI 4
## CHỦ ĐỀ: QUẢN LÝ TỆP TIN BỎ QUA (.GITIGNORE) VÀ SỬA ĐỔI LỊCH SỬ COMMIT (AMEND)

- **Môn học**: IT209 - Quản lý Phiên bản và Hợp tác Mã nguồn
- **Học viên**: Trần Trí Dương (`duongtran1702`)
- **Email**: `duong170226@gmail.com`
- **Đường dẫn bài nộp trên GitHub**: `homework/session_04/ex4/`

---

## 1. MỤC TIÊU BÀI TẬP
1. Thiết lập tệp tin ẩn `.gitignore` nhằm ngăn chặn việc đẩy các tệp tin chứa dữ liệu nhạy cảm (mật khẩu, khóa API, token) hoặc các tệp tin rác của hệ điều hành lên kho mã nguồn từ xa.
2. Nắm vững lệnh `git rm --cached` để gỡ bỏ tệp tin đã vô tình được thêm vào bộ nhớ đệm (index / staging area) hoặc đã commit trước đó ra khỏi sự theo dõi của Git mà **không làm mất tệp tin vật lý trên ổ đĩa**.
3. Sử dụng tùy chọn `git commit --amend` để gộp các thay đổi khắc phục và sửa đổi thông điệp commit gần nhất một cách sạch sẽ, không tạo thêm các commit sửa lỗi thừa thãi.

---

## 2. BỐI CẢNH VÀ NGUYÊN TẮC GIẢI QUYẾT

### 2.1. Bối cảnh
Trong quá trình phát triển mã nguồn ứng dụng (`app.js`), học viên vô tình đưa file chứa thông tin đăng nhập và khóa bí mật `credentials.txt` vào commit gần nhất trên Git:
```bash
git commit -m "feat(ex4): Khoi tao app.js va nham file credentials.txt"
```

### 2.2. Ràng buộc quan trọng
- **Tuyệt đối không xóa vật lý tệp tin trên hệ điều hành**: Tệp tin `credentials.txt` vẫn cần thiết cho việc cấu hình, kiểm thử ở môi trường cục bộ (local environment).
- Phải loại bỏ tệp khỏi sự theo dõi của Git và ngăn chặn Git theo dõi lại trong tương lai.

---

## 3. CÁC BƯỚC THỰC HIỆN CHI TIẾT

### Bước 1: Gỡ bỏ tệp khỏi cache theo dõi bằng `git rm --cached`
Lệnh `git rm` thông thường sẽ xóa cả tệp tin trên ổ đĩa. Tùy chọn `--cached` chỉ gỡ bỏ tệp tin khỏi **chỉ mục (Index / Staging Area)** của Git, giữ nguyên tệp tin vật lý trong thư mục làm việc (**Working Directory**):

```bash
git rm --cached homework/session_04/ex4/credentials.txt
```
**Kết quả terminal**:
```text
rm 'homework/session_04/ex4/credentials.txt'
```

Kiểm tra trạng thái `git status` ngay sau khi chạy lệnh:
```text
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	deleted:    homework/session_04/ex4/credentials.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	homework/session_04/ex4/credentials.txt
```
> **Giải thích**: Git đã lên lịch xóa tệp `credentials.txt` khỏi kho lưu trữ (`deleted`), đồng thời nhận diện tệp này là một tệp mới chưa được theo dõi (`Untracked`). Tệp vật lý vẫn nguyên vẹn trên đĩa.

---

### Bước 2: Cấu hình tệp `.gitignore` để bỏ qua vĩnh viễn
Tạo tệp `homework/session_04/ex4/.gitignore` với nội dung khai báo các tệp tin cần bỏ qua:
```gitignore
# Tệp tin cấu hình bỏ qua các dữ liệu nhạy cảm và bảo mật
credentials.txt

# Bỏ qua các file cấu hình môi trường và logs
*.env
*.env.local
*.log
```

Sau khi tạo `.gitignore`, kiểm tra `git status`:
```text
On branch main
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
	deleted:    homework/session_04/ex4/credentials.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	homework/session_04/ex4/.gitignore
```
> **Nhận xét**: Tệp `credentials.txt` đã hoàn toàn biến mất khỏi danh sách `Untracked files` vì đã được quy tắc trong `.gitignore` áp dụng.

---

### Bước 3: Đưa tệp `.gitignore` vào Staging Area
```bash
git add homework/session_04/ex4/.gitignore
```

---

### Bước 4: Sửa đổi commit gần nhất bằng tùy chọn `--amend`
Thay vì tạo một commit mới để sửa sai (vốn sẽ để lại lịch sử lộ tệp nhạy cảm ở commit trước đó), ta sử dụng `git commit --amend` để ghi đè trực tiếp lên commit bị lỗi:
```bash
git commit --amend -m "feat(ex4): Khoi tao ma nguon ung dung app.js va cau hinh .gitignore bao mat"
```

**Kết quả terminal**:
```text
[main 6864f58] feat(ex4): Khoi tao ma nguon ung dung app.js va cau hinh .gitignore bao mat
 Date: Sun Oct 4 23:05:28 2026 +0700
 2 files changed, 15 insertions(+)
 create mode 100644 homework/session_04/ex4/.gitignore
 create mode 100644 homework/session_04/ex4/app.js
```
> **Phân tích kết quả**:
> - Commit chỉ còn chứa 2 tệp an toàn: `.gitignore` và `app.js`.
> - Tệp `credentials.txt` đã được loại bỏ hoàn toàn khỏi cây commit lịch sử.
> - Thông điệp commit được thay đổi thành thông điệp chuẩn xác, sạch sẽ.

---

## 4. KẾT QUẢ KIỂM TRA THỰC TẾ

### 4.1. Kiểm tra trạng thái làm việc (`git status`)
```bash
git status
```
**Kết quả hiển thị**:
```text
On branch main
nothing to commit, working tree clean
```
- Tệp `credentials.txt` không còn xuất hiện trong danh sách theo dõi của Git (không ở dạng Staged hay Modified hay Untracked).

### 4.2. Kiểm tra commit gần nhất (`git log -n 1`)
```bash
git log -n 1
```
**Kết quả hiển thị**:
```text
commit 6864f58b0bd13a0621c709ef02a96b2b3feacf51
Author: duongtran1702 <duong170226@gmail.com>
Date:   Sun Oct 4 23:05:28 2026 +0700

    feat(ex4): Khoi tao ma nguon ung dung app.js va cau hinh .gitignore bao mat
```
- Thông điệp commit gần nhất đã được sửa đổi thành công.

### 4.3. Kiểm tra tính toàn vẹn của tệp vật lý trên ổ đĩa
```powershell
Get-ChildItem -Path "homework/session_04/ex4"
```
**Kết quả hiển thị**:
```text
Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----         10/4/2026  11:06 PM            177 .gitignore
-a----         10/4/2026  11:05 PM            224 app.js
-a----         10/4/2026  11:05 PM            211 credentials.txt
```
- Tệp `credentials.txt` vẫn tồn tại nguyên vẹn trên đĩa cứng cục bộ, đảm bảo đúng yêu cầu ràng buộc không xóa vật lý của đề bài.

---

## 5. BÀI HỌC KINH NGHIỆM VÀ QUY TẮC AN TOÀN
1. **Luôn cấu hình `.gitignore` ngay khi khởi tạo dự án**: Khai báo các file cấu hình bí mật (`.env`, `credentials.txt`, `config.json`) và thư mục build (`node_modules/`, `dist/`, `.venv/`) trước khi thực hiện commit đầu tiên.
2. **Sử dụng `git rm --cached`**: Đây là giải pháp tiêu chuẩn khi một file đã bị track lỡ dở, giúp ngừng theo dõi mà không làm gián đoạn môi trường làm việc local.
3. **Lưu ý với `git commit --amend`**:
   - Chỉ nên amend đối với các commit **chưa push** lên remote repository dùng chung.
   - Nếu commit đã push lên remote, việc amend sẽ thay đổi commit hash và yêu cầu `git push --force`, điều này có thể gây xung đột lịch sử cho các thành viên khác trong nhóm.
