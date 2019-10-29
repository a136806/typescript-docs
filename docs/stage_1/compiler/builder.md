## 类型接口
### ReusableDiagnosticRelatedInformation
```ts
export interface ReusableDiagnosticRelatedInformation {
    category: DiagnosticCategory;
    code: number;
    file: string | undefined;
    start: number | undefined;
    length: number | undefined;
    messageText: string | ReusableDiagnosticMessageChain;
}
```
### ReusableDiagnostic
```ts
export interface ReusableDiagnostic extends ReusableDiagnosticRelatedInformation {
    reportsUnnecessary?: {};
    source?: string;
    relatedInformation?: ReusableDiagnosticRelatedInformation[];
}
```

### ReusableDiagnosticMessageChain
```ts
export type ReusableDiagnosticMessageChain = DiagnosticMessageChain;
```

### ReusableBuilderProgramState
```ts
export interface ReusableBuilderProgramState extends ReusableBuilderState {
    semanticDiagnosticsPerFile?: ReadonlyMap<readonly ReusableDiagnostic[] | readonly Diagnostic[]> | undefined;
    changedFilesSet?: ReadonlyMap<true>;
    affectedFiles?: readonly SourceFile[] | undefined;
    currentChangedFilePath?: Path | undefined;
    currentAffectedFilesSignatures?: ReadonlyMap<string> | undefined;
    currentAffectedFilesExportedModulesMap?: Readonly<BuilderState.ComputingExportedModulesMap> | undefined;
    semanticDiagnosticsFromOldState?: Map<true>;
    program?: Program | undefined;
    compilerOptions: CompilerOptions;
    affectedFilesPendingEmit?: readonly Path[] | undefined;
    affectedFilesPendingEmitKind?: ReadonlyMap<BuilderFileEmit> | undefined;
    affectedFilesPendingEmitIndex?: number | undefined;
    hasReusableDiagnostic?: true;
}   
```

### BuilderFileEmit
```ts
export const enum BuilderFileEmit {
    DtsOnly,
    Full
}
```

### BuilderProgramState
```ts
export interface BuilderProgramState extends BuilderState {
    semanticDiagnosticsPerFile: Map<readonly Diagnostic[]> | undefined;
    changedFilesSet: Map<true>;
    affectedFiles: readonly SourceFile[] | undefined;
    affectedFilesIndex: number | undefined;
    currentChangedFilePath: Path | undefined;
    currentAffectedFilesSignatures: Map<string> | undefined;
    currentAffectedFilesExportedModulesMap: BuilderState.ComputingExportedModulesMap | undefined;
    seenAffectedFiles: Map<true> | undefined;
    cleanedDiagnosticsOfLibFiles?: boolean;
    semanticDiagnosticsFromOldState?: Map<true>;
    program: Program | undefined;
    compilerOptions: CompilerOptions;
    affectedFilesPendingEmit: Path[] | undefined;
    affectedFilesPendingEmitKind: Map<BuilderFileEmit> | undefined;
    affectedFilesPendingEmitIndex: number | undefined;
    emittedBuildInfo?: boolean;
    seenEmittedFiles: Map<BuilderFileEmit> | undefined;     
    programEmitComplete?: true;
}
```
### ProgramBuildInfoDiagnostic
```ts
export type ProgramBuildInfoDiagnostic 
    = string 
    | [string, readonly ReusableDiagnostic[]];
```
### ProgramBuildInfo
```ts
export interface ProgramBuildInfo {
    fileInfos: MapLike<BuilderState.FileInfo>;
    options: CompilerOptions;
    referencedMap?: MapLike<string[]>;
    exportedModulesMap?: MapLike<string[]>;
    semanticDiagnosticsPerFile?: ProgramBuildInfoDiagnostic[];
}
```

### BuilderProgramKind
```ts
export enum BuilderProgramKind {
    SemanticDiagnosticsBuilderProgram,
    EmitAndSemanticDiagnosticsBuilderProgram
}
```

### BuilderCreationParameters
```ts
export interface BuilderCreationParameters {
    newProgram: Program;
    host: BuilderProgramHost;
    oldProgram: BuilderProgram | undefined;
    configFileParsingDiagnostics: readonly Diagnostic[];
}
```

### AffectedFileResult
```ts
export type AffectedFileResult<T> 
    = { result: T; affected: SourceFile | Program; } 
    | undefined;
```

