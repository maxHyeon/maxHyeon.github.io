---
layout: posts
title: "젠킨스 Ngnix Reverse Proxy 설정"
date: 2021-01-12 12:00:00 -0400
categories: jeknins
---

1. 젠킨스 Ngnix Reverse Proxy 설정 설정 방법
    - 80 Port 사용 하지 않고 설정 

2. 설정 방법
    - Nginx 설정 진행  
    - 자꾸 pluginManager 같이 몇몇 메뉴 선택시 80 포트 선택을 하게 되면서 잘 안되었다. 
    - proxy_set_header   Host              $host:21080; 설정부분이 
    
```bash
upstream jenkins {
  keepalive 32; # keepalive connections
  server 192.168.42.21:8080; # jenkins ip and port
}

# Required for Jenkins websocket agents
map $http_upgrade $connection_upgrade {
        default upgrade;
        '' close;
}
#server {
#  listen 80;
#  listen [::]:80;
#  server_name localhost fail_timeout=0;
#  return 301 localhost:21080$request_uri;
#}
server {
  listen          21080 default;       # Listen on port 80 for IPv4 requests

  server_name     192.168.42.21:8080;

  # pass through headers from Jenkins that Nginx considers invalid
  ignore_invalid_headers off;



  location / {
      sendfile off;
      proxy_pass         http://192.168.42.21:8080;
      proxy_redirect     default;
      proxy_http_version 1.1;

      # Required for Jenkins websocket agents
      proxy_set_header   Connection        $connection_upgrade;
      proxy_set_header   Upgrade           $http_upgrade;

      proxy_set_header   Host              $host:21080;
      proxy_set_header   X-Real-IP         $remote_addr;
      proxy_set_header   X-Forwarded-For   $proxy_add_x_forwarded_for;
      proxy_set_header   X-Forwarded-Proto $scheme;
      proxy_max_temp_file_size 0;

      #this is the maximum upload size
      client_max_body_size       10m;
      client_body_buffer_size    128k;

      proxy_connect_timeout      90;
      proxy_send_timeout         90;
      proxy_read_timeout         90;
      proxy_buffering            off;
      proxy_request_buffering    off; # Required for HTTP CLI commands
      proxy_set_header Connection ""; # Clear for keepalive
  }

}

```

