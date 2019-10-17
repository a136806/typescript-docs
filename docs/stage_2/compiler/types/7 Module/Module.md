##
## 源代码
### ModuleName
```ts
export type ModuleName = Identifier | StringLiteral;
```

### ModuleBody
```ts
export type ModuleBody = NamespaceBody | JSDocNamespaceBody;
```

### ModuleDeclaration
```ts
export interface ModuleDeclaration extends DeclarationStatement, JSDocContainer {
    kind: SyntaxKind.ModuleDeclaration;
    parent: ModuleBody | SourceFile;
    name: ModuleName;
    body?: ModuleBody | JSDocNamespaceDeclaration;
}
```

### NamespaceBody
```ts
export interface NamespaceDeclaration extends ModuleDeclaration {
    name: Identifier;
    body: NamespaceBody;
}
```

### JSDocNamespaceBody
```ts
export type JSDocNamespaceBody = Identifier | JSDocNamespaceDeclaration;
```

### JSDocNamespaceDeclaration
```ts
export interface JSDocNamespaceDeclaration extends ModuleDeclaration {
    name: Identifier;
    body?: JSDocNamespaceBody;
}
```

### ModuleBlock
```ts
export interface ModuleBlock extends Node, Statement {
    kind: SyntaxKind.ModuleBlock;
    parent: ModuleDeclaration;
    statements: NodeArray<Statement>;
}
```

### ModuleReference
```ts
export type ModuleReference = EntityName | ExternalModuleReference;
```

### ImportEqualsDeclaration
```ts
export interface ImportEqualsDeclaration extends DeclarationStatement, JSDocContainer {
        kind: SyntaxKind.ImportEqualsDeclaration;
        parent: SourceFile | ModuleBlock;
        name: Identifier;
        moduleReference: ModuleReference;
    }
```

### ExternalModuleReference
```ts
export interface ExternalModuleReference extends Node {
    kind: SyntaxKind.ExternalModuleReference;
    parent: ImportEqualsDeclaration;
    expression: Expression;
}
```

### ImportDeclaration
```ts
export interface ImportDeclaration extends Statement {
    kind: SyntaxKind.ImportDeclaration;
    parent: SourceFile | ModuleBlock;
    importClause?: ImportClause;
    /** If this is not a StringLiteral it will be a grammar error. */
    moduleSpecifier: Expression;
}
```

### NamedImportBindings
```ts
export type NamedImportBindings = NamespaceImport | NamedImports;
```

### ImportClause
```ts
export interface ImportClause extends NamedDeclaration {
    kind: SyntaxKind.ImportClause;
    parent: ImportDeclaration;
    name?: Identifier; // Default binding
    namedBindings?: NamedImportBindings;
}
```

### NamespaceImport
```ts
export interface NamespaceImport extends NamedDeclaration {
    kind: SyntaxKind.NamespaceImport;
    parent: ImportClause;
    name: Identifier;
}
```

### NamespaceExportDeclaration
```ts
export interface NamespaceExportDeclaration extends DeclarationStatement {
    kind: SyntaxKind.NamespaceExportDeclaration;
    name: Identifier;
}
```

### ExportDeclaration
```ts
export interface ExportDeclaration extends DeclarationStatement, JSDocContainer {
    kind: SyntaxKind.ExportDeclaration;
    parent: SourceFile | ModuleBlock;
    exportClause?: NamedExports;
    moduleSpecifier?: Expression;
}
```

### NamedImports
```ts
export interface NamedImports extends Node {
    kind: SyntaxKind.NamedImports;
    parent: ImportClause;
    elements: NodeArray<ImportSpecifier>;
}
```

### NamedExports
```ts
export interface NamedExports extends Node {
        kind: SyntaxKind.NamedExports;
        parent: ExportDeclaration;
        elements: NodeArray<ExportSpecifier>;
    }
```

### NamedImportsOrExports
```ts
export type NamedImportsOrExports = NamedImports | NamedExports;
```

### ImportSpecifier
```ts
export interface ImportSpecifier extends NamedDeclaration {
    kind: SyntaxKind.ImportSpecifier;
    parent: NamedImports;
    propertyName?: Identifier;  // Name preceding "as" keyword (or undefined when "as" is absent)
    name: Identifier;           // Declared name
}
```

### ExportSpecifier
```ts
export interface ExportSpecifier extends NamedDeclaration {
    kind: SyntaxKind.ExportSpecifier;
    parent: NamedExports;
    propertyName?: Identifier;  // Name preceding "as" keyword (or undefined when "as" is absent)
    name: Identifier;           // Declared name
}
```

### ImportOrExportSpecifier
```ts
export type ImportOrExportSpecifier = ImportSpecifier | ExportSpecifier;
```

### ExportAssignment
```ts
export interface ExportAssignment extends DeclarationStatement {
    kind: SyntaxKind.ExportAssignment;
    parent: SourceFile;
    isExportEquals?: boolean;
    expression: Expression;
}
```

### AnyImportSyntax
```ts
export type AnyImportSyntax = ImportDeclaration | ImportEqualsDeclaration;
```

### AnyImportOrReExport
```ts
export type AnyImportOrReExport = AnyImportSyntax | ExportDeclaration;
```

### ValidImportTypeNode
```ts
export interface ValidImportTypeNode extends ImportTypeNode {
    argument: LiteralTypeNode & { literal: StringLiteral };
}
```

### AnyValidImportOrReExport

```ts
export type AnyValidImportOrReExport =
    | (ImportDeclaration | ExportDeclaration) & { moduleSpecifier: StringLiteral }
    | ImportEqualsDeclaration & { moduleReference: ExternalModuleReference & { expression: StringLiteral } }
    | RequireOrImportCall
    | ValidImportTypeNode;
```

### RequireOrImportCall
```ts
 export type RequireOrImportCall = CallExpression & { arguments: [StringLiteralLike] };
```

### AllAccessorDeclarations
```ts
export interface AllAccessorDeclarations {
    firstAccessor: AccessorDeclaration;
    secondAccessor: AccessorDeclaration | undefined;
    getAccessor: GetAccessorDeclaration | undefined;
    setAccessor: SetAccessorDeclaration | undefined;
}
```

### TypeReferenceSerializationKind
```ts
export enum TypeReferenceSerializationKind {
    Unknown,
    TypeWithConstructSignatureAndValue,
    // The TypeReferenceNode resolves to a Void-like, Nullable, or Never type.
    VoidNullableOrNeverType,
    // The TypeReferenceNode resolves to a Number-like type.
    NumberLikeType,
    // The TypeReferenceNode resolves to a BigInt-like type.
    BigIntLikeType,
    // The TypeReferenceNode resolves to a String-like type.
    StringLikeType,
    // The TypeReferenceNode resolves to a Boolean-like type.
    BooleanType,
    // The TypeReferenceNode resolves to an Array-like type.
    ArrayLikeType,
    // The TypeReferenceNode resolves to the ESSymbol type.
    ESSymbolType,
    // The TypeReferenceNode resolved to the global Promise constructor symbol.
    Promise,
    // The TypeReferenceNode resolves to a Function type or a type with call signatures.
    TypeWithCallSignature,
    // The TypeReferenceNode resolves to any other type.
    ObjectType,
}
```

### PatternAmbientModule
```ts
export interface PatternAmbientModule {
    pattern: Pattern;
    symbol: Symbol;
}
```


```