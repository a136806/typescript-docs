## 类型接口
### EmitUsingBuildInfoResult
```ts
export type EmitUsingBuildInfoResult = string | readonly OutputFile[];
```

### TempFlags
```ts
const enum TempFlags {
    Auto = 0x00000000,  
    CountMask = 0x0FFFFFFF,  
    _i = 0x10000000,  
}
```

### EmitUsingBuildInfoHost
```ts
export interface EmitUsingBuildInfoHost extends ModuleResolutionHost {
    getCurrentDirectory(): string;
    getCanonicalFileName(fileName: string): string;
    useCaseSensitiveFileNames(): boolean;
    getNewLine(): string;
}
```

### PipelinePhase
```ts
const enum PipelinePhase {
    Notification,
    Substitution,
    Comments,
    SourceMaps,
    Emit
}
```
## 实例对象
### Base
```ts
const brackets = createBracketsMap();
const syntheticParent: TextRange = { pos: -1, end: -1 };
export const notImplementedResolver: EmitResolver = {

}
```

## 功能函数
### isBuildInfoFile
```ts
export function isBuildInfoFile(file: string) {
    return fileExtensionIs(file, Extension.TsBuildInfo);
}
```

### forEachEmittedFile
```ts
export function forEachEmittedFile<T>(
    host: EmitHost, 
    action: (emitFileNames: EmitFileNames, sourceFileOrBundle: SourceFile | Bundle | undefined) => T,
    sourceFilesOrTargetSourceFile?: readonly SourceFile[] | SourceFile,
    forceDtsEmit = false,
    onlyBuildInfo?: boolean,
    includeBuildInfo?: boolean) {

}
```

### getTsBuildInfoEmitOutputFilePath
```ts
export function getTsBuildInfoEmitOutputFilePath(
    options: CompilerOptions) {
    ...
}
```

### getOutputPathsForBundle
```ts
export function getOutputPathsForBundle(
    options: CompilerOptions, 
    forceDtsPaths: boolean): EmitFileNames {
    ...
}
```

### getOutputPathsFor
```ts
export function getOutputPathsFor(
    sourceFile: SourceFile | Bundle, 
    host: EmitHost, 
    forceDtsPaths: boolean): EmitFileNames {
    ...
}
```

### getOutputExtension
```ts
export function getOutputExtension(
    sourceFile: SourceFile, 
    options: CompilerOptions): Extension {
    ...
}
```

### getOutputDeclarationFileName
```ts
export function getOutputDeclarationFileName(
    inputFileName: string, 
    configFile: ParsedCommandLine, 
    ignoreCase: boolean) {
    ...
}
```

### getAllProjectOutputs
```ts
export function getAllProjectOutputs(
    configFile: ParsedCommandLine, 
    ignoreCase: boolean): readonly string[] {
    ...
}
```

### getOutputFileNames
```ts
export function getOutputFileNames(
    commandLine: ParsedCommandLine,
    inputFileName: string, 
    ignoreCase: boolean): readonly string[] {
    ...
}
```

#### getFirstProjectOutput
```ts
export function getFirstProjectOutput(
    configFile: ParsedCommandLine, 
    ignoreCase: boolean): string {
    ...
}
```

