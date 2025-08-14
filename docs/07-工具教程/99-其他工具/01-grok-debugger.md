# 1、简介

这个客户端应用程序使用 WebAssembly 在浏览器中模拟 logstash grok 库。它响应迅速、功能丰富，能帮助你快速调试你的 grok 模式。虽然它在大多数情况下运行良好，但它并不是 logstash grok 的精确移植，所以在部署前务必在你的环境中测试你的模式。

**功能：**

- 实时处理（边输入边看到变化）
- 自动补全
- 匹配高亮
- 语法高亮
- 多行调试
- 20+ 模式集（AWS、Grok、防火墙、Java 等）
- 通过 URL 添加更多模式集
- 保存并使用您自己的自定义模式

**限制：**

- 不支持在命名中使用嵌套对象，例如 `%{IP:user.ip}` 或 `<%{NONNEGINT:[log][syslog][priority]}>`
- 不支持显式定义的输出类型，例如 `%{NUMBER:status:int}`
- 不支持行内标志修饰符，例如 `(?i)opid=%{NOTSPACE:event_operation_id}` [#6](https://github.com/cjslack/grok-debugger/issues/6)



**项目地址：**https://github.com/cjslack/grok-debugger

![image-20250814161324966](https://cdn.jsdelivr.net/gh/xmtxsec/picture/imgl/202508141614803.png)



# 2、环境搭建

**1、安装[node.js](https://nodejs.org/en)和npm**

```
# 解压下载好的安装包
tar -xzf node-v16.20.2-linux-x64.tar.gz

# 移动到系统目录
sudo mv node-v16.20.2-linux-x64 /usr/local/nodejs

# 创建符号链接，让 node 和 npm 全局可用
sudo ln -s /usr/local/nodejs/bin/node /usr/bin/node
sudo ln -s /usr/local/nodejs/bin/npm /usr/bin/npm
sudo ln -s /usr/local/nodejs/bin/npx /usr/bin/npx

# 验证安装
node --version
npm --version
```



**2、安装 PM2（进程管理工具）**

```
sudo npm install -g pm2 --registry https://registry.npmmirror.com

# 验证 PM2 是否安装成功
pm2 --version

# 如果提示 command not found，请参考前面创建符号链接的方法：
sudo ln -s /usr/local/nodejs/bin/pm2 /usr/bin/pm2
```



**3、准备项目文件**

```
cd /opt
git clone https://github.com/cjslack/grok-debugger.git
```



**4、安装项目依赖**

```
cd /opt/grok-debugger
npm install --registry https://registry.npmmirror.com
```



**5、使用 PM2 运行项目**

```
pm2 start npm --name "grok-debugger" -- start

# 查看状态
pm2 list

# 设置开机自启
pm2 startup

# 按提示执行输出的 sudo 命令
pm2 save
```



**6、扩展pm2命令**

```
查看进程：pm2 list
停止应用：pm2 stop <name> 或 pm2 stop <id>
删除应用：pm2 delete <name>
停止所有：pm2 stop all
删除所有：pm2 delete all
```



# 3、打包为Docker镜像

**1、创建构建目录**

```
mkdir /tmp/grok-docker-build
cd /tmp/grok-docker-build
```



**2、复制必要文件（不包含无关内容）**

```
cp /opt/grok-debugger/package*.json ./
cp -r /opt/grok-debugger/node_modules ./
cp -r /opt/grok-debugger/public ./
cp -r /opt/grok-debugger/src ./
```



**3、创建 `Dockerfile`**

```
nano Dockerfile
```

粘贴以下内容：

```
FROM node:16.20.2-alpine

WORKDIR /app

ENV NODE_ENV=production

# 复制依赖声明
COPY package*.json ./

# 复制已安装的 node_modules（跳过 npm install）
COPY node_modules ./node_modules

# 复制源码
COPY . .

EXPOSE 3000

# 创建非 root 用户
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001 && \
    chown -R nodejs:nodejs /app
USER nodejs

# 使用局部 PM2 启动（无需全局安装）
CMD ["node_modules/.bin/pm2-runtime", "npm", "--", "start"]
```



**4、构建 Docker 镜像**

```
docker build -t grok-debugger:latest .
```



**5、验证镜像运行**

```
docker run -d -p 3000:3000 --name grok-test grok-debugger:latest
docker logs grok-test
```



# 4、上传镜像到DockerHub

**1、登录到 Docker CLI**

```
docker login
```

输入 Docker Hub 用户名和密码：

```
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: yourusername
Password: *******
Login Succeeded
```



**2、给镜像打标签**

Docker 镜像要上传到 Docker Hub，必须打上 **`username/repository:tag`** 格式的标签

查看当前的镜像：

```
docker images | grep grok-debugger
# 输出类似：
# grok-debugger                 latest           59485481e51c   About an hour ago   1.42GB
```

重新打标签：格式：<你的用户名>/<项目名>:<版本>

```
docker tag grok-debugger:latest yourusername/grok-debugger:latest
```



**3、推送镜像到 Docker Hub**

```
docker push yourusername/grok-debugger:latest
```



