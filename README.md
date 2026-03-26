# ISV多语言补丁包合并工具

将ISV多语言补丁包合并到产品补丁包结构中，自动完成解压、目录重组和datamodel.xml更新。

## 功能特性

- ✅ 自动提取语言代码（从文件名解析）
- ✅ 批量解压dm目录下的所有zip文件
- ✅ 自动创建 `mul_<语言代码>` 目录结构
- ✅ 自动更新datamodel.xml应用列表
- ✅ 智能冲突检测与处理
- ✅ 规范性校验（检查kdpkgs.xml）

## 使用场景

当需要将ISV多语言补丁包（如泰语、越南语等）合并到产品补丁包时，使用本技能自动化处理繁琐的解压、重组和配置更新工作。

## 参数说明

| 参数 | 必填 | 说明 |
|------|------|------|
| `zip_path` | 是 | 多语言补丁包zip文件的完整路径 |
| `target_dir` | 否 | 目标路径，默认为zip文件所在目录 |

## 终止条件

以下情况会终止任务执行：

1. **文件不存在**: `zip_path` 指定的文件不存在
2. **非规范补丁包**: zip文件中缺少 `kdpkgs.xml` 文件
3. **用户取消**: 存在datamodel冲突时用户选择取消

## 输出结构

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
```
zip_path: D:\Work\CUS.DIGI.V7.0.6.001_0323.TH.zip
target_dir: D:\Work\Output
```

### 输出
```
D:\Work\Output\datamodel\mul_TH\datamodel.xml
D:\Work\Output\datamodel\mul_TH\1.5\TH\digi_asset_mgt\
D:\Work\Output\datamodel\mul_TH\1.5\TH\digi_assets\
...
```

## 安装

将此技能文件夹复制到 QoderWork 技能目录：

```bash
# Windows
%USERPROFILE%\.qoderwork\skills\isv-multilingual-patch-merger\

# macOS/Linux
~/.qoderwork/skills/isv-multilingual-patch-merger/
```

## 版本历史

- **v1.0** - 初始版本，支持基本的合并功能
- **v1.1** - 增加规范性校验（kdpkgs.xml检查）

## License

MIT
