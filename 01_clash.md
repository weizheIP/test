# Linux 下使用 Clash 科学上网

clash下载

https://www.clash.la/releases/
节点订阅

https://lmspeedapp.com/#/dashboard

节点下载

> [订阅链接]&flag=clash

# 安装clash

https://www.clash.la/releases/

- 下载

  ~~~c
  wget -O clash.gz https://github.com/Dreamacro/clash/releases/download/v1.6.5/clash-linux-amd64-v1.6.5.gz
  
  wget -O clash.gz https://down.clash.la/Clash/Core/Releases/clash-linux-arm64-v1.18.0.gz
  ~~~

- 解压缩

- https://forums.100ask.net/t/topic/2986

  ~~~bash
  gzip -dc clash.gz > /usr/local/bin/clash
  chmod +x /usr/local/bin/clash
  rm -f clash.gz
  ~~~

  

- 创建配置文件目录，并下载 MMDB 文件

  ~~~c
  mkdir /etc/clash
  wget -O /etc/clash/Country.mmdb https://www.sub-speeder.com/client-download/Country.mmdb
  ~~~

  

- 创建 `systemd` 脚本，脚本文件路径为 `/etc/systemd/system/clash.service`，内容如下：

  ~~~c
  [Unit]
  Description=clash daemon
  
  [Service]
  Type=simple
  User=root
  ExecStart=/usr/local/bin/clash -d /etc/clash/
  Restart=on-failure
  
  [Install]
  WantedBy=multi-user.target
  ~~~

  

- 重载 systemctl daemon

  ~~~c
  systemctl daemon-reload
  ~~~

# 配置代理上网

- 导入已有的科学上网`订阅链接`

  ~~~c
  wget -O /etc/clash/config.yaml [你的订阅链接]
  ~~~

  

- 设置系统代理，添加配置文件 `/etc/profile.d/proxy.sh` 并在其中写入如下内容：

  ~~~c
  export http_proxy="127.0.0.1:7890"
  export https_proxy="127.0.0.1:7890"
  export no_proxy="localhost, 127.0.0.1"
  ~~~

  

- 重载 `/etc/profile` 配置

  ~~~c
  source /etc/profile
  ~~~

  

- 启动 `clash` 服务，并设置为开机自动启

  ~~~c
  systemctl start clash
  systemctl enable clash
  ~~~

  

- 测试 goolge.com 访问

  ~~~c
  # curl google.com
  <HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
  <TITLE>301 Moved</TITLE></HEAD><BODY>
  <H1>301 Moved</H1>
  The document has moved
  <A HREF="http://www.google.com/">here</A>.
  </BODY></HTML>
  ~~~

  

# 配置 web-ui

- 克隆 [clash-dashboard](https://github.com/Dreamacro/clash-dashboard) 项目到本地

  ~~~c
  git clone -b gh-pages --depth 1 https://github.com/Dreamacro/clash-dashboard /opt/clash-dashboard
  ~~~

  

- 修改 `clash` 配置文件中 `external-ui` 的值为 `/opt/clash-dashboard`

  ~~~c
  sed -i "s/^#\{0,1\} \{0,1\}external-ui.*/external-ui: \/opt\/clash-dashboard/" /etc/clash/config.yaml
  ~~~

  

- 重启 clash 服务

  ~~~c
  systemctl restart clash
  ~~~

  

- 通过浏览器访问 `localhost:9090/ui`，其中 `localhost` 替换为 clash 部署服务器的 IP

  

# 配置定时更新订阅

- 使用如下脚本填写相关配置项目并放入 `/etc/cron.weekly` 目录下，每周自动更新订阅配置文件即可

  ~~~c
  #!/usr/bin/env bash
  
  # 订阅链接地址
  SUBSCRIBE=""
  # web-ui存放目录，留空则保持默认不修改
  WEB_UI=""
  # API 端口，留空则保持默认不修改
  CONTROLLER_API_PROT=""
  # API 口令，留空则保持默认不修改
  SECRET=""
  
  CLASH_CONFIG="/etc/clash/config.yaml"
  
  
  if [ -z "${SUBSCRIBE}" ]; then
      echo "Subscription address cannot be empty"
      exit 1
  fi
  
  systemctl stop clash
  
  wget --no-proxy -O ${CLASH_CONFIG} ${SUBSCRIBE}
  
  if [ -n "${WEB_UI}" ]; then
  sed -i "s?^#\{0,1\} \{0,1\}external-ui.*?external-ui: ${WEB_UI}?" ${CLASH_CONFIG}
  fi
  
  if [ -n "${CONTROLLER_API_PROT}" ]; then
  sed -i "s?^external-controller.*?external-controller: '0.0.0.0:${CONTROLLER_API_PROT}'?" ${CLASH_CONFIG}
  fi
  
  if [ -n "${SECRET}" ]; then
  sed -i "s?^secret.*?secret: '${SECRET}'?" ${CLASH_CONFIG}
  fi
  
  systemctl start clash
  ~~~

- 上述脚本写入 `/etc/cron.weekly/clash.sh` 并配置好相关变量后，保存退出并赋予可执行权限

  ~~~c
  chmod 0755 /etc/cron.weekly/clash.sh
  ~~~

  

