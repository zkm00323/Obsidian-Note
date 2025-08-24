**📁 VS Code 調試配置檔案**

```json title:/.vscode/launch.json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "start iOS",
            "type": "node",
            "request": "launch",
            "cwd": "${workspaceRoot}",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run",
                "restart-ios"
            ],
            "console": "integratedTerminal"
        },
        {
            "name": "start Android",
            "type": "node",
            "request": "launch",
            "cwd": "${workspaceRoot}",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run",
                "restart-android"
            ],
            "console": "integratedTerminal"
        },
        {
            "name": "run iOS",
            "type": "node",
            "request": "launch",
            "cwd": "${workspaceRoot}",
            "runtimeExecutable": "npx",
            "runtimeArgs": [
                "expo",
                "run:ios"
            ],
            "console": "integratedTerminal"
        },
        {
            "name": "run Android",
            "type": "node",
            "request": "launch",
            "cwd": "${workspaceRoot}",
            "runtimeExecutable": "npx",
            "runtimeArgs": [
                "expo",
                "run:android"
            ],
            "console": "integratedTerminal"
        }
    ]
}
```

**🎯 配置說明**

**基本結構**
- `version`: 配置檔案版本，目前為 `0.2.0`
- `configurations`: 調試配置陣列，可包含多個不同的啟動配置

**配置參數詳解**
- `name`: 配置名稱，會顯示在 VS Code 調試面板中
- `type`: 調試器類型，這裡使用 `node` 來執行 Node.js 命令
- `request`: 請求類型，`launch` 表示啟動新的程序
- `cwd`: 工作目錄，`${workspaceRoot}` 指向專案根目錄
- `runtimeExecutable`: 執行的程序，可以是 `npm` 或 `npx`
- `runtimeArgs`: 傳遞給執行程序的參數陣列
- `console`: 控制台輸出位置，`integratedTerminal` 使用 VS Code 整合終端

**📱 四種啟動模式**

**1. start iOS (重啟模式)**
```bash
npm run restart-ios
```
- 清除快取並重新啟動 iOS 模擬器
- 適用於需要完全重新載入的情況

**2. start Android (重啟模式)**
```bash
npm run restart-android
```
- 清除快取並重新啟動 Android 模擬器
- 適用於需要完全重新載入的情況

**3. run iOS (直接執行)**
```bash
npx expo run:ios
```
- 直接在 iOS 模擬器中運行應用
- 使用 Expo CLI 的原生執行命令

**4. run Android (直接執行)**
```bash
npx expo run:android
```
- 直接在 Android 模擬器中運行應用
- 使用 Expo CLI 的原生執行命令

**🚀 使用方式**

1. **透過 VS Code 調試面板**
   - 按 `Ctrl+Shift+D` (Windows/Linux) 或 `Cmd+Shift+D` (Mac) 開啟調試面板
   - 在上方下拉選單中選擇對應的配置
   - 點擊綠色播放按鈕執行

2. **透過命令面板**
   - 按 `Ctrl+Shift+P` (Windows/Linux) 或 `Cmd+Shift+P` (Mac)
   - 輸入 "Debug: Start Debugging"
   - 選擇要執行的配置

**💡 實用技巧**

**自定義配置範例**
```json
{
    "name": "Metro Bundler",
    "type": "node",
    "request": "launch",
    "cwd": "${workspaceRoot}",
    "runtimeExecutable": "npx",
    "runtimeArgs": [
        "expo",
        "start",
        "--clear"
    ],
    "console": "integratedTerminal"
}
```

**環境變數設定**
```json
{
    "name": "start iOS (Development)",
    "type": "node",
    "request": "launch",
    "cwd": "${workspaceRoot}",
    "runtimeExecutable": "npm",
    "runtimeArgs": ["run", "restart-ios"],
    "console": "integratedTerminal",
    "env": {
        "NODE_ENV": "development",
        "EXPO_USE_FAST_REFRESH": "true"
    }
}
```

**✅ 好處與優勢**

- **一鍵啟動**: 無需記憶複雜的命令行指令
- **環境切換**: 可輕鬆在不同平台間切換調試
- **整合體驗**: 直接在 VS Code 中管理所有開發流程
- **可視化操作**: 透過圖形界面操作，提高開發效率
- **團隊協作**: 配置檔案可與團隊成員共享 