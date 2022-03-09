Hạ tầng đề xuất
===============

CPC-APIM là nơi chia sẻ dữ liệu tập trung, nghĩa là sẽ chịu tải lớn lên đến hàng chục ngàn RPS,
hàng ngàn DAU, SLA yêu cầu xấp xỉ 99%. Khi thiết lập hệ thống CPC-APIM, đội vận hành hệ thống nên xem xét cân bằng 
yêu cầu về hiệu năng và tiết kiệm tài nguyên phần cứng.

CPU
---

CPC-APIM phải xử lý nhiều requests liên tục và thực hiện nhiều tasks, vậy nên cần nhiều CPU core để xử lý (ví dụ 8 core hoặc nhiều hơn), và 
clock rate của CPU cao (tính bằng GHz). 

RAM
---

Nhìn chung, càng nhiều RAM càng tốt. Các máy chủ xử lý nhiều tasks như Identity Service, API Gateway, Monitoring cần khoảng 16GB RAM để hoạt động ở mức tối thiểu, 
Database server cần khoảng 32GB RAM. 

Disk
----

**HDD** 

Server lưu trữ

**SSD**

Hiệu năng của CPC-APIM sẽ được nâng cao bằng cách sử dụng SSD. Điều này giúp giảm thời gian random access và giảm delay khi 
tăng throughput.

Network
-------

Hầu hết để truy cập vào server CPC tại Data Center đều phải qua firewall và sử dụng VPN. Người dùng phải có tài khoản AD và được quyền VPN 
vào hệ thống mạng. Các máy chủ cũng phải được mở kết nối với internet để download các packages.

Hạ tầng tối thiểu
-----------------

+-----------------------+---------------+-------------+-------------+-------------------+
|        Server         |   CPU (Core)  |   RAM (GB)  |   Disk (GB) |       OS          |
|                       |               |             +-----+-------+                   |
|                       |               |             | OS  |  Data |                   |
+=======================+===============+=============+=====+=======+===================+
| Load Balancer         |       4       |      8      | 70  |       |   Ubuntu 20.04    |
+-----------------------+---------------+-------------+-----+-------+-------------------+
| Identity Server 1     |       8       |      16     | 100 |       |   Ubuntu 20.04    |
+-----------------------+---------------+-------------+-----+-------+-------------------+
| Identity Server 2     |       8       |      16     | 100 |       |   Ubuntu 20.04    |
+-----------------------+---------------+-------------+-----+-------+-------------------+
| API Gateway 1         |       8       |      16     | 100 |       |   Ubuntu 20.04    |
+-----------------------+---------------+-------------+-----+-------+-------------------+
| API Gateway 2         |       8       |      16     | 100 |       |   Ubuntu 20.04    |
+-----------------------+---------------+-------------+-----+-------+-------------------+
| API Integrator 1      |       8       |      16     | 100 |       |   Ubuntu 20.04    |
+-----------------------+---------------+-------------+-----+-------+-------------------+
| API Integrator 1      |       8       |      16     | 100 |       |   Ubuntu 20.04    |
+-----------------------+---------------+-------------+-----+-------+-------------------+
| API Control Plane     |       8       |      16     | 100 |       |   Ubuntu 20.04    |
+-----------------------+---------------+-------------+-----+-------+-------------------+
| Analytics & Monitoring|       8       |      32     | 100 | 500   |   Ubuntu 20.04    |
+-----------------------+---------------+-------------+-----+-------+-------------------+
| Database              |       8       |      32     | 100 | 500   |Windows Server 2016|
+-----------------------+---------------+-------------+-----+-------+-------------------+
| Deployment            |       8       |      32     | 100 | 500   |   Ubuntu 20.04    |
+-----------------------+---------------+-------------+-----+-------+-------------------+
