Micro Integrator
================

Cài đặt Micro Integrator

.. important:: 
    Trước khi cài đặt, hãy xem xét về yêu cầu `hệ thống tối thiểu của APIM <https://apim.docs.wso2.com/en/latest/install-and-setup/install/installation-prerequisites>`_ 
    và :ref:`Hạ tầng đề xuất của CPC-APIM<recommended_hardware>`.

Download
--------

.. important::
    Để download được các package từ internet, hãy xem thêm về yêu cầu :ref:`network<recommended_hardware_network>` tại EVNCPC Data Center.

Tải file cài đặt Micro Integrator Runtime server và Micro Integrator Dashboard tại `website WSO2 <https://wso2.com/integration/micro-integrator/#>`_ và giải nén vào thư
mục `/opt/wso2`.

.. note::
    Vào thời điểm hiện tại, CPC-APIM đang cài đặt `WSO2 Micro Integrator 4.1.0 <https://wso2.com/integration/micro-integrator>`_ .
    Trong tương lai có thể phiên bản WSO2 Micro Integrator khác sẽ được cài đặt.

.. code-block:: bash

    # Install unzip
    sudo apt install unzip
    # Unzip package
    sudo unzip wso2mi-4.1.0.zip -d /opt/wso2
    sudo unzip wso2mi-dashboard-4.1.0.zip -d /opt/wso2


Export environment variables
----------------------------

.. code-block:: bash

    # Update the BASHRC file
    export MI_HOME=/opt/WSO2/wso2mi-4.1.0
    export MI_DASHBOARD_HOME=/opt/WSO2/wso2mi-dashboard-4.1.0

Config MI as linux service
--------------------------

Tạo file `wso2mi.service`  và  `wso2mi-dashboard.service` trong thư mục `/etc/systemd/system/`

.. code-block:: bash

    cd /etc/systemd/system/
    sudo nano wso2mi.service

Thêm config vào file `wso2is.service`

.. code-block:: bash

    [Unit]
    Description=WSO2 Micro Integrator - Default profile
    After=network.target

    [Service]
    ExecStart=/opt/wso2/wso2mi-4.1.0/bin/micro-integrator.sh start
    ExecStop=/opt/wso2/wso2mi-4.1.0/bin/micro-integrator.sh stop
    ExecRestart=/opt/wso2/wso2mi-4.1.0/bin/micro-integrator.sh restart
    PIDFile=/opt/wso2/wso2mi-4.1.0/wso2carbon.pid
    User=root
    Group=root
    Type=forking
    Restart=on-failure
    RestartSec=5
    StartLimitInterval=60s
    StartLimitBurst=3

    [Install]
    WantedBy=multi-user.target

.. code-block:: bash

    cd /etc/systemd/system/
    sudo nano wso2mi.service

Thêm config vào file `wso2mi-dashboard.service`

.. code-block:: bash

    [Unit]
    Description=WSO2 Micro Dashboard - Default profile
    After=network.target

    [Service]
    ExecStart=/opt/wso2/wso2mi-dashboard-4.1.0/bin/dashboard.sh start
    ExecStop=/opt/wso2/wso2mi-dashboard-4.1.0/bin/dashboard.sh stop
    ExecRestart=/opt/wso2/wso2mi-dashboard-4.1.0/bin/dashboard.sh restart
    PIDFile=/opt/wso2/wso2mi-dashboard-4.1.0/runtime.pid
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

    # wso2mi
    # Start
    $ sudo systemctl start wso2mi
    # Stop
    $ sudo systemctl stop  wso2mi
    # Check status
    $ sudo systemctl status wso2mi
    # restart
    $ sudo systemctl restart wso2mi

.. code-block:: bash

    # wso2mi-dashboard
    # Start
    sudo systemctl start wso2mi-dashboard
    # Stop
    sudo systemctl stop  wso2mi-dashboard
    # Check status
    sudo systemctl status wso2mi-dashboard
    # restart
    sudo systemctl restart wso2mi-dashboard

Config Micro Integrator
-----------------------

.. _install_mi_config:

**Cấu hình kết nối MI server và Dashboard**

Update file `<MI_HOME>/conf/deployment.toml`, thêm các config sau:

.. code-block:: bash

    [dashboard_config]
    dashboard_url = "https://localhost:9743/dashboard/api/"
    heartbeat_interval = 5
    group_id = "mi_dev"
    node_id = "dev_node_1"

    [cluster_config]
    node_id = "dev_node_1"

**Cấu hình User Store**

