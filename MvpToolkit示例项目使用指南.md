# MvpToolkit 示例项目使用指南

## 概述

MvpToolkit 提供了两个示例项目，帮助您快速了解和学习库的使用方法：

1. **Data Presenters Gallery** - 展示所有数据 Presenter 和动画选项
2. **Simple Game** - 基础游戏 UI 设置，包含场景间导航

## 共同要求

两个示例项目都有以下共同要求：

1. **TextMesh Pro 包** - 示例中使用了 TextMesh Pro 组件
2. **TextMesh Pro Essentials 资源** - 需要导入 TextMesh Pro 基础资源

## 安装步骤

### 第一步：准备 TextMesh Pro

1. **安装 TextMesh Pro 包**：
   - 打开 Unity Package Manager
   - 搜索 "TextMesh Pro" 并安装

2. **导入 TextMesh Pro Essentials**：
   - 安装后会自动弹出 TextMesh Pro 导入向导
   - 点击 "Import TMP Essential Resources" 导入基础资源
   - 或者在 Window -> TextMeshPro -> Import TMP Essential Resources

### 第二步：安装示例项目

#### 安装 Data Presenters Gallery 示例

1. 在 Unity 编辑器菜单中选择：
   ```
   MvpToolkit -> Data Presenters Gallery Sample
   ```

2. 在打开的窗口中：
   - 如果所有要求都已满足，会显示 "All requirements are met"
   - 点击 "Install Sample" 按钮

3. 安装完成后：
   - 系统会自动配置场景和图层
   - 自动打开 `DataPresentersGalleryRootScene.unity` 场景

#### 安装 Simple Game 示例

1. 在 Unity 编辑器菜单中选择：
   ```
   MvpToolkit -> Simple Game Sample
   ```

2. 在打开的窗口中：
   - 如果所有要求都已满足，会显示 "All requirements are met"
   - 点击 "Install Sample" 按钮

3. 安装完成后：
   - 系统会自动配置场景和图层
   - 自动打开 `SimpleGamePreloaderScene.unity` 场景

## 示例项目详解

### Data Presenters Gallery 示例

**功能**：展示所有数据 Presenter 和动画选项，帮助您了解不同类型的 Presenter 如何工作。

**包含的场景**：
- `DataPresentersGalleryRootScene` - 根场景
- `DataPresentersGalleryElementsScene` - 元素场景

**主要内容**：
- 展示各种数据展示方式（列表、网格、滑动等）
- 演示不同的动画效果
- 展示数据绑定和更新机制

**使用方法**：
1. 安装完成后进入 Play 模式
2. 浏览不同的 Presenter 示例
3. 查看代码结构了解实现方式

### Simple Game 示例

**功能**：展示基础游戏 UI 设置和场景间导航，帮助您了解如何构建完整的游戏 UI 流程。

**包含的场景**：
- `SimpleGamePreloaderScene` - 预加载场景
- `SimpleGameRootScene` - 根场景
- `SimpleGameLobbyScene` - 大厅场景
- `SimpleGameGameplayScene` - 游戏场景
- `SimpleGameRankingScene` - 排行榜场景

**主要内容**：
- 场景间导航
- UI 面板切换
- 简单的游戏流程
- 数据传递和状态管理

**使用方法**：
1. 安装完成后进入 Play 模式
2. 跟随游戏流程体验导航功能
3. 查看代码结构了解如何实现场景管理和导航

## 技术细节

### 安装机制

示例项目使用了以下安装机制：

```csharp
// 安装器类使用 InitializeOnLoad 特性，在 Unity 编辑器加载时执行
[InitializeOnLoad]
public static class DataPresentersGallerySampleInstaller {
    // 检查 TextMesh Pro 是否安装
    public static bool HasTextMeshProPackage() {
        // ...
    }
    
    // 检查 TextMesh Pro Essentials 是否导入
    public static bool HasTextMeshProEssentials() {
        // ...
    }
    
    // 安装资源包
    public static void InstallAssetPackage() {
        AssetDatabase.ImportPackage(_ASSET_PACKAGE_PATH, false);
    }
    
    // 配置场景
    private static void SetupScenes() {
        // ...
    }
    
    // 配置图层
    private static void SetupLayers() {
        // ...
    }
}
```

### 资源包位置

示例项目的资源包位于：
- Data Presenters Gallery: `Packages/com.behc.mvptoolkit/AssetPackages/sample_gallery.unitypackage`
- Simple Game: `Packages/com.behc.mvptoolkit/AssetPackages/simple_game.unitypackage`

## 学习建议

1. **先运行示例**：
   - 先安装并运行示例，了解整体效果

2. **查看代码结构**：
   - 查看场景中的 GameObject 结构
   - 研究 Presenter 和 Model 的实现
   - 了解数据绑定和更新机制

3. **修改和扩展**：
   - 尝试修改现有代码
   - 添加新的 Presenter 或功能
   - 实践学到的知识

4. **结合文档**：
   - 参考 [MvpToolkit 使用指南](MvpToolkit使用指南.md) 深入理解每个组件的作用
   - 了解更高级的用法和最佳实践

## 常见问题

### Q: 安装时提示 TextMesh Pro 相关错误

**A:** 确保已正确安装 TextMesh Pro 包并导入了 Essentials 资源。可以通过以下步骤验证：
- 检查是否存在 `Assets/TextMesh Pro/Resources/TMP Settings.asset` 文件
- 在 Package Manager 中确认 TextMesh Pro 已安装

### Q: 安装后没有自动打开场景

**A:** 可以手动打开以下场景：
- Data Presenters Gallery: `Assets/DataPresentersGallery/RootScene/DataPresentersGalleryRootScene.unity`
- Simple Game: `Assets/SimpleGame/PreloaderScene/SimpleGamePreloaderScene.unity`

### Q: 示例运行时出现错误

**A:** 确保已安装正确版本的 Unity（至少 2019.4）和 MvpToolkit 包。可以尝试重新导入示例项目。

## 总结

MvpToolkit 的示例项目是学习该库的最佳方式。通过安装和运行这些示例，您可以快速了解如何使用 MVP 模式构建 Unity 项目，以及如何使用库提供的各种组件。

建议按照以下顺序学习：
1. 先运行 Simple Game 示例，了解整体架构和导航系统
2. 然后运行 Data Presenters Gallery 示例，学习不同类型的 Presenter
3. 结合使用指南深入理解每个组件的原理和用法
4. 尝试在自己的项目中应用学到的知识
