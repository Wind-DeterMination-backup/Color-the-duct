# src/main/java/colortheducts Detail

## 本层职责

这个包是模组的唯一代码包，也是 `mod.json` 中 `main` 字段指向的入口包。当前只有 `ColorTheDuctsMod.java` 一个类，因此整个模组逻辑都在这个类里闭环实现。

## `ColorTheDuctsMod.java` 做了什么

- 继承 `mindustry.mod.Mod`，把自己注册为 Mindustry Java 模组。
- 在 `ClientLoadEvent` 上设置默认配置并注册设置面板。这保证 UI 已经完成初始化后再向设置页注入条目。
- 在 `Trigger.draw` 上执行 `drawLiquidSquares()`，把显示逻辑挂到每帧绘制阶段，而不是修改建筑本身的 update 逻辑。
- 定义了五个设置键：
  - 启用总开关。
  - 方块缩放。
  - 是否只高亮鼠标悬停导管。
  - 悬停时是否沿整条连通线高亮。
  - 染色透明度。
- `drawLiquidSquares()` 会先判定世界和游戏状态是否有效，再决定是“全图遍历 `Groups.build`”还是“只处理鼠标目标”。
- `drawConnectedLine()` 用 BFS 风格的队列遍历整条液体导管链，并用 `IntSet` 分别做已入队和已访问去重，避免桥接结构造成重复遍历。
- 连通性判断不只看相邻四方向，还额外处理：
  - `LiquidJunction` 的对向穿透。
  - `ItemBridge` / `DirectionBridge` 家族的显式链接与占用信息。
  - `getLiquidDestination()` 所代表的真实液体转移方向。
- `drawForBuilding()` 最终只做一件事：读取当前液体颜色，在建筑中心按缩放值画一个方块，渲染层级放在 `Layer.blockOver + 0.1f`，确保覆盖在方块之上但不过度干扰其他界面。

## 实现方式上的特点

- 这是纯客户端渲染增强，不改方块数据结构，不改液体流动逻辑，也不要求服务器安装。
- 包内没有额外工具类，说明作者选择了“单类集中实现”。对于当前体量，这降低了跳转成本，但后续若功能继续扩张，可能会开始显得拥挤。
- 设置文本没有硬编码成人类可读文案，而是交给 `bundle` 资源文件，通过 key 查找，这符合仓库约束里“用户可见文案优先走资源文件”的要求。

## 与其他层级的关系

- 上游依赖 `src/main/resources/bundles` 提供设置分类和描述文本。
- 下游会被编译成 `build/classes/java/main/colortheducts/ColorTheDuctsMod.class`。
- 再下游它既会进入普通桌面包，也会先装入 D8 输入 JAR，再转成安卓 `classes.dex`。
