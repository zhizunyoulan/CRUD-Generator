# Code Template Environment Variables

## 1. Overview

This document helps developers understand and use all predefined environment variables built into the code generation templates. These variables are automatically injected by the template engine and can be directly used in `.vm` template files to generate full-stack CRUD code based on Spring + MyBatis + frontend frameworks.

### 1.1 Design Philosophy
This design is driven by the **Main Model**, supports **multi-model associations**, and can generate complete CRUD, import, and export functionality code. Through a rich set of metadata objects, the template provides great flexibility to handle various scenarios from simple forms to complex nested queries.

### 1.2 How to Use
In Velocity templates, reference these variables using `$` followed by the variable name.
**Example:**
```velocity
package $mainModel.packageName;

@RestController
@RequestMapping("/$mainModel.kebabCaseName")
public class $mainModel.simpleName Controller {
    @Autowired
    private $serviceClass.simpleName $serviceClass.camelCaseName;
}
```

## 2. Environment Variables Overview

| Variable Name | Type | Nullable | Description |
| :--- | :--- | :--- | :--- |
| `mainModel` | [`ModelClass`](#31-modelclass-core-model-object) | **No** | Core metadata definition of the **main model** |
| `mapperClass` | [`ModelClass`](#31-modelclass-core-model-object) | **No** | MyBatis Mapper interface class information |
| `repositoryClass` | [`ModelClass`](#31-modelclass-core-model-object) | **No** | JPA Repository interface class information |
| `serviceClass` | [`ModelClass`](#31-modelclass-core-model-object) | **No** | Service interface class information |
| `serviceImplClass` | [`ModelClass`](#31-modelclass-core-model-object) | **No** | Service implementation class information |
| `readDefinition` | [`ReadDefinition`](#34-readdefinition-query-function) | Yes | Query function definition |
| `createDefinition` | [`CreateDefinition`](#38-createdefinition-create-function) | Yes | Create function definition |
| `updateDefinition` | [`UpdateDefinition`](#39-updatedefinition-update-function) | Yes | Update function definition |
| `deleteDefinition` | [`DeleteDefinition`](#310-deletedefinition-delete-function) | Yes | Delete function definition |
| `currentClass` | [`ModelClass`](#31-modelclass-core-model-object) | **Yes** | Java class information corresponding to the **current template** (**Not null only in backend Java class templates**) |
| `currentSourceModel` | [`ModelClass`](#31-modelclass-core-model-object) | **No** | Source model corresponding to the **current generation item** (The generation environment supports multiple models; some generation items generate one per model, such as DTO classes) |
| `author` | `String` | No | Current operating system username, used for code comment signature |
| `module` | `String` | No | Module name to which the current function belongs |
| `groupId` | `String` | No | Maven/Gradle GroupId |
| `global` | `Map<String, Object>` | No | Global variables for storing and passing custom data |
| `currentDateTime` | `LocalDateTime` | No | Current date and time |
| `myTool` | [`MyTool`](#314-mytool-utility-functions) | No | Built-in utility function set |

## 3. Core Type Details

### 3.1 `ModelClass` (Core Model Object)

**Description**: Defines the core metadata of a data model, including its name, database mapping information, field list, and primary key information. `ModelClass` extends `ClassInfo` and integrates the functionality of the original `JavaClass`.
**Available Instances:** `$mainModel`, `$currentSourceModel`, `$mapperClass`, `$serviceClass`, etc.

**Fields:**
*   `simpleName` (String): Simple name of the model class (e.g., `UserDept`).
*   `kebabCaseName` (String): Kebab-case format of the model name (e.g., `user-dept`). Commonly used in URLs.
*   `camelCaseName` (String): Camel-case format of the model name (e.g., `userDept`). Commonly used in variable names.
*   `description` (String): Description information of the model.
*   `packageName` (String): Package name where the class resides (e.g., `com.example.model`).
*   `qualifiedName` (String): Fully qualified name of the class (e.g., `com.example.model.User`).
*   `superClass` ([`ClassInfo`](#33-classinfo-base-class-for-class-information)): Parent class information of this class.
*   `extendsJavaType` (String): Fully qualified name of the parent class's Java type.
*   `declaredFields` (List<[`ModelField`](#32-modelfield-field-definition-object)>): List of fields declared in the current class (excluding fields from parent classes).
*   `fields` (List<[`ModelField`](#32-modelfield-field-definition-object)>): List of all fields in the class (including fields from parent classes).
*   `primaryKeyFields` (List<[`ModelField`](#32-modelfield-field-definition-object)>): List of fields that are part of the primary key.
*   `primaryKeyField` ([`ModelField`](#32-modelfield-field-definition-object)): Primary key field (returns the first one for composite primary keys).

**Methods:**
*   `containsProperty(String propertyName)` (Boolean): Checks if the model contains a property with the specified name.

### 3.2 `ModelField` (Field Definition Object)

**Description**: Defines detailed information about a field (property) in a model.
**Common Locations:** `$mainModel.fields`, `$readDefinition.conditionFields`

**Fields:**
*   `property` (String): Property name of the Java entity class (e.g., `userName`).
*   `label` (String): Description or label information of the field (e.g., `Username`).
*   `javaType` (String): Fully qualified Java type name of the field (e.g., `java.lang.String`).
*   `enumType` (Boolean): Indicates whether the field is an enum type.
*   `jdbcType` (String): JDBC type mapped to the database (e.g., `VARCHAR`).
*   `columnName` (String): Database table column name mapped to this field.
*   `columnType` (String): Database column type (e.g., `varchar(255)`).
*   `nullable` (Boolean): Indicates whether the column allows NULL values.
*   `unique` (Boolean): Indicates whether the column has a unique constraint.
*   `maxLength` (Integer): Maximum length of the field data (for string types, etc.), can be null.
*   `primaryKey` (Boolean): Indicates whether the field is part of the primary key.
*   `format` (String): Data format (commonly used for date/time fields, e.g., `yyyy-MM-dd HH:mm:ss`).
*   `exist` (Boolean): Indicates whether the property actually maps to a database table field.
*   `selectAttributes` ([`SelectAttributes`](#311-selectattributes-select-attributes)): Configures the behavior of this field during **data retrieval**.
*   `conditionAttributes` ([`ConditionAttributes`](#312-conditionattributes-condition-attributes)): Configures the behavior of this field when used as a **query condition**.
*   `formAttributes` ([`FormAttributes`](#313-formattributes-form-attributes)): Configures the behavior of this field in the **frontend form**.

### 3.3 `ClassInfo` (Base Class for Class Information)

**Description**: Base class for class information, inherited by `ModelClass`.
**Available Location:** `$modelClass.superClass`

**Fields:**
*   `packageName` (String): Package name where the class resides (e.g., `com.example.model`).
*   `qualifiedName` (String): Fully qualified name of the class (e.g., `com.example.model.User`).
*   `simpleName` (String): Simple name of the class (e.g., `User`).
*   `superClass` ([`ClassInfo`](#33-classinfo-base-class-for-class-information)): Parent class information of this class.

### 3.4 `ReadDefinition` (Query Function)

**Description**: Defines the complete configuration for the query function, supporting complex queries and drilling.
**Available Instance:** `$readDefinition` (may be null)

**Fields:**
*   `functionName` (String): Core name of the function (e.g., `getUserList`).
*   `singleResult` (Boolean): Query scope (`'list'` or `'one'`), `true` for single result, `false` for list.
*   `description` (String): Description information of the function.
*   `requestModelClass` ([`ModelClass`](#31-modelclass-core-model-object)): Query parameter class information. **If null, indicates parameters are scattered fields**.
*   `resultModelClass` ([`ModelClass`](#31-modelclass-core-model-object)): Query result class information.
*   `models` (List<[`ReadModel`](#35-readmodel-query-model)>): List of models for multi-model association queries.
*   `flattenableSelectFields` (List<[`ModelField`](#32-modelfield-field-definition-object)>): List of flattenable select fields.
*   `orderedJoins` (List<[`ModelJoin`](#36-modeljoin-model-association)>): Ordered list of model associations.
*   `selectFields` (List<[`ModelField`](#32-modelfield-field-definition-object)>): All fields that need to be selected.
*   `resultFields` (List<[`ModelField`](#32-modelfield-field-definition-object)>): Fields included in the final result entity.
*   `conditionFields` (List<[`ModelField`](#32-modelfield-field-definition-object)>): All condition fields needed.
*   `requestParamFields` (List<[`ModelField`](#32-modelfield-field-definition-object)>): All condition fields needed (when not using entity to receive parameters).
*   `resultFieldsTree` (Object): Tree structure of selected fields, used to generate nested `resultMap`.
*   `recursiveNestedInfo` ([`RecursiveNestedInfo`](#37-recursivenestedinfo-recursive-nested-information)): Recursive nested configuration information (e.g., tree structure).

### 3.5 `ReadModel` (Query Model)

**Description**: Defines the configuration of a single model during multi-model association queries.
**Available Location:** `$readDefinition.models`

**Fields:**
*   `tableAlias` (String): Table alias.
*   `selectFields` (List<[`ModelField`](#32-modelfield-field-definition-object)>): List of fields to select from this model.
*   `conditionFields` (List<[`ModelField`](#32-modelfield-field-definition-object)>): List of condition fields needed for this model.
*   `joins` (List<[`ModelJoin`](#36-modeljoin-model-association)>): List of associations for this model.

### 3.6 `ModelJoin` (Model Association)

**Description**: Defines the association relationship between two models (tables).
**Available Locations:** `$readDefinition.orderedJoins`, `$readModel.joins`

**Fields:**
*   `thisModel` ([`ModelClass`](#31-modelclass-core-model-object)): Current model (main table) object.
*   `thisField` ([`ModelField`](#32-modelfield-field-definition-object)): Field in the current model used for association.
*   `referencedModel` ([`ModelClass`](#31-modelclass-core-model-object)): Referenced model (association table) object.
*   `referencedField` ([`ModelField`](#32-modelfield-field-definition-object)): Field in the referenced model used for association.

### 3.7 `RecursiveNestedInfo` (Recursive Nested Information)

**Description**: Defines configuration information for recursive nested queries.
**Available Location:** `$readDefinition.recursiveNestedInfo`

**Fields:**
*   `parentProperty` (String): Property name pointing to the parent node ID (e.g., `parentId`).
*   `parentLabel` (String): Label name of the parent node property (e.g., `Parent ID`).
*   `childrenProperty` (String): Collection property name that holds the recursive subset (e.g., `children`).
*   `childrenLabel` (String): Label name of the children collection property (e.g., `Children List`).
*   `lazy` (Boolean): Whether to use lazy loading.

### 3.8 `CreateDefinition` (Create Function)

**Description**: Defines the configuration for the create function.
**Available Instance:** `$createDefinition` (may be null)

**Fields:**
*   `functionName` (String): Core name of the function (e.g., `createUser`).
*   `formFields` (List<[`ModelField`](#32-modelfield-field-definition-object)>): List of fields to handle in the "create" form.

### 3.9 `UpdateDefinition` (Update Function)

**Description**: Defines the configuration for the update function.
**Available Instance:** `$updateDefinition` (may be null)

**Fields:**
*   `functionName` (String): Core name of the function (e.g., `updateUser`).
*   `formFields` (List<[`ModelField`](#32-modelfield-field-definition-object)>): List of fields to handle in the "update" form.

### 3.10 `DeleteDefinition` (Delete Function)

**Description**: Defines the configuration for the delete function.
**Available Instance:** `$deleteDefinition` (may be null)

**Fields:**
*   `functionName` (String): Core name of the function (e.g., `deleteUserById`).

### 3.11 `SelectAttributes` (Select Attributes)

**Description**: Configures the behavior of a field during data retrieval.
**Available Location:** `$modelField.selectAttributes`

**Fields:**
*   `fullNestedProperty` (String): Full path property name of the field in the complete nested relationship (e.g., `user.dept.name`).
*   `propAlias` (String): Property alias after field selection.
*   `labelAlias` (String): Display name alias after field selection.
*   `columnAlias` (String): Column alias of the database column corresponding to the field.
*   `visible` (Boolean): Whether the field is visible in the frontend.
*   `lazy` (Boolean): Whether to use lazy loading.

### 3.12 `ConditionAttributes` (Condition Attributes)

**Description**: Configures the behavior of a field when used as a query condition.
**Available Location:** `$modelField.conditionAttributes`

**Fields:**
*   `fullNestedProperty` (String): Full path property name of the field in the complete nested relationship (e.g., `user.dept.name`).
*   `propAlias` (String): Property alias of the condition field.
*   `labelAlias` (String): Display name alias of the condition field.
*   `operator` (String): SQL condition operator (e.g., `'='`, `'LIKE'`, `'>'`).
*   `valueType` (String): Operand type. Used to specify the source of the operand value for a field. Optional values are "variable" or "constant".
*   `valueRange` (String): Range of variable values (e.g., `'any'`, `'options'`).
*   `value`: Specific value of the constant. Valid when valueType is "constant". Its type is determined by the corresponding field type. If the field is a date/time type, this value can be a predefined constant used to generate a specific time point, such as: currentDateTime, currentDate, currentTime, currentMonthStart, currentYearStart.
*   `betweenLeftValue`: Left interval constant value for BETWEEN operator. Its definition, valid conditions, value type, and optional date/time values are exactly the same as the `value` property.
*   `betweenRightValue`: Right interval constant value for BETWEEN operator. Its definition, valid conditions, value type, and optional date/time values are exactly the same as the `value` property.
*   `betweenLeftProp` (String): Left interval variable property name. When the operand type (valueType) of the left interval of the BETWEEN operator is "variable", this field specifies the request parameter property name used to receive the variable value.
*   `betweenRightProp` (String): Right interval variable property name. When the operand type (valueType) of the right interval of the BETWEEN operator is "variable", this field specifies the request parameter property name used to receive the variable value.
*   `betweenLeftLabel` (String): Left interval condition display name. Used to clearly display the Chinese or friendly name of the left interval condition to users on the query form or interface.
*   `betweenRightLabel` (String): Right interval condition display name. Used to clearly display the Chinese or friendly name of the right interval condition to users on the query form or interface.
*   `required` (Boolean): Whether the condition field value must be non-null.
*   `nullHandle` (String): Null value handling strategy. When a condition field is marked as non-mandatory and the frontend does not pass the parameter value, this determines the backend processing logic. Optional values are "skip" (skip judgment) or "isNull" (query IS NULL).

### 3.13 `FormAttributes` (Form Attributes)

**Description**: Configures the behavior of a field in the frontend form.
**Available Location:** `$modelField.formAttributes`

**Fields:**
*   `inputType` (String): Type of input component in the frontend form (e.g., `'input'`, `'textarea'`).
*   `span` (Integer): Number of grid columns occupied by this field in the grid layout (based on 24-column system).
*   `editable` (Boolean): Whether the field is editable in the form.

### 3.14 `MyTool` (Utility Functions)

**Description**: Provides a collection of utility functions for use in templates.
**Available Instance:** `$myTool`

**Methods:**
*   `getFieldGetterName(`[`ModelField`](#32-modelfield-field-definition-object)` field)` (String): Generates the Getter method name (e.g., `getUserName`).
*   `getFieldSetterName(`[`ModelField`](#32-modelfield-field-definition-object)` field)` (String): Generates the Setter method name (e.g., `setUserName`).
*   `toUpperCamelCase(String str)` (String): Converts to PascalCase format (e.g., `userName` -> `UserName`).
*   `toLowerCamelCase(String str)` (String): Converts to camelCase format (e.g., `UserName` -> `userName`).
*   `getJavaTypeSimpleName(String javaType, boolean withGenerics)` (String): Gets the simple name of a Java type.
*   `getAliasOrProperty(`[`ModelField`](#32-modelfield-field-definition-object)` field)` (String): Gets the field alias or property name.
*   `getTsTypeForJavaType(String javaType)` (String): Gets the TypeScript type corresponding to a Java type.
*   `deduplicate(Collection<Object>... collections)` (List<Object>): Merges and deduplicates collections.
*   `extractImportedTypesFromJavaTypes(Collection<String> javaTypes)` (List<String>): Extracts a list of types that need to be imported from a collection of Java types.
*   `extractImportedTypesFromFields(Collection<`[`ModelField`](#32-modelfield-field-definition-object)`> fields)` (List<String>): Extracts a list of types that need to be imported from a collection of fields.
*   `pluralize(String word)` (String): Converts a word to its plural form.
*   `getAncestorNestedProperties(`[`ModelField`](#32-modelfield-field-definition-object)` field)` (List<String>): Gets the list of ancestor nested property paths for a field.
*   `getDateTimeFormatterOfPattern(String pattern)` (DateTimeFormatter): Gets a date-time formatter based on a pattern string.

## 4. Velocity Macros

### 4.1 Built-in Velocity Macros
The following macros can be directly used in templates:

*   `#getTsType($javaType)` - Gets the TypeScript type corresponding to a Java type
*   `#getFieldGetterName($field)` - Generates the Getter method name for a field
*   `#getFieldSetterName($field)` - Generates the Setter method name for a field
*   `#toUpperCamelCase($str)` - Converts to PascalCase format
*   `#toLowerCamelCase($str)` - Converts to camelCase format
*   `#getAliasOrProperty($field)` - Gets the field alias or property name
*   `#getJavaTypeSimpleName($javaType)` - Gets the simple name of a Java type
*   `#getJavaTypeSimpleName($javaType, $withGenerics)` - Gets the simple name of a Java type (optionally including generics)

### 4.2 Overridable Velocity Macros (velocimacros.vtl)
The following macros can be overridden as needed to customize generation logic:

*   `#mybatisResultMapFields($resultFieldsTree)` - Automatically generates MyBatis ResultMap field mapping section.
*   `sqlPlainConditionExpression`, `betweenConditionExpression`, `conditionPart` - These three macros are used together to dynamically construct the WHERE condition part of SQL queries in code templates. Their current implementation is based on MySQL syntax.

## 5. Comprehensive Examples

### 5.1 Generate Java Model Class
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

public class ${currentClass.simpleName} #if($currentClass.extendsJavaType != $null)extends $myTool.getJavaTypeSimpleName($currentClass.extendsJavaType, true) #end {

    private static final long serialVersionUID = 1L;

#foreach($field in $currentClass.declaredFields)
    /** ${field.label} */
    private $myTool.getJavaTypeSimpleName($field.javaType) $field.property;
#end
}
```

### 5.2 Generating MyBatis Mapper XML (Handling Scattered Parameter Scenario)
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
        #mybatisResultMap($readDefinition.mybatisResultMap)
    </resultMap>
</mapper>
```

### 5.3 Generating Service Interface (Handling Scattered Parameter Scenario)
```java
package $serviceClass.packageName;

import $readDefinition.resultModelClass.qualifiedName;
import java.util.List;

public interface $serviceClass.simpleName {
    List<$readDefinition.resultModelClass.simpleName> ${readDefinition.functionName}(
        #if($readDefinition.requestModelClass == $null) ## If request model is empty, use scattered fields as parameters
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
Back to [Home](./README.md)