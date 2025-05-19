```cmd title:install-async-storage
npx expo install @react-native-async-storage/async-storage
```
![[更好的error处理(IResult)]]

```tsx title:IStorage
export interface IStorage {
  set<T>(key: string, value: T): Promise<void>;
  get<T>(key: string): Promise<T | null>;
  remove(key: string): Promise<void>;
}
```

```tsx title:AsyncStorage
import AsyncStorage from '@react-native-async-storage/async-storage';
import { IStorage } from 'IStorage';

interface Result<T> {
  result: boolean;
  data?: T;
  errorInfo?: string;
}

export class AsyncStorageHelper implements IStorage {
  async set<T>(key: string, value: T): Promise<Result<null>> {
    try {
      const json = JSON.stringify(value);
      await AsyncStorage.setItem(key, json);
      return { result: true, data: null };
    } catch (e: any) {
      console.error(`儲存失敗: ${key}`, e);
      return { result: false, errorInfo: e?.message || '未知錯誤' };
    }
  }

  async get<T>(key: string): Promise<Result<T | null>> {
    try {
      const json = await AsyncStorage.getItem(key);
      const data = json != null ? (JSON.parse(json) as T) : null;
      return { result: true, data };
    } catch (e: any) {
      console.error(`讀取失敗: ${key}`, e);
      return { result: false, errorInfo: e?.message || '未知錯誤' };
    }
  }

  async remove(key: string): Promise<Result<null>> {
    try {
      await AsyncStorage.removeItem(key);
      return { result: true, data: null };
    } catch (e: any) {
      console.error(`刪除失敗: ${key}`, e);
      return { result: false, errorInfo: e?.message || '未知錯誤' };
    }
  }
}
```