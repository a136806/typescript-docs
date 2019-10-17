## 

## 
### Symbol
```ts
export interface Symbol {
    flags: SymbolFlags;                     // Symbol flags
    escapedName: __String;                  // Name of symbol
    declarations: Declaration[];            // Declarations associated with this symbol
    valueDeclaration: Declaration;          // First value declaration of the symbol
    members?: SymbolTable;                  // Class, interface or object literal instance members
    exports?: SymbolTable;                  // Module exports
    globalExports?: SymbolTable;            // Conditional global UMD exports
    /* @internal */ id?: number;            // Unique id (used to look up SymbolLinks)
    /* @internal */ mergeId?: number;       // Merge id (used to look up merged symbol)
    /* @internal */ parent?: Symbol;        // Parent symbol
    /* @internal */ exportSymbol?: Symbol;  // Exported symbol associated with this symbol
    /* @internal */ nameType?: Type;        // Type associated with a late-bound symbol
    /* @internal */ constEnumOnlyModule?: boolean; // True if module contains only const enums or other modules with only const enums
    /* @internal */ isReferenced?: SymbolFlags; // True if the symbol is referenced elsewhere. Keeps track of the meaning of a reference in case a symbol is both a type parameter and parameter.
    /* @internal */ isReplaceableByMethod?: boolean; // Can this Javascript class property be replaced by a method symbol?
    /* @internal */ isAssigned?: boolean;   // True if the symbol is a parameter with assignments
    /* @internal */ assignmentDeclarationMembers?: Map<Declaration>; // detected late-bound assignment declarations associated with the symbol
}
```
### SymbolFlags
```ts
export const enum SymbolFlags {
    None                    = 0,
    FunctionScopedVariable  = 1 << 0,   // Variable (var) or parameter
    BlockScopedVariable     = 1 << 1,   // A block-scoped variable (let or const)
    Property                = 1 << 2,   // Property or enum member
    EnumMember              = 1 << 3,   // Enum member
    Function                = 1 << 4,   // Function
    Class                   = 1 << 5,   // Class
    Interface               = 1 << 6,   // Interface
    ConstEnum               = 1 << 7,   // Const enum
    RegularEnum             = 1 << 8,   // Enum
    ValueModule             = 1 << 9,   // Instantiated module
    NamespaceModule         = 1 << 10,  // Uninstantiated module
    TypeLiteral             = 1 << 11,  // Type Literal or mapped type
    ObjectLiteral           = 1 << 12,  // Object Literal
    Method                  = 1 << 13,  // Method
    Constructor             = 1 << 14,  // Constructor
    GetAccessor             = 1 << 15,  // Get accessor
    SetAccessor             = 1 << 16,  // Set accessor
    Signature               = 1 << 17,  // Call, construct, or index signature
    TypeParameter           = 1 << 18,  // Type parameter
    TypeAlias               = 1 << 19,  // Type alias
    ExportValue             = 1 << 20,  // Exported value marker (see comment in declareModuleMember in binder)
    Alias                   = 1 << 21,  // An alias for another symbol (see comment in isAliasSymbolDeclaration in checker)
    Prototype               = 1 << 22,  // Prototype property (no source representation)
    ExportStar              = 1 << 23,  // Export * declaration
    Optional                = 1 << 24,  // Optional property
    Transient               = 1 << 25,  // Transient symbol (created during type check)
    Assignment              = 1 << 26,  // Assignment treated as declaration (eg `this.prop = 1`)
    ModuleExports           = 1 << 27,  // Symbol for CommonJS `module` of `module.exports`

    /* @internal */
    All = FunctionScopedVariable | BlockScopedVariable | Property | EnumMember | Function | Class | Interface | ConstEnum | RegularEnum | ValueModule | NamespaceModule | TypeLiteral
        | ObjectLiteral | Method | Constructor | GetAccessor | SetAccessor | Signature | TypeParameter | TypeAlias | ExportValue | Alias | Prototype | ExportStar | Optional | Transient,

    Enum = RegularEnum | ConstEnum,
    Variable = FunctionScopedVariable | BlockScopedVariable,
    Value = Variable | Property | EnumMember | ObjectLiteral | Function | Class | Enum | ValueModule | Method | GetAccessor | SetAccessor,
    Type = Class | Interface | Enum | EnumMember | TypeLiteral | TypeParameter | TypeAlias,
    Namespace = ValueModule | NamespaceModule | Enum,
    Module = ValueModule | NamespaceModule,
    Accessor = GetAccessor | SetAccessor,

    // Variables can be redeclared, but can not redeclare a block-scoped declaration with the
    // same name, or any other value that is not a variable, e.g. ValueModule or Class
    FunctionScopedVariableExcludes = Value & ~FunctionScopedVariable,

    // Block-scoped declarations are not allowed to be re-declared
    // they can not merge with anything in the value space
    BlockScopedVariableExcludes = Value,

    ParameterExcludes = Value,
    PropertyExcludes = None,
    EnumMemberExcludes = Value | Type,
    FunctionExcludes = Value & ~(Function | ValueModule | Class),
    ClassExcludes = (Value | Type) & ~(ValueModule | Interface | Function), // class-interface mergability done in checker.ts
    InterfaceExcludes = Type & ~(Interface | Class),
    RegularEnumExcludes = (Value | Type) & ~(RegularEnum | ValueModule), // regular enums merge only with regular enums and modules
    ConstEnumExcludes = (Value | Type) & ~ConstEnum, // const enums merge only with const enums
    ValueModuleExcludes = Value & ~(Function | Class | RegularEnum | ValueModule),
    NamespaceModuleExcludes = 0,
    MethodExcludes = Value & ~Method,
    GetAccessorExcludes = Value & ~SetAccessor,
    SetAccessorExcludes = Value & ~GetAccessor,
    TypeParameterExcludes = Type & ~TypeParameter,
    TypeAliasExcludes = Type,
    AliasExcludes = Alias,

    ModuleMember = Variable | Function | Class | Interface | Enum | Module | TypeAlias | Alias,

    ExportHasLocal = Function | Class | Enum | ValueModule,

    BlockScoped = BlockScopedVariable | Class | Enum,

    PropertyOrAccessor = Property | Accessor,

    ClassMember = Method | Accessor | Property,

    /* @internal */
    ExportSupportsDefaultModifier = Class | Function | Interface,

    /* @internal */
    ExportDoesNotSupportDefaultModifier = ~ExportSupportsDefaultModifier,

    /* @internal */
    // The set of things we consider semantically classifiable.  Used to speed up the LS during
    // classification.
    Classifiable = Class | Enum | TypeAlias | Interface | TypeParameter | Module | Alias,

    /* @internal */
    LateBindingContainer = Class | Interface | TypeLiteral | ObjectLiteral | Function,
}
```