### BuilderProgramHost
```ts
export interface BuilderProgramHost {
    useCaseSensitiveFileNames(): boolean;
    createHash?: (data: string) => string;
    writeFile?: WriteFileCallback;
}
```

### BuilderProgram
```ts
export interface BuilderProgram {
    getState(): ReusableBuilderProgramState;
    backupState(): void;
    restoreState(): void;
    getProgram(): Program;
    getProgramOrUndefined(): Program | undefined;
    releaseProgram(): void;
    getCompilerOptions(): CompilerOptions;
    getSourceFile(fileName: string): SourceFile | undefined;
    getSourceFiles(): readonly SourceFile[];
    getOptionsDiagnostics(cancellationToken?: CancellationToken): readonly Diagnostic[];
    getGlobalDiagnostics(cancellationToken?: CancellationToken): readonly Diagnostic[];
    getConfigFileParsingDiagnostics(): readonly Diagnostic[];
    getSyntacticDiagnostics(sourceFile?: SourceFile, cancellationToken?: CancellationToken): readonly Diagnostic[];
    getDeclarationDiagnostics(sourceFile?: SourceFile, cancellationToken?: CancellationToken): readonly DiagnosticWithLocation[];
    getAllDependencies(sourceFile: SourceFile): readonly string[];
    getSemanticDiagnostics(sourceFile?: SourceFile, cancellationToken?: CancellationToken): readonly Diagnostic[];
    emit(targetSourceFile?: SourceFile, writeFile?: WriteFileCallback, cancellationToken?: CancellationToken, emitOnlyDtsFiles?: boolean, customTransformers?: CustomTransformers): EmitResult;
    getCurrentDirectory(): string;
}
```

### SemanticDiagnosticsBuilderProgram
```ts
export interface SemanticDiagnosticsBuilderProgram extends BuilderProgram {
    getSemanticDiagnosticsOfNextAffectedFile(
        cancellationToken?: CancellationToken, 
        ignoreSourceFile?: (sourceFile: SourceFile) => boolean
    ): AffectedFileResult<readonly Diagnostic[]>;
}
```

### EmitAndSemanticDiagnosticsBuilderProgram
```ts
export interface EmitAndSemanticDiagnosticsBuilderProgram extends SemanticDiagnosticsBuilderProgram {
    emitNextAffectedFile(
         writeFile?: WriteFileCallback, 
         cancellationToken?: CancellationToken, 
         emitOnlyDtsFiles?: boolean, 
         customTransformers?: CustomTransformers
    ): AffectedFileResult<EmitResult>;
}
```

## 实例对象
## 功能函数

### getBuilderCreationParameters
```ts
export function getBuilderCreationParameters(
    newProgramOrRootNames: Program | readonly string[] | undefined, hostOrOptions: BuilderProgramHost | CompilerOptions | undefined, oldProgramOrHost?: BuilderProgram | CompilerHost, configFileParsingDiagnosticsOrOldProgram?: readonly Diagnostic[] | BuilderProgram, configFileParsingDiagnostics?: readonly Diagnostic[], projectReferences?: readonly ProjectReference[]
): BuilderCreationParameters {
    ...
}
```

### createBuilderProgram
```ts
export function createBuilderProgram(
    kind: BuilderProgramKind.SemanticDiagnosticsBuilderProgram, builderCreationParameters: BuilderCreationParameters
): SemanticDiagnosticsBuilderProgram;

export function createBuilderProgram(
    kind: BuilderProgramKind.EmitAndSemanticDiagnosticsBuilderProgram, builderCreationParameters: BuilderCreationParameters
): EmitAndSemanticDiagnosticsBuilderProgram;

export function createBuilderProgram(
    kind: BuilderProgramKind, 
    { newProgram, host, oldProgram, configFileParsingDiagnostics }: BuilderCreationParameters
) {
}
```

### createBuildProgramUsingProgramBuildInfo
```ts
export function createBuildProgramUsingProgramBuildInfo(
    program: ProgramBuildInfo, 
    buildInfoPath: string, 
    host: ReadBuildProgramHost
): EmitAndSemanticDiagnosticsBuilderProgram {

}
```

### createRedirectedBuilderProgram
```ts
export function createRedirectedBuilderProgram(
    state: { program: Program | undefined; compilerOptions: CompilerOptions; }, configFileParsingDiagnostics: readonly Diagnostic[]
): BuilderProgram {

}
```

