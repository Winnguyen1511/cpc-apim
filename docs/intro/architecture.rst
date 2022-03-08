Kiến trúc
=========
Kiến trúc chia sẻ API/WS qua CPC-APIM:

.. images:: images/cpc-apim-architecture.png

**Chia sẻ dữ liệu giữa các ứng dụng:**

* Người dùng (User) truy cập vào các chức năng trên các ứng dụng.

* App truy cập thông qua APIM, thông tin được logging, mornitoring.

* API được AuthN qua Identity Service (IS).

* Đối với các dữ liệu cần AuthZ, App sẽ truy cập database của IS để lấy Access Control List (ACL).

* API sau khi tích hợp sẽ được phân chia theo Scope.
