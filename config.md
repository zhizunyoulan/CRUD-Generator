# config.xml Configuration File Documentation

## Overview

The `config.xml` file defines template configurations for the code generator, supporting separated frontend/backend template management and multi-environment code generation.

## Basic Structure

### Root Node
```xml
<config>
    <!-- Backend template configuration -->
    <templates location="backend">...</templates>
    
    <!-- Frontend template configuration -->
    <templates location="frontend">...</templates>

    <!-- Backend groupId -->
    <groupId>...</groupId>
</config>
```

## templates Node

The `templates` node wraps a group of template definitions.

**Attributes:**
- `location` (Required): Identifies template type
  - `backend`: Backend templates
  - `frontend`: Frontend templates

## groupId Node (Optional)

When the project is not built by maven (such as gradle), please configure this item to provide the name of the backend base package


## profile Node (Optional)

The `profile` node is used for grouping templates, particularly useful for frontend multi-environment scenarios.

**Attributes:**
- `id` (Required): Unique group identifier
- `label` (Required): Display name shown in the preview window of the design page
- `device` (Optional): Specifies frontend device type
  - `desktop`: Desktop devices
  - `mobile`: Mobile devices

## template Node

The `template` node defines configuration for a single template.

**Child Tags:**

### id (Required)
Unique identifier for the template.
**Note:** Some IDs have special meanings within the software and should not be modified arbitrarily.

### sourcePath (Required)
Relative path to the template file, relative to the `templates` directory in the workspace.

### targetPath (Required)
Path for the generated target file:
- **Backend templates**: Path relative to the target module
- **Frontend templates**: Path relative to the frontend project root directory

### condition (Optional)
Conditional expression that controls whether the template is generated. When returns `false`, the corresponding template file will not be generated.

**Available Variables:**
- `multiKey`: Whether the main entity has multiple primary keys
- `enableRead`: Whether query functionality is enabled
- `enableCreate`: Whether create functionality is enabled
- `enableUpdate`: Whether update functionality is enabled
- `enableDelete`: Whether delete functionality is enabled

**Examples:**
```xml
<condition>enableRead</condition>          <!-- Generate only when query is enabled -->
<condition>!enableRead</condition>         <!-- Generate when query is not enabled -->
<condition>enableCreate && enableUpdate</condition>  <!-- Generate when both create and update are enabled -->
```

## Path Variables

The following variables can be used in `sourcePath` and `targetPath`:

- `__base_package__`: Base package name
- `__modelName__`: Model name (camelCase)
- `__kebabCaseModelName__`: Model name (kebab-case)
- `__module__`: Module name

## Built-in Templates Configs

- [Mybatis](./built-in-templates/mybatis/config.xml)
- [JPA](./built-in-templates/jpa/config.xml)
