Identity Server
===============

Cài đặt Identity Server

.. important:: 
    Trước khi cài đặt, hãy xem xét về yêu cầu `hệ thống tối thiểu của APIM <https://apim.docs.wso2.com/en/latest/install-and-setup/install/installation-prerequisites>`_ 
    và :ref:`Hạ tầng đề xuất của CPC-APIM<recommended_hardware>`.

Download
--------

.. note::
    Vào thời điểm hiện tại, CPC-APIM đang cài đặt `WSO2 Identity Server 5.11.0 <https://wso2.com/identity-server/>`_ .
    Trong tương lai có thể phiên bản WSO2 Identity Server khác sẽ được cài đặt.

.. important::
    Để download được các package từ internet, hãy xem thêm về yêu cầu :ref:`network<recommended_hardware_network>` tại EVNCPC Data Center.

Sử dụng ``wget`` để download package

.. code-block:: bash
    
    # Download packagae
    wget --user-agent="testuser" --referer="http://connect.wso2.com/wso2/getform/reg/new_product_download"  http://product-dist.wso2.com/products/identity-server/5.11.0/wso2is-5.11.0.zip
    # Install unzip
    sudo apt install unzip
    # Unzip package
    sudo unzip wso2is-5.11.0.zip -d /opt/WSO2
    # check help for more command detail
    sh /opt/WSO2/wso2is-5.11.0/bin/wso2server.sh -help

Export environment variables
----------------------------

.. code-block:: bash

    # Update the BASHRC file
    export IS_HOME=/opt/WSO2/wso2is-5.11.0

Config IS as linux service
--------------------------

Tạo file `wso2is.service` ở thư mục `/etc/systemd/system/`

.. code-block:: bash

    cd /etc/systemd/system/
    sudo nano wso2is.service

Thêm config vào file `wso2is.service`

.. important::
    Các entry ``ExecStart``, ``ExecStop``, ``ExecRestart``, ``PIDFile`` phải dùng absolut path, 
    lưu ý path `/opt/WSO2/wso2is-5.11.0` chính là ``${IS_HOME}``

.. code-block:: bash

    [Unit]
    Description=WSO2 Identity Server - Default profile
    After=network.target

    [Service]
    Environment=JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64/
    ExecStart=/opt/WSO2/wso2is-5.11.0/bin/wso2server.sh start
    ExecStop=/opt/WSO2/wso2is-5.11.0/bin/wso2server.sh stop
    ExecRestart=/opt/WSO2/wso2is-5.11.0/bin/wso2server.sh restart
    PIDFile=/opt/WSO2/wso2is-5.11.0/wso2carbon.pid
    Type=forking
    Restart=on-failure
    RestartSec=5
    StartLimitInterval=60s
    StartLimitBurst=3

    [Install]
    WantedBy=multi-user.target

Run service
-----------

.. code-block:: bash

    # Start
    sudo systemctl start  wso2is
    # Stop
    sudo systemctl stop  wso2is
    # Check status
    sudo systemctl status wso2is
    # restart
    sudo systemctl restart wso2is

Config APIM
-----------

.. _install_apim_config_database:

**Cấu hình MSSQL Database**

.. important:: 

    Cần phải :ref:`cài đặt server database<install_database>` MSSQL cho CPC-APIM trước khi thực hiện các bước tiếp theo.

#. Tải thư viện MSSQL JDBC mới nhất, bỏ vào thư mục `<IS_HOME>/repository/components/lib/`
#. Tạo mới tài khoản MSSQL để cấu hình IS, ví dụ tên user **wso2is**
#. Tạo 2 database **WSO2IDENTITY_DB** và **WSO2SHARED_DB** trong mssql và cấu hình phân quyền đầy đủ
#. Chạy các script tương ứng để khởi tạo data cho các database:

    #. WSO2_SHARED_DB : <IS_HOME>/dbscripts/mssql.sql
    #. WSO2IDENTITY_DB : 
        #. `<IS-HOME>/dbscripts/identity/mssql.sql`
        #. `<IS-HOME>/dbscripts/identity/uma/mssql.sql`
        #. `<IS-HOME>/dbscripts/consent/mssql.sql`
    
