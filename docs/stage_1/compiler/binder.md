## 

## 类型接口
### ModuleInstanceState
```ts
export const enum ModuleInstanceState {
    NonInstantiated = 0,
    Instantiated = 1,
    ConstEnumOnly = 2
}
```

### ActiveLabel
```ts
interface ActiveLabel {
    name: __String;
    breakTarget: FlowLabel;
    continueTarget: FlowLabel;
    referenced: boolean;
}
```

### ContainerFlags

```ts
const enum ContainerFlags {
    None = 0,
    IsContainer = 1 << 0,

    IsBlockScopedContainer = 1 << 1,

    IsControlFlowContainer = 1 << 2,

    IsFunctionLike = 1 << 3,
    IsFunctionExpression = 1 << 4,
    HasLocals = 1 << 5,
    IsInterface = 1 << 6,
    IsObjectLiteralOrClassExpressionMethod = 1 << 7,
}
```

## 实例对象
```ts
let flowNodeCreated: <T extends FlowNode>(node: T) => T = initFlowNode;
const binder = createBinder();
```
## 功能函数
### getModuleInstanceState
```ts
export function getModuleInstanceState(node: ModuleDeclaration, visited?: Map<ModuleInstanceState | undefined>): ModuleInstanceState {
    if (node.body && !node.body.parent) {
        // getModuleInstanceStateForAliasTarget needs to walk up the parent chain, so parent pointers must be set on this tree already
        setParentPointers(node, node.body);
    }
    return node.body ? getModuleInstanceStateCached(node.body, visited) : ModuleInstanceState.Instantiated;
}
```

### bindSourceFile
```ts
export function bindSourceFile(file: SourceFile, options: CompilerOptions) {
    performance.mark("beforeBind");
    perfLogger.logStartBindFile("" + file.fileName);
    //核心流程
    binder(file, options);
    perfLogger.logStopBindFile();
    performance.mark("afterBind");
    performance.measure("Bind", "beforeBind", "afterBind");
}
```
### isExportsOrModuleExportsOrAlias
```ts
export function isExportsOrModuleExportsOrAlias(sourceFile: SourceFile, node: Expression): boolean {
    ...
}
```

### computeTransformFlagsForNode
```ts
export function computeTransformFlagsForNode(node: Node, subtreeFlags: TransformFlags): TransformFlags {
    ...
}
```

### getTransformFlagsSubtreeExclusions
```ts
export function getTransformFlagsSubtreeExclusions(kind: SyntaxKind) {
    ...
}
```

## 内部函数

### getModuleInstanceStateCached
```ts
function getModuleInstanceStateCached(node: Node, visited = createMap<ModuleInstanceState | undefined>()) {
    ...
}
```

### getModuleInstanceStateWorker
```ts
function getModuleInstanceStateWorker(node: Node, visited: Map<ModuleInstanceState | undefined>): ModuleInstanceState {
    ...
}
```

### getModuleInstanceStateWorker
```ts
function getModuleInstanceStateWorker(
        node: Node, 
        visited: Map<ModuleInstanceState | undefined>): ModuleInstanceState 
        {
    ...
}
```

### getModuleInstanceStateForAliasTarget
```ts
function getModuleInstanceStateForAliasTarget(
     specifier: ExportSpecifier,
     visited: Map<ModuleInstanceState | undefined>) {
    ...
}
```

### initFlowNode
```ts
function initFlowNode<T extends FlowNode>(node: T) {
    Debug.attachFlowNodeDebugInfo(node);
    return node;
}
```

### createBinder
> Binder核心实现
```ts
function createBinder(): (file: SourceFile, options: CompilerOptions) => void {
    ...
    function bindSourceFile(f: SourceFile, opts: CompilerOptions) {
        file = f;
        options = opts;
        languageVersion = getEmitScriptTarget(options);
        inStrictMode = bindInStrictMode(file, opts);
        classifiableNames = createUnderscoreEscapedMap<true>();
        symbolCount = 0;
        skipTransformFlagAggregation = file.isDeclarationFile;

        Symbol = objectAllocator.getSymbolConstructor();

        // Attach debugging information if necessary
        Debug.attachFlowNodeDebugInfo(unreachableFlow);
        Debug.attachFlowNodeDebugInfo(reportedUnreachableFlow);

        if (!file.locals) {
            bind(file);
            file.symbolCount = symbolCount;
            file.classifiableNames = classifiableNames;
            delayedBindJSDocTypedefTag();
        }

        file = undefined!;
        options = undefined!;
        languageVersion = undefined!;
        parent = undefined!;
        container = undefined!;
        thisParentContainer = undefined!;
        blockScopeContainer = undefined!;
        lastContainer = undefined!;
        delayedTypeAliases = undefined!;
        seenThisKeyword = false;
        currentFlow = undefined!;
        currentBreakTarget = undefined;
        currentContinueTarget = undefined;
        currentReturnTarget = undefined;
        currentTrueTarget = undefined;
        currentFalseTarget = undefined;
        activeLabels = undefined!;
        hasExplicitReturn = false;
        emitFlags = NodeFlags.None;
        subtreeTransformFlags = TransformFlags.None;
    }

    return bindSourceFile;

    function bind(node: Node | undefined): void {

    }
    function bindWorker(node: Node) {
    
    }
    function bindBlockScopedDeclaration(node: Declaration, symbolFlags: SymbolFlags, symbolExcludes: SymbolFlags) {
        switch (blockScopeContainer.kind) {
            case SyntaxKind.ModuleDeclaration:
                declareModuleMember(node, symbolFlags, symbolExcludes);
                break;
            case SyntaxKind.SourceFile:
                if (isExternalOrCommonJsModule(<SourceFile>container)) {
                    declareModuleMember(node, symbolFlags, symbolExcludes);
                    break;
                }
                // falls through
            default:
                if (!blockScopeContainer.locals) {
                    blockScopeContainer.locals = createSymbolTable();
                    addToContainerChain(blockScopeContainer);
                }
                declareSymbol(blockScopeContainer.locals, /*parent*/ undefined, node, symbolFlags, symbolExcludes);
        }
    }

    function declareSymbol(symbolTable: SymbolTable, parent: Symbol | undefined, node: Declaration, includes: SymbolFlags, excludes: SymbolFlags, isReplaceableByMethod?: boolean): Symbol {
        ...
    
    
    }

    function createSymbol(flags: SymbolFlags, name: __String): Symbol {
        symbolCount++;
        return new Symbol(flags, name);
    }

    function addDeclarationToSymbol(symbol: Symbol, node: Declaration, symbolFlags: SymbolFlags) {
        ...
    }

    function setValueDeclaration(symbol: Symbol, node: Declaration): void {
        ...
    }
    ...
}
```

### eachUnreachableRange
### isExecutableStatement
### isPurelyTypeDeclaration
### lookupSymbolForNameWorker
### computeCallExpression
### computeNewExpression
### computeBinaryExpression
### computeParameter
### computeParenthesizedExpression
### computeClassDeclaration
### computeClassExpression
### computeHeritageClause
### computeCatchClause
### computeExpressionWithTypeArguments
### setParentPointers