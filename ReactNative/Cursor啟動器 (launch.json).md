# 🚀 Expo + VS Code Launch 配置快速部署指南

## 📋 AI 實施步驟

### STEP 1: 檢查 package.json 腳本
確保專案中包含以下腳本：
```json
"scripts": {
  "start": "expo start",
  "android": "expo run:android",
  "ios": "expo run:ios",
  "restart-ios": "(killall Simulator || true) && expo start --ios",
  "restart-android": "(killall adb || true) && expo start --android"
}
```

### STEP 2: 創建 .vscode/launch.json
在專案根目錄創建 `.vscode/launch.json` 文件：
```json
{
    "version": "0.2.0",
    "configurations": [
        {
            "name": "🍎 iOS 重啟",
            "type": "node",
            "request": "launch",
            "cwd": "${workspaceRoot}",
            "runtimeExecutable": "npm",
            "runtimeArgs": ["run", "restart-ios"],
            "console": "integratedTerminal"
        },
        {
            "name": "🤖 Android 重啟",
            "type": "node",
            "request": "launch",
            "cwd": "${workspaceRoot}",
            "runtimeExecutable": "npm",
            "runtimeArgs": ["run", "restart-android"],
            "console": "integratedTerminal"
        },
        {
            "name": "▶️ iOS 運行",
            "type": "node",
            "request": "launch",
            "cwd": "${workspaceRoot}",
            "runtimeExecutable": "npm",
            "runtimeArgs": ["run", "ios"],
            "console": "integratedTerminal"
        },
        {
            "name": "▶️ Android 運行",
            "type": "node",
            "request": "launch",
            "cwd": "${workspaceRoot}",
            "runtimeExecutable": "npm",
            "runtimeArgs": ["run", "android"],
            "console": "integratedTerminal"
        }
    ]
}
```

### STEP 3: 使用方式
- 按 `F5` 或 `Cmd+Shift+D` 開啟調試面板
- 選擇對應配置並執行

## 🎯 配置模式選擇

| 配置名稱 | 使用時機 | 實際命令 |
|---------|---------|----------|
| iOS/Android 重啟 | 模擬器卡住、需要清除狀態 | `killall + expo start` |
| iOS/Android 運行 | 正常開發、快速啟動 | `expo run:platform` |

## 🔧 可選擴展配置

```json
{
    "name": "🌐 Web 開發",
    "type": "node",
    "request": "launch",
    "cwd": "${workspaceRoot}",
    "runtimeExecutable": "npm",
    "runtimeArgs": ["run", "web"],
    "console": "integratedTerminal"
}
```

## ⚡ 快速故障排除

**問題**: 模擬器無響應
**解決**: 使用「重啟」配置而不是「運行」配置

**問題**: 端口被佔用  
**解決**: 手動執行 `killall node` 或重啟終端 