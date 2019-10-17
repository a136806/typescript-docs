## 

## 源代码

### Statement
```ts
export interface NotEmittedStatement extends Statement {
    kind: SyntaxKind.NotEmittedStatement;
}
```

### EndOfDeclarationMarker
```ts
export interface EndOfDeclarationMarker extends Statement {
    kind: SyntaxKind.EndOfDeclarationMarker;
}
```

###  MergeDeclarationMarker
```ts
export interface MergeDeclarationMarker extends Statement {
    kind: SyntaxKind.MergeDeclarationMarker;
}
```

### EmptyStatement
```ts
export interface EmptyStatement extends Statement {
    kind: SyntaxKind.EmptyStatement;
}
```

### DebuggerStatement
```ts
export interface DebuggerStatement extends Statement {
    kind: SyntaxKind.DebuggerStatement;
}
```

### DeclarationStatement
```ts
export interface DeclarationStatement extends NamedDeclaration, Statement {
    name?: Identifier | StringLiteral | NumericLiteral;
}
```

### MissingDeclaration
```ts
export interface MissingDeclaration extends DeclarationStatement {
    kind: SyntaxKind.MissingDeclaration;
    name?: Identifier;
}
```

### BlockLike
```ts
export type BlockLike = SourceFile | Block | ModuleBlock | CaseOrDefaultClause;
```

### Block
```ts
export interface Block extends Statement {
    kind: SyntaxKind.Block;
    statements: NodeArray<Statement>;
    /*@internal*/ multiLine?: boolean;
}
```

### VariableStatement
```ts
export interface VariableStatement extends Statement, JSDocContainer {
    kind: SyntaxKind.VariableStatement;
    declarationList: VariableDeclarationList;
}
```

### ExpressionStatement
```ts
export interface ExpressionStatement extends Statement, JSDocContainer {
    kind: SyntaxKind.ExpressionStatement;
    expression: Expression;
}
```

### PrologueDirective
```ts
export interface PrologueDirective extends ExpressionStatement {
    expression: StringLiteral;
}
```

### IfStatement
```ts
export interface IfStatement extends Statement {
    kind: SyntaxKind.IfStatement;
    expression: Expression;
    thenStatement: Statement;
    elseStatement?: Statement;
}
```

### IterationStatement
```ts
export interface IterationStatement extends Statement {
    statement: Statement;
}
```

### DoStatement
```ts
export interface DoStatement extends IterationStatement {
    kind: SyntaxKind.DoStatement;
    expression: Expression;
}
```

### WhileStatement
```ts
export interface WhileStatement extends IterationStatement {
    kind: SyntaxKind.WhileStatement;
    expression: Expression;
}
```


### ForStatement
```ts
export type ForInitializer = VariableDeclarationList | Expression;

export interface ForStatement extends IterationStatement {
    kind: SyntaxKind.ForStatement;
    initializer?: ForInitializer;
    condition?: Expression;
    incrementor?: Expression;
}
```

### ForInOrOfStatement
```ts
export type ForInOrOfStatement = ForInStatement | ForOfStatement;
```

### ForInStatement
```ts
export interface ForInStatement extends IterationStatement {
    kind: SyntaxKind.ForInStatement;
    initializer: ForInitializer;
    expression: Expression;
}
```

### ForOfStatement
```ts
export interface ForOfStatement extends IterationStatement {
    kind: SyntaxKind.ForOfStatement;
    awaitModifier?: AwaitKeywordToken;
    initializer: ForInitializer;
    expression: Expression;
}
```

### BreakStatement
```ts
 export interface BreakStatement extends Statement {
    kind: SyntaxKind.BreakStatement;
    label?: Identifier;
}
```

### ContinueStatement
```ts
export interface ContinueStatement extends Statement {
    kind: SyntaxKind.ContinueStatement;
    label?: Identifier;
}
```

### BreakOrContinueStatement
```ts
export type BreakOrContinueStatement = BreakStatement | ContinueStatement;
```

### ReturnStatement
```ts
export interface ReturnStatement extends Statement {
    kind: SyntaxKind.ReturnStatement;
    expression?: Expression;
}
```

### WithStatement
```ts
export interface WithStatement extends Statement {
    kind: SyntaxKind.WithStatement;
    expression: Expression;
    statement: Statement;
}
```

### SwitchStatement
```ts
export interface SwitchStatement extends Statement {
    kind: SyntaxKind.SwitchStatement;
    expression: Expression;
    caseBlock: CaseBlock;
    possiblyExhaustive?: boolean;
}
```

### LabeledStatement

```ts
export interface LabeledStatement extends Statement, JSDocContainer {
    kind: SyntaxKind.LabeledStatement;
    label: Identifier;
    statement: Statement;
}
```

### ThrowStatement
```ts
export interface ThrowStatement extends Statement {
    kind: SyntaxKind.ThrowStatement;
    expression?: Expression;
}
```


### TryStatement
```ts
export interface TryStatement extends Statement {
    kind: SyntaxKind.TryStatement;
    tryBlock: Block;
    catchClause?: CatchClause;
    finallyBlock?: Block;
}
```

### LateVisibilityPaintedStatement

```ts
export type LateVisibilityPaintedStatement =
    | AnyImportSyntax
    | VariableStatement
    | ClassDeclaration
    | FunctionDeclaration
    | ModuleDeclaration
    | TypeAliasDeclaration
    | InterfaceDeclaration
    | EnumDeclaration;
```

### SymbolVisibilityResult
```ts
export interface SymbolVisibilityResult {
    accessibility: SymbolAccessibility;
    aliasesToMakeVisible?: LateVisibilityPaintedStatement[]; // aliases that need to have this symbol visible
    errorSymbolName?: string; // Optional symbol name that results in error
    errorNode?: Node; // optional node that results in error
}
```

### SymbolAccessibilityResult
```ts
export interface SymbolAccessibilityResult extends SymbolVisibilityResult {
    errorModuleName?: string; // If the symbol is not visible from module, module's name
}
```

