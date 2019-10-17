

## 源代码

### VariableLikeDeclaration
```ts
export type VariableLikeDeclaration =
    | VariableDeclaration
    | ParameterDeclaration
    | BindingElement
    | PropertyDeclaration
    | PropertyAssignment
    | PropertySignature
    | JsxAttribute
    | ShorthandPropertyAssignment
    | EnumMember
    | JSDocPropertyTag
    | JSDocParameterTag;
```
### VariableDeclaration
```ts
export interface VariableDeclaration extends NamedDeclaration {
    kind: SyntaxKind.VariableDeclaration;
    parent: VariableDeclarationList | CatchClause;
    name: BindingName;                    // Declared variable name
    exclamationToken?: ExclamationToken;  // Optional definite assignment assertion
    type?: TypeNode;                      // Optional type annotation
    initializer?: Expression;             // Optional initializer
}
```
### ParameterDeclaration
```ts
export interface ParameterDeclaration extends NamedDeclaration, JSDocContainer {
    kind: SyntaxKind.Parameter;
    parent: SignatureDeclaration;
    dotDotDotToken?: DotDotDotToken;    // Present on rest parameter
    name: BindingName;                  // Declared parameter name.
    questionToken?: QuestionToken;      // Present on optional parameter
    type?: TypeNode;                    // Optional type annotation
    initializer?: Expression;           // Optional initializer
}
```

### BindingElement
```ts
export interface BindingElement extends NamedDeclaration {
    kind: SyntaxKind.BindingElement;
    parent: BindingPattern;
    propertyName?: PropertyName;        // Binding property name (in object binding pattern)
    dotDotDotToken?: DotDotDotToken;    // Present on rest element (in object binding pattern)
    name: BindingName;                  // Declared binding element name
    initializer?: Expression;           // Optional initializer
}
```
### PropertyDeclaration
> (ClassObjectDeclaration)
### PropertyAssignment
> (ClassObjectDeclaration)
### PropertySignature
### JsxAttribute
### ShorthandPropertyAssignment
### EnumMember
### JSDocPropertyTag
### JSDocParameterTag;