### emitFiles
```ts
export function emitFiles(
    resolver: EmitResolver, 
    host: EmitHost, 
    targetSourceFile: SourceFile | undefined, 
    { scriptTransformers, declarationTransformers }: EmitTransformers, 
    emitOnlyDtsFiles?: boolean,
    onlyBuildInfo?: boolean, 
    forceDtsEmit?: boolean): EmitResult {

    const compilerOptions = host.getCompilerOptions();
    const sourceMapDataList: SourceMapEmitResult[] | undefined = (compilerOptions.sourceMap || compilerOptions.inlineSourceMap || getAreDeclarationMapsEnabled(compilerOptions)) ? [] : undefined;
    const emittedFilesList: string[] | undefined = compilerOptions.listEmittedFiles ? [] : undefined;
    const emitterDiagnostics = createDiagnosticCollection();
    const newLine = getNewLineCharacter(compilerOptions, () => host.getNewLine());
    const writer = createTextWriter(newLine);
    const { enter, exit } = performance.createTimer("printTime", "beforePrint", "afterPrint");
    let bundleBuildInfo: BundleBuildInfo | undefined;
    let emitSkipped = false;
    let exportedModulesFromDeclarationEmit: ExportedModulesFromDeclarationEmit | undefined;


    enter();
    forEachEmittedFile(
        host,
        emitSourceFileOrBundle,
        getSourceFilesToEmit(host, targetSourceFile),
        forceDtsEmit,
        onlyBuildInfo,
        !targetSourceFile
    );
    exit();


    return {
        emitSkipped,
        diagnostics: emitterDiagnostics.getDiagnostics(),
        emittedFiles: emittedFilesList,
        sourceMaps: sourceMapDataList,
        exportedModulesFromDeclarationEmit
    };

    function emitSourceFileOrBundle(
        {  
            jsFilePath, 
            sourceMapFilePath,
            declarationFilePath, 
            declarationMapPath, 
            buildInfoPath 
        }: EmitFileNames, 
        sourceFileOrBundle: SourceFile | Bundle | undefined) {
        

         let buildInfoDirectory: string | undefined;
        if (buildInfoPath && sourceFileOrBundle && isBundle(sourceFileOrBundle)) {
            buildInfoDirectory = getDirectoryPath(getNormalizedAbsolutePath(buildInfoPath, host.getCurrentDirectory()));
            bundleBuildInfo = {
                commonSourceDirectory: relativeToBuildInfo(host.getCommonSourceDirectory()),
                sourceFiles: sourceFileOrBundle.sourceFiles.map(file => relativeToBuildInfo(getNormalizedAbsolutePath(file.fileName, host.getCurrentDirectory())))
            };
        }
        emitJsFileOrBundle(sourceFileOrBundle, jsFilePath, sourceMapFilePath, relativeToBuildInfo);
        emitDeclarationFileOrBundle(sourceFileOrBundle, declarationFilePath, declarationMapPath, relativeToBuildInfo);
        emitBuildInfo(bundleBuildInfo, buildInfoPath);
        ...
    }

    // emitJsFileOrBundle
    function emitJsFileOrBundle(
        sourceFileOrBundle: SourceFile | Bundle | undefined,
        jsFilePath: string | undefined,
        sourceMapFilePath: string | undefined,
        relativeToBuildInfo: (path: string) => string) {
        if (!sourceFileOrBundle || emitOnlyDtsFiles || !jsFilePath) {
            return;
        }

        // Make sure not to write js file and source map file if any of them cannot be written
        if ((jsFilePath && host.isEmitBlocked(jsFilePath)) || compilerOptions.noEmit) {
            emitSkipped = true;
            return;
        }

        // Transformer
        const transform = transformNodes(resolver, host, compilerOptions, [sourceFileOrBundle], scriptTransformers, /*allowDtsFiles*/ false);
    

        //Printer
        const printerOptions: PrinterOptions = {
            removeComments: compilerOptions.removeComments,
            newLine: compilerOptions.newLine,
            noEmitHelpers: compilerOptions.noEmitHelpers,
            module: compilerOptions.module,
            target: compilerOptions.target,
            sourceMap: compilerOptions.sourceMap,
            inlineSourceMap: compilerOptions.inlineSourceMap,
            inlineSources: compilerOptions.inlineSources,
            extendedDiagnostics: compilerOptions.extendedDiagnostics,
            writeBundleFileInfo: !!bundleBuildInfo,
            relativeToBuildInfo
        };

        const printer = createPrinter(printerOptions, {
            hasGlobalName: resolver.hasGlobalName,

            onEmitNode: transform.emitNodeWithNotification,
            substituteNode: transform.substituteNode,
        });

        Debug.assert(transform.transformed.length === 1, "Should only see one output from the transform");
        printSourceFileOrBundle(jsFilePath, sourceMapFilePath, transform.transformed[0], printer, compilerOptions);

        transform.dispose();
        if (bundleBuildInfo) bundleBuildInfo.js = printer.bundleFileInfo;
    }

    // emitDeclarationFileOrBundle
    function emitDeclarationFileOrBundle(
        sourceFileOrBundle: SourceFile | Bundle | undefined,
        declarationFilePath: string | undefined,
        declarationMapPath: string | undefined,
        relativeToBuildInfo: (path: string) => string) {

        if (!sourceFileOrBundle || !declarationFilePath) {
            return;
        }
        const sourceFiles = isSourceFile(sourceFileOrBundle) 
            ? [sourceFileOrBundle]
            : sourceFileOrBundle.sourceFiles;
        const inputListOrBundle = (compilerOptions.outFile || compilerOptions.out) 
                ? [createBundle(sourceFiles, !isSourceFile(sourceFileOrBundle) 
                    ? sourceFileOrBundle.prepends 
                    : undefined)] 
                : sourceFiles;
        if (emitOnlyDtsFiles && !getEmitDeclarations(compilerOptions)) {
            sourceFiles.forEach(collectLinkedAliases);
        }    


        // Transform
        const declarationTransform = transformNodes(resolver, host, compilerOptions, inputListOrBundle, declarationTransformers, /*allowDtsFiles*/ false);
        if (length(declarationTransform.diagnostics)) {
            for (const diagnostic of declarationTransform.diagnostics!) {
                emitterDiagnostics.add(diagnostic);
            }
        }


        const printerOptions: PrinterOptions = {
            removeComments: compilerOptions.removeComments,
            newLine: compilerOptions.newLine,
            noEmitHelpers: true,
            module: compilerOptions.module,
            target: compilerOptions.target,
            sourceMap: compilerOptions.sourceMap,
            inlineSourceMap: compilerOptions.inlineSourceMap,
            extendedDiagnostics: compilerOptions.extendedDiagnostics,
            onlyPrintJsDocStyle: true,
            writeBundleFileInfo: !!bundleBuildInfo,
            recordInternalSection: !!bundleBuildInfo,
            relativeToBuildInfo
        };

        const declarationPrinter = createPrinter(printerOptions, {
            hasGlobalName: resolver.hasGlobalName,

            onEmitNode: declarationTransform.emitNodeWithNotification,
            substituteNode: declarationTransform.substituteNode,
        });

        const declBlocked = (!!declarationTransform.diagnostics && !!declarationTransform.diagnostics.length) || !!host.isEmitBlocked(declarationFilePath) || !!compilerOptions.noEmit;
        emitSkipped = emitSkipped || declBlocked;


         if (!declBlocked || forceDtsEmit) {
            Debug.assert(declarationTransform.transformed.length === 1, "Should only see one output from the decl transform");
            printSourceFileOrBundle(
                declarationFilePath,
                declarationMapPath,
                declarationTransform.transformed[0],
                declarationPrinter,
                {
                    sourceMap: compilerOptions.declarationMap,
                    sourceRoot: compilerOptions.sourceRoot,
                    mapRoot: compilerOptions.mapRoot,
                    extendedDiagnostics: compilerOptions.extendedDiagnostics,
                    // Explicitly do not passthru either `inline` option
                }
            );
            if (forceDtsEmit && declarationTransform.transformed[0].kind === SyntaxKind.SourceFile) {
                const sourceFile = declarationTransform.transformed[0];
                exportedModulesFromDeclarationEmit = sourceFile.exportedModulesFromDeclarationEmit;
            }
        }
        declarationTransform.dispose();
        if (bundleBuildInfo) bundleBuildInfo.dts = declarationPrinter.bundleFileInfo;
    }


    function collectLinkedAliases(node: Node) {
        if (isExportAssignment(node)) {
            if (node.expression.kind === SyntaxKind.Identifier) {
                resolver.collectLinkedAliases(node.expression as Identifier, /*setVisibility*/ true);
            }
            return;
        }
        else if (isExportSpecifier(node)) {
            resolver.collectLinkedAliases(node.propertyName || node.name, /*setVisibility*/ true);
            return;
        }
        forEachChild(node, collectLinkedAliases);
    }


    function printSourceFileOrBundle(
        jsFilePath: string, 
        sourceMapFilePath: string | undefined, 
        sourceFileOrBundle: SourceFile | Bundle, 
        printer: Printer, 
        mapOptions: SourceMapOptions) {
    
    }

    function shouldEmitSourceMaps(
        mapOptions: SourceMapOptions, 
        sourceFileOrBundle: SourceFile | Bundle) {
    }

    function getSourceRoot(
        mapOptions: SourceMapOptions) {
    }

    function getSourceMapDirectory(
        mapOptions: SourceMapOptions, 
        filePath: string, 
        sourceFile: SourceFile | undefined) {
    }

    function getSourceMappingURL(
        mapOptions: SourceMapOptions, 
        sourceMapGenerator: SourceMapGenerator, 
        filePath: string, 
        sourceMapFilePath: string | undefined, 
        sourceFile: SourceFile | undefined) {

    }

    function emitBuildInfo(bundle: BundleBuildInfo | undefined, buildInfoPath: string | undefined) {
        if (!buildInfoPath || targetSourceFile || emitSkipped) return;
        const program = host.getProgramBuildInfo();
        if (host.isEmitBlocked(buildInfoPath) || compilerOptions.noEmit) {
            emitSkipped = true;
            return;
        }
        writeFile(host, emitterDiagnostics, buildInfoPath, getBuildInfoText({ bundle, program, version }), /*writeByteOrderMark*/ false);
    }
}
```

