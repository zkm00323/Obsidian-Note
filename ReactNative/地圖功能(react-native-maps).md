```cmd title:install-react-native-maps
npx expo install react-native-maps
```

```json hl:5-13,title:app.json
{
  "expo": {
    "plugins": [
      [
        "expo-location",
        {
          "locationAlwaysAndWhenInUsePermission": "允許 $(PRODUCT_NAME) 使用您的位置資訊。",
          "locationAlwaysPermission": "允許 $(PRODUCT_NAME) 在背景使用您的位置資訊。",
          "locationWhenInUsePermission": "允許 $(PRODUCT_NAME) 在使用時存取您的位置資訊。",
          "isIosBackgroundLocationEnabled": true,
          "isAndroidBackgroundLocationEnabled": true
        }
      ]
    ],
    "android": {
      "config": {
        "googleMaps": {
          "apiKey": "YOUR_ANDROID_API_KEY"
        }
      }
    },
    "ios": {
      "config": {
        "googleMapsApiKey": "YOUR_IOS_API_KEY"
      }
    }
  }
}
```

```tsx title:CustomMap.tsx
import React, { useRef } from 'react';
import { StyleSheet, View } from 'react-native';
import MapView, { PROVIDER_GOOGLE } from 'react-native-maps';

export default function CustomMap() {
  const mapRef = useRef<MapView>(null);

  return (
    <View style={styles.container}>
      <MapView
        ref={mapRef}
        style={styles.map}
        provider={PROVIDER_GOOGLE}
        initialRegion={{
          latitude: 25.0330,
          longitude: 121.5654,
          latitudeDelta: 0.0922,
          longitudeDelta: 0.0421,
        }}
        showsCompass={true}
        showsScale={true}
        showsBuildings={true}
        showsTraffic={true}
        showsUserLocation={true}
        showsMyLocationButton={true}
      >
      </MapView>
    </View>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
  },
  map: {
    flex: 1,
  }
});
```

## 使用說明

這是一個基於 Google Maps 的地圖組件實現，具有以下特點：

1. **基本配置**：
   - 需要安裝 `react-native-maps` 依賴
   - 需要配置 Google Maps API Key
   - 需要配置位置權限

2. **主要功能**：
   - 顯示地圖
   - 顯示用戶位置
   - 顯示指南針
   - 顯示比例尺
   - 顯示建築物
   - 顯示交通狀況
   - 顯示定位按鈕

3. **Props 說明**：
   ```typescript
   interface MapViewProps {
     provider?: 'google' | 'osmdroid';  // 地圖提供商
     initialRegion?: {                  // 初始顯示區域
       latitude: number;                // 緯度
       longitude: number;               // 經度
       latitudeDelta: number;           // 緯度範圍
       longitudeDelta: number;          // 經度範圍
     };
     showsCompass?: boolean;            // 顯示指南針
     showsScale?: boolean;              // 顯示比例尺
     showsBuildings?: boolean;          // 顯示建築物
     showsTraffic?: boolean;            // 顯示交通狀況
     showsUserLocation?: boolean;       // 顯示用戶位置
     showsMyLocationButton?: boolean;   // 顯示定位按鈕
   }
   ```

4. **常用方法**：
   ```typescript
   // 移動到指定位置
   mapRef.current?.animateToRegion({
     latitude: 25.0330,
     longitude: 121.5654,
     latitudeDelta: 0.0922,
     longitudeDelta: 0.0421,
   });

   // 移動到用戶位置
   mapRef.current?.animateToUserLocation();

   // 獲取當前地圖區域
   mapRef.current?.getCamera();
   ```

5. **標記點（Marker）使用**：
   ```tsx
   <MapView>
     <Marker
       coordinate={{
         latitude: 25.0330,
         longitude: 121.5654,
       }}
       title="標記標題"
       description="標記描述"
     />
   </MapView>
   ```

6. **多邊形（Polygon）使用**：
   ```tsx
   <MapView>
     <Polygon
       coordinates={[
         { latitude: 25.0330, longitude: 121.5654 },
         { latitude: 25.0331, longitude: 121.5655 },
         { latitude: 25.0332, longitude: 121.5656 },
       ]}
       strokeColor="#000"
       fillColor="rgba(0,0,0,0.5)"
     />
   </MapView>
   ```

7. **折線（Polyline）使用**：
   ```tsx
   <MapView>
     <Polyline
       coordinates={[
         { latitude: 25.0330, longitude: 121.5654 },
         { latitude: 25.0331, longitude: 121.5655 },
       ]}
       strokeColor="#000"
       strokeWidth={2}
     />
   </MapView>
   ```

