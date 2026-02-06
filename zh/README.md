

# **智能代码生成器 (Yimi CRUD Generator) - 文档中心** [(English Document) 🔗](../README.md)

> 一款基于IntelliJ IDEA的可视化代码生成插件，支持Spring, MyBatis, JPA等技术栈。


## 🚀 快速开始

1. **安装插件**: 从JetBrains Marketplace安装`Yimi CRUD Generator`插件
2. **选择模型**: 在IDE中右键点击Model/Entity类，选择"Generate Code / CRUD"
3. **编排功能**: 在可视化界面中配置字段、查询条件、排序规则
4. **生成代码**: 生成前后端代码文件，保持文件或合并到已有文件


## 💡 核心特性

- ✅ **双ORM支持**: 同时支持MyBatis和JPA数据模型
- ✅ **可视化编排**: 图形化界面配置字段行为和查询逻辑
- ✅ **多环境生成**: 支持admin、app等多客户端代码生成
- ✅ **自定义变量**: 为功能与字段添加扩展属性，支持变量依赖 **[新]**
- ✅ **模板定制**: 支持自定义Velocity模板，多端模板组管理 **[增强]**



## [🧩 环境变量详解](./env-variables.md)
- `ModelDefinition`, `FieldDefinition`, `JavaClass` 等核心类型说明
- `readDefinition`, `createDefinition` 等功能定义对象详解
- `myTool` 工具函数API参考
- 在Velocity模板中使用这些变量的示例

## 🛠️ 高级定制 **【新增】**
- [自定义变量与模板](./custom/variables.md)
- [模板管理指南](./custom/templates.md)

## 💻 内置模板库

- **后端模板**
  - [Spring MVC Controller模板](../built-in-templates/mybatis/spring/src/main/java/__base_package__/controller/__modelName__Controller.java.vm)
  - [Spring MVC + MyBatis Service层模板](../built-in-templates/mybatis/spring/src/main/java/__base_package__/service/__modelName__Service.java.vm)
  - [Spring MVC + JPA Repository模板](../built-in-templates/jpa/spring/src/main/java/__base_package__/dao/__modelName__Repository.java.vm)
  - [MyBatis Mapper XML模板](../built-in-templates/mybatis/spring/src/main/resources/mybatis/__module__/mapper/__modelName__Mapper.xml.vm)
- **前端模板**
  - [Vue 3 + TypeScript + Element Plus View模板](../built-in-templates/mybatis/front_end/admin/src/views/__module__/__kebabCaseModelName__/index.vue.vm)
  - [API调用层模板](../built-in-templates/mybatis/front_end/admin/src/apis/__module__/__kebabCaseModelName__.ts.vm)

  
## 🏗️ 支持的开发平台

以下开发平台已提供现成的模板套件：

### [🚀 Yimi框架](https://gitee.com/uublue/yimi-doc)
- **状态**: ✅ 生产就绪
- **配置文件**: [`config.xml`](../dev-platforms/Yimi/templates/config.xml)
- **描述**: 专为Yimi框架生态系统设计的全栈模板，包括优化的控制器、服务和前端组件。
- **特性**: MyBatis集成, Vue 3前端, 后台管理模板

### [🔄 JHipster](https://github.com/zhizunyoulan/yimi-templates-jhipster.git)
- **状态**: 🔄 开发中
- **描述**: 用于JHipster项目的模板套件，遵循JHipster的约定和最佳实践。
- **计划特性**: JPA实体模板, Liquibase迁移脚本, Angular/React前端支持


## [⚙️ 配置文件指南](./config.md)
- 多环境配置详解 (admin, app, mini-program)
- 模板路径自定义配置

---

## 🔗 相关链接

- [JetBrains Marketplace 发布页面](https://plugins.jetbrains.com/plugin/26463-crud-generator)
- [用法介绍](https://medium.com/@zhizunyoulan/an-intellij-idea-code-generation-tool-for-crud-complex-queries-custom-templates-and-variables-d0b6c69ec261)
- [演示视频(YouTube)](https://youtu.be/KGRnpuScThI)


---

