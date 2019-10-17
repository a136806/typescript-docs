## 源代码
### Expression
```ts
export interface Expression extends Node {
    _expressionBrand: any;
}
```

### OmittedExpression
```ts
export interface OmittedExpression extends Expression {
    kind: SyntaxKind.OmittedExpression;
}
```

### Unary(单目表达式)
### UnaryExpression
```ts
export interface UnaryExpression extends Expression {
     _unaryExpressionBrand: any;
}
```

### UpdateExpression
```ts
export type IncrementExpression = UpdateExpression;
export interface UpdateExpression extends UnaryExpression {
    _updateExpressionBrand: any;
}
```

### PrefixUnaryExpression
> 前缀单目表达式
```ts
export interface PrefixUnaryExpression extends UpdateExpression {
    kind: SyntaxKind.PrefixUnaryExpression;
    operator: PrefixUnaryOperator;
    operand: UnaryExpression;
}
```


### PostfixUnaryExpression
> 
```ts
export interface PostfixUnaryExpression extends UpdateExpression {
    kind: SyntaxKind.PostfixUnaryExpression;
    operand: LeftHandSideExpression;
    operator: PostfixUnaryOperator;
}
```

### LeftHandSideExpression
```ts
export interface LeftHandSideExpression extends UpdateExpression {
     _leftHandSideExpressionBrand: any;
}
```

### PartiallyEmittedExpression
```ts
export interface PartiallyEmittedExpression extends LeftHandSideExpression {
    kind: SyntaxKind.PartiallyEmittedExpression;
    expression: Expression;
}
```

### MemberExpression
```ts
export interface MemberExpression extends LeftHandSideExpression {
    _memberExpressionBrand: any;
}
```

### PrimaryExpression
```ts
export interface PrimaryExpression extends MemberExpression {
    _primaryExpressionBrand: any;
}
```

### NullLiteral
### BooleanLiteral
> (Literal)

### ThisExpression
```ts
export interface ThisExpression extends PrimaryExpression, KeywordTypeNode {
    kind: SyntaxKind.ThisKeyword;
}
```

###  NewExpression
```ts
export interface NewExpression extends PrimaryExpression, Declaration {
    kind: SyntaxKind.NewExpression;
    expression: LeftHandSideExpression;
    typeArguments?: NodeArray<TypeNode>;
    arguments?: NodeArray<Expression>;
}
```
### SuperExpression
```ts
export interface SuperExpression extends PrimaryExpression {
    kind: SyntaxKind.SuperKeyword;
}
```
### ImportExpression
```ts
export interface ImportExpression extends PrimaryExpression {
    kind: SyntaxKind.ImportKeyword;
}
```



### DeleteExpression
```ts
export interface DeleteExpression extends UnaryExpression {
    kind: SyntaxKind.DeleteExpression;
    expression: UnaryExpression;
}
```
### TypeOfExpression
```ts
export interface TypeOfExpression extends UnaryExpression {
    kind: SyntaxKind.TypeOfExpression;
    expression: UnaryExpression;
}
```
### VoidExpression
```ts
export interface VoidExpression extends UnaryExpression {
    kind: SyntaxKind.VoidExpression;
    expression: UnaryExpression;
}
```
### AwaitExpression
```ts
export interface AwaitExpression extends UnaryExpression {
    kind: SyntaxKind.AwaitExpression;
    expression: UnaryExpression;
}
```
### YieldExpression
```ts
export interface YieldExpression extends Expression {
    kind: SyntaxKind.YieldExpression;
    asteriskToken?: AsteriskToken;
    expression?: Expression;
}
```

### SyntheticExpression


### BinaryExpression
```ts
export interface BinaryExpression extends Expression, Declaration {
        kind: SyntaxKind.BinaryExpression;
        left: Expression;
        operatorToken: BinaryOperatorToken;
        right: Expression;
    }
```