#. Cấu hình connection tới database:

    Sửa file `<API_M_HOME>/repository/conf/deployment.toml`, thêm config connection như sau:

    .. code-block:: bash

        [database.identity_db]
        type = "mssql"
        url = "jdbc:sqlserver://wso2.mssql.db:1433;databaseName=IS_WSO2IDENTITY_DB;SendStringParametersAsUnicode=false;sslProtocol=TLSv1.2;encrypt=true;trustServerCertificate=true"
        username = "wso2is"
        password = "Evncpc@12345"
        driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        validationQuery = "SELECT 1"
        
        [database.shared_db]
        type = "mssql"
        url = "jdbc:sqlserver://wso2.mssql.db:1433;databaseName=IS_WSO2_SHARED_DB;SendStringParametersAsUnicode=false;sslProtocol=TLSv1.2;encrypt=true;trustServerCertificate=true"
        username = "wso2is"
        password = "Evncpc@12345"
        driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        validationQuery = "SELECT 1"

    .. note:: 
        
        Tham khảo thêm về kết nối database tại `đây<https://apim.docs.wso2.com/en/latest/install-and-setup/setup/setting-up-databases/changing-default-databases/changing-to-mssql/>_`.

**Cấu hình domain**

.. important::
    
    Trước khi cấu hình domain, phải cài đặt :ref:`Gateway<install_gateway>` cho CPC-APIM.

Update file `/etc/hosts`

.. code-block:: bash
    
    wso2.mssql.db   <mssql-db-server-ip>

Update file `<API_M_HOME>/repository/conf/deployment.toml`, thêm các config sau:

.. code-block:: bash
    
    [server]
    hostname = "identity.cpc.vn"
    
    [transport.https.properties]
    proxyPort = 443

**Run service**

.. code-block:: bash
    
    sudo systemctl start wso2is

**Bổ sung certificate**

#. Truy cập `admin console<https://identity.cpc.vn/carbon>_`
#. Vào menu `Manager > Keystore > List`
#. Chọn `Import Cert` , chọn file pem chưa certificate cần import để thực hiện import

Cập nhật Nginx proxy
--------------------

Truy cập server proxy vào tạo mới file `/etc/nginx/conf.d/identiy.conf`

.. code-block:: nginx

    upstream identity_server {
       server 10.72.2.215:9443;
   }
   
   server {
           listen 80;
           server_name identity.cpc.vn;
           location / {
               proxy_set_header X-Forwarded-Host $host;
               proxy_set_header X-Forwarded-Server $host;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
               proxy_set_header Host $http_host;
               proxy_read_timeout 5m;
               proxy_send_timeout 5m;
               proxy_pass http://identity_server;
   
               proxy_http_version 1.1;
               proxy_set_header Upgrade $http_upgrade;
               proxy_set_header Connection "upgrade";
           }
   }
   
   server {
       listen 443 ssl;
       server_name identity.cpc.vn;
       proxy_set_header X-Forwarded-Port 443;
       ssl_certificate /etc/nginx/ssl/cpc-chain.pem;
       ssl_certificate_key /etc/nginx/ssl/cpc-chain.pem;
       location / {
               proxy_set_header X-Forwarded-Host $host;
               proxy_set_header X-Forwarded-Server $host;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
               proxy_set_header Host $http_host;
               proxy_read_timeout 5m;
               proxy_send_timeout 5m;
               proxy_pass https://identity_server;
       }
   
       location /oauth2/authorize {
           proxy_set_header X-Forwarded-Host $host;
           proxy_set_header X-Forwarded-Server $host;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; proxy_set_header Host $http_host;
           proxy_read_timeout 5m;
           proxy_send_timeout 5m;
           proxy_pass https://identity_server/oauth2/authorize;
       }
   
       location /authenticationendpoint/ {
           proxy_set_header X-Forwarded-Host $host;
           proxy_set_header X-Forwarded-Server $host;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for; proxy_set_header Host $http_host;
           proxy_read_timeout 5m;
           proxy_send_timeout 5m;
           proxy_pass https://identity_server/authenticationendpoint/;
       }
   
       access_log /var/log/nginx/is/https/access.log;
       error_log /var/log/nginx/is/https/error.log;
   }

Khởi động lại Nginx