# React Native 列表組件 (FlatList)

## 基本用法

以下是一個使用 `FlatList` 組件的基本示例：

```tsx
import { FlatList, SafeAreaView, StyleSheet } from 'react-native';
import { ThemedText } from '@/components/ThemedText';
import { ThemedView } from '@/components/ThemedView';

// 列表數據
const listData = [
  {
    id: '1',
    title: '歡迎使用',
    content: '這是一個垂直列表示例',
  },
  {
    id: '2',
    title: '功能一',
    content: '這是第一個功能項目的描述',
  },
  {
    id: '3',
    title: '功能二',
    content: '這是第二個功能項目的描述',
  },
  {
    id: '4',
    title: '功能三',
    content: '這是第三個功能項目的描述',
  },
];

// 列表項組件
interface ListItemProps {
  item: {
    id: string;
    title: string;
    content: string;
  };
}

const ListItem = ({ item }: ListItemProps) => (
  <ThemedView style={styles.listItem}>
    <ThemedText type="subtitle">{item.title}</ThemedText>
    <ThemedText>{item.content}</ThemedText>
  </ThemedView>
);

// 主屏幕組件
export default function HomeScreen() {
  return (
    <SafeAreaView style={styles.container}>
      <FlatList
        data={listData}
        renderItem={({ item }) => <ListItem item={item} />}
        keyExtractor={item => item.id}
        style={styles.list}
        scrollEnabled={true}
        contentContainerStyle={styles.listContent}
      />
    </SafeAreaView>
  );
}

// 樣式定義
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#000',
  },
  listContent: {
    padding: 16,
  },
  titleContainer: {
    flexDirection: 'row',
    alignItems: 'center',
    gap: 8,
    marginBottom: 16,
  },
  list: {
    flex: 1,
  },
  listItem: {
    padding: 16,
    marginBottom: 8,
    borderRadius: 8,
    backgroundColor: 'rgba(255, 255, 255, 0.1)',
  },
  reactLogo: {
    height: 178,
    width: 290,
    bottom: 0,
    left: 0,
    position: 'absolute',
  },
  separator: {
    height: 1,
    backgroundColor: 'rgba(255, 255, 255, 0.1)',
    marginVertical: 8,
  },
  emptyContainer: {
    padding: 20,
    alignItems: 'center',
  },
});

## 主要特點

1. **性能優化**：
   - 只渲染可見區域的項目
   - 自動處理項目回收和重用
   - 支持大量數據的高效渲染

2. **基本屬性**：
   - `data`: 列表數據源
   - `renderItem`: 渲染每個項目的函數
   - `keyExtractor`: 為每個項目生成唯一 key
   - `scrollEnabled`: 是否允許滾動
   - `contentContainerStyle`: 內容容器樣式

3. **常用功能**：
   - 下拉刷新
   - 上拉加載更多
   - 項目分隔線
   - 空列表顯示
   - 列表頭部和尾部

## 進階用法

### 1. 添加下拉刷新
```tsx
const [refreshing, setRefreshing] = useState(false);

const onRefresh = useCallback(() => {
  setRefreshing(true);
  // 執行刷新邏輯
  setTimeout(() => {
    setRefreshing(false);
  }, 2000);
}, []);

<FlatList
  refreshing={refreshing}
  onRefresh={onRefresh}
  // ... 其他屬性
/>
```

### 2. 添加分隔線
```tsx
const ItemSeparator = () => (
  <View style={styles.separator} />
);

<FlatList
  ItemSeparatorComponent={ItemSeparator}
  // ... 其他屬性
/>
```

### 3. 空列表顯示
```tsx
const EmptyList = () => (
  <View style={styles.emptyContainer}>
    <Text>沒有數據</Text>
  </View>
);

<FlatList
  ListEmptyComponent={EmptyList}
  // ... 其他屬性
/>
```

### 4. 列表頭部和尾部
```tsx
const ListHeader = () => (
  <View style={styles.header}>
    <Text>列表頭部</Text>
  </View>
);

const ListFooter = () => (
  <View style={styles.footer}>
    <Text>列表尾部</Text>
  </View>
);

<FlatList
  ListHeaderComponent={ListHeader}
  ListFooterComponent={ListFooter}
  // ... 其他屬性
/>
```

## 進階示例：帶動畫效果的雙列表交互

以下是一個展示帶有動畫效果的雙列表交互示例：

```tsx
import { useState } from 'react';
import { SafeAreaView, StyleSheet, TouchableOpacity } from 'react-native';
import Animated, {
  Easing,
  FadeIn,
  Layout,
  SequencedTransition,
  ZoomOut,
} from 'react-native-reanimated';

// 定義數據類型
interface ListItemData {
  id: string;
  title: string;
  content: string;
}

// 定義列表項組件屬性
interface ListItemProps {
  item: ListItemData;
  onPress: () => void;  // 新增：點擊事件處理
}