### getBuildInfoText
```ts
export function getBuildInfoText(buildInfo: BuildInfo) {
    return JSON.stringify(buildInfo, undefined, 2);
}
```

### getBuildInfo
```ts
export function getBuildInfo(buildInfoText: string) {
    return JSON.parse(buildInfoText) as BuildInfo;
}
```

### emitUsingBuildInfo
```ts
export function emitUsingBuildInfo(
        config: ParsedCommandLine,
        host: EmitUsingBuildInfoHost,
        getCommandLine: (ref: ProjectReference) => ParsedCommandLine | undefined,
        customTransformers?: CustomTransformers
    ): EmitUsingBuildInfoResult {
        ..
    }
```

### createPrinter
```ts
export function createPrinter(
    printerOptions: PrinterOptions = {}, 
    handlers: PrintHandlers = {}): Printer {

    ...

    return {
        // public API
        printNode,
        printList,
        printFile,
        printBundle,

        // internal API
        writeNode,
        writeList,
        writeFile,
        writeBundle,
        bundleFileInfo
    };

    ...
    function printNode(hint: EmitHint, node: Node, sourceFile: SourceFile): string {}
    function printList<T extends Node>(format: ListFormat, nodes: NodeArray<T>, sourceFile: SourceFile) {
    }
    function printBundle(bundle: Bundle): string {}
    function printFile(sourceFile: SourceFile): string {}
    function printUnparsedSource(unparsed: UnparsedSource): string {}

    function writeNode(hint: EmitHint, node: TypeNode, sourceFile: undefined, output: EmitTextWriter): void;
    function writeNode(hint: EmitHint, node: Node, sourceFile: SourceFile, output: EmitTextWriter): void;
    function writeNode(hint: EmitHint, node: Node, sourceFile: SourceFile | undefined, output: EmitTextWriter) {
    }

    function writeList<T extends Node>(format: ListFormat, nodes: NodeArray<T>, sourceFile: SourceFile | undefined, output: EmitTextWriter) {
    }
    function writeBundle(bundle: Bundle, output: EmitTextWriter, sourceMapGenerator: SourceMapGenerator | undefined) {}
    function writeUnparsedSource(unparsed: UnparsedSource, output: EmitTextWriter) {}
    function writeFile(sourceFile: SourceFile, output: EmitTextWriter, sourceMapGenerator: SourceMapGenerator | undefined) {}

    function getTextPosWithWriteLine() {}
    function updateOrPushBundleFileTextLike(pos: number, end: number, kind: BundleFileTextLikeKind) {}
    function recordBundleFileInternalSectionStart(node: Node) {}
    function recordBundleFileInternalSectionEnd(prevSourceFileTextKind: ReturnType<typeof recordBundleFileInternalSectionStart>) {}
    function recordBundleFileTextLikeSection(end: number) {}

    function beginPrint() {}
    function endPrint() {}
    function print(hint: EmitHint, node: Node, sourceFile: SourceFile | undefined) {}
    function setSourceFile(sourceFile: SourceFile | undefined) {}
    function setWriter(_writer: EmitTextWriter | undefined, _sourceMapGenerator: SourceMapGenerator | undefined) {}
    function reset() {}
    function getCurrentLineMap() {}
    

    function emit(node: Node): Node;
    function emit(node: Node | undefined): Node | undefined;
    function emit(node: Node | undefined) {
        if (node === undefined) return;

        const prevSourceFileTextKind = recordBundleFileInternalSectionStart(node);
        const substitute = pipelineEmit(EmitHint.Unspecified, node);
        recordBundleFileInternalSectionEnd(prevSourceFileTextKind);
        return substitute;
    }

    function emitIdentifierName(node: Identifier): Node;
    function emitIdentifierName(node: Identifier | undefined): Node | undefined;
    function emitIdentifierName(node: Identifier | undefined): Node | undefined {
    }

    function emitExpression(node: Expression): Node;
    function emitExpression(node: Expression | undefined): Node | undefined;
    function emitExpression(node: Expression | undefined): Node | undefined {
    }

    function pipelineEmit(emitHint: EmitHint, node: Node) {}
    function getPipelinePhase(phase: PipelinePhase, node: Node) {}
    function getNextPipelinePhase(currentPhase: PipelinePhase, node: Node) {
        return getPipelinePhase(currentPhase + 1, node);
    }
    function pipelineEmitWithNotification(hint: EmitHint, node: Node) {}
    function pipelineEmitWithHint(hint: EmitHint, node: Node): void {}

}
```

