## 源代码

### IndexKind
```ts
export const enum IndexKind {
    String,
    Number,
}
```

### IndexInfo
```ts
export interface IndexInfo {
    type: Type;
    isReadonly: boolean;
    declaration?: IndexSignatureDeclaration;
}
```

### TypeMapper
```ts
export type TypeMapper = (t: TypeParameter) => Type;
```

### InferencePriority
```ts
export const enum InferencePriority {
    NakedTypeVariable            = 1 << 0,  // Naked type variable in union or intersection type
    HomomorphicMappedType        = 1 << 1,  // Reverse inference for homomorphic mapped type
    PartialHomomorphicMappedType = 1 << 2,  // Partial reverse inference for homomorphic mapped type
    MappedTypeConstraint         = 1 << 3,  // Reverse inference for mapped type
    ReturnType                   = 1 << 4,  // Inference made from return type of generic function
    LiteralKeyof                 = 1 << 5,  // Inference made from a string literal to a keyof T
    NoConstraints                = 1 << 6,  // Don't infer from constraints of instantiable types
    AlwaysStrict                 = 1 << 7,  // Always use strict rules for contravariant inferences
    MaxValue                     = 1 << 8,  // Seed for inference priority tracking

    PriorityImpliesCombination = ReturnType | MappedTypeConstraint | LiteralKeyof,  // These priorities imply that the resulting type should be a combination of all candidates
    Circularity = -1,  // Inference circularity (value less than all other priorities)
}
```

### InferenceInfo

```ts
export interface InferenceInfo {
    typeParameter: TypeParameter;            // Type parameter for which inferences are being made
    candidates: Type[] | undefined;          // Candidates in covariant positions (or undefined)
    contraCandidates: Type[] | undefined;    // Candidates in contravariant positions (or undefined)
    inferredType?: Type;                     // Cache for resolved inferred type
    priority?: InferencePriority;            // Priority of current inference set
    topLevel: boolean;                       // True if all inferences are to top level occurrences
    isFixed: boolean;                        // True if inferences are fixed
}
```

### InferenceFlags
```ts
export const enum InferenceFlags {
    None            =      0,  // No special inference behaviors
    NoDefault       = 1 << 0,  // Infer unknownType for no inferences (otherwise anyType or emptyObjectType)
    AnyDefault      = 1 << 1,  // Infer anyType for no inferences (otherwise emptyObjectType)
    SkippedGenericFunction = 1 << 2, // A generic function was skipped during inference
}
```

### Ternary
```ts
export const enum Ternary {
    False = 0,
    Maybe = 1,
    True = -1
}
```

### TypeComparer
```ts
export type TypeComparer = (s: Type, t: Type, reportErrors?: boolean) => Ternary;
```

### InferenceContext
```ts
export interface InferenceContext {
    inferences: InferenceInfo[];                  // Inferences made for each type parameter
    signature?: Signature;                        // Generic signature for which inferences are made (if any)
    flags: InferenceFlags;                        // Inference flags
    compareTypes: TypeComparer;                   // Type comparer function
    mapper: TypeMapper;                           // Mapper that fixes inferences
    nonFixingMapper: TypeMapper;                  // Mapper that doesn't fix inferences
    returnMapper?: TypeMapper;                    // Type mapper for inferences from return types (if any)
    inferredTypeParameters?: readonly TypeParameter[]; // Inferred type parameters for function result
}
```

### WideningContext
```ts
export interface WideningContext {
    parent?: WideningContext;       // Parent context
    propertyName?: __String;        // Name of property in parent
    siblings?: Type[];              // Types of siblings
    resolvedProperties?: Symbol[];  // Properties occurring in sibling object literals
}
```

### AssignmentDeclarationKind
```ts 
export const enum AssignmentDeclarationKind {
    None,
    /// exports.name = expr
    ExportsProperty,
    /// module.exports = expr
    ModuleExports,
    /// className.prototype.name = expr
    PrototypeProperty,
    /// this.name = expr
    ThisProperty,
    // F.name = expr
    Property,
    // F.prototype = { ... }
    Prototype,
    // Object.defineProperty(x, 'name', { value: any, writable?: boolean (false by default) });
    // Object.defineProperty(x, 'name', { get: Function, set: Function });
    // Object.defineProperty(x, 'name', { get: Function });
    // Object.defineProperty(x, 'name', { set: Function });
    ObjectDefinePropertyValue,
    // Object.defineProperty(exports || module.exports, 'name', ...);
    ObjectDefinePropertyExports,
    // Object.defineProperty(Foo.prototype, 'name', ...);
    ObjectDefinePrototypeProperty,
}
```