// 初始數據
const listData: ListItemData[] = [
  {
    id: '1',
    title: '歡迎使用aaa',
    content: '這是一個垂直列表示例',
  },
  // ... 其他數據
];

// 重點修改1：創建動畫組件
const AnimatedTouchable = Animated.createAnimatedComponent(TouchableOpacity);

// 重點修改2：添加動畫效果到列表項
const ListItem = ({ item, onPress }: ListItemProps) => (
  <AnimatedTouchable 
    style={styles.listItem} 
    onPress={onPress}
    entering={FadeIn.duration(300)}  // 新增：進入動畫
    exiting={ZoomOut.duration(300).easing(Easing.bezier(0.4, 0, 0.2, 1))}  // 新增：退出動畫
    layout={Layout.springify()}  // 新增：佈局動畫
  >
    <ThemedText type="subtitle">{item.title}</ThemedText>
    <ThemedText>{item.content}</ThemedText>
  </AnimatedTouchable>
);

// 主屏幕組件
export default function HomeScreen() {
  // 新增：兩個列表的狀態管理
  const [firstData, setFirstData] = useState<ListItemData[]>(listData);
  const [secondData, setSecondData] = useState<ListItemData[]>([]);

  // 新增：處理項目從第一個列表移動到第二個列表
  const handleItemPress = (item: ListItemData) => {
    setFirstData(prevData => prevData.filter(data => data.id !== item.id));
    setSecondData(prevData => [...prevData, item]);
  };

  // 新增：處理項目從第二個列表移動到第一個列表
  const handleItemPress2 = (item: ListItemData) => {
    setSecondData(prevData => prevData.filter(data => data.id !== item.id));
    setFirstData(prevData => [...prevData, item]);
  };

  return (
    <SafeAreaView style={styles.container}>
      {/* 重點修改3：使用 Animated.FlatList */}
      <Animated.FlatList
        data={firstData}
        renderItem={({ item }) => (
          <ListItem 
            item={item} 
            onPress={() => handleItemPress(item)}
          />
        )}
        keyExtractor={item => item.id}
        style={styles.list}
        scrollEnabled={true}
        contentContainerStyle={styles.listContent}
        itemLayoutAnimation={SequencedTransition.duration(300)}  // 新增：列表項動畫
      />
      <Animated.FlatList
        data={secondData}
        renderItem={({ item }) => (
          <ListItem 
            item={item} 
            onPress={() => handleItemPress2(item)}
          />
        )}
        keyExtractor={item => item.id}
        style={styles.list}
        contentContainerStyle={styles.listContent}
        itemLayoutAnimation={SequencedTransition.duration(300)}  // 新增：列表項動畫
      />
    </SafeAreaView>
  );
}

// 樣式定義
const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#000',
  },
  listContent: {
    padding: 16,
  },
  titleContainer: {
    flexDirection: 'row',
    alignItems: 'center',
    gap: 8,
    marginBottom: 16,
  },
  list: {
    flex: 1,
  },
  listItem: {
    padding: 16,
    marginBottom: 8,
    borderRadius: 8,
    backgroundColor: 'rgba(255, 255, 255, 0.1)',
  },
  reactLogo: {
    height: 178,
    width: 290,
    bottom: 0,
    left: 0,
    position: 'absolute',
  },
  separator: {
    height: 1,
    backgroundColor: 'rgba(255, 255, 255, 0.1)',
    marginVertical: 8,
  },
  emptyContainer: {
    padding: 20,
    alignItems: 'center',
  },
});

## 性能優化建議

1. **使用 `keyExtractor`**：
   - 確保每個項目有唯一的 key
   - 避免使用索引作為 key

2. **優化 `renderItem`**：
   - 使用 `React.memo` 包裝列表項組件
   - 避免在 `renderItem` 中創建新函數

3. **使用 `getItemLayout`**：
   - 當項目高度固定時使用
   - 可以提高滾動性能

4. **控制重渲染**：
   - 使用 `useCallback` 包裝回調函數
   - 使用 `useMemo` 緩存計算結果

## 注意事項

1. **數據更新**：
   - 確保數據源是新的引用
   - 使用不可變數據更新方式

2. **樣式處理**：
   - 注意內容容器的樣式
   - 處理不同屏幕尺寸的適配

3. **錯誤處理**：
   - 處理空數據情況
   - 處理加載錯誤情況

4. **性能監控**：
   - 監控列表滾動性能
   - 監控內存使用情況

## 常見問題

1. **列表不滾動**：
   - 檢查 `scrollEnabled` 屬性
   - 檢查容器樣式設置

2. **性能問題**：
   - 檢查 `renderItem` 實現
   - 檢查數據更新方式

3. **樣式問題**：
   - 檢查容器樣式
   - 檢查內容容器樣式

4. **數據更新問題**：
   - 確保數據源更新
   - 檢查 key 的設置 