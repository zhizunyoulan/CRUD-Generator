# 代码模板环境变量

## 一、概述

本文档旨在帮助开发人员理解和使用内置于代码生成模板中的所有预定义环境变量。这些变量由模板引擎自动注入，可在 `.vm` 模板文件中直接使用，用于生成基于 Spring + MyBatis + 前端框架的全栈 CRUD 代码。

### 1.1 设计哲学
本设计基于**主模型（Main Model）**驱动，支持**多模型关联**，可生成完整的增删改查(CRUD)、导入、导出功能代码。通过一套丰富的元数据对象，为模板提供了极大的灵活性，能够处理简单表单到复杂嵌套查询等各种场景。

### 1.2 如何使用
在 Velocity 模板中，通过 `$` 加变量名的方式引用这些变量。
**示例：**
```velocity
package $mainModel.packageName;

@RestController
@RequestMapping("/$mainModel.kebabCaseName")
public class ${mainModel.simpleName}Controller {
    @Autowired
    private $serviceClass.simpleName $serviceClass.camelCaseName;
}
```

## 二、环境变量总览

| 变量名 | 类型 | 可空性 | 简介 |
| :--- | :--- | :--- | :--- |
| `mainModel` | [`ModelClass`](#31-核心模型对象-modelclass) | **No** | **主模型**的核心元数据定义 |
| `mapperClass` | [`ModelClass`](#31-核心模型对象-modelclass) | **No** | MyBatis Mapper 接口类信息 |
| `repositoryClass` | [`ModelClass`](#31-核心模型对象-modelclass) | **No** | JPA Repository 接口类信息 |
| `serviceClass` | [`ModelClass`](#31-核心模型对象-modelclass) | **No** | Service 接口类信息 |
| `serviceImplClass` | [`ModelClass`](#31-核心模型对象-modelclass) | **No** | Service 实现类信息 |
| `readDefinition` | [`ReadDefinition`](#34-readdefinition-查询功能) | Yes | 【查询】功能定义 |
| `createDefinition` | [`CreateDefinition`](#38-createdefinition-创建功能) | Yes | 【创建】功能定义 |
| `updateDefinition` | [`UpdateDefinition`](#39-updatedefinition-修改功能) | Yes | 【修改】功能定义 |
| `deleteDefinition` | [`DeleteDefinition`](#310-deletedefinition-删除功能) | Yes | 【删除】功能定义 |
| `currentClass` | [`ModelClass`](#31-核心模型对象-modelclass) | **Yes** | **当前模板**对应的 Java 类信息 (**仅后端 Java 类模板中不为空**) |
| `currentSourceModel` | [`ModelClass`](#31-核心模型对象-modelclass) | **No** | **当前生成项**对应的源模型（生成环境支持多个模型，有的生成项每个模型生成一个，比如DTO类） |
| `author` | `String` | No | 当前操作系统用户名，用于代码注释署名 |
| `module` | `String` | No | 当前功能所属模块名 |
| `groupId` | `String` | No | Maven/Gradle 的 GroupId |
| `global` | `Map<String, Object>` | No | 全局变量，可用于存储和传递自定义数据 |
| `currentDateTime` | `LocalDateTime` | No | 当前时间 |
| `myTool` | [`MyTool`](#314-工具函数-mytool) | No | 内置工具函数集 |

## 三、核心类型详解

### 3.1 核心模型对象 (`ModelClass`)

**描述**: 定义了数据模型的核心元数据，包括其名称、数据库映射信息、字段列表和主键信息。`ModelClass` 继承自 `ClassInfo`，整合了原 `JavaClass` 的功能。
**可用实例:** `$mainModel`, `$currentSourceModel`, `$mapperClass`, `$serviceClass` 等

**字段:**
*   `simpleName` (String): 模型类的简单名称 (e.g., `UserDept`).
*   `kebabCaseName` (String): 模型名的短横线分隔格式 (e.g., `user-dept`). 常用于 URL.
*   `camelCaseName` (String): 模型名的小驼峰格式 (e.g., `userDept`). 常用于变量名.
*   `description` (String): 模型的描述信息.
*   `packageName` (String): 类所在的包名 (e.g., `com.example.model`).
*   `qualifiedName` (String): 类的全限定名 (e.g., `com.example.model.User`).
*   `superClass` ([`ClassInfo`](#33-classinfo-类信息基类)): 该类的父类信息.
*   `extendsJavaType` (String): 父类的 Java 类型全限定名.
*   `declaredFields` (List<[`ModelField`](#32-字段定义对象-modelfield)>): 当前类声明的字段列表（不包含父类字段）.
*   `fields` (List<[`ModelField`](#32-字段定义对象-modelfield)>): 该类的所有字段列表（包含父类字段）.
*   `primaryKeyFields` (List<[`ModelField`](#32-字段定义对象-modelfield)>): 作为主键的字段列表.
*   `primaryKeyField` ([`ModelField`](#32-字段定义对象-modelfield)): 主键字段 (多主键时返回第一个).

**方法:**
*   `containsProperty(String propertyName)` (Boolean): 判断模型是否包含指定名称的属性.

### 3.2 字段定义对象 (`ModelField`)

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
*   `selectAttributes` ([`SelectAttributes`](#311-selectattributes-检索属性)): 配置该字段在**数据检索**时的行为.
*   `conditionAttributes` ([`ConditionAttributes`](#312-conditionattributes-条件属性)): 配置该字段作为**查询条件**时的行为.
*   `formAttributes` ([`FormAttributes`](#313-formattributes-表单属性)): 配置该字段在**前端表单**中的行为.

### 3.3 `ClassInfo` (类信息基类)

**描述**: 类信息的基类，`ModelClass` 继承自此。
**可用位置:** `$modelClass.superClass`

**字段:**
*   `packageName` (String): 类所在的包名 (e.g., `com.example.model`).
*   `qualifiedName` (String): 类的全限定名 (e.g., `com.example.model.User`).
*   `simpleName` (String): 类的简单名称 (e.g., `User`).
*   `superClass` ([`ClassInfo`](#33-classinfo-类信息基类)): 该类的父类信息.

### 3.4 `ReadDefinition` (查询功能)

**描述**: 定义了【查询】功能的完整配置，支持复杂查询和钻取。
**可用实例:** `$readDefinition` (可为空)

**字段:**
*   `functionName` (String): 功能的核心名称 (e.g., `getUserList`).
*   `singleResult` (Boolean): 查询范围 (`'list'` 或 `'one'`), `true`为查询单条，`false`为查询列表.
*   `description` (String): 功能的描述信息.
*   `requestModelClass` ([`ModelClass`](#31-核心模型对象-modelclass)): 查询参数类信息。**若为空，表示参数为分散字段**.
*   `resultModelClass` ([`ModelClass`](#31-核心模型对象-modelclass)): 查询结果类信息.
*   `models` (List<[`ReadModel`](#35-readmodel-查询模型)>): 多模型关联查询时的模型列表.
*   `flattenableSelectFields` (List<[`ModelField`](#32-字段定义对象-modelfield)>): 可扁平化的查询字段列表.
*   `orderedJoins` (List<[`ModelJoin`](#36-modeljoin-模型关联)>): 有序的模型关联列表.
*   `selectFields` (List<[`ModelField`](#32-字段定义对象-modelfield)>): 需要检出的**所有**字段.
*   `resultFields` (List<[`ModelField`](#32-字段定义对象-modelfield)>): 最终结果实体中包含的字段.
*   `conditionFields` (List<[`ModelField`](#32-字段定义对象-modelfield)>): 需要用到的所有条件字段.
*   `requestParamFields` (List<[`ModelField`](#32-字段定义对象-modelfield)>): 需要用到的所有条件字段（当不使用实体接收参数时）.
*   `resultFieldsTree` (Object): 检出字段的树形结构，用于生成嵌套 `resultMap`.
*   `recursiveNestedInfo` ([`RecursiveNestedInfo`](#37-recursivenestedinfo-递归嵌套信息)): 递归嵌套配置信息（如树形结构）.

### 3.5 `ReadModel` (查询模型)

**描述**: 定义了多模型关联查询时的单个模型配置。
**可用位置:** `$readDefinition.models`

**字段:**
*   `tableAlias` (String): 表别名.
*   `selectFields` (List<[`ModelField`](#32-字段定义对象-modelfield)>): 该模型需要检出的字段列表.
*   `conditionFields` (List<[`ModelField`](#32-字段定义对象-modelfield)>): 该模型需要的条件字段列表.
*   `joins` (List<[`ModelJoin`](#36-modeljoin-模型关联)>): 该模型的关联关系列表.

### 3.6 `ModelJoin` (模型关联)

**描述**: 定义了两个模型（表）之间的关联关系。
**可用位置:** `$readDefinition.orderedJoins`, `$readModel.joins`

**字段:**
*   `thisModel` ([`ModelClass`](#31-核心模型对象-modelclass)): 当前模型（主表）对象.
*   `thisField` ([`ModelField`](#32-字段定义对象-modelfield)): 当前模型中用于关联的字段.
*   `referencedModel` ([`ModelClass`](#31-核心模型对象-modelclass)): 被引用的模型（关联表）对象.
*   `referencedField` ([`ModelField`](#32-字段定义对象-modelfield)): 被引用模型中用于关联的字段.

### 3.7 `RecursiveNestedInfo` (递归嵌套信息)

**描述**: 定义了递归嵌套查询的配置信息。
**可用位置:** `$readDefinition.recursiveNestedInfo`

**字段:**
*   `parentProperty` (String): 指向父节点ID的属性名 (e.g., `parentId`).
*   `parentLabel` (String): 父节点属性的标签名 (e.g., `父节点ID`).
*   `childrenProperty` (String): 承载递归子集的集合属性名 (e.g., `children`).
*   `childrenLabel` (String): 子节点集合属性的标签名 (e.g., `子节点列表`).
*   `lazy` (Boolean): 是否采用懒加载.

### 3.8 `CreateDefinition` (创建功能)

**描述**: 定义了【创建】功能的配置。
**可用实例:** `$createDefinition` (可为空)

**字段:**
*   `functionName` (String): 功能的核心名称 (e.g., `createUser`).
*   `formFields` (List<[`ModelField`](#32-字段定义对象-modelfield)>): 在"新增"表单中需要处理的字段列表.

### 3.9 `UpdateDefinition` (修改功能)

**描述**: 定义了【修改】功能的配置。
**可用实例:** `$updateDefinition` (可为空)

**字段:**
*   `functionName` (String): 功能的核心名称 (e.g., `updateUser`).
*   `formFields` (List<[`ModelField`](#32-字段定义对象-modelfield)>): 在"修改"表单中需要处理的字段列表.

### 3.10 `DeleteDefinition` (删除功能)

**描述**: 定义了【删除】功能的配置。
**可用实例:** `$deleteDefinition` (可为空)

**字段:**
*   `functionName` (String): 功能的核心名称 (e.g., `deleteUserById`).

### 3.11 `SelectAttributes` (检索属性)

**描述**: 配置字段在数据检索时的行为。
**可用位置:** `$modelField.selectAttributes`

**字段:**
*   `fullNestedProperty` (String): 字段在完整嵌套关系中的全路径属性名 (e.g., `user.dept.name`).
*   `propAlias` (String): 字段检出后的属性别名.
*   `labelAlias` (String): 字段检出后的显示名称别名.
*   `columnAlias` (String): 字段对应的数据库列的别名.
*   `visible` (Boolean): 字段是否在前端显示.
*   `lazy` (Boolean): 是否懒加载.

### 3.12 `ConditionAttributes` (条件属性)

**描述**: 配置字段作为查询条件时的行为。
**可用位置:** `$modelField.conditionAttributes`

**字段:**
*   `fullNestedProperty` (String): 字段在完整嵌套关系中的全路径属性名 (e.g., `user.dept.name`).
*   `propAlias` (String): 条件字段的属性别名.
*   `labelAlias` (String): 条件字段的显示名称别名.
*   `operator` (String): SQL 条件字段的操作符 (e.g., `'='`, `'LIKE'`, `'>'`).
*   `valueType` (String): 操作数类型。用于指定某字段操作数的取值来源，可选值为 "variable" (变量) 或 "constant" (固定值)。
*   `valueRange` (String): 变量值的范围 (e.g., `'any'`, `'options'`).
*   `value`: 固定值的具体取值。当 valueType 为 "constant" 时，此字段有效。其值类型由对应字段类型决定。若字段为时间类型，此值可以是用于生成特定时间点的预定义常量，例如：currentDateTime (当前日期时间)、currentDate (当前日期)、currentTime (当前时间)、currentMonthStart (本月第一天)、currentYearStart (本年第一天).
*   `betweenLeftValue`: BETWEEN 操作符的左区间固定值。其定义、生效条件、值类型及时间类型可选值均与 `value` 属性完全相同.
*   `betweenRightValue`: BETWEEN 操作符的右区间固定值。其定义、生效条件、值类型及时间类型可选值均与 `value` 属性完全相同.
*   `betweenLeftProp` (String): 左区间变量属性名。当 BETWEEN 操作符的左区间操作数类型 (valueType) 为变量 ("variable") 时，此字段指定用于接收该变量值的请求参数属性名.
*   `betweenRightProp` (String): 右区间变量属性名。当 BETWEEN 操作符的右区间操作数类型 (valueType) 为变量 ("variable") 时，此字段指定用于接收该变量值的请求参数属性名.
*   `betweenLeftLabel` (String): 左区间条件显示名。用于在查询表单或界面上，向用户清晰展示左区间条件的中文或友好名称.
*   `betweenRightLabel` (String): 右区间条件显示名。用于在查询表单或界面上，向用户清晰展示右区间条件的中文或友好名称.
*   `required` (Boolean): 条件字段的值是否必须非空.
*   `nullHandle` (String): 空值处理策略。当查询条件的字段被标记为非必传，且前端未传入该参数值时，用于决定后端的处理逻辑。可选值为 "skip" (跳过判断) 或 "isNull" (查询 IS NULL).

### 3.13 `FormAttributes` (表单属性)

**描述**: 配置字段在前端表单中的行为。
**可用位置:** `$modelField.formAttributes`

**字段:**
*   `inputType` (String): 前端表单中输入组件的类型 (e.g., `'input'`, `'textarea'`).
*   `span` (Integer): 在栅格布局中该字段所占的栅格数 (基于 24 分制).
*   `editable` (Boolean): 该字段在表单中是否可编辑.

### 3.14 工具函数 (`myTool`)

**描述**: 提供了在模板中使用的工具函数集合。
**可用实例:** `$myTool`

**方法:**
*   `getFieldGetterName(`[`ModelField`](#32-字段定义对象-modelfield)` field)` (String): 生成 Getter 方法名 (e.g., `getUserName`).
*   `getFieldSetterName(`[`ModelField`](#32-字段定义对象-modelfield)` field)` (String): 生成 Setter 方法名 (e.g., `setUserName`).
*   `toUpperCamelCase(String str)` (String): 转换为大驼峰格式 (e.g., `userName` -> `UserName`).
*   `toLowerCamelCase(String str)` (String): 转换为小驼峰格式 (e.g., `UserName` -> `userName`).
*   `getJavaTypeSimpleName(String javaType, boolean withGenerics)` (String): 获取Java类型简称.
*   `getAliasOrProperty(`[`ModelField`](#32-字段定义对象-modelfield)` field)` (String): 获取字段别名或属性名.
*   `getTsTypeForJavaType(String javaType)` (String): 获取Java类型对应的TS类型.
*   `deduplicate(Collection<Object>... collections)` (List<Object>): 对集合进行合并并去重.
*   `extractImportedTypesFromJavaTypes(Collection<String> javaTypes)` (List<String>): 从Java类型集合中提取需要导入的类型列表.
*   `extractImportedTypesFromFields(Collection<`[`ModelField`](#32-字段定义对象-modelfield)`> fields)` (List<String>): 从字段集合中提取需要导入的类型列表.
*   `pluralize(String word)` (String): 将单词转换为复数形式.
*   `getAncestorNestedProperties(`[`ModelField`](#32-字段定义对象-modelfield)` field)` (List<String>): 获取字段的祖先嵌套属性路径列表.
*   `getDateTimeFormatterOfPattern(String pattern)` (DateTimeFormatter): 根据模式字符串获取日期时间格式化器.

## 四、Velocity 宏

### 4.1 内置 Velocity 宏
以下宏可直接在模板中使用：

*   `#getTsType($javaType)` - 获取 Java 类型对应的 TypeScript 类型
*   `#getFieldGetterName($field)` - 生成字段的 Getter 方法名
*   `#getFieldSetterName($field)` - 生成字段的 Setter 方法名
*   `#toUpperCamelCase($str)` - 转换为大驼峰格式
*   `#toLowerCamelCase($str)` - 转换为小驼峰格式
*   `#getAliasOrProperty($field)` - 获取字段别名或属性名
*   `#getJavaTypeSimpleName($javaType)` - 获取 Java 类型的简单名称
*   `#getJavaTypeSimpleName($javaType, $withGenerics)` - 获取 Java 类型的简单名称（可选择是否包含泛型）

### 4.2 可重写的 Velocity 宏（velocimacros.vtl）
以下宏可根据需要进行重写以定制生成逻辑：

*   `#mybatisResultMapFields($resultFieldsTree)` - 自动生成 MyBatis ResultMap 字段映射部分.
*   `sqlPlainConditionExpression`、`betweenConditionExpression`、`conditionPart` - 这三个宏共同用于在代码模板中动态构造 SQL 查询的 WHERE 条件部分。它们的当前实现基于 MySQL 语法.


## 五、综合示例

### 5.1 生成 Java Model 类
```velocity
/**
 * ${currentClass.description}
 * Author: $author
 * Module: $module
 */
package $currentClass.packageName;

import java.io.Serializable;
#foreach ($importType in $myTool.extractImportedTypesFromFields($currentClass.declaredFields))
import ${importType};
#end

public class ${currentClass.simpleName} #if($currentClass.extendsJavaType != $null)extends $myTool.getJavaTypeSimpleName($currentClass.extendsJavaType, true) #end{

    private static final long serialVersionUID = 1L;

#foreach($field in $currentClass.declaredFields)
    /** ${field.label} */
    private $myTool.getJavaTypeSimpleName($field.javaType) $field.property;
#end
}
```

### 5.2 生成 MyBatis Mapper XML (处理分散参数场景)
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
        #mybatisResultMap($readDefinition.mybatisResultMap)
    </resultMap>
</mapper>
```

### 5.3 生成 Service 接口 (处理分散参数场景)
```java
package $serviceClass.packageName;

import $readDefinition.resultModelClass.qualifiedName;
import java.util.List;

public interface $serviceClass.simpleName {
    List<$readDefinition.resultModelClass.simpleName> ${readDefinition.functionName}(
        #if($readDefinition.requestModelClass == $null) ## 如果请求模型为空，则使用分散字段作为参数
            #foreach($condition in $readDefinition.conditionFields)
                @Param("$condition.property") $myTool.getJavaTypeSimpleName($condition.javaType) $condition.property #if( $foreach.hasNext ), #end
            #end
        #else
            @Param("param") $readDefinition.requestModelClass.simpleName param
        #end
    );
}
```
------
回到[上一页](./README.md)