# config.xml 配置文件说明

## 概述

`config.xml` 文件用于定义代码生成器的模板配置，支持前后端分离的模板管理和多环境代码生成。

## 基本结构

### 根节点
```xml
<config>
    <!-- 后端模板配置 -->
    <templates location="backend">...</templates>
    
    <!-- 前端模板配置 -->
    <templates location="frontend">...</templates>

    <!-- 后端groupId -->
    <groupId>...</groupId>
</config>
```

## templates 节点

`templates` 节点用于包裹一组模板定义。

**属性：**
- `location` (必需): 标识模板类型
  - `backend`: 后端模板
  - `frontend`: 前端模板

## groupId 节点（可选）

项目不是由maven构建时（比如gradle），请配置该项，以提供后端基础包名

## profile 节点（可选）

`profile` 节点用于对模板进行分组管理，特别适用于前端多环境场景。

**属性：**
- `id` (必需): 分组唯一标识符
- `label` (必需): 分组显示名称，在设计页面的预览窗口中显示
- `device` (可选): 指定前端设备类型
  - `desktop`: 桌面端
  - `mobile`: 移动端

## template 节点

`template` 节点定义单个模板的配置。

**子标签：**

### id (必需)
模板的唯一标识符。
**注意：** 部分ID在软件内有特殊含义，请勿随意更改。

### sourcePath (必需)
模板文件的相对路径，相对于工作空间内的 `templates` 目录。

### targetPath (必需)
生成目标文件的路径：
- **后端模板**: 相对于目标模块的路径
- **前端模板**: 相对于前端项目根目录的路径

### condition (可选)
条件表达式，控制模板是否生成。当返回 `false` 时，对应的模板文件不会生成。

**可用变量：**
- `multiKey`: 主实体是否有多个主键
- `enableRead`: 是否启用查询功能
- `enableCreate`: 是否启用新增功能
- `enableUpdate`: 是否启用修改功能
- `enableDelete`: 是否启用删除功能

**示例：**
```xml
<condition>enableRead</condition>          <!-- 仅当启用查询功能时生成 -->
<condition>!enableRead</condition>         <!-- 当未启用查询功能时生成 -->
<condition>enableCreate && enableUpdate</condition>  <!-- 同时启用新增和修改时生成 -->
```

## 路径变量说明

在 `sourcePath` 和 `targetPath` 中可使用以下变量：

- `__base_package__`: 基础包名
- `__modelName__`: 模型名称（驼峰格式）
- `__kebabCaseModelName__`: 模型名称（短横线格式）
- `__module__`: 模块名称

## 内置模板配置文件

- [Mybatis](./built-in-templates/mybatis/config.xml)
- [JPA](./built-in-templates/jpa/config.xml)