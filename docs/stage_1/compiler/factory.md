## 功能说明
## 类型接口
### TypeOfTag
```ts
export type TypeOfTag = "undefined" | "number" | "boolean" | "string" | "symbol" | "object" | "function";

```
## 实例对象
```ts
let nextAutoGenerateId = 0;
let rawTextScanner: Scanner | undefined;
const invalidValueSentinel: object = {};
let allUnscopedEmitHelpers: ReadonlyMap<UnscopedEmitHelper> | undefined;
let SourceMapSource: new (fileName: string, text: string, skipTrivia?: (pos: number) => number) => SourceMapSource;
export const nullTransformationContext: TransformationContext = {
    enableEmitNotification: noop,
    enableSubstitution: noop,
    endLexicalEnvironment: returnUndefined,
    getCompilerOptions: notImplemented,
    getEmitHost: notImplemented,
    getEmitResolver: notImplemented,
    hoistFunctionDeclaration: noop,
    hoistVariableDeclaration: noop,
    isEmitNotificationEnabled: notImplemented,
    isSubstitutionEnabled: notImplemented,
    onEmitNode: noop,
    onSubstituteNode: notImplemented,
    readEmitHelpers: notImplemented,
    requestEmitHelper: noop,
    resumeLexicalEnvironment: noop,
    startLexicalEnvironment: noop,
    suspendLexicalEnvironment: noop,
    addDiagnostic: noop,
};
```
## 功能函数
### updateNode
```ts
export function updateNode<T extends Node>(updated: T, original: T): T {
    if (updated !== original) {
        setOriginalNode(updated, original);
        setTextRange(updated, original);
        aggregateTransformFlags(updated);
    }
    return updated;
}
```

### createNodeArray
```ts
export function createNodeArray<T extends Node>(elements?: T[], hasTrailingComma?: boolean): MutableNodeArray<T>;

export function createNodeArray<T extends Node>(elements?: readonly T[], hasTrailingComma?: boolean): NodeArray<T>;

export function createNodeArray<T extends Node>(elements?: readonly T[], hasTrailingComma?: boolean): NodeArray<T> {
    if (!elements || elements === emptyArray) {
        elements = [];
    }
    else if (isNodeArray(elements)) {
        return elements;
    }

    const array = <NodeArray<T>>elements;
    array.pos = -1;
    array.end = -1;
    array.hasTrailingComma = hasTrailingComma;
    return array;
}

```

### getSynthesizedClone
```ts
export function getSynthesizedClone<T extends Node>(node: T): T {
    ...
}
```

## Literals

### createLiteral
```ts
export function createLiteral(value: string | StringLiteral | NoSubstitutionTemplateLiteral | NumericLiteral | Identifier, isSingleQuote: boolean): StringLiteral;
export function createLiteral(value: string | StringLiteral | NoSubstitutionTemplateLiteral | NumericLiteral | Identifier): StringLiteral;
export function createLiteral(value: number | PseudoBigInt): NumericLiteral;
export function createLiteral(value: boolean): BooleanLiteral;

export function createLiteral(value: string | number | PseudoBigInt | boolean): PrimaryExpression;


export function createLiteral(value: string | number | PseudoBigInt | boolean | StringLiteral | NoSubstitutionTemplateLiteral | NumericLiteral | Identifier, isSingleQuote?: boolean): PrimaryExpression {
    if (typeof value === "number") {
        return createNumericLiteral(value + "");
    }
    // eslint-disable-next-line no-in-operator
    if (typeof value === "object" && "base10Value" in value) { // PseudoBigInt
        return createBigIntLiteral(pseudoBigIntToString(value) + "n");
    }
    if (typeof value === "boolean") {
        return value ? createTrue() : createFalse();
    }
    if (isString(value)) {
        const res = createStringLiteral(value);
        if (isSingleQuote) res.singleQuote = true;
        return res;
    }
    return createLiteralFromNode(value);
}
```

### createNumericLiteral
```ts
export function createNumericLiteral(value: string, numericLiteralFlags: TokenFlags = TokenFlags.None): NumericLiteral {
    const node = <NumericLiteral>createSynthesizedNode(SyntaxKind.NumericLiteral);
    node.text = value;
    node.numericLiteralFlags = numericLiteralFlags;
    return node;
}
```

###  createBigIntLiteral
```ts
export function createBigIntLiteral(value: string): BigIntLiteral {
    const node = <BigIntLiteral>createSynthesizedNode(SyntaxKind.BigIntLiteral);
    node.text = value;
    return node;
}
```
### createStringLiteral
```ts
export function createStringLiteral(text: string): StringLiteral {
    const node = <StringLiteral>createSynthesizedNode(SyntaxKind.StringLiteral);
    node.text = text;
    return node;
}
```
### createRegularExpressionLiteral
```ts
export function createRegularExpressionLiteral(text: string): RegularExpressionLiteral {
    const node = <RegularExpressionLiteral>createSynthesizedNode(SyntaxKind.RegularExpressionLiteral);
    node.text = text;
    return node;
}
```

## Identifiers

### createIdentifier
```ts
export function createIdentifier(text: string): Identifier;
export function createIdentifier(text: string, typeArguments: readonly (TypeNode | TypeParameterDeclaration)[] | undefined): Identifier;
 export function createIdentifier(text: string, typeArguments?: readonly (TypeNode | TypeParameterDeclaration)[]): Identifier {
    const node = <Identifier>createSynthesizedNode(SyntaxKind.Identifier);
    node.escapedText = escapeLeadingUnderscores(text);
    node.originalKeywordKind = text ? stringToToken(text) : SyntaxKind.Unknown;
    node.autoGenerateFlags = GeneratedIdentifierFlags.None;
    node.autoGenerateId = 0;
    if (typeArguments) {
        node.typeArguments = createNodeArray(typeArguments as readonly TypeNode[]);
    }
    return node;
}
```