### createSemanticDiagnosticsBuilderProgram
```ts
export function createSemanticDiagnosticsBuilderProgram(
    newProgram: Program, 
    host: BuilderProgramHost, 
    oldProgram?: SemanticDiagnosticsBuilderProgram, 
    configFileParsingDiagnostics?: readonly Diagnostic[]
): SemanticDiagnosticsBuilderProgram;

export function createSemanticDiagnosticsBuilderProgram(
    rootNames: readonly string[] | undefined, 
    options: CompilerOptions | undefined, 
    host?: CompilerHost, 
    oldProgram?: SemanticDiagnosticsBuilderProgram,
    configFileParsingDiagnostics?: readonly Diagnostic[], 
    projectReferences?: readonly ProjectReference[]
): SemanticDiagnosticsBuilderProgram;

export function createSemanticDiagnosticsBuilderProgram(
    newProgramOrRootNames: Program | readonly string[] | undefined, hostOrOptions: BuilderProgramHost | CompilerOptions | undefined, oldProgramOrHost?: CompilerHost | SemanticDiagnosticsBuilderProgram, configFileParsingDiagnosticsOrOldProgram?: readonly Diagnostic[] | SemanticDiagnosticsBuilderProgram, 
    configFileParsingDiagnostics?: readonly Diagnostic[], 
    projectReferences?: readonly ProjectReference[]
) {
    return createBuilderProgram(BuilderProgramKind.SemanticDiagnosticsBuilderProgram, getBuilderCreationParameters(newProgramOrRootNames, hostOrOptions, oldProgramOrHost, configFileParsingDiagnosticsOrOldProgram, configFileParsingDiagnostics, projectReferences));
}
```

### createEmitAndSemanticDiagnosticsBuilderProgram
```ts
export function createEmitAndSemanticDiagnosticsBuilderProgram(
    newProgram: Program, 
    host: BuilderProgramHost, 
    oldProgram?: EmitAndSemanticDiagnosticsBuilderProgram, configFileParsingDiagnostics?: readonly Diagnostic[]
): EmitAndSemanticDiagnosticsBuilderProgram;

export function createEmitAndSemanticDiagnosticsBuilderProgram(
    rootNames: readonly string[] | undefined, 
    options: CompilerOptions | undefined, 
    host?: CompilerHost, 
    oldProgram?: EmitAndSemanticDiagnosticsBuilderProgram, configFileParsingDiagnostics?: readonly Diagnostic[], 
    projectReferences?: readonly ProjectReference[]
): EmitAndSemanticDiagnosticsBuilderProgram;


export function createEmitAndSemanticDiagnosticsBuilderProgram(
    newProgramOrRootNames: Program | readonly string[] | undefined, hostOrOptions: BuilderProgramHost | CompilerOptions | undefined, oldProgramOrHost?: CompilerHost | EmitAndSemanticDiagnosticsBuilderProgram, configFileParsingDiagnosticsOrOldProgram?: readonly Diagnostic[] | EmitAndSemanticDiagnosticsBuilderProgram, 
    configFileParsingDiagnostics?: readonly Diagnostic[], 
    projectReferences?: readonly ProjectReference[]
) {
    return createBuilderProgram(BuilderProgramKind.EmitAndSemanticDiagnosticsBuilderProgram, getBuilderCreationParameters(newProgramOrRootNames, hostOrOptions, oldProgramOrHost, configFileParsingDiagnosticsOrOldProgram, configFileParsingDiagnostics, projectReferences));
}
```

### createAbstractBuilder
```ts
export function createAbstractBuilder(
    newProgram: Program, 
    host: BuilderProgramHost, 
    oldProgram?: BuilderProgram, 
    configFileParsingDiagnostics?: readonly Diagnostic[]
): BuilderProgram;

export function createAbstractBuilder(
    rootNames: readonly string[] | undefined, 
    options: CompilerOptions | undefined, 
    host?: CompilerHost, 
    oldProgram?: BuilderProgram, 
    configFileParsingDiagnostics?: readonly Diagnostic[], 
    projectReferences?: readonly ProjectReference[]
): BuilderProgram;

export function createAbstractBuilder(
    newProgramOrRootNames: Program | readonly string[] | undefined, hostOrOptions: BuilderProgramHost | CompilerOptions | undefined, oldProgramOrHost?: CompilerHost | BuilderProgram, configFileParsingDiagnosticsOrOldProgram?: readonly Diagnostic[] | BuilderProgram, 
    configFileParsingDiagnostics?: readonly Diagnostic[], 
    projectReferences?: readonly ProjectReference[]
): BuilderProgram {
    const { newProgram, configFileParsingDiagnostics: newConfigFileParsingDiagnostics } = getBuilderCreationParameters(newProgramOrRootNames, hostOrOptions, oldProgramOrHost, configFileParsingDiagnosticsOrOldProgram, configFileParsingDiagnostics, projectReferences);
    
    return createRedirectedBuilderProgram({ program: newProgram, compilerOptions: newProgram.getCompilerOptions() }, newConfigFileParsingDiagnostics);
}
```

