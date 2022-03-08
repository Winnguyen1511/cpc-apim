Bức tranh lớn
=============

Hầu hết các ứng dụng tại EVNCPC đang chia sẻ dữ liệu theo mô hình sau:

..images:: images/present-cpc-shared-data.png

**Hiện trạng:**

* API/WS chưa được quản lý tập trung.

* API/WS chồng chéo, không kiểm soát được.

* Chưa có chuẩn, định hướng chung cho việc chia sẻ.

* Các vấn đề về ATTT, phân quyền chưa có giải pháp triệt để.

**Sự cần thiết:**

* Mục tiêu 01: Có quy tắc, chuẩn, kiến trúc chung cho các API/WS.

* Mục tiêu 02: Có giải pháp quản lý tập trung, vận hành các API/WS chia sẻ, đảm bảo hiệu năng và ổn định.

* Mục tiêu 03: Có lộ trình thay đổi, nâng cấp, quy hoạch các API/WS có nhiều tồn tại, không thích hợp với mô hình chung.

**CPC-APIM có thể làm gì?**
CPC-APIM được xây dựng để đáp ứng mục tiêu 02: 

..images:: images/how-cpc-apim-can-help.png

CPC-APIM gồm các tính năng chính như sau:

* API Gateway: Che giấu hệ thống với bên ngoài, việc chia sẻ sẽ gọn nhẹ hơn. API Gateway cũng giúp tích hợp API/WS đa nền tảng
(API, WS, FTP, MQ, ...).

* Load balancing và cache các request.

* Bảo mật, ngăn chặn DDoS, Injections, ...

* Authentication (AuthN) và Authorization (AuthZ)

* Monitoring tập trung.
