```cmd title:install-expo-location
npx expo install expo-location
```

```json hl:5-13,title:app.json
{
	"expo": {
		"plugins": [
		...,
		[
			"expo-location",
			{
				"locationAlwaysAndWhenInUsePermission": "允許 $(PRODUCT_NAME) 使用您的位置資訊。",
				"locationAlwaysPermission": "允許 $(PRODUCT_NAME) 在背景使用您的位置資訊。",
				"locationWhenInUsePermission": "允許 $(PRODUCT_NAME) 在使用時存取您的位置資訊。",
				"isIosBackgroundLocationEnabled": true,
				"isAndroidBackgroundLocationEnabled": true
			}
		],
		...
	}
}
```

```tsx title:locationHelper
import * as Location from 'expo-location';
import { IResult } from './IResult';

// 位置追蹤配置介面
export interface LocationTrackingOptions {
	accuracy?: Location.Accuracy;
	timeInterval?: number;
	distanceInterval?: number;
}

// 預設配置
const DEFAULT_OPTIONS: LocationTrackingOptions = {
	accuracy: Location.Accuracy.Balanced,
	timeInterval: 5000, // 改為 5 秒更新一次
	distanceInterval: 5 // 改為 5 米更新一次
};

// 檢查位置權限
const checkLocationPermission = async (isBackground: boolean = false): Promise<IResult<Location.PermissionStatus>> => {
	try {
		const { status } = isBackground
			? await Location.requestBackgroundPermissionsAsync()
			: await Location.requestForegroundPermissionsAsync();
		return {
			result: status === 'granted',
			data: status,
			errorInfo: status !== 'granted' ? '未獲得位置權限' : undefined
		};
	} catch (error) {
		return {
			result: false,
			errorInfo: '檢查權限時發生錯誤'
		};
	}
};

// 獲取當前位置（單次）
export const getCurrentLocation = async (): Promise<IResult<Location.LocationObject>> => {
	const permissionResult = await checkLocationPermission();
	if (!permissionResult.result) {
		return {
			result: false,
			errorInfo: permissionResult.errorInfo
		};
	}

	try {
		const location = await Location.getCurrentPositionAsync({
			accuracy: DEFAULT_OPTIONS.accuracy,
		});
		return {
			result: true,
			data: location
		};
	} catch (error) {
		return {
			result: false,
			errorInfo: '獲取位置時發生錯誤'
		};
	}
};

// 獲取最後已知位置
export const getLastKnownLocation = async (maxAge: number = 10000): Promise<IResult<Location.LocationObject>> => {
	const permissionResult = await checkLocationPermission();
	if (!permissionResult.result) {
		return {
			result: false,
			errorInfo: permissionResult.errorInfo
		};
	}

	try {
		const location = await Location.getLastKnownPositionAsync({
			maxAge,
		});
		if (!location) {
			return {
				result: false,
				errorInfo: '無法獲取最後已知位置'
			};
		}

		return {
			result: true,
			data: location
		};
	} catch (error) {
		return {
			result: false,
			errorInfo: '獲取最後已知位置時發生錯誤'
		};
	}
};

// 獲取位置權限狀態
export const getLocationPermissionStatus = async (): Promise<IResult<Location.LocationPermissionResponse>> => {
	try {
		const permissionStatus = await Location.getForegroundPermissionsAsync();
		return {
			result: true,
			data: permissionStatus
		};
	} catch (error) {
		return {
			result: false,
			errorInfo: '獲取權限狀態時發生錯誤'
		};
	}
};

// 計算兩點之間的距離（米）
export const calculateDistance = (
	lat1: number,
	lon1: number,
	lat2: number,
	lon2: number
): IResult<number> => {
	try {
		const R = 6371e3; // 地球半徑（米）
		const φ1 = (lat1 * Math.PI) / 180;
		const φ2 = (lat2 * Math.PI) / 180;
		const Δφ = ((lat2 - lat1) * Math.PI) / 180;
		const Δλ = ((lon2 - lon1) * Math.PI) / 180;

		const a =
			Math.sin(Δφ / 2) * Math.sin(Δφ / 2) +
			Math.cos(φ1) * Math.cos(φ2) * Math.sin(Δλ / 2) * Math.sin(Δλ / 2);
		const c = 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));

		return {
			result: true,
			data: R * c
		};
	} catch (error) {
		return {
			result: false,
			errorInfo: '計算距離時發生錯誤'
		};
	}
};

export const startLocationTracking = async (
	setLocation: (location: Location.LocationObject) => void,
	setIsTracking: (isTracking: boolean) => void,
	setSubscription: (subscription: Location.LocationSubscription) => void,
	onLocationUpdate?: (location: Location.LocationObject) => void,
	options: LocationTrackingOptions = {}
): Promise<IResult<void>> => {
	const permissionResult = await checkLocationPermission(true);
	if (!permissionResult.result) {
		return {
			result: false,
			errorInfo: permissionResult.errorInfo
		};
	}

	try {
		const trackingOptions = {
			...DEFAULT_OPTIONS,
			...options
		};

		const sub = await Location.watchPositionAsync(
			{
				accuracy: trackingOptions.accuracy,
				timeInterval: trackingOptions.timeInterval,
				distanceInterval: trackingOptions.distanceInterval
			},
			(location) => {
				setLocation(location);
				if (onLocationUpdate) {
					onLocationUpdate(location);
				}
			}
		);

		setSubscription(sub);
		setIsTracking(true);
		return {
			result: true
		};
	} catch (error) {
		return {
			result: false,
			errorInfo: '啟動位置追蹤時發生錯誤'
		};
	}
};

export const stopLocationTracking = (
	subscription: Location.LocationSubscription | null,
	setSubscription: (subscription: Location.LocationSubscription | null) => void,
	setIsTracking: (isTracking: boolean) => void
): IResult<void> => {
	try {
		if (subscription) {
			subscription.remove();
			setSubscription(null);
		}
		setIsTracking(false);
		return {
			result: true
		};
	} catch (error) {
		return {
			result: false,
			errorInfo: '停止位置追蹤時發生錯誤'
		};
	}
};
```