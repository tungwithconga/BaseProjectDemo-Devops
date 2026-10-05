# Thực hành Git Conflict -- 3 người làm trên 3 máy

## 1. Mục tiêu

Tạo một base project có file `README.md`, sau đó 3 thành viên cùng clone
project và chỉnh sửa **cùng một nội dung trên nhánh `main` theo 3 hướng
khác nhau** để tạo ra Git Conflict và thực hành xử lý conflict.

### Thành viên

1.  Nguyễn Văn A
2.  Nguyễn Văn B
3.  Nguyễn Văn C

------------------------------------------------------------------------

## 2. Máy A -- Tạo repository ban đầu

Tạo project:

``` bash
mkdir git-conflict-demo
cd git-conflict-demo
git init
```

Tạo file `README.md` ban đầu:

``` md
# Conflict khi merge code vào nhánh

Tạo dự án base project chứa 1 file README.md

/*
Team 1:
1. Nguyễn Văn A
2. Nguyễn Văn B
3. Nguyễn Văn C
*/

## Nội dung project

Trạng thái project: Chưa cập nhật
```

Commit project:

``` bash
git add .
git commit -m "Initial project"
git branch -M main
git remote add origin <URL_REPOSITORY>
git push -u origin main
```

Sau đó Máy A cấp quyền repository cho Nguyễn Văn B và Nguyễn Văn C.

------------------------------------------------------------------------

## 3. Máy B và Máy C -- Clone project

### Máy B

``` bash
git clone <URL_REPOSITORY>
cd git-conflict-demo
```

### Máy C

``` bash
git clone <URL_REPOSITORY>
cd git-conflict-demo
```

Sau bước này, cả 3 máy đều có dòng:

``` text
Trạng thái project: Chưa cập nhật
```

------------------------------------------------------------------------

## 4. Cả 3 thành viên cùng sửa README.md

**Lưu ý:** Cả 3 người cùng sửa trước khi thực hiện `pull` để tạo
conflict.

### Máy A sửa thành

``` text
Trạng thái project: Đã cập nhật bởi Nguyễn Văn A
```

### Máy B sửa thành

``` text
Trạng thái project: Đã cập nhật bởi Nguyễn Văn B
```

### Máy C sửa thành

``` text
Trạng thái project: Đã cập nhật bởi Nguyễn Văn C
```

Sau đó cả 3 máy đều commit:

``` bash
git add README.md
git commit -m "Update README"
```

------------------------------------------------------------------------

## 5. Máy A push trước

Máy A chạy:

``` bash
git push origin main
```

Push thành công.

Repository hiện tại sẽ chứa:

``` text
Trạng thái project: Đã cập nhật bởi Nguyễn Văn A
```

------------------------------------------------------------------------

## 6. Máy B push và gặp Conflict

Máy B thử:

``` bash
git push origin main
```

Git sẽ từ chối do repository đã có commit mới của A.

Máy B chạy:

``` bash
git pull origin main
```

Nếu Git yêu cầu chọn cách pull:

``` bash
git config pull.rebase false
git pull origin main
```

Git có thể thông báo:

``` text
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
```

Kiểm tra:

``` bash
git status
```

Sẽ thấy:

``` text
both modified: README.md
```

------------------------------------------------------------------------

## 7. Máy B xử lý Conflict

Mở `README.md`, nội dung conflict sẽ có dạng:

``` text
<<<<<<< HEAD
Trạng thái project: Đã cập nhật bởi Nguyễn Văn B
=======
Trạng thái project: Đã cập nhật bởi Nguyễn Văn A
>>>>>>> ...
```

Ý nghĩa:

-   `<<<<<<< HEAD`: code hiện tại trên máy B.
-   `=======`: ngăn cách hai phiên bản.
-   `>>>>>>>`: code lấy về từ repository.

Máy B sửa lại thành:

``` text
Trạng thái project: Đã cập nhật bởi Nguyễn Văn A và Nguyễn Văn B
```

Xóa các marker:

``` text
<<<<<<< HEAD
=======
>>>>>>>
```

Sau đó:

``` bash
git add README.md
git commit -m "Resolve conflict between A and B"
git push origin main
```

------------------------------------------------------------------------

## 8. Máy C tạo Conflict lần 2

Máy C đang giữ phiên bản:

``` text
Trạng thái project: Đã cập nhật bởi Nguyễn Văn C
```

Máy C thử push:

``` bash
git push origin main
```

Push sẽ bị từ chối.

Thực hiện:

``` bash
git pull origin main
```

Nếu cần:

``` bash
git config pull.rebase false
git pull origin main
```

Conflict sẽ có dạng:

``` text
<<<<<<< HEAD
Trạng thái project: Đã cập nhật bởi Nguyễn Văn C
=======
Trạng thái project: Đã cập nhật bởi Nguyễn Văn A và Nguyễn Văn B
>>>>>>> ...
```

Máy C xử lý thành:

``` text
Trạng thái project: Đã cập nhật bởi Nguyễn Văn A, Nguyễn Văn B và Nguyễn Văn C
```

Sau đó:

``` bash
git add README.md
git commit -m "Resolve conflict between team members"
git push origin main
```

------------------------------------------------------------------------

## 9. Kết quả cuối cùng

File `README.md` trên nhánh `main`:

``` md
# Conflict khi merge code vào nhánh

Tạo dự án base project chứa 1 file README.md

/*
Team 1:
1. Nguyễn Văn A
2. Nguyễn Văn B
3. Nguyễn Văn C
*/

## Nội dung project

Trạng thái project: Đã cập nhật bởi Nguyễn Văn A, Nguyễn Văn B và Nguyễn Văn C
```

------------------------------------------------------------------------

## 10. Luồng thực hiện

``` text
A -- sửa README -- commit -- push --------------------+
                                                       |
B -- sửa README -- commit -- push X                   |
                            |                          |
                            +-- pull --> CONFLICT      |
                                         |             |
                                   Resolve --> push ---+
                                                       |
C -- sửa README -- commit -- push X                   |
                            |                          |
                            +-- pull --> CONFLICT      |
                                         |             |
                                   Resolve --> push ---+
                                                       |
                                                       v
                                                 MAIN hoàn chỉnh
```

------------------------------------------------------------------------

## 11. Các lệnh Git cần nhớ

``` bash
# Xem trạng thái
git status

# Xem lịch sử commit
git log --oneline --graph --all

# Lấy code mới
git pull origin main

# Thêm file
git add README.md

# Commit
git commit -m "message"

# Push
git push origin main
```

## Kết luận

Bài thực hành mô phỏng tình huống nhiều lập trình viên cùng chỉnh sửa
một phần code trên cùng một nhánh. Khi một thành viên đã push thay đổi
lên remote trước, những thành viên còn lại phải lấy thay đổi mới về và
xử lý conflict trước khi có thể push code của mình.
