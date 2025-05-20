```tsx title:BottomSheet.tsx
import React from 'react'
import { Modal, StyleSheet, TouchableWithoutFeedback, View } from 'react-native'
import Animated, {
    FadeIn,
    FadeOut,
    SlideInDown,
    SlideOutDown
} from 'react-native-reanimated'

interface BottomSheetProps {
  isVisible: boolean
  setIsVisible: (isVisible: boolean) => void
  children: React.ReactNode
  height?: number
}

const BottomSheet: React.FC<BottomSheetProps> = ({ 
  isVisible, 
  setIsVisible, 
  children,
  height = 300 
}) => {
  if (!isVisible) return null

  return (
    <Modal
      visible={isVisible}
      transparent
      animationType="none"
      onRequestClose={() => setIsVisible(false)}
    >
      <Animated.View 
        entering={FadeIn}
        exiting={FadeOut}
        style={styles.overlay}
      >
        <TouchableWithoutFeedback onPress={() => setIsVisible(false)}>
          <View style={styles.overlay} />
        </TouchableWithoutFeedback>
        
        <Animated.View 
          entering={SlideInDown}
          exiting={SlideOutDown}
          style={[styles.bottomSheet, { height }]}
        >
          {children}
        </Animated.View>
      </Animated.View>
    </Modal>
  )
}

const styles = StyleSheet.create({
  overlay: {
    flex: 1,
    backgroundColor: 'rgba(0, 0, 0, 0.5)',
  },
  bottomSheet: {
    position: 'absolute',
    bottom: 0,
    left: 0,
    right: 0,
    backgroundColor: 'white',
    borderTopLeftRadius: 12,
    borderTopRightRadius: 12,
  }
})

export default BottomSheet
```

## 使用說明

這是一個底部彈出式面板組件，具有以下特點：

1. **基本功能**：
   - 從底部滑出的動畫效果
   - 半透明背景遮罩
   - 點擊背景可關閉
   - 可自定義高度
   - 圓角設計

2. **Props 說明**：
   ```typescript
   interface BottomSheetProps {
     isVisible: boolean;           // 控制顯示/隱藏
     setIsVisible: (isVisible: boolean) => void;  // 設置顯示狀態的函數
     children: React.ReactNode;    // 子組件內容
     height?: number;              // 面板高度，默認為 300
   }
   ```

3. **使用示例**：
   ```tsx
   const [isVisible, setIsVisible] = useState(false);

   return (
     <BottomSheet
       isVisible={isVisible}
       setIsVisible={setIsVisible}
       height={400}
     >
       <View style={{ padding: 16 }}>
         <Text>底部面板內容</Text>
       </View>
     </BottomSheet>
   );
   ```

4. **動畫效果**：
   - 使用 `react-native-reanimated` 實現流暢的動畫
   - 背景遮罩使用淡入淡出效果（FadeIn/FadeOut）
   - 面板使用滑入滑出效果（SlideInDown/SlideOutDown）

5. **樣式特點**：
   - 半透明黑色背景（rgba(0, 0, 0, 0.5)）
   - 白色面板背景
   - 頂部圓角（12px）
   - 固定寬度（左右貼邊）
   - 可自定義高度

6. **注意事項**：
   - 需要安裝 `react-native-reanimated` 依賴
   - 確保在 `babel.config.js` 中正確配置 reanimated 插件
   - 點擊背景遮罩會自動關閉面板
   - 可以通過 `onRequestClose` 處理 Android 返回按鈕事件

7. **安裝依賴**：
   ```bash
   npx expo install react-native-reanimated
   ```

8. **babel.config.js 配置**：
   ```javascript
   module.exports = {
     plugins: [
       'react-native-reanimated/plugin',
     ],
   };
   ``` 