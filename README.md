# **CRUD Generator - Documentation Hub** [(中文文档)🔗](./zh/README.md)

> A visual code generation plugin for IntelliJ IDEA, supporting Spring Boot, MyBatis, JPA, and more.


## 🚀 Quick Start

1.  **Install Plugin**: Install the `CRUD Generator` plugin from the JetBrains Marketplace.
2.  **Create Configuration**: Create a `config.xml` file in your project root directory.
3.  **Select Model**: Right-click a Model/Entity class in the IDE and select "Generate Code / CRUD".
4.  **Orchestrate Functions**: Configure fields, query conditions, and sorting rules in the visual interface.
5.  **Generate Code**: Generate frontend and backend code files, save or merge files.

## 💡 Core Features

-   ✅ **Dual ORM Support**: Supports both MyBatis and JPA data models.
-   ✅ **Visual Orchestration**: Graphically configure field behaviors and query logic.
-   ✅ **Multi-Environment Generation**: Generate code for different clients (admin, app, etc.).
-   ✅ **Template Customization**: Supports custom Velocity templates.


## [🧩 Environment Variables Reference](./env-variables.md)
- Core type definitions: `ModelDefinition`, `FieldDefinition`, `JavaClass`, etc.
- Detailed explanation of functional definition objects: `readDefinition`, `createDefinition`, etc.
- `myTool` utility function API reference
- Examples of using these variables in Velocity templates

## 💻 Built-in Template Library

- **Backend Templates**
  - [Spring MVC Controller Template](./built-in-templates/mybatis/spring/src/main/java/__base_package__/controller/__modelName__Controller.java.vm)
  - [Spring MVC + MyBatis Service Layer Template](./built-in-templates/mybatis/spring/src/main/java/__base_package__/service/__modelName__Service.java.vm)
  - [Spring MVC + JPA Repository Template](./built-in-templates/jpa/spring/src/main/java/__base_package__/dao/__modelName__Repository.java.vm)
  - [MyBatis Mapper XML Template](./built-in-templates/mybatis/spring/src/main/resources/mybatis/__module__/mapper/__modelName__Mapper.xml.vm)
- **Frontend Templates**
  - [Vue 3 + TypeScript + Element Plus View Template](./built-in-templates/mybatis/front_end/admin/src/views/__module__/__kebabCaseModelName__/index.vue.vm)
  - [API Service Layer Template](./built-in-templates/mybatis/front_end/admin/src/apis/__module__/__kebabCaseModelName__.ts.vm)

## 🏗️ Supported Development Platforms

The following development platforms have ready-to-use template suites available:

### [🚀 Yimi Framework](https://gitee.com/uublue/yimi-doc)
- **Status**: ✅ Production Ready
- **Configuration**: [`config.xml`](./dev-platforms/Yimi/templates/config.xml)
- **Description**: Full-stack templates specifically designed for the Yimi Framework ecosystem, including optimized controllers, services, and frontend components.
- **Features**: MyBatis integration, Vue 3 frontend, admin management templates

### [🔄 JHipster](#)
- **Status**: 🔄 In Preparation
- **Description**: Template suite for JHipster-based projects, following JHipster conventions and best practices.
- **Planned Features**: JPA entity templates, Liquibase migration scripts, Angular/React frontend support


## [⚙️ Configuration Guide](./config.md)
- Multi-environment Configuration Details (admin, app, mini-program)
- Custom Template Path Configuration

---

## 🔗 Related Links

-   [JetBrains Marketplace Page](https://plugins.jetbrains.com/plugin/26463-crud-generator)
-   [Reporting Issues](https://github.com/zhizunyoulan/CRUD-Generator/issues)

---