## 内部函数

###  hasSameKeys
```ts
function hasSameKeys<T, U>(
    map1: ReadonlyMap<T> | undefined, 
    map2: ReadonlyMap<U> | undefined): boolean {

    return map1 as ReadonlyMap<T | U> === map2 
        || map1 !== undefined && map2 !== undefined && map1.size === map2.size && !forEachKey(map1, key => !map2.has(key));
}
```

### createBuilderProgramState
```ts
function createBuilderProgramState(
    newProgram: Program, 
    getCanonicalFileName: GetCanonicalFileName, 
    oldState?: Readonly<ReusableBuilderProgramState>): BuilderProgramState {
    
    ...
}
```

### convertToDiagnostics
```ts
function convertToDiagnostics(
    diagnostics: readonly ReusableDiagnostic[], 
    newProgram: Program, 
    getCanonicalFileName: GetCanonicalFileName): readonly Diagnostic[] {

}
```

### convertToDiagnosticRelatedInformation
```ts
function convertToDiagnosticRelatedInformation(
    diagnostic: ReusableDiagnosticRelatedInformation, 
    newProgram: Program, 
    toPath: (path: string) => Path): DiagnosticRelatedInformation {

}
```

### releaseCache
```ts
function releaseCache(state: BuilderProgramState) {
    BuilderState.releaseCache(state);
    state.program = undefined;
}
```

### cloneBuilderProgramState
```ts
function cloneBuilderProgramState(state: Readonly<BuilderProgramState>): BuilderProgramState {

}
```

### assertSourceFileOkWithoutNextAffectedCall
```ts
function assertSourceFileOkWithoutNextAffectedCall(state: BuilderProgramState, sourceFile: SourceFile | undefined) {
    Debug.assert(!sourceFile || !state.affectedFiles || state.affectedFiles[state.affectedFilesIndex! - 1] !== sourceFile || !state.semanticDiagnosticsPerFile!.has(sourceFile.path));
}
```

### getNextAffectedFile
```ts
function getNextAffectedFile(
    state: BuilderProgramState, 
    cancellationToken: CancellationToken | undefined, 
    computeHash: BuilderState.ComputeHash): SourceFile | Program | undefined {
    ...
}
```

### getNextAffectedFilePendingEmit
```ts
function getNextAffectedFilePendingEmit(state: BuilderProgramState) {

}
```

### handleDtsMayChangeOfAffectedFile
```ts
function handleDtsMayChangeOfAffectedFile(
    state: BuilderProgramState, 
    affectedFile: SourceFile, 
    cancellationToken: CancellationToken | undefined, 
    computeHash: BuilderState.ComputeHash) {
    ...
}
```

### handleDtsMayChangeOf
```ts
function handleDtsMayChangeOf(
    state: BuilderProgramState, 
    path: Path, 
    cancellationToken: CancellationToken | undefined, 
    computeHash: BuilderState.ComputeHash) {
    ...
}
```

### removeSemanticDiagnosticsOf
```ts
function removeSemanticDiagnosticsOf(state: BuilderProgramState, path: Path) {
    if (!state.semanticDiagnosticsFromOldState) {
        return true;
    }
    state.semanticDiagnosticsFromOldState.delete(path);
    state.semanticDiagnosticsPerFile!.delete(path);
    return !state.semanticDiagnosticsFromOldState.size;
}
```

### isChangedSignagure
```ts
function isChangedSignagure(state: BuilderProgramState, path: Path) {
    const newSignature = Debug.assertDefined(state.currentAffectedFilesSignatures).get(path);
    const oldSignagure = Debug.assertDefined(state.fileInfos.get(path)).signature;
    return newSignature !== oldSignagure;
}
```

