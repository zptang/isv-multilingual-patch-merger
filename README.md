# ISV多语言包处理工具

将ISV多语言补丁包合并到产品补丁包结构中，自动完成解压、目录重组和datamodel.xml更新。支持批量处理多个补丁包。

## 功能特性

- ✅ 批量处理多个多语言补丁包
- ✅ 自动识别语言代码（从datamodel.xml的item path属性解析）
- ✅ 批量解压dm目录下的所有zip文件
- ✅ 自动创建 `mul_<语言代码>` 目录结构
- ✅ 自动更新datamodel.xml应用列表
- ✅ 智能冲突检测与处理（每个包单独确认）
- ✅ 规范性校验（检查kdpkgs.xml）

## 使用场景

当需要将多个ISV多语言补丁包（如泰语、越南语、印尼语等）批量合并到产品补丁包时，使用本工具自动化处理繁琐的解压、重组和配置更新工作。

## 界面说明

- **多语言补丁包列表**: 显示已选择的补丁包，支持添加、移除、清空操作
- **目标目录**: 统一的目标目录（可选），留空则使用各补丁包所在目录
- **开始处理**: 逐个处理列表中的所有补丁包

## 批量处理流程

1. 点击"添加..."选择多个多语言补丁包zip文件
2. （可选）设置统一的目标目录
3. 点击"开始处理"
4. 工具会逐个处理每个补丁包：
   - 检查并确认datamodel冲突
   - 解压dm目录下的zip文件
   - 读取datamodel.xml识别语言代码
   - 创建mul_<语言代码>目录并移动文件
   - 更新datamodel.xml
5. 处理完成后显示统计结果

## 参数说明

| 参数 | 必填 | 说明 |
|------|------|------|
| 多语言补丁包列表 | 是 | 一个或多个多语言补丁包zip文件 |
| 目标目录 | 否 | 统一的目标目录，默认为各补丁包所在目录 |

## 语言代码识别

语言代码通过以下方式自动识别：

1. 解压补丁包后，读取 `datamodel/datamodel.xml`
2. 获取第一个 `<item>` 节点的 `path` 属性
3. 取 `path` 值中第一个 `/` 之前的部分作为语言代码
4. 示例：`path="TH/digi_asset_mgt"` → 语言代码为 `TH`

## 终止条件

以下情况会跳过当前补丁包或终止任务：

1. **文件不存在**: 补丁包文件不存在
2. **非规范补丁包**: zip文件中缺少 `kdpkgs.xml` 文件
3. **用户取消**: 存在datamodel冲突时用户选择取消（跳过当前包，继续处理下一个）

## 输出结构

每个补丁包处理完成后，目标目录下的结构为:
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
- 补丁包列表:
  - `D:\Work\Patches\CUS.DIGI.V7.0.6.001_0323.TH.zip` (泰语)
  - `D:\Work\Patches\CUS.DIGI.V7.0.6.001_0323.VN.zip` (越南语)
  - `D:\Work\Patches\CUS.DIGI.V7.0.6.001_0323.ID.zip` (印尼语)
- 目标目录: `D:\Work\Output` (或留空)

### 输出
```
D:\Work\Output\datamodel\mul_TH\datamodel.xml
D:\Work\Output\datamodel\mul_TH\1.5\TH\digi_asset_mgt\
...
D:\Work\Output\datamodel\mul_VN\datamodel.xml
D:\Work\Output\datamodel\mul_VN\1.5\VN\digi_asset_mgt\
...
D:\Work\Output\datamodel\mul_ID\datamodel.xml
D:\Work\Output\datamodel\mul_ID\1.5\ID\digi_asset_mgt\
...
```

## 安装

将项目文件夹复制到任意目录，确保已安装 Java 8 或更高版本。

### 运行方式
```bash
# Windows
java -jar isv-multilingual-patch-merger-1.0.0.jar

# 或使用启动脚本
run.bat
```

## 系统要求

- Windows 7/8/10/11
- Java 8 或更高版本（JRE/JDK）

## 版本历史

- **v1.0** - 初始版本，支持基本的合并功能
- **v1.1** - 增加规范性校验（kdpkgs.xml检查）
- **v1.2** - 语言代码改为从datamodel.xml自动识别，工具更名为ISV多语言包处理工具
- **v2.0** - 支持批量处理多个补丁包，界面增加列表管理功能

## License

MIT
