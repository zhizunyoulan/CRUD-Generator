# 代码模板环境变量

## 一、概述

本文档旨在帮助开发人员理解和使用内置于代码生成模板中的所有预定义环境变量。这些变量由模板引擎自动注入，可在 `.vm` 模板文件中直接使用，用于生成基于 Spring + MyBatis + 前端框架的全栈 CRUD 代码。

### 1.1 设计哲学
本设计基于**主模型（Main Model）**驱动，支持**多模型关联**，可生成完整的增删改查(CRUD)、导入、导出功能代码。通过一套丰富的元数据对象，为模板提供了极大的灵活性，能够处理简单表单到复杂嵌套查询等各种场景。

### 1.2 如何使用
在 Velocity 模板中，通过 `$` 加变量名的方式引用这些变量。
**示例：**
```velocity
package $mainModelClass.packageName;

@RestController
@RequestMapping("/$mainModel.kebabCaseModelName")
public class $mainModelClass.simpleNameController {
    @Autowired
    private $serviceClass.simpleName $serviceClass.camelCaseModelName;
}
```

## 二、环境变量总览

| 变量名 | 类型 | 可空性 | 简介 |
| :--- | :--- | :--- | :--- |
| `mainModel` | [`ModelDefinition`](#31-核心模型对象-modeldefinition) | **No** | **主模型**的核心元数据定义 |
| `mainModelClass` | [`JavaClass`](#33-java类信息对象-javaclass) | **No** | 主模型对应的 Java 类信息 |
| `mapperClass` | [`JavaClass`](#33-java类信息对象-javaclass) | **No** | MyBatis Mapper 接口类信息 |
| `repositoryClass` | [`JavaClass`](#33-java类信息对象-javaclass) | **No** | JPA Repository 接口类信息 |
| `serviceClass` | [`JavaClass`](#33-java类信息对象-javaclass) | **No** | Service 接口类信息 |
| `serviceImplClass` | [`JavaClass`](#33-java类信息对象-javaclass) | **No** | Service 实现类信息 |
| `requestModelClass` | [`JavaClass`](#33-java类信息对象-javaclass) | **Yes** | 查询参数类信息。**若为空，表示参数为分散字段** |
| `resultModelClass` | [`JavaClass`](#33-java类信息对象-javaclass) | **No** | 查询结果类信息 |
| `readDefinition` | [`ReadDefinition`](#341-readdefinition-查询功能) | Yes | 【查询】功能定义 |
| `createDefinition` | [`CreateDefinition`](#342-createdefinition-创建功能) | Yes | 【创建】功能定义 |
| `updateDefinition` | [`UpdateDefinition`](#343-updatedefinition-修改功能) | Yes | 【修改】功能定义 |
| `deleteDefinition` | [`DeleteDefinition`](#344-deletedefinition-删除功能) | Yes | 【删除】功能定义 |
| `currentClass` | [`JavaClass`](#33-java类信息对象-javaclass) | **Yes** | **当前模板**对应的 Java 类信息 (**仅后端 Java 类模板中不为空**) |
| `currentModel` | [`ModelDefinition`](#31-核心模型对象-modeldefinition) | **No** | **当前模板**对应的模型信息 (Model/Entity 模板可用) |
| `currentModelClass` | [`JavaClass`](#33-java类信息对象-javaclass) | **No** | **当前模板**对应的模型 Java 类信息 (Model/Entity 模板可用) |
| `author` | `String` | No | 当前操作系统用户名，用于代码注释署名 |
| `module` | `String` | No | 当前功能所属模块名 |
| `groupId` | `String` | No | Maven/Gradle 的 GroupId |
| `myTool` | [`MyTool`](#36-工具函数-mytool) | No | 内置工具函数集 |

## 三、变量详解

### 3.1 核心模型对象 (`ModelDefinition`)

**描述**: 定义了数据模型的核心元数据，包括其名称、数据库映射信息、字段列表和主键信息。
**可用实例:** `$mainModel`, `$currentModel`

**字段:**
*   `modelName` (String): 模型类的简单名称 (e.g., `UserDept`).
*   `kebabCaseModelName` (String): 模型名的短横线分隔格式 (e.g., `user-dept`). 常用于 URL.
*   `camelCaseModelName` (String): 模型名的驼峰格式 (e.g., `userDept`). 常用于变量名.
*   `description` (String): 模型的描述信息.
*   `tableName` (String): 对应的数据库表名.
*   `tableAlias` (String): 数据库表的别名 (用于 SQL 查询).
*   `fields` (List<[`FieldDefinition`](#32-字段定义对象-fielddefinition)>): 模型的所有字段列表.
*   `primaryKeyFields` (List<[`FieldDefinition`](#32-字段定义对象-fielddefinition)>): 作为主键的字段列表.
*   `primaryKeyField` ([`FieldDefinition`](#32-字段定义对象-fielddefinition)): 主键字段 (多主键时返回第一个).

### 3.2 字段定义对象 (`FieldDefinition`)

**描述**: 定义了模型中的一个字段（属性）的详细信息。
**常用位置:** `$mainModel.fields`, `$readDefinition.conditionFields`

**字段:**
*   `property` (String): Java 实体类的属性名 (e.g., `userName`).
*   `label` (String): 字段的说明或标签信息 (e.g., `用户名`).
*   `javaType` (String): 字段的 Java 全限定类型名 (e.g., `java.lang.String`).
*   `enumType` (Boolean): 指示该字段是否为枚举类型.
*   `jdbcType` (String): 映射到数据库的 JDBC 类型 (e.g., `VARCHAR`).
*   `columnName` (String): 映射的数据库表列名.
*   `columnType` (String): 数据库列的类型 (e.g., `varchar(255)`).
*   `nullable` (Boolean): 指示该列是否允许 NULL 值.
*   `unique` (Boolean): 指示该列是否具有唯一约束.
*   `maxLength` (Integer): 字段数据的最大长度（对于字符串等类型），可为 null.
*   `primaryKey` (Boolean): 指示该字段是否为主键的一部分.
*   `format` (String): 数据格式（常用于日期时间字段，如 `yyyy-MM-dd HH:mm:ss`）.
*   `exist` (Boolean): 指示该属性是否真正映射到数据库表字段.
*   `selectProperties` ([`SelectProperties`](#356-selectproperties-检索属性)): 配置该字段在**数据检索**时的行为.
*   `conditionProperties` ([`ConditionProperties`](#357-conditionproperties-条件属性)): 配置该字段作为**查询条件**时的行为.
*   `formProperties` ([`FormProperties`](#358-formproperties-表单属性)): 配置该字段在**前端表单**中的行为.

### 3.3 Java类信息对象 (`JavaClass`)

**描述**: 定义了一个 Java 类的元数据信息。
**可用实例:** `$mainModelClass`, `$currentClass`, `$serviceClass`

**字段:**
*   `packageName` (String): 类所在的包名 (e.g., `com.example.model`).
*   `qualifiedName` (String): 类的全限定名 (e.g., `com.example.model.User`).
*   `simpleName` (String): 类的简单名称 (e.g., `User`).
*   `fields` (List<[`FieldDefinition`](#32-字段定义对象-fielddefinition)>): 该类中定义的所有字段的列表.
*   `dynamicImports` (List<String>): 需要导入的其他类的全限定名列表.
*   `superClass` ([`JavaClass`](#33-java类信息对象-javaclass)): 该类的父类信息.

**方法:**
*   `containsProperty(String propertyName)` (Boolean): 判断当前类的字段列表中是否包含指定属性名。

### 3.4 功能定义对象

#### 3.4.1 `ReadDefinition` (查询功能)
**描述**: 定义了【查询】功能的完整配置，支持复杂查询和钻取。
**可用实例:** `$readDefinition` (可为空)

**字段:**
*   `functionName` (String): 功能的核心名称 (e.g., `getUserList`).
*   `singleResult` (Boolean): 查询范围 (`'list'` 或 `'one'`), `true`为查询单条，`false`为查询列表.
*   `description` (String): 功能的描述信息.
*   `selectFields` (List<[`FieldDefinition`](#32-字段定义对象-fielddefinition)>): 需要检出的**所有**字段.
*   `resultFields` (List<[`FieldDefinition`](#32-字段定义对象-fielddefinition)>): 最终结果实体中包含的字段.
*   `conditionFields` (List<[`FieldDefinition`](#32-字段定义对象-fielddefinition)>): 需要用到的所有条件字段.
*   `requestParamFields` (List<[`FieldDefinition`](#32-字段定义对象-fielddefinition)>): 需要用到的所有条件字段（当不使用实体接收参数时）.
*   `resultFieldsTree` (Object): 检出字段的树形结构，用于生成嵌套 `resultMap`.
*   `recursiveNestedField` ([`RecursiveNestedField`](#352-recursivenestedfield-递归嵌套)): 递归嵌套字段配置（如树形结构）.
*   `select` ([`SelectDefinition`](#354-selectdefinition-查询定义)): SQL查询片段的描述.


#### 3.4.2 `CreateDefinition` (创建功能)
**描述**: 定义了【创建】功能的配置。
**可用实例:** `$createDefinition` (可为空)
**字段:**
*   `functionName` (String): 功能的核心名称 (e.g., `createUser`).
*   `formFields` (List<[`FieldDefinition`](#32-字段定义对象-fielddefinition)>): 在"新增"表单中需要处理的字段列表.

#### 3.4.3 `UpdateDefinition` (修改功能)
**描述**: 定义了【修改】功能的配置。
**可用实例:** `$updateDefinition` (可为空)
**字段:**
*   `functionName` (String): 功能的核心名称 (e.g., `updateUser`).
*   `formFields` (List<[`FieldDefinition`](#32-字段定义对象-fielddefinition)>): 在"修改"表单中需要处理的字段列表.

#### 3.4.4 `DeleteDefinition` (删除功能)
**描述**: 定义了【删除】功能的配置。
**可用实例:** `$deleteDefinition` (可为空)
**字段:**
*   `functionName` (String): 功能的核心名称 (e.g., `deleteUserById`).

### 3.5 关联类型详解

#### 3.5.1 `RecursiveNestedField` (递归嵌套)
**描述**: 定义了递归嵌套查询的特殊字段配置。
**可用位置:** `$readDefinition.recursiveNestedField`
**字段:**
*   `property` (String): 承载递归子集的集合属性名 (e.g., `children`).
*   `foreignProperty` (String): 指向父节点ID的外键属性名 (e.g., `parentId`).
*   `lazy` (Boolean): 是否对递归子集采用懒加载.

#### 3.5.2 `SelectDefinition` (查询定义)
**描述**: 定义了一个SQL查询片段的核心要素。
**可用位置:** `$readDefinition.allSelects`
**字段:**
*   `functionName` (String): 查询片段对应的方法名/SQL ID.
*   `selectFields` (List<[`FieldDefinition`](#32-字段定义对象-fielddefinition)>): 该查询片段需要检出的字段列表.
*   `conditionFields` (List<[`FieldDefinition`](#32-字段定义对象-fielddefinition)>): 该查询片段需要的条件字段列表.
*   `tableJoins` (List<[`TableJoin`](#353-tablejoin-表关联)>): 该查询片段需要用到的表关联关系列表.

#### 3.5.3 `TableJoin` (表关联)
**描述**: 定义了两个数据库表之间的关联关系。
**可用位置:** `$selectDefinition.tableJoins`
**字段:**
*   `tableName` (String): **当前表**的表名.
*   `tableAlias` (String): **当前表**的别名.
*   `columnName` (String): **当前表**中用于关联的列名.
*   `relatedTableName` (String): 被关联的表名.
*   `relatedTableAlias` (String): 被关联表的别名.
*   `relatedColumnName` (String): **被关联表**中用于关联的列名.

#### 3.5.4 `SelectProperties` (检索属性)
**描述**: 配置字段在数据检索时的行为。
**可用位置:** `$fieldDefinition.selectProperties`
**字段:**
*   `propAlias` (String): 字段检出后的属性别名.
*   `labelAlias` (String): 字段检出后的显示名称别名.
*   `columnAlias` (String): 字段对应的数据库列的别名.
*   `visible` (Boolean): 字段是否在前端显示.
*   `lazy` (Boolean): 是否懒加载.

#### 3.5.5 `ConditionProperties` (条件属性)
**描述**: 配置字段作为查询条件时的行为。
**可用位置:** `$fieldDefinition.conditionProperties`
**字段:**
*   `propAlias` (String): 条件字段的属性别名.
*   `labelAlias` (String): 条件字段的显示名称别名.
*   `inputType` (String): 前端输入组件类型 (e.g., `'input'`, `'select'`).
*   `compareType` (String): SQL 比较操作符 (e.g., `'='`, `'LIKE'`, `'>'`).
*   `required` (Boolean): 条件字段的值是否必须非空.

#### 3.5.6 `FormProperties` (表单属性)
**描述**: 配置字段在前端表单中的行为。
**可用位置:** `$fieldDefinition.formProperties`
**字段:**
*   `inputType` (String): 前端表单中输入组件的类型 (e.g., `'input'`, `'textarea'`).
*   `span` (Integer): 在栅格布局中该字段所占的栅格数 (基于 24 分制).
*   `editable` (Boolean): 该字段在表单中是否可编辑.

### 3.6 工具函数 (`myTool`)

**描述**: 提供了在模板中使用的工具函数集合。
**可用实例:** `$myTool`

**方法:**
*   `getFieldGetterName(`[`FieldDefinition`](#32-字段定义对象-fielddefinition)` field)` (String): 生成 Getter 方法名 (e.g., `getUserName`).
*   `getFieldSetterName(`[`FieldDefinition`](#32-字段定义对象-fielddefinition)` field)` (String): 生成 Setter 方法名 (e.g., `setUserName`).
*   `toUpperCamelCase(String str)` (String): 转换为大驼峰格式 (e.g., `userName` -> `UserName`).
*   `toLowerCamelCase(String str)` (String): 转换为小驼峰格式 (e.g., `UserName` -> `userName`).
*   `getJavaTypeSimpleName(String javaType, boolean withGenerics)` (String): 获取Java类型简称.
*   `getAliasOrProperty(`[`FieldDefinition`](#32-字段定义对象-fielddefinition)` field)` (String): 获取字段别名或属性名.
*   `getTsTypeForJavaType(String javaType)` (String): 获取Java类型对应的TS类型.
*   `deduplicate(Collection<Object>... collections)` (List<Object>): 对集合进行合并并去重.

**Velocity 宏:**
*   `#getTsType($javaType)`
*   `#getFieldGetterName($field)`
*   `#getFieldSetterName($field)`
*   `#toUpperCamelCase($str)`
*   `#toLowerCamelCase($str)`
*   `#getAliasOrProperty($field)`
*   `#getJavaTypeSimpleName($javaType)`
*   `#getJavaTypeSimpleName($javaType, $withGenerics)`
*   `#mybatisResultMapFields($resultFieldsTree)`: 自动生成 MyBatis ResultMap 字段映射部分.

## 四、综合示例

### 4.1 生成 Java Model 类
```velocity
/**
 * ${currentModel.description}
 * Author: $author
 * Module: $module
 */
package $currentClass.packageName;

import java.io.Serializable;
#foreach($imp in $currentClass.dynamicImports)
import $imp;
#end

public class $currentClass.simpleName #if($currentClass.superClass)extends $currentClass.superClass.simpleName #end implements Serializable {

    private static final long serialVersionUID = 1L;

#foreach($field in $currentClass.fields)
    /** ${field.label} */
    private $myTool.getJavaTypeSimpleName($field.javaType) $field.property;
#end
}
```

### 4.2 生成 MyBatis Mapper XML (处理分散参数场景)
```xml
<mapper namespace="$mapperClass.qualifiedName">
    <select id="$readDefinition.functionName" resultMap="BaseResultMap">
        SELECT
        #foreach($field in $readDefinition.selectFields)
            ${field.columnName}#if( $foreach.hasNext ), #end
        #end
        FROM $mainModel.tableName
        <where>
            #foreach($condition in $readDefinition.conditionFields)
            <if test="$condition.property != null and $condition.property != ''">
                AND ${condition.columnName} = #{${condition.property}}
            </if>
            #end
        </where>
    </select>

    <resultMap id="BaseResultMap" type="$resultModelClass.qualifiedName">
        <!-- 使用宏自动生成所有字段映射 -->
        #mybatisResultMapFields($readDefinition.resultFieldsTree)
    </resultMap>
</mapper>
```

### 4.3 生成 Service 接口 (处理分散参数场景)
```java
package $serviceClass.packageName;

import $resultModelClass.qualifiedName;
import java.util.List;

public interface $serviceClass.simpleName {
    List<$resultModelClass.simpleName> $readDefinition.functionName(
        #if(!$requestModelClass) ## 如果请求模型为空，则使用分散字段作为参数
            #foreach($condition in $readDefinition.conditionFields)
                @Param("$condition.property") $myTool.getJavaTypeSimpleName($condition.javaType) $condition.property #if( $foreach.hasNext ), #end
            #end
        #else
            @Param("param") $requestModelClass.simpleName param
        #end
    );
}
```
------
回到[上一页](./README.md)