### updateIdentifier
```ts
export function updateIdentifier(node: Identifier): Identifier;
export function updateIdentifier(node: Identifier, typeArguments: NodeArray<TypeNode | TypeParameterDeclaration> | undefined): Identifier; 

export function updateIdentifier(node: Identifier, typeArguments?: NodeArray<TypeNode | TypeParameterDeclaration> | undefined): Identifier {
    return node.typeArguments !== typeArguments
        ? updateNode(createIdentifier(idText(node), typeArguments), node)
        : node;
}
```

### createTempVariable
```ts
export function createTempVariable(recordTempVariable: ((node: Identifier) => void) | undefined): Identifier;
export function createTempVariable(recordTempVariable: ((node: Identifier) => void) | undefined, reservedInNestedScopes: boolean): GeneratedIdentifier;
export function createTempVariable(recordTempVariable: ((node: Identifier) => void) | undefined, reservedInNestedScopes?: boolean): GeneratedIdentifier {
    const name = createIdentifier("") as GeneratedIdentifier;
    name.autoGenerateFlags = GeneratedIdentifierFlags.Auto;
    name.autoGenerateId = nextAutoGenerateId;
    nextAutoGenerateId++;
    if (recordTempVariable) {
        recordTempVariable(name);
    }
    if (reservedInNestedScopes) {
        name.autoGenerateFlags |= GeneratedIdentifierFlags.ReservedInNestedScopes;
    }
    return name;
}
```

### createLoopVariable
```ts
export function createLoopVariable(): Identifier {
    const name = createIdentifier("");
    name.autoGenerateFlags = GeneratedIdentifierFlags.Loop;
    name.autoGenerateId = nextAutoGenerateId;
    nextAutoGenerateId++;
    return name;
}
```

### createUniqueName
```ts
export function createUniqueName(text: string): Identifier {
    const name = createIdentifier(text);
    name.autoGenerateFlags = GeneratedIdentifierFlags.Unique;
    name.autoGenerateId = nextAutoGenerateId;
    nextAutoGenerateId++;
    return name;
}
```

### createOptimisticUniqueName
```ts
export function createOptimisticUniqueName(text: string): GeneratedIdentifier;
export function createOptimisticUniqueName(text: string): Identifier;
export function createOptimisticUniqueName(text: string): GeneratedIdentifier {
    const name = createIdentifier(text) as GeneratedIdentifier;
    name.autoGenerateFlags = GeneratedIdentifierFlags.Unique | GeneratedIdentifierFlags.Optimistic;
    name.autoGenerateId = nextAutoGenerateId;
    nextAutoGenerateId++;
    return name;
}
```

### createFileLevelUniqueName
```ts
export function createFileLevelUniqueName(text: string): Identifier {
    const name = createOptimisticUniqueName(text);
    name.autoGenerateFlags |= GeneratedIdentifierFlags.FileLevel;
    return name;
}
```

### getGeneratedNameForNode
```ts
export function getGeneratedNameForNode(node: Node | undefined): Identifier;
export function getGeneratedNameForNode(node: Node | undefined, flags: GeneratedIdentifierFlags): Identifier;
export function getGeneratedNameForNode(node: Node | undefined, flags?: GeneratedIdentifierFlags): Identifier {
    const name = createIdentifier(node && isIdentifier(node) ? idText(node) : "");
    name.autoGenerateFlags = GeneratedIdentifierFlags.Node | flags!;
    name.autoGenerateId = nextAutoGenerateId;
    name.original = node;
    nextAutoGenerateId++;
    return name;
}
```

## Punctuation

### createToken
```ts
export function createToken<TKind extends SyntaxKind>(token: TKind) {
    return <Token<TKind>>createSynthesizedNode(token);
}
```

## Reserved words

### createSuper
```ts
export function createSuper() {
    return <SuperExpression>createSynthesizedNode(SyntaxKind.SuperKeyword);
}
```

### createThis
```ts
export function createThis() {
    return <ThisExpression & Token<SyntaxKind.ThisKeyword>>createSynthesizedNode(SyntaxKind.ThisKeyword);
}
```

### createNull
```ts
export function createNull() {
    return <NullLiteral & Token<SyntaxKind.NullKeyword>>createSynthesizedNode(SyntaxKind.NullKeyword);
}
```

### createTrue
```ts
export function createTrue() {
    return <BooleanLiteral & Token<SyntaxKind.TrueKeyword>>createSynthesizedNode(SyntaxKind.TrueKeyword);
}
```
### createFalse
```ts
export function createFalse() {
    return <BooleanLiteral & Token<SyntaxKind.FalseKeyword>>createSynthesizedNode(SyntaxKind.FalseKeyword);
}
```

## Modifiers

### createModifier
```ts
export function createModifier<T extends Modifier["kind"]>(kind: T): Token<T> {
    return createToken(kind);
}
```

