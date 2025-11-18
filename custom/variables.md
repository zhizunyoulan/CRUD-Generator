
## Overview

The advanced customization feature allows you to define custom variables for the code generation process through XML configuration, meeting specific business scenario requirements. Variables are divided into two levels: field-level variables and function-level variables.

---

## ⚙️ Custom Variable Configuration

### Configuration Structure

```xml
<custom>
    <!-- Field-level variable definitions -->
    <fields belongTo="read" label="Output Fields" fieldsProperty="selectFields" 
            propertiesProperty="selectProperties" showCheckbox="true" sortable="false">
        <variable belongTo="read.selectFields.selectProperties">
            <property>show</property>
            <label>Show in Display</label>
            <javaType>Boolean</javaType>
            <defaultValue>false</defaultValue>
            <inputType>input</inputType>
        </variable>
    </fields>
    
    <!-- Function-level variable definitions -->
    <variable belongTo="read">
        <property>paging</property>
        <label>Enable Paging</label>
        <javaType>Boolean</javaType>
        <defaultValue>false</defaultValue>
        <inputType>input</inputType>
    </variable>
</custom>
```

### Field-level Variables

Field-level variables act on specific field collections of functions, defined through `<fields>` tags:

**Attribute Description:**
- `belongTo`: Belonging function (read, create, update, delete)
- `label`: Label text displayed in the interface
- `fieldsProperty`: Corresponding field collection property name
- `propertiesProperty`: Field property object name
- `showCheckbox`: Whether to show checkbox
- `sortable`: Whether sortable

**Example Configuration:**
```xml
<!-- Output fields configuration for query function -->
<fields belongTo="read" label="Output Fields" fieldsProperty="selectFields" 
        propertiesProperty="selectProperties" showCheckbox="true" sortable="false">
    <variable belongTo="read.selectFields.selectProperties">
        <property>show</property>
        <label>Show in Display</label>
        <javaType>Boolean</javaType>
        <defaultValue>false</defaultValue>
        <inputType>input</inputType>
    </variable>
</fields>

<!-- Form fields configuration for create function -->
<fields belongTo="create" label="Form Fields" fieldsProperty="formFields" 
        propertiesProperty="formProperties" showCheckbox="true" sortable="false">
    <variable belongTo="create.formFields.formProperties">
        <property>color</property>
        <label>Text Color</label>
        <javaType>String</javaType>
        <inputType>input</inputType>
    </variable>
</fields>
```

### Function-level Variables

Function-level variables act directly on entire functions, defined through `<variable>` tags:

**Example Configuration:**
```xml
<!-- Paging control for query function -->
<variable belongTo="read">
    <property>paging</property>
    <label>Enable Paging</label>
    <javaType>Boolean</javaType>
    <defaultValue>false</defaultValue>
    <inputType>input</inputType>
</variable>

<!-- Import control for create function -->
<variable belongTo="create">
    <property>enableImport</property>
    <label>Enable Import</label>
    <javaType>Boolean</javaType>
    <defaultValue>false</defaultValue>
    <inputType>input</inputType>
</variable>
```

### Variable Properties Details

#### `<variable>` Child Tags:

| Tag | Description | Example |
|-----|-------------|---------|
| `property` | Variable property name, key name accessed in templates | `show`, `paging` |
| `label` | Interface display label | `"Show in Display"`, `"Enable Paging"` |
| `javaType` | Variable data type | `Boolean`, `String`, `Integer` |
| `defaultValue` | Default value (optional) | `false`, `"pageSize"` |
| `inputType` | Input control type | `input`, `select` |
| `condition` | Display condition (optional) | `paging[=]true` |
| `options` | Dropdown options (for select type only) | Option list |

#### `belongTo` Attribute Format:

**Field-level Variables:**
```
functionName.fieldCollectionName.propertyObjectName
Example: read.selectFields.selectProperties
```

**Function-level Variables:**
```
functionName
Example: read, create, update, delete
```

### Conditional Dependencies

Implement dependencies between variables through `condition` tag:

```xml
<variable belongTo="read">
    <property>paging</property>
    <label>Enable Paging</label>
    <javaType>Boolean</javaType>
    <defaultValue>false</defaultValue>
    <inputType>input</inputType>
</variable>

<variable belongTo="read">
    <property>pageSizeProp</property>
    <label>Page Size Property</label>
    <javaType>String</javaType>
    <defaultValue>pageSize</defaultValue>
    <inputType>input</inputType>
    <condition>paging[=]true</condition>
</variable>
```

**Condition Syntax:**
- `paging[=]true`: Show when paging equals true
- `exportType[!=]excel`: Show when exportType not equals excel
- `enableExport[Exist]`: Show when enableExport variable exists
- `oldField[NotExist]`: Show when oldField variable does not exist

