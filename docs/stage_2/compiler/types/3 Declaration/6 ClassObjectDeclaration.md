## 功能说明

## 源代码

### ClassLikeDeclarationBase
```ts
export interface ClassLikeDeclarationBase extends NamedDeclaration, JSDocContainer {
    kind: SyntaxKind.ClassDeclaration | SyntaxKind.ClassExpression;
    name?: Identifier;
    typeParameters?: NodeArray<TypeParameterDeclaration>;
    heritageClauses?: NodeArray<HeritageClause>;
    members: NodeArray<ClassElement>;
}
```

### ClassDeclaration
```ts
export interface ClassDeclaration extends ClassLikeDeclarationBase, DeclarationStatement {
    kind: SyntaxKind.ClassDeclaration;
    /** May be undefined in `export default class { ... }`. */
    name?: Identifier;
}
```
### ClassExpression
```ts
export interface ClassExpression extends ClassLikeDeclarationBase, PrimaryExpression {
    kind: SyntaxKind.ClassExpression;
}
```
### ClassLikeDeclaration
```ts
export type ClassLikeDeclaration = ClassDeclaration | ClassExpression;
```

###  ClassElement
```ts
export interface ClassElement extends NamedDeclaration {
    _classElementBrand: any;
    name?: PropertyName;
}
```

### ObjectLiteralElement
```ts
export interface ObjectLiteralElement extends NamedDeclaration {
    _objectLiteralBrand: any;
    name?: PropertyName;
}
```

### ObjectLiteralElementLike
```ts
export type ObjectLiteralElementLike
    = PropertyAssignment
    | ShorthandPropertyAssignment
    | SpreadAssignment
    | MethodDeclaration
    | AccessorDeclaration
    ;
```
### SemicolonClassElement
```ts
export interface SemicolonClassElement extends ClassElement {
    kind: SyntaxKind.SemicolonClassElement;
    parent: ClassLikeDeclaration;
}
```

### PropertyDeclaration
```ts
export interface PropertyDeclaration extends ClassElement, JSDocContainer {
    kind: SyntaxKind.PropertyDeclaration;
    parent: ClassLikeDeclaration;
    name: PropertyName;
    questionToken?: QuestionToken;      // Present for use with reporting a grammar error
    exclamationToken?: ExclamationToken;
    type?: TypeNode;
    initializer?: Expression;           // Optional initializer
}
```

### MethodDeclaration
```ts
export interface MethodDeclaration extends FunctionLikeDeclarationBase, ClassElement, ObjectLiteralElement, JSDocContainer {
    kind: SyntaxKind.MethodDeclaration;
    parent: ClassLikeDeclaration | ObjectLiteralExpression;
    name: PropertyName;
    body?: FunctionBody;
}
```
### ConstructorDeclaration
```ts
export interface ConstructorDeclaration extends FunctionLikeDeclarationBase, ClassElement, JSDocContainer {
    kind: SyntaxKind.Constructor;
    parent: ClassLikeDeclaration;
    body?: FunctionBody;
    /* @internal */ returnFlowNode?: FlowNode;
}
```

### GetAccessorDeclaration
```ts
export interface GetAccessorDeclaration extends FunctionLikeDeclarationBase, ClassElement, ObjectLiteralElement, JSDocContainer {
    kind: SyntaxKind.GetAccessor;
    parent: ClassLikeDeclaration | ObjectLiteralExpression;
    name: PropertyName;
    body?: FunctionBody;
}
```
### SetAccessorDeclaration
```ts
export interface SetAccessorDeclaration extends FunctionLikeDeclarationBase, ClassElement, ObjectLiteralElement, JSDocContainer {
    kind: SyntaxKind.SetAccessor;
    parent: ClassLikeDeclaration | ObjectLiteralExpression;
    name: PropertyName;
    body?: FunctionBody;
}
```

### PropertyAssignment
```ts
export interface PropertyAssignment extends ObjectLiteralElement, JSDocContainer {
    parent: ObjectLiteralExpression;
    kind: SyntaxKind.PropertyAssignment;
    name: PropertyName;
    questionToken?: QuestionToken;
    initializer: Expression;
}
```
### ShorthandPropertyAssignment
```ts
export interface ShorthandPropertyAssignment extends ObjectLiteralElement, JSDocContainer {
    parent: ObjectLiteralExpression;
    kind: SyntaxKind.ShorthandPropertyAssignment;
    name: Identifier;
    questionToken?: QuestionToken;
    exclamationToken?: ExclamationToken;
    // used when ObjectLiteralExpression is used in ObjectAssignmentPattern
    // it is grammar error to appear in actual object initializer
    equalsToken?: Token<SyntaxKind.EqualsToken>;
    objectAssignmentInitializer?: Expression;
}
```
### SpreadAssignment
```ts
export interface SpreadAssignment extends ObjectLiteralElement, JSDocContainer {
    parent: ObjectLiteralExpression;
    kind: SyntaxKind.SpreadAssignment;
    expression: Expression;
}
```
### AccessorDeclaration
```ts
export type AccessorDeclaration = GetAccessorDeclaration | SetAccessorDeclaration;
```

### PropertyDescriptorAttributes
```ts
export interface PropertyDescriptorAttributes {
    enumerable?: boolean | Expression;
    configurable?: boolean | Expression;
    writable?: boolean | Expression;
    value?: Expression;
    get?: Expression;
    set?: Expression;
}
```