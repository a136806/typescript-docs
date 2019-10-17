### TypeFlags
```ts
export const enum TypeFlags {
        Any             = 1 << 0,
        Unknown         = 1 << 1,
        String          = 1 << 2,
        Number          = 1 << 3,
        Boolean         = 1 << 4,
        Enum            = 1 << 5,
        BigInt          = 1 << 6,
        StringLiteral   = 1 << 7,
        NumberLiteral   = 1 << 8,
        BooleanLiteral  = 1 << 9,
        EnumLiteral     = 1 << 10,  // Always combined with StringLiteral, NumberLiteral, or Union
        BigIntLiteral   = 1 << 11,
        ESSymbol        = 1 << 12,  // Type of symbol primitive introduced in ES6
        UniqueESSymbol  = 1 << 13,  // unique symbol
        Void            = 1 << 14,
        Undefined       = 1 << 15,
        Null            = 1 << 16,
        Never           = 1 << 17,  // Never type
        TypeParameter   = 1 << 18,  // Type parameter
        Object          = 1 << 19,  // Object type
        Union           = 1 << 20,  // Union (T | U)
        Intersection    = 1 << 21,  // Intersection (T & U)
        Index           = 1 << 22,  // keyof T
        IndexedAccess   = 1 << 23,  // T[K]
        Conditional     = 1 << 24,  // T extends U ? X : Y
        Substitution    = 1 << 25,  // Type parameter substitution
        NonPrimitive    = 1 << 26,  // intrinsic object type

        /* @internal */
        AnyOrUnknown = Any | Unknown,
        /* @internal */
        Nullable = Undefined | Null,
        Literal = StringLiteral | NumberLiteral | BigIntLiteral | BooleanLiteral,
        Unit = Literal | UniqueESSymbol | Nullable,
        StringOrNumberLiteral = StringLiteral | NumberLiteral,
        /* @internal */
        StringOrNumberLiteralOrUnique = StringLiteral | NumberLiteral | UniqueESSymbol,
        /* @internal */
        DefinitelyFalsy = StringLiteral | NumberLiteral | BigIntLiteral | BooleanLiteral | Void | Undefined | Null,
        PossiblyFalsy = DefinitelyFalsy | String | Number | BigInt | Boolean,
        /* @internal */
        Intrinsic = Any | Unknown | String | Number | BigInt | Boolean | BooleanLiteral | ESSymbol | Void | Undefined | Null | Never | NonPrimitive,
        /* @internal */
        Primitive = String | Number | BigInt | Boolean | Enum | EnumLiteral | ESSymbol | Void | Undefined | Null | Literal | UniqueESSymbol,
        StringLike = String | StringLiteral,
        NumberLike = Number | NumberLiteral | Enum,
        BigIntLike = BigInt | BigIntLiteral,
        BooleanLike = Boolean | BooleanLiteral,
        EnumLike = Enum | EnumLiteral,
        ESSymbolLike = ESSymbol | UniqueESSymbol,
        VoidLike = Void | Undefined,
        /* @internal */
        DisjointDomains = NonPrimitive | StringLike | NumberLike | BigIntLike | BooleanLike | ESSymbolLike | VoidLike | Null,
        UnionOrIntersection = Union | Intersection,
        StructuredType = Object | Union | Intersection,
        TypeVariable = TypeParameter | IndexedAccess,
        InstantiableNonPrimitive = TypeVariable | Conditional | Substitution,
        InstantiablePrimitive = Index,
        Instantiable = InstantiableNonPrimitive | InstantiablePrimitive,
        StructuredOrInstantiable = StructuredType | Instantiable,
        /* @internal */
        ObjectFlagsType = Nullable | Never | Object | Union | Intersection,
        /* @internal */
        Simplifiable = IndexedAccess | Conditional,
        // 'Narrowable' types are types where narrowing actually narrows.
        // This *should* be every type other than null, undefined, void, and never
        Narrowable = Any | Unknown | StructuredOrInstantiable | StringLike | NumberLike | BigIntLike | BooleanLike | ESSymbol | UniqueESSymbol | NonPrimitive,
        NotUnionOrUnit = Any | Unknown | ESSymbol | Object | NonPrimitive,
        /* @internal */
        NotPrimitiveUnion = Any | Unknown | Enum | Void | Never | StructuredOrInstantiable,
        // The following flags are aggregated during union and intersection type construction
        /* @internal */
        IncludesMask = Any | Unknown | Primitive | Never | Object | Union | NonPrimitive,
        // The following flags are used for different purposes during union and intersection type construction
        /* @internal */
        IncludesStructuredOrInstantiable = TypeParameter,
        /* @internal */
        IncludesNonWideningType = Intersection,
        /* @internal */
        IncludesWildcard = Index,
        /* @internal */
        IncludesEmptyObject = IndexedAccess,
        // The following flag is used for different purposes by maybeTypeOfKind
        /* @internal */
        GenericMappedType = Never,
}

```

