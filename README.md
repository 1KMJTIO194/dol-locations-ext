# DoL 地点扩展 LocationsExt

《Degrees of Lewdity》**ModLoader 模组**：在现有地图上增加**旅店**，并给**公寓楼**（Barb Street 的 Flats）增加一个**可购买的房间**。
**不修改任何游戏文件**，全部通过 ModLoader 的段落替换（TweeReplacer）实现。

> 适配版本：**DoL 0.5.12.13**（0.5.11.9 的补丁锚点同样兼容）
> 语言：中文 · 许可：MIT（代码）

## 新增内容

| 地点 | 位置 | 功能 |
|---|---|---|
| **旅店** | High Street（商业街）底部新增链接 | £40/晚单间（原版 `<<sleep>>` 睡眠流程）；**淋浴间**：租客免费，否则 £5（原版 `<<wash>>`） |
| **待售的房间** | Flats（Barb Street 公寓楼）底部新增链接 | £62000 一次付清买下；买下后链接变为「回到自己的房间」 |
| **自己的房间** | 公寓楼走廊尽头 | 睡觉、**衣柜**（自定义退出：关上衣柜回房间）、离开 |
| 房间升级 | 自己房间 →「看看能添置什么」 | 舒适床垫 £300 ／ 加装淋浴间 £200 ／ 二手书架 £150（每天看书放松一次） |

## 实现方式

- 睡眠/醒来：原版 `<<sleep>>` + `<<wakingEffects $sleepHoursTotal>>`
- 洗澡：原版 `<<wash>>`
- 衣柜：原版 `<<wardrobeSelection>>` / `<<wardrobewear>>` / `<<wardrobe>>`，退出口替换为「关上衣柜 → 回房间」+ `<<cleanupOnWardrobeExit>>`
- 状态变量：`$caeFlatsRoom`（已买房间）、`$caeInnStays`（住宿次数）、`$caeFlatsBed` / `$caeFlatsBath` / `$caeFlatsShelf`（升级）、`$daily.caeFlatsRead`（每日看书）

## 接线（2 条 TweeReplacer 补丁，锚点均为纯代码）

| 段落 | 锚点 | 插入 |
|---|---|---|
| `High Street` | `<<set $eventskip to 0>>` | 「旅店」链接 |
| `Flats` | `<<set $eventskip to 0>>` | 「看看待售的房间」/「回到自己的房间」链接 |

## 安装

1. ModLoader → 添加 zip → 选 `LocationsExt_v1.1.0.zip`
2. 刷新页面
3. 前置：ModLoader + TweeReplacer

## 构建

```powershell
Set-ExecutionPolicy -Scope Process Bypass -Force
pwsh -File .\build-locations.ps1
# 或指定游戏目录：
pwsh -File .\build-locations.ps1 -GameDirOverride '<游戏目录>' -OutDir '<输出目录>'
```

## 声明

- 非官方模组，《Degrees of Lewdity》版权归 **Vrelnir** 所有，请支持正版
- 本 mod 只含自写文本，不含任何游戏资源；免费发布、禁止商用