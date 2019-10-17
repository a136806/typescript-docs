##

### CaseBlock
```ts
export interface CaseBlock extends Node {
    kind: SyntaxKind.CaseBlock;
    parent: SwitchStatement;
    clauses: NodeArray<CaseOrDefaultClause>;
}
```

### CaseClause
```ts
export interface CaseClause extends Node {
    kind: SyntaxKind.CaseClause;
    parent: CaseBlock;
    expression: Expression;
    statements: NodeArray<Statement>;
}
```

### DefaultClause
```ts
export interface DefaultClause extends Node {
    kind: SyntaxKind.DefaultClause;
    parent: CaseBlock;
    statements: NodeArray<Statement>;
}
```

### CaseOrDefaultClause
```ts
export type CaseOrDefaultClause = CaseClause | DefaultClause;
```


### CatchClause
```ts
export interface CatchClause extends Node {
    kind: SyntaxKind.CatchClause;
    parent: TryStatement;
    variableDeclaration?: VariableDeclaration;
    block: Block;
}
```


### InterfaceDeclaration
```ts
export interface InterfaceDeclaration extends DeclarationStatement, JSDocContainer {
    kind: SyntaxKind.InterfaceDeclaration;
    name: Identifier;
    typeParameters?: NodeArray<TypeParameterDeclaration>;
    heritageClauses?: NodeArray<HeritageClause>;
    members: NodeArray<TypeElement>;
}
```

### HeritageClause
```ts
export interface HeritageClause extends Node {
    kind: SyntaxKind.HeritageClause;
    parent: InterfaceDeclaration | ClassLikeDeclaration;
    token: SyntaxKind.ExtendsKeyword | SyntaxKind.ImplementsKeyword;
    types: NodeArray<ExpressionWithTypeArguments>;
}
```