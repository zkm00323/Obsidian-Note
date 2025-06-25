**📁 檔案結構**
```tsx title:/helpers/UserHelper.ts'
export class UserHelper {
  private username: string;

  constructor() {
    this.username = "TestUser";
  }

  public getUsername(): string {
    return this.username;
  }

  public setUsername(name: string) {
    this.username = name;
  }
}
```
```tsx title:/helpers/MathHelper.ts

export class MathHelper {
  public add(a: number, b: number): number {
    return a + b;
  }

  public subtract(a: number, b: number): number {
    return a - b;
  }

  public getDescription(): string {
    return "由 MathHelper 計算";
  }
}
```
```tsx title:/AppContext.tsx
// i.tsx

import { createContext, useContext } from 'react';
import { MathHelper } from './helpers/MathHelper';
import { UserHelper } from './helpers/UserHelper';

export interface App {
  mathHelper: MathHelper;
  userHelper: UserHelper;
}

const mathHelper = new MathHelper();
const userHelper = new UserHelper();

export const AppContext = createContext<App>({
  mathHelper,
  userHelper,
});

export const AppProvider = ({ children }: { children: React.ReactNode }) => {
  return (
    <AppContext.Provider value={{ mathHelper, userHelper }}>
      {children}
    </AppContext.Provider>
  );
};

export const useApp = () => useContext(AppContext);
```
``` tsx title:/App.tsx

import { AppProvider, useApp } from './AppContext';
import { View, Text } from 'react-native'; // 可改為 React Web 也適用

function MainComponent() {
  const { mathHelper, userHelper } = useApp();

  const sum = mathHelper.add(1, 1);
  const username = userHelper.getUsername();

  return (
    <View style={{ padding: 20 }}>
      <Text>1 + 1 = {sum}</Text>
      <Text>{mathHelper.getDescription()}</Text>
      <Text>用戶名稱是：{username}</Text>
    </View>
  );
}

export default function App() {
  return (
    <AppProvider>
      <MainComponent />
    </AppProvider>
  );
}
```

**✅ 畫面結果**
```
1 + 1 = 2
由 MathHelper 計算
用戶名稱是：TestUser
```