.. code-block:: bash

    [internal_apis.file_user_store]
    enable = false

    [user_store]
    class = "org.wso2.micro.integrator.security.user.core.jdbc.JDBCUserStoreManager"
    type = "database"

    [[datasource]]
    id = "WSO2CarbonDB"
    url = "jdbc:sqlserver://wso2.mssql.db:1433;databaseName=MI_USER_DB;SendStringParametersAsUnicode=false;sslProtocol=TLSv1.2;encrypt=true;trustServerCertificate=true"
    username = "wso2apim"
    password = "Evncpc@12345"
    driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
    pool_options.maxActive=50
    pool_options.maxWait = 60000
    pool_options.testOnBorrow = true

    [[datasource]]
    id = "WSO2_COORDINATION_DB"
    url = "jdbc:sqlserver://wso2.mssql.db:1433;databaseName=MI_CLUSTER_DB;SendStringParametersAsUnicode=false;sslProtocol=TLSv1.2;encrypt=true;trustServerCertificate=true"
    username = "wso2apim"
    password = "Evncpc@12345"
    driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
    pool_options.maxActive=50
    pool_options.maxWait = 60000
    pool_options.testOnBorrow = true

    [[datasource]]
    id = "WSO2_TRANSACTION_DB"
    url = "jdbc:sqlserver://wso2.mssql.db:1433;databaseName=MI_TRANSACTION_DB;SendStringParametersAsUnicode=false;sslProtocol=TLSv1.2;encrypt=true;trustServerCertificate=true"
    username = "wso2apim"
    password = "Evncpc@12345"
    driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
    pool_options.maxActive=50
    pool_options.maxWait = 60000
    pool_options.testOnBorrow = true
    [transaction_counter]
    enable = true
    data_source = "WSO2_TRANSACTION_DB"
    update_interval = 2

.. _install_apim_config_database:

**Cấu hình MSSQL Database**

.. important:: 

    Cần phải :ref:`cài đặt server database<install_database>` MSSQL cho CPC-APIM trước khi thực hiện các bước tiếp theo.

#. Tải thư viện MSSQL JDBC mới nhất, bỏ vào thư mục `<MI_HOME>/lib/`
#. Tạo mới tài khoản MSSQL để cấu hình MI, ví dụ tên user **wso2mi**
#. Tạo 2 database **MI_USER_DB**, **MI_CLUSTER_DB**, **MI_TRANSACTION_DB** trong mssql và cấu hình phân quyền đầy đủ
#. Chạy các script tương ứng để khởi tạo data cho các database:

    #. MI_USER_DB : `<MI_HOME>/dbscripts/mssql/mssql_user.sql`
    #. MI_CLUSTER_DB : `<MI_HOME>/dbscripts/mssql/mssql_cluster.sql`
    #. MI_TRANSACTION_DB: `<MI_HOME>/dbscripts/mssql/mssql_transaction_count.sql`

**Cấu hình CORS**

.. code-block:: bash

    [management_api.cors]
    enabled = true
    allowed_origins = "127.0.0.1, ei.cpc.vn, apim.cpc.vn, gw.cpc.vn"
    allowed_headers = "Authorization"

**Cấu hình kết nối với APIM**

.. code-block:: bash

    [[service_catalog]]
    apim_host = "https://apim.cpc.vn"
    enable = true
    username = "<your_mi_username>"
    password = "<your_mi_password>"

.. note::

    Tắt chuyển tự động chuyển đổi giờ hệ thống thành giờ UTC trước khi insert dữ liệu và DB (nếu cần) bằng cách thêm tham số `-Ddss.legacy.timezone.mode=true` 
    vào file `<MI_HOME>/bin/micro-integrator.sh`

Config MI Dashboard
-------------------

Update file `<MI_DASHBOARD_HOME>/conf/deployment.toml`, thêm các config sau

.. code-block:: bash

    [server_config]
    port = 9743

    [heartbeat_config]
    pool_size = 15

    [mi_user_store]
    username = "admin"
    password = "admin"

    [keystore]
    file_name = "conf/security/dashboard.jks"
    password = "wso2carbon"
    key_password = "wso2carbon"

Cập nhật Nginx proxy
--------------------

.. code-block:: nginx

    upstream ei.service {
    server 10.72.120.18:8290;
    }

    upstream ssl.ei.service {
        server 10.72.120.18:9743;
    }

    server {
        listen 80;
        server_name ei.cpc.vn;
        
        location / {
            proxy_set_header X-Forwarded-Host $host;
            proxy_set_header X-Forwarded-Server $host;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_read_timeout 5m;
            proxy_send_timeout 5m;
            proxy_pass http://ei.service;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }
    }

    server {
        listen 443 ssl;
        server_name ei.cpc.vn;
        ssl_certificate /etc/nginx/ssl/cpc-chain.pem;
        ssl_certificate_key /etc/nginx/ssl/cpc-chain.pem;
        
        location / {
            proxy_set_header X-Forwarded-Host $host;
            proxy_set_header X-Forwarded-Server $host;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_read_timeout 5m;
            proxy_send_timeout 5m;
            proxy_pass https://ssl.ei.service;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
        }
    }

Truy cập   `WSO2 MI Dashboard 4.1.0 <https://ei.cpc.vn>`_

.. important::
    
    Cấu hình Logging tham khảo thêm chi tiết tại `đây <https://apim.docs.wso2.com/en/latest/observe/micro-integrator/classic-observability-logs/configuring-log4j2-properties/>`_