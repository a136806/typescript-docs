
## 源代码

### TypePredicateKind
```ts
export const enum TypePredicateKind {
    This,
    Identifier,
    AssertsThis,
    AssertsIdentifier
}
```

### TypePredicateBase
```ts
export interface TypePredicateBase {
    kind: TypePredicateKind;
    type: Type | undefined;
}
```

### ThisTypePredicate
```ts
export interface ThisTypePredicate extends TypePredicateBase {
    kind: TypePredicateKind.This;
    parameterName: undefined;
    parameterIndex: undefined;
    type: Type;
}
```

### IdentifierTypePredicate
```ts
export interface IdentifierTypePredicate extends TypePredicateBase {
    kind: TypePredicateKind.Identifier;
    parameterName: string;
    parameterIndex: number;
    type: Type;
}
```

### AssertsThisTypePredicate
```ts
export interface AssertsThisTypePredicate extends TypePredicateBase {
    kind: TypePredicateKind.AssertsThis;
    parameterName: undefined;
    parameterIndex: undefined;
    type: Type | undefined;
}
```

### AssertsIdentifierTypePredicate

```ts
export interface AssertsIdentifierTypePredicate extends TypePredicateBase {
    kind: TypePredicateKind.AssertsIdentifier;
    parameterName: string;
    parameterIndex: number;
    type: Type | undefined;
}
```


### TypePredicate
```ts
export type TypePredicate 
    = ThisTypePredicate 
    | IdentifierTypePredicate 
    | AssertsThisTypePredicate 
    | AssertsIdentifierTypePredicate
    ;
```

### 