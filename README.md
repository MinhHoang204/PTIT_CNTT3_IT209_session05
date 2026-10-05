# Bài tập Session 05 - Ex2: Interactive Rebase
## Các bước thực hiện
### Bước 1: Tạo 4 commit theo đúng kịch bản đề bài
Trước tiên, bạn cần tái hiện tình huống "lịch sử bẩn" như mô tả trong đề bài - tạo lần lượt 4 commit trên nhánh làm việc 
hiện tại:
```bash
# Commit 1: Tạo auth.js
echo "// auth module" > auth.js
git add auth.js
git commit -m "feat: khoi tao module auth"

# Commit 2: Sửa auth.js
echo "// fix typo" >> auth.js
git add auth.js
git commit -m "fix typo"

# Commit 3: Sửa auth.js
echo "// utility functions" >> auth.js
git add auth.js
git commit -m "adds utility functions"

# Commit 4: Tạo temp.txt
echo "debug temp" > temp.txt
git add temp.txt
git commit -m "add temp file for debug"
```

Kiểm tra trạng thái lịch sử trước khi rebase:
```bash
git log --oneline
```

Bạn sẽ thấy 4 commit theo thứ tự từ mới nhất xuống cũ nhất:
```text
<hash4> add temp file for debug
<hash3> adds utility functions
<hash2> fix typo
<hash1> feat: khoi tao module auth
```

### Bước 2: Chạy Interactive Rebase
```bash
git rebase -i HEAD~4
```

Lệnh này mở trình soạn thảo mặc định (thường là Vim hoặc Nano) hiển thị danh sách 4 commit theo thứ tự từ cũ đến mới - ngược 
với git log:

```text
pick <hash1> feat: khoi tao module auth
pick <hash2> fix typo
pick <hash3> adds utility functions
pick <hash4> add temp file for debug

# Rebase <hash0>..<hash4> onto <hash0> (4 commands)
#
# Commands:
# p, pick <commit> = use commit
# r, reword <commit> = use commit, but edit the commit message
# e, edit <commit> = use commit, but stop for amending
# s, squash <commit> = use commit, but meld into previous commit
# f, fixup <commit> = like "squash", but discard this commit's log message
# x, exec <command> = run command (the rest of the line) using shell
# b, break = stop here (continue rebase later with 'git rebase --continue')
# d, drop <commit> = remove commit
```

### Bước 3: Cấu hình các hành động trong trình soạn thảo
Theo yêu cầu đề bài, sửa đổi các dòng như sau:
```text
pick <hash1> feat: khoi tao module auth
squash <hash2> fix typo
squash <hash3> adds utility functions
drop <hash4> add temp file for debug
```

Giải thích từng hành động:
* pick (commit 1): Giữ nguyên commit gốc - đây là commit "nền" để 2 commit sau được gộp vào
* squash (commit 2 và 3): Gộp 2 commit này vào commit phía trên (commit 1), tạo thành 1 commit duy nhất
* drop (commit 4): Xóa bỏ hoàn toàn commit chứa temp.txt - file rác sẽ không còn trong lịch sử

### Bước 4: Sửa thông điệp commit gộp
Sau khi lưu danh sách hành động, Git mở lại trình soạn thảo với nội dung tổng hợp thông điệp của 3 commit đang được gộp:
```text
# This is a combination of 3 commits.
# The first commit's message is:
feat: khoi tao module auth
#
# This is the 2nd commit message:
fix typo
#
# This is the 3rd commit message:
adds utility functions
```

Xóa toàn bộ nội dung cũ và thay bằng thông điệp mới theo yêu cầu đề bài:
```text
feat: hoan thien module authentication
```

Lưu và đóng trình soạn thảo. Git sẽ hiển thị thông báo hoàn tất:
```text
Successfully rebased and updated refs/heads/<tên nhánh>.
```

### Bước 5: Kiểm tra lịch sử commit sạch sẽ
```bash
git log --oneline
```

Kết quả mong đợi - chỉ còn duy nhất 1 commit:
```text
<new_hash> feat: hoan thien module authentication
```

Kiểm tra thêm rằng file rác đã bị xóa khỏi theo dõi:
```bash
git log --all --oneline -- temp.txt
```

Lệnh này không trả về kết quả nào - chứng minh commit chứa temp.txt đã bị loại bỏ hoàn toàn khỏi lịch sử.