### createModifiersFromModifierFlags
> 创建Modefier数组
```ts
export function createModifiersFromModifierFlags(flags: ModifierFlags) {
    const result: Modifier[] = [];
    if (flags & ModifierFlags.Export) { result.push(createModifier(SyntaxKind.ExportKeyword)); }
    if (flags & ModifierFlags.Ambient) { result.push(createModifier(SyntaxKind.DeclareKeyword)); }
    if (flags & ModifierFlags.Default) { result.push(createModifier(SyntaxKind.DefaultKeyword)); }
    if (flags & ModifierFlags.Const) { result.push(createModifier(SyntaxKind.ConstKeyword)); }
    if (flags & ModifierFlags.Public) { result.push(createModifier(SyntaxKind.PublicKeyword)); }
    if (flags & ModifierFlags.Private) { result.push(createModifier(SyntaxKind.PrivateKeyword)); }
    if (flags & ModifierFlags.Protected) { result.push(createModifier(SyntaxKind.ProtectedKeyword)); }
    if (flags & ModifierFlags.Abstract) { result.push(createModifier(SyntaxKind.AbstractKeyword)); }
    if (flags & ModifierFlags.Static) { result.push(createModifier(SyntaxKind.StaticKeyword)); }
    if (flags & ModifierFlags.Readonly) { result.push(createModifier(SyntaxKind.ReadonlyKeyword)); }
    if (flags & ModifierFlags.Async) { result.push(createModifier(SyntaxKind.AsyncKeyword)); }
    return result;
}
```

## Names

### createQualifiedName
```ts
export function createQualifiedName(left: EntityName, right: string | Identifier) {
    const node = <QualifiedName>createSynthesizedNode(SyntaxKind.QualifiedName);
    node.left = left;
    node.right = asName(right);
    return node;
}
```

### updateQualifiedName
```ts
export function updateQualifiedName(node: QualifiedName, left: EntityName, right: Identifier) {
    return node.left !== left
        || node.right !== right
        ? updateNode(createQualifiedName(left, right), node)
        : node;
}
```

### createComputedPropertyName
```ts
export function createComputedPropertyName(expression: Expression) {
    const node = <ComputedPropertyName>createSynthesizedNode(SyntaxKind.ComputedPropertyName);
    node.expression = parenthesizeForComputedName(expression);
    return node;
}
```

### updateComputedPropertyName
```ts
export function updateComputedPropertyName(node: ComputedPropertyName, expression: Expression) {
    return node.expression !== expression
        ? updateNode(createComputedPropertyName(expression), node)
        : node;
}
```

## Signature elements

### createTypeParameterDeclaration
```ts
export function createTypeParameterDeclaration(name: string | Identifier, constraint?: TypeNode, defaultType?: TypeNode) {
    const node = createSynthesizedNode(SyntaxKind.TypeParameter) as TypeParameterDeclaration;
    node.name = asName(name);
    node.constraint = constraint;
    node.default = defaultType;
    return node;
}
```

### updateTypeParameterDeclaration
```ts
xport function updateTypeParameterDeclaration(node: TypeParameterDeclaration, name: Identifier, constraint: TypeNode | undefined, defaultType: TypeNode | undefined) {
    return node.name !== name
        || node.constraint !== constraint
        || node.default !== defaultType
        ? updateNode(createTypeParameterDeclaration(name, constraint, defaultType), node)
        : node;
}
```

### export function createParameter(
```ts
export function createParameter(
    decorators: readonly Decorator[] | undefined,
    modifiers: readonly Modifier[] | undefined,
    dotDotDotToken: DotDotDotToken | undefined,
    name: string | BindingName,
    questionToken?: QuestionToken,
    type?: TypeNode,
    initializer?: Expression) {
        
    const node = <ParameterDeclaration>createSynthesizedNode(SyntaxKind.Parameter);
    node.decorators = asNodeArray(decorators);
    node.modifiers = asNodeArray(modifiers);
    node.dotDotDotToken = dotDotDotToken;
    node.name = asName(name);
    node.questionToken = questionToken;
    node.type = type;
    node.initializer = initializer ? parenthesizeExpressionForList(initializer) : undefined;
    return node;
}
```

### updateParameter
```ts
export function updateParameter(
    node: ParameterDeclaration,
    decorators: readonly Decorator[] | undefined,
    modifiers: readonly Modifier[] | undefined,
    dotDotDotToken: DotDotDotToken | undefined,
    name: string | BindingName,
    questionToken: QuestionToken | undefined,
    type: TypeNode | undefined,
    initializer: Expression | undefined) {
    return node.decorators !== decorators
        || node.modifiers !== modifiers
        || node.dotDotDotToken !== dotDotDotToken
        || node.name !== name
        || node.questionToken !== questionToken
        || node.type !== type
        || node.initializer !== initializer
        ? updateNode(createParameter(decorators, modifiers, dotDotDotToken, name, questionToken, type, initializer), node)
        : node;
}
```

### createDecorator
```ts
export function createDecorator(expression: Expression) {
    const node = <Decorator>createSynthesizedNode(SyntaxKind.Decorator);
    node.expression = parenthesizeForAccess(expression);
    return node;
}
```

### updateDecorator
```ts
export function updateDecorator(node: Decorator, expression: Expression) {
    return node.expression !== expression
        ? updateNode(createDecorator(expression), node)
        : node;
}
```

## Type Elements

### createPropertySignature
```ts
export function createPropertySignature(
    modifiers: readonly Modifier[] | undefined,
    name: PropertyName | string,
    questionToken: QuestionToken | undefined,
    type: TypeNode | undefined,
    initializer: Expression | undefined): PropertySignature {
    const node = createSynthesizedNode(SyntaxKind.PropertySignature) as PropertySignature;
    node.modifiers = asNodeArray(modifiers);
    node.name = asName(name);
    node.questionToken = questionToken;
    node.type = type;
    node.initializer = initializer;
    return node;
}
```

### updatePropertySignature
```ts
export function updatePropertySignature(
    node: PropertySignature,
    modifiers: readonly Modifier[] | undefined,
    name: PropertyName,
    questionToken: QuestionToken | undefined,
    type: TypeNode | undefined,
    initializer: Expression | undefined) {
    return node.modifiers !== modifiers
        || node.name !== name
        || node.questionToken !== questionToken
        || node.type !== type
        || node.initializer !== initializer
        ? updateNode(createPropertySignature(modifiers, name, questionToken, type, initializer), node)
        : node;
}
```