### forEachReferencingModulesOfExportOfAffectedFile
```ts
function forEachReferencingModulesOfExportOfAffectedFile(
    state: BuilderProgramState, 
    affectedFile: SourceFile, 
    fn: (state: BuilderProgramState, filePath: Path) => boolean) {
    ...
}
```

### forEachFilesReferencingPath
```ts
function forEachFilesReferencingPath(
    state: BuilderProgramState, 
    referencedPath: Path, 
    seenFileAndExportsOfFile: Map<true>, 
    fn: (state: BuilderProgramState, filePath: Path) => boolean) {
        return forEachEntry(state.referencedMap!, (referencesInFile, filePath) =>
            referencesInFile.has(referencedPath) && forEachFileAndExportsOfFile(state, filePath as Path, seenFileAndExportsOfFile, fn)
        );
    }
```

### forEachFileAndExportsOfFile
```ts
function forEachFileAndExportsOfFile(
    state: BuilderProgramState, 
    filePath: Path, 
    seenFileAndExportsOfFile: Map<true>, 
    fn: (state: BuilderProgramState, filePath: Path) => boolean
): boolean {

}
```

### doneWithAffectedFile
```ts
function doneWithAffectedFile(
    state: BuilderProgramState,
    affected: SourceFile | Program,
    emitKind?: BuilderFileEmit,
    isPendingEmit?: boolean,
    isBuildInfoEmit?: boolean
) {
    ...
}
```

### toAffectedFileResult
```ts
function toAffectedFileResult<T>(
    state: BuilderProgramState, 
    result: T, 
    affected: SourceFile | Program
): AffectedFileResult<T> {
    doneWithAffectedFile(state, affected);
    return { result, affected };
}
```

### toAffectedFileEmitResult
```ts
function toAffectedFileEmitResult(
    state: BuilderProgramState,
    result: EmitResult,
    affected: SourceFile | Program,
    emitKind: BuilderFileEmit,
    isPendingEmit?: boolean,
    isBuildInfoEmit?: boolean
): AffectedFileResult<EmitResult> {
    doneWithAffectedFile(state, affected, emitKind, isPendingEmit, isBuildInfoEmit);
    return { result, affected };
}
```

### getSemanticDiagnosticsOfFile
```ts
function getSemanticDiagnosticsOfFile(
    state: BuilderProgramState, 
    sourceFile: SourceFile, 
    cancellationToken?: CancellationToken
): readonly Diagnostic[] {
    ...
}
```

### getProgramBuildInfo
```ts
function getProgramBuildInfo(
    state: Readonly<ReusableBuilderProgramState>, 
    getCanonicalFileName: GetCanonicalFileName
): ProgramBuildInfo | undefined {
    ...
}
```

### convertToReusableCompilerOptions
```ts
function convertToReusableCompilerOptions(
    options: CompilerOptions, 
    relativeToBuildInfo: (path: string) => string
) {
}
```

### convertToReusableCompilerOptionValue
```ts
function convertToReusableCompilerOptionValue(
    option: CommandLineOption | undefined, 
    value: CompilerOptionsValue, 
    relativeToBuildInfo: (path: string) => string
) {
}
```

### convertToReusableDiagnostics
```ts
function convertToReusableDiagnostics(
    diagnostics: readonly Diagnostic[], 
    relativeToBuildInfo: (path: string) => string
): readonly ReusableDiagnostic[] {
}
```

### convertToReusableDiagnosticRelatedInformation
```ts
function convertToReusableDiagnosticRelatedInformation(
    diagnostic: DiagnosticRelatedInformation, 
    relativeToBuildInfo: (path: string) => string
): ReusableDiagnosticRelatedInformation {

}
```

### getMapOfReferencedSet
```ts
function getMapOfReferencedSet(
    mapLike: MapLike<readonly string[]> | undefined, 
    toPath: (path: string) => Path
): ReadonlyMap<BuilderState.ReferencedSet> | undefined {

}
```

### convertFromReusableCompilerOptions
```ts
function convertFromReusableCompilerOptions(
    options: CompilerOptions, 
    toAbsolutePath: (path: string) => string) {

}
```

### convertFromReusableCompilerOptionValue
```ts
function convertFromReusableCompilerOptionValue(
    option: CommandLineOption | undefined, 
    value: CompilerOptionsValue, 
    toAbsolutePath: (path: string) => string) {
    
}
```

