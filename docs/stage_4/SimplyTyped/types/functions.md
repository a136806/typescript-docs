## 功能说明
> 函数类型
```ts
export type ConstructorFunction<T extends object> =
    new (...args:any[]) => T;

export type Predicate<A = any> = (arg: A) => boolean

export type AnyFunc(R = any) = (...args: any[]) => R;

export type OverwriteReturn<F extends Function R> = 
    F extends ((...x: infer T) => unknown) 
        ?  ((...x: T) => R)
        : never;
        
export type ArgsAsTuple<F extends Function> = 
    F extends((...x: infer T) => unknown) 
        ? T
        : never;
```