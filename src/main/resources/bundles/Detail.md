# src/main/resources/bundles Detail

## 本层职责

`bundles/` 是当前模组的国际化文案层。这里不实现逻辑，但它直接决定设置菜单中玩家看到的分类名、开关名、说明文字和“关闭”字样。

## 文件作用

- `bundle.properties`：默认语言文本，当前内容为英文。
- `bundle_zh_CN.properties`：简体中文文本。

## 实现方式

源码通过 `Core.bundle.get(...)` 和 `table.checkPref` / `sliderPref` 的 key 约定读取这些文本。键名采用 `ctd.*` 与 `setting.ctd-*` 结构，形成比较清晰的命名空间，避免与其他模组或游戏内置文本冲突。

## 与其他层级的关系

- 上游属于 `src/main/resources/`。
- 下游会被复制到 `build/resources/main/bundles/`，并进一步被打进 `build/release-stage`、`build/libs` 与 `dist/` 中的包。

## 细节观察

- 中文文本不是完全翻译英文标题，而是保留了“导管染色 Color-the-ducts”这种中英混合命名，兼顾中文可读性和项目辨识度。
- `ctd.scale.off` 这种文本 key 被代码中的 slider 文本格式化逻辑直接使用，说明 bundle 参与的不只是静态说明，还参与设置值展示。
