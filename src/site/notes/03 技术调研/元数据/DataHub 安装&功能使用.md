---
{"dg-publish":true,"permalink":"/03 技术调研/元数据/DataHub 安装&功能使用/","noteIcon":"","created":"2024-01-08T15:19:18.714+08:00","updated":"2024-01-09T20:30:16.783+08:00"}
---

> 本文参考 https://datahubproject.io/docs/quickstart#install-the-datahub-cli
## 1.安装DataHub CLI
```
python3 -m pip install --upgrade pip wheel setuptools  
python3 -m pip install --upgrade acryl-datahub  
datahub version
```
![image.png](https://s2.loli.net/2024/01/08/WeA2ISmM4xjJw7G.png)
## 2.安装DataHub 
我本地环境是m1芯片所以增加 **--arch m1** 参数
```
datahub docker quickstart --arch m1
```
> 因为网络原因 registry-1.docker.io 拉取镜像会超时 通过下面方式解决
> i。通过命令 dig @114.114.114.114 registry-1.docker.io 找到对应IP 放到hosts文件中
> ii。科学上网工具调整区域最终拉取镜像成功
![image.png](https://s2.loli.net/2024/01/09/kvJbA97QrPKE1Cs.png)
## 3.访问DataHub 
> 访问http://localhost:9002/ 账号密码默认均为datahub

看到下面的界面则为安装成功
![image.png](https://s2.loli.net/2024/01/09/ClieT4EtjHY2dcM.png)
## 4. 通过产品UI配置元数据采集
进入元数据采集模块
![image.png](https://s2.loli.net/2024/01/09/eRNcrkiCdhuO17z.png)
配置数据源
![image.png](https://s2.loli.net/2024/01/09/a5JjN8xdSRIfFYP.png)
元数据采集实例
![image.png](https://s2.loli.net/2024/01/09/HCLYXFeJc3twfdz.png)
## 5. 通过Cli配置元数据采集
安装mysql采集器
```
pip install 'acryl-datahub[mysql]'
```
![image.png](https://s2.loli.net/2024/01/09/dJO1rQqpDo4NmCE.png)

> 安装过程中报错
> ![image.png](https://s2.loli.net/2024/01/09/KHtdbuzYEAqxGJ6.png)
> 是这个错误信息来自于使用 Meson 构建系统时出现的问题。Meson 是一个项目构建系统，用于编译和生成代码。错误信息表明 Meson 无法识别或找到列出的 Fortran 编译器（`gfortran`, `flang`, `nvfortran`, `pgfortran`, `ifort`, `ifx`, `g95` 等）
> 因为我是mac m1 芯片所以使用 brew install gcc 命令安装 GCC，其中包括 `gfortran`。


否则报错如下
![image.png](https://s2.loli.net/2024/01/09/yphIGS4VWfJj7vN.png)
创建采集器的配置文件
```
source:
  type: mysql
  config:
    # Coordinates
    host_port: 192.168.1.180:3306
    database: simba

    # Credentials
    username: root
    password: xxx

    # If you need to use SSL with MySQL:
    # options:
    #   connect_args:
    #     ssl_ca: "path_to/server-ca.pem"
    #     ssl_cert: "path_to/client-cert.pem"
    #     ssl_key: "path_to/client-key.pem"

sink:
  type: "datahub-rest"
  config:
    server: "http://localhost:8080"
```
通过datahub cli 执行元数据采集
