## 类型接口
### visitNode
```ts
export function visitNode<T extends Node>(
    node: T | undefined, 
    visitor: Visitor | undefined, 
    test?: (node: Node) => boolean, 
    lift?: (node: NodeArray<Node>) => T): T;

export function visitNode<T extends Node>(
    node: T | undefined, 
    visitor: Visitor | undefined, 
    test?: (node: Node) => boolean, 
    lift?: (node: NodeArray<Node>) => T): T | undefined;

```

### visitNodes
```ts
export function visitNodes<T extends Node>(
    nodes: NodeArray<T> | undefined, 
    visitor: Visitor, 
    test?: (node: Node) => boolean, 
    start?: number, count?: number): NodeArray<T>;
export function visitNodes<T extends Node>(
    nodes: NodeArray<T> | undefined, 
    visitor: Visitor,
    test?: (node: Node) => boolean, 
    start?: number, count?: number): NodeArray<T> | undefined;
```

### visitFunctionBody
```ts
export function visitFunctionBody(
    node: FunctionBody, 
    visitor: Visitor, 
    context: TransformationContext): FunctionBody;
export function visitFunctionBody(
    node: FunctionBody | undefined, 
    visitor: Visitor, 
    context: TransformationContext): FunctionBody | undefined;
export function visitFunctionBody(
    node: ConciseBody, 
    visitor: Visitor,
     context: TransformationContext): ConciseBody;
```

### visitEachChild
```ts
export function visitEachChild<T extends Node>(
    node: T, 
    visitor: Visitor, 
    context: TransformationContext): T;
export function visitEachChild<T extends Node>(
    node: T | undefined, 
    visitor: Visitor, 
    context: TransformationContext, 
    nodesVisitor?: typeof visitNodes, 
    tokenVisitor?: Visitor): T | undefined;
```

### mergeLexicalEnvironment
```ts
export function mergeLexicalEnvironment(statements: NodeArray<Statement>, declarations: readonly Statement[] | undefined): NodeArray<Statement>;
export function mergeLexicalEnvironment(statements: Statement[], declarations: readonly Statement[] | undefined): Statement[];
```

## 实例对象
### isTypeNodeOrTypeParameterDeclaration
```ts
const isTypeNodeOrTypeParameterDeclaration = or(isTypeNode, isTypeParameterDeclaration);
```
## 功能函数
### visitNode
```ts
export function visitNode<T extends Node>(
    node: T | undefined, 
    visitor: Visitor | undefined, 
    test?: (node: Node) => boolean, 
    lift?: (node: NodeArray<Node>) => T): T | undefined {
     ...
}
```

### visitNodes
```ts
export function visitNodes<T extends Node>(nodes: NodeArray<T> | undefined, visitor: Visitor, test?: (node: Node) => boolean, start?: number, count?: number): NodeArray<T> | undefined {
    ...
}
```

### visitLexicalEnvironment
```ts
export function visitLexicalEnvironment(statements: NodeArray<Statement>, visitor: Visitor, context: TransformationContext, start?: number, ensureUseStrict?: boolean) {
    context.startLexicalEnvironment();
    statements = visitNodes(statements, visitor, isStatement, start);
    return mergeLexicalEnvironment(statements, context.endLexicalEnvironment());
}
```

### visitParameterList
```ts
export function visitParameterList(nodes: NodeArray<ParameterDeclaration> | undefined, visitor: Visitor, context: TransformationContext, nodesVisitor = visitNodes) {
    context.startLexicalEnvironment();
    const updated = nodesVisitor(nodes, visitor, isParameterDeclaration);
    context.suspendLexicalEnvironment();
    return updated;
}
```

### visitFunctionBody
```ts
export function visitFunctionBody(node: ConciseBody | undefined, visitor: Visitor, context: TransformationContext): ConciseBody | undefined {
    ...
}
```

### visitEachChild
```ts
export function visitEachChild(node: Node | undefined, visitor: Visitor, context: TransformationContext, nodesVisitor = visitNodes, tokenVisitor?: Visitor): Node | undefined {
    ...
}
```

### reduceEachChild
```ts
export function reduceEachChild<T>(node: Node | undefined, initial: T, cbNode: (memo: T, node: Node) => T, cbNodeArray?: (memo: T, nodes: NodeArray<Node>) => T): T {
    ...
}
```

### mergeLexicalEnvironment
```ts
export function mergeLexicalEnvironment(
    statements: Statement[] | NodeArray<Statement>, 
    declarations: readonly Statement[] | undefined) {
    ...
}
```

### liftToBlock
### aggregateTransformFlags


## 内部函数
### extractSingleNode
```ts
function extractSingleNode(nodes: readonly Node[]): Node | undefined {
    Debug.assert(nodes.length <= 1, "Too many nodes written to output.");
    return singleOrUndefined(nodes);
}
```

### reduceNode
### reduceNodeArray
### aggregateTransformFlagsForNode
### aggregateTransformFlagsForNodeArray
### aggregateTransformFlagsForSubtree
### aggregateTransformFlagsForChildNode