


###  KeywordTypeNode
```ts
export interface KeywordTypeNode extends TypeNode {
    kind:
        | SyntaxKind.AnyKeyword
        | SyntaxKind.UnknownKeyword
        | SyntaxKind.NumberKeyword
        | SyntaxKind.BigIntKeyword
        | SyntaxKind.ObjectKeyword
        | SyntaxKind.BooleanKeyword
        | SyntaxKind.StringKeyword
        | SyntaxKind.SymbolKeyword
        | SyntaxKind.ThisKeyword
        | SyntaxKind.VoidKeyword
        | SyntaxKind.UndefinedKeyword
        | SyntaxKind.NullKeyword
        | SyntaxKind.NeverKeyword;
}
```

### ImportTypeNode 
```ts
export interface ImportTypeNode extends NodeWithTypeArguments {
        kind: SyntaxKind.ImportType;
        isTypeOf?: boolean;
        argument: TypeNode;
        qualifier?: EntityName;
    }

```

### LiteralImportTypeNode
```ts
export type LiteralImportTypeNode = ImportTypeNode & { argument: LiteralTypeNode & { literal: StringLiteral } };
```


### ThisTypeNode
```ts
export interface ThisTypeNode extends TypeNode {
    kind: SyntaxKind.ThisType;
}
```