### createProperty
```ts
export function createProperty(
    decorators: readonly Decorator[] | undefined,
    modifiers: readonly Modifier[] | undefined,
    name: string | PropertyName,
    questionOrExclamationToken: QuestionToken | ExclamationToken | undefined,
    type: TypeNode | undefined,
    initializer: Expression | undefined) {

    const node = <PropertyDeclaration>createSynthesizedNode(SyntaxKind.PropertyDeclaration);
    node.decorators = asNodeArray(decorators);
    node.modifiers = asNodeArray(modifiers);
    node.name = asName(name);
    node.questionToken = questionOrExclamationToken !== undefined && questionOrExclamationToken.kind === SyntaxKind.QuestionToken ? questionOrExclamationToken : undefined;
    node.exclamationToken = questionOrExclamationToken !== undefined && questionOrExclamationToken.kind === SyntaxKind.ExclamationToken ? questionOrExclamationToken : undefined;
    node.type = type;
    node.initializer = initializer;
    return node;
}
```

### updateProperty
```ts
export function updateProperty(
    node: PropertyDeclaration,
    decorators: readonly Decorator[] | undefined,
    modifiers: readonly Modifier[] | undefined,
    name: string | PropertyName,
    questionOrExclamationToken: QuestionToken | ExclamationToken | undefined,
    type: TypeNode | undefined,
    initializer: Expression | undefined) {

    return node.decorators !== decorators
        || node.modifiers !== modifiers
        || node.name !== name
        || node.questionToken !== (questionOrExclamationToken !== undefined && questionOrExclamationToken.kind === SyntaxKind.QuestionToken ? questionOrExclamationToken : undefined)
        || node.exclamationToken !== (questionOrExclamationToken !== undefined && questionOrExclamationToken.kind === SyntaxKind.ExclamationToken ? questionOrExclamationToken : undefined)
        || node.type !== type
        || node.initializer !== initializer
        ? updateNode(createProperty(decorators, modifiers, name, questionOrExclamationToken, type, initializer), node)
        : node;
}
```

### createMethodSignature
```ts
export function createMethodSignature(
    typeParameters: readonly TypeParameterDeclaration[] | undefined,
    parameters: readonly ParameterDeclaration[],
    type: TypeNode | undefined,
    name: string | PropertyName,
    questionToken: QuestionToken | undefined) {
    const node = createSignatureDeclaration(SyntaxKind.MethodSignature, typeParameters, parameters, type) as MethodSignature;
    node.name = asName(name);
    node.questionToken = questionToken;
    return node;
}
```

### updateMethodSignature
```ts
export function updateMethodSignature(node: MethodSignature, typeParameters: NodeArray<TypeParameterDeclaration> | undefined, parameters: NodeArray<ParameterDeclaration>, type: TypeNode | undefined, name: PropertyName, questionToken: QuestionToken | undefined) {
    return node.typeParameters !== typeParameters
        || node.parameters !== parameters
        || node.type !== type
        || node.name !== name
        || node.questionToken !== questionToken
        ? updateNode(createMethodSignature(typeParameters, parameters, type, name, questionToken), node)
        : node;
}
```

### createMethod
```ts
export function createMethod(
    decorators: readonly Decorator[] | undefined,
    modifiers: readonly Modifier[] | undefined,
    asteriskToken: AsteriskToken | undefined,
    name: string | PropertyName,
    questionToken: QuestionToken | undefined,
    typeParameters: readonly TypeParameterDeclaration[] | undefined,
    parameters: readonly ParameterDeclaration[],
    type: TypeNode | undefined,
    body: Block | undefined) {
    const node = <MethodDeclaration>createSynthesizedNode(SyntaxKind.MethodDeclaration);
    node.decorators = asNodeArray(decorators);
    node.modifiers = asNodeArray(modifiers);
    node.asteriskToken = asteriskToken;
    node.name = asName(name);
    node.questionToken = questionToken;
    node.typeParameters = asNodeArray(typeParameters);
    node.parameters = createNodeArray(parameters);
    node.type = type;
    node.body = body;
    return node;
}
```

### updateMethod 
```ts
export function updateMethod(
    node: MethodDeclaration,
    decorators: readonly Decorator[] | undefined,
    modifiers: readonly Modifier[] | undefined,
    asteriskToken: AsteriskToken | undefined,
    name: PropertyName,
    questionToken: QuestionToken | undefined,
    typeParameters: readonly TypeParameterDeclaration[] | undefined,
    parameters: readonly ParameterDeclaration[],
    type: TypeNode | undefined,
    body: Block | undefined) {
    return node.decorators !== decorators
        || node.modifiers !== modifiers
        || node.asteriskToken !== asteriskToken
        || node.name !== name
        || node.questionToken !== questionToken
        || node.typeParameters !== typeParameters
        || node.parameters !== parameters
        || node.type !== type
        || node.body !== body
        ? updateNode(createMethod(decorators, modifiers, asteriskToken, name, questionToken, typeParameters, parameters, type, body), node)
        : node;
}
```

### createConstructor
```ts
export function createConstructor(decorators: readonly Decorator[] | undefined, modifiers: readonly Modifier[] | undefined, parameters: readonly ParameterDeclaration[], body: Block | undefined) {
    const node = <ConstructorDeclaration>createSynthesizedNode(SyntaxKind.Constructor);
    node.decorators = asNodeArray(decorators);
    node.modifiers = asNodeArray(modifiers);
    node.typeParameters = undefined;
    node.parameters = createNodeArray(parameters);
    node.type = undefined;
    node.body = body;
    return node;
}
```

