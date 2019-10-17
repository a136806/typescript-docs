

### FunctionOrConstructorTypeNode
```ts
export type FunctionOrConstructorTypeNode = FunctionTypeNode | ConstructorTypeNode;
```
### FunctionOrConstructorTypeNodeBase
```ts
export interface FunctionOrConstructorTypeNodeBase extends TypeNode, SignatureDeclarationBase {
    kind: SyntaxKind.FunctionType | SyntaxKind.ConstructorType;
    type: TypeNode;
}
```
### FunctionTypeNode
```ts
export interface FunctionTypeNode extends FunctionOrConstructorTypeNodeBase {
    kind: SyntaxKind.FunctionType;
}
```

### ConstructorTypeNode
```ts
export interface ConstructorTypeNode extends FunctionOrConstructorTypeNodeBase {
    kind: SyntaxKind.ConstructorType;
}
```