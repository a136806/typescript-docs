## 
## 源代码
### FunctionLikeDeclarationBase
 
```ts
export interface FunctionLikeDeclarationBase extends SignatureDeclarationBase {
    _functionLikeDeclarationBrand: any;

    asteriskToken?: AsteriskToken;
    questionToken?: QuestionToken;
    exclamationToken?: ExclamationToken;
    body?: Block | Expression;
    /* @internal */ endFlowNode?: FlowNode;
}
```
### FunctionLikeDeclaration

```ts
export type FunctionLikeDeclaration =
    | FunctionDeclaration
    | MethodDeclaration
    | GetAccessorDeclaration
    | SetAccessorDeclaration
    | ConstructorDeclaration
    | FunctionExpression
    | ArrowFunction;
export type FunctionLike = SignatureDeclaration;
```


### FunctionDeclaration
```ts
export interface FunctionDeclaration extends FunctionLikeDeclarationBase, DeclarationStatement {
    kind: SyntaxKind.FunctionDeclaration;
    name?: Identifier;
    body?: FunctionBody;
}
```
### MethodDeclaration
> (ClassObjectDeclaration)
### GetAccessorDeclaration
> (ClassObjectDeclaration)
### SetAccessorDeclaration
> (ClassObjectDeclaration)
### ConstructorDeclaration
> (ClassObjectDeclaration)
### FunctionExpression
```ts
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