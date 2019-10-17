

## 源代码
### TypeNode
```ts
export interface TypeNode extends Node {
        _typeNodeBrand: any;
 }
```

### KeywordTypeNode
### ThisTypeNode
### ImportTypeNode
### FunctionOrConstructororTypeNdoeBase
### NodeWithTypeArguments
### TypePredicateNode
```ts
export interface TypePredicateNode extends TypeNode {
        kind: SyntaxKind.TypePredicate;
        parent: SignatureDeclaration | JSDocTypeExpression;
        assertsModifier?: AssertsToken;
        parameterName: Identifier | ThisTypeNode;
        type?: TypeNode;
}
```

### TypeQueryNode
```ts
export interface TypeQueryNode extends TypeNode {
        kind: SyntaxKind.TypeQuery;
        exprName: EntityName;
}
```

### TypeLiteralNode
```ts
export interface TypeLiteralNode extends TypeNode, Declaration {
        kind: SyntaxKind.TypeLiteral;
        members: NodeArray<TypeElement>;
}
```

### ArrayTypeNode
```ts
export interface ArrayTypeNode extends TypeNode {
        kind: SyntaxKind.ArrayType;
        elementType: TypeNode;
}
```

### TupleTypeNode
```ts
export interface TupleTypeNode extends TypeNode {
        kind: SyntaxKind.TupleType;
        elementTypes: NodeArray<TypeNode>;
}
```

### OptionalTypeNode
```ts
export interface OptionalTypeNode extends TypeNode {
        kind: SyntaxKind.OptionalType;
        type: TypeNode;
}
```

### RestTypeNode
```ts
export interface RestTypeNode extends TypeNode {
        kind: SyntaxKind.RestType;
        type: TypeNode;
}
```

### UnionOrIntersectionTypeNode
```ts
export type UnionOrIntersectionTypeNode = UnionTypeNode | IntersectionTypeNode;
```
### UnionTypeNode
```ts
export interface UnionTypeNode extends TypeNode {
        kind: SyntaxKind.UnionType;
        types: NodeArray<TypeNode>;
}
```

### IntersectionTypeNode
```ts
export interface IntersectionTypeNode extends TypeNode {
        kind: SyntaxKind.IntersectionType;
        types: NodeArray<TypeNode>;
}
```

### ConditionalTypeNode
```ts
export interface ConditionalTypeNode extends TypeNode {
        kind: SyntaxKind.ConditionalType;
        checkType: TypeNode;
        extendsType: TypeNode;
        trueType: TypeNode;
        falseType: TypeNode;
}
```

### InferTypeNode
```ts
export interface InferTypeNode extends TypeNode {
        kind: SyntaxKind.InferType;
        typeParameter: TypeParameterDeclaration;
}
```

### ParenthesizedTypeNode
```ts
export interface ParenthesizedTypeNode extends TypeNode {
        kind: SyntaxKind.ParenthesizedType;
        type: TypeNode;
}
```

### TypeOperatorNode
```ts
export interface TypeOperatorNode extends TypeNode {
        kind: SyntaxKind.TypeOperator;
        operator: SyntaxKind.KeyOfKeyword | SyntaxKind.UniqueKeyword | SyntaxKind.ReadonlyKeyword;
        type: TypeNode;
}

export interface UniqueTypeOperatorNode extends TypeOperatorNode {
        operator: SyntaxKind.UniqueKeyword;
}
```

### IndexedAccessTypeNode
```ts
export interface IndexedAccessTypeNode extends TypeNode {
        kind: SyntaxKind.IndexedAccessType;
        objectType: TypeNode;
        indexType: TypeNode;
}
```

### MappedTypeNode
```ts
export interface MappedTypeNode extends TypeNode, Declaration {
        kind: SyntaxKind.MappedType;
        readonlyToken?: ReadonlyToken | PlusToken | MinusToken;
        typeParameter: TypeParameterDeclaration;
        questionToken?: QuestionToken | PlusToken | MinusToken;
        type?: TypeNode;
}
```

### LiteralTypeNode
```ts
export interface LiteralTypeNode extends TypeNode {
        kind: SyntaxKind.LiteralType;
        literal: BooleanLiteral | LiteralExpression | PrefixUnaryExpression;
}
```