### DestructuringPattern
```ts
export type DestructuringPattern = BindingPattern | ObjectLiteralExpression | ArrayLiteralExpression;
```

### Type
```ts
export interface Type {
    flags: TypeFlags;                // Flags
    /* @internal */ id: number;      // Unique ID
    /* @internal */ checker: TypeChecker;
    symbol: Symbol;                  // Symbol associated with type (if any)
    pattern?: DestructuringPattern;  // Destructuring pattern represented by type (if any)
    aliasSymbol?: Symbol;            // Alias associated with type
    aliasTypeArguments?: readonly Type[];     // Alias type arguments (if any)
    /* @internal */ aliasTypeArgumentsContainsMarker?: boolean;   // Alias type arguments (if any)
    /* @internal */
    permissiveInstantiation?: Type;  // Instantiation with type parameters mapped to wildcard type
    /* @internal */
    restrictiveInstantiation?: Type; // Instantiation with type parameters mapped to unconstrained form
    /* @internal */
    immediateBaseConstraint?: Type;  // Immediate base constraint cache
    /* @internal */
    widened?: Type; // Cached widened form of the type
}
```

### IntrinsicType
```ts
export interface IntrinsicType extends Type {
    intrinsicName: string;        // Name of intrinsic type
    objectFlags: ObjectFlags;
}

```

### NullableType
```ts
export interface NullableType extends IntrinsicType {
    objectFlags: ObjectFlags;
}
```

### FreshableIntrinsicType
```ts
export interface FreshableIntrinsicType extends IntrinsicType {
    freshType: IntrinsicType;     // Fresh version of type
    regularType: IntrinsicType;   // Regular version of type
}
```
### FreshableType
```ts
export type FreshableType = LiteralType | FreshableIntrinsicType;
```

### LiteralType

```ts
export interface LiteralType extends Type {
    value: string | number | PseudoBigInt; // Value of literal
    freshType: LiteralType;                // Fresh version of type
    regularType: LiteralType;              // Regular version of type
}
```

### UniqueESSymbolType
```ts
export interface UniqueESSymbolType extends Type {
    symbol: Symbol;
    escapedName: __String;
}
```
### StringLiteralType
```ts
export interface StringLiteralType extends LiteralType {
    value: string;
}
```

### NumberLiteralType
```ts
export interface NumberLiteralType extends LiteralType {
    value: number;
}
```

### BigIntLiteralType
```ts
export interface BigIntLiteralType extends LiteralType {
    value: PseudoBigInt;
}
```

### EnumType
```ts
export interface EnumType extends Type {
}
```

