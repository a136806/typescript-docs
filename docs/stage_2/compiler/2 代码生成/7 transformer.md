## 功能说明
> 语法转换器
> 拦截visitor对语法树节点操作
### 核心操作

#### getTransformers
> 获取所有的转换器
```ts
export function getTransformers(
    compilerOptions: CompilerOptions, 
    customTransformers?: CustomTransformers, 
    emitOnlyDtsFiles?: boolean
): EmitTransformers {
    return {
        scriptTransformers: getScriptTransformers(compilerOptions, customTransformers, emitOnlyDtsFiles),
        declarationTransformers: getDeclarationTransformers(customTransformers),
    };
}
```
#### transformNodes
> 对节点进行转换
```ts
export function transformNodes<T extends Node>(
    resolver: EmitResolver | undefined, 
    host: EmitHost | undefined, 
    options: CompilerOptions, 
    nodes: readonly T[], 
    transformers: readonly TransformerFactory<T>[], 
    allowDtsFiles: boolean
): TransformationResult<T> {

    /*  */
    const enabledSyntaxKindFeatures = new Array<SyntaxKindFeatureFlags>(SyntaxKind.Count);
    let lexicalEnvironmentVariableDeclarations: VariableDeclaration[];
    let lexicalEnvironmentFunctionDeclarations: FunctionDeclaration[];
    let lexicalEnvironmentVariableDeclarationsStack: VariableDeclaration[][] = [];
    let lexicalEnvironmentFunctionDeclarationsStack: FunctionDeclaration[][] = [];
    let lexicalEnvironmentStackOffset = 0;
    let lexicalEnvironmentSuspended = false;
    let emitHelpers: EmitHelper[] | undefined;
    let onSubstituteNode: TransformationContext["onSubstituteNode"] = noEmitSubstitution;
    let onEmitNode: TransformationContext["onEmitNode"] = noEmitNotification;
    let state = TransformationState.Uninitialized;
    const diagnostics: DiagnosticWithLocation[] = [];

    /* TransformationContext */
    const context: TransformationContext = {
        getCompilerOptions: () => options,
        getEmitResolver: () => resolver!, // TODO: GH#18217
        getEmitHost: () => host!, // TODO: GH#18217
        startLexicalEnvironment,
        suspendLexicalEnvironment,
        resumeLexicalEnvironment,
        endLexicalEnvironment,
        hoistVariableDeclaration,
        hoistFunctionDeclaration,
        requestEmitHelper,
        readEmitHelpers,
        enableSubstitution,
        enableEmitNotification,
        isSubstitutionEnabled,
        isEmitNotificationEnabled,
        get onSubstituteNode() { return onSubstituteNode; },
        set onSubstituteNode(value) {
            Debug.assert(state < TransformationState.Initialized, "Cannot modify transformation hooks after initialization has completed.");
            Debug.assert(value !== undefined, "Value must not be 'undefined'");
            onSubstituteNode = value;
        },
        get onEmitNode() { return onEmitNode; },
        set onEmitNode(value) {
            Debug.assert(state < TransformationState.Initialized, "Cannot modify transformation hooks after initialization has completed.");
            Debug.assert(value !== undefined, "Value must not be 'undefined'");
            onEmitNode = value;
        },
        addDiagnostic(diag) {
            diagnostics.push(diag);
        }
    };

    for (const node of nodes) {
        disposeEmitNodes(getSourceFileOfNode(getParseTreeNode(node)));
    }

    /* transformer chain */
    const transformersWithContext = transformers.map(t => t(context));
    const transformation = (node: T): T => {
        for (const transform of transformersWithContext) {
            node = transform(node);
        }
        return node;
    };

    state = TransformationState.Initialized;
    /* 转换处理 */
    const transformed = map(nodes, allowDtsFiles ? transformation : transformRoot);
    state = TransformationState.Completed;

    return {
        transformed,
        substituteNode,
        emitNodeWithNotification,
        dispose,
        diagnostics
    };
}
```
### 重要转换器
#### 转换器执行顺序
```ts
function getScriptTransformers(
    compilerOptions: CompilerOptions, 
    customTransformers?: CustomTransformers, emitOnlyDtsFiles?: boolean
) {
    ...
    transformers.push(transformTypeScript);
    transformers.push(transformClassFields);

    if (jsx === JsxEmit.React) {
        transformers.push(transformJsx);
    }

    if (languageVersion < ScriptTarget.ESNext) {
        transformers.push(transformESNext);
    }

    if (languageVersion < ScriptTarget.ES2019) {
        transformers.push(transformES2019);
    }

    if (languageVersion < ScriptTarget.ES2018) {
        transformers.push(transformES2018);
    }

    if (languageVersion < ScriptTarget.ES2017) {
        transformers.push(transformES2017);
    }

    if (languageVersion < ScriptTarget.ES2016) {
        transformers.push(transformES2016);
    }

    if (languageVersion < ScriptTarget.ES2015) {
        transformers.push(transformES2015);
        transformers.push(transformGenerators);
    }

    transformers.push(getModuleTransformer(moduleKind));

    // The ES5 transformer is last so that it can substitute expressions like `exports.default`
    // for ES3.
    if (languageVersion < ScriptTarget.ES5) {
        transformers.push(transformES5);
    }
    ...
}
```

