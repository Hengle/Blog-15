# Editor 的调用及初始化

## Editor 界面点击调用静态方法

#### 菜单栏静态方法

```text
[MenuItem("Tools/Init")]
static void Init() {}
```

#### Hierachy 或 Project 右键静态方法

实际上就是菜单栏的 Assets 中的方法

```text
[MenuItem("Assets/Create/Init")]
static void Init() {}
```

## EditorWindow

提供 snippet

```text
using UnityEditor;
using UnityEngine;

public class $WindowName$EditorWindow : EditorWindow {
    public $WindowName$EditorWindow() {
        titleContent = new GUIContent("$WindowName$");
    }

    [MenuItem("Tools/$WindowName$")]
    static void Init() {
        var window = GetWindow<$WindowName$EditorWindow>();
        window.Show();
    }

    private void OnGUI() {

        $selected$ $end$
    }
}
```

## EditorInspector

提供 snippet

```text
using UnityEditor;
using UnityEngine;

[CustomEditor(typeof($TargetClass$))]
public class $TargetClass$Editor : Editor {
    public override void OnInspectorGUI() {
        serializedObject.Update();
        $TargetClass$ comp = target as $TargetClass$;

        $selected$ $end$

        serializedObject.ApplyModifiedProperties();
    }
}
```

## 界面渲染

移步 [Unity/OnGUI](https://github.com/okzkx/Blog/tree/a336a6991dae53d05a744c68889c42072bb07c75/2020/06/17/Unity/OnGUI/README.md)

## 资料

### 综合

[UnityEditor-Windows编辑器与Inspector编辑器详细教程](https://www.jianshu.com/p/97520d98a1f2)

[Unity Editor基础篇](https://www.jianshu.com/p/8432ad6fac64)

### EditorWindow

[https://blog.csdn.net/qq\_35030499/article/details/88350521](https://blog.csdn.net/qq_35030499/article/details/88350521)

[https://docs.unity3d.com/ScriptReference/EditorWindow.html](https://docs.unity3d.com/ScriptReference/EditorWindow.html)

### 资源管理

[Unity编辑器扩展之Project视图面板](https://blog.csdn.net/weixin_42540271/article/details/90761102)

[原创 （二）Unity编辑器扩展之Hierarchy视图面板](https://blog.csdn.net/weixin_42540271/article/details/90815480)

[原创 Unity小工具一 自动改变导入图片类型为sprite模式](https://blog.csdn.net/weixin_42540271/article/details/104622939)

### Addressable

[Unity Addressable Asset system](https://docs.unity3d.com/Packages/com.unity.addressables@1.7/manual/index.html) 

### Input System

### Asset Import Pipline
[The new Asset Import Pipeline: Solid foundation for speeding up asset imports](https://blog.unity.com/technology/the-new-asset-import-pipeline-solid-foundation-for-speeding-up-asset-imports)

