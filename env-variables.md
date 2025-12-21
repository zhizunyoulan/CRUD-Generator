# Code Template Environment Variables

## 1. Overview

This document aims to help developers understand and use all predefined environment variables built into the code generation templates. These variables are automatically injected by the template engine and can be used directly in `.vm` template files to generate full-stack CRUD code based on Spring + MyBatis + Frontend frameworks.

### 1.1 Design Philosophy
This design is driven by a **Main Model**, supports **multi-model associations**, and can generate complete CRUD, import, and export functional code. Through a rich set of metadata objects, it provides great flexibility for templates, capable of handling various scenarios from simple forms to complex nested queries.

### 1.2 How to Use
In Velocity templates, reference these variables using `$` followed by the variable name.
**Example:**
```velocity
package $mainModelClass.packageName;

@RestController
@RequestMapping("/$mainModel.kebabCaseModelName")
public class $mainModelClass.simpleName Controller {
    @Autowired
    private $serviceClass.simpleName $serviceClass.camelCaseModelName;
}
```

## 2. Environment Variables Overview

| Variable Name | Type | Nullable | Description |
| :--- | :--- | :--- | :--- |
| `mainModel` | [`ModelDefinition`](#31-core-model-object-modeldefinition) | **No** | Core metadata definition for the **Main Model**. |
| `mainModelClass` | [`JavaClass`](#33-java-class-information-object-javaclass) | **No** | Java class information corresponding to the main model. |
| `mapperClass` | [`JavaClass`](#33-java-class-information-object-javaclass) | **No** | MyBatis Mapper interface class information. |
| `repositoryClass` | [`JavaClass`](#33-java-class-information-object-javaclass) | **No** | JPA Repository interface class information. |
| `serviceClass` | [`JavaClass`](#33-java-class-information-object-javaclass) | **No** | Service interface class information. |
| `serviceImplClass` | [`JavaClass`](#33-java-class-information-object-javaclass) | **No** | Service implementation class information. |
| `requestModelClass` | [`JavaClass`](#33-java-class-information-object-javaclass) | **Yes** | Query parameter class information. **If null, parameters are scattered fields**. |
| `resultModelClass` | [`JavaClass`](#33-java-class-information-object-javaclass) | **No** | Query result class information. |
| `readDefinition` | [`ReadDefinition`](#341-readdefinition-query-function) | Yes | **【Query】** function definition. |
| `createDefinition` | [`CreateDefinition`](#342-createdefinition-create-function) | Yes | **【Create】** function definition. |
| `updateDefinition` | [`UpdateDefinition`](#343-updatedefinition-update-function) | Yes | **【Update】** function definition. |
| `deleteDefinition` | [`DeleteDefinition`](#344-deletedefinition-delete-function) | Yes | **【Delete】** function definition. |
| `currentClass` | [`JavaClass`](#33-java-class-information-object-javaclass) | **Yes** | Java class information for the **current template** (**Non-null only in backend Java class templates**). |
| `currentModel` | [`ModelDefinition`](#31-core-model-object-modeldefinition) | **No** | Model information for the **current template** (Only used in the Model/Entity templates, due to the multi-model design). |
| `currentModelClass` | [`JavaClass`](#33-java-class-information-object-javaclass) | **No** | Model Java class information for the **current template** (Only available in Entity template in designs where Model and Entity coexist). |
| `author` | `String` | No | Current OS username, used for code comment attribution. |
| `module` | `String` | No | Module name to which the current function belongs. |
| `groupId` | `String` | No | Maven/Gradle GroupId. |
| `myTool` | [`MyTool`](#36-utility-functions-mytool) | No | Built-in utility function collection. |

## 3. Variable Details

### 3.1 Core Model Object (`ModelDefinition`)

**Description**: Defines the core metadata of a data model, including its name, database mapping information, field list, and primary key information.
**Available Instances:** `$mainModel`, `$currentModel`

**Fields:**
*   `modelName` (String): The simple name of the model class (e.g., `UserDept`).
*   `kebabCaseModelName` (String): The kebab-case format of the model name (e.g., `user-dept`). Often used in URLs.
*   `camelCaseModelName` (String): The camelCase format of the model name (e.g., `userDept`). Often used for variable names.
*   `description` (String): Description of the model.
*   `tableName` (String): Corresponding database table name.
*   `tableAlias` (String): Alias for the database table (used in SQL queries).
*   `fields` (List<[`FieldDefinition`](#32-field-definition-object-fielddefinition)>): List of all fields in the model.
*   `primaryKeyFields` (List<[`FieldDefinition`](#32-field-definition-object-fielddefinition)>): List of fields that are primary keys.
*   `primaryKeyField` ([`FieldDefinition`](#32-field-definition-object-fielddefinition)): The primary key field (returns the first one if multiple exist).

### 3.2 Field Definition Object (`FieldDefinition`)

**Description**: Defines the detailed information for a field (property) in a model.
**Common Locations:** `$mainModel.fields`, `$readDefinition.conditionFields`

**Fields:**
*   `property` (String): The property name in the Java entity class (e.g., `userName`).
*   `label` (String): The description or label of the field (e.g., `Username`).
*   `javaType` (String): The fully qualified Java type name of the field (e.g., `java.lang.String`).
*   `enumType` (Boolean): Indicates if this field is an enum type.
*   `jdbcType` (String): The JDBC type mapped to the database (e.g., `VARCHAR`).
*   `columnName` (String): The mapped database table column name.
*   `columnType` (String): The type of the database column (e.g., `varchar(255)`).
*   `nullable` (Boolean): Indicates if the column allows NULL values.
*   `unique` (Boolean): Indicates if the column has a unique constraint.
*   `maxLength` (Integer): The maximum length of the field data (for types like String), can be null.
*   `primaryKey` (Boolean): Indicates if the field is part of the primary key.
*   `format` (String): Data format (often used for datetime fields, e.g., `yyyy-MM-dd HH:mm:ss`).
*   `exist` (Boolean): Indicates if this property actually maps to a database table column.
*   `tableName` (String): The name of the database table to which the model belongs.
*   `tableAlias` (String): The alias of the database table for the model (used in SQL queries).
*   `selectProperties` ([`SelectProperties`](#355-selectproperties-retrieval-properties)): Configures the field's behavior during **data retrieval**.
*   `conditionProperties` ([`ConditionProperties`](#356-conditionproperties-condition-properties)): Configures the field's behavior when used as a **query condition**.
*   `formProperties` ([`FormProperties`](#357-formproperties-form-properties)): Configures the field's behavior in the **frontend form**.

### 3.3 Java Class Information Object (`JavaClass`)

**Description**: Defines the metadata information for a Java class.
**Available Instances:** `$mainModelClass`, `$currentClass`, `$serviceClass`

**Fields:**
*   `packageName` (String): The package name where the class resides (e.g., `com.example.model`).
*   `qualifiedName` (String): The fully qualified name of the class (e.g., `com.example.model.User`).
*   `simpleName` (String): The simple name of the class (e.g., `User`).
*   `fields` (List<[`FieldDefinition`](#32-field-definition-object-fielddefinition)>): List of all fields defined in this class.
*   `allFields` (List<[`FieldDefinition`](#32-field-definition-object-fielddefinition)>): A list of all fields of this class, including those from its parent class.
*   `dynamicImports` (List<String>): List of fully qualified names of other classes that need to be imported.
*   `superClass` ([`JavaClass`](#33-java-class-information-object-javaclass)): The parent class information of this class.

**Methods:**
*   `containsProperty(String propertyName)` (Boolean): Checks if the current class's field list contains the specified property name.

### 3.4 Function Definition Objects

#### 3.4.1 `ReadDefinition` (Query Function)
**Description**: Defines the complete configuration for the 【Query】 function, supporting complex queries and drilling.
**Available Instances:** `$readDefinition` (Can be null)

**Fields:**
*   `functionName` (String): The core name of the function (e.g., `getUserList`).
*   `singleResult` (Boolean): Query scope (`'list'` or `'one'`), `true` for single item query, `false` for list query.
*   `description` (String): Description of the function.
*   `selectFields` (List<[`FieldDefinition`](#32-field-definition-object-fielddefinition)>): **All** fields that need to be selected.
*   `resultFields` (List<[`FieldDefinition`](#32-field-definition-object-fielddefinition)>): Fields contained in the final result entity.
*   `conditionFields` (List<[`FieldDefinition`](#32-field-definition-object-fielddefinition)>): All condition fields used.
*   `requestParamFields` (List<[`FieldDefinition`](#32-field-definition-object-fielddefinition)>): All condition fields used (when not using an entity to receive parameters).
*   `resultFieldsTree` (Object): Tree structure of selected fields, used for generating nested `resultMap`.
*   `recursiveNestedField` ([`RecursiveNestedField`](#351-recursivenestedfield-recursive-nesting)): Recursive nested field configuration (e.g., tree structure).
*   `select` ([`SelectDefinition`](#353-selectdefinition-query-definition)): Description of the SQL query fragment.

#### 3.4.2 `CreateDefinition` (Create Function)
**Description**: Defines the configuration for the 【Create】 function.
**Available Instances:** `$createDefinition` (Can be null)
**Fields:**
*   `functionName` (String): The core name of the function (e.g., `createUser`).
*   `formFields` (List<[`FieldDefinition`](#32-field-definition-object-fielddefinition)>): List of fields that need to be processed in the "Create" form.

#### 3.4.3 `UpdateDefinition` (Update Function)
**Description**: Defines the configuration for the 【Update】 function.
**Available Instances:** `$updateDefinition` (Can be null)
**Fields:**
*   `functionName` (String): The core name of the function (e.g., `updateUser`).
*   `formFields` (List<[`FieldDefinition`](#32-field-definition-object-fielddefinition)>): List of fields that need to be processed in the "Update" form.

#### 3.4.4 `DeleteDefinition` (Delete Function)
**Description**: Defines the configuration for the 【Delete】 function.
**Available Instances:** `$deleteDefinition` (Can be null)
**Fields:**
*   `functionName` (String): The core name of the function (e.g., `deleteUserById`).

### 3.5 Associated Type Details

#### 3.5.1 `RecursiveNestedField` (Recursive Nesting)
**Description**: Defines special field configuration for recursive nested queries.
**Available Location:** `$readDefinition.recursiveNestedField`
**Fields:**
*   `property` (String): The collection property name that holds the recursive subset (e.g., `children`).
*   `joinProperty` (String): The foreign key property name pointing to the parent node ID (e.g., `parentId`).
*   `lazy` (Boolean): Whether to use lazy loading for the recursive subset.

#### 3.5.2 `SelectDefinition` (Query Definition)
**Description**: Defines the core elements of an SQL query fragment.
**Available Location:** `$readDefinition.select`
**Fields:**
*   `selectFields` (List<[`FieldDefinition`](#32-field-definition-object-fielddefinition)>): List of fields to be selected in this query fragment.
*   `conditionFields` (List<[`FieldDefinition`](#32-field-definition-object-fielddefinition)>): List of condition fields required for this query fragment.
*   `tableJoins` (List<[`TableJoin`](#352-tablejoin-table-join)>): List of table join relationships required for this query fragment.

#### 3.5.3 `TableJoin` (Table Join)
**Description**: Defines the join relationship between two database tables.
**Available Location:** `$selectDefinition.tableJoins`
**Fields:**
*   `tableName` (String): The table name of the **current table**.
*   `tableAlias` (String): The alias of the **current table**.
*   `columnName` (String): The column name used for joining in the **current table**.
*   `relatedTableName` (String): The name of the related table.
*   `relatedTableAlias` (String): The alias of the related table.
*   `relatedColumnName` (String): The column name used for joining in the **related table**.

#### 3.5.4 `SelectProperties` (Retrieval Properties)
**Description**: Configures the field's behavior during data retrieval.
**Available Location:** `$fieldDefinition.selectProperties`
**Fields:**
*   `propAlias` (String): The property alias after the field is selected.
*   `labelAlias` (String): The display name alias after the field is selected.
*   `columnAlias` (String): The alias for the database column corresponding to the field.
*   `visible` (Boolean): Whether the field is displayed on the frontend.
*   `lazy` (Boolean): Whether to use lazy loading.

#### 3.5.5 `ConditionProperties` (Condition Properties)
**Description**: Configures the field's behavior when used as a query condition.
**Available Location:** `$fieldDefinition.conditionProperties`
**Fields:**
*   `propAlias` (String): The property alias of the condition field.
*   `labelAlias` (String): The display name alias of the condition field.

*   `operator` (String): SQL comparison operator (e.g., `'='`, `'LIKE'`, `'>'`).
*   `valueType` (String): Operand Type. Specifies the source of a field's operand value. Possible values are "variable" or "constant".
*   `valueRange` (String): The range of variable values. Can be "any" (for any value) or "options" (for selecting from a defined set of options).

*   `value`: The concrete value of the constant. This field is valid only when valueType is "constant". Its data type is determined by the type of the field it corresponds to. If the field is of time type, this value can be a predefined constant for generating specific time points, for example: currentDateTime (current date and time), currentDate (current date), currentTime (current time), currentMonthStart (first day of the current month), currentYearStart (first day of the current year).
*   `betweenLeftValue`: The left interval fixed value for the BETWEEN operator. Its definition, conditions for taking effect, value type, and optional values for time-type fields are exactly the same as the `value` property.
*   `betweenRightValue`: The right interval fixed value for the BETWEEN operator. Its definition, conditions for taking effect, value type, and optional values for time-type fields are exactly the same as the `value` property.
*   `betweenLeftProp` (String): **Property name for the left-interval variable**. When the operand type (`valueType`) for the left interval of the `BETWEEN` operator is a variable (`"variable"`), this field specifies the **request parameter property name** used to receive that variable‘s value.
*   `betweenRightProp` (String): **Property name for the right-interval variable**. When the operand type (`valueType`) for the right interval of the `BETWEEN` operator is a variable (`"variable"`), this field specifies the **request parameter property name** used to receive that variable‘s value.
*   `betweenLeftLabel` (String): **Display label for the left-interval condition**. Used on query forms or interfaces to clearly show the user a **Chinese or friendly name** for the left-interval condition.
*   `betweenRightLabel` (String): **Display label for the right-interval condition**. Used on query forms or interfaces to clearly show the user a **Chinese or friendly name** for the right-interval condition.
*   `required` (Boolean): Whether the value of the condition field must be non-empty.
*   `nullHandle` (String): Null-value handling strategy. Determines the backend logic when a query condition field is marked as non-required and no value is passed from the frontend. Possible values are "skip" (skip the condition) or "isNull" (query with IS NULL).


#### 3.5.6 `FormProperties` (Form Properties)
**Description**: Configures the field's behavior in the frontend form.
**Available Location:** `$fieldDefinition.formProperties`
**Fields:**
*   `inputType` (String): The type of input component in the frontend form (e.g., `'input'`, `'textarea'`).
*   `span` (Integer): The number of grid columns this field occupies in a grid layout (based on a 24-column system).
*   `editable` (Boolean): Whether the field is editable in the form.

### 3.6 Utility Functions (`myTool`)

**Description**: Provides a collection of utility functions for use in templates.
**Available Instances:** `$myTool`

**Methods:**
*   `getFieldGetterName(`[`FieldDefinition`](#32-field-definition-object-fielddefinition)` field)` (String): Generates the Getter method name (e.g., `getUserName`).
*   `getFieldSetterName(`[`FieldDefinition`](#32-field-definition-object-fielddefinition)` field)` (String): Generates the Setter method name (e.g., `setUserName`).
*   `toUpperCamelCase(String str)` (String): Converts to UpperCamelCase format (e.g., `userName` -> `UserName`).
*   `toLowerCamelCase(String str)` (String): Converts to LowerCamelCase format (e.g., `UserName` -> `userName`).
*   `getJavaTypeSimpleName(String javaType, boolean withGenerics)` (String): Gets the simple name of the Java type.
*   `getAliasOrProperty(`[`FieldDefinition`](#32-field-definition-object-fielddefinition)` field)` (String): Gets the field alias or property name.
*   `getTsTypeForJavaType(String javaType)` (String): Gets the TypeScript type corresponding to the Java type.
*   `deduplicate(Collection<Object>... collections)` (List<Object>): Merges and deduplicates collections.

**Velocity Macros:**
*   `#getTsType($javaType)`
*   `#getFieldGetterName($field)`
*   `#getFieldSetterName($field)`
*   `#toUpperCamelCase($str)`
*   `#toLowerCamelCase($str)`
*   `#getAliasOrProperty($field)`
*   `#getJavaTypeSimpleName($javaType)`
*   `#getJavaTypeSimpleName($javaType, $withGenerics)`


**Overridable Velocity Macros(velocimacros.vtl):**
*   `#mybatisResultMapFields($resultFieldsTree)`: Automatically generates the field mapping part of the MyBatis ResultMap.
*   `sqlPlainConditionExpression`, `betweenConditionExpression`, and `conditionPart`: These three macros are used together to dynamically construct the WHERE clause conditions for SQL queries within code templates. Their current implementation is based on MySQL syntax.

## 4. Comprehensive Examples

### 4.1 Generating a Java Model Class
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

### 4.2 Generating MyBatis Mapper XML (Handling Scattered Parameter Scenario)
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
        <!-- Use macro to automatically generate all field mappings -->
        #mybatisResultMapFields($readDefinition.resultFieldsTree)
    </resultMap>
</mapper>
```

### 4.3 Generating Service Interface (Handling Scattered Parameter Scenario)
```java
package $serviceClass.packageName;

import $resultModelClass.qualifiedName;
import java.util.List;

public interface $serviceClass.simpleName {
    List<$resultModelClass.simpleName> $readDefinition.functionName(
        #if(!$requestModelClass) ## If request model is empty, use scattered fields as parameters
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
Back to [Home](./README.md)