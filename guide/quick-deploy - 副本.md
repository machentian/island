
## GitHub Pages

参考： [https://docsify.js.org/#/zh-cn/deploy](https://docsify.js.org/#/zh-cn/deploy)

使用`GitHub Pages`部署该文档项目非常简单，这里假定你已经有了`GitHub`账号，没有的话，注册一下。

首页

1.上方显示主机名称

2.联网时，联网图标显示

3.断网时，断网图标显示

4.最小化按钮。

5.最大化按钮。

6.有密码时最小化按钮。

7.设置里面点击数据同步正常，显示最新的同步时间。

8.设置里面断网情况下数据同步报错。

9.点击按钮跳转到登录界面。

登录界面

1.刷卡，有工作中心情况下直接登录。

2.刷卡，没有工作中心情况下报警告。

3.刷卡，用户配置多个工作中心情况下，勾选工作中心操作。

4.取消返回首页，登录跳转到主页。

5.配置界面配置摄像头后上方有个摄像头框。

6录像功能在我的文档会有视频，能正常播放。

主页

1.上方显示卡号，工作中心，用户名。最大化，最小化功能正常。

2.左侧按钮正常显示。正常跳转。

3.调整用户角色，同步数据，重新登录，可以看到左侧按钮权限的改变。

4.退出登录按钮调整到首页。

5.中间表格数据正常显示，数量ok。滚动条功能正常。
取料

1.标题栏正确显示。

2.搜索框能正确搜索。

3.中间显示15个物料，能显示图片，能点击（库存为0除外）。

4.下方返回按钮工作正常。能正常分页。

5.点击图片，可以加入购物车，可以直接取料。

6.购物车支持多取料，可以跨页面添加购物车，同一个物料无法重复加购物车。支持购物车取消物料。购物车上方显示红色的物料数量。

7.选物料，打开仓门后，称重提示，

8.取料，关闭仓位，读数，自动读秒退出，

9.购物车取料，支持跨仓门取料。

10.取料时出现数量异常情况，提示，重新打开仓位。

11.取料时没有关闭里面的仓位，直接关闭外面的仓门，提示，并且重新打开仓门。关闭后，如果取一个物料时数量也是对的。
补料

1.空仓位补料，支持一次补多个料。界面上多点点，几种情况特殊情况。
盘点，清库存，重置仓位功能正常。
机器断网情况，正常的取料操作。数据不上传。

下一次联网后，再取一次料，数据上传。



- 第一步：`Fork`我当前`island`仓库，即 [https://github.com/bytesfly/island](https://github.com/bytesfly/island)

![](https://img2020.cnblogs.com/blog/1546632/202111/1546632-20211112104413154-992050666.png)

- 第二步：在刚`Fork`的仓库设置(`Settings`)页面开启`GitHub Pages`功能

![](https://img2020.cnblogs.com/blog/1546632/202111/1546632-20211112104758702-781100833.png)

然后，你就可以打开`https://<yourname>.github.io/island`看看效果了。


## 本地部署

> [!NOTE|label:Question]
> 如何在本地编辑文档并实时预览效果呢？

- 第一步： 克隆文档项目

仓库所在地址： [https://github.com/bytesfly/island](https://github.com/bytesfly/island)
```bash
git clone git@github.com:bytesfly/island.git
```

- 第二步： 安装启动nginx

`Linux`系统：
```bash
# 安装
sudo apt-get install nginx

# 查看状态
sudo systemctl status nginx

# 启动
sudo systemctl start nginx
```

`Windows`系统(待补充)：
```bash
# TODO
```
如果安装启动成功，浏览器打开 [http://localhost/](http://localhost/) ，可见如下界面：

![](images/nginx-20211105143122.jpg)

- 第三步： 配置nginx

`Linux`系统：

```bash
# 进入nginx配置目录
cd /etc/nginx/conf.d

# 创建新配置
sudo touch island.conf
```
然后打开`island.conf`，添加如下内容：
```text
server {
  listen 12345;
  root /home/bytesfly/proj/island;
  index index.html;
  location ~* ^.+\.(jpg|jpeg|gif|png|ico|css|js|pdf|txt){
    root /home/bytesfly/proj/island;
  }
}
```
其中`root`后面配置的是刚才克隆的`island`项目绝对路径。

再执行命令让`nginx`重新加载：
```bash
sudo nginx -s reload
```
浏览器打开 [http://localhost:12345/](http://localhost:12345/) ,如下

![](https://img2020.cnblogs.com/blog/1546632/202111/1546632-20211112100936828-238827122.png)

> [!WARNING]
> 此时，用你喜欢的本地编辑器编写`Markdown`文档并保存，浏览器刷新页面(`Ctrl + F5`)即可实时预览效果。

## 补充Docker部署

当然，如果本地有Docker环境，也可使用Docker部署。下面以`docker-compose`为例。

下面是整体目录结构，当前目录下有`docker-compose.yml`文件和`conf.d`文件夹，`conf.d`文件夹下有`island.conf`文件。

```bash
➜  ~ tree
.
├── conf.d
│   └── island.conf
└── docker-compose.yml

1 directory, 2 files
```

`docker-compose.yml`文件内容如下：

```yaml
version: '3.9'
services:
  nginx:
    image: nginx:1.20.1
    volumes:
      - ./conf.d:/etc/nginx/conf.d:ro
      - /home/bytesfly/proj/island:/var/www
    ports:
      - "8080:8080"
    networks:
      internal:  {}
    restart: always
networks:
  internal: {}
```

其中`/home/bytesfly/proj/island`是文档项目所在绝对路径。

`island.conf`文件内容如下：

```text
server {
  listen 8080;
  root /var/www;
  index index.html;
  location ~* ^.+\.(jpg|jpeg|gif|png|ico|css|js|pdf|txt){
    root /var/www;
  }
}
```

在`docker-compose.yml`当前目录执行如下命令：

```bash
sudo docker-compose up -d
```

如果没有其他问题的话，浏览器打开 [http://localhost:8080/](http://localhost:8080/) 查看文档。