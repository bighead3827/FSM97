# MvpToolkit 库详细使用指南

## 目录
- [概述](#概述)
- [安装](##安装)
- [核心架构](#核心架构)
- [MVP 模式](#mvp-模式)
- [配置系统](#配置系统)
- [导航系统](#导航系统)
- [MiniTween 动画系统](#minetween-动画系统)
- [数据模型](#数据模型)
- [组件库](#组件库)
- [工具类](#工具类)

---

## 概述

MvpToolkit 是一个为 Unity 游戏开发设计的轻量级架构库，版本为 0.14.2，基于 Model-View-Presenter (MVP) 模式构建。它主要用于快速构建 UI 繁重的游戏。

**主要特点：**
- 轻量级架构，没有外部依赖
- 支持依赖注入的配置系统
- 内置导航系统
- 内置简单的动画系统 (MiniTween)
- 响应式数据模型
- 提供多个示例项目

---

## 安装

### 使用 Package Manager 安装

1. **通过 OpenUPM 安装（推荐）：**
   - 在 Unity 的 Project Settings 中添加 Scoped Registry：
     - URL: `https://package.openupm.com`
     - Scope: `com.behc`
   - 在 Package Manager 中选择 "My Registries"，搜索并安装 MvpToolkit

2. **通过 Git 链接直接安装：**
   - 在 Package Manager 中选择 "Add package from git URL"
   - 输入仓库的 Git URL

### 示例项目

库包含两个示例项目：
- **Data Presenters Gallery** - 展示所有数据 Presenter 和动画选项（需要 TextMesh Pro）
- **Simple Game** - 基础游戏 UI 设置，包含场景间导航（需要 TextMesh Pro）

---

## 核心架构

MvpToolkit 主要分为以下几个模块：

1. **Configuration** - 配置和依赖注入系统
2. **MVP** - Model-View-Presenter 架构实现
3. **Navigation** - 场景和 UI 导航系统
4. **MiniTween** - 轻量级补间动画系统
5. **Utils** - 工具类和辅助方法

---

## MVP 模式

### Presenter 基础

Presenter 是 MVP 模式的核心，负责连接 Model 和 View。

#### 基础 Presenter 类

**PanelPresenter** (`Runtime/Mvp/Presenters/PanelPresenter.cs`)
```csharp
namespace Behc.Mvp.Presenters
{
    public class PanelPresenter<T> : PanelPresenterBase<T> where T : class
    {   
        public sealed override void Initialize(IPresenterMap presenterMap, PresenterUpdateKernel kernel)
        {
            base.Initialize(presenterMap, kernel);
        }
        
        public sealed override void Activate()
        {
            base.Activate();
        }

        public sealed override void Deactivate()
        {
            base.Deactivate();
        }
    }
}
```

#### Presenter 生命周期

Presenter 主要生命周期方法：
- `Initialize()` - 初始化
- `Activate()` - 激活
- `Deactivate()` - 停用
- `OnBind()` - 绑定数据
- `OnUnbind()` - 解绑数据
- `OnAnimateShow()` - 显示动画
- `OnAnimateHide()` - 隐藏动画

#### 常用 Presenter 类型

1. **PanelPresenter** - 基础面板 Presenter
2. **PanelCanvasPresenter** - Canvas 面板 Presenter
3. **PanelCanvasGroupPresenter** - CanvasGroup 面板 Presenter（支持透明度控制）
4. **DataSlotPresenter** - 单个数据槽 Presenter
5. **DataSlotCurtainPresenter** - 带幕布动画的数据槽 Presenter
6. **DataSlotSlidePresenter** - 带滑动动画的数据槽 Presenter
7. **DataCollectionPresenter** - 数据集合 Presenter
8. **DataCollectionAnimatedPresenter** - 带动画的数据集合 Presenter
9. **DataCollectionSimplePresenter** - 简单数据集合 Presenter
10. **DataStackPresenter** - 数据堆栈 Presenter

#### Presenter 工厂

库提供了多种 Presenter 工厂实现：

- **SimpleFactory** - 简单工厂
- **SimpleInjectableFactory** - 支持注入的简单工厂
- **PooledFactory** - 对象池工厂
- **PooledInjectableFactory** - 支持注入的对象池工厂
- **SingleInstanceFactory** - 单例工厂

---

## 配置系统

### MiniDiContainer - 轻量级依赖注入容器

**文件位置：** `Runtime/Configuration/MiniDiContainer.cs`

MiniDiContainer 是一个简单但功能强大的依赖注入容器。

#### 基本用法

```csharp
// 创建容器
var container = new MiniDiContainer();

// 绑定实例
container.BindInstance<IMyService>(myServiceInstance);

// 绑定命名实例
container.BindNamedInstance<IMyService>("serviceName", myServiceInstance);

// 解析实例
var service = container.Resolve(typeof(IMyService), null, true) as IMyService;
```

#### 绑定方法

- `BindInstance<T>(T instance)` - 绑定类型实例
- `BindInstance(Type type, object instance)` - 绑定类型实例（非泛型）
- `BindNamedInstance<T>(string name, T instance)` - 绑定命名实例
- `BindInstance(Type type, string name, object instance)` - 绑定命名实例（非泛型）

#### 解析方法

- `Resolve(Type type, string name, bool allowUnnamed)` - 解析实例

### SceneConfigurator - 场景配置器

**文件位置：** `Runtime/Configuration/SceneConfigurator.cs`

SceneConfigurator 用于在场景加载时配置依赖注入容器。

#### 使用示例

```csharp
public class MySceneConfigurator : SceneConfigurator
{
    protected void OnLoad(IDependencyResolver resolver)
    {
        // 在场景加载时执行配置
        var container = (MiniDiContainer)resolver;
        container.BindInstance<IMyService>(new MyService());
    }

    protected void OnUnload(IDependencyResolver resolver)
    {
        // 在场景卸载时执行清理
    }
}
```

### ScriptableConfigurator - ScriptableObject 配置器

类似 SceneConfigurator，但基于 ScriptableObject，适合全局配置。

### ConfiguratorSet - 配置器集合

用于组织多个配置器。

### 配置加载器

库提供多种配置加载器：

- **BuildSceneConfiguratorLoader** - 从构建场景加载
- **ResourceScriptableConfiguratorLoader** - 从 Resources 加载
- **AddressableSceneConfiguratorLoader** - 从 Addressables 加载场景
- **AddressableScriptableConfiguratorLoader** - 从 Addressables 加载 ScriptableObject
- **AssetBundleScriptableConfiguratorLoader** - 从 AssetBundle 加载

---

## 导航系统

### NavigationManager - 导航管理器

**文件位置：** `Runtime/Navigation/NavigationManager.cs`

NavigationManager 管理场景和 UI 的导航堆栈。

#### 导航模式

```csharp
public enum NavigationMode
{
    REPLACE_LAST,    // 替换最后一个
    REPLACE_ALL,     // 替换所有
    KEEP_ALL         // 保留所有（推入新的）
}
```

#### 基本用法

```csharp
// 创建导航管理器
var navigationManager = new NavigationManager(navigableFactory);

// 导航到新页面
navigationManager.NavigateTo("pageName", parameters, NavigationMode.REPLACE_LAST, true);

// 返回上一页
navigationManager.NavigateBack();

// 提交延迟的导航更改（需要在 Update 中调用）
navigationManager.CommitDeferredChanges();

// 获取当前活动的导航点
if (navigationManager.TryGetActiveNavigationPoint(out string name, out INavigable navigable))
{
    // 使用 name 和 navigable
}

// 获取所有导航点
foreach (var (pointName, pointNavigable) in navigationManager.GetNavigationPoints())
{
    // 遍历导航堆栈
}
```

#### INavigable 接口

导航元素需要实现 `INavigable` 接口：

```csharp
public interface INavigable
{
    void Start();
    void Stop();
    void Restart(object parameters);
    void LongDispose(Action onComplete);
}
```

### BackButtonManager - 返回按钮管理器

管理返回按钮的行为。

### NavigationRegistry - 导航注册表

注册导航项。

---

## MiniTween 动画系统

### 概述

MiniTween 是一个非常基础的补间动画引擎，只支持简单的动画，不支持序列或复杂参数如循环。

**文件位置：** `Runtime/MiniTween/`

### 获取 MiniTween 实例

MiniTween 没有静态/单例实例，需要通过 ScriptableObject Provider 获取：

```csharp
[SerializeField] private AbstractProvider<ITweenSystem> _miniTweenProvider;

// 获取实例
var tweenSystem = _miniTweenProvider.GetInstance();
```

### 基本动画用法

```csharp
// CanvasGroup 透明度动画
_canvasGroup.AnimateAlpha(tweenSystem, 1.0f, 0.3f)
    .SetCompleteCallback(onFinish);

// Image 颜色动画
_image.AnimateColor(tweenSystem, targetColor, 0.5f);

// RectTransform 位置动画
_rectTransform.Animate AnchoredPosition(tweenSystem, targetPosition, 0.3f);

// RectTransform 缩放动画
_rectTransform.AnimateScale(tweenSystem, targetScale, 0.3f);
```

### 内置扩展方法

#### CanvasGroupTweenExtensions
- `AnimateAlpha()` - 透明度动画

#### ImageTweenExtensions
- `AnimateColor()` - 颜色动画

#### RectTransformTweenExtensions
- `AnimateAnchoredPosition()` - 锚点位置动画
- `AnimateScale()` - 缩放动画
- `AnimateSizeDelta()` - 大小动画

#### TextMeshProTweenExtensions
- `AnimateColor()` - 颜色动画
- `AnimateFontSize()` - 字体大小动画

### 自定义动画

```csharp
tweenSystem.Animate(
    owner: this,
    setter: (obj, value) => { /* 设置值 */ },
    from: startValue,
    to: endValue,
    duration: 0.5f
)
.SetCompleteCallback(() => { /* 动画完成回调 */ })
.SetUpdateCallback(() => { /* 每帧更新回调 */ })
.SetEase(animationCurve); // 设置动画曲线
```

### ITween 接口方法

- `Kill()` - 终止动画
- `Complete()` - 立即完成动画
- `SetCurrentTime(float time)` - 设置当前时间
- `SetId(string id)` - 设置 ID
- `SetUpdateCallback(Action onUpdate)` - 设置更新回调
- `SetCompleteCallback(Action onComplete)` - 设置完成回调
- `SetEase(AnimationCurve curve)` - 设置缓动曲线

### 在 Presenter 中使用 MiniTween

```csharp
public class ExamplePanelPresenter : PanelPresenterBase<ExamplePanel>
{
    [SerializeField] private CanvasGroup _canvasGroup;
    [SerializeField] private AbstractProvider<ITweenSystem> _miniTweenProvider;

    protected override void OnBind(bool prepareForAnimation)
    {
        // 初始化
        _canvasGroup.alpha = prepareForAnimation ? 0 : 1;
    }

    protected override void OnAnimateShow(float startTime, Action onFinish)
    {
        _canvasGroup.AnimateAlpha(
            _miniTweenProvider.GetInstance(), 
            1.0f, 
            0.3f
        ).SetCompleteCallback(onFinish);
    }
}
```

---

## 数据模型

### ReactiveModel - 响应式模型基类

**文件位置：** `Runtime/Mvp/Models/ReactiveModel.cs`

ReactiveModel 是所有响应式模型的基类，提供了变化通知机制。

```csharp
public class ReactiveModel : IReactive
{
    public IDisposable Subscribe(Action action);
    protected void NotifyChanges();
}
```

#### 使用示例

```csharp
public class PlayerModel : ReactiveModel
{
    private int _health;
    
    public int Health
    {
        get => _health;
        set
        {
            if (_health == value) return;
            _health = value;
            NotifyChanges();
        }
    }
}

// 订阅变化
var subscription = playerModel.Subscribe(() => 
{
    Debug.Log("Player model changed!");
});

// 取消订阅
subscription.Dispose();
```

### DataSlot - 数据槽

**文件位置：** `Runtime/Mvp/Models/DataSlot.cs`

DataSlot 用于存储单个数据对象。

```csharp
// 非泛型版本
var slot = new DataSlot();
slot.Data = myData;

// 泛型版本
var slot = new DataSlot<MyDataType>();
slot.Data = myData;

// 订阅变化
slot.Subscribe(() => { /* 数据变化 */ });
```

### DataCollection - 数据集合

**文件位置：** `Runtime/Mvp/Models/DataCollection.cs`

DataCollection 用于存储数据集合。

```csharp
// 非泛型版本
var collection = new DataCollection();
collection.Add(item1);
collection.AddRange(items);
collection.Remove(item);
collection.Clear();
collection.Refresh();

// 泛型版本
var collection = new DataCollection<MyItemType>();
collection.Add(item);
collection.Remove(item);
collection.Clear();

// 订阅变化
collection.Subscribe(() => { /* 集合变化 */ });
```

### DataStack - 数据堆栈

类似于数据集合，但有堆栈行为。

### TrackableReactiveModel - 可追踪的响应式模型

扩展了 ReactiveModel，添加了追踪功能。

### ToastManager - 提示管理器

管理提示消息。

### ToolTipManager - 工具提示管理器

管理工具提示。

### PopupMenuManager - 弹出菜单管理器

管理弹出菜单。

---

## 组件库

### 动画组件

**文件位置：** `Runtime/Mvp/Animations/`

- **FadeAnimation** - 淡入淡出动画
- **ScaleAnimation** - 缩放动画

### UI 组件

**文件位置：** `Runtime/Mvp/Components/`

- **ButtonEx** - 扩展按钮，支持长按等功能
- **Curtain** - 幕布基类
- **FadeCurtain** - 淡入淡出幕布
- **SimpleCurtain** - 简单幕布
- **ViewRegion** - 视图区域

#### ButtonEx 使用

```csharp
[SerializeField] private ButtonEx _button;

void Start()
{
    _button.OnClick.AddListener(OnButtonClick);
    _button.OnLongPress.AddListener(OnButtonLongPress);
    _button.OnLongPressCancel.AddListener(OnButtonLongPressCancel);
}
```

### 集合布局

**文件位置：** `Runtime/Mvp/Presenters/Layout/`

- **FixedHorizontalListLayout** - 固定水平列表布局
- **FixedVerticalListLayout** - 固定垂直列表布局
- **VariableHorizontalListLayout** - 可变水平列表布局
- **VariableVerticalListLayout** - 可变垂直列表布局
- **GridLayout** - 网格布局

---

## 工具类

### AbstractProvider - 抽象提供者

**文件位置：** `Runtime/Utils/AbstractProvider.cs`

ScriptableObject 支持的服务提供者。

```csharp
[SerializeField] private AbstractProvider<IMyService> _serviceProvider;

// 获取服务实例
var service = _serviceProvider.GetInstance();
```

### TickerManager - 帧管理器

管理需要每帧更新的系统。

```csharp
public interface ITickable
{
    void Tick(float deltaTime);
}

// 注册
tickerManager.Register(tickable);

// 注销
tickerManager.Unregister(tickable);
```

### 工厂接口

**文件位置：** `Runtime/Utils/IFactory.cs`

```csharp
public interface IFactory<TParam, TResult>
{
    TResult Create(TParam param);
}

public interface IFactory<TParam1, TParam2, TResult>
{
    TResult Create(TParam1 param1, TParam2 param2);
}
```

### 注入接口

**文件位置：** `Runtime/Utils/IInjectable.cs`

标记可注入的类。

### 扩展方法

**文件位置：** `Runtime/Utils/DisposableExtensions.cs`
**文件位置：** `Runtime/Utils/EventsExtensions.cs`
**文件位置：** `Runtime/Utils/UnityObjectExtensions.cs`

### 辅助类

- **GenericDisposable** - 通用 disposable
- **SmallSet** - 小型集合
- **WhenAll** - 等待所有任务完成
- **WhenBoth** - 等待两个任务完成

---

## 完整示例：创建一个简单的 UI 面板

### 1. 创建 Model

```csharp
using Behc.Mvp.Models;

public class MyPanelModel : ReactiveModel
{
    private string _title;
    private int _counter;

    public string Title
    {
        get => _title;
        set
        {
            if (_title == value) return;
            _title = value;
            NotifyChanges();
        }
    }

    public int Counter
    {
        get => _counter;
        set
        {
            if (_counter == value) return;
            _counter = value;
            NotifyChanges();
        }
    }
}
```

### 2. 创建 View（MonoBehaviour）

```csharp
using UnityEngine;
using UnityEngine.UI;
using TMPro;

public class MyPanelView : MonoBehaviour
{
    [SerializeField] private TextMeshProUGUI _titleText;
    [SerializeField] private TextMeshProUGUI _counterText;
    [SerializeField] private Button _incrementButton;
    [SerializeField] private CanvasGroup _canvasGroup;

    public TextMeshProUGUI TitleText => _titleText;
    public TextMeshProUGUI CounterText => _counterText;
    public Button IncrementButton => _incrementButton;
    public CanvasGroup CanvasGroup => _canvasGroup;
}
```

### 3. 创建 Presenter

```csharp
using Behc.Mvp.Presenters;
using Behc.MiniTween;
using UnityEngine;

public class MyPanelPresenter : PanelPresenter<MyPanelView>
{
    [SerializeField] private AbstractProvider<ITweenSystem> _miniTweenProvider;
    
    private MyPanelModel _model;
    private System.IDisposable _subscription;

    public void Initialize(MyPanelModel model)
    {
        _model = model;
    }

    protected override void OnBind(bool prepareForAnimation)
    {
        _subscription = _model.Subscribe(UpdateView);
        View.IncrementButton.onClick.AddListener(OnIncrementClicked);
        
        View.CanvasGroup.alpha = prepareForAnimation ? 0 : 1;
        UpdateView();
    }

    protected override void OnUnbind()
    {
        _subscription?.Dispose();
        View.IncrementButton.onClick.RemoveListener(OnIncrementClicked);
    }

    protected override void OnAnimateShow(float startTime, System.Action onFinish)
    {
        View.CanvasGroup.AnimateAlpha(
            _miniTweenProvider.GetInstance(),
            1.0f,
            0.3f
        ).SetCompleteCallback(onFinish);
    }

    protected override void OnAnimateHide(float startTime, System.Action onFinish)
    {
        View.CanvasGroup.AnimateAlpha(
            _miniTweenProvider.GetInstance(),
            0.0f,
            0.3f
        ).SetCompleteCallback(onFinish);
    }

    private void UpdateView()
    {
        View.TitleText.text = _model.Title;
        View.CounterText.text = _model.Counter.ToString();
    }

    private void OnIncrementClicked()
    {
        _model.Counter++;
    }
}
```

### 4. 创建 SceneConfigurator

```csharp
using Behc.Configuration;
using Behc.Mvp.Presenters;

public class GameSceneConfigurator : SceneConfigurator
{
    [SerializeField] private MyPanelPresenter _myPanelPrefab;

    protected void OnLoad(IDependencyResolver resolver)
    {
        var container = (MiniDiContainer)resolver;
        
        // 创建 Model
        var model = new MyPanelModel();
        model.Title = "Hello MvpToolkit!";
        model.Counter = 0;
        
        container.BindInstance(model);
        
        // 这里可以配置更多的依赖...
    }
}
```

---

## 最佳实践

1. **使用响应式模型** - 继承 ReactiveModel 并在属性变化时调用 NotifyChanges()
2. **正确管理生命周期** - 在 OnBind 中订阅事件，在 OnUnbind 中取消订阅
3. **使用 Presenter 工厂** - 对于需要频繁创建的 Presenter，使用对象池工厂提高性能
4. **利用配置系统** - 使用 SceneConfigurator 或 ScriptableConfigurator 组织依赖注入
5. **合理使用动画** - MiniTween 适合简单动画，复杂动画考虑使用其他库
6. **遵循导航模式** - 使用 NavigationManager 管理场景和 UI 导航

---

## 已知问题

根据 CHANGELOG.md，当前已知问题：
- MiniTween 不防止访问已终止的 tween

---

## 总结

MvpToolkit 是一个轻量级但功能完整的 Unity 架构库，特别适合构建 UI 繁重的游戏。它提供了：

- 清晰的 MVP 架构实现
- 简单的依赖注入系统
- 实用的导航管理
- 基础的动画支持
- 响应式数据模型
- 丰富的 UI 组件

通过合理使用这些组件，可以快速构建出结构清晰、易于维护的 Unity 游戏项目。