### AssignmentExpression
```ts
export interface AssignmentExpression<TOperator extends AssignmentOperatorToken> extends BinaryExpression {
        left: LeftHandSideExpression;
        operatorToken: TOperator;
    }
```

### ObjectDestructuringAssignment
```ts
export interface ObjectDestructuringAssignment extends AssignmentExpression<EqualsToken> {
    left: ObjectLiteralExpression;
}
```
### ArrayDestructuringAssignment
```ts
export interface ArrayDestructuringAssignment extends AssignmentExpression<EqualsToken> {
        left: ArrayLiteralExpression;
    }
```

### DestructuringAssignment
```ts
export type DestructuringAssignment
        = ObjectDestructuringAssignment
        | ArrayDestructuringAssignment
        ;
```

### BindingOrAssignmentElement
```ts
export type BindingOrAssignmentElement
        = VariableDeclaration
        | ParameterDeclaration
        | BindingElement
        | PropertyAssignment // AssignmentProperty
        | ShorthandPropertyAssignment // AssignmentProperty
        | SpreadAssignment // AssignmentRestProperty
        | OmittedExpression // Elision
        | SpreadElement // AssignmentRestElement
        | ArrayLiteralExpression // ArrayAssignmentPattern
        | ObjectLiteralExpression // ObjectAssignmentPattern
        | AssignmentExpression<EqualsToken> // AssignmentElement
        | Identifier // DestructuringAssignmentTarget
        | PropertyAccessExpression // DestructuringAssignmentTarget
        | ElementAccessExpression // DestructuringAssignmentTarget
        ;
```

### BindingOrAssignmentElementRestIndicator
```ts
export type BindingOrAssignmentElementRestIndicator
        = DotDotDotToken // from BindingElement
        | SpreadElement // AssignmentRestElement
        | SpreadAssignment // AssignmentRestProperty
        ;
```

### BindingOrAssignmentElementTarget
```ts
export type BindingOrAssignmentElementTarget 
        = BindingOrAssignmentPattern 
        | Identifier 
        | PropertyAccessExpression 
        | ElementAccessExpression 
        | OmittedExpression;

```

### ObjectBindingOrAssignmentPattern
```ts
export type ObjectBindingOrAssignmentPattern
        = ObjectBindingPattern
        | ObjectLiteralExpression // ObjectAssignmentPattern
        ;
```

### ArrayBindingOrAssignmentPattern
```ts
export type ArrayBindingOrAssignmentPattern
        = ArrayBindingPattern
        | ArrayLiteralExpression // ArrayAssignmentPattern
        ;
```

### AssignmentPattern
```ts
export type AssignmentPattern = ObjectLiteralExpression | ArrayLiteralExpression;
```

### BindingOrAssignmentPattern
```ts
export type BindingOrAssignmentPattern 
    = ObjectBindingOrAssignmentPattern 
    | ArrayBindingOrAssignmentPattern;
```

### ConditionalExpression
```ts
export interface ConditionalExpression extends Expression {
        kind: SyntaxKind.ConditionalExpression;
        condition: Expression;
        questionToken: QuestionToken;
        whenTrue: Expression;
        colonToken: ColonToken;
        whenFalse: Expression;
    }
```

### FunctionExpression
```ts
export type FunctionBody = Block;
export type ConciseBody = FunctionBody | Expression;

export interface FunctionExpression extends PrimaryExpression, FunctionLikeDeclarationBase, JSDocContainer {
    kind: SyntaxKind.FunctionExpression;
    name?: Identifier;
    body: FunctionBody;  // Required, whereas the member inherited from FunctionDeclaration is optional
}
```

### ArrowFunction
```ts
export interface ArrowFunction extends Expression, FunctionLikeDeclarationBase, JSDocContainer {
        kind: SyntaxKind.ArrowFunction;
        equalsGreaterThanToken: EqualsGreaterThanToken;
        body: ConciseBody;
        name: never;
    }
```

### TemplateExpression
```ts
export interface TemplateExpression extends PrimaryExpression {
        kind: SyntaxKind.TemplateExpression;
        head: TemplateHead;
        templateSpans: NodeArray<TemplateSpan>;
    }
```

