# Nghiên cứu, xây dựng mô hình mạng an toàn cho Công ty CP Kỹ thuật Hòa Phát

Giáo viên hướng dẫn : TS. Phạm Văn Hiệp

Nhóm sinh viên thực hiện :

1. Nguyễn Văn Hoàng Mã SV: 2023604024
2. Ngô Văn Khang Mã SV: 2023606083
3. Bùi Văn Quang Mã SV: 2023604780
4. Nguyễn Văn Thủy Mã SV: 2023603354
5. Vũ Thế Vượng Mã SV: 2023600855

## 1. Giới thiệu đề tài

Đề tài tập trung nghiên cứu và xây dựng mô hình mạng an toàn cho Công ty CP Kỹ thuật Hòa Phát dựa trên kiến trúc mạng doanh nghiệp có phân vùng bảo mật. Mô hình được thiết kế và mô phỏng bằng Cisco Packet Tracer, nhằm kiểm soát truy cập giữa các vùng mạng, bảo vệ hệ thống máy chủ và hạn chế rủi ro tấn công từ bên ngoài cũng như bên trong mạng nội bộ.

Hệ thống được xây dựng theo hướng chia tách rõ ràng các vùng mạng gồm:

- Vùng Internet/Outside
- Vùng mạng nội bộ LAN/Inside
- Vùng DMZ chứa các Server
- Các VLAN phòng ban: Kỹ thuật, Kinh doanh, Kế toán, Quản trị

Firewall ASA 5505 được sử dụng làm thiết bị bảo mật trung tâm, kiểm soát luồng dữ liệu giữa Internet, LAN và DMZ. Core Switch Layer 3 đảm nhiệm chức năng định tuyến nội bộ và áp dụng chính sách ACL để cách ly các VLAN phòng ban.

---

## 2. Mục tiêu đề tài

- Tìm hiểu tổng quan về an ninh mạng và các mô hình mạng an toàn trong doanh nghiệp.
- Thiết kế mô hình mạng có phân vùng bảo mật theo cấu trúc LAN – DMZ – Internet.
- Phân chia VLAN theo từng phòng ban để tăng khả năng quản lý và kiểm soát truy cập.
- Cấu hình Firewall ASA để kiểm soát lưu lượng giữa Internet, DMZ và LAN.
- Thiết lập các chính sách ACL nhằm cách ly các VLAN và bảo vệ vùng quản trị.
- Mô phỏng, triển khai và đánh giá kết quả thực nghiệm trên Cisco Packet Tracer.

---

## 3. Công nghệ và công cụ sử dụng

| Công cụ / Công nghệ | Vai trò                                              |
| ------------------- | ---------------------------------------------------- |
| Cisco Packet Tracer | Thiết kế, cấu hình và mô phỏng mô hình mạng          |
| Cisco ASA 5505      | Firewall kiểm soát luồng truy cập giữa các vùng mạng |
| Core Switch Layer 3 | Định tuyến giữa các VLAN, áp dụng ACL nội bộ         |
| Access Switch       | Kết nối các thiết bị đầu cuối trong từng VLAN        |
| VLAN                | Phân chia mạng theo phòng ban                        |
| ACL                 | Kiểm soát truy cập giữa các VLAN và các vùng mạng    |
| NAT/PAT             | Cho phép LAN truy cập Internet thông qua ASA         |
| DMZ                 | Vùng đặt Web Server, FTP Server và Mail Server       |

---

## 4. Cấu trúc mô hình mạng

Mô hình gồm các vùng chính:

```text
Internet / ISP
      |
Router ISP
      |
Firewall ASA 5505
   /       \
Inside     DMZ
  |          |
Core Switch  DMZ Switch
  |                  |
VLAN 10/20/30/40   Web / FTP / Mail Server
```

### Các VLAN trong mô hình