### Declaration
> (Declaration)
### SymbolTable
```ts
export type __String = (string & { __escapedIdentifier: void }) | (void & { __escapedIdentifier: void }) | InternalSymbolName;

/** ReadonlyMap where keys are `__String`s. */
export interface ReadonlyUnderscoreEscapedMap<T> {
    get(key: __String): T | undefined;
    has(key: __String): boolean;
    forEach(action: (value: T, key: __String) => void): void;
    readonly size: number;
    keys(): Iterator<__String>;
    values(): Iterator<T>;
    entries(): Iterator<[__String, T]>;
}

/** Map where keys are `__String`s. */
export interface UnderscoreEscapedMap<T> extends ReadonlyUnderscoreEscapedMap<T> {
    set(key: __String, value: T): this;
    delete(key: __String): boolean;
    clear(): void;
}
export type SymbolTable = UnderscoreEscapedMap<Symbol>;
```


### SymbolLinks
```ts
export interface SymbolLinks {
    immediateTarget?: Symbol;                   // Immediate target of an alias. May be another alias. Do not access directly, use `checker.getImmediateAliasedSymbol` instead.
    target?: Symbol;                            // Resolved (non-alias) target of an alias
    type?: Type;                                // Type of value symbol
    uniqueESSymbolType?: Type;                  // UniqueESSymbol type for a symbol
    declaredType?: Type;                        // Type of class, interface, enum, type alias, or type parameter
    resolvedJSDocType?: Type;                   // Resolved type of a JSDoc type reference
    typeParameters?: TypeParameter[];           // Type parameters of type alias (undefined if non-generic)
    outerTypeParameters?: TypeParameter[];      // Outer type parameters of anonymous object type
    instantiations?: Map<Type>;                 // Instantiations of generic type alias (undefined if non-generic)
    inferredClassSymbol?: Map<TransientSymbol>; // Symbol of an inferred ES5 constructor function
    mapper?: TypeMapper;                        // Type mapper for instantiation alias
    referenced?: boolean;                       // True if alias symbol has been referenced as a value
    containingType?: UnionOrIntersectionType;   // Containing union or intersection type for synthetic property
    leftSpread?: Symbol;                        // Left source for synthetic spread property
    rightSpread?: Symbol;                       // Right source for synthetic spread property
    syntheticOrigin?: Symbol;                   // For a property on a mapped or spread type, points back to the original property
    isDiscriminantProperty?: boolean;           // True if discriminant synthetic property
    resolvedExports?: SymbolTable;              // Resolved exports of module or combined early- and late-bound static members of a class.
    resolvedMembers?: SymbolTable;              // Combined early- and late-bound members of a symbol
    exportsChecked?: boolean;                   // True if exports of external module have been checked
    typeParametersChecked?: boolean;            // True if type parameters of merged class and interface declarations have been checked.
    isDeclarationWithCollidingName?: boolean;   // True if symbol is block scoped redeclaration
    bindingElement?: BindingElement;            // Binding element associated with property symbol
    exportsSomeValue?: boolean;                 // True if module exports some value (not just types)
    enumKind?: EnumKind;                        // Enum declaration classification
    originatingImport?: ImportDeclaration | ImportCall; // Import declaration which produced the symbol, present if the symbol is marked as uncallable but had call signatures in `resolveESModuleSymbol`
    lateSymbol?: Symbol;                // Late-bound symbol for a computed property
    specifierCache?: Map<string>;     // For symbols corresponding to external modules, a cache of incoming path -> module specifier name mappings
    extendedContainers?: Symbol[];      // Containers (other than the parent) which this symbol is aliased in
    extendedContainersByFile?: Map<Symbol[]>;      // Containers (other than the parent) which this symbol is aliased in
    variances?: VarianceFlags[];             // Alias symbol type argument variance cache
    deferralConstituents?: Type[];      // Calculated list of constituents for a deferred type
    deferralParent?: Type;              // Source union/intersection of a deferred type
    cjsExportMerged?: Symbol;           // Version of the symbol with all non export= exports merged with the export= target
}

```

