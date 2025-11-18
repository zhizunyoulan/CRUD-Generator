## 概述

高级定制功能通过XML配置方式，让您为代码生成过程定义自定义变量，满足特定业务场景需求。变量分为字段级变量和功能级变量两个层级。

---

## ⚙️ 自定义变量配置

### 配置结构

```xml
<custom>
    <!-- 字段级变量定义 -->
    <fields belongTo="read" label="检出字段" fieldsProperty="selectFields" 
            propertiesProperty="selectProperties" showCheckbox="true" sortable="false">
        <variable belongTo="read.selectFields.selectProperties">
            <property>show</property>
            <label>是否显示</label>
            <javaType>Boolean</javaType>
            <defaultValue>false</defaultValue>
            <inputType>input</inputType>
        </variable>
    </fields>
    
    <!-- 功能级变量定义 -->
    <variable belongTo="read">
        <property>paging</property>
        <label>是否分页</label>
        <javaType>Boolean</javaType>
        <defaultValue>false</defaultValue>
        <inputType>input</inputType>
    </variable>
</custom>
```

### 字段级变量 (Fields-level Variables)

字段级变量作用于特定功能的字段集合上，通过 `<fields>` 标签定义：

**属性说明：**
- `belongTo`: 所属功能（read, create, update, delete）
- `label`: 在界面上显示的标签文本
- `fieldsProperty`: 对应的字段集合属性名
- `propertiesProperty`: 字段属性对象名
- `showCheckbox`: 是否显示复选框
- `sortable`: 是否可排序

**示例配置：**
```xml
<!-- 查询功能的检出字段配置 -->
<fields belongTo="read" label="检出字段" fieldsProperty="selectFields" 
        propertiesProperty="selectProperties" showCheckbox="true" sortable="false">
    <variable belongTo="read.selectFields.selectProperties">
        <property>show</property>
        <label>是否显示</label>
        <javaType>Boolean</javaType>
        <defaultValue>false</defaultValue>
        <inputType>input</inputType>
    </variable>
</fields>

<!-- 创建功能的表单字段配置 -->
<fields belongTo="create" label="表单字段" fieldsProperty="formFields" 
        propertiesProperty="formProperties" showCheckbox="true" sortable="false">
    <variable belongTo="create.formFields.formProperties">
        <property>color</property>
        <label>颜色</label>
        <javaType>String</javaType>
        <inputType>input</inputType>
    </variable>
</fields>
```

### 功能级变量 (Function-level Variables)

功能级变量直接作用于整个功能，通过 `<variable>` 标签定义：

**示例配置：**
```xml
<!-- 查询功能的分页控制 -->
<variable belongTo="read">
    <property>paging</property>
    <label>是否分页</label>
    <javaType>Boolean</javaType>
    <defaultValue>false</defaultValue>
    <inputType>input</inputType>
</variable>

<!-- 创建功能的导入控制 -->
<variable belongTo="create">
    <property>enableImport</property>
    <label>是否导入</label>
    <javaType>Boolean</javaType>
    <defaultValue>false</defaultValue>
    <inputType>input</inputType>
</variable>
```

### 变量属性详解

#### `<variable>` 子标签：

| 标签 | 说明 | 示例 |
|------|------|------|
| `property` | 变量属性名，在模板中访问的键名 | `show`, `paging` |
| `label` | 界面显示标签 | `"是否显示"`, `"是否分页"` |
| `javaType` | 变量数据类型 | `Boolean`, `String`, `Integer` |
| `defaultValue` | 默认值（可选） | `false`, `"pageSize"` |
| `inputType` | 输入控件类型 | `input`, `select` |
| `condition` | 显示条件（可选） | `paging[=]true` |
| `options` | 下拉选项（select类型专用） | 选项列表 |

#### `belongTo` 属性格式：

**字段级变量：**
```
功能名.字段集合名.属性对象名
示例：read.selectFields.selectProperties
```

**功能级变量：**
```
功能名
示例：read, create, update, delete
```

### 条件依赖 (Conditional Dependencies)

通过 `condition` 标签实现变量间的依赖关系：

```xml
<variable belongTo="read">
    <property>paging</property>
    <label>是否分页</label>
    <javaType>Boolean</javaType>
    <defaultValue>false</defaultValue>
    <inputType>input</inputType>
</variable>

<variable belongTo="read">
    <property>pageSizeProp</property>
    <label>页面大小</label>
    <javaType>String</javaType>
    <defaultValue>pageSize</defaultValue>
    <inputType>input</inputType>
    <condition>paging[=]true</condition>
</variable>
```

