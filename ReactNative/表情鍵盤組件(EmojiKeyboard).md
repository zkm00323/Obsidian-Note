# React Native 表情鍵盤組件 (EmojiKeyboard)

## 安裝

首先需要安裝 `rn-emoji-keyboard` 包：

```bash
npm install rn-emoji-keyboard
# 或
yarn add rn-emoji-keyboard
```

## 基本用法

以下是一個使用 `EmojiKeyboard` 組件的基本示例：

```tsx
import { ThemedText } from '@/components/ThemedText';
import { useState } from 'react';
import { SafeAreaView, StyleSheet, TouchableOpacity, View } from 'react-native';
import EmojiKeyboard from 'rn-emoji-keyboard';

export default function TabTwoScreen() {
  // 狀態管理
  const [isOpen, setIsOpen] = useState(false);
  const [selectedEmoji, setSelectedEmoji] = useState('😊');

  // 表情選擇處理
  const onEmojiSelected = (emoji: { emoji: string }) => {
    setSelectedEmoji(emoji.emoji);
    setIsOpen(false);
  };

  return (
    <SafeAreaView style={styles.container}>
      <View style={styles.emojiWrapper}>
        {/* 表情按鈕 */}
        <TouchableOpacity 
          style={styles.emojiButton} 
          onPress={() => setIsOpen(true)}
        >
          <ThemedText style={styles.emojiText}>{selectedEmoji}</ThemedText>
        </TouchableOpacity>

        {/* 表情鍵盤組件 */}
        <EmojiKeyboard
          onEmojiSelected={onEmojiSelected}
          open={isOpen}
          onClose={() => setIsOpen(false)}
          allowMultipleSelections={false}
          theme={{
            backdrop: '#16161888',
            knob: '#766dfc',
            container: '#282829',
            header: '#fff',
            skinTonesContainer: '#252427',
            category: {
              icon: '#766dfc',
              iconActive: '#fff',
              container: '#252427',
              containerActive: '#766dfc',
            },
          }}
        />
      </View>
    </SafeAreaView>
  );
}
```

## 主要特點

1. **基本功能**：
   - 表情選擇
   - 表情預覽
   - 自定義主題
   - 多選控制

2. **主要屬性**：
   - `open`: 控制鍵盤顯示/隱藏
   - `onEmojiSelected`: 表情選擇回調
   - `onClose`: 關閉鍵盤回調
   - `allowMultipleSelections`: 是否允許多選
   - `theme`: 自定義主題配置

3. **主題配置**：
   ```tsx
   theme={{
     backdrop: '#16161888',        // 背景遮罩顏色
     knob: '#766dfc',             // 滑塊顏色
     container: '#282829',        // 容器背景色
     header: '#fff',              // 頭部文字顏色
     skinTonesContainer: '#252427', // 膚色選擇器背景色
     category: {
       icon: '#766dfc',           // 分類圖標顏色
       iconActive: '#fff',        // 激活狀態圖標顏色
       container: '#252427',      // 分類容器背景色
       containerActive: '#766dfc', // 激活狀態容器背景色
     },
   }}
   ```

## 樣式定義

```tsx
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#000',
  },
  emojiWrapper: {
    // 表情容器樣式
  },
  emojiButton: {
    width: 60,
    height: 60,
    backgroundColor: '#f0f0f0',
    borderRadius: 12,
    justifyContent: 'center',
    alignItems: 'center',
  },
  emojiText: {
    fontSize: 30,
    lineHeight: 40,
    textAlignVertical: 'center',
  },
});
```

## 使用建議

1. **狀態管理**：
   - 使用 `useState` 管理鍵盤顯示狀態
   - 使用 `useState` 管理選中的表情

2. **事件處理**：
   - 實現 `onEmojiSelected` 處理表情選擇
   - 實現 `onClose` 處理鍵盤關閉

3. **主題定制**：
   - 根據應用主題自定義顏色
   - 保持視覺一致性

4. **性能優化**：
   - 避免不必要的重渲染
   - 使用 `useCallback` 優化回調函數

## 注意事項

1. **兼容性**：
   - 確保與 React Native 版本兼容
   - 測試不同設備上的顯示效果

2. **性能考慮**：
   - 控制表情數量
   - 優化渲染性能

3. **用戶體驗**：
   - 提供適當的視覺反饋
   - 確保操作流暢

4. **錯誤處理**：
   - 處理表情加載失敗
   - 處理選擇錯誤

## 常見問題

1. **鍵盤不顯示**：
   - 檢查 `open` 狀態
   - 檢查組件引入

2. **主題不生效**：
   - 檢查主題配置
   - 確保顏色值正確

3. **選擇不響應**：
   - 檢查事件處理函數
   - 檢查狀態更新

4. **樣式問題**：
   - 檢查容器樣式
   - 檢查按鈕樣式 