### ObjectFlags
```ts
export const enum ObjectFlags {
    Class            = 1 << 0,  // Class
    Interface        = 1 << 1,  // Interface
    Reference        = 1 << 2,  // Generic type reference
    Tuple            = 1 << 3,  // Synthesized generic tuple type
    Anonymous        = 1 << 4,  // Anonymous
    Mapped           = 1 << 5,  // Mapped
    Instantiated     = 1 << 6,  // Instantiated anonymous or mapped type
    ObjectLiteral    = 1 << 7,  // Originates in an object literal
    EvolvingArray    = 1 << 8,  // Evolving array type
    ObjectLiteralPatternWithComputedProperties = 1 << 9,  // Object literal pattern with computed properties
    ContainsSpread   = 1 << 10, // Object literal contains spread operation
    ReverseMapped    = 1 << 11, // Object contains a property from a reverse-mapped type
    JsxAttributes    = 1 << 12, // Jsx attributes type
    MarkerType       = 1 << 13, // Marker type used for variance probing
    JSLiteral        = 1 << 14, // Object type declared in JS - disables errors on read/write of nonexisting members
    FreshLiteral     = 1 << 15, // Fresh object literal
    ArrayLiteral     = 1 << 16, // Originates in an array literal
    /* @internal */
    PrimitiveUnion   = 1 << 17, // Union of only primitive types
    /* @internal */
    ContainsWideningType = 1 << 18, // Type is or contains undefined or null widening type
    /* @internal */
    ContainsObjectOrArrayLiteral = 1 << 19, // Type is or contains object literal type
    /* @internal */
    NonInferrableType = 1 << 20, // Type is or contains anyFunctionType or silentNeverType
    ClassOrInterface = Class | Interface,
    /* @internal */
    RequiresWidening = ContainsWideningType | ContainsObjectOrArrayLiteral,
    /* @internal */
    PropagatingFlags = ContainsWideningType | ContainsObjectOrArrayLiteral | NonInferrableType
}

```

### ObjectFlagsType
```ts
export type ObjectFlagsType 
    = NullableType 
    | ObjectType 
    | UnionType 
    | IntersectionType
    ;
```

### ObjectType
```ts
export interface ObjectType extends Type {
    objectFlags: ObjectFlags;
    /* @internal */ members?: SymbolTable;             // Properties by name
    /* @internal */ properties?: Symbol[];             // Properties
    /* @internal */ callSignatures?: readonly Signature[];      // Call signatures of type
    /* @internal */ constructSignatures?: readonly Signature[]; // Construct signatures of type
    /* @internal */ stringIndexInfo?: IndexInfo;      // String indexing info
    /* @internal */ numberIndexInfo?: IndexInfo;      // Numeric indexing info
}
```

### InterfaceType
```ts
export interface InterfaceType extends ObjectType {
    typeParameters: TypeParameter[] | undefined;      // Type parameters (undefined if non-generic)
    outerTypeParameters: TypeParameter[] | undefined; // Outer type parameters (undefined if none)
    localTypeParameters: TypeParameter[] | undefined; // Local type parameters (undefined if none)
    thisType: TypeParameter | undefined;              // The "this" type (undefined if none)
    /* @internal */
    resolvedBaseConstructorType?: Type;               // Resolved base constructor type of class
    /* @internal */
    resolvedBaseTypes: BaseType[];                    // Resolved base types
}
```

### BaseType
```ts
export type BaseType = ObjectType | IntersectionType | TypeVariable; 
```

### InterfaceTypeWithDeclaredMembers
```ts
export interface InterfaceTypeWithDeclaredMembers extends InterfaceType {
    declaredProperties: Symbol[];                   // Declared members
    declaredCallSignatures: Signature[];            // Declared call signatures
    declaredConstructSignatures: Signature[];       // Declared construct signatures
    declaredStringIndexInfo?: IndexInfo; // Declared string indexing info
    declaredNumberIndexInfo?: IndexInfo; // Declared numeric indexing info
}
```

### TypeReference
```ts
export interface TypeReference extends ObjectType {
    target: GenericType;    // Type reference target
    node?: TypeReferenceNode | ArrayTypeNode | TupleTypeNode;
    /* @internal */
    mapper?: TypeMapper;
    /* @internal */
    resolvedTypeArguments?: readonly Type[];  // Resolved type reference type arguments
    /* @internal */
    literalType?: TypeReference;  // Clone of type with ObjectFlags.ArrayLiteral set
}
```