### updateConstructor
```ts
export function updateConstructor(
    node: ConstructorDeclaration,
    decorators: readonly Decorator[] | undefined,
    modifiers: readonly Modifier[] | undefined,
    parameters: readonly ParameterDeclaration[],
    body: Block | undefined) {
    return node.decorators !== decorators
        || node.modifiers !== modifiers
        || node.parameters !== parameters
        || node.body !== body
        ? updateNode(createConstructor(decorators, modifiers, parameters, body), node)
        : node;
}
```

### createGetAccessor
```ts
export function createGetAccessor(
    decorators: readonly Decorator[] | undefined,
    modifiers: readonly Modifier[] | undefined,
    name: string | PropertyName,
    parameters: readonly ParameterDeclaration[],
    type: TypeNode | undefined,
    body: Block | undefined) {
    const node = <GetAccessorDeclaration>createSynthesizedNode(SyntaxKind.GetAccessor);
    node.decorators = asNodeArray(decorators);
    node.modifiers = asNodeArray(modifiers);
    node.name = asName(name);
    node.typeParameters = undefined;
    node.parameters = createNodeArray(parameters);
    node.type = type;
    node.body = body;
    return node;
}
```

### updateGetAccessor
```ts
export function updateGetAccessor(
    node: GetAccessorDeclaration,
    decorators: readonly Decorator[] | undefined,
    modifiers: readonly Modifier[] | undefined,
    name: PropertyName,
    parameters: readonly ParameterDeclaration[],
    type: TypeNode | undefined,
    body: Block | undefined) {
    return node.decorators !== decorators
        || node.modifiers !== modifiers
        || node.name !== name
        || node.parameters !== parameters
        || node.type !== type
        || node.body !== body
        ? updateNode(createGetAccessor(decorators, modifiers, name, parameters, type, body), node)
        : node;
}
```

### createSetAccessor
```ts
export function createSetAccessor(
    decorators: readonly Decorator[] | undefined,
    modifiers: readonly Modifier[] | undefined,
    name: string | PropertyName,
    parameters: readonly ParameterDeclaration[],
    body: Block | undefined) {
    const node = <SetAccessorDeclaration>createSynthesizedNode(SyntaxKind.SetAccessor);
    node.decorators = asNodeArray(decorators);
    node.modifiers = asNodeArray(modifiers);
    node.name = asName(name);
    node.typeParameters = undefined;
    node.parameters = createNodeArray(parameters);
    node.body = body;
    return node;
}
```

### updateSetAccessor
```ts
export function updateSetAccessor(
    node: SetAccessorDeclaration,
    decorators: readonly Decorator[] | undefined,
    modifiers: readonly Modifier[] | undefined,
    name: PropertyName,
    parameters: readonly ParameterDeclaration[],
    body: Block | undefined) {
    return node.decorators !== decorators
        || node.modifiers !== modifiers
        || node.name !== name
        || node.parameters !== parameters
        || node.body !== body
        ? updateNode(createSetAccessor(decorators, modifiers, name, parameters, body), node)
        : node;
}
```

### createCallSignature
```ts
export function createCallSignature(typeParameters: readonly TypeParameterDeclaration[] | undefined, parameters: readonly ParameterDeclaration[], type: TypeNode | undefined) {
    return createSignatureDeclaration(SyntaxKind.CallSignature, typeParameters, parameters, type) as CallSignatureDeclaration;
}
```

### updateCallSignature
```ts
export function updateCallSignature(node: CallSignatureDeclaration, typeParameters: NodeArray<TypeParameterDeclaration> | undefined, parameters: NodeArray<ParameterDeclaration>, type: TypeNode | undefined) {
    return updateSignatureDeclaration(node, typeParameters, parameters, type);
}
```

### createConstructSignature
```ts
export function createConstructSignature(typeParameters: readonly TypeParameterDeclaration[] | undefined, parameters: readonly ParameterDeclaration[], type: TypeNode | undefined) {
    return createSignatureDeclaration(SyntaxKind.ConstructSignature, typeParameters, parameters, type) as ConstructSignatureDeclaration;
}
```

### updateConstructSignature
```ts
export function updateConstructSignature(node: ConstructSignatureDeclaration, typeParameters: NodeArray<TypeParameterDeclaration> | undefined, parameters: NodeArray<ParameterDeclaration>, type: TypeNode | undefined) {
    return updateSignatureDeclaration(node, typeParameters, parameters, type);
}
```

### createIndexSignature
```ts
export function createIndexSignature(
    decorators: readonly Decorator[] | undefined,
    modifiers: readonly Modifier[] | undefined,
    parameters: readonly ParameterDeclaration[],
    type: TypeNode): IndexSignatureDeclaration {
    const node = createSynthesizedNode(SyntaxKind.IndexSignature) as IndexSignatureDeclaration;
    node.decorators = asNodeArray(decorators);
    node.modifiers = asNodeArray(modifiers);
    node.parameters = createNodeArray(parameters);
    node.type = type;
    return node;
}
```

### updateIndexSignature
```ts
export function updateIndexSignature(
    node: IndexSignatureDeclaration,
    decorators: readonly Decorator[] | undefined,
    modifiers: readonly Modifier[] | undefined,
    parameters: readonly ParameterDeclaration[],
    type: TypeNode) {
    return node.parameters !== parameters
        || node.type !== type
        || node.decorators !== decorators
        || node.modifiers !== modifiers
        ? updateNode(createIndexSignature(decorators, modifiers, parameters, type), node)
        : node;
}
```