| VLAN    | Tên vùng     | Dải mạng        | Gateway                  | Chức năng                   |
| ------- | ------------ | --------------- | ------------------------ | --------------------------- |
| VLAN 10 | Kỹ thuật     | 192.168.10.0/24 | 192.168.10.1             | Thiết bị phòng Kỹ thuật     |
| VLAN 20 | Kinh doanh   | 192.168.20.0/24 | 192.168.20.1             | Thiết bị phòng Kinh doanh   |
| VLAN 30 | Kế toán      | 192.168.30.0/24 | 192.168.30.1             | Thiết bị phòng Kế toán      |
| VLAN 40 | Quản trị     | 192.168.40.0/24 | 192.168.40.1             | Máy quản trị hệ thống       |
| DMZ     | Server       | 10.0.0.0/24     | 10.0.0.1                 | Web, FTP, Mail Server       |
| Transit | Core – ASA   | 172.16.0.0/30   | 172.16.0.1 / 172.16.0.2  | Kết nối Core Switch với ASA |
| Outside | Internet/ISP | 203.0.113.0/30  | ASA Outside / Router ISP | Kết nối ra mạng ngoài       |

---

## 5. Chính sách bảo mật chính

Mô hình được triển khai theo nguyên tắc phân vùng, phân quyền và kiểm soát truy cập:

| Chính sách                                                 | Ý nghĩa                                           |
| ---------------------------------------------------------- | ------------------------------------------------- |
| Internet chỉ được truy cập các dịch vụ cần thiết trong DMZ | Cho phép công khai Web, FTP, Mail có kiểm soát    |
| Internet không được truy cập trực tiếp vào LAN             | Bảo vệ máy trạm và tài nguyên nội bộ              |
| LAN được phép truy cập DMZ theo nhu cầu sử dụng            | Nhân viên nội bộ dùng được dịch vụ Web, FTP, Mail |
| DMZ không được truy cập tự do vào LAN                      | Ngăn tấn công lan truyền nếu Server bị khai thác  |
| VLAN 10, 20, 30 bị cách ly với nhau                        | Hạn chế truy cập trái phép giữa các phòng ban     |
| VLAN 40 Quản trị được quyền truy cập các VLAN khác         | Phục vụ quản trị, kiểm tra và xử lý sự cố         |
| LAN truy cập Internet thông qua NAT/PAT                    | Che giấu địa chỉ IP nội bộ khi ra Internet        |

---

## 6. Các kịch bản thực nghiệm

| STT | Kịch bản                                | Người thực hiện  | Mục tiêu                                                           |
| --- | --------------------------------------- | ---------------- | ------------------------------------------------------------------ |
| 1   | Thiết lập truy cập Internet vào DMZ     | Ngô Văn Khang    | Cho phép Internet truy cập Web/FTP/Mail Server, chặn truy cập khác |
| 2   | LAN truy cập DMZ                        | Nguyễn Văn Hoàng | Kiểm tra các VLAN nội bộ truy cập dịch vụ DMZ                      |
| 3   | DMZ không được truy cập ngược vào LAN   | Bùi Văn Quang    | Chặn Server trong DMZ truy cập trái phép vào mạng nội bộ           |
| 4   | LAN truy cập Internet thông qua NAT/PAT | Vũ Thế Vượng     | Cho phép PC nội bộ ra Internet qua địa chỉ outside của ASA         |
| 5   | Thiết lập ACL cách ly giữa các VLAN     | Nguyễn Văn Thuỷ  | Cách ly VLAN 10/20/30, cho phép VLAN 40 quản trị các vùng khác     |

---

## 7. Hướng phát triển

Trong phạm vi đề tài, mô hình được triển khai ở mức mô phỏng trên Cisco Packet Tracer. Trong tương lai, mô hình có thể được mở rộng theo các hướng:

- Bổ sung IDS/IPS để phát hiện và ngăn chặn tấn công.
- Triển khai VPN cho nhân viên làm việc từ xa.
- Bổ sung hệ thống giám sát log tập trung.
- Tăng cường xác thực quản trị thiết bị mạng.
- Mở rộng mô hình cho nhiều chi nhánh doanh nghiệp.
- Thử nghiệm trên thiết bị thật hoặc môi trường giả lập nâng cao hơn.
