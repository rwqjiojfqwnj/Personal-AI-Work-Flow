# 使用场景
服务部署在测试环境后，ai没法直接读取日志，每次都要人工下载日志后，让ai根据日志定位，本文通过ssh-mcp-server接入linux，直接读取日志

# 操作步骤

## 1.在服务器上创建只读账号
```
# 创建用户
sudo useradd -m -s /bin/bash mcp-log-reader

# 只给日志目录的读权限
sudo setfacl -R -m u:mcp-log-reader:r-x /var/log/your-app/
sudo setfacl -R -m u:mcp-log-reader:r-- /var/log/your-app/*.log
```

## 2.安装ssh-mcp-server
让ai安装ssh-mcp-server
```
帮我配置 ssh-mcp-server
```
## 3.配置SSH密钥
```
ssh-keygen -t ed25519 -C "your_email@example.com" -f C:\用户\.ssh\id_ed25519_mcp
```





