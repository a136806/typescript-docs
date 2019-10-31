# tsc 流程

## `tsc.ts`
- 1 `executeCommandLine`              命令行入口     (tsc.ts)
    - `parseCommandLine`              命令行解析(compiler/comandLinerParser.ts)
- 2 `executeCommandLineWorker`        命令行操作处理    (tsc.ts)
    - `tsc --xx`处理
- 3.2 `performIncrementalCompilation`                增量编译入口   (tsc.ts)
    - `ts.performIncrementalCompilation`             增量编译(compiler/watch.ts) 
        - `createIncrementalProgram`                 文件解析  (compiler/watch.ts)
        - `emitFilesAndReportErrorsAndGetExitStatus` 文件生成  (compiler/watch.ts)
- 3.3 `performCompilation`                           普通编译入口 (tsc.ts)
    - `createCompilerHost`                           文件解析  (compiler/program.ts)
    - `createProgram`                                文件解析  (compiler/program.ts)
    - `emitFilesAndReportErrorsAndGetExitStatus`     文件生成  (compiler/watch.ts)

##  compiler
- 1 compiler初始化
- `createCompilerHost`          文件解析  (compiler/program.ts)
- `createCompilerHostWorker`    文件解析  (compiler/program.ts)
```ts
const compilerHost: CompilerHost = {
    getSourceFile,
    getDefaultLibLocation,
    getDefaultLibFileName: options => combinePaths(getDefaultLibLocation(), getDefaultLibFileName(options)),
    writeFile,
    getCurrentDirectory: memoize(() => system.getCurrentDirectory()),
    useCaseSensitiveFileNames: () => system.useCaseSensitiveFileNames,
    getCanonicalFileName,
    getNewLine: () => newLine,
    fileExists: fileName => system.fileExists(fileName),
    readFile: fileName => system.readFile(fileName),
    trace: (s: string) => system.write(s + newLine),
    directoryExists: directoryName => system.directoryExists(directoryName),
    getEnvironmentVariable: name => system.getEnvironmentVariable ? system.getEnvironmentVariable(name) : "",
    getDirectories: (path: string) => system.getDirectories(path),
    realpath,
    readDirectory: (path, extensions, include, exclude, depth) => system.readDirectory(path, extensions, include, exclude, depth),
    createDirectory: d => system.createDirectory(d),
    createHash: maybeBind(system, system.createHash)
};
return compilerHost;
```
- 2 文本解析
- `compilerHost.getSourceFile()`
- `ts.createSourceFile`            文件解析  (compiler/parser.ts)
```ts
//解析结果
let result: SourceFile;
result = Parser.parseSourceFile(fileName, sourceText, languageVersion, /*syntaxCursor*/ undefined, setParentNodes, ScriptKind.JSON);
return result;
```
- `Parser.parseSourceFile`      文件解析    (compiler/parser.ts)
- `parseSourceFileWorker`       文件解析    (compiler/parser.ts)
- `Parser.createSourceFile`
- `parseStatement`              Parser入口  (compiler/parser.ts)
- `parseList`

- 3 parseStatement

