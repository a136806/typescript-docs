## 功能说明
> 函数签名声明

###  SignatureKind
```ts
export const enum SignatureKind {
    Call,
    Construct,
}
```
###  Signature
```ts
export interface Signature {
    declaration?: SignatureDeclaration | JSDocSignature; // Originating declaration
    typeParameters?: readonly TypeParameter[];   // Type parameters (undefined if non-generic)
    parameters: readonly Symbol[];               // Parameters
    /* @internal */
    thisParameter?: Symbol;             // symbol of this-type parameter
    /* @internal */
    // See comment in `instantiateSignature` for why these are set lazily.
    resolvedReturnType?: Type;          // Lazily set by `getReturnTypeOfSignature`.
    /* @internal */
    // Lazily set by `getTypePredicateOfSignature`.
    // `undefined` indicates a type predicate that has not yet been computed.
    // Uses a special `noTypePredicate` sentinel value to indicate that there is no type predicate. This looks like a TypePredicate at runtime to avoid polymorphism.
    resolvedTypePredicate?: TypePredicate;
    /* @internal */
    minArgumentCount: number;           // Number of non-optional parameters
    /* @internal */
    hasRestParameter: boolean;          // True if last parameter is rest parameter
    /* @internal */
    hasLiteralTypes: boolean;           // True if specialized
    /* @internal */
    target?: Signature;                 // Instantiation target
    /* @internal */
    mapper?: TypeMapper;                // Instantiation mapper
    /* @internal */
    unionSignatures?: Signature[];      // Underlying signatures of a union signature
    /* @internal */
    erasedSignatureCache?: Signature;   // Erased version of signature (deferred)
    /* @internal */
    canonicalSignatureCache?: Signature; // Canonical version of signature (deferred)
    /* @internal */
    isolatedSignatureType?: ObjectType; // A manufactured type that just contains the signature for purposes of signature comparison
    /* @internal */
    instantiations?: Map<Signature>;    // Generic signature instantiation cache
    /* @internal */
    isOptionalCall?: boolean;
}
```
###  SignatureDeclaration
```ts
export type SignatureDeclaration =
    | CallSignatureDeclaration
    | ConstructSignatureDeclaration
    | MethodSignature
    | IndexSignatureDeclaration
    | FunctionTypeNode
    | ConstructorTypeNode
    | JSDocFunctionType
    | FunctionDeclaration
    | MethodDeclaration
    | ConstructorDeclaration
    | AccessorDeclaration
    | FunctionExpression
    | ArrowFunction;
```

### SignatureDeclarationBase
```ts
export interface SignatureDeclarationBase extends NamedDeclaration, JSDocContainer {
    kind: SignatureDeclaration["kind"];
    name?: PropertyName;
    typeParameters?: NodeArray<TypeParameterDeclaration>;
    parameters: NodeArray<ParameterDeclaration>;
    type?: TypeNode;
    /* @internal */ typeArguments?: NodeArray<TypeNode>; // Used for quick info, replaces typeParameters for instantiated signatures
}
```

### CallSignatureDeclaration
```ts
export interface CallSignatureDeclaration extends SignatureDeclarationBase, TypeElement {
    kind: SyntaxKind.CallSignature;
}
```

### ConstructSignatureDeclaration

### MethodSignature
```ts
export interface MethodSignature extends SignatureDeclarationBase, TypeElement {
    kind: SyntaxKind.MethodSignature;
    parent: ObjectTypeDeclaration;
    name: PropertyName;
}
```

### IndexSignatureDeclaration
```ts
export interface IndexSignatureDeclaration extends SignatureDeclarationBase, ClassElement, TypeElement {
    kind: SyntaxKind.IndexSignature;
    parent: ObjectTypeDeclaration;
}
```
### FunctionTypeNode
### ConstructorTypeNode
### JSDocFunctionType
### FunctionDeclaration
### MethodDeclaration
### ConstructorDeclaration
### AccessorDeclaration
### FunctionExpression
### ArrowFunction;

