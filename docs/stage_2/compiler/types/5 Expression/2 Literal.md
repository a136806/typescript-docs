### NullLiteral
```ts
export interface NullLiteral extends PrimaryExpression, TypeNode {
    kind: SyntaxKind.NullKeyword;
}
```

### BooleanLiteral
```ts
export interface BooleanLiteral extends PrimaryExpression, TypeNode {
    kind: SyntaxKind.TrueKeyword | SyntaxKind.FalseKeyword;
}
```

### NumericLiteral
```ts
export interface NumericLiteral extends LiteralExpression, Declaration {
        kind: SyntaxKind.NumericLiteral;
        /* @internal */
        numericLiteralFlags: TokenFlags;
    }
```

### BigIntLiteral
```ts
 export interface BigIntLiteral extends LiteralExpression {
        kind: SyntaxKind.BigIntLiteral;
    }
```
### LiteralExpression
```ts
export interface LiteralExpression extends LiteralLikeNode, PrimaryExpression {
    _literalExpressionBrand: any;
}
```
### RegularExpressionLiteral
```ts
export interface RegularExpressionLiteral extends LiteralExpression {
    kind: SyntaxKind.RegularExpressionLiteral;
}
```
### NoSubstitutionTemplateLiteral
```ts
export interface NoSubstitutionTemplateLiteral extends LiteralExpression, TemplateLiteralLikeNode, Declaration {
    kind: SyntaxKind.NoSubstitutionTemplateLiteral;
}
```

### LiteralLikeNode
```ts
export interface LiteralLikeNode extends Node {
    text: string;
    isUnterminated?: boolean;
    hasExtendedUnicodeEscape?: boolean;
}
```

### TemplateLiteralLikeNode
```ts
export interface TemplateLiteralLikeNode extends LiteralLikeNode {
        rawText?: string;
    }
```

### TemplateHead
```ts
export interface TemplateHead extends TemplateLiteralLikeNode {
        kind: SyntaxKind.TemplateHead;
        parent: TemplateExpression;
    }
```
### TemplateMiddle
```ts
export interface TemplateMiddle extends TemplateLiteralLikeNode {
        kind: SyntaxKind.TemplateMiddle;
        parent: TemplateSpan;
    }
```
### TemplateTail
```ts
export interface TemplateTail extends TemplateLiteralLikeNode {
        kind: SyntaxKind.TemplateTail;
        parent: TemplateSpan;
    }
```

### TemplateLiteral
```ts
export type TemplateLiteral = TemplateExpression | NoSubstitutionTemplateLiteral;
```


### ObjectLiteralExpression
```ts
export interface ObjectLiteralExpressionBase<T extends ObjectLiteralElement> extends PrimaryExpression, Declaration {
        properties: NodeArray<T>;
    }

export interface ObjectLiteralExpression extends ObjectLiteralExpressionBase<ObjectLiteralElementLike> {
    kind: SyntaxKind.ObjectLiteralExpression;
    /* @internal */
    multiLine?: boolean;
}
```

### ArrayLiteralExpression
```ts
export interface ArrayLiteralExpression extends PrimaryExpression {
    kind: SyntaxKind.ArrayLiteralExpression;
    elements: NodeArray<Expression>;
    /* @internal */
    multiLine?: boolean;
}
```