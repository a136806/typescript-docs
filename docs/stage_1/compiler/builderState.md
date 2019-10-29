## 类型接口
### EmitOutput
```ts
export interface EmitOutput {
    outputFiles: OutputFile[];
    emitSkipped: boolean;
    exportedModulesFromDeclarationEmit?: ExportedModulesFromDeclarationEmit;
}

```
### OutputFile
```ts
export interface OutputFile {
    name: string;
    writeByteOrderMark: boolean;
    text: string;
}
```

### ReusableBuilderState
```ts
export interface ReusableBuilderState {
    fileInfos: ReadonlyMap<BuilderState.FileInfo>;
    readonly referencedMap?: ReadonlyMap<BuilderState.ReferencedSet> | undefined;
    readonly exportedModulesMap?: ReadonlyMap<BuilderState.ReferencedSet> | undefined;
}
```

### BuilderState
```ts
export interface BuilderState {
    fileInfos: Map<BuilderState.FileInfo>;
    readonly referencedMap: ReadonlyMap<BuilderState.ReferencedSet> | undefined;
    readonly exportedModulesMap: Map<BuilderState.ReferencedSet> | undefined;
    hasCalledUpdateShapeSignature: Map<true>;
    allFilesExcludingDefaultLibraryFile?: readonly SourceFile[];
    allFileNames?: readonly string[];
}
```

### FileInfo
```ts
export interface FileInfo {
    readonly version: string;
    signature: string | undefined;
}
```

### ReferencedSet
```ts
export type ReferencedSet = ReadonlyMap<true>;
```

### ComputeHash
```ts
export type ComputeHash = (data: string) => string;
```

### ComputingExportedModulesMap
```ts
export type ComputingExportedModulesMap = Map<ReferencedSet | false>;
```


## 实例对象
## 功能函数
### getFileEmitOutput
```ts
export function getFileEmitOutput(
    program: Program, 
    sourceFile: SourceFile, 
    emitOnlyDtsFiles: boolean,
    cancellationToken?: CancellationToken, 
    customTransformers?: CustomTransformers, 
    forceDtsEmit?: boolean): EmitOutput {
    const outputFiles: OutputFile[] = [];
    const emitResult = program.emit(sourceFile, writeFile, cancellationToken, emitOnlyDtsFiles, customTransformers, forceDtsEmit);
    return { outputFiles, emitSkipped: emitResult.emitSkipped, exportedModulesFromDeclarationEmit: emitResult.exportedModulesFromDeclarationEmit };

    function writeFile(fileName: string, text: string, writeByteOrderMark: boolean) {
        outputFiles.push({ name: fileName, writeByteOrderMark, text });
    }
}
```

### cloneMapOrUndefined
```ts
export function cloneMapOrUndefined<T>(map: ReadonlyMap<T> | undefined) {
    return map ? cloneMap(map) : undefined;
}
```

### canReuseOldState
```ts
export function canReuseOldState(
    newReferencedMap: ReadonlyMap<ReferencedSet> | undefined, 
    oldState: Readonly<ReusableBuilderState> | undefined) {
    
    return oldState && !oldState.referencedMap === !newReferencedMap;
}
```

### create
```ts
export function create(
    newProgram: Program, 
    getCanonicalFileName: GetCanonicalFileName, 
    oldState?: Readonly<ReusableBuilderState>): BuilderState {
    ...

    return {
        fileInfos,
        referencedMap,
        exportedModulesMap,
        hasCalledUpdateShapeSignature
    };
}
```

### releaseCache
```ts
export function releaseCache(state: BuilderState) {
    state.allFilesExcludingDefaultLibraryFile = undefined;
    state.allFileNames = undefined;
}
```

### clone
```ts
export function clone(state: Readonly<BuilderState>): BuilderState {
    const fileInfos = createMap<FileInfo>();
    state.fileInfos.forEach((value, key) => {
        fileInfos.set(key, { ...value });
    });

    return {
        fileInfos,
        referencedMap: cloneMapOrUndefined(state.referencedMap),
        exportedModulesMap: cloneMapOrUndefined(state.exportedModulesMap),
        hasCalledUpdateShapeSignature: cloneMap(state.hasCalledUpdateShapeSignature),
    };
}
```

### getFilesAffectedBy
```ts
export function getFilesAffectedBy(
    state: BuilderState, 
    programOfThisState: Program,
    path: Path, 
    cancellationToken: CancellationToken | undefined, 
    computeHash: ComputeHash, 
    cacheToUpdateSignature?: Map<string>, 
    exportedModulesMapCache?: ComputingExportedModulesMap): readonly SourceFile[] {
     ...
}   
```

### updateSignaturesFromCache
```ts
export function updateSignaturesFromCache(
    state: BuilderState, 
    signatureCache: Map<string>) {
    signatureCache.forEach((signature, path) => {
        state.fileInfos.get(path)!.signature = signature;
        state.hasCalledUpdateShapeSignature.set(path, true);
    });
}
```

### updateShapeSignature
```ts
export function updateShapeSignature(
    state: Readonly<BuilderState>, 
    programOfThisState: Program, 
    sourceFile: SourceFile, 
    cacheToUpdateSignature: Map<string>, 
    cancellationToken: CancellationToken | undefined, 
    computeHash: ComputeHash, 
    exportedModulesMapCache?: ComputingExportedModulesMap) {
    ...
}
```

