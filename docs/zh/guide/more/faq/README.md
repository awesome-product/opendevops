### FAQ

[[toc]]

### 邮箱设置问题
> 由于常见的Email有很多，这里列举下最常见的Email设置
::: details 详细信息

- Q: QQ邮箱怎么设置？
- A: QQ邮箱主要注意使用网页生成授权码进行作为密码登陆
    - [SMTP地址](https://service.mail.qq.com/cgi-bin/help?id=28&no=167&subtype=1)：smtp.qq.com
    - SMTP端口：465
    - SSL加密：True, 开启
    - 用   户: <your_qq>@qq.com
    - 密   码： 授权码（一般为16位）



- Q: 腾讯企业邮箱如何设置？
- A: 腾讯企业邮箱同QQ邮箱，唯一一点SMTP服务器不同
    - [SMTP地址](https://service.exmail.qq.com/cgi-bin/help?subtype=1&id=28&no=1000585)：smtp.exmail.qq.com
    - SMTP端口：465
    - SSL加密：True, 开启
    - 用   户: <your_name>@domain.com
    - 密   码： 授权码（一般为16位）



- Q: 网易163邮箱如何设置？
- A: 同上，唯一一点SMTP服务器不同
    - [SMTP地址](http://help.163.com/09/1223/14/5R7P3QI100753VB8.html)：smtp.163.com
    - SMTP端口：465
    - SSL加密：True, 开启
    - 用   户: <your_name>@163.com
    - 密   码： 授权码（一般为16位）



- Q: Gmail邮箱如何设置？
- A: Gmail邮箱考虑到安全，需要注意以下2步
  -  首先开启Google 二步认证
  -  [生成Google 应用专用密码](https://support.google.com/mail/answer/185833?hl=zh-Hans)
  - [SMTP地址](https://support.google.com/mail/answer/7126229?hl=zh-Hans&visit_id=636771670247559816-1235179449&rd=2)：smtp.gmail.com
  - SMTP端口：465
  - SSL加密：True, 开启
  - 用   户: <your_name>@gmail.com
  - 密   码： 授权码（一般为16位）
:::



### Docker Bulid 报错问题
::: details 详细信息
- 安装依赖报错：`Could not install packages due to an EnvironmentError'`

```
错误信息：
Step 10/16 : RUN pip3 install --upgrade pip
---> Running in 83716c526776

Collecting pip
Downloading https://files.pythonhosted.org/packages/c2/d7/90f34cb0d83a6c5631cf71dfe64cc1054598c843a92b400e55675cc2ac37/pip-18.1-py2.py3-none-any.whl (1.3MB)
Installing collected packages: pip
Found existing installation: pip 10.0.1
Uninstalling pip-10.0.1:
Successfully uninstalled pip-10.0.1
Could not install packages due to an EnvironmentError: [Errno 2] No such file or directory: '/tmp/pip-uninstall-y8n2hlf9/usr/local/bin/pip3'

The command '/bin/sh -c pip3 install --upgrade pip' returned a non-zero code: 1
```


- 解决办法：
```shell

Python3x版本后Docker里面需要加入--user的参数，修改Dockerfile, 加上--user参数， 如：pip3 install --user --upgrade pip
```
:::



### 常见的错误码error code排错思路

::: details 详细信息

- 404：  一般都是自己修改了域名或者配置错误，导致网关转发的时候没办法找到
- 500：  每个模块服务的后端报错，具体可参看报错模块日志： /var/log/supervisor/
- 502：  502错误是网关配置错误，请仔细检查网关配置和DNS配置，确保域名正常解析，且能访问。
:::

### Google Authenticator怎么使用?
> 安全一定是最重要的，相信很多人都知道谷歌身份验证器，这里简单说下Google身份验证器怎么使用, 本平台Google验证码密钥是发送到用户邮箱的。
::: details 详细信息
**下载谷歌验证器**
- iOS用户登录AppStore搜索"Authenticator"
- 安卓用户登录应用商店或利用手机浏览器搜索“谷歌验证器”下载,也可[点击下载](https://www.wandoujia.com/apps/com.google.android.apps.authenticator2)

**添加所需网站验证码**

`PS 由于国内Android多数都被阉割了Google框架，不能扫码的输入手动贴入密钥`

- 扫描条形码  

![](/google_auth01.jpg)

- 手动输入密钥  

![](/google_auth02.jpg)


**手机丢失怎么办**

1. 你可以搜索你之前的邮件记录，或者自行记录保存下来
2. 对于CODO超级管理员可以登陆数据库自行查看，SQL语法：`select google_key from codo_admin.mg_users where username='codo_test';`

:::

### Login界面/登陆后一直自动刷新/闪屏/退出？

::: details 详细信息
Q：可以登陆进去，但是一直闪屏刷新/退出？
  
A：出现这个问题是因为网关`token_secret`和`codo-admin`里面`token_secret`配置不一样，改成一样即可。
请仔细阅读网关配置中的注册API网关文件 `configs.lua`这块
:::
  
###  Docker内部的MySQL、Redis无法连接问题  

> 有些同学发现在Docker内部无法连接到外面的MySQL，如下报错，一般都是防火墙引起的，防火墙不能关闭，因为NAT在用，清空掉Filter链 `iptables -F`或者添加上3306端口尝试下。  
::: details 详细信息
![](/243fb74c5a73af046696423d6ce7b92.png)

:::

### 网关提示域名gw.opendevops.cn找不到，或者网络不通
```
iptables -F
### 这个是环境准备时候部署的dnsmasq所在的地址，请睁大眼睛不要写错了
echo "nameserver 127.0.0.1" > /etc/resolv.conf   
service dnsmasq restart
systemctl restart docker.service
ntpdate ntp1.aliyun.com
```
### 新部署的系统无法创建User？没办法看到创建/重置/获取Token？  

::: details 详细信息
Q：新部署的时候登陆进去，点击用户列表没办法创建/看不到重置密码/重置MFA按钮等问题？ 

A：这个问题是因为没有创建角色，默认部署的时候权限/组件数据都是初始化进去的，但是角色需要用户自己创建，用户可以点击**用户管理**-->**角色管理**  新建一个角色，给所有组件/菜单/权限  然后退出，强刷下浏览器就可以了  

操作步骤可参考：https://docs.opendevops.cn/zh/guide/more/permission/

:::

### 如何在 CODO 上开发新应用

> 基于CODO的开发框架进行开发新应用，前端、后端怎么开发和注册
::: details 详细信息

Q：前端怎么开发接入？

1. CODO 使用 qiankun 微前端架构进行模块集成。
2. 使用CODO官方模板：[codo-subapp-tpl 前端模板]((https://github.com/opendevops-cn/codo-subapp-tpl))
3. 开发完成后，将子应用注册到 CODO 主框架中，关于注册和通信方式，详见：[微前端开发文档]((https://github.com/opendevops-cn/codo-subapp-tpl)) 
   
Q：后端怎么开发接入？

1. 后端服务应提供标准 RESTful 接口，并通过 天门网关 接入 CODO。
2. 使用任意后端框架（如 SpringBoot / Node.js / Flask）
3. 接口路径规范化，并配置到天门网关中
4. 配置参考文档 [天门文档](https://github.com/opendevops-cn/codo-gateway)

Q：应用展示与注册？
> 将你的应用注册到 CODO 管理后台即可在主界面展示。

1. 登录 CODO 管理后台
2. 进入 应用管理 页面，添加你的应用信息
3. 具体配置字段详见： [用户手册]((https://github.com/opendevops-cn/codo-admin/blob/main/docs/codo-admin.md))。

**📚 其他参考资料** 

- 🔧 [qiankun 微前端框架](https://github.com/umijs/qiankun)

::: 

### Docker compose 的部署如何更新最新版本
::: details 详细信息

1. 更新镜像并重启

```bash
docker compose -f docker-compose-app.yaml pull
docker compose -f docker-compose-app.yaml up -d
```

2. 更新后报500错误，一般是字段发生变更需要删表重建或者自己补齐字段 

:::



### 如何获取参数与上下文传递变量

在流程自动化和脚本执行中，获取参数与传递变量是常见需求。以下介绍从不同来源获取参数并在上下文中传递变量的方法。
::: details 详细信息

#### 1. 生成流程过程变量

通过脚本输出标准化的格式，以便后续从日志中提取变量。约定格式为： 

- 以 ###SOF### 开头，###EOF### 结尾。
- 内容为合法的 JSON 字符串。

**示例代码（Python）:**
```python 
#!/usr/bin/env python

import json

# 定义变量
data = {
    "name": "John Doe",
    "age": 30,
    "is_employee": True
}

# 输出标准化数据
print(f"###SOF###{json.dumps(data)}###EOF###")
```
注意:

- 日志处理程序需提取最后一组匹配 ###SOF### 和 ###EOF### 的内容。

- 确保输出为合法 JSON 格式，使用双引号以符合 JSON 标准。

#### 2. 无论使用何种语言，都可以从环境变量或上一步输出中获取上下文数据。以下是不同场景的实现方式：

> FLOW_ID 和 NODE_ID 属于系统内置的变量可以直接获取
> 环境变量 需要加codo_前缀

**示例代码（Shell/Bash）:**
```bash
#!/bin/bash

# 打印所有环境变量
echo "==== 环境变量 ===="
printenv

echo -e "------------\n\n"
# 系统内置的可以直接取
echo "订单的ID: ${FLOW_ID}"
echo "当前任务节点的ID: ${NODE_ID}"
echo "当前任务执行的agent的crc32之后agent_id: ${crc32_agent_id}"
echo -e "------------\n\n"
# 环境变量 需要加codo_前缀
echo "上个节点输出的名称: ${codo_name}"

```

**示例代码（Python）:**
```python
import os

# 获取环境变量
flow_id = os.getenv("FLOW_ID")
node_id = os.getenv("NODE_ID")
crc32_agent_id = os.getenv("crc32_agent_id")
codo_name = os.getenv("codo_name")

# 输出环境变量信息
print("==== 环境变量 ====")
print(f"订单的 ID: {flow_id}")
print(f"当前任务节点的 ID: {node_id}")
print(f"Agent 的 CRC32 编码后 ID: {crc32_agent_id}")
print(f"上个节点输出的名称: {codo_name}")

```


#### **tips: 在自动化流程中，生成唯一标识很重要。以下组合能确保标识的唯一性：**

- FLOW_ID + NODE_ID：保证全局唯一性，用于标识同一流程中不同节点。
- FLOW_ID + NODE_ID + crc32_agent_id：在批量执行场景中，可进一步区分任务实例。
  

:::