### Dropdown Options Configuration

For variables with `inputType="select"`, you can define option lists:

```xml
<variable belongTo="create.importFields.importProperties">
    <property>javaType</property>
    <label>Data Type</label>
    <javaType>String</javaType>
    <defaultValue>String</defaultValue>
    <inputType>select</inputType>
    <options>
        <option label="String" value="String"></option>
        <option label="Boolean" value="Boolean"></option>
        <option label="Integer" value="Integer"></option>
        <option label="Date" value="Date"></option>
    </options>
</variable>
```

**Dropdown Variable Example:**
```xml
<variable belongTo="read">
    <property>exportType</property>
    <label>Export Format</label>
    <javaType>String</javaType>
    <defaultValue>excel</defaultValue>
    <inputType>select</inputType>
    <options>
        <option label="Excel File" value="excel"></option>
        <option label="PDF Document" value="pdf"></option>
        <option label="CSV File" value="csv"></option>
    </options>
    <condition>enableExport[=]true</condition>
</variable>
```

---

## 🎨 Using Variables in Templates

### Accessing Function-level Variables

```velocity
## Access paging variable - direct access, no customVariables needed
#if($readDefinition.paging)
    ## Paging query logic
    public Page<${resultModelClass.simpleName}> ${readDefinition.functionName}() {
#else
    ## Regular list query
    public List<${resultModelClass.simpleName}> ${readDefinition.functionName}() {
#end

## Access page size property
#if($readDefinition.pageSizeProp)
    @RequestParam(defaultValue = "20") $readDefinition.pageSizeProp
#end

## Access export type
#if($readDefinition.exportType)
    ## Generate different code based on selected export type
    #if($readDefinition.exportType == "excel")
    exportToExcel();
    #elseif($readDefinition.exportType == "pdf")
    exportToPDF();
    #end
#end
```

### Accessing Field-level Variables

```velocity
## Iterate through output fields and check display property
#foreach($field in $readDefinition.selectFields)
    #if($field.selectProperties.show)
    <td>$field.label</td>
    #end
#end

## Access form field color property
#foreach($field in $createDefinition.formFields)
    #if($field.formProperties.color)
    <el-form-item label="$field.label">
        <el-input style="color: $field.formProperties.color" 
                  v-model="formData.$field.property"/>
    </el-form-item>
    #end
#end
```

### Accessing Import Field Type Properties

```velocity
## Process import field type mapping
#foreach($field in $createDefinition.importFields)
    #if($field.importProperties.javaType)
    ## Data conversion based on configured type
    #if($field.importProperties.javaType == "Date")
    private Date $field.property;
    #else
    private $field.importProperties.javaType $field.property;
    #end
    #end
#end
```

---

## 📋 Typical Application Scenarios

### 1. Paging Control
```xml
<variable belongTo="read">
    <property>paging</property>
    <label>Enable Paging</label>
    <javaType>Boolean</javaType>
    <defaultValue>true</defaultValue>
    <inputType>input</inputType>
</variable>
```

### 2. Export Function Configuration
```xml
<variable belongTo="read">
    <property>enableExport</property>
    <label>Enable Export</label>
    <javaType>Boolean</javaType>
    <defaultValue>false</defaultValue>
    <inputType>input</inputType>
</variable>

<variable belongTo="read">
    <property>exportType</property>
    <label>Export Format</label>
    <javaType>String</javaType>
    <defaultValue>excel</defaultValue>
    <inputType>select</inputType>
    <options>
        <option label="Excel" value="excel"></option>
        <option label="PDF" value="pdf"></option>
    </options>
    <condition>enableExport[=]true</condition>
</variable>
```

### 3. Field Display Control
```xml
<fields belongTo="read" label="Output Fields" fieldsProperty="selectFields" 
        propertiesProperty="selectProperties" showCheckbox="true" sortable="false">
    <variable belongTo="read.selectFields.selectProperties">
        <property>show</property>
        <label>Show in Display</label>
        <javaType>Boolean</javaType>
        <defaultValue>true</defaultValue>
        <inputType>input</inputType>
    </variable>
</fields>
```

### 4. Form Style Customization
```xml
<fields belongTo="create" label="Form Fields" fieldsProperty="formFields" 
        propertiesProperty="formProperties" showCheckbox="true" sortable="false">
    <variable belongTo="create.formFields.formProperties">
        <property>color</property>
        <label>Text Color</label>
        <javaType>String</javaType>
        <defaultValue>#333333</defaultValue>
        <inputType>input</inputType>
    </variable>
</fields>
```

---