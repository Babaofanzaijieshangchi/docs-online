# Docsify 部署到 Nginx 步骤

## 准备工作

1. **确保 Nginx 已安装**
   ```bash
   # Ubuntu/Debian
   sudo apt update && sudo apt install nginx

   # CentOS/RHEL
   sudo yum install epel-release && sudo yum install nginx

   # macOS
   brew install nginx
   ```

2. **验证 Nginx 安装**
   ```bash
   nginx -v
   ```

## 部署步骤

### 步骤 1: 复制 Docsify 项目文件

将 `docs` 目录下的所有文件复制到 Nginx 的网站根目录（通常是 `/usr/share/nginx/html`）：

```bash
# 复制项目文件到 Nginx 根目录
sudo cp -r /path/to/your/docsify/docs/* /usr/share/nginx/html/

# 给 Nginx 用户添加权限
sudo chown -R nginx:nginx /usr/share/nginx/html/
sudo chmod -R 755 /usr/share/nginx/html/
```

### 步骤 2: 配置 Nginx

1. **创建 Nginx 配置文件**
   ```bash
   sudo vi /etc/nginx/conf.d/docsify.conf
   ```

2. **粘贴以下配置**
   ```nginx
   server {
       listen       80;
       server_name  your-domain.com;  # 替换为你的域名或IP

       root /usr/share/nginx/html;
       index index.html index.htm;

       # 处理单页应用路由
       location / {
           try_files $uri $uri/ /index.html;
       }

       # 静态文件缓存设置
       location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
           expires 1y;
           add_header Cache-Control "public, max-age=31536000";
       }

       error_page  404  /_404.md;
   }
   ```

### 步骤 3: 测试配置

```bash
# 测试 Nginx 配置是否正确
sudo nginx -t
```

### 步骤 4: 重启 Nginx

```bash
# 重启 Nginx 服务
sudo systemctl restart nginx

# 或者
sudo service nginx restart
```

### 步骤 5: 验证部署

在浏览器中访问你的域名或服务器IP，确认 Docsify 网站正常运行：
```
http://your-domain.com
# 或
http://your-server-ip
```

## 高级配置

### HTTPS 配置（使用 Let's Encrypt）

```bash
# 安装 Certbot
sudo apt install certbot python3-certbot-nginx

# 获取 SSL 证书
sudo certbot --nginx -d your-domain.com
```

### 启用 Gzip 压缩

在 Nginx 配置中添加：

```nginx
gzip on;
gzip_types text/plain text/css application/json application/javascript text/xml application/xml text/javascript;
```

### 启用 HTTP/2

在 `listen` 指令中添加 `http2`：

```nginx
listen 443 ssl http2;
```

## 常见问题

### 问题 1: 404 错误
- 确保 `try_files $uri $uri/ /index.html;` 配置正确
- 检查文件权限是否正确

### 问题 2: 样式或脚本不加载
- 检查文件路径是否正确
- 确保静态文件权限正确

### 问题 3: Nginx 无法启动
- 检查配置文件语法：`nginx -t`
- 检查端口是否被占用：`lsof -i :80`

## 本地开发测试

如果你想在本地测试 Nginx 配置：

```bash
# 复制配置文件
cp nginx.conf.example /usr/local/etc/nginx/nginx.conf

# 启动 Nginx
brew services start nginx

# 访问
http://localhost
```
