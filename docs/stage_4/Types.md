## TOC
- BasicType
    - PrimitiveType
    - InterfaceTypes
    - TupleTypes
    - FunctionTypes
    - ObjectTypes
    - ClassTypes
- AdvanceType
    - EnumMemberTypes
    - LiteralTypes
    - IndexTypes
- Type
    - IntersectionTypes
    - UnionTypes
    - ConditionTypes
    - MappedTypes
    - GenericType
- operator
    - typeof
    - instanceof
    - as
    - is
    - keyof
    - in
    - kind
    - []
    - infer
## BasicType
### Primitype
```ts
/* 
boolean,
number,
string,
symbol
void,
null,
undefined,
 */
let b1:boolean = true;
let n1:number = 123;
let s1:string = 'abc'
let key = Symbol();
```
### interface
```ts
interface Point {
    x:number,
    y:number,
    width:number,
    height:number
}
```
### Tuple
```ts
type args1 = [number,string,string];
```
## AdvanceType
### EnumMemberType
```ts
enum LogLevel {
    ERROR, WARN, INFO, DEBUG
}
/**
 * type LogLevelStrings = 'ERROR' | 'WARN' | 'INFO' | 'DEBUG';
 */
type LogLevelStrings = keyof typeof LogLevel;
```
### LiteralType
- stringLiteral
```ts
```
- numberLitral
### IndexType
```ts
```
## Type
### IntersectionTypes
- & 
> 多个类型的交集

### UnionType
- |
> 多个类型的并集 
### ConditionType
- extends
- Exclude
- Extract
- NonNullable
- ReturnType
- InstanceType
### MappedType
- Readonly
- Partial
- Pick
- Record
### GenericType
- <T>
## operator
- typeof typeVar
> 获取基础类型
- instanceof typeVar
> 获取object类型
- as typeVar
> 断言类型
- is typeVar
> 声明类型判断函数
- keyof typeVar
> 获取Type的key类型
- in typeVar
- [typeVar]
> 获取Type的key对应的value的类型
- infer typeVar
> 推断类型
- kind
> 自定义kind类型判断