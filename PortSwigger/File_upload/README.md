# Lab 1: RCE via web shell upload
## Lab Description:
<div style="display:flex; justify-content: center;">
    <img src="./src/01_1.png" style="width: 90%">
</div>

## Summary:
* Lỗ hổng ở phần upload ảnh và không có tí kiểm duyệt nào
* Để solve lab, upload một PHP web shell và lấy nội dung của file /home/carlos/secret
* Cho trước tài khoản: wiener:peter

## Solution:
* Đăng nhập vào tài khoản được cung cấp
<div style="display:flex; justify-content: center;">
    <img src="./src/01_2.png" style="width: 90%">
</div>

* App chuyển hướng tới My Account. Có mục upload file thay ava, chọn 1 file ảnh bthg, thấy hiển thị file được upload thành công và ảnh đã thay đổi
<div style="display:flex; justify-content: center;">
    <img src="./src/01_3.png" style="width: 90%">
</div>

* Vào Proxy/HTTP History của Burp, thấy một request có dạng như sau:
<div style="display:flex; justify-content: center;">
    <img src="./src/01_4.png" style="width: 90%">
</div>

* Tạo một file PHP với nội dung là:
<div style="display:flex; justify-content: center;">
    <img src="./src/01_5.png" style="width: 90%">
</div>

❗Hàm ***file_get_contents(file_name)***: dùng để đọc toàn bộ nội dung file, trả về nội dung dưới dạng một chuỗi (file_name thường có dạng là địa chỉ tuyệt đối)

* Upload file PHP đấy lên ava, hiển thị file upload thành công. Dùng Burp xem response trả về thấy được chuỗi sau:
<div style="display:flex; justify-content: center;">
    <img src="./src/01_6.png" style="width: 90%">
</div>

* Lấy chuỗi submit solution, và lab được solved

<br><br>

# Lab 2: Web shell upload via Content-Type restriction bypass
## Lab Description:
<div style="display:flex; justify-content: center;">
    <img src="./src/02_1.png" style="width: 90%">
</div>

## Summary:
* Lỗ hổng ở phần upload file ảnh, và đã có một chút kiểm duyệt ở file type nhưng phụ thuộc vào trường nhập mà user có thể kiểm soát được
* Để solve lab, upload một PHP web shell và phát tán nội dung của file /home/carlos/secret
* Cho trước tài khoản: wiener:peter
## Solution:
* Tương tự lab trước, nhưng đến khi thử upload webshell.php thì file không gửi được và lỗi hiển thị như sau:
<div style="display:flex; justify-content: center;">
    <img src="./src/02_2.png" style="width: 90%">
</div>

* Bật Burp interception lên, gửi lại webshell. Bắt request đó rồi dưới phần request body, sửa Content-Type thành image/jpg. Lần này web không hiển thị lỗi nữa, mà còn upload thành công
<div style="display:flex; justify-content: center;">
    <img src="./src/02_3.png" style="width: 90%">
</div>

* Do file được upload thành công, vào HTTP History tìm response trả về, nhận được chuỗi. Nhập chuỗi và lab được solved

<br><br>

# Lab 3: Web shell upload via path traversal
## Lab Description:
<div style="display:flex; justify-content: center;">
    <img src="./src/03_1.png" style="width: 90%">
</div>

## Summary:
* Lỗ hổng ở phần upload file ảnh, server đã được cấu hình để ngăn thực thi các files do người dùng upload
* Để solve lab, upload một PHP web shell và phát tán nội dung của file /home/carlos/secret
* Cho trước tài khoản: wiener:peter

## Solution:
* Giống các lab trước, ta upload webshell vào mục file ảnh. Nhưng lần này web trả về upload thành công
<div style="display:flex; justify-content: center;">
    <img src="./src/03_2.png" style="width: 90%">
</div>

* Xem response trả về trong Proxy/HTTP history, ta chỉ thấy trong body là plain text nội dung của webshell
<div style="display:flex; justify-content: center;">
    <img src="./src/03_3.png" style="width: 90%">
</div>

