```tsx title:/src/IResult
export interface IResult<T> {
  result: boolean;
  data?: T;
  errorInfo?: string;
}
```