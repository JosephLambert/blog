# Javascript编程入门-前后端结合

**Http请求**

1. get请求：uri（唯一的网络资源标识符），应用：我想访问你的某个资源；数据轻量且公开
2. post请求：uri+参数，应用：我想把信息告诉你；数据多样化且隐蔽

**前端/后端**

1. 前端：浏览器端运行的HTML、CSS、Javascript
2. 后端：服务器端运行的业务逻辑、db

**搭建 web app/web应用/web服务器**

1. 安装NodeJS框架express：`npm isntall -g express-generator@4`
2. 建新文件夹：`first_web_app`
3. 进入文件夹：`cd first_web_app`
4. 生成项目：`express --view=ejs`
5. 安装package.json依赖：`npm install`
6. 启动web服务器：`npm start`
7. 查看默认页面`index.ejs`：`localohost:3000`

小技巧：定义函数立即执行技巧，将JSON数据放入JS中