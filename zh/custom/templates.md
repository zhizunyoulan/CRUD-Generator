## 概述

模板管理功能允许您在可视化界面中设计和管理自定义模板，包括模板文件的选择、输出目录的配置以及多客户端模板组的创建。

---

## ⚙️ 可视化模板配置

### 配置界面功能

在插件的配置页面中，您可以：

1. **选择模板文件**：从工作空间的 `templates` 目录中选择自定义模板
2. **配置输出路径**：为每个模板设置生成文件的输出目录
3. **管理模板组**：为不同客户端创建独立的模板配置
4. **预览目录结构**：实时查看生成的目录结构

### 操作流程

```
工作空间 templates 目录
        ↓
选择自定义模板文件 (.vm)
        ↓
配置输出目录结构
        ↓
设置模板条件和属性
        ↓
保存到 config.xml
```

---

## 📁 模板目录结构

### 标准目录布局

```
workspace/
├── templates/                    # 模板根目录
│   ├── backend/                 # 后端模板
│   │   ├── spring/             # Spring 框架模板
│   │   │   ├── src/main/java/__base_package__/
│   │   │   │   ├── controller/__modelName__Controller.java.vm
│   │   │   │   ├── service/__modelName__Service.java.vm
│   │   │   │   ├── model/__modelName__.java.vm
│   │   │   │   └── entity/__modelName__Entity.java.vm
│   │   │   └── src/main/resources/mybatis/
│   │   │       └── mapper/__modelName__Mapper.xml.vm
│   │   └── my-custom/          # 自定义后端模板组
│   │       ├── java-templates/
│   │       └── resource-templates/
│   └── frontend/               # 前端模板
│       ├── admin/              # 管理后台模板
│       │   ├── src/apis/__module__/__kebabCaseModelName__.ts.vm
│       │   ├── src/views/__module__/__kebabCaseModelName__/
│       │   └── src/components/__module__/
│       ├── app/                # 移动端模板
│       │   ├── pages/__modelName__/
│       │   └── components/
│       ├── mini-program/       # 小程序模板
│       │   ├── pages/__modelName__/
│       │   └── components/
│       └── my-custom-frontend/ # 自定义前端模板
│           ├── custom-components/
│           └── custom-pages/
└── config.xml                  # 配置文件
```

---

## 🎯 多客户端模板组

### 模板组配置示例

```xml
<templates location="frontend">
    <!-- 管理后台模板组 -->
    <profile id="admin" label="管理后台" device="desktop">
        <template>
            <id>ADMIN_API</id>
            <sourcePath>frontend/admin/src/apis/__module__/__kebabCaseModelName__.ts.vm</sourcePath>
            <targetPath>src/apis/__module__/__kebabCaseModelName__.ts</targetPath>
        </template>
        <template>
            <id>ADMIN_VIEW</id>
            <condition>enableRead</condition>
            <sourcePath>frontend/admin/src/views/__module__/__kebabCaseModelName__/index.vue.vm</sourcePath>
            <targetPath>src/views/__module__/__kebabCaseModelName__/index.vue</targetPath>
        </template>
    </profile>

    <!-- 移动端APP模板组 -->
    <profile id="app" label="移动端APP" device="mobile">
        <template>
            <id>APP_API</id>
            <sourcePath>frontend/app/src/api/__modelName__.ts.vm</sourcePath>
            <targetPath>src/api/__modelName__.ts</targetPath>
        </template>
        <template>
            <id>APP_PAGE</id>
            <condition>enableRead</condition>
            <sourcePath>frontend/app/src/pages/__modelName__/index.vue.vm</sourcePath>
            <targetPath>src/pages/__modelName__/index.vue</targetPath>
        </template>
    </profile>

    <!-- 微信小程序模板组 -->
    <profile id="mini-program" label="微信小程序" device="mobile">
        <template>
            <id>MINI_API</id>
            <sourcePath>frontend/mini-program/api/__modelName__.js.vm</sourcePath>
            <targetPath>api/__modelName__.js</targetPath>
        </template>
        <template>
            <id>MINI_PAGE</id>
            <condition>enableRead</condition>
            <sourcePath>frontend/mini-program/pages/__modelName__/index.wxml.vm</sourcePath>
            <targetPath>pages/__modelName__/index.wxml</targetPath>
        </template>
    </profile>
</templates>
```

### 后端模板组配置

