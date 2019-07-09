# nginx

## 使用nginx部署web页面

* 启动nginx

* 配置nginx
  
  - cd /etc/nginx/site-enabled 进入nginx配置
  
  - rm 之前default可执行文件

  - 创建一个.h5文件， 如： test.h5

  - vim test.h5，把下面的内容拷贝到文件中去

  ```
  server {
    listen      0.0.0.0:7777;    //端口
    charset     utf-8;  //格式
    access_log off;  //日志
    root   /opt/work/brilljoy/qp/common-client;
    //前端代码目录：可以根据自己放置的位置调整
    index  index.html index.htm index.php; // 主页面

    location ~ /.*res/ {
      try_files $uri /ResourcesFull/$uri =404;
    }
  }
  ```

  - sudo nginx -s reload 重新加载nginx配置

* 启动页面： 打开浏览器，链接到相应的位置。例如：127.0.0.1:7777



