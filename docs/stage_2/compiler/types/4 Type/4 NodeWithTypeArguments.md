

## 源代码
### TypeReferenceType
```ts
export type TypeReferenceType = TypeReferenceNode | ExpressionWithTypeArguments;
```
### NodeWithTypeArguments
```ts
export interface NodeWithTypeArguments extends TypeNode {
    typeArguments?: NodeArray<TypeNode>;
}
```

### TypeReferenceType

```ts
export interface TypeReferenceNode extends NodeWithTypeArguments {
    kind: SyntaxKind.TypeReference;
    typeName: EntityName;
}

export type TypeReferenceType = TypeReferenceNode | ExpressionWithTypeArguments;
```