**条件语法：**
- `paging[=]true`: 当 paging 等于 true 时显示
- `exportType[!=]excel`: 当 exportType 不等于 excel 时显示
- `enableExport[Exist]`: 当 enableExport 变量存在时显示
- `oldField[NotExist]`: 当 oldField 变量不存在时显示

### 下拉选项配置

对于 `inputType="select"` 的变量，可以定义选项列表：

```xml
<variable belongTo="create.importFields.importProperties">
    <property>javaType</property>
    <label>类型</label>
    <javaType>String</javaType>
    <defaultValue>String</defaultValue>
    <inputType>select</inputType>
    <options>
        <option label="字符串" value="String"></option>
        <option label="布尔" value="Boolean"></option>
        <option label="数字" value="Integer"></option>
        <option label="日期" value="Date"></option>
    </options>
</variable>
```

**下拉选项变量示例：**
```xml
<variable belongTo="read">
    <property>exportType</property>
    <label>导出格式</label>
    <javaType>String</javaType>
    <defaultValue>excel</defaultValue>
    <inputType>select</inputType>
    <options>
        <option label="Excel文件" value="excel"></option>
        <option label="PDF文档" value="pdf"></option>
        <option label="CSV文件" value="csv"></option>
    </options>
    <condition>enableExport[=]true</condition>
</variable>
```

---

## 🎨 在模板中使用变量

### 访问功能级变量

```velocity
## 访问分页变量 - 直接访问，无需customVariables
#if($readDefinition.paging)
    ## 分页查询逻辑
    public Page<${resultModelClass.simpleName}> ${readDefinition.functionName}() {
#else
    ## 普通列表查询
    public List<${resultModelClass.simpleName}> ${readDefinition.functionName}() {
#end

## 访问页面大小属性
#if($readDefinition.pageSizeProp)
    @RequestParam(defaultValue = "20") $readDefinition.pageSizeProp
#end

## 访问导出类型
#if($readDefinition.exportType)
    ## 根据选择的导出类型生成不同代码
    #if($readDefinition.exportType == "excel")
    exportToExcel();
    #elseif($readDefinition.exportType == "pdf")
    exportToPDF();
    #end
#end
```

### 访问字段级变量

```velocity
## 遍历检出字段并检查显示属性
#foreach($field in $readDefinition.selectFields)
    #if($field.selectProperties.show)
    <td>$field.label</td>
    #end
#end

## 访问表单字段的颜色属性
#foreach($field in $createDefinition.formFields)
    #if($field.formProperties.color)
    <el-form-item label="$field.label">
        <el-input style="color: $field.formProperties.color" 
                  v-model="formData.$field.property"/>
    </el-form-item>
    #end
#end
```

### 访问导入字段的类型属性

```velocity
## 处理导入字段的类型映射
#foreach($field in $createDefinition.importFields)
    #if($field.importProperties.javaType)
    ## 根据配置的类型进行数据转换
    #if($field.importProperties.javaType == "Date")
    private Date $field.property;
    #else
    private $field.importProperties.javaType $field.property;
    #end
    #end
#end
```

---

## 📋 典型应用场景

### 1. 分页控制
```xml
<variable belongTo="read">
    <property>paging</property>
    <label>是否分页</label>
    <javaType>Boolean</javaType>
    <defaultValue>true</defaultValue>
    <inputType>input</inputType>
</variable>
```

### 2. 导出功能配置
```xml
<variable belongTo="read">
    <property>enableExport</property>
    <label>启用导出</label>
    <javaType>Boolean</javaType>
    <defaultValue>false</defaultValue>
    <inputType>input</inputType>
</variable>

<variable belongTo="read">
    <property>exportType</property>
    <label>导出格式</label>
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

### 3. 字段显示控制
```xml
<fields belongTo="read" label="检出字段" fieldsProperty="selectFields" 
        propertiesProperty="selectProperties" showCheckbox="true" sortable="false">
    <variable belongTo="read.selectFields.selectProperties">
        <property>show</property>
        <label>是否显示</label>
        <javaType>Boolean</javaType>
        <defaultValue>true</defaultValue>
        <inputType>input</inputType>
    </variable>
</fields>
```

### 4. 表单样式定制
```xml
<fields belongTo="create" label="表单字段" fieldsProperty="formFields" 
        propertiesProperty="formProperties" showCheckbox="true" sortable="false">
    <variable belongTo="create.formFields.formProperties">
        <property>color</property>
        <label>文字颜色</label>
        <javaType>String</javaType>
        <defaultValue>#333333</defaultValue>
        <inputType>input</inputType>
    </variable>
</fields>
```

---