```ts
function parseStatement(): Statement {
    switch (token()) {
        case SyntaxKind.SemicolonToken:
            return parseEmptyStatement();
        case SyntaxKind.OpenBraceToken:
            return parseBlock(/*ignoreMissingOpenBrace*/ false);
        case SyntaxKind.VarKeyword:
            return parseVariableStatement(<VariableStatement>createNodeWithJSDoc(SyntaxKind.VariableDeclaration));
        case SyntaxKind.LetKeyword:
            if (isLetDeclaration()) {
                return parseVariableStatement(<VariableStatement>createNodeWithJSDoc(SyntaxKind.VariableDeclaration));
            }
            break;
        case SyntaxKind.FunctionKeyword:
            return parseFunctionDeclaration(<FunctionDeclaration>createNodeWithJSDoc(SyntaxKind.FunctionDeclaration));
        case SyntaxKind.ClassKeyword:
            return parseClassDeclaration(<ClassDeclaration>createNodeWithJSDoc(SyntaxKind.ClassDeclaration));
        case SyntaxKind.IfKeyword:
            return parseIfStatement();
        case SyntaxKind.DoKeyword:
            return parseDoStatement();
        case SyntaxKind.WhileKeyword:
            return parseWhileStatement();
        case SyntaxKind.ForKeyword:
            return parseForOrForInOrForOfStatement();
        case SyntaxKind.ContinueKeyword:
            return parseBreakOrContinueStatement(SyntaxKind.ContinueStatement);
        case SyntaxKind.BreakKeyword:
            return parseBreakOrContinueStatement(SyntaxKind.BreakStatement);
        case SyntaxKind.ReturnKeyword:
            return parseReturnStatement();
        case SyntaxKind.WithKeyword:
            return parseWithStatement();
        case SyntaxKind.SwitchKeyword:
            return parseSwitchStatement();
        case SyntaxKind.ThrowKeyword:
            return parseThrowStatement();
        case SyntaxKind.TryKeyword:
        // Include 'catch' and 'finally' for error recovery.
        // falls through
        case SyntaxKind.CatchKeyword:
        case SyntaxKind.FinallyKeyword:
            return parseTryStatement();
        case SyntaxKind.DebuggerKeyword:
            return parseDebuggerStatement();
        case SyntaxKind.AtToken:
            return parseDeclaration();
        case SyntaxKind.AsyncKeyword:
        case SyntaxKind.InterfaceKeyword:
        case SyntaxKind.TypeKeyword:
        case SyntaxKind.ModuleKeyword:
        case SyntaxKind.NamespaceKeyword:
        case SyntaxKind.DeclareKeyword:
        case SyntaxKind.ConstKeyword:
        case SyntaxKind.EnumKeyword:
        case SyntaxKind.ExportKeyword:
        case SyntaxKind.ImportKeyword:
        case SyntaxKind.PrivateKeyword:
        case SyntaxKind.ProtectedKeyword:
        case SyntaxKind.PublicKeyword:
        case SyntaxKind.AbstractKeyword:
        case SyntaxKind.StaticKeyword:
        case SyntaxKind.ReadonlyKeyword:
        case SyntaxKind.GlobalKeyword:
            if (isStartOfDeclaration()) {
                return parseDeclaration();
            }
            break;
    }
    return parseExpressionOrLabeledStatement();
}

```


##  program初始化
- 1 `createProgram`                                      (compiler/program.ts) 

##  emit代码生成
- 1   watch
    - `emitFilesAndReportErrorsAndGetExitStatus`         (compiler/watch.ts)
    - `emitFilesAndReportErrors`                         (compiler/watch.ts)   
- 2   program
    - `program.emit()`                                   (compiler/program.ts) 
    - `emitWorker()`                                     (compiler/program.ts) 
- 3   emitter
    - `emitFiles()`                                      (compiler/emitter.ts)
    - `getTransformers`                                  (compiler/transformer.ts)   
    - `forEachEmittedFile`                               (compiler/emitter.ts)           
    - `emitSourceFileOrBundle`                           (compiler/emitter.ts)   
        - `emitJsFileOrBundle`                           (compiler/emitter.ts)
            - `transformNodes`                           (compiler/transformer.ts)(4)
            - `createPrinter`                            (compiler/emitter.ts)   
        - `emitDeclarationFileOrBundle`                  (compiler/emitter.ts)
            - `transformNodes`                           (compiler/transformer.ts)
            - `createPrinter`                            (compiler/emitter.ts)(4)
        - `emitBuildInfo`                                (compiler/emitter.ts)
            - `getProgramBuildInfo`                      (compiler/program.ts)   
            - `writeFile`                                (compiler/program.ts)
- 4  transformer  
    - `getTransformers`                                  (compiler/transformer.ts)  
        - `getScriptTransformers`                        (compiler/transformer.ts)
        - `getDeclarationTransformers`                   (compiler/transformer.ts)
    - `transformNodes`                                   (compiler/transformer.ts)
    - `transformRoot`                                    (compiler/transformer.ts)
    - `transformation`                                   (compiler/transformer.ts)

- 5 getScriptTransformers
    - `customTransformers.before`
    - `transformTypeScript`
    - `transformClassFields`
    - `transformJsx`
    - `transformESNext`
    - `transformES2019`
    - `transformES2018`
    - `transformES2017`
    - `transformES2016`
    - `transformES2015`
    - `transformGenerators`
    - `transformES5`
    - `customTransformers.after`
    getDeclarationTransformers
    -  `transformDeclarations`
- 6  transformers\ts.ts
    - `transformTypeScript`  
    - `transformSourceFileOrBundle`
    - `transformSourceFile`
    - `visitSourceFile`
        - `sourceElementVisitor`
            - `sourceElementVisitorWorker`
            - ``
        - `visitLexicalEnvironment`
            - `startLexicalEnvironment`
            - `visitNodes`
            - `mergeLexicalEnvironment`
        - `updateSourceFileNode`

-   transformers\es2015.ts
    - `transformES2015`
    - `chainBundle(transformSourceFile)`
    - `transformSourceFileOrBundle`
    - `transformSourceFile`
    - `visitSourceFile`
-   visitSourceFile       
    - `visitNodes`
    - `updateSourceFileNode`    
