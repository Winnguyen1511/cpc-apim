Java
============

Cài đặt Java là bước quan trọng trong việc cài đặt CPC-APIM. 

.. note::

   Phần hướng dẫn này tập trung vào OpenJDK Java 11. Ngoài ra, có thể cài đặt OpenJDK Java 8 hoặc các phiên bản 
   Java khác, xem thêm tại trang của `WSO2 <https://apim.docs.wso2.com/en/latest/install-and-setup/setup/reference/product-compatibility>`_.

Cài đặt JDK
-----------
Sử dụng ``apt`` để cài đặt OpenJDK Java 11

.. code-block:: bash

   sudo apt install openjdk-11-jdk

Setup JAVA_HOME
---------------

Export JAVA_HOME theo đúng đường dẫn mà Java JDK được cài đặt.

.. code-block:: bash

   # In your home directory, update the BASHRC file, example ~/.bashrc
   # Assuming JDK location is /usr/lib/jvm/java-11-openjdk-amd64
   export JAVA_HOME=/usr/lib/jvm/java-11-openjdk-amd64
   export PATH=${JAVA_HOME}/bin:${PATH}