## UnaryOperator
> 单目运算符

### PrefixUnaryOperator
> 前缀单目运算符(单个参数)
```ts
export type PrefixUnaryOperator
        = SyntaxKind.PlusPlusToken
        | SyntaxKind.MinusMinusToken
        | SyntaxKind.PlusToken
        | SyntaxKind.MinusToken
        | SyntaxKind.TildeToken
        | SyntaxKind.ExclamationToken;
```

### PostfixUnaryOperator
> 后缀单目运算符
```ts
export type PostfixUnaryOperator
        = SyntaxKind.PlusPlusToken
        | SyntaxKind.MinusMinusToken
        ;
```

## BinaryOperator
> 双目运算符

### ExponentiationOperator
```ts
export type ExponentiationOperator
    = SyntaxKind.AsteriskAsteriskToken
    ;
```
### MultiplicativeOperator
```ts
export type MultiplicativeOperator
    = SyntaxKind.AsteriskToken
    | SyntaxKind.SlashToken
    | SyntaxKind.PercentToken
    ;
```
### MultiplicativeOperatorOrHigher
```ts
export type MultiplicativeOperatorOrHigher
    = ExponentiationOperator
    | MultiplicativeOperator
    ;
```

### AdditiveOperator
```ts
export type AdditiveOperator
        = SyntaxKind.PlusToken
        | SyntaxKind.MinusToken
        ;
```

### ShiftOperator
```ts
export type ShiftOperator
        = SyntaxKind.LessThanLessThanToken  /* << */
        | SyntaxKind.GreaterThanGreaterThanToken /* >> */
        | SyntaxKind.GreaterThanGreaterThanGreaterThanToken /* >>> */
        ;
```

### ShiftOperatorOrHigher
```ts
export type ShiftOperatorOrHigher
    = AdditiveOperatorOrHigher
    | ShiftOperator
    ;
```

### RelationalOperator
```ts
export type RelationalOperator
    = SyntaxKind.LessThanToken
    | SyntaxKind.LessThanEqualsToken
    | SyntaxKind.GreaterThanToken
    | SyntaxKind.GreaterThanEqualsToken
    | SyntaxKind.InstanceOfKeyword
    | SyntaxKind.InKeyword
    ;
```

### RelationalOperatorOrHigher
```ts
export type RelationalOperatorOrHigher
        = ShiftOperatorOrHigher
        | RelationalOperator
        ;
```

### EqualityOperator
```ts
export type EqualityOperator
        = SyntaxKind.EqualsEqualsToken
        | SyntaxKind.EqualsEqualsEqualsToken
        | SyntaxKind.ExclamationEqualsEqualsToken
        | SyntaxKind.ExclamationEqualsToken
        ;
```

### EqualityOperatorOrHigher
```ts
export type EqualityOperatorOrHigher
        = RelationalOperatorOrHigher
        | EqualityOperator;
```

### BitwiseOperator
```ts
export type BitwiseOperator
        = SyntaxKind.AmpersandToken
        | SyntaxKind.BarToken
        | SyntaxKind.CaretToken
        ;
```

### BitwiseOperatorOrHigher
```ts
export type BitwiseOperatorOrHigher
        = EqualityOperatorOrHigher
        | BitwiseOperator
        ;
```

### LogicalOperator
```ts
export type LogicalOperator
        = SyntaxKind.AmpersandAmpersandToken
        | SyntaxKind.BarBarToken
        ;
```

### LogicalOperatorOrHigher
```ts
export type LogicalOperatorOrHigher
        = BitwiseOperatorOrHigher
        | LogicalOperator
        ;
```

### CompoundAssignmentOperator
```ts
export type CompoundAssignmentOperator
        = SyntaxKind.PlusEqualsToken
        | SyntaxKind.MinusEqualsToken
        | SyntaxKind.AsteriskAsteriskEqualsToken
        | SyntaxKind.AsteriskEqualsToken
        | SyntaxKind.SlashEqualsToken
        | SyntaxKind.PercentEqualsToken
        | SyntaxKind.AmpersandEqualsToken
        | SyntaxKind.BarEqualsToken
        | SyntaxKind.CaretEqualsToken
        | SyntaxKind.LessThanLessThanEqualsToken
        | SyntaxKind.GreaterThanGreaterThanGreaterThanEqualsToken
        | SyntaxKind.GreaterThanGreaterThanEqualsToken
        ;
```

### AssignmentOperator
```ts
export type AssignmentOperator
        = SyntaxKind.EqualsToken
        | CompoundAssignmentOperator
        ;
```

### AssignmentOperatorOrHigher
```ts
export type AssignmentOperatorOrHigher
        = SyntaxKind.QuestionQuestionToken
        | LogicalOperatorOrHigher
        | AssignmentOperator
        ;
```

### BinaryOperator
```ts
export type BinaryOperator
        = AssignmentOperatorOrHigher
        | SyntaxKind.CommaToken
        ;
```