8. **注意事項**：
   - 必須在 `app.json` 中配置 Google Maps API Key
   - iOS 和 Android 需要分別配置 API Key
   - 需要正確配置位置權限
   - 建議使用 `useRef` 來控制地圖實例
   - 注意處理地圖加載錯誤的情況

9. **性能優化**：
   - 使用 `useRef` 而不是 `useState` 來存儲地圖實例
   - 避免頻繁更新地圖區域
   - 合理設置 `latitudeDelta` 和 `longitudeDelta`
   - 使用 `react-native-maps` 的最新版本

## 放置圖釘功能

這是一個允許用戶在地圖上添加圖釘的功能實現，具有以下特點：

1. **基本功能**：
   - 切換添加圖釘模式
   - 顯示臨時圖釘指示器
   - 保存已添加的圖釘
   - 顯示圖釘標題和描述
   - 圖釘列表顯示
   - 刪除圖釘功能
   - 移動到指定圖釘位置

2. **代碼實現**：
   ```tsx
   import React, { useRef, useState } from 'react';
   import { ScrollView, StyleSheet, Text, TouchableOpacity, View } from 'react-native';
   import MapView, { Marker, PROVIDER_GOOGLE } from 'react-native-maps';

   interface Pin {
     id: string;
     coordinate: {
       latitude: number;
       longitude: number;
     };
     title: string;
     description: string;
   }

   export default function CustomMap() {
     const mapRef = useRef<MapView>(null);
     const [isAddingPin, setIsAddingPin] = useState(false);
     const [pins, setPins] = useState<Pin[]>([]);
     const [centerCoordinate, setCenterCoordinate] = useState<{
       latitude: number;
       longitude: number;
     } | null>(null);

     // 切換添加圖釘模式
     const toggleAddPin = () => {
       if (isAddingPin) {
         // 如果當前是添加模式，點擊確認按鈕時添加圖釘
         if (centerCoordinate) {
           const newPin: Pin = {
             id: Date.now().toString(),
             coordinate: centerCoordinate,
             title: '新位置',
             description: '點擊編輯描述',
           };
           setPins([...pins, newPin]);
         }
       } else {
         // 進入添加模式時，獲取當前地圖中心點
         mapRef.current?.getCamera().then(camera => {
           setCenterCoordinate({
             latitude: camera.center.latitude,
             longitude: camera.center.longitude,
           });
         });
       }
       setIsAddingPin(!isAddingPin);
     };

     // 處理地圖移動結束事件
     const handleRegionChangeComplete = (region: any) => {
       if (isAddingPin) {
         setCenterCoordinate({
           latitude: region.latitude,
           longitude: region.longitude,
         });
       }
     };

     // 刪除圖釘
     const removePin = (pinId: string) => {
       setPins(pins.filter(pin => pin.id !== pinId));
     };

     // 移動到指定圖釘位置
     const moveToPin = (pin: Pin) => {
       mapRef.current?.animateToRegion({
         latitude: pin.coordinate.latitude,
         longitude: pin.coordinate.longitude,
         latitudeDelta: 0.01,
         longitudeDelta: 0.01,
       }, 1000);
     };

     return (
       <View style={styles.container}>
         <View style={styles.mapContainer}>
           <MapView
             ref={mapRef}
             style={styles.map}
             provider={PROVIDER_GOOGLE}
             initialRegion={{
               latitude: 25.0330,
               longitude: 121.5654,
               latitudeDelta: 0.0922,
               longitudeDelta: 0.0421,
             }}
             showsCompass={true}
             showsScale={true}
             showsBuildings={true}
             showsTraffic={true}
             showsUserLocation={true}
             showsMyLocationButton={true}
             onRegionChangeComplete={handleRegionChangeComplete}
           >
             {/* 顯示所有已放置的圖釘 */}
             {pins.map((pin) => (
               <Marker
                 key={pin.id}
                 coordinate={pin.coordinate}
                 title={pin.title}
                 description={pin.description}
                 pinColor="red"
               />
             ))}

             {/* 顯示中心點標記（僅在添加模式時顯示） */}
             {isAddingPin && centerCoordinate && (
               <Marker
                 coordinate={centerCoordinate}
                 pinColor="red"
                 opacity={0.5}
               />
             )}
           </MapView>

           {/* 添加/確認按鈕 */}
           <TouchableOpacity 
             style={[styles.actionButton, isAddingPin && styles.activeButton]}
             onPress={toggleAddPin}
           >
             <Text style={styles.buttonText}>
               {isAddingPin ? '確認位置' : '加入圖釘'}
             </Text>
           </TouchableOpacity>

           {/* 半透明紅色臨時圖釘指示器 */}
           {isAddingPin && (
             <View style={styles.tempPinContainer}>
               <View style={styles.tempPin} />
             </View>
           )}
         </View>

         {/* 圖釘列表 */}
         <View style={styles.listContainer}>
           <Text style={styles.listTitle}>已添加的位置</Text>
           <ScrollView style={styles.list}>
             {pins.map((pin) => (
               <TouchableOpacity 
                 key={pin.id}
                 style={styles.listItem}
                 onPress={() => moveToPin(pin)}
               >
                 <View style={styles.listItemContent}>
                   <Text style={styles.listItemTitle}>{pin.title}</Text>
                   <Text style={styles.listItemDescription}>
                     經度: {pin.coordinate.longitude.toFixed(6)}
                     {'\n'}
                     緯度: {pin.coordinate.latitude.toFixed(6)}
                   </Text>
                 </View>
                 <TouchableOpacity 
                   style={styles.deleteButton}
                   onPress={() => removePin(pin.id)}
                 >
                   <Text style={styles.deleteButtonText}>刪除</Text>
                 </TouchableOpacity>
               </TouchableOpacity>
             ))}
           </ScrollView>
         </View>
       </View>
     );
   }
   ```