### DeferredTypeReference
```ts
export interface DeferredTypeReference extends TypeReference {
    /* @internal */
    node: TypeReferenceNode | ArrayTypeNode | TupleTypeNode;
    /* @internal */
    mapper?: TypeMapper;
}
```

### VarianceFlags
```ts
export const enum VarianceFlags {
    Invariant     =      0,  // Neither covariant nor contravariant
    Covariant     = 1 << 0,  // Covariant
    Contravariant = 1 << 1,  // Contravariant
    Bivariant     = Covariant | Contravariant,  // Both covariant and contravariant
    Independent   = 1 << 2,  // Unwitnessed type parameter
    VarianceMask  = Invariant | Covariant | Contravariant | Independent, // Mask containing all measured variances without the unmeasurable flag
    Unmeasurable  = 1 << 3,  // Variance result is unusable - relationship relies on structural comparisons which are not reflected in generic relationships
    Unreliable    = 1 << 4,  // Variance result is unreliable - checking may produce false negatives, but not false positives
    AllowsStructuralFallback = Unmeasurable | Unreliable,
}
```

### GenericType
```ts
export interface GenericType extends InterfaceType, TypeReference {
    /* @internal */
    instantiations: Map<TypeReference>;  // Generic instantiation cache
    /* @internal */
    variances?: VarianceFlags[];  // Variance of each type parameter
}
```

### TupleType
```ts
export interface TupleType extends GenericType {
    minLength: number;
    hasRestElement: boolean;
    readonly: boolean;
    associatedNames?: __String[];
}
```

### TupleTypeReference
```ts
export interface TupleTypeReference extends TypeReference {
    target: TupleType;
}
```

### UnionOrIntersectionType
```ts
export interface UnionOrIntersectionType extends Type {
    types: Type[];                    // Constituent types
    /* @internal */
    objectFlags: ObjectFlags;
    /* @internal */
    propertyCache: SymbolTable;       // Cache of resolved properties
    /* @internal */
    resolvedProperties: Symbol[];
    /* @internal */
    resolvedIndexType: IndexType;
    /* @internal */
    resolvedStringIndexType: IndexType;
    /* @internal */
    resolvedBaseConstraint: Type;
    /* @internal */
    couldContainTypeVariables: boolean;
}
```

### UnionType
```ts
export interface UnionType extends UnionOrIntersectionType {
    /* @internal */
    possiblePropertyCache?: SymbolTable;       // Cache of _all_ resolved properties less any from aparent members
}
```

### IntersectionType
```ts
export interface IntersectionType extends UnionOrIntersectionType {
    /* @internal */
    resolvedApparentType: Type;
}
```

### StructuredType
```ts
export type StructuredType = ObjectType | UnionType | IntersectionType;
```

### AnonymousType
```ts
export interface AnonymousType extends ObjectType {
    target?: AnonymousType;  // Instantiation target
    mapper?: TypeMapper;     // Instantiation mapper
}
```

### MappedType
```ts
export interface MappedType extends AnonymousType {
    declaration: MappedTypeNode;
    typeParameter?: TypeParameter;
    constraintType?: Type;
    templateType?: Type;
    modifiersType?: Type;
    resolvedApparentType?: Type;
}
```

### EvolvingArrayType
```ts
export interface EvolvingArrayType extends ObjectType {
    elementType: Type;      // Element expressions of evolving array type
    finalArrayType?: Type;  // Final array type of evolving array type
}
```

### ReverseMappedType
```ts
export interface ReverseMappedType extends ObjectType {
    source: Type;
    mappedType: MappedType;
    constraintType: IndexType;
}
```

### ResolvedType
```ts
export interface ResolvedType extends ObjectType, UnionOrIntersectionType {
    members: SymbolTable;             // Properties by name
    properties: Symbol[];             // Properties
    callSignatures: readonly Signature[];      // Call signatures of type
    constructSignatures: readonly Signature[]; // Construct signatures of type
}
```