### createSignatureDeclaration
```ts
export function createSignatureDeclaration(kind: SyntaxKind, typeParameters: readonly TypeParameterDeclaration[] | undefined, parameters: readonly ParameterDeclaration[], type: TypeNode | undefined, typeArguments?: readonly TypeNode[] | undefined) {
    const node = createSynthesizedNode(kind) as SignatureDeclaration;
    node.typeParameters = asNodeArray(typeParameters);
    node.parameters = asNodeArray(parameters);
    node.type = type;
    node.typeArguments = asNodeArray(typeArguments);
    return node;
}
```



### createObjectDefinePropertyCall
```ts
export function createObjectDefinePropertyCall(target: Expression, propertyName: string | Expression, attributes: Expression) {
    return createGlobalMethodCall("Object", "defineProperty", [target, asExpression(propertyName), attributes]);
}
```

### createPropertyDescriptor
```ts
export function createPropertyDescriptor(attributes: PropertyDescriptorAttributes, singleLine?: boolean) {
    const properties: PropertyAssignment[] = [];
    tryAddPropertyAssignment(properties, "enumerable", asExpression(attributes.enumerable));
    tryAddPropertyAssignment(properties, "configurable", asExpression(attributes.configurable));

    let isData = tryAddPropertyAssignment(properties, "writable", asExpression(attributes.writable));
    isData = tryAddPropertyAssignment(properties, "value", attributes.value) || isData;

    let isAccessor = tryAddPropertyAssignment(properties, "get", attributes.get);
    isAccessor = tryAddPropertyAssignment(properties, "set", attributes.set) || isAccessor;

    Debug.assert(!(isData && isAccessor), "A PropertyDescriptor may not be both an accessor descriptor and a data descriptor.");
    return createObjectLiteral(properties, !singleLine);
}
```

## Types
### createKeywordTypeNode
```ts
export function createKeywordTypeNode(kind: KeywordTypeNode["kind"]) {
    return <KeywordTypeNode>createSynthesizedNode(kind);
}
```

### createTypePredicateNode
```ts
export function createTypePredicateNode(parameterName: Identifier | ThisTypeNode | string, type: TypeNode) {
    return createTypePredicateNodeWithModifier(/*assertsModifier*/ undefined, parameterName, type);
}
```

### createTypePredicateNodeWithModifier
```ts
export function createTypePredicateNodeWithModifier(assertsModifier: AssertsToken | undefined, parameterName: Identifier | ThisTypeNode | string, type: TypeNode | undefined) {
    const node = createSynthesizedNode(SyntaxKind.TypePredicate) as TypePredicateNode;
    node.assertsModifier = assertsModifier;
    node.parameterName = asName(parameterName);
    node.type = type;
    return node;
}

```

### updateTypePredicateNode
```ts
export function updateTypePredicateNode(node: TypePredicateNode, parameterName: Identifier | ThisTypeNode, type: TypeNode) {
    return updateTypePredicateNodeWithModifier(node, node.assertsModifier, parameterName, type);
}
```

### updateTypePredicateNodeWithModifier
```ts
export function updateTypePredicateNodeWithModifier(node: TypePredicateNode, assertsModifier: AssertsToken | undefined, parameterName: Identifier | ThisTypeNode, type: TypeNode | undefined) {
    return node.assertsModifier !== assertsModifier
        || node.parameterName !== parameterName
        || node.type !== type
        ? updateNode(createTypePredicateNodeWithModifier(assertsModifier, parameterName, type), node)
        : node;
}
```

### createTypeReferenceNode
```ts
export function createTypeReferenceNode(typeName: string | EntityName, typeArguments: readonly TypeNode[] | undefined) {
    const node = createSynthesizedNode(SyntaxKind.TypeReference) as TypeReferenceNode;
    node.typeName = asName(typeName);
    node.typeArguments = typeArguments && parenthesizeTypeParameters(typeArguments);
    return node;
}
```


### updateTypeReferenceNode
```ts
export function updateTypeReferenceNode(node: TypeReferenceNode, typeName: EntityName, typeArguments: NodeArray<TypeNode> | undefined) {
    return node.typeName !== typeName
        || node.typeArguments !== typeArguments
        ? updateNode(createTypeReferenceNode(typeName, typeArguments), node)
        : node;
}
```

### createFunctionTypeNode
```ts
export function createFunctionTypeNode(typeParameters: readonly TypeParameterDeclaration[] | undefined, parameters: readonly ParameterDeclaration[], type: TypeNode | undefined) {
    return createSignatureDeclaration(SyntaxKind.FunctionType, typeParameters, parameters, type) as FunctionTypeNode;
}
```

### updateFunctionTypeNode
```ts
export function updateFunctionTypeNode(node: FunctionTypeNode, typeParameters: NodeArray<TypeParameterDeclaration> | undefined, parameters: NodeArray<ParameterDeclaration>, type: TypeNode | undefined) {
    return updateSignatureDeclaration(node, typeParameters, parameters, type);
}
```

### createConstructorTypeNode
```ts
export function createConstructorTypeNode(typeParameters: readonly TypeParameterDeclaration[] | undefined, parameters: readonly ParameterDeclaration[], type: TypeNode | undefined) {
    return createSignatureDeclaration(SyntaxKind.ConstructorType, typeParameters, parameters, type) as ConstructorTypeNode;
}
```

### updateConstructorTypeNode
```ts
export function updateConstructorTypeNode(node: ConstructorTypeNode, typeParameters: NodeArray<TypeParameterDeclaration> | undefined, parameters: NodeArray<ParameterDeclaration>, type: TypeNode | undefined) {
    return updateSignatureDeclaration(node, typeParameters, parameters, type);
}
```

### createTypeQueryNode
```ts
export function createTypeQueryNode(exprName: EntityName) {
    const node = createSynthesizedNode(SyntaxKind.TypeQuery) as TypeQueryNode;
    node.exprName = exprName;
    return node;
}
```

