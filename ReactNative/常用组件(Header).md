```tsx title:/compoments/Header.tsx
import React from 'react';
import { StyleSheet, TouchableOpacity, View } from 'react-native';
import { ThemedText } from './ThemedText';

interface HeaderProps {
  title: string;
  leftButton?: {
    text?: string;
    color?: string;
    onPress: () => void;
  };
  rightButton?: {
    text?: string;
    color?: string;
    onPress: () => void;
  };
  backgroundColor?: string;
  titleColor?: string;
}

const Header = ({
  title,
  leftButton,
  rightButton,
  backgroundColor = '#f8f8f8',
  titleColor = '#000',
}: HeaderProps) => {
  return (
    <View style={[styles.container, { backgroundColor }]}>
      <View style={styles.leftContainer}>
        {leftButton && (
          <TouchableOpacity style={styles.button} onPress={leftButton.onPress}>
            <ThemedText style={[styles.buttonText, { color: leftButton.color }]}>
              {leftButton.text || '取消'}
            </ThemedText>
          </TouchableOpacity>
        )}
      </View>

      <ThemedText style={[styles.title, { color: titleColor }]} numberOfLines={1}>
        {title}
      </ThemedText>

      <View style={styles.rightContainer}>
        {rightButton && (
          <TouchableOpacity style={styles.button} onPress={rightButton.onPress}>
            <ThemedText style={[styles.buttonText, { color: rightButton.color }]}>
              {rightButton.text || '完成'}
            </ThemedText>
          </TouchableOpacity>
        )}
      </View>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    height: 44,
    flexDirection: 'row',
    alignItems: 'center',
    justifyContent: 'space-between',
    paddingHorizontal: 16,
    backgroundColor: '#f8f8f8',
    borderBottomWidth: StyleSheet.hairlineWidth,
    borderBottomColor: '#e0e0e0',
  },
  leftContainer: {
    width: 80,
    alignItems: 'flex-start',
  },
  rightContainer: {
    width: 80,
    alignItems: 'flex-end',
  },
  title: {
    fontSize: 17,
    fontWeight: '600',
    textAlign: 'center',
    flex: 1,
  },
  button: {
    padding: 8,
  },
  buttonText: {
    fontSize: 16,
    fontWeight: '500',
  },
});

export default Header;
```

## 使用說明

這是一個通用的頁面頂部導航欄組件，具有以下特點：

1. **基本功能**：
   - 顯示標題
   - 可選的左側按鈕
   - 可選的右側按鈕
   - 自定義背景色和文字顏色

2. **Props 說明**：
   ```typescript
   interface HeaderProps {
     title: string;                // 標題文字
     leftButton?: {               // 左側按鈕（可選）
       text?: string;             // 按鈕文字，默認為"取消"
       color?: string;            // 按鈕顏色
       onPress: () => void;       // 點擊事件
     };
     rightButton?: {              // 右側按鈕（可選）
       text?: string;             // 按鈕文字，默認為"完成"
       color?: string;            // 按鈕顏色
       onPress: () => void;       // 點擊事件
     };
     backgroundColor?: string;     // 背景顏色，默認為 '#f8f8f8'
     titleColor?: string;         // 標題顏色，默認為 '#000'
   }
   ```

3. **使用示例**：
   ```tsx
   <Header
     title="設置"
     leftButton={{
       text: "返回",
       color: "#007AFF",
       onPress: () => navigation.goBack()
     }}
     rightButton={{
       text: "保存",
       color: "#007AFF",
       onPress: () => handleSave()
     }}
     backgroundColor="#FFFFFF"
     titleColor="#000000"
   />
   ```

4. **樣式特點**：
   - 固定高度 44px
   - 底部有細線分隔
   - 左右按鈕區域寬度固定為 80px
   - 標題居中顯示，超出部分自動省略
   - 按鈕有 8px 的內邊距，方便點擊

5. **注意事項**：
   - 需要配合 `ThemedText` 組件使用
   - 按鈕文字默認為"取消"和"完成"
   - 標題文字超出時會自動省略
   - 按鈕區域即使沒有按鈕也會佔據空間，保持佈局對稱 