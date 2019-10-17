## 功能说明
- Token类型

## 源代码
```ts
export interface Token<TKind extends SyntaxKind> extends Node {
    kind: TKind;
}

export type DotToken = Token<SyntaxKind.DotToken>;
export type DotDotDotToken = Token<SyntaxKind.DotDotDotToken>;
export type QuestionToken = Token<SyntaxKind.QuestionToken>;
export type QuestionDotToken = Token<SyntaxKind.QuestionDotToken>;
export type ExclamationToken = Token<SyntaxKind.ExclamationToken>;
export type ColonToken = Token<SyntaxKind.ColonToken>;
export type EqualsToken = Token<SyntaxKind.EqualsToken>;
export type AsteriskToken = Token<SyntaxKind.AsteriskToken>;
export type EqualsGreaterThanToken = Token<SyntaxKind.EqualsGreaterThanToken>;
export type EndOfFileToken = Token<SyntaxKind.EndOfFileToken> & JSDocContainer;
export type ReadonlyToken = Token<SyntaxKind.ReadonlyKeyword>;
export type AwaitKeywordToken = Token<SyntaxKind.AwaitKeyword>;
export type PlusToken = Token<SyntaxKind.PlusToken>;
export type MinusToken = Token<SyntaxKind.MinusToken>;
export type AssertsToken = Token<SyntaxKind.AssertsKeyword>;
```

### BinaryOperatorToken
```ts
export type BinaryOperatorToken = Token<BinaryOperator>;
```

### AssignmentOperatorToken
```ts
export type AssignmentOperatorToken = Token<AssignmentOperator>;
```