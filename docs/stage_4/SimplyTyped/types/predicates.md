## 功能说明

```ts
export type KnownProblemPrototypeKeys = 'toString' | 'toLocaleString' | 'hasOwnProperty' | 'isPrototypeOf' | 'propertyIsEnumerable' | 'constructor' | 'valueOf';

export type ArrayPrototypeKeys = keyof unknown[];
export type NumberPrototypeKeys = keyof number;
export type BooleanPrototypeKeys = keyof false;
export type ObjectPrototypeKeys = keyof Object;
export type FunctionPrototypKeys = keyof Function;


export type IsNever<S extends string> = 
    Not<(Record<S,True> & Record<string,Record<string,False>>)>
export type IsType<T,X> = X extends T ? True : False;
export type IsArray<T> = T extends unknown[] ? True : False;
export type IsNumber<T> = T extends number ? True : False;
export type IsString<T> = T extends string ? True : False;

export type IsFunction<T> = 
    Or<
        T extends Function ? True : False,
        T extends Function ? True : False
    >;
export type IsStringFunction<T extends string> =    
    And<IsString<T>,IsNever<T>>;
export type IsBoolean<T> = T extends boolean ? True : False;
export type IsNull<T> = T extends null ? True : False;
export type IsUndefined<T> = T extends undefined ? True : False;
export type IsNil<T> = OR<IsNull<T>,IsUndefined<T>>;
export type IsObject<T> = 
    And<
        T extends object ? True : False,
        And<Not<IsFunction<T>>>,
        Not<IsArray<T>>
    >
export type IsAny<T> = 0 extends (1 & T) ? True : False;
```