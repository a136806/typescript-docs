# 功能说明
## 源代码
### Node
```ts
export interface Node extends TextRange {
    kind: SyntaxKind;
    flags: NodeFlags;
    /* @internal */ modifierFlagsCache: ModifierFlags;
    /* @internal */ transformFlags: TransformFlags;       // Flags for transforms, possibly undefined
    decorators?: NodeArray<Decorator>;                    // Array of decorators (in document order)
    modifiers?: ModifiersArray;                           // Array of modifiers
    /* @internal */ id?: number;                          // Unique id (used to look up NodeLinks)
    parent: Node;                                         // Parent node (initialized by binding)
    /* @internal */ original?: Node;                      // The original node if this is an updated node.
    /* @internal */ symbol: Symbol;                       // Symbol declared by node (initialized by binding)
    /* @internal */ locals?: SymbolTable;                 // Locals associated with node (initialized by binding)
    /* @internal */ nextContainer?: Node;                 // Next container in declaration order (initialized by binding)
    /* @internal */ localSymbol?: Symbol;                 // Local symbol declared by node (initialized by binding only for exported nodes)
    /* @internal */ flowNode?: FlowNode;                  // Associated FlowNode (initialized by binding)
    /* @internal */ emitNode?: EmitNode;                  // Associated EmitNode (initialized by transforms)
    /* @internal */ contextualType?: Type;                // Used to temporarily assign a contextual type during overload resolution
    /* @internal */ inferenceContext?: InferenceContext;  // Inference context for contextual type
}
```
### SyntaxKind
> 详细见(0Kind)
### NodeFlags
```ts
export const enum NodeFlags {
    None               = 0,
    Let                = 1 << 0,  // Variable declaration
    Const              = 1 << 1,  // Variable declaration
    NestedNamespace    = 1 << 2,  // Namespace declaration
    Synthesized        = 1 << 3,  // Node was synthesized during transformation
    Namespace          = 1 << 4,  // Namespace declaration
    OptionalChain      = 1 << 5,  // Chained MemberExpression rooted to a pseudo-OptionalExpression
    ExportContext      = 1 << 6,  // Export context (initialized by binding)
    ContainsThis       = 1 << 7,  // Interface contains references to "this"
    HasImplicitReturn  = 1 << 8,  // If function implicitly returns on one of codepaths (initialized by binding)
    HasExplicitReturn  = 1 << 9,  // If function has explicit reachable return on one of codepaths (initialized by binding)
    GlobalAugmentation = 1 << 10,  // Set if module declaration is an augmentation for the global scope
    HasAsyncFunctions  = 1 << 11, // If the file has async functions (initialized by binding)
    DisallowInContext  = 1 << 12, // If node was parsed in a context where 'in-expressions' are not allowed
    YieldContext       = 1 << 13, // If node was parsed in the 'yield' context created when parsing a generator
    DecoratorContext   = 1 << 14, // If node was parsed as part of a decorator
    AwaitContext       = 1 << 15, // If node was parsed in the 'await' context created when parsing an async function
    ThisNodeHasError   = 1 << 16, // If the parser encountered an error when parsing the code that created this node
    JavaScriptFile     = 1 << 17, // If node was parsed in a JavaScript
    ThisNodeOrAnySubNodesHasError = 1 << 18, // If this node or any of its children had an error
    HasAggregatedChildData = 1 << 19, // If we've computed data from children and cached it in this node

    // These flags will be set when the parser encounters a dynamic import expression or 'import.meta' to avoid
    // walking the tree if the flags are not set. However, these flags are just a approximation
    // (hence why it's named "PossiblyContainsDynamicImport") because once set, the flags never get cleared.
    // During editing, if a dynamic import is removed, incremental parsing will *NOT* clear this flag.
    // This means that the tree will always be traversed during module resolution, or when looking for external module indicators.
    // However, the removal operation should not occur often and in the case of the
    // removal, it is likely that users will add the import anyway.
    // The advantage of this approach is its simplicity. For the case of batch compilation,
    // we guarantee that users won't have to pay the price of walking the tree if a dynamic import isn't used.
    /* @internal */ PossiblyContainsDynamicImport = 1 << 20,
    /* @internal */ PossiblyContainsImportMeta    = 1 << 21,

    JSDoc                                         = 1 << 22, // If node was parsed inside jsdoc
    /* @internal */ Ambient                       = 1 << 23, // If node was inside an ambient context -- a declaration file, or inside something with the `declare` modifier.
    /* @internal */ InWithStatement               = 1 << 24, // If any ancestor of node was the `statement` of a WithStatement (not the `expression`)
    JsonFile                                      = 1 << 25, // If node was parsed in a Json

    BlockScoped = Let | Const,

    ReachabilityCheckFlags = HasImplicitReturn | HasExplicitReturn,
    ReachabilityAndEmitFlags = ReachabilityCheckFlags | HasAsyncFunctions,

    // Parsing context flags
    ContextFlags = DisallowInContext | YieldContext | DecoratorContext | AwaitContext | JavaScriptFile | InWithStatement | Ambient,

    // Exclude these flags when parsing a Type
    TypeExcludesFlags = YieldContext | AwaitContext,

    // Represents all flags that are potentially set once and
    // never cleared on SourceFiles which get re-used in between incremental parses.
    // See the comment above on `PossiblyContainsDynamicImport` and `PossiblyContainsImportMeta`.
    /* @internal */ PermanentlySetIncrementalFlags = PossiblyContainsDynamicImport | PossiblyContainsImportMeta,
}
```
### ModifierFlags
```ts
export const enum ModifierFlags {
    None =               0,
    Export =             1 << 0,  // Declarations
    Ambient =            1 << 1,  // Declarations

    Public =             1 << 2,  // Property/Method
    Private =            1 << 3,  // Property/Method
    Protected =          1 << 4,  // Property/Method
    Static =             1 << 5,  // Property/Method
    Readonly =           1 << 6,  // Property/Method

    Abstract =           1 << 7,  // Class/Method/ConstructSignature
    Async =              1 << 8,  // Property/Method/Function
    Default =            1 << 9,  // Function/Class (export default declaration)
    Const =              1 << 11, // Const enum
    HasComputedFlags =   1 << 29, // Modifier flags have been computed

    AccessibilityModifier = Public | Private | Protected,
    // Accessibility modifiers and 'readonly' can be attached to a parameter in a constructor to make it a property.
    ParameterPropertyModifier = AccessibilityModifier | Readonly,
    NonPublicAccessibilityModifier = Private | Protected,

    TypeScriptModifier = Ambient | Public | Private | Protected | Readonly | Abstract | Const,
    ExportDefault = Export | Default,
    All = Export | Ambient | Public | Private | Protected | Static | Readonly | Abstract | Async | Default | Const
}
```
### TransformFlags
```ts
 export const enum TransformFlags {
    None = 0,

    // Facts
    // - Flags used to indicate that a node or subtree contains syntax that requires transformation.
    ContainsTypeScript = 1 << 0,
    ContainsJsx = 1 << 1,
    ContainsESNext = 1 << 2,
    ContainsES2019 = 1 << 3,
    ContainsES2018 = 1 << 4,
    ContainsES2017 = 1 << 5,
    ContainsES2016 = 1 << 6,
    ContainsES2015 = 1 << 7,
    ContainsGenerator = 1 << 8,
    ContainsDestructuringAssignment = 1 << 9,

    // Markers
    // - Flags used to indicate that a subtree contains a specific transformation.
    ContainsTypeScriptClassSyntax = 1 << 10, // Decorators, Property Initializers, Parameter Property Initializers
    ContainsLexicalThis = 1 << 11,
    ContainsRestOrSpread = 1 << 12,
    ContainsObjectRestOrSpread = 1 << 13,
    ContainsComputedPropertyName = 1 << 14,
    ContainsBlockScopedBinding = 1 << 15,
    ContainsBindingPattern = 1 << 16,
    ContainsYield = 1 << 17,
    ContainsHoistedDeclarationOrCompletion = 1 << 18,
    ContainsDynamicImport = 1 << 19,
    ContainsClassFields = 1 << 20,

    // Please leave this as 1 << 29.
    // It is the maximum bit we can set before we outgrow the size of a v8 small integer (SMI) on an x86 system.
    // It is a good reminder of how much room we have left
    HasComputedFlags = 1 << 29, // Transform flags have been computed.

    // Assertions
    // - Bitmasks that are used to assert facts about the syntax of a node and its subtree.
    AssertTypeScript = ContainsTypeScript,
    AssertJsx = ContainsJsx,
    AssertESNext = ContainsESNext,
    AssertES2019 = ContainsES2019,
    AssertES2018 = ContainsES2018,
    AssertES2017 = ContainsES2017,
    AssertES2016 = ContainsES2016,
    AssertES2015 = ContainsES2015,
    AssertGenerator = ContainsGenerator,
    AssertDestructuringAssignment = ContainsDestructuringAssignment,

    // Scope Exclusions
    // - Bitmasks that exclude flags from propagating out of a specific context
    //   into the subtree flags of their container.
    OuterExpressionExcludes = HasComputedFlags,
    PropertyAccessExcludes = OuterExpressionExcludes,
    NodeExcludes = PropertyAccessExcludes,
    ArrowFunctionExcludes = NodeExcludes | ContainsTypeScriptClassSyntax | ContainsBlockScopedBinding | ContainsYield | ContainsHoistedDeclarationOrCompletion | ContainsBindingPattern | ContainsObjectRestOrSpread,
    FunctionExcludes = NodeExcludes | ContainsTypeScriptClassSyntax | ContainsLexicalThis | ContainsBlockScopedBinding | ContainsYield | ContainsHoistedDeclarationOrCompletion | ContainsBindingPattern | ContainsObjectRestOrSpread,
    ConstructorExcludes = NodeExcludes | ContainsLexicalThis | ContainsBlockScopedBinding | ContainsYield | ContainsHoistedDeclarationOrCompletion | ContainsBindingPattern | ContainsObjectRestOrSpread,
    MethodOrAccessorExcludes = NodeExcludes | ContainsLexicalThis | ContainsBlockScopedBinding | ContainsYield | ContainsHoistedDeclarationOrCompletion | ContainsBindingPattern | ContainsObjectRestOrSpread,
    PropertyExcludes = NodeExcludes | ContainsLexicalThis,
    ClassExcludes = NodeExcludes | ContainsTypeScriptClassSyntax | ContainsComputedPropertyName,
    ModuleExcludes = NodeExcludes | ContainsTypeScriptClassSyntax | ContainsLexicalThis | ContainsBlockScopedBinding | ContainsHoistedDeclarationOrCompletion,
    TypeExcludes = ~ContainsTypeScript,
    ObjectLiteralExcludes = NodeExcludes | ContainsTypeScriptClassSyntax | ContainsComputedPropertyName | ContainsObjectRestOrSpread,
    ArrayLiteralOrCallOrNewExcludes = NodeExcludes | ContainsRestOrSpread,
    VariableDeclarationListExcludes = NodeExcludes | ContainsBindingPattern | ContainsObjectRestOrSpread,
    ParameterExcludes = NodeExcludes,
    CatchClauseExcludes = NodeExcludes | ContainsObjectRestOrSpread,
    BindingPatternExcludes = NodeExcludes | ContainsRestOrSpread,

    // Propagating flags
    // - Bitmasks for flags that should propagate from a child
    PropertyNamePropagatingFlags = ContainsLexicalThis,

    // Masks
    // - Additional bitmasks
}
```
### NodeArray
```ts
export interface NodeArray<T extends Node> extends ReadonlyArray<T>, TextRange {
    hasTrailingComma?: boolean;
    /* @internal */ transformFlags: TransformFlags;   // Flags for transforms, possibly undefined
}
```
### Decorator
```ts
export interface Decorator extends Node {
    kind: SyntaxKind.Decorator;
    parent: NamedDeclaration;
    expression: LeftHandSideExpression;
}
```
### ModifiersArray
```ts
export type ModifiersArray = NodeArray<Modifier>;
```
### Symbol
> (Symbol)
### SymbolTable
> (Symbol)
### FlowNode
### EmitNode
### Type
### InferenceContext

