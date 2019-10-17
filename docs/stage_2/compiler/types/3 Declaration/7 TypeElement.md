

## 源代码
### TypeElement
```ts
export interface TypeElement extends NamedDeclaration {
    _typeElementBrand: any;
    name?: PropertyName;
    questionToken?: QuestionToken;
}
```

### PropertySignature
```ts
export interface PropertySignature extends TypeElement, JSDocContainer {
    kind: SyntaxKind.PropertySignature;
    name: PropertyName;                 // Declared property name
    questionToken?: QuestionToken;      // Present on optional property
    type?: TypeNode;                    // Optional type annotation
    initializer?: Expression;           // Optional initializer
}
```