### updateTypeQueryNode
```ts
export function updateTypeQueryNode(node: TypeQueryNode, exprName: EntityName) {
    return node.exprName !== exprName
        ? updateNode(createTypeQueryNode(exprName), node)
        : node;
}
```

### createTypeLiteralNode
```ts
export function createTypeLiteralNode(members: readonly TypeElement[] | undefined) {
    const node = createSynthesizedNode(SyntaxKind.TypeLiteral) as TypeLiteralNode;
    node.members = createNodeArray(members);
    return node;
}
```

### updateTypeLiteralNode
```ts
export function updateTypeLiteralNode(node: TypeLiteralNode, members: NodeArray<TypeElement>) {
    return node.members !== members
        ? updateNode(createTypeLiteralNode(members), node)
        : node;
}
```
### createArrayTypeNode
```ts
export function createArrayTypeNode(elementType: TypeNode) {
    const node = createSynthesizedNode(SyntaxKind.ArrayType) as ArrayTypeNode;
    node.elementType = parenthesizeArrayTypeMember(elementType);
    return node;
}
```

### updateArrayTypeNode
```ts
export function updateArrayTypeNode(node: ArrayTypeNode, elementType: TypeNode): ArrayTypeNode {
    return node.elementType !== elementType
        ? updateNode(createArrayTypeNode(elementType), node)
        : node;
}
```

### createTupleTypeNode
### updateTupleTypeNode
### createOptionalTypeNode
### createRestTypeNode
### updateRestTypeNode
### createUnionTypeNode
### updateUnionTypeNode
### createIntersectionTypeNode
### updateIntersectionTypeNode
### createUnionOrIntersectionTypeNode
### updateUnionOrIntersectionTypeNode
### createConditionalTypeNode
### updateConditionalTypeNode
### createInferTypeNode
### updateInferTypeNode
### createImportTypeNode
### updateImportTypeNode
### createParenthesizedType
### updateParenthesizedType
### createThisTypeNode
### createTypeOperatorNode
### updateTypeOperatorNode
### createIndexedAccessTypeNode
### updateIndexedAccessTypeNode
### createMappedTypeNode
### updateMappedTypeNode
### createLiteralTypeNode
### updateLiteralTypeNode

## Binding Patterns

### createObjectBindingPattern
### updateObjectBindingPattern
### createArrayBindingPattern
### updateArrayBindingPattern
### createBindingElement
### updateBindingElement

## Expression
### createArrayLiteral
### updateArrayLiteral
### createObjectLiteral
### updateObjectLiteral
### createPropertyAccess
### updatePropertyAccess
### createPropertyAccessChain
### updatePropertyAccessChain
### createElementAccess
### updateElementAccess
### createElementAccessChain
### updateElementAccessChain
### createCall
### updateCall
### createCallChain
### updateCallChain
### createNew
### updateNew
### createTaggedTemplate
### updateTaggedTemplate
### createTypeAssertion
### updateTypeAssertion
### createParen
### updateParen
### createFunctionExpression
### updateFunctionExpression
### createArrowFunction
### updateArrowFunction
### createDelete
### updateDelete
### createTypeOf
### updateTypeOf
### createVoid
### updateVoid
### createAwait
### updateAwait
### createPrefix
### updatePrefix
### createPostfix
### updatePostfix
### createBinary
### updateBinary
### createConditional
### updateConditional
### createTemplateExpression
### updateTemplateExpression
### createDelete
### createDelete
### createDelete
### createDelete
### createDelete
### createDelete
### createDelete
### createDelete
### createDelete
### createDelete
### createTemplateHead
### createTemplateMiddle
### createTemplateTail
### createNoSubstitutionTemplateLiteral
### createYield
### updateYield
### createSpread
### updateSpread
### createClassExpression
### updateClassExpression
### createOmittedExpression
### createExpressionWithTypeArguments
### updateExpressionWithTypeArguments
### createAsExpression
### updateAsExpression
### createNonNullExpression
### updateNonNullExpression
### createMetaProperty
### updateMetaProperty

## Misc
### createTemplateSpan
### updateTemplateSpan
### createSemicolonClassElement

## Statement
### createBlock
### updateBlock
### createVariableStatement
### updateVariableStatement
### createEmptyStatement
### createExpressionStatement
### updateExpressionStatement
### createIf
### updateIf
### createDo
### updateDo
### createWhile
### updateWhile
### createFor
### updateFor
### createForIn
### updateForIn
### createForOf
### updateForOf
### createContinue
### updateContinue
### createBreak
### updateBreak
### createReturn
### updateReturn 
### createWith
### updateWith
### createSwitch
### updateSwitch
### createLabel
### updateLabel
### createThrow
### updateThrow
### createTry
### updateTry
### createDebuggerStatement 

## Declaration

### updateVariableDeclaration
### createVariableDeclarationList
### updateVariableDeclarationList
### createFunctionDeclaration
### updateFunctionDeclaration
### createClassDeclaration
### updateClassDeclaration
### createInterfaceDeclaration
### updateInterfaceDeclaration
### createTypeAliasDeclaration
### updateTypeAliasDeclaration
### createEnumDeclaration
### updateEnumDeclaration
### createModuleDeclaration
### updateModuleDeclaration
### createModuleBlock
### updateModuleBlock
### createCaseBlock
### updateCaseBlock
### createNamespaceExportDeclaration
### updateNamespaceExportDeclaration
### createImportEqualsDeclaration
### updateImportEqualsDeclaration
### createImportDeclaration
### updateImportDeclaration
### createImportClause
### updateImportClause
### createNamespaceImport
### updateNamespaceImport
### createNamedImports
### updateNamedImports
### createImportSpecifier
### updateImportSpecifier 
### createExportAssignment
### updateExportAssignment
### createExportDeclaration
### updateExportDeclaration
### createEmptyExports
### createNamedExports
### updateNamedExports
### createExportSpecifier
### updateExportSpecifier

