
## Overview

The Template Management feature allows you to design and manage custom templates in a visual interface, including template file selection, output directory configuration, and multi-client template group creation.

---

## ⚙️ Visual Template Configuration

### Configuration Interface Features

In the plugin's configuration page, you can:

1. **Select Template Files**: Choose custom templates from the workspace's `templates` directory
2. **Configure Output Paths**: Set output directories for generated files for each template
3. **Manage Template Groups**: Create independent template configurations for different clients
4. **Preview Directory Structure**: Real-time viewing of generated directory structure

### Operation Flow

```
Workspace templates directory
        ↓
Select custom template files (.vm)
        ↓
Configure output directory structure
        ↓
Set template conditions and properties
        ↓
Save to config.xml
```

---

## 📁 Template Directory Structure

### Standard Directory Layout

```
workspace/
├── templates/                    # Template root directory
│   ├── backend/                 # Backend templates
│   │   ├── spring/             # Spring framework templates
│   │   │   ├── src/main/java/__base_package__/
│   │   │   │   ├── controller/__modelName__Controller.java.vm
│   │   │   │   ├── service/__modelName__Service.java.vm
│   │   │   │   ├── model/__modelName__.java.vm
│   │   │   │   └── entity/__modelName__Entity.java.vm
│   │   │   └── src/main/resources/mybatis/
│   │   │       └── mapper/__modelName__Mapper.xml.vm
│   │   └── my-custom/          # Custom backend template group
│   │       ├── java-templates/
│   │       └── resource-templates/
│   └── frontend/               # Frontend templates
│       ├── admin/              # Admin dashboard templates
│       │   ├── src/apis/__module__/__kebabCaseModelName__.ts.vm
│       │   ├── src/views/__module__/__kebabCaseModelName__/
│       │   └── src/components/__module__/
│       ├── app/                # Mobile app templates
│       │   ├── pages/__modelName__/
│       │   └── components/
│       ├── mini-program/       # Mini-program templates
│       │   ├── pages/__modelName__/
│       │   └── components/
│       └── my-custom-frontend/ # Custom frontend templates
│           ├── custom-components/
│           └── custom-pages/
└── config.xml                  # Configuration file
```

---

## 🎯 Multi-Client Template Groups

### Template Group Configuration Example

```xml
<templates location="frontend">
    <!-- Admin dashboard template group -->
    <profile id="admin" label="Admin Dashboard" device="desktop">
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

    <!-- Mobile APP template group -->
    <profile id="app" label="Mobile APP" device="mobile">
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

    <!-- WeChat Mini-program template group -->
    <profile id="mini-program" label="WeChat Mini-program" device="mobile">
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

### Backend Template Group Configuration

```xml
<templates location="backend">
    <!-- MyBatis template group -->
    <profile id="mybatis" label="MyBatis Framework">
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

    <!-- JPA template group -->
    <profile id="jpa" label="JPA Framework">
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

## 📋 Output Directory Standards

### Backend Output Directory Structure

Must comply with Maven project specifications:

```
src/
├── main/
│   ├── java/                   # Java source code directory
│   │   └── __base_package__/   # Base package path
│   │       ├── controller/     # Controller layer
│   │       ├── service/        # Service layer
│   │       │   └── impl/       # Service implementation layer
│   │       ├── model/          # Model layer
│   │       ├── entity/         # Entity layer
│   │       ├── repository/     # Data access layer (JPA)
│   │       ├── mapper/         # Mapping layer (MyBatis)
│   │       └── dto/            # Data Transfer Objects
│   └── resources/              # Resource files directory
│       ├── mybatis/            # MyBatis mapping files
│       │   └── mapper/
│       ├── templates/          # Template files
│       └── application.yml     # Configuration files
└── test/                       # Test code directory
    └── java/__base_package__/  # Test code
```

### Frontend Output Directory Structure

Supports flexible project structures:

#### Vue Project Structure
```
src/
├── apis/                       # API interface files
│   └── __module__/             # Module grouping
├── views/                      # Page components
│   └── __module__/             # Module grouping
│       └── __kebabCaseModelName__/  # Feature pages
├── components/                 # Common components
│   └── __module__/             # Module components
├── router/                     # Routing configuration
├── store/                      # State management
└── utils/                      # Utility functions
```

#### Mobile Project Structure
```
src/
├── pages/                      # Page files
│   └── __modelName__/          # Feature page directory
├── components/                 # Component files
├── api/                        # API interfaces
└── utils/                      # Utility functions
```

#### Mini-program Project Structure
```
pages/
└── __modelName__/              # Page directory
    ├── index.wxml              # Template files
    ├── index.wxss              # Style files
    ├── index.js                # Logic files
    └── index.json              # Configuration files
components/                     # Component directory
api/                            # API interfaces
utils/                          # Utility functions
```

---

## 🎨 Visual Configuration Interface

### Interface Features

1. **Template File Browser**
   - Browse template files in workspace
   - Support file filtering (.vm files only)
   - Real-time template content preview

2. **Output Path Configurator**
   - Visual path selection
   - Automatic variable hints (__base_package__, __modelName__, etc.)
   - Path validation and error prompts

3. **Template Group Manager**
   - Create, edit, delete template groups
   - Drag-and-drop template file sorting
   - Batch operation support

4. **Condition Configurator**
   - Visual condition expression building
   - Automatic available variable hints
   - Condition logic validation

### Configuration Example Interface

```
[Template Management]
├── 🔧 Backend Templates
│   ├── 📄 Controller.java.vm    → src/main/java/.../Controller.java
│   ├── 📄 Service.java.vm       → src/main/java/.../Service.java
│   └── 📄 Mapper.xml.vm         → src/main/resources/.../Mapper.xml
├── 🎨 Frontend Templates
│   ├── 🖥️  Admin Dashboard (admin)
│   │   ├── 📄 api.ts.vm         → src/apis/.../api.ts
│   │   └── 📄 index.vue.vm      → src/views/.../index.vue
│   ├── 📱 Mobile App (app)
│   │   ├── 📄 api.ts.vm         → src/api/.../api.ts
│   │   └── 📄 index.vue.vm      → src/pages/.../index.vue
│   └── 💬 Mini-program (mini-program)
│       ├── 📄 api.js.vm         → api/.../api.js
│       └── 📄 index.wxml.vm     → pages/.../index.wxml
└── ➕ Add Template Group
```

---