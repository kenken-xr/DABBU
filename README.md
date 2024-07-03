# 功能设计

![功能设计](img/功能设计.png)

# 进度

- [x] 登录(注册)
  - [x] 手机号登录
  - [x] 密码登录
- [ ] 我的
  - [ ] 主页展示
    - [x] 个人资料
    - [x] 动态
    - [ ] 点赞、评论、收藏、浏览记录(待评估)
  - [x] 设置
    - [x] 账号与安全
    - [ ] 隐私设置
    - [x] 通知设置
  - [x] 编辑资料
    - [x] 头像、背景修改
    - [x] 昵称、签名修改
    - [x] 出生日期、所在城市、性别选择
    - [x] 标签修改
- [x] 关注、粉丝、互关
  - [x] 基本列表项
  - [x] 关注、取关等交互
  - [x] 置顶、修改备注等交互
- [ ] 社区
  - [x] 主要列表项(社区、关注、附近)
  - [ ] 列表项展示
    - [x] 图片视频组件封装
    - [x] 圈子、话题组件封装
    - [x] 点赞、关注等交互
    - [ ] 详情页
      - [x] 顶部基本信息
      - [x] 主要内容区域
      - [ ] 底部评论(待评估)
- [x] [im聊天](./docx/im聊天.md)
  - [ ] 聊天列表
  - [ ] 聊天界面
- [ ] 寻搭
- [x] [缺省图、基类封装、错误重试](./docx/基类封装.md)
  - 网络请求基于Retrofit+Dio实现，配合官方JsonSerialize实现解析
  - 网络请求的基础封装
  - 封装项目页面多状态（加载中，成功，失败，空数据
- [ ] UI
  - [x] [自定义圆弧指示器](./docx/自定义圆弧indicator.md)
  ![自定义圆弧指示器](/img/ui_widget/圆弧.png)

# 部分截图

已实现

<p align = "center">    
  <img src="img/登录.jpg" width="30%" />
  <img src="img/个人主页.jpg" width="30%" />
  <img src="img/个人资料编辑.jpg" width="30%" />
</p>

<p align = "center">    
  <img src="img/设置.jpg" width="30%" />
  <img src="img/通用设置.jpg" width="30%" />
  <img src="img/通知设置.jpg" width="30%" />
</p>

<p align = "center">    
  <img src="img/修改密码.jpg" width="30%" />
  <img src="img/账号与安全.jpg" width="30%" />
</p>

<p align = "center">    
  <img src="img/标签选择.jpg" width="30%" />
  <img src="img/查看他人主页.jpg" width="30%" />
  <img src="img/动态详情页.jpg" width="30%" />
</p>

<p align = "center">    
  <img src="img/关注列表.jpg" width="30%" />
  <img src="img/关注设置.jpg" width="30%" />
  <img src="img/社区.jpg" width="30%" />
</p>

<p align = "center">    
  <img src="img/视频播放.jpg" width="30%" />
  <img src="img/图片预览.jpg" width="30%" />
</p>

待优化

<p align = "center">    
  <img src="img/聊天列表.jpg" width="30%" />
  <img src="img/消息页.png" width="30%" />
</p>

<p align = "center">    
  <img src="img/聊天页.png" width="30%" />
  <img src="img/聊天界面.jpg" width="30%" />
</p>

待实现

<p align = "center">    
  <img src="img/首页寻搭2.png" width="30%" />
  <img src="img/活动详情.png" width="30%" />
  <img src="img/动态发布.png" width="30%" />
</p>

# 产品设计文档
https://lxe4xw.axshare.com/?id=3fd07a523a8e4a81addb237859e49f42