### ParenthesizedExpression
```ts
export interface ParenthesizedExpression extends PrimaryExpression, JSDocContainer {
    kind: SyntaxKind.ParenthesizedExpression;
    expression: Expression;
}
```

### SpreadElement
```ts
export interface SpreadElement extends Expression {
    kind: SyntaxKind.SpreadElement;
    parent: ArrayLiteralExpression | CallExpression | NewExpression;
    expression: Expression;
}
```

### EntityNameExpression
```ts
export type EntityNameExpression = Identifier | PropertyAccessEntityNameExpression;
```

### Identifier
```ts
export const enum GeneratedIdentifierFlags {
    // Kinds
    None = 0,                           // Not automatically generated.
    Auto = 1,                           // Automatically generated identifier.
    Loop = 2,                           // Automatically generated identifier with a preference for '_i'.
    Unique = 3,                         // Unique name based on the 'text' property.
    Node = 4,                           // Unique name based on the node in the 'original' property.
    KindMask = 7,                       // Mask to extract the kind of identifier from its flags.

    // Flags
    ReservedInNestedScopes = 1 << 3,    // Reserve the generated name in nested scopes
    Optimistic = 1 << 4,                // First instance won't use '_#' if there's no conflict
    FileLevel = 1 << 5,                 // Use only the file identifiers list and not generated names to search for conflicts
}

export interface Identifier extends PrimaryExpression, Declaration {
    kind: SyntaxKind.Identifier;
    escapedText: __String;
    originalKeywordKind?: SyntaxKind;            
    /*@internal*/ autoGenerateFlags?: GeneratedIdentifierFlags; 
    /*@internal*/ autoGenerateId?: number;                   
    isInJSDocNamespace?: boolean;                           
    /*@internal*/ typeArguments?: NodeArray<TypeNode | TypeParameterDeclaration>; 
    /*@internal*/ jsdocDotPos?: number;         
}
```

### TransientIdentifier
```ts
export interface TransientIdentifier extends Identifier {
    resolvedSymbol: Symbol;
}
```
### GeneratedIdentifier
```ts
export interface GeneratedIdentifier extends Identifier {
    autoGenerateFlags: GeneratedIdentifierFlags;
}
```

### AccessExpression
```ts
export type AccessExpression = PropertyAccessExpression | ElementAccessExpression;
```

### PropertyAccessExpression
```ts
export interface PropertyAccessExpression extends MemberExpression, NamedDeclaration {
    kind: SyntaxKind.PropertyAccessExpression;
    expression: LeftHandSideExpression;
    questionDotToken?: QuestionDotToken;
    name: Identifier;
}
```

### PropertyAccessChain
```ts
export interface PropertyAccessChain extends PropertyAccessExpression {
    _optionalChainBrand: any;
}
```

### PropertyAccessChainRoot
```ts
export interface PropertyAccessChainRoot extends PropertyAccessChain {
    questionDotToken: QuestionDotToken;
}
```

### SuperPropertyAccessExpression
```ts
export interface SuperPropertyAccessExpression extends PropertyAccessExpression {
    expression: SuperExpression;
}
```

### PropertyAccessEntityNameExpression
```ts
export interface PropertyAccessEntityNameExpression extends PropertyAccessExpression {
    _propertyAccessExpressionLikeQualifiedNameBrand?: any;
    expression: EntityNameExpression;
}
```

### ElementAccessExpression
```ts
export interface ElementAccessExpression extends MemberExpression {
    kind: SyntaxKind.ElementAccessExpression;
    expression: LeftHandSideExpression;
    questionDotToken?: QuestionDotToken;
    argumentExpression: Expression;
}
```

###  ElementAccessChain
```ts
export interface ElementAccessChain extends ElementAccessExpression {
    _optionalChainBrand: any;
}
```

