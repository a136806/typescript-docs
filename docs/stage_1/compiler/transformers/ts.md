## 功能说明
## 类型接口
### TypeScriptSubstitutionFlags
```ts
const enum TypeScriptSubstitutionFlags {
    ClassAliases = 1 << 0,
    NamespaceExports = 1 << 1,
    NonQualifiedEnumMembers = 1 << 3
}
```

### ClassFacts
```ts
const enum ClassFacts {
    None = 0,
    HasStaticInitializedProperties = 1 << 0,
    HasConstructorDecorators = 1 << 1,
    HasMemberDecorators = 1 << 2,
    IsExportOfNamespace = 1 << 3,
    IsNamedExternalExport = 1 << 4,
    IsDefaultExternalExport = 1 << 5,
    IsDerivedClass = 1 << 6,
    UseImmediatelyInvokedFunctionExpression = 1 << 7,

    HasAnyDecorators = HasConstructorDecorators | HasMemberDecorators,
    NeedsName = HasStaticInitializedProperties | HasMemberDecorators,
    MayNeedImmediatelyInvokedFunctionExpression = HasAnyDecorators | HasStaticInitializedProperties,
    IsExported = IsExportOfNamespace | IsDefaultExternalExport | IsNamedExternalExport,
}
```
## 实例对象
### USE_NEW_TYPE_METADATA_FORMAT
```ts
const USE_NEW_TYPE_METADATA_FORMAT = false;
```
## 功能函数

### transformTypeScript
```ts
export function transformTypeScript(context: TransformationContext) {
    const {
        startLexicalEnvironment,
        resumeLexicalEnvironment,
        endLexicalEnvironment,
        hoistVariableDeclaration,
    } = context;


    const resolver = context.getEmitResolver();
    const compilerOptions = context.getCompilerOptions();
    const strictNullChecks = getStrictOptionValue(compilerOptions, "strictNullChecks");
    const languageVersion = getEmitScriptTarget(compilerOptions);
    const moduleKind = getEmitModuleKind(compilerOptions);

    //缓存
    const previousOnEmitNode = context.onEmitNode;
    const previousOnSubstituteNode = context.onSubstituteNode;

    context.onEmitNode = onEmitNode;
    context.onSubstituteNode = onSubstituteNode;


    context.enableSubstitution(SyntaxKind.PropertyAccessExpression);
    context.enableSubstitution(SyntaxKind.ElementAccessExpression);


    let currentSourceFile: SourceFile;
    let currentNamespace: ModuleDeclaration;
    let currentNamespaceContainerName: Identifier;
    let currentLexicalScope: SourceFile | Block | ModuleBlock | CaseBlock;
    let currentNameScope: ClassDeclaration | undefined;
    let currentScopeFirstDeclarationsOfName: UnderscoreEscapedMap<Node> | undefined;
    let currentClassHasParameterProperties: boolean | undefined;
    let enabledSubstitutions: TypeScriptSubstitutionFlags;
    let classAliases: Identifier[];
    let applicableSubstitutions: TypeScriptSubstitutionFlags;

    return transformSourceFileOrBundle;


    //转换入口
    function transformSourceFileOrBundle(node: SourceFile | Bundle) {
        if (node.kind === SyntaxKind.Bundle) {
            return transformBundle(node);
        }
        return transformSourceFile(node);
    }


    // Bundle转换
    function transformBundle(node: Bundle) {
        return createBundle(node.sourceFiles.map(transformSourceFile), mapDefined(node.prepends, prepend => {
            if (prepend.kind === SyntaxKind.InputFiles) {
                return createUnparsedSourceFile(prepend, "js");
            }
            return prepend;
        }));
    }

    // SourceFile转换
    function transformSourceFile(node: SourceFile) {
        if (node.isDeclarationFile) {
            return node;
        }

        currentSourceFile = node;

        const visited = saveStateAndInvoke(node, visitSourceFile);
        addEmitHelpers(visited, context.readEmitHelpers());

        currentSourceFile = undefined!;
        return visited;
    }

    function saveStateAndInvoke<T>(node: Node, f: (node: Node) => T): T {
         // Save state
        const savedCurrentScope = currentLexicalScope;
        const savedCurrentNameScope = currentNameScope;
        const savedCurrentScopeFirstDeclarationsOfName = currentScopeFirstDeclarationsOfName;
        const savedCurrentClassHasParameterProperties = currentClassHasParameterProperties;

        // Handle state changes before visiting a node.
        onBeforeVisitNode(node);
        //转换处理 visitSourceFile
        const visited = f(node);

        // Restore state
        if (currentLexicalScope !== savedCurrentScope) {
            currentScopeFirstDeclarationsOfName = savedCurrentScopeFirstDeclarationsOfName;
        }

        currentLexicalScope = savedCurrentScope;
        currentNameScope = savedCurrentNameScope;
        currentClassHasParameterProperties = savedCurrentClassHasParameterProperties;
        
        return visited;
    }
    function onBeforeVisitNode(node: Node) {

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

    function visitEllidableStatement(node: ImportDeclaration | ImportEqualsDeclaration | ExportAssignment | ExportDeclaration): VisitResult<Node> {
         const parsed = getParseTreeNode(node);
            if (parsed !== node) {
                if (node.transformFlags & TransformFlags.ContainsTypeScript) {
                    return visitEachChild(node, visitor, context);
                }
                return node;
            }

            switch (node.kind) {
                case SyntaxKind.ImportDeclaration:
                    return visitImportDeclaration(node);
                case SyntaxKind.ImportEqualsDeclaration:
                    return visitImportEqualsDeclaration(node);
                case SyntaxKind.ExportAssignment:
                    return visitExportAssignment(node);
                case SyntaxKind.ExportDeclaration:
                    return visitExportDeclaration(node);
                default:
                    Debug.fail("Unhandled ellided statement");
            }
    }

    function visitorWorker(node: Node): VisitResult<Node> {
        if (node.transformFlags & TransformFlags.ContainsTypeScript) {
            return visitTypeScript(node);
        }
        return node;
    }
    ......
}
```
## 内部函数