### ts
```ts
export function transformTypeScript(context: TransformationContext) {
    ...
    return transformSourceFileOrBundle;
    function transformSourceFileOrBundle(node: SourceFile | Bundle) {
        if (node.kind === SyntaxKind.Bundle) {
            return transformBundle(node);
        }
        return transformSourceFile(node);
    }

    function transformBundle(node: Bundle) {
        return createBundle(node.sourceFiles.map(transformSourceFile), mapDefined(node.prepends, prepend => {
            if (prepend.kind === SyntaxKind.InputFiles) {
                return createUnparsedSourceFile(prepend, "js");
            }
            return prepend;
        }));
    }
    function transformSourceFile(node: SourceFile) {
        if (node.isDeclarationFile) {
            return node;
        }

        currentSourceFile = node;
        /* vistiSourceFile调用 */
        const visited = saveStateAndInvoke(node, visitSourceFile);
        addEmitHelpers(visited, context.readEmitHelpers());

        currentSourceFile = undefined!;
        return visited;
    }
    function visitSourceFile(node: SourceFile) {
        const alwaysStrict = getStrictOptionValue(compilerOptions, "alwaysStrict") &&
            !(isExternalModule(node) && moduleKind >= ModuleKind.ES2015) &&
            !isJsonSourceFile(node);

        return updateSourceFileNode(
            node,
            visitLexicalEnvironment(node.statements, sourceElementVisitor, context, /*start*/ 0, alwaysStrict));
    }

    function sourceElementVisitor(node: Node): VisitResult<Node> {
        return saveStateAndInvoke(node, sourceElementVisitorWorker);
    }

    function sourceElementVisitorWorker(node: Node): VisitResult<Node> {
        switch (node.kind) {
            case SyntaxKind.ImportDeclaration:
            case SyntaxKind.ImportEqualsDeclaration:
            case SyntaxKind.ExportAssignment:
            case SyntaxKind.ExportDeclaration:
                return visitEllidableStatement(<ImportDeclaration | ImportEqualsDeclaration | ExportAssignment | ExportDeclaration>node);
            default:
                return visitorWorker(node);
        }
    }

    function visitorWorker(node: Node): VisitResult<Node> {
        if (node.transformFlags & TransformFlags.ContainsTypeScript) {
            return visitTypeScript(node);
        }
        return node;
    }

    /* 重载的visitor方法 */
    function visitTypeScript(node: Node): VisitResult<Node> {
        if (isStatement(node) && hasModifier(node, ModifierFlags.Ambient)) {
            return createNotEmittedStatement(node);
        }
         switch (node.kind) {
            case SyntaxKind.ExportKeyword:
            case SyntaxKind.DefaultKeyword:
                // ES6 export and default modifiers are elided when inside a namespace.
                return currentNamespace ? undefined : node;

            case SyntaxKind.PublicKeyword:
            case SyntaxKind.PrivateKeyword:
            case SyntaxKind.ProtectedKeyword:
            case SyntaxKind.AbstractKeyword:
            case SyntaxKind.ConstKeyword:
            case SyntaxKind.DeclareKeyword:
            case SyntaxKind.ReadonlyKeyword:
            // TypeScript accessibility and readonly modifiers are elided
            // falls through
            case SyntaxKind.ArrayType:
            case SyntaxKind.TupleType:
            case SyntaxKind.OptionalType:
            case SyntaxKind.RestType:
            case SyntaxKind.TypeLiteral:
            case SyntaxKind.TypePredicate:
            case SyntaxKind.TypeParameter:
            case SyntaxKind.AnyKeyword:
            case SyntaxKind.UnknownKeyword:
            case SyntaxKind.BooleanKeyword:
            case SyntaxKind.StringKeyword:
            case SyntaxKind.NumberKeyword:
            case SyntaxKind.NeverKeyword:
            case SyntaxKind.VoidKeyword:
            case SyntaxKind.SymbolKeyword:
            case SyntaxKind.ConstructorType:
            case SyntaxKind.FunctionType:
            case SyntaxKind.TypeQuery:
            case SyntaxKind.TypeReference:
            case SyntaxKind.UnionType:
            case SyntaxKind.IntersectionType:
            case SyntaxKind.ConditionalType:
            case SyntaxKind.ParenthesizedType:
            case SyntaxKind.ThisType:
            case SyntaxKind.TypeOperator:
            case SyntaxKind.IndexedAccessType:
            case SyntaxKind.MappedType:
            case SyntaxKind.LiteralType:
                // TypeScript type nodes are elided.
                // falls through

            case SyntaxKind.IndexSignature:
                // TypeScript index signatures are elided.
                // falls through

            case SyntaxKind.Decorator:
                // TypeScript decorators are elided. They will be emitted as part of visitClassDeclaration.
                // falls through

            case SyntaxKind.TypeAliasDeclaration:
                // TypeScript type-only declarations are elided.
                return undefined;

            case SyntaxKind.PropertyDeclaration:
                // TypeScript property declarations are elided. However their names are still visited, and can potentially be retained if they could have sideeffects
                return visitPropertyDeclaration(node as PropertyDeclaration);

            case SyntaxKind.NamespaceExportDeclaration:
                // TypeScript namespace export declarations are elided.
                return undefined;

            case SyntaxKind.Constructor:
                return visitConstructor(<ConstructorDeclaration>node);

            case SyntaxKind.InterfaceDeclaration:
                // TypeScript interfaces are elided, but some comments may be preserved.
                // See the implementation of `getLeadingComments` in comments.ts for more details.
                return createNotEmittedStatement(node);

            case SyntaxKind.ClassDeclaration:
                // This may be a class declaration with TypeScript syntax extensions.
                //
                // TypeScript class syntax extensions include:
                // - decorators
                // - optional `implements` heritage clause
                // - parameter property assignments in the constructor
                // - index signatures
                // - method overload signatures
                return visitClassDeclaration(<ClassDeclaration>node);

            case SyntaxKind.ClassExpression:
                // This may be a class expression with TypeScript syntax extensions.
                //
                // TypeScript class syntax extensions include:
                // - decorators
                // - optional `implements` heritage clause
                // - parameter property assignments in the constructor
                // - index signatures
                // - method overload signatures
                return visitClassExpression(<ClassExpression>node);

            case SyntaxKind.HeritageClause:
                // This may be a heritage clause with TypeScript syntax extensions.
                //
                // TypeScript heritage clause extensions include:
                // - `implements` clause
                return visitHeritageClause(<HeritageClause>node);

            case SyntaxKind.ExpressionWithTypeArguments:
                // TypeScript supports type arguments on an expression in an `extends` heritage clause.
                return visitExpressionWithTypeArguments(<ExpressionWithTypeArguments>node);

            case SyntaxKind.MethodDeclaration:
                // TypeScript method declarations may have decorators, modifiers
                // or type annotations.
                return visitMethodDeclaration(<MethodDeclaration>node);

            case SyntaxKind.GetAccessor:
                // Get Accessors can have TypeScript modifiers, decorators, and type annotations.
                return visitGetAccessor(<GetAccessorDeclaration>node);

            case SyntaxKind.SetAccessor:
                // Set Accessors can have TypeScript modifiers and type annotations.
                return visitSetAccessor(<SetAccessorDeclaration>node);

            case SyntaxKind.FunctionDeclaration:
                // Typescript function declarations can have modifiers, decorators, and type annotations.
                return visitFunctionDeclaration(<FunctionDeclaration>node);

            case SyntaxKind.FunctionExpression:
                // TypeScript function expressions can have modifiers and type annotations.
                return visitFunctionExpression(<FunctionExpression>node);

            case SyntaxKind.ArrowFunction:
                // TypeScript arrow functions can have modifiers and type annotations.
                return visitArrowFunction(<ArrowFunction>node);

            case SyntaxKind.Parameter:
                // This may be a parameter declaration with TypeScript syntax extensions.
                //
                // TypeScript parameter declaration syntax extensions include:
                // - decorators
                // - accessibility modifiers
                // - the question mark (?) token for optional parameters
                // - type annotations
                // - this parameters
                return visitParameter(<ParameterDeclaration>node);

            case SyntaxKind.ParenthesizedExpression:
                // ParenthesizedExpressions are TypeScript if their expression is a
                // TypeAssertion or AsExpression
                return visitParenthesizedExpression(<ParenthesizedExpression>node);

            case SyntaxKind.TypeAssertionExpression:
            case SyntaxKind.AsExpression:
                // TypeScript type assertions are removed, but their subtrees are preserved.
                return visitAssertionExpression(<AssertionExpression>node);

            case SyntaxKind.CallExpression:
                return visitCallExpression(<CallExpression>node);

            case SyntaxKind.NewExpression:
                return visitNewExpression(<NewExpression>node);

            case SyntaxKind.TaggedTemplateExpression:
                return visitTaggedTemplateExpression(<TaggedTemplateExpression>node);

            case SyntaxKind.NonNullExpression:
                // TypeScript non-null expressions are removed, but their subtrees are preserved.
                return visitNonNullExpression(<NonNullExpression>node);

            case SyntaxKind.EnumDeclaration:
                // TypeScript enum declarations do not exist in ES6 and must be rewritten.
                return visitEnumDeclaration(<EnumDeclaration>node);

            case SyntaxKind.VariableStatement:
                // TypeScript namespace exports for variable statements must be transformed.
                return visitVariableStatement(<VariableStatement>node);

            case SyntaxKind.VariableDeclaration:
                return visitVariableDeclaration(<VariableDeclaration>node);

            case SyntaxKind.ModuleDeclaration:
                // TypeScript namespace declarations must be transformed.
                return visitModuleDeclaration(<ModuleDeclaration>node);

            case SyntaxKind.ImportEqualsDeclaration:
                // TypeScript namespace or external module import.
                return visitImportEqualsDeclaration(<ImportEqualsDeclaration>node);

            default:
                // node contains some other TypeScript syntax
                return visitEachChild(node, visitor, context);
        }
    }

}
```

### transformClassFields
```ts
export function transformClassFields(context: TransformationContext) {
    ....
    return chainBundle(transformSourceFile);

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

    /* 拦截的visitor */
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

    /* visitClassExpression */
    function visitClassExpression(node: ClassExpression): Expression {

    }
    /* visitClassDeclaration */
    function visitClassDeclaration(node: ClassDeclaration) {
    
    }
    /* visitVariableStatement */
    function visitVariableStatement(node: VariableStatement) {
    }
}
```
 