### ElementAccessChainRoot
```ts
export interface ElementAccessChainRoot extends ElementAccessChain {
    questionDotToken: QuestionDotToken;
}
```

### SuperElementAccessExpression
```ts
export interface SuperElementAccessExpression extends ElementAccessExpression {
    expression: SuperExpression;
}
```

### SuperProperty
```ts
export type SuperProperty = SuperPropertyAccessExpression | SuperElementAccessExpression;
```

### CallExpression
```ts
export interface CallExpression extends LeftHandSideExpression, Declaration {
    kind: SyntaxKind.CallExpression;
    expression: LeftHandSideExpression;
    questionDotToken?: QuestionDotToken;
    typeArguments?: NodeArray<TypeNode>;
    arguments: NodeArray<Expression>;
}
```

### CallChain 
```ts
export interface CallChain extends CallExpression {
    _optionalChainBrand: any;
}
```

### CallChainRoot
```ts
export interface CallChainRoot extends CallChain {
    questionDotToken: QuestionDotToken;
}
```

### OptionalChain
```ts
export type OptionalChain =
    | PropertyAccessChain
    | ElementAccessChain
    | CallChain
    ;
```

### OptionalChainRoot
```ts
export type OptionalChainRoot =
    | PropertyAccessChainRoot
    | ElementAccessChainRoot
    | CallChainRoot
    ;
```

### SuperCall
```ts
export interface SuperCall extends CallExpression {
    expression: SuperExpression;
}
```

### ImportCall
```ts
export interface ImportCall extends CallExpression {
    expression: ImportExpression;
}
```

### ExpressionWithTypeArguments
```ts
export interface ExpressionWithTypeArguments extends NodeWithTypeArguments {
    kind: SyntaxKind.ExpressionWithTypeArguments;
    parent: HeritageClause | JSDocAugmentsTag;
    expression: LeftHandSideExpression;
}
```

### TaggedTemplateExpression
```ts
export interface TaggedTemplateExpression extends MemberExpression {
    kind: SyntaxKind.TaggedTemplateExpression;
    tag: LeftHandSideExpression;
    typeArguments?: NodeArray<TypeNode>;
    template: TemplateLiteral;
    /*@internal*/ questionDotToken?: QuestionDotToken;
}
```

### CallLikeExpression
```ts
 export type CallLikeExpression 
    = CallExpression 
    | NewExpression 
    | TaggedTemplateExpression 
    | Decorator 
    | JsxOpeningLikeElement;
```

### AsExpression
```ts
export interface AsExpression extends Expression {
    kind: SyntaxKind.AsExpression;
    expression: Expression;
    type: TypeNode;
}
```

### TypeAssertion
```ts
export interface TypeAssertion extends UnaryExpression {
    kind: SyntaxKind.TypeAssertionExpression;
    type: TypeNode;
    expression: UnaryExpression;
}
```

### AssertionExpression
```ts
export type AssertionExpression = TypeAssertion | AsExpression;
```

### NonNullExpression
```ts
export interface NonNullExpression extends LeftHandSideExpression {
    kind: SyntaxKind.NonNullExpression;
    expression: Expression;
}
```

### MetaProperty
```ts
export interface MetaProperty extends PrimaryExpression {
    kind: SyntaxKind.MetaProperty;
    keywordToken: SyntaxKind.NewKeyword | SyntaxKind.ImportKeyword;
    name: Identifier;
}
```
### ImportMetaProperty
```ts
export interface ImportMetaProperty extends MetaProperty {
    keywordToken: SyntaxKind.ImportKeyword;
    name: Identifier & { escapedText: __String & "meta" };
}
```

### CommaListExpression
```ts
export interface CommaListExpression extends Expression {
    kind: SyntaxKind.CommaListExpression;
    elements: NodeArray<Expression>;
}
```

### SyntheticReferenceExpression
```ts
export interface SyntheticReferenceExpression extends LeftHandSideExpression {
    kind: SyntaxKind.SyntheticReferenceExpression;
    expression: Expression;
    thisArg: Expression;
}
```