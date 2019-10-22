## 类型接口
### TransformationState
```ts
const enum TransformationState {
    Uninitialized, //待初始化
    Initialized,   //已初始化
    Completed,     //已完成
    Disposed       //已释放
}
```
### SyntaxKindFeatureFlags
```ts
const enum SyntaxKindFeatureFlags {
    Substitution = 1 << 0,
    EmitNotifications = 1 << 1,
}
```

## 实例对象
> 空Transformer对象
> `scriptTransformers`,`declarationTransformers`
### noTransformers
```ts
export const noTransformers: EmitTransformers = { 
    scriptTransformers: emptyArray, 
    declarationTransformers: emptyArray 
};
```

## 功能函数
### getTransformers
> 获取配置的转换器
```ts
export function getTransformers(
    compilerOptions: CompilerOptions, 
    customTransformers?: CustomTransformers, 
    emitOnlyDtsFiles?: boolean): EmitTransformers {
    return {
        scriptTransformers: getScriptTransformers(
            compilerOptions, 
            customTransformers, 
            emitOnlyDtsFiles),
        declarationTransformers: getDeclarationTransformers(customTransformers),
    };
}
```

### noEmitSubstitution
```ts
export function noEmitSubstitution(_hint: EmitHint, node: Node) {
    return node;
}
```

### noEmitNotification
```ts
export function noEmitNotification(
    hint: EmitHint, 
    node: Node, 
    callback: (hint: EmitHint, node: Node) => void) {
    callback(hint, node);
}
```

### transformNodes
```ts
export function transformNodes<T extends Node>(

    resolver: EmitResolver | undefined, 
    host: EmitHost | undefined, 
    options: CompilerOptions, 
    nodes: readonly T[], 
    transformers: readonly TransformerFactory<T>[], allowDtsFiles: boolean
    
    ): TransformationResult<T> {
    
    //声明转换结果
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

    //转换配置项
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


    // Ensure the parse tree is clean before applying transformations
    for (const node of nodes) {
        disposeEmitNodes(getSourceFileOfNode(getParseTreeNode(node)));
    }

    performance.mark("beforeTransform");

    // 转换器配置
    const transformersWithContext = transformers.map(t => t(context));
    const transformation = (node: T): T => {
        for (const transform of transformersWithContext) {
            node = transform(node);
        }
        return node;
    };

    // 转换状态记录
    state = TransformationState.Initialized;

    // 转换处理
    const transformed = map(nodes, allowDtsFiles ? transformation : transformRoot);

    // 转换状态记录
    state = TransformationState.Completed;

    performance.mark("afterTransform");
    performance.measure("transformTime", "beforeTransform", "afterTransform");

    //返回的转换结果
    return {
        transformed,
        substituteNode,
        emitNodeWithNotification,
        dispose,
        diagnostics
    };

    
}
```
## 内部函数

### getModuleTransformer
> 获取模块转换器(es,system,module)
```ts
function getModuleTransformer(moduleKind: ModuleKind): TransformerFactory<SourceFile | Bundle> {
    switch (moduleKind) {
        case ModuleKind.ESNext:
        case ModuleKind.ES2015:
            return transformES2015Module;
        case ModuleKind.System:
            return transformSystemModule;
        default:
            return transformModule;
    }
}
```

### getScriptTransformers
> script转换器
```ts
function getScriptTransformers(
    compilerOptions: CompilerOptions, 
    customTransformers?: CustomTransformers, 
    emitOnlyDtsFiles?: boolean) {
    //返回空数组
    if (emitOnlyDtsFiles) return emptyArray;


    const jsx = compilerOptions.jsx;
    const languageVersion = getEmitScriptTarget(compilerOptions);
    const moduleKind = getEmitModuleKind(compilerOptions);
    const transformers: TransformerFactory<SourceFile | Bundle>[] = [];

    //自定义转换器前置操作
    addRange(transformers, customTransformers && map(customTransformers.before, wrapScriptTransformerFactory));

    //TypeScript,ClassFields
    transformers.push(transformTypeScript);
    transformers.push(transformClassFields);

    // jsx
    if (jsx === JsxEmit.React) {
        transformers.push(transformJsx);
    }

    // ES
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

    // Module
    transformers.push(getModuleTransformer(moduleKind));


    // ES3
    if (languageVersion < ScriptTarget.ES5) {
        transformers.push(transformES5);
    }

    // 自定义转换器后置操作
    addRange(transformers, customTransformers && map(customTransformers.after, wrapScriptTransformerFactory));

    return transformers;
}
```

### getDeclarationTransformers
```ts
function getDeclarationTransformers(customTransformers?: CustomTransformers) {
    const transformers: TransformerFactory<SourceFile | Bundle>[] = [];

    // transformDeclarations
    transformers.push(transformDeclarations);

    // 自定义转换器后置操作
    addRange(transformers, customTransformers && map(customTransformers.afterDeclarations, wrapDeclarationTransformerFactory));

    return transformers;
}
```

### wrapCustomTransformer
```ts
function wrapCustomTransformer(
    transformer: CustomTransformer
    ): Transformer<Bundle | SourceFile> {
    return node => isBundle(node) 
        ? transformer.transformBundle(node) 
        : transformer.transformSourceFile(node);
}
```
### wrapCustomTransformerFactory
```ts
function wrapCustomTransformerFactory<T extends SourceFile | Bundle>(
    transformer: TransformerFactory<T> | CustomTransformerFactory, 
    handleDefault: (node: Transformer<T>) => Transformer<Bundle | SourceFile>
    ): TransformerFactory<Bundle | SourceFile> {
    
    return context => {
        const customTransformer = transformer(context);

        return typeof customTransformer === "function"
            ? handleDefault(customTransformer)
            : wrapCustomTransformer(customTransformer);
    };
}
```
### wrapScriptTransformerFactory
```ts
function wrapScriptTransformerFactory(
    transformer: TransformerFactory<SourceFile> | CustomTransformerFactory
    ): TransformerFactory<Bundle | SourceFile> {
    return wrapCustomTransformerFactory(transformer, chainBundle);
}
```
### wrapDeclarationTransformerFactory
```ts
function wrapDeclarationTransformerFactory(
    transformer: TransformerFactory<Bundle | SourceFile> | CustomTransformerFactory
    ) : TransformerFactory<Bundle | SourceFile> {
    return wrapCustomTransformerFactory(transformer, identity);
}
```