3. **樣式定義**：
   ```typescript
   const styles = StyleSheet.create({
     container: {
       flex: 1,
       backgroundColor: '#fff',
     },
     mapContainer: {
       flex: 1,
       position: 'relative',
     },
     map: {
       flex: 1,
     },
     actionButton: {
       position: 'absolute',
       top: 20,
       right: 20,
       backgroundColor: 'white',
       padding: 10,
       borderRadius: 8,
       elevation: 4,
       shadowColor: '#000',
       shadowOffset: {
         width: 0,
         height: 2,
       },
       shadowOpacity: 0.25,
       shadowRadius: 3.84,
     },
     activeButton: {
       backgroundColor: '#4CAF50',
     },
     buttonText: {
       fontSize: 16,
       fontWeight: 'bold',
     },
     tempPinContainer: {
       position: 'absolute',
       top: 0,
       left: 0,
       right: 0,
       bottom: 0,
       justifyContent: 'center',
       alignItems: 'center',
       pointerEvents: 'none',
     },
     tempPin: {
       width: 5,
       height: 5,
       backgroundColor: 'rgba(255, 0, 0, 0.5)',
       borderRadius: 15,
       borderWidth: 2,
       borderColor: 'rgba(255, 0, 0, 0.8)',
     },
     listContainer: {
       height: 200,
       backgroundColor: '#f5f5f5',
       borderTopWidth: 1,
       borderTopColor: '#ddd',
     },
     listTitle: {
       fontSize: 18,
       fontWeight: 'bold',
       padding: 10,
       backgroundColor: '#fff',
       borderBottomWidth: 1,
       borderBottomColor: '#ddd',
     },
     list: {
       flex: 1,
     },
     listItem: {
       flexDirection: 'row',
       padding: 10,
       borderBottomWidth: 1,
       borderBottomColor: '#ddd',
       backgroundColor: '#fff',
     },
     listItemContent: {
       flex: 1,
     },
     listItemTitle: {
       fontSize: 16,
       fontWeight: 'bold',
     },
     listItemDescription: {
       fontSize: 12,
       color: '#666',
       marginTop: 4,
     },
     deleteButton: {
       backgroundColor: '#ff4444',
       padding: 8,
       borderRadius: 4,
       justifyContent: 'center',
     },
     deleteButtonText: {
       color: '#fff',
       fontSize: 12,
     },
   });
   ```

4. **使用說明**：
   - 點擊"加入圖釘"按鈕進入添加模式
   - 移動地圖到想要添加圖釘的位置
   - 點擊"確認位置"按鈕添加圖釘
   - 圖釘會顯示在地圖上，可以點擊查看標題和描述
   - 在底部列表中可以看到所有已添加的圖釘
   - 點擊列表項可以移動到對應的圖釘位置
   - 點擊刪除按鈕可以移除圖釘

5. **注意事項**：
   - 使用 `useState` 管理圖釘列表和添加狀態
   - 使用 `useRef` 獲取地圖實例
   - 注意處理地圖移動事件
   - 考慮添加圖釘編輯功能
   - 可以添加圖釘刪除功能
   - 考慮保存圖釘數據到本地存儲
   - 注意處理列表滾動和地圖交互的衝突
   - 考慮添加圖釘搜索功能
   - 可以添加圖釘分類功能
   - 考慮添加圖釘分享功能 