## 功能说明
> object类型辅助

```ts
export type PlainObject = 
    Record<string,any>;

/* 
K in keyof T 返回T的索引类型Union "stringLiteral" | "numberLiteral" | "symbol1"| "symbol2" | ...
T[K]         获取属性的类型组合 "v1type" | "v2type" | ... 
 */
export type ObjectType<T> = {
    [k in keyof T] : T[K]
}
/* 
& 交叉类型 Record类型的+
*/
export type CombinObjects<T extends object, U extends object>
    = ObjectType<T & U>;

export type ObjectKeys = keyof any;
export type StringKeys = Exclude<keyof T,number | symbol>;
export type SharedKeys<T,U> = keyof T & keyof U;
export type AllKeys<T,U> = keyof T | keyof U;
export type DiffKeys<T,U> = Exclude<keyof T,keyof U>;
export type HasKey<T,K extends keyof any> = 
    K extends keyof T 
        ? True
        : False;
export type UnionKeys<T> = 
    T extends unkown 
        ? keyof T
        : never;
export type UnionzeProperties<T extends object> = T[keyof T];

export type Omit<T extends object,K extends keyof T> = 
    Pick<T,Exclude<keyof T, K>>
export type Intersect<T extends object,U extends Partial<T>> =
    Omit<U,DiffKeys<U,T>>;
export type Overwrite<T extends object,U extends object> = {
    [k in keyof T] : k extends keyof U ? U[k] : T[k]
}
export type Merge<T extends object, U extends object> = 
    Overwrite<T, U> & U;

```