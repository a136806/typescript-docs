## 
## 源代码

### JSDocTypeExpression
```ts
export interface JSDocTypeExpression extends TypeNode {
    kind: SyntaxKind.JSDocTypeExpression;
    type: TypeNode;
}
```

### JSDocType
```ts
export interface JSDocType extends TypeNode {
    _jsDocTypeBrand: any;
}
```

### JSDocAllType
```ts
export interface JSDocAllType extends JSDocType {
    kind: SyntaxKind.JSDocAllType;
}
```

### JSDocUnknownType
```ts
export interface JSDocUnknownType extends JSDocType {
    kind: SyntaxKind.JSDocUnknownType;
}
```

### JSDocNonNullableType
```ts
export interface JSDocNonNullableType extends JSDocType {
    kind: SyntaxKind.JSDocNonNullableType;
    type: TypeNode;
}
```

### JSDocNullableType
```ts
export interface JSDocNullableType extends JSDocType {
    kind: SyntaxKind.JSDocNullableType;
    type: TypeNode;
}
```

### JSDocOptionalType
```ts
export interface JSDocOptionalType extends JSDocType {
    kind: SyntaxKind.JSDocOptionalType;
    type: TypeNode;
}
```

### JSDocFunctionType
```ts
export interface JSDocFunctionType extends JSDocType, SignatureDeclarationBase {
    kind: SyntaxKind.JSDocFunctionType;
}
```

### JSDocVariadicType
```ts
export interface JSDocVariadicType extends JSDocType {
    kind: SyntaxKind.JSDocVariadicType;
    type: TypeNode;
}  
```

### JSDocNamepathType
```ts
export interface JSDocNamepathType extends JSDocType {
    kind: SyntaxKind.JSDocNamepathType;
    type: TypeNode;
}
```

### JSDocTypeReferencingNode
```ts
export type JSDocTypeReferencingNode 
    = JSDocVariadicType 
    | JSDocOptionalType 
    | JSDocNullableType 
    | JSDocNonNullableType;

```

### JSDoc
```ts
export interface JSDoc extends Node {
    kind: SyntaxKind.JSDocComment;
    parent: HasJSDoc;
    tags?: NodeArray<JSDocTag>;
    comment?: string;
}
```

### JSDocTag
```ts
export interface JSDocTag extends Node {
    parent: JSDoc | JSDocTypeLiteral;
    tagName: Identifier;
    comment?: string;
}
```

### JSDocUnknownTag
```ts
export interface JSDocUnknownTag extends JSDocTag {
    kind: SyntaxKind.JSDocTag;
}
```

### JSDocAugmentsTag
```ts
export interface JSDocAugmentsTag extends JSDocTag {
    kind: SyntaxKind.JSDocAugmentsTag;
    class: ExpressionWithTypeArguments & { expression: Identifier | PropertyAccessEntityNameExpression };
}
```

### JSDocAuthorTag
```ts
export interface JSDocAuthorTag extends JSDocTag {
    kind: SyntaxKind.JSDocAuthorTag;
}
```

### JSDocClassTag
```ts
export interface JSDocClassTag extends JSDocTag {
    kind: SyntaxKind.JSDocClassTag;
}
```

### JSDocEnumTag
```ts
export interface JSDocEnumTag extends JSDocTag, Declaration {
    parent: JSDoc;
    kind: SyntaxKind.JSDocEnumTag;
    typeExpression?: JSDocTypeExpression;
}
```

### JSDocThisTag
```ts
export interface JSDocThisTag extends JSDocTag {
    kind: SyntaxKind.JSDocThisTag;
    typeExpression?: JSDocTypeExpression;
}
```

### JSDocTemplateTag
```ts
export interface JSDocTemplateTag extends JSDocTag {
    kind: SyntaxKind.JSDocTemplateTag;
    constraint: JSDocTypeExpression | undefined;
    typeParameters: NodeArray<TypeParameterDeclaration>;
}
```

### JSDocReturnTag
```ts
export interface JSDocReturnTag extends JSDocTag {
    kind: SyntaxKind.JSDocReturnTag;
    typeExpression?: JSDocTypeExpression;
}
```

### JSDocTypeTag
```ts
export interface JSDocTypeTag extends JSDocTag {
    kind: SyntaxKind.JSDocTypeTag;
    typeExpression: JSDocTypeExpression;
}
```

### JSDocTypedefTag
```ts
export interface JSDocTypedefTag extends JSDocTag, NamedDeclaration {
    parent: JSDoc;
    kind: SyntaxKind.JSDocTypedefTag;
    fullName?: JSDocNamespaceDeclaration | Identifier;
    name?: Identifier;
    typeExpression?: JSDocTypeExpression | JSDocTypeLiteral;
}
```

### JSDocCallbackTag
```ts
export interface JSDocCallbackTag extends JSDocTag, NamedDeclaration {
    parent: JSDoc;
    kind: SyntaxKind.JSDocCallbackTag;
    fullName?: JSDocNamespaceDeclaration | Identifier;
    name?: Identifier;
    typeExpression: JSDocSignature;
}
```

### JSDocSignature
```ts
export interface JSDocSignature extends JSDocType, Declaration {
    kind: SyntaxKind.JSDocSignature;
    typeParameters?: readonly JSDocTemplateTag[];
    parameters: readonly JSDocParameterTag[];
    type: JSDocReturnTag | undefined;
}
```

### JSDocPropertyLikeTag
```ts
export interface JSDocPropertyLikeTag extends JSDocTag, Declaration {
    parent: JSDoc;
    name: EntityName;
    typeExpression?: JSDocTypeExpression;
    /** Whether the property name came before the type -- non-standard for JSDoc, but Typescript-like */
    isNameFirst: boolean;
    isBracketed: boolean;
}
```

### JSDocPropertyTag
```ts
export interface JSDocPropertyTag extends JSDocPropertyLikeTag {
    kind: SyntaxKind.JSDocPropertyTag;
}
```

### JSDocParameterTag
```ts
export interface JSDocParameterTag extends JSDocPropertyLikeTag {
    kind: SyntaxKind.JSDocParameterTag;
}
```

### JSDocTypeLiteral
```ts
export interface JSDocTypeLiteral extends JSDocType {
    kind: SyntaxKind.JSDocTypeLiteral;
    jsDocPropertyTags?: readonly JSDocPropertyLikeTag[];
    /** If true, then this type literal represents an *array* of its type. */
    isArrayType?: boolean;
}
```