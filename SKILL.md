---
name: isv-multilingual-patch-merger
description: ISV多语言包处理工具。将ISV多语言补丁包合并到产品补丁包中。当用户需要将多语言补丁包(dm目录下的zip文件)解压、重组并更新datamodel.xml时触发使用。支持从datamodel.xml自动识别语言代码、创建mul_<语言代码>目录结构、更新应用列表。
---

# ISV多语言包处理工具

## 功能说明

本技能用于将ISV多语言补丁包（包含dm目录的zip文件）合并到产品补丁包结构中，自动完成解压、目录重组和datamodel.xml更新。

## 入参

1. **zip_path** (必需): 多语言补丁包zip文件的完整路径
   - 示例: `D:\Work\CUS.DIGI.V7.0.6.001_0323.TH.zip`
   - 若文件不存在，需提示用户并终止任务

2. **target_dir** (可选): 目标路径
   - 若未提供或为空，默认为zip文件所在目录
   - 示例: `D:\Work\Multilingual`

## 执行步骤

### 步骤1: 参数校验与准备

```
1. 检查zip_path是否存在:
   - 若不存在 → 提示"错误：多语言补丁包文件不存在: <路径>"，终止任务
   
2. 确定target_dir:
   - 若target_dir为空 → 使用zip_path所在目录
   
3. 检查冲突:
   - 若target_dir下已存在datamodel文件夹:
     → 使用AskUserQuestion询问用户是否确认删除
     → 若用户确认 → 删除现有datamodel文件夹
     → 若用户取消 → 终止任务
```

### 步骤2: 验证多语言补丁包规范性

```
1. 创建临时解压目录
2. 将zip_path解压到临时目录
3. 检查临时目录下是否存在kdpkgs.xml文件:
   - 若不存在 → 提示"错误：该文件不是规范的多语言补丁包（缺少kdpkgs.xml）"，清理临时目录，终止任务
   - 若存在 → 继续执行
```

### 步骤3: 解压多语言补丁包

```
1. 遍历临时目录/dm/下的所有zip文件:
   - 将每个zip文件解压到target_dir
2. 清理临时目录
```

### 步骤4: 重组目录结构

```
1. 读取target_dir/datamodel/datamodel.xml文件
2. 提取语言代码:
   - 读取第一个<item>节点的path属性值
   - 取path值中第一个"/"之前的部分作为语言代码
   - 示例: path="TH/digi_asset_mgt" → 语言代码为 "TH"
3. 在target_dir/datamodel/下创建目录: mul_<语言代码>
   示例: mul_TH
4. 将target_dir/datamodel/下的所有内容（除mul_<语言代码>外）移动到mul_<语言代码>/中
   - 包括: version目录(如1.5)、datamodel.xml等
```

### 步骤5: 更新datamodel.xml

```
1. 读取mul_<语言代码>/datamodel.xml:
   - 提取ver节点的值 → 保存到变量version
   
2. 清空app节点的所有item子节点

3. 遍历mul_<语言代码>/<version>/<语言代码>/下的所有文件夹:
   - 对每个文件夹名称folder_name:
     - 在app节点下添加:
       <item id="folder_name" name="folder_name" pkname="folder_name" path="<语言代码>/folder_name"/>

4. 修改Package节点的name属性为: mul_<语言代码>

5. 保存datamodel.xml
```

## 输出结果

任务完成后，target_dir下的结构应为:
```
target_dir/
└── datamodel/
    └── mul_<语言代码>/
        ├── datamodel.xml      (已更新)
        └── <version>/
            └── <语言代码>/
                ├── app1/
                ├── app2/
                └── ...
```

## 示例

### 输入
- zip_path: `D:\Work\CUS.DIGI.V7.0.6.001_0323.TH.zip`
- target_dir: `D:\Work\Output` (或留空，默认为`D:\Work`)

### 处理过程
1. 解压dm目录下的zip文件
2. 读取datamodel.xml，识别语言代码: `TH`
3. 创建 `mul_TH` 目录，移入所有文件
4. 更新datamodel.xml，包含所有应用的item

### 输出
```
D:\Work\Output\datamodel\mul_TH\datamodel.xml
D:\Work\Output\datamodel\mul_TH\1.5\TH\digi_asset_mgt\
D:\Work\Output\datamodel\mul_TH\1.5\TH\digi_assets\
...
```