## 内部函数

### getSourceMapFilePath
```ts
function getSourceMapFilePath(
    jsFilePath: string, 
    options: CompilerOptions) {
    return (options.sourceMap && !options.inlineSourceMap) 
        ? jsFilePath + ".map" 
        : undefined;
}
```

### rootDirOfOptions
```ts
function rootDirOfOptions(configFile: ParsedCommandLine) {
    ...
}
```

### getOutputPathWithoutChangingExt
```ts
function getOutputPathWithoutChangingExt(
    inputFileName: string, 
    configFile: ParsedCommandLine, 
    ignoreCase: boolean, 
    outputDir: string | undefined) {
    ... 
}
```

### getOutputJSFileName
```ts
function getOutputJSFileName(
    inputFileName: string, 
    configFile: ParsedCommandLine, 
    ignoreCase: boolean) {

}
```

### createAddOutput
```ts
function createAddOutput() {
    let outputs: string[] | undefined;
    return { addOutput, getOutputs };
    function addOutput(path: string | undefined) {
        if (path) {
            (outputs || (outputs = [])).push(path);
        }
    }
    function getOutputs(): readonly string[] {
        return outputs || emptyArray;
    }
}
```

### getSingleOutputFileNames
```ts
function getSingleOutputFileNames(configFile: ParsedCommandLine, addOutput: ReturnType<typeof createAddOutput>["addOutput"]) {
    const { jsFilePath, sourceMapFilePath, declarationFilePath, declarationMapPath, buildInfoPath } = getOutputPathsForBundle(configFile.options, /*forceDtsPaths*/ false);
    addOutput(jsFilePath);
    addOutput(sourceMapFilePath);
    addOutput(declarationFilePath);
    addOutput(declarationMapPath);
    addOutput(buildInfoPath);
}
```

### getOwnOutputFileNames
```ts
function getOwnOutputFileNames(
    configFile: ParsedCommandLine, 
    inputFileName: string, 
    ignoreCase: boolean, 
    addOutput: ReturnType<typeof createAddOutput>["addOutput"]) {
    ...
}
```

### createSourceFilesFromBundleBuildInfo
```ts
function createSourceFilesFromBundleBuildInfo(
    bundle: BundleBuildInfo,
    buildInfoDirectory: string, 
    host: EmitUsingBuildInfoHost): readonly SourceFile[] {
    ...
}
```


### createBracketsMap
```ts
function createBracketsMap() {
    const brackets: string[][] = [];
    brackets[ListFormat.Braces] = ["{", "}"];
    brackets[ListFormat.Parenthesis] = ["(", ")"];
    brackets[ListFormat.AngleBrackets] = ["<", ">"];
    brackets[ListFormat.SquareBrackets] = ["[", "]"];
    return brackets;
}
```

### getOpeningBracket
```ts
function getOpeningBracket(format: ListFormat) {
    return brackets[format & ListFormat.BracketsMask][0];
}
```

### getClosingBracket
```ts
function getClosingBracket(format: ListFormat) {
    return brackets[format & ListFormat.BracketsMask][1];
}
```