### 1. Brute force

- [Gobuster](https://github.com/OJ/gobuster): brute force web để tìm thư mục và page ẩn
    - Câu lệnh: gobuster -u url -w wordlist dir
- Tìm key của JWT: hashcat -m 16500 -a 3 jwt ?a?a?a?a
- Hashcat: crack password

### 2. Điều tra web

- Điều tra công cụ: điều tra user-agent
- URL2PNG: screen short web không cần truy cập

### 3. Công cụ mở file

- XML: mở bằng thunderbird
- FTK Imager: mở file .ad1 (ổ đĩa)

### 4. Phân tích file

- aureport: phân tích file log
- PhotoRec: khôi phục dữ liệu file theo signature
    - photorec recoverfiles.dd
- Audacity: chỉnh sửa, phân tích file âm thanh
- steghide: giấu và trích xuất data giấu vào file ảnh, audio
- fcrackzip: crack password file zip
- ExifTool: xem chỉnh sửa metadata file
- Thunderbird: lọc và phân tích file email
- Phân tích disk image: BTLO/[**Spectrum**](https://blueteamlabs.online/home/challenge/spectrum-d6ff2a32b9)
- peepdf: phân tích file PDF độc hại (có lợi trong tìm malware ẩn trong PDF)
- oledump.py: phân tích file tài liệu của Microsoft Office (thường liên quan macro)
- Scalpel: trích xuất file dựa trên cấu hình user

### 5. OSINT

- Tra tọa độ: https://www.gps-coordinates.net/

### 6. Phân tích MEM

- Volatility: phân tích RAM (tiến trình, bộ nhớ, password. kết nối mạng, file, reg,…) - BTLO/[**Memory Analysis - Ransomware**](https://blueteamlabs.online/home/challenge/memory-analysis-ransomware-7da6c9244d)

### 7. Mã hóa / giải mã

- VeraCrypt: tạo ổ đĩa ảo, mã hóa ổ đĩa
- GnuPG (GPG): mã hóa và kí số dữ liệu