### updateExportedFilesMapFromCache
```ts
export function updateExportedFilesMapFromCache(
    state: BuilderState, 
    exportedModulesMapCache: ComputingExportedModulesMap | undefined) {
    
    if (exportedModulesMapCache) {
        Debug.assert(!!state.exportedModulesMap);
        exportedModulesMapCache.forEach((exportedModules, path) => {
            if (exportedModules) {
                state.exportedModulesMap!.set(path, exportedModules);
            }
            else {
                state.exportedModulesMap!.delete(path);
            }
        });
    }
}
```

### getAllDependencies
```ts
export function getAllDependencies(
    state: BuilderState, 
    programOfThisState: Program, 
    sourceFile: SourceFile): readonly string[] {
    ...
}
```

### getReferencedByPaths
```ts
export function getReferencedByPaths(state: Readonly<BuilderState>, referencedFilePath: Path) {
    return arrayFrom(mapDefinedIterator(state.referencedMap!.entries(), ([filePath, referencesInFile]) =>
        referencesInFile.has(referencedFilePath) ? filePath as Path : undefined
    ));
}
```

## 内部函数

### getReferencedFileFromImportedModuleSymbol
```ts
function getReferencedFileFromImportedModuleSymbol(symbol: Symbol) {
    if (symbol.declarations && symbol.declarations[0]) {
        const declarationSourceFile = getSourceFileOfNode(symbol.declarations[0]);
        return declarationSourceFile && declarationSourceFile.resolvedPath;
    }
}
```

### getReferencedFileFromImportLiteral
```ts
function getReferencedFileFromImportLiteral(
    checker: TypeChecker, 
    importName: StringLiteralLike) {
    const symbol = checker.getSymbolAtLocation(importName);
    return symbol && getReferencedFileFromImportedModuleSymbol(symbol);
}
```

### getReferencedFileFromFileName
```ts
function getReferencedFileFromFileName(
    program: Program, 
    fileName: string, 
    sourceFileDirectory: Path, 
    getCanonicalFileName: GetCanonicalFileName): Path {
    
    return toPath(program.getProjectReferenceRedirect(fileName) || fileName, sourceFileDirectory, getCanonicalFileName);
}
```

### getReferencedFiles
```ts
function getReferencedFiles(
    program: Program, 
    sourceFile: SourceFile, 
    getCanonicalFileName: GetCanonicalFileName): Map<true> | undefined {
    
    ...
}
```

### updateExportedModules
```ts
function updateExportedModules(
    sourceFile: SourceFile, 
    exportedModulesFromDeclarationEmit: ExportedModulesFromDeclarationEmit | undefined, 
    exportedModulesMapCache: ComputingExportedModulesMap) {
    ...
}
```

### getAllFileNames
```ts
function getAllFileNames(
    state: BuilderState, 
    programOfThisState: Program): readonly string[] {
    
    if (!state.allFileNames) {
        const sourceFiles = programOfThisState.getSourceFiles();
        state.allFileNames = sourceFiles === emptyArray ? emptyArray : sourceFiles.map(file => file.fileName);
    }
    return state.allFileNames;
}
```

### containsOnlyAmbientModules
```ts
function containsOnlyAmbientModules(sourceFile: SourceFile) {
    for (const statement of sourceFile.statements) {
        if (!isModuleWithStringLiteralName(statement)) {
            return false;
        }
    }
    return true;
}
```

### containsGlobalScopeAugmentation
```ts
function containsGlobalScopeAugmentation(sourceFile: SourceFile) {
    return some(sourceFile.moduleAugmentations, augmentation => isGlobalScopeAugmentation(augmentation.parent as ModuleDeclaration));
}
```

### isFileAffectingGlobalScope
```ts
function isFileAffectingGlobalScope(sourceFile: SourceFile) {
    return containsGlobalScopeAugmentation(sourceFile) ||
        !isExternalModule(sourceFile) && !containsOnlyAmbientModules(sourceFile);
}
```

### getAllFilesExcludingDefaultLibraryFile
```ts
function getAllFilesExcludingDefaultLibraryFile(
    state: BuilderState, 
    programOfThisState: Program, 
    firstSourceFile: SourceFile): readonly SourceFile[] {
    ...
}
```

### getFilesAffectedByUpdatedShapeWhenNonModuleEmit
```ts
function getFilesAffectedByUpdatedShapeWhenNonModuleEmit(
    state: BuilderState, 
    programOfThisState: Program, 
    sourceFileWithUpdatedShape: SourceFile) {
    ...
}
```

### getFilesAffectedByUpdatedShapeWhenModuleEmit
```ts
function getFilesAffectedByUpdatedShapeWhenModuleEmit(
    state: BuilderState, 
    programOfThisState: Program, 
    sourceFileWithUpdatedShape: SourceFile, 
    cacheToUpdateSignature: Map<string>, 
    cancellationToken: CancellationToken | undefined, 
    computeHash: ComputeHash | undefined,
    exportedModulesMapCache: ComputingExportedModulesMap | undefined) {
    ...
}
```