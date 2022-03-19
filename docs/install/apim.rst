API Manager Runtime
===================

Cài đặt API Manager Runtime

.. important:: 
    Trước khi cài đặt, hãy xem xét về yêu cầu `hệ thống tối thiểu của APIM <https://apim.docs.wso2.com/en/latest/install-and-setup/install/installation-prerequisites>`_ 
    và :ref:`Hạ tầng đề xuất của CPC-APIM<recommended_hardware>`.

Download
--------

.. note::
    Vào thời điểm hiện tại, CPC-APIM đang cài đặt `WSO2 API Manager 4.0.0 <https://wso2.com/library/articles/introducing-wso2-api-manager-4-0/>`_ .
    Trong tương lai có thể phiên bản WSO2 API Manager khác sẽ được cài đặt.

.. important::
    Để download được các package từ internet, hãy xem thêm về yêu cầu :ref:`network<recommended_hardware_network>` tại EVNCPC Data Center.

Sử dụng ``wget`` để download package

.. code-block:: bash
    
    # Download packagae
    wget https://product-dist.wso2.com/products/api-manager/4.0.0/wso2am-4.0.0.zip
    # Install unzip
    sudo apt install unzip
    # Unzip package
    sudo unzip wso2am-4.0.0.zip -d /opt/WSO2
    # check help for more command detail
    sh /opt/WSO2/wso2am-4.0.0/bin/api-manager.sh -help


Export environment variables
----------------------------

.. code-block:: bash

    # Update the BASHRC file
    export API_M_HOME=/opt/WSO2/wso2am-4.0.0

Config API-M as linux service
-----------------------------

Tạo file `wso2apim.service` ở thư mục `/etc/systemd/system/`

.. code-block:: bash

    cd /etc/systemd/system/
    sudo nano wso2apim.service

Thêm config vào file `wso2apim.service`

.. important::
    Các entry ``ExecStart``, ``ExecStop``, ``ExecRestart``, ``PIDFile`` phải dùng absolut path, 
    lưu ý path `/opt/WSO2/wso2am-4.0.0` chính là ``${API_M_HOME}``

.. code-block:: bash

    [Unit]
    Description=WSO2 API Manager - Default profile
    After=network.target
    
    [Service]
    Environment=JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64/
    ExecStart=/opt/WSO2/wso2am-4.0.0/bin/api-manager.sh start
    ExecStop=/opt/WSO2/wso2am-4.0.0/bin/api-manager.sh stop
    ExecRestart=/opt/WSO2/wso2am-4.0.0/bin/api-manager.sh restart
    PIDFile=/opt/WSO2/wso2am-4.0.0/wso2carbon.pid
    User=root
    Group=root
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
    sudo systemctl start  wso2apim
    # Stop
    sudo systemctl stop  wso2apim
    # Check status
    sudo systemctl status wso2apim
    # restart
    sudo systemctl restart wso2apim

Config APIM
-----------

.. _install_apim_config_database:

**Cấu hình MSSQL Database**

.. important:: 

    Cần phải :ref:`cài đặt server database<install_database>` MSSQL cho CPC-APIM trước khi thực hiện các bước tiếp theo.

#. Tải thư viện MSSQL JDBC mới nhất, bỏ vào thư mục `<API_M_HOME>/repository/components/lib/`
#. Tạo mới tài khoản MSSQL để cấu hình APIM, ví dụ tên user **wso2apim**
#. Tạo 2 database **WSO2AM_DB** và **WSO2SHARED_DB** trong mssql và cấu hình phân quyền đầy đủ
#. Chạy các script tương ứng để khởi tạo data cho các database:

    #. WSO2_SHARED_DB : <API_M_HOME>/dbscripts/mssql.sql
    #. WSO2AM_DB : <API_M_HOME>/dbscripts/apimgt/mssql.sql
    
#. Cấu hình connection tới database:

    Sửa file `<API_M_HOME>/repository/conf/deployment.toml`, thêm config connection như sau:

    .. code-block:: bash

        [database.apim_db]
        type = "mssql"
        url = "jdbc:sqlserver://wso2.mssql.db:1433;databaseName=APIM_WSO2AM_DB;SendStringParametersAsUnicode=false;sslProtocol=TLSv1.2;encrypt=true;trustServerCertificate=true"
        username = "<username>"
        password = "<password>"
        driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        validationQuery = "SELECT 1"
        
        [database.shared_db]
        type = "mssql"
        url = "jdbc:sqlserver://wso2.mssql.db:1433;databaseName=APIM_WSO2SHARED_DB;SendStringParametersAsUnicode=false;sslProtocol=TLSv1.2;encrypt=true;trustServerCertificate=true"
        username = "<username>"
        password = "<passworkd>"
        driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
        validationQuery = "SELECT 1"

    .. note:: 
        
        Tham khảo thêm về kết nối database tại `đây<https://apim.docs.wso2.com/en/latest/install-and-setup/setup/setting-up-databases/changing-default-databases/changing-to-mssql/>_`.


**Cấu hình domain**

.. important::
    
    Trước khi cấu hình domain, phải cài đặt :ref:`Gateway<install_gateway>` cho CPC-APIM.

.. code-block:: bash

    [server]
    hostname = "apim.cpc.vn"
    
    [apim.publisher]
    url = "https://apim.cpc.vn:443/publisher"
    
    [apim.devportal]
    url = "https://apim.cpc.vn:443/devportal"
    
    [[apim.gateway.environment]]
    name = "Default"
    type = "hybrid"
    display_in_api_console = true
    description = "This is a hybrid gateway that handles both production and sandbox token traffic."
    show_as_token_endpoint_url = true
    service_url = "https://apim.cpc.vn:443/services/"
    username= "${admin.username}"
    password= "${admin.password}"
    ws_endpoint = "ws://gw.cpc.vn:9099"
    wss_endpoint = "wss://gw.cpc.vn:8099"
    http_endpoint = "http://gw.cpc.vn"
    https_endpoint = "https://gw.cpc.vn:443"
    websub_event_receiver_http_endpoint = "http://localhost:9021"
    websub_event_receiver_https_endpoint = "https://localhost:8021"
    
    [transport.https.properties]
    proxyPort = 443

**Run service**

.. code-block:: bash

    sudo systemctl start  wso2apim

Cập nhật Nginx proxy
--------------------

.. code-block:: nginx

    upstream sslapi.am.wso2.com {
        server 10.72.2.217:9443;
    }
    
    upstream sslgw.am.wso2.com {
        server 10.72.2.217:8243;
    }
    
    server {
        listen 80;
        server_name apim.cpc.vn;
        rewrite ^/(.*) https://apim.cpc.vn/$1 permanent;
    }
    
    server {
        listen 443 ssl;
        server_name apim.cpc.vn;
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
                proxy_pass https://sslapi.am.wso2.com;
            }
    
            access_log /var/log/nginx/am/https/access.log;
            error_log /var/log/nginx/am/https/error.log;
    }
    
    server {
        listen 443 ssl;
        server_name gw.cpc.vn;
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
                proxy_pass https://sslgw.am.wso2.com;
            }
    
            access_log /var/log/nginx/gw/https/access.log;
            error_log /var/log/nginx/gw/https/error.log;
    
    }

Cấu hình SSO với Identity Server
--------------------------------

.. note:: 

    Xem thêm về cách cấu hình Identity Server của `WSO2 <https://apim.docs.wso2.com/en/latest/install-and-setup/setup/sso/configuring-identity-server-as-external-idp-using-oidc/>`_.