# Litematica Printer 1.21.11

由 [@slime1207](https://github.com/slime1207) 維護的 Minecraft 1.21.11 移植版本。

Litematica Printer 是 Litematica 的列印功能擴充，可以自動選取並放置玩家附近的正確方塊，協助快速完成大型建築。本分支以 1.21.4 版本為基礎，完成 Minecraft 1.21.11 API 遷移與封包同步修復。

> 本專案是 Litematica Printer 的非官方分支。遇到本模組造成的問題時，請勿向 Litematica 原作者回報。

## 版本需求

| 元件 | 版本 |
| --- | --- |
| Minecraft | 1.21.11 |
| Java | 21 或以上 |
| Fabric Loader | 0.18.1 或以上 |
| Fabric API | 0.139.5+1.21.11 |
| MaLiLib | 1.21.11-0.27.8 |
| Litematica | 1.21.11-0.26.3（Sakura Ryoko 版本） |

## 安裝

1. 安裝 [Fabric Loader](https://fabricmc.net/use/installer/) 及適用於 Minecraft 1.21.11 的 [Fabric API](https://modrinth.com/mod/fabric-api)。
2. 安裝適用於 1.21.11 的 [MaLiLib](https://github.com/sakura-ryoko/malilib) 與 [Litematica](https://github.com/sakura-ryoko/litematica)。
3. 將 `litematica-printer-3.4.0-mc1.21.11.jar` 與上述依賴放入 Minecraft 的 `mods` 資料夾。
4. 使用 Fabric 1.21.11 遊戲設定檔啟動遊戲。

目前建置成品位於 [`version/1.21.11`](version/1.21.11)。

## 使用方式

- 預設按 `Caps Lock` 開啟或關閉 Printer。
- 預設按住 `V` 可暫時啟用列印，不受開關狀態影響。
- 按 `M + C` 開啟 Litematica 設定，在 **Generic** 頁面底部調整 Printer 選項。
- Printer 選項名稱皆以 `printer` 開頭；快捷鍵可在 **Hotkeys** 頁面重新綁定。

建議先使用預設值測試。提高放置速度或距離可能導致伺服器拒絕封包、物品欄不同步或方塊方向錯誤。

## 1.21.11 更新內容

- 新增 Minecraft 1.21.11 模組與獨立建置流程。
- Yarn mappings 更新至 `1.21.11+build.4`。
- Fabric Loader 更新至 `0.18.1`。
- Fabric API 更新至 `0.139.5+1.21.11`。
- MaLiLib 更新至 `1.21.11-0.27.8`。
- Sakura Ryoko Litematica 更新至 `1.21.11-0.26.3`。
- 保留 Air Place、物品欄管理、Free Look、自動格式轉換及水浸方塊等原有功能。
- 新增可只載入 `v1_21_11` 子專案的 Gradle 選項，避免其他 Minecraft 版本的依賴影響建置。

## 修復內容

### 幽靈方塊與物品欄同步

舊版會攔截快捷欄內方塊物品的所有欄位更新，包括伺服器正常扣除已放置方塊數量的封包。這會讓客戶端保留錯誤數量，進而產生幽靈方塊或幽靈物品。

現在只會忽略物品種類、組件與數量完全相同的重複封包。伺服器傳回的實際數量修正會正常套用，讓客戶端物品欄保持同步。

### 蹲下放置封包

Minecraft 1.21.11 改變了玩家輸入同步方式。按下與放開 Shift 現在使用 `PlayerInputC2SPacket` 傳送，不再依賴舊版的 `ClientCommandC2SPacket`，修復需要蹲下互動時無法正確放置的問題。

### 列印視角與 Free Look

- 移除 1.21.11 已不存在的 `lastYaw`、`lastPitch` 與 `lastSneaking` 欄位存取。
- 恢復 Printer 放置動作需要的 yaw/pitch 封包覆寫。
- 改用公開的 `getInvertMouseY()` API，修復開啟反轉 Y 軸時 Free Look 崩潰的問題。

### Schematic 自動轉換

配合新版 Litematica 將建構函式參數由 `File` 改為 `Path`，更新 `.nbt` 自動轉換 `.litematic` 的流程。這也修復了載入結構時可能發生的 mixin 失敗與 Network Protocol Error。

### Minecraft 1.21.11 API 相容性

- 更新玩家位置、物品欄及滑鼠設定 API。
- 增加新版玩家物品欄所需的 accessor。
- 調整玩家佔用方塊判定與方塊距離排序。
- 恢復 Air Place 副手欄位抑制和高速放置時的幽靈物品修正。

## 建議設定

### Grim Rotation

適合支援相關放置方式的伺服器：

- `printerRange`: `4.5` 至 `5.0`
- `printerRotatePlayer`: `false`
- `printerGrimRotate`: `true`
- `printerAirPlace`: 視伺服器規則啟用

若發生回彈、封包拒絕或位置修正，請停用 `printerGrimRotate` 並改用下方的嚴格設定。

### 嚴格反作弊

此設定更接近原版玩家行為，速度較慢但相容性通常較高：

- 將 `printerFreeLookToggle` 綁定為與 Printer 開關相同的按鍵。
- `printerRange`: `4.3` 至 `4.5`
- `printerRotatePlayer`: `true`
- `printerGrimRotate`: `false`

### Map Art

可啟用 `printerIgnoreRotation`。這適用於多數方塊，但部分原木會因方向不同而在地圖上顯示不同顏色。

## 主要功能

- `printerAirPlace`: 在沒有相鄰支撐方塊時嘗試放置。
- `printerInventoryManagementMode`: 自動管理快捷欄並選取所需方塊。
- `printerFreeLook`: 列印時允許鏡頭與玩家朝向分離。
- `printerWaterlogging`: 對 schematic 中應含水的方塊自動使用水桶。
- `autoConvertSchematicToLitematicOnLoad`: 載入時將 Vanilla `.nbt` 結構自動轉換為 `.litematic`。
- `printerPlaceObserversLast`: 延後放置 Observer，降低提前觸發紅石的機會。
- `printerSuperChineseGhostItemFix`: 過濾高速放置時伺服器送出的重複欄位封包。

## 已知限制

- 不支援直接放置液體，但可以在液體內列印方塊。
- 軌道放置演算法可能無法完成所有位置，以避免放置錯誤方向的軌道。
- 高延遲、丟包或伺服器反作弊可能造成方塊方向錯誤、回彈或偶發錯放。
- Air Place 的可用性取決於伺服器；必要時提高 `printerTickDelay`。
- 本模組不會自動挖除錯誤方塊，也不會修復既有建築。
- 本分支主要針對 2b2t 類型環境調整，不保證相容所有伺服器或 ViaFabricPlus 組合。

## 建置

需要完整的 **JDK 21**，只有 JRE 無法編譯。

在 Windows 執行：

```powershell
$env:JAVA_HOME = "C:\path\to\jdk-21"
.\gradlew.bat --no-daemon -PtargetVersion=v1_21_11 :v1_21_11:build
```

建置後的 remapped JAR 位於：

```text
v1_21_11/build/libs/litematica-printer-3.4.0-mc1.21.11.jar
```

VS Code 也可執行 **Build Litematica Printer 1.21.11** task。建置成功後，執行 **Copy Litematica Printer 1.21.11** task 可將成品複製到 `version/1.21.11`。

## 授權

本儲存庫採用 [GNU Affero General Public License v3.0](LICENSE.md)。
