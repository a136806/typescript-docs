## 功能说明

```
NameDeclaration
    DynamicNamedDeclaration
        LateBoundDeclaration
    TypeParameterDeclaration
    SignatureDeclarationBase
         CallSignatureDeclaration
         ConstructSignatureDeclaration
         MethodSignature
         IndexSignatureDeclaration
         FunctionTypeNode
         ConstructorTypeNode
         JSDocFunctionType
         FunctionDeclaration
         MethodDeclaration
         ConstructorDeclaration
         AccessorDeclaration
         FunctionExpression
         ArrowFunction;
```
## 源代码

### NamedDeclaration
```ts
export interface NamedDeclaration extends Declaration {
     name?: DeclarationName;
}
```
### DynamicNamedDeclaration
```ts
export interface DynamicNamedDeclaration extends NamedDeclaration {
    name: ComputedPropertyName;
}

export interface ComputedPropertyName extends Node {
    parent: Declaration;
    kind: SyntaxKind.ComputedPropertyName;
    expression: Expression;
}
```

### LateBoundDeclaration
```ts
export interface LateBoundDeclaration extends DynamicNamedDeclaration {
    name: LateBoundName;
}

export interface LateBoundName extends ComputedPropertyName {
    expression: EntityNameExpression;
}
```
### TypeParameterDeclaration
```ts
export interface TypeParameterDeclaration extends NamedDeclaration {
    kind: SyntaxKind.TypeParameter;
    parent: DeclarationWithTypeParameterChildren | InferTypeNode;
    name: Identifier;
    /** Note: Consider calling `getEffectiveConstraintOfTypeParameter` */
    constraint?: TypeNode;
    default?: TypeNode;

    // For error recovery purposes.
    expression?: Expression;
}
```

### PropertyLikeDeclaration
```ts
export interface PropertyLikeDeclaration extends NamedDeclaration {
    name: PropertyName;
}

```
###  ClassElement
> (ClassObjectDeclaration)



### SignatureDeclarationBase
(SignatureDclaration)

### VariableDeclaration
> (VariableDeclaration.md)

### ParameterDeclaration
> (VariableDeclaration.md)

### BindingElement
> (VariableDeclaration.md)

### ClassLikeDeclarationBase
### ObjectLiteralElement
> (ClassObjectDeclaration)
### ClassElement
### TypeElement
### EnumMember
```ts
export interface EnumMember extends NamedDeclaration, JSDocContainer {
    kind: SyntaxKind.EnumMember;
    parent: EnumDeclaration;
    // This does include ComputedPropertyName, but the parser will give an error
    // if it parses a ComputedPropertyName in an EnumMember
    name: PropertyName;
    initializer?: Expression;
}
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

###  ImportSpecifier
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