## Module references
### createExternalModuleReference
### updateExternalModuleReference

## JSDoc
## JSX
## Clauses

### createCaseClause
### updateCaseClause
### createDefaultClause
### updateDefaultClause
### createHeritageClause
### updateHeritageClause
### createCatchClause
### updateCatchClause

## Property assignments
### createPropertyAssignment
### updatePropertyAssignment
### createShorthandPropertyAssignment
### updateShorthandPropertyAssignment
### createSpreadAssignment
### updateSpreadAssignment
### createEnumMember
### updateEnumMember

## Top-level nodes
### updateSourceFileNode
### getMutableClone
### createNotEmittedStatement
### createEndOfDeclarationMarker
### createMergeDeclarationMarker
### createPartiallyEmittedExpression
### updatePartiallyEmittedExpression
### createCommaList
### updateCommaList
### createSyntheticReferenceExpression
### updateSyntheticReferenceExpression
### createBundle
### createUnparsedSourceFile
### createInputFiles
### updateBundle

## Compound nodes
### createImmediatelyInvokedFunctionExpression
### createImmediatelyInvokedArrowFunction
### createComma
### createLessThan
### createAssignment
### createStrictEquality
### createStrictInequality
### createAdd
### createSubtract
### createPostfixIncrement
### createLogicalAnd
### createLogicalOr
### createNullishCoalesce
### createLogicalNot
### createVoidZero
### createExportDefault
### createExternalModuleExport

## Utilities

### disposeEmitNodes
### getOrCreateEmitNode
### removeAllComments
### setTextRange
### setEmitFlags
### addEmitFlags
### getSourceMapRange
### setSourceMapRange
### createSourceMapSource
### getTokenSourceMapRange
### setTokenSourceMapRange
### getStartsOnNewLine
### setStartsOnNewLine
### getCommentRange 
### setCommentRange
### getSyntheticLeadingComments
### setSyntheticLeadingComments
### addSyntheticLeadingComment
### getSyntheticTrailingComments
### setSyntheticTrailingComments
### addSyntheticTrailingComment
### moveSyntheticComments
### getConstantValue
### setConstantValue
### addEmitHelper
### addEmitHelpers
### removeEmitHelper
### getEmitHelpers
### moveEmitHelpers
### compareEmitHelpers
### setOriginalNode

###  createTypeCheck
```ts
export function createTypeCheck(value: Expression, tag: TypeOfTag) {
    return tag === "undefined"
        ? createStrictEquality(value, createVoidZero())
        : createStrictEquality(createTypeOf(value), createLiteral(tag));
}
```

### createMemberAccessForPropertyName
### createFunctionCall
### createFunctionApply
### createArraySlice
### createArrayConcat
### createMathPow
### createReactNamespace
### createValuesHelper
### createReadHelper
### createSpreadHelper
### createSpreadArraysHelper
### createForOfBindingStatement
### insertLeadingStatement
### restoreEnclosingLabel


## 内部函数
### createSynthesizedNode
```ts
function createSynthesizedNode(kind: SyntaxKind): Node {
    const node = createNode(kind, -1, -1);
    node.flags |= NodeFlags.Synthesized;
    return node;
}
```

### createLiteralFromNode
```ts
function createLiteralFromNode(sourceNode: PropertyNameLiteral): StringLiteral {
    const node = createStringLiteral(getTextOfIdentifierOrLiteral(sourceNode));
    node.textSourceNode = sourceNode;
    return node;
}
```

### parenthesizeForComputedName
```ts
function parenthesizeForComputedName(expression: Expression): Expression {
    return isCommaSequence(expression)
        ? createParen(expression)
        : expression;
}
```

### createMethodCall
```ts
function createMethodCall(object: Expression, methodName: string | Identifier, argumentsList: readonly Expression[]) {
    return createCall(
        createPropertyAccess(object, asName(methodName)),
        /*typeArguments*/ undefined,
        argumentsList
    );
}
```

### createGlobalMethodCall
```ts
function createGlobalMethodCall(globalObjectName: string, methodName: string, argumentsList: readonly Expression[]) {
    return createMethodCall(createIdentifier(globalObjectName), methodName, argumentsList);
}
```

### tryAddPropertyAssignment
```ts
function tryAddPropertyAssignment(properties: Push<PropertyAssignment>, propertyName: string, expression: Expression | undefined) {
    if (expression) {
        properties.push(createPropertyAssignment(propertyName, expression));
        return true;
    }
    return false;
}
```

### updateSignatureDeclaration
```ts
function updateSignatureDeclaration<T extends SignatureDeclaration>(node: T, typeParameters: NodeArray<TypeParameterDeclaration> | undefined, parameters: NodeArray<ParameterDeclaration>, type: TypeNode | undefined): T {
    return node.typeParameters !== typeParameters
        || node.parameters !== parameters
        || node.type !== type
        ? updateNode(<T>createSignatureDeclaration(node.kind, typeParameters, parameters, type), node)
        : node;
}
```

### getCookedText
### createTemplateLiteralLikeNode
### flattenCommaElements
### getAllUnscopedEmitHelpers
### createUnparsedSource
### parseUnparsedSourceFile
### parseOldFileOfCurrentEmit
### mapBundleFileSectionKindToSyntaxKind
### createUnparsedNode
### createUnparsedSyntheticReference
### asName
### asExpression
### asNodeArray
### asToken
### asEmbeddedStatement
### mergeEmitNode
### mergeTokenSourceMapRanges