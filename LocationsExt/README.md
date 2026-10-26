# LocationsExt（旅店 & 公寓房间）

《Degrees of Lewdity》ModLoader 模组：在现有地图上增加**旅店**，并给**公寓楼（Barb Street 公寓）**增加一个**可购买的房间**。

> 适配 **DoL 0.5.12.13** · 需要 ModLoader + **TweeReplacer** · 不修改任何游戏文件

## 新增内容

| 地点 | 位置 | 功能 |
|---|---|---|
| **旅店** | High Street（商业街，底部新增链接） | 花 £40 租一晚单间 → 原版 `<<sleep>>` 睡到早上（恢复疲劳/压力，走原版 `wakingEffects` 逻辑）；**淋浴间**：租客免费，否则 £5 |
| **待售的房间** | Flats（Barb Street 的公寓楼，入口新增链接） | £62000 一次付清买下；买下后入口链接变成「回到自己的房间」 |
| **自己的房间** | 公寓楼走廊尽头 | 睡觉（同原版睡眠流程）、**衣柜**（自定义退出：关上衣柜回房间）、离开 |
| 房间升级 | 自己房间 → 「看看能添置什么」 | 舒适床垫 £300（睡觉额外 −100 压力、−1 trauma）／加装淋浴间 £200（解锁「洗个澡」）／二手书架 £150（每天可看书放松 −200 压力，0:30） |

## 实现方式（全部复用原版机制）

- 睡眠：`<<sleep>>` + `<<effects>>` + `<<wakingEffects $sleepHoursTotal>>`（与游戏里 `Asylum Sleep` 等一致）
- 金钱：`<<money -4000>>`（£40）/ `<<money -6200000>>`（£62000），单位是便士（£1 = 100）
- 购买状态：存档变量 `$caeFlatsRoom`（买下后为 1）
- 旅店住宿次数：`$caeInnStays`（统计用，可为空；也用于判断淋浴是否对租客免费）
- **洗澡**：原版 `<<wash>>` 宏（和拳击俱乐部淋浴同一套清洁逻辑）
- **衣柜**：复制原版 `Wardrobe` 段落的宏组合（`<<wardrobeSelection>>` / `<<wardrobewear>>` / `<<wardrobe>>`），
  只把 `<<wardrobeExits>>` 换成自己的「关上衣柜 → 回房间」链接 + `<<cleanupOnWardrobeExit>>`
- **房间升级**：`$caeFlatsBed` / `$caeFlatsBath` / `$caeFlatsShelf` 三个存档变量；升级一次付清
- **每天一次看书**：`$daily.caeFlatsRead`（游戏每天会重置 `$daily`）

## 接线（2 条 TweeReplacer 补丁）

| 段落 | 锚点 | 插入 |
|---|---|---|
| `High Street` | `<<set $eventskip to 0>>`（段落最后一行，纯代码） | 「旅店」链接 |
| `Flats` | `<<set $eventskip to 0>>`（段落最后一行，纯代码） | 「看看待售的房间」/「回到自己的房间」链接 |

锚点都是**纯代码**（不含英文 UI 文本），汉化 mod 替换文本时不会失效。

## 安装

1. 游戏内 ModLoader → 添加 zip → 选 `LocationsExt_v1.1.0.zip`
2. 刷新页面（或由网站在 `modList.json` 里自动加载）
3. 前置：ModLoader + TweeReplacer

## 卸载

ModLoader 里移除即可；已购买的 `$caeFlatsRoom` 会留在存档里（重装后仍然拥有房间），需要彻底清除可在控制台执行 `<<unset $caeFlatsRoom>>` 对应的 `State.variables.caeFlatsRoom = undefined`。

## 构建

```powershell
Set-ExecutionPolicy -Scope Process Bypass -Force
Set-Location ..\     # modsrc 目录
pwsh -File .\build-locations.ps1
# 或指定游戏目录：
pwsh -File .\build-locations.ps1 -GameDirOverride '<游戏目录>' -OutDir '<游戏目录>'
```

构建会断言 2 个锚点唯一、校验 twee 段落/标签/运算符，生成 `boot.json` 并打包，
合并进 `modList.json`（保留其它 mod 条目），最后校验游戏 HTML 的 SHA256 未变。

## 声明

- 非官方模组，《Degrees of Lewdity》版权归 **Vrelnir** 所有，请支持正版
- 本 mod 只含自写文本，不含任何游戏资源；免费发布、禁止商用