### FreshObjectLiteralType
```ts
export interface FreshObjectLiteralType extends ResolvedType {
    regularType: ResolvedType;  // Regular version of fresh type
}
```

### IterationTypes
```ts
export interface IterationTypes {
    readonly yieldType: Type;
    readonly returnType: Type;
    readonly nextType: Type;
}
```

### IterableOrIteratorType
```ts
export interface IterableOrIteratorType extends ObjectType, UnionType {
    iterationTypesOfGeneratorReturnType?: IterationTypes;
    iterationTypesOfAsyncGeneratorReturnType?: IterationTypes;
    iterationTypesOfIterable?: IterationTypes;
    iterationTypesOfIterator?: IterationTypes;
    iterationTypesOfAsyncIterable?: IterationTypes;
    iterationTypesOfAsyncIterator?: IterationTypes;
    iterationTypesOfIteratorResult?: IterationTypes;
}
```


### PromiseOrAwaitableType
```ts
export interface PromiseOrAwaitableType extends ObjectType, UnionType {
    promiseTypeOfPromiseConstructor?: Type;
    promisedTypeOfPromise?: Type;
    awaitedTypeOfType?: Type;
}
```

### SyntheticDefaultModuleType
```ts
export interface SyntheticDefaultModuleType extends Type {
    syntheticType?: Type;
}
```

### InstantiableType
```ts
export interface InstantiableType extends Type {
    /* @internal */
    resolvedBaseConstraint?: Type;
    /* @internal */
    resolvedIndexType?: IndexType;
    /* @internal */
    resolvedStringIndexType?: IndexType;
}
```

### TypeParameter
```ts
export interface TypeParameter extends InstantiableType {
    /** Retrieve using getConstraintFromTypeParameter */
    /* @internal */
    constraint?: Type;        // Constraint
    /* @internal */
    default?: Type;
    /* @internal */
    target?: TypeParameter;  // Instantiation target
    /* @internal */
    mapper?: TypeMapper;     // Instantiation mapper
    /* @internal */
    isThisType?: boolean;
    /* @internal */
    resolvedDefaultType?: Type;
}
```

### IndexedAccessType
```ts
export interface IndexedAccessType extends InstantiableType {
    objectType: Type;
    indexType: Type;
    constraint?: Type;
    simplifiedForReading?: Type;
    simplifiedForWriting?: Type;
}
```

### TypeVariable
```ts
export type TypeVariable = TypeParameter | IndexedAccessType;
```

### IndexType
```ts
export interface IndexType extends InstantiableType {
    type: InstantiableType | UnionOrIntersectionType;
    /* @internal */
    stringsOnly: boolean;
}
```

### ConditionalRoot
```ts
export interface ConditionalRoot {
    node: ConditionalTypeNode;
    checkType: Type;
    extendsType: Type;
    trueType: Type;
    falseType: Type;
    isDistributive: boolean;
    inferTypeParameters?: TypeParameter[];
    outerTypeParameters?: TypeParameter[];
    instantiations?: Map<Type>;
    aliasSymbol?: Symbol;
    aliasTypeArguments?: Type[];
}
```

### ConditionalType
```ts
export interface ConditionalType extends InstantiableType {
    root: ConditionalRoot;
    checkType: Type;
    extendsType: Type;
    resolvedTrueType: Type;
    resolvedFalseType: Type;
    /* @internal */
    resolvedInferredTrueType?: Type; // The `trueType` instantiated with the `combinedMapper`, if present
    /* @internal */
    resolvedDefaultConstraint?: Type;
    /* @internal */
    mapper?: TypeMapper;
    /* @internal */
    combinedMapper?: TypeMapper;
}
```

### SubstitutionType
```ts
export interface SubstitutionType extends InstantiableType {
    typeVariable: TypeVariable;  // Target type variable
    substitute: Type;            // Type to substitute for type parameter
}
```