* Ta thử đổi tên của file thành tên khác. Tìm request có dạng POST trong HTTP request rồi ném nó sang Burp Repeater
* Dưới phần body của request, ở phần liên quan đến webshell của mình, thêm chuỗi traversal để thành "../webshell.php" thì nhận được response với dòng thông báo giống ban đầu. Có thể đoán được là server đã lọc chuỗi traversal ra khỏi filename
<div style="display:flex; justify-content: center;">
    <img src="./src/03_4.png" style="width: 90%">
</div>

* Không bỏ cuộc, ta mã hóa URL chuỗi traversal thành "..%2f", thay lại vào filename, ta đã thấy chuỗi traversal trong tên ở response
<div style="display:flex; justify-content: center;">
    <img src="./src/03_5.png" style="width: 90%">
</div>

* Quay trở lại trình duyệt rồi load lại trang. Xong bật lại Burp lên xem HTTP request, tìm request có dạng sau: GET /files/avatars/..%2fwebshell.php. Xem response nó trả về thì thấy nội dung của file, nhập chuỗi và lab đã được solved
<div style="display:flex; justify-content: center;">
    <img src="./src/03_6.png" style="width: 90%">
</div>

<br><br>

# Lab 4: Web shell upload via extension blacklist bypass
## Lab Description:
<div style="display:flex; justify-content: center;">
    <img src="./src/04_1.png" style="width: 90%">
</div>

## Summary:
* Lỗ hổng ở phần upload file ảnh, một vài ext đã bị cho vào blacklist
* Để solve lab, upload một PHP web shell và phát tán nội dung của file /home/carlos/secret
* Cho trước tài khoản: wiener:peter
## Solution:
* Giống các lab trước, ta upload webshell vào mục file ảnh. Nhưng lần này web trả về lỗi do ext không hợp lệ
<div style="display:flex; justify-content: center;">
    <img src="./src/04_2.png" style="width: 90%">
</div>

* Lần này ta tạo một file khác có tên là .htaccess với nội dung như sau:
<div style="display:flex; justify-content: center;">
    <img src="./src/04_3.png" style="width: 90%">
</div>

* Câu code trên giúp map ext .caigicungduoc tới một MIME type thực thi được là application/x-httpd-php
* Sau đó upload file ht.access đó lên, ta nhận được upload thành công
<div style="display:flex; justify-content: center;">
    <img src="./src/04_4.png" style="width: 90%">
</div>

* Sau đó ta quay trở lại cái request POST lúc upload webshell lên và đổi filename thành webshell.caigicungduoc, ta nhận được phản hồi upload thành công
<div style="display:flex; justify-content: center;">
    <img src="./src/04_5.png" style="width: 90%">
</div>

* Sau đó về xem phản hồi của webshell, thấy trả về nội dung file, nhập chuỗi và lab đã được solved
<div style="display:flex; justify-content: center;">
    <img src="./src/04_6.png" style="width: 90%">
</div>

# Lab 5: Web shell upload via obfuscated file extension
## Lab Description:
<div style="display:flex; justify-content: center;">
    <img src="./src/05_1.png" style="width: 90%">
</div>

## Summary:
* Lỗ hổng ở phần upload file ảnh, một vài ext đã bị cho vào blacklist
* Để solve lab, upload một PHP web shell và phát tán nội dung của file /home/carlos/secret
* Cho trước tài khoản: wiener:peter

## Solution:
* Vẫn như lab cũ, upload webshell, nhưng lần này response trả về yêu cầu chỉ ext là .jpg hoặc .png mới được upload
<div style="display:flex; justify-content: center;">
    <img src="./src/05_2.png" style="width: 90%">
</div>

* Đổi filename thêm null byte và .jpg thành webshell.php%00.jpg, nhận được phản hồi upload thành công. Có thể thấy filename đã bị lược đi .jpg ở cuối và null byte đã làm tên file chỉ còn webshell.php
<div style="display:flex; justify-content: center;">
    <img src="./src/05_3.png" style="width: 90%">
</div>

* Load lại trình duyệt, xem phản hồi trả về, nhập chuỗi, và lab đã được solved
<div style="display:flex; justify-content: center;">
    <img src="./src/05_4.png" style="width: 90%">
</div>