### EnumKind
```ts
export const enum EnumKind {
    Numeric,                            // Numeric enum (each member has a TypeFlags.Enum type)
    Literal                             // Literal enum (each member has a TypeFlags.EnumLiteral type)
}
```

### TransientSymbol
```ts
export interface TransientSymbol extends Symbol, SymbolLinks {
    checkFlags: CheckFlags;
}
```

### ReverseMappedSymbol
```ts
export interface ReverseMappedSymbol extends TransientSymbol {
    propertyType: Type;
    mappedType: MappedType;
    constraintType: IndexType;
}
```

### InternalSymbolName
```ts
export const enum InternalSymbolName {
    Call = "__call", // Call signatures
    Constructor = "__constructor", // Constructor implementations
    New = "__new", // Constructor signatures
    Index = "__index", // Index signatures
    ExportStar = "__export", // Module export * declarations
    Global = "__global", // Global self-reference
    Missing = "__missing", // Indicates missing symbol
    Type = "__type", // Anonymous type literal symbol
    Object = "__object", // Anonymous object literal declaration
    JSXAttributes = "__jsxAttributes", // Anonymous JSX attributes object literal declaration
    Class = "__class", // Unnamed class expression
    Function = "__function", // Unnamed function expression
    Computed = "__computed", // Computed property name declaration with dynamic name
    Resolving = "__resolving__", // Indicator symbol used to mark partially resolved type aliases
    ExportEquals = "export=", // Export assignment symbol
    Default = "default", // Default export symbol (technically not wholly internal, but included here for usability)
    This = "this",
}
```