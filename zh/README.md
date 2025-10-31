

# **智能代码生成器 (CRUD Generator) - 文档中心** [(English Document) 🔗](../README.md)

> 一款基于IntelliJ IDEA的可视化代码生成插件，支持Spring, MyBatis, JPA等技术栈。


## 🚀 快速开始

1. **安装插件**: 从JetBrains Marketplace安装`CRUD Generator`插件
2. **创建配置**: 在项目根目录创建 `config.xml` 文件
3. **选择模型**: 在IDE中右键点击Model/Entity类，选择"Generate Code / CRUD"
4. **编排功能**: 在可视化界面中配置字段、查询条件、排序规则
5. **生成代码**: 生成前后端代码文件，保持文件或合并到已有文件


## 💡 核心特性

- ✅ **双ORM支持**: 同时支持MyBatis和JPA数据模型
- ✅ **可视化编排**: 图形化界面配置字段行为和查询逻辑
- ✅ **多环境生成**: 支持admin、app等多客户端代码生成
- ✅ **模板定制**: 支持自定义Velocity模板



## [🧩 环境变量详解](./env-variables.md)
- `ModelDefinition`, `FieldDefinition`, `JavaClass` 等核心类型说明
- `readDefinition`, `createDefinition` 等功能定义对象详解
- `myTool` 工具函数API参考
- 在Velocity模板中使用这些变量的示例

## 💻 内置模板库

- **后端模板**
  - [Spring MVC Controller模板](./built-in-templates/mybatis/spring/src/main/java/__base_package__/controller/__modelName__Controller.java.vm)
  - [Spring MVC + MyBatis Service层模板](./built-in-templates/mybatis/spring/src/main/java/__base_package__/service/__modelName__Service.java.vm)
  - [Spring MVC + JPA Repository模板](./built-in-templates/jpa/spring/src/main/java/__base_package__/dao/__modelName__Repository.java.vm)
  - [MyBatis Mapper XML模板](./built-in-templates/mybatis/spring/src/main/resources/mybatis/__module__/mapper/__modelName__Mapper.xml.vm)
- **前端模板**
  - [Vue 3 + TypeScript + Element Plus View模板](./built-in-templates/mybatis/front_end/admin/src/views/__module__/__kebabCaseModelName__/index.vue.vm)
  - [API调用层模板](./built-in-templates/mybatis/front_end/admin/src/apis/__module__/__kebabCaseModelName__.ts.vm)

## [⚙️ 配置文件指南](./config.md)
- 多环境配置详解 (admin, app, mini-program)
- 模板路径自定义配置

---

## 🔗 相关链接

- [JetBrains Marketplace 发布页面](https://plugins.jetbrains.com/plugin/26463-crud-generator)
- [问题反馈](https://github.com/zhizunyoulan/CRUD-Generator/issues)


---

