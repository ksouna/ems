# 联通用户管理系统（EMS）

基于 Django + MySQL 的简易用户信息管理系统，包含部门、用户、靓号三个管理模块。

## 功能

- **部门管理**：列表、新增、编辑、删除
- **用户管理**：列表、新增、编辑、删除（基于 ModelForm）
- **靓号管理**：列表、新增、编辑、删除
  - 支持按号码模糊搜索（`?q=关键字`）
  - 支持分页，每页 4 条（首页 / 上一页 / 页码 / 下一页 / 尾页 / 页码跳转）
  - 号码校验：必须为 11 位数字，且不允许重复
- 公共代码：表单基类、分页工具、分页组件、表单字段组件均已提取复用

## 技术栈

- Python 3.12
- Django 6.1
- MySQL
- Bootstrap 3.4.1（本地静态资源）

## 目录结构

```
ems/
├── manage.py
├── ems/                        # 项目配置
│   ├── settings.py             # 数据库、静态文件等配置
│   └── urls.py                 # 总路由
├── app01/                      # 业务应用
│   ├── models.py               # Department / UserInfo / PrettyNum
│   ├── forms.py                # 公共 ModelForm 基类 + 业务表单
│   ├── views.py                # 部门 / 用户 / 靓号视图
│   ├── utils.py                # 公共分页工具 paginate()
│   ├── templatetags/
│   │   └── pagination.py       # 分页地址标签 page_url
│   ├── templates/
│   │   ├── includes/
│   │   │   ├── form_fields.html    # 公共表单字段渲染组件
│   │   │   └── pagination.html     # 公共分页组件
│   │   ├── layout.html         # 公共布局
│   │   └── ...                 # 各业务页面
│   ├── static/                 # Bootstrap、jQuery 等静态资源
│   ├── migrations/             # 数据库迁移文件
│   └── sql/user_data.sql       # 用户数据脚本
└── README.md
```

## 快速开始

1. 安装依赖：

   ```bash
   pip install django mysqlclient
   ```

2. 创建数据库：

   ```sql
   CREATE DATABASE ems DEFAULT CHARACTER SET utf8mb4;
   ```

3. 修改 `ems/settings.py` 中的 `DATABASES` 配置（数据库名、用户名、密码）。

4. 执行迁移并启动：

   ```bash
   python manage.py migrate
   python manage.py runserver
   ```

5. 浏览器访问：

   - 部门管理：<http://127.0.0.1:8000/depart/list/>
   - 用户管理：<http://127.0.0.1:8000/userlist/>
   - 靓号管理：<http://127.0.0.1:8000/pretty/list/>

## 主要路由

| 路径 | 功能 |
| --- | --- |
| `/depart/list/` | 部门列表 |
| `/depart/add/` | 新增部门 |
| `/depart/edit/?nid=1` | 编辑部门 |
| `/depart/delete/?nid=1` | 删除部门 |
| `/userlist/` | 用户列表 |
| `/user/add/` | 新增用户 |
| `/user/edit/?nid=1` | 编辑用户 |
| `/user/delete/?nid=1` | 删除用户 |
| `/pretty/list/` | 靓号列表（支持 `?q=搜索词&page=页码`） |
| `/pretty/add/` | 新增靓号 |
| `/pretty/edit/?nid=1` | 编辑靓号 |
| `/pretty/delete/?nid=1` | 删除靓号 |

## 公共代码说明

- **`BootstrapModelForm`**（`app01/forms.py`）：公共 ModelForm 基类，自动为所有字段添加 `form-control` 样式，子类只需关注字段与校验。
- **`UserBaseModelForm`**（`app01/forms.py`）：用户新建 / 编辑表单的公共配置，消除重复代码。
- **`paginate()`**（`app01/utils.py`）：公共分页工具，统一处理页码非法、越界等异常。
- **`page_url`**（`app01/templatetags/pagination.py`）：生成翻页地址时保留当前查询参数（如搜索词 `q`），只替换 `page`。
- **`includes/pagination.html`**：可复用的分页组件，任意列表页 `{% include %}` 即可使用。
- **`includes/form_fields.html`**：可复用的表单字段渲染组件，统一 label、控件与错误提示。

## 说明

- 项目为本地开发配置（`DEBUG = True`），数据库密码与 `SECRET_KEY` 仅用于学习环境。
- 部署到生产环境前需关闭 `DEBUG`、配置 `ALLOWED_HOSTS`，并通过环境变量管理敏感信息。
- 项目未实现登录鉴权，导航栏中的登录入口仅为占位。
