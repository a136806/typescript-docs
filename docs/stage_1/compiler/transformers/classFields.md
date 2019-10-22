## 类型接口
### ClassPropertySubstitutionFlags
```ts
const enum ClassPropertySubstitutionFlags {
    ClassAliases = 1 << 0,
}
```

## 功能函数

###  transformClassFields
```ts
export function transformClassFields(context: TransformationContext) {
    const {
        hoistVariableDeclaration,
        endLexicalEnvironment,
        resumeLexicalEnvironment
    } = context;
    const resolver = context.getEmitResolver();

    //缓存
    const previousOnSubstituteNode = context.onSubstituteNode;
    context.onSubstituteNode = onSubstituteNode;

    //
    let enabledSubstitutions: ClassPropertySubstitutionFlags;
    let classAliases: Identifier[];
    let pendingExpressions: Expression[] | undefined;
    let pendingStatements: Statement[] | undefined;

    // (utilities.md)
    return chainBundle(transformSourceFile);

    // 代码转换
    function transformSourceFile(node: SourceFile) {
        const options = context.getCompilerOptions();
        if (node.isDeclarationFile
            || options.useDefineForClassFields && options.target === ScriptTarget.ESNext) {
            return node;
        }
        const visited = visitEachChild(node, visitor, context);
        addEmitHelpers(visited, context.readEmitHelpers());
        return visited;
    }

    // visitor入口
    function visitor(node: Node): VisitResult<Node> {
        if (!(node.transformFlags & TransformFlags.ContainsClassFields)) return node;

        switch (node.kind) {
            case SyntaxKind.ClassExpression:
                return visitClassExpression(node as ClassExpression);
            case SyntaxKind.ClassDeclaration:
                return visitClassDeclaration(node as ClassDeclaration);
            case SyntaxKind.VariableStatement:
                return visitVariableStatement(node as VariableStatement);
        }
        return visitEachChild(node, visitor, context);
    }

    function visitClassExpression(node: ClassExpression): Expression {
    }
    function visitClassDeclaration(node: ClassDeclaration) {
    }
    function visitVariableStatement(node: VariableStatement) {
    }


    function transformClassMembers(node: ClassDeclaration | ClassExpression, isDerivedClass: boolean) {
    }
    function transformConstructor(node: ClassDeclaration | ClassExpression, isDerivedClass: boolean) {
    }
    function transformConstructorBody(node: ClassDeclaration | ClassExpression, constructor: ConstructorDeclaration | undefined, isDerivedClass: boolean) {
    }
    function classElementVisitor(node: Node): VisitResult<Node> {
    }
    function visitPropertyDeclaration(node: PropertyDeclaration) {
    }
    function visitComputedPropertyName(name: ComputedPropertyName) {
    }

    function addPropertyStatements(statements: Statement[], properties: readonly PropertyDeclaration[], receiver: LeftHandSideExpression) {
    }
    function generateInitializedPropertyExpressions(properties: readonly PropertyDeclaration[], receiver: LeftHandSideExpression) {
    }
    function transformInitializedProperty(property: PropertyDeclaration, receiver: LeftHandSideExpression) {
    }
    function enableSubstitutionForClassAliases() {
    }

    function onSubstituteNode(hint: EmitHint, node: Node) {
    }
    function substituteExpression(node: Expression) {
    }
    function substituteExpressionIdentifier(node: Identifier): Expression {
    }
    function trySubstituteClassAlias(node: Identifier): Expression | undefined {
    }    

    function getPropertyNameExpressionIfNeeded(name: PropertyName, shouldHoist: boolean): Expression | undefined {
    }
}
```