```xml
<templates location="backend">
    <!-- MyBatis 模板组 -->
    <profile id="mybatis" label="MyBatis框架">
        <template>
            <id>MYBATIS_CONTROLLER</id>
            <sourcePath>backend/spring/mybatis/controller.java.vm</sourcePath>
            <targetPath>src/main/java/__base_package__/controller/__modelName__Controller.java</targetPath>
        </template>
        <template>
            <id>MYBATIS_MAPPER</id>
            <sourcePath>backend/spring/mybatis/mapper.java.vm</sourcePath>
            <targetPath>src/main/java/__base_package__/mapper/__modelName__Mapper.java</targetPath>
        </template>
    </profile>

    <!-- JPA 模板组 -->
    <profile id="jpa" label="JPA框架">
        <template>
            <id>JPA_CONTROLLER</id>
            <sourcePath>backend/spring/jpa/controller.java.vm</sourcePath>
            <targetPath>src/main/java/__base_package__/controller/__modelName__Controller.java</targetPath>
        </template>
        <template>
            <id>JPA_REPOSITORY</id>
            <sourcePath>backend/spring/jpa/repository.java.vm</sourcePath>
            <targetPath>src/main/java/__base_package__/repository/__modelName__Repository.java</targetPath>
        </template>
    </profile>
</templates>
```

---

## 📋 输出目录规范

### 后端输出目录结构

必须符合 Maven 项目规范：

```
src/
├── main/
│   ├── java/                   # Java 源代码目录
│   │   └── __base_package__/   # 基础包路径
│   │       ├── controller/     # 控制器层
│   │       ├── service/        # 服务层
│   │       │   └── impl/       # 服务实现层
│   │       ├── model/          # 模型层
│   │       ├── entity/         # 实体层
│   │       ├── repository/     # 数据访问层 (JPA)
│   │       ├── mapper/         # 映射层 (MyBatis)
│   │       └── dto/            # 数据传输对象
│   └── resources/              # 资源文件目录
│       ├── mybatis/            # MyBatis 映射文件
│       │   └── mapper/
│       ├── templates/          # 模板文件
│       └── application.yml     # 配置文件
└── test/                       # 测试代码目录
    └── java/__base_package__/  # 测试代码
```

### 前端输出目录结构

支持灵活的项目结构：

#### Vue 项目结构
```
src/
├── apis/                       # API 接口文件
│   └── __module__/             # 模块分组
├── views/                      # 页面组件
│   └── __module__/             # 模块分组
│       └── __kebabCaseModelName__/  # 功能页面
├── components/                 # 公共组件
│   └── __module__/             # 模块组件
├── router/                     # 路由配置
├── store/                      # 状态管理
└── utils/                      # 工具函数
```

#### 移动端项目结构
```
src/
├── pages/                      # 页面文件
│   └── __modelName__/          # 功能页面目录
├── components/                 # 组件文件
├── api/                        # API 接口
└── utils/                      # 工具函数
```

#### 小程序项目结构
```
pages/
└── __modelName__/              # 页面目录
    ├── index.wxml              # 模板文件
    ├── index.wxss              # 样式文件
    ├── index.js                # 逻辑文件
    └── index.json              # 配置文件
components/                     # 组件目录
api/                            # API 接口
utils/                          # 工具函数
```

---

## 🎨 可视化配置界面

### 界面功能特性

1. **模板文件浏览器**
   - 浏览工作空间中的模板文件
   - 支持文件过滤（只显示 .vm 文件）
   - 实时预览模板内容

2. **输出路径配置器**
   - 可视化路径选择
   - 变量自动提示（__base_package__, __modelName__ 等）
   - 路径验证和错误提示

3. **模板组管理器**
   - 创建、编辑、删除模板组
   - 拖拽排序模板文件
   - 批量操作支持

4. **条件配置器**
   - 可视化条件表达式构建
   - 可用变量自动提示
   - 条件逻辑验证

### 配置示例界面

```
[模板管理]
├── 🔧 后端模板
│   ├── 📄 Controller.java.vm    → src/main/java/.../Controller.java
│   ├── 📄 Service.java.vm       → src/main/java/.../Service.java
│   └── 📄 Mapper.xml.vm         → src/main/resources/.../Mapper.xml
├── 🎨 前端模板
│   ├── 🖥️  管理后台 (admin)
│   │   ├── 📄 api.ts.vm         → src/apis/.../api.ts
│   │   └── 📄 index.vue.vm      → src/views/.../index.vue
│   ├── 📱 移动端 (app)
│   │   ├── 📄 api.ts.vm         → src/api/.../api.ts
│   │   └── 📄 index.vue.vm      → src/pages/.../index.vue
│   └── 💬 小程序 (mini-program)
│       ├── 📄 api.js.vm         → api/.../api.js
│       └── 📄 index.wxml.vm     → pages/.../index.wxml
└── ➕ 添加模板组
```

---