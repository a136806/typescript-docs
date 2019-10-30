## 类型接口
### UpToDateStatusType
```ts
export enum UpToDateStatusType {
    Unbuildable,
    UpToDate,
    UpToDateWithUpstreamTypes,
    OutOfDateWithPrepend,
    OutputMissing,
    OutOfDateWithSelf,
    OutOfDateWithUpstream,
    UpstreamOutOfDate,
    UpstreamBlocked,
    ComputingUpstream,
    TsVersionOutputOfDate,
    ContainerOnly
}
```
### UpToDateStatus
```ts
export type UpToDateStatus =
    | Status.Unbuildable
    | Status.UpToDate
    | Status.OutOfDateWithPrepend
    | Status.OutputMissing
    | Status.OutOfDateWithSelf
    | Status.OutOfDateWithUpstream
    | Status.UpstreamOutOfDate
    | Status.UpstreamBlocked
    | Status.ComputingUpstream
    | Status.TsVersionOutOfDate
    | Status.ContainerOnly;
```

### Unbuildable
```ts
export interface Unbuildable {
    type: UpToDateStatusType.Unbuildable;
    reason: string;
}
```

### ContainerOnly
```ts
export interface ContainerOnly {
    type: UpToDateStatusType.ContainerOnly;
}
```

### UpToDate
```ts
export interface UpToDate {
    type: UpToDateStatusType.UpToDate | UpToDateStatusType.UpToDateWithUpstreamTypes;
    newestInputFileTime?: Date;
    newestInputFileName?: string;
    newestDeclarationFileContentChangedTime?: Date;
    newestOutputFileTime?: Date;
    newestOutputFileName?: string;
    oldestOutputFileName: string;
}
```

### OutOfDateWithPrepend
```ts
export interface OutOfDateWithPrepend {
    type: UpToDateStatusType.OutOfDateWithPrepend;
    outOfDateOutputFileName: string;
    newerProjectName: string;
}
```

### OutputMissing
```ts
export interface OutputMissing {
    type: UpToDateStatusType.OutputMissing;
    missingOutputFileName: string;
}
```

### OutOfDateWithSelf
```ts
export interface OutOfDateWithSelf {
    type: UpToDateStatusType.OutOfDateWithSelf;
    outOfDateOutputFileName: string;
    newerInputFileName: string;
}
```

### UpstreamOutOfDate
```ts
export interface UpstreamOutOfDate {
    type: UpToDateStatusType.UpstreamOutOfDate;
    upstreamProjectName: string;
}
```

### UpstreamBlocked
```ts
export interface UpstreamBlocked {
    type: UpToDateStatusType.UpstreamBlocked;
    upstreamProjectName: string;
    upstreamProjectBlocked: boolean;
}
```

### ComputingUpstream
```ts
export interface ComputingUpstream {
    type: UpToDateStatusType.ComputingUpstream;
}
```

### TsVersionOutOfDate
```ts
export interface TsVersionOutOfDate {
    type: UpToDateStatusType.TsVersionOutputOfDate;
    version: string;
}
```

### OutOfDateWithUpstream
```ts
export interface OutOfDateWithUpstream {
    type: UpToDateStatusType.OutOfDateWithUpstream;
    outOfDateOutputFileName: string;
    newerProjectName: string;
}
```

### BuildOptions
```ts
export interface BuildOptions {
    dry?: boolean;
    force?: boolean;
    verbose?: boolean;

    /*@internal*/ clean?: boolean;
    /*@internal*/ watch?: boolean;
    /*@internal*/ help?: boolean;

    /*@internal*/ preserveWatchOutput?: boolean;
    /*@internal*/ listEmittedFiles?: boolean;
    /*@internal*/ listFiles?: boolean;
    /*@internal*/ pretty?: boolean;
    incremental?: boolean;

    traceResolution?: boolean;
    /* @internal */ diagnostics?: boolean;
    /* @internal */ extendedDiagnostics?: boolean;
    /* @internal */ locale?: string;
    /* @internal */ generateCpuProfile?: string;

    [option: string]: CompilerOptionsValue | undefined;
}
```

### BuildResultFlags
```ts
enum BuildResultFlags {
    None = 0,

    Success = 1 << 0,
    DeclarationOutputUnchanged = 1 << 1,

    ConfigFileErrors = 1 << 2,
    SyntaxErrors = 1 << 3,
    TypeErrors = 1 << 4,
    DeclarationEmitErrors = 1 << 5,
    EmitErrors = 1 << 6,

    AnyErrors = ConfigFileErrors 
        | SyntaxErrors 
        | TypeErrors 
        | DeclarationEmitErrors 
        | EmitErrors
}
```

### ResolvedConfigFilePath
```ts
export type ResolvedConfigFilePath = ResolvedConfigFileName & Path;
```

### FileMap
```ts
interface FileMap<T, U extends Path = Path> extends Map<T> {
    get(key: U): T | undefined;
    has(key: U): boolean;
    forEach(action: (value: T, key: U) => void): void;
    readonly size: number;
    keys(): Iterator<U>;
    values(): Iterator<T>;
    entries(): Iterator<[U, T]>;
    set(key: U, value: T): this;
    delete(key: U): boolean;
    clear(): void;
}
```

### ConfigFileMap
```ts
type ConfigFileMap<T> = FileMap<T, ResolvedConfigFilePath>;
```

### ReportEmitErrorSummary
```ts
export type ReportEmitErrorSummary = (errorCount: number) => void;
```

### SolutionBuilderHostBase
```ts
export interface SolutionBuilderHostBase<T extends BuilderProgram> extends ProgramHost<T> {
    createDirectory?(path: string): void;

    writeFile?(path: string, data: string, writeByteOrderMark?: boolean): void;

    getModifiedTime(fileName: string): Date | undefined;
    setModifiedTime(fileName: string, date: Date): void;
    deleteFile(fileName: string): void;
    getParsedCommandLine?(fileName: string): ParsedCommandLine | undefined;


    reportDiagnostic: DiagnosticReporter;
    reportSolutionBuilderStatus: DiagnosticReporter;
    afterProgramEmitAndDiagnostics?(program: T): void;
    now?(): Date;
}    
```

### SolutionBuilderHost
```ts
export interface SolutionBuilderHost<T extends BuilderProgram> extends SolutionBuilderHostBase<T> {
    reportErrorSummary?: ReportEmitErrorSummary;
}
```

### SolutionBuilderWithWatchHost 
```ts
export interface SolutionBuilderWithWatchHost<T extends BuilderProgram> extends SolutionBuilderHostBase<T>, WatchHost {
}
```

### BuildOrder
```ts
export type BuildOrder = readonly ResolvedConfigFileName[];
```

### CircularBuildOrder
```ts
export interface CircularBuildOrder {
    buildOrder: BuildOrder;
    circularDiagnostics: readonly Diagnostic[];
}
```

### AnyBuildOrder
```ts
export type AnyBuildOrder = BuildOrder | CircularBuildOrder;
```


### SolutionBuilder
```ts
export interface SolutionBuilder<T extends BuilderProgram> {
    build(project?: string, cancellationToken?: CancellationToken): ExitStatus;
    clean(project?: string): ExitStatus;
    buildReferences(project: string, cancellationToken?: CancellationToken): ExitStatus;
    cleanReferences(project?: string): ExitStatus;
    getNextInvalidatedProject(cancellationToken?: CancellationToken): InvalidatedProject<T> | undefined;

    // Currently used for testing but can be made public if needed:
    /*@internal*/ getBuildOrder(): AnyBuildOrder;

    // Testing only
    /*@internal*/ getUpToDateStatusOfProject(project: string): UpToDateStatus;
    /*@internal*/ invalidateProject(configFilePath: ResolvedConfigFilePath, reloadLevel?: ConfigFileProgramReloadLevel): void;
    /*@internal*/ buildNextInvalidatedProject(): void;
    /*@internal*/ getAllParsedConfigs(): readonly ParsedCommandLine[];
}
```

### ConfigFileCacheEntry
```ts
type ConfigFileCacheEntry = ParsedCommandLine | Diagnostic;
```
### SolutionBuilderStateCache
```ts
interface SolutionBuilderStateCache {
    originalReadFile: CompilerHost["readFile"];
    originalFileExists: CompilerHost["fileExists"];
    originalDirectoryExists: CompilerHost["directoryExists"];
    originalCreateDirectory: CompilerHost["createDirectory"];
    originalWriteFile: CompilerHost["writeFile"] | undefined;
    originalReadFileWithCache: CompilerHost["readFile"];
    originalGetSourceFile: CompilerHost["getSourceFile"];
}
```

### SolutionBuilderState
```ts
interface SolutionBuilderState<T extends BuilderProgram = BuilderProgram> {
    readonly host: SolutionBuilderHost<T>;
    readonly hostWithWatch: SolutionBuilderWithWatchHost<T>;
    readonly currentDirectory: string;
    readonly getCanonicalFileName: GetCanonicalFileName;
    readonly parseConfigFileHost: ParseConfigFileHost;
    readonly writeFileName: ((s: string) => void) | undefined;


    readonly options: BuildOptions;
    readonly baseCompilerOptions: CompilerOptions;
    readonly rootNames: readonly string[];

    readonly resolvedConfigFilePaths: Map<ResolvedConfigFilePath>;
    readonly configFileCache: ConfigFileMap<ConfigFileCacheEntry>;


    readonly projectStatus: ConfigFileMap<UpToDateStatus>;
    readonly buildInfoChecked: ConfigFileMap<true>;
    readonly extendedConfigCache: Map<ExtendedConfigCacheEntry>;

    readonly builderPrograms: ConfigFileMap<T>;
    readonly diagnostics: ConfigFileMap<readonly Diagnostic[]>;
    readonly projectPendingBuild: ConfigFileMap<ConfigFileProgramReloadLevel>;
    readonly projectErrorsReported: ConfigFileMap<true>;

    readonly compilerHost: CompilerHost;
    readonly moduleResolutionCache: ModuleResolutionCache | undefined;


    // Mutable state
    buildOrder: AnyBuildOrder | undefined;
    readFileWithCache: (f: string) => string | undefined;
    projectCompilerOptions: CompilerOptions;
    cache: SolutionBuilderStateCache | undefined;
    allProjectBuildPending: boolean;
    needsSummary: boolean;
    watchAllProjectsPending: boolean;
    currentInvalidatedProject: InvalidatedProject<T> | undefined;


    // Watch state
    readonly watch: boolean;
    readonly allWatchedWildcardDirectories: ConfigFileMap<Map<WildcardDirectoryWatcher>>;
    readonly allWatchedInputFiles: ConfigFileMap<Map<FileWatcher>>;
    readonly allWatchedConfigFiles: ConfigFileMap<FileWatcher>;


    timerToBuildInvalidatedProject: any;
    reportFileChangeDetected: boolean;
    watchFile: WatchFile<WatchType, ResolvedConfigFileName>;
    watchFilePath: WatchFilePath<WatchType, ResolvedConfigFileName>;
    watchDirectory: WatchDirectory<WatchType, ResolvedConfigFileName>;
    writeLog: (s: string) => void;

}    
```

### InvalidatedProjectKind
```ts
export enum InvalidatedProjectKind {
    Build,
    UpdateBundle,
    UpdateOutputFileStamps
}
```

### InvalidatedProjectBase
```ts
export interface InvalidatedProjectBase {
    readonly kind: InvalidatedProjectKind;
    readonly project: ResolvedConfigFileName;
    /*@internal*/ readonly projectPath: ResolvedConfigFilePath;
    /*@internal*/ readonly buildOrder: readonly ResolvedConfigFileName[];
    /**
        *  To dispose this project and ensure that all the necessary actions are taken and state is updated accordingly
        */
    done(cancellationToken?: CancellationToken, writeFile?: WriteFileCallback, customTransformers?: CustomTransformers): ExitStatus;
    getCompilerOptions(): CompilerOptions;
    getCurrentDirectory(): string;
}
```

### UpdateOutputFileStampsProject
```ts
export interface UpdateOutputFileStampsProject extends InvalidatedProjectBase {
    readonly kind: InvalidatedProjectKind.UpdateOutputFileStamps;
    updateOutputFileStatmps(): void;
}
```

### BuildInvalidedProject
```ts
export interface BuildInvalidedProject<T extends BuilderProgram> extends InvalidatedProjectBase {


}
```

### UpdateBundleProject
```ts
export interface UpdateBundleProject<T extends BuilderProgram> extends InvalidatedProjectBase {
    readonly kind: InvalidatedProjectKind.UpdateBundle;
    emit(writeFile?: WriteFileCallback, customTransformers?: CustomTransformers): EmitResult | BuildInvalidedProject<T> | undefined;
}
```

### InvalidatedProject
```ts
export type InvalidatedProject<T extends BuilderProgram> 
    = UpdateOutputFileStampsProject 
    | BuildInvalidedProject<T> 
    | UpdateBundleProject<T>;
```

## 实例对象
### Base
```ts
const minimumDate = new Date(-8640000000000000);
const maximumDate = new Date(8640000000000000);
```

## 功能函数
### resolveConfigFileProjectName
```ts
export function resolveConfigFileProjectName(
    project: string
): ResolvedConfigFileName {
    if (fileExtensionIs(project, Extension.Json)) {
        return project as ResolvedConfigFileName;
    }

    return combinePaths(project, "tsconfig.json") as ResolvedConfigFileName;
}
```

### isCircularBuildOrder
```ts
export function isCircularBuildOrder(
    buildOrder: AnyBuildOrder
): buildOrder is CircularBuildOrder {
    return !!buildOrder && !!(buildOrder as CircularBuildOrder).buildOrder;
}
```

### getBuildOrderFromAnyBuildOrder
```ts
export function getBuildOrderFromAnyBuildOrder(
    anyBuildOrder: AnyBuildOrder
): BuildOrder {
    return isCircularBuildOrder(anyBuildOrder) ? anyBuildOrder.buildOrder : anyBuildOrder;
}
```

### createBuilderStatusReporter
```ts
export function createBuilderStatusReporter(
    system: System, 
    pretty?: boolean
): DiagnosticReporter {
    return diagnostic => {
        let output = pretty ? `[${formatColorAndReset(getLocaleTimeString(system), ForegroundColorEscapeSequences.Grey)}] ` : `${getLocaleTimeString(system)} - `;
        output += `${flattenDiagnosticMessageText(diagnostic.messageText, system.newLine)}${system.newLine + system.newLine}`;
        system.write(output);
    };
}
```

### createSolutionBuilderHost
```ts
export function createSolutionBuilderHost<T extends BuilderProgram = EmitAndSemanticDiagnosticsBuilderProgram>(
    system = sys, 
    createProgram?: CreateProgram<T>, 
    reportDiagnostic?: DiagnosticReporter, 
    reportSolutionBuilderStatus?: DiagnosticReporter, 
    reportErrorSummary?: ReportEmitErrorSummary
) {
    const host = createSolutionBuilderHostBase(system, createProgram, reportDiagnostic, reportSolutionBuilderStatus) as SolutionBuilderHost<T>;
    host.reportErrorSummary = reportErrorSummary;
    return host;
}
```

### createSolutionBuilderWithWatchHost
```ts
export function createSolutionBuilderWithWatchHost<T extends BuilderProgram = EmitAndSemanticDiagnosticsBuilderProgram>(
    system = sys, 
    createProgram?: CreateProgram<T>, 
    reportDiagnostic?: DiagnosticReporter, 
    reportSolutionBuilderStatus?: DiagnosticReporter, 
    reportWatchStatus?: WatchStatusReporter
) {
    const host = createSolutionBuilderHostBase(system, createProgram, reportDiagnostic, reportSolutionBuilderStatus) as SolutionBuilderWithWatchHost<T>;
    const watchHost = createWatchHost(system, reportWatchStatus);
    copyProperties(host, watchHost);
    return host;
}
```

### createSolutionBuilder
```ts
export function createSolutionBuilder<T extends BuilderProgram>(
    host: SolutionBuilderHost<T>, 
    rootNames: readonly string[], 
    defaultOptions: BuildOptions
): SolutionBuilder<T> {
    return createSolutionBuilderWorker(/*watch*/ false, host, rootNames, defaultOptions);
}
```

### createSolutionBuilderWithWatch
```ts
export function createSolutionBuilderWithWatch<T extends BuilderProgram>(
    host: SolutionBuilderWithWatchHost<T>, 
    rootNames: readonly string[], 
    defaultOptions: BuildOptions
): SolutionBuilder<T> {
    return createSolutionBuilderWorker(/*watch*/ true, host, rootNames, defaultOptions);
}
```



## 内部函数
### createConfigFileMap
```ts
function createConfigFileMap<T>(): ConfigFileMap<T> {
    return createMap() as ConfigFileMap<T>;
}
```

### getOrCreateValueFromConfigFileMap
```ts
function getOrCreateValueFromConfigFileMap<T>(configFileMap: ConfigFileMap<T>, resolved: ResolvedConfigFilePath, createT: () => T): T {
    const existingValue = configFileMap.get(resolved);
    let newValue: T | undefined;
    if (!existingValue) {
        newValue = createT();
        configFileMap.set(resolved, newValue);
    }
    return existingValue || newValue!;
}
```

### getOrCreateValueMapFromConfigFileMap
```ts
function getOrCreateValueMapFromConfigFileMap<T>(
    configFileMap: ConfigFileMap<Map<T>>, 
    resolved: ResolvedConfigFilePath): Map<T> {
    return getOrCreateValueFromConfigFileMap<Map<T>>(configFileMap, resolved, createMap);
}
```

### newer
```ts
function newer(date1: Date, date2: Date): Date {
    return date2 > date1 ? date2 : date1;
}
```

### isDeclarationFile
```ts
function isDeclarationFile(fileName: string) {
    return fileExtensionIs(fileName, Extension.Dts);
}
```

### createSolutionBuilderHostBase
```ts
function createSolutionBuilderHostBase<T extends BuilderProgram>(
    system: System, 
    createProgram: CreateProgram<T> | undefined, 
    reportDiagnostic?: DiagnosticReporter,
    reportSolutionBuilderStatus?: DiagnosticReporter) {
    ...
}
```

### getCompilerOptionsOfBuildOptions
```ts
function getCompilerOptionsOfBuildOptions(
    buildOptions: BuildOptions
): CompilerOptions {
    const result = {} as CompilerOptions;
    commonOptionsWithBuild.forEach(option => {
        if (hasProperty(buildOptions, option.name)) result[option.name] = buildOptions[option.name];
    });
    return result;
}
```

### createSolutionBuilderState
```ts
function createSolutionBuilderState<T extends BuilderProgram>(
    watch: boolean, 
    hostOrHostWithWatch: SolutionBuilderHost<T> | SolutionBuilderWithWatchHost<T>, rootNames: readonly string[], 
    options: BuildOptions
): SolutionBuilderState<T> {

}
```

### toPath
```ts
function toPath(state: SolutionBuilderState, fileName: string) {
    return ts.toPath(fileName, state.currentDirectory, state.getCanonicalFileName);
}
```

### toResolvedConfigFilePath
```ts
function toResolvedConfigFilePath(state: SolutionBuilderState, fileName: ResolvedConfigFileName): ResolvedConfigFilePath {
    const { resolvedConfigFilePaths } = state;
    const path = resolvedConfigFilePaths.get(fileName);
    if (path !== undefined) return path;

    const resolvedPath = toPath(state, fileName) as ResolvedConfigFilePath;
    resolvedConfigFilePaths.set(fileName, resolvedPath);
    return resolvedPath;
}
```

### isParsedCommandLine
```ts
function isParsedCommandLine(
    entry: ConfigFileCacheEntry
): entry is ParsedCommandLine {
    return !!(entry as ParsedCommandLine).options;
}
```

### parseConfigFile
```ts
function parseConfigFile(
    state: SolutionBuilderState, 
    configFileName: ResolvedConfigFileName, 
    configFilePath: ResolvedConfigFilePath
): ParsedCommandLine | undefined {

}
```

### resolveProjectName
```ts
function resolveProjectName(
    state: SolutionBuilderState, 
    name: string
): ResolvedConfigFileName {
    return resolveConfigFileProjectName(resolvePath(state.currentDirectory, name));
}
```

### createBuildOrder
```ts
function createBuildOrder(
    state: SolutionBuilderState, 
    roots: readonly ResolvedConfigFileName[]
): AnyBuildOrder {

}
```

### getBuildOrder
```ts
function getBuildOrder(state: SolutionBuilderState) {
    return state.buildOrder || createStateBuildOrder(state);
}
```

### createStateBuildOrder
```ts
function createStateBuildOrder(state: SolutionBuilderState) {

} 
```

### getBuildOrderFor
```ts
function getBuildOrderFor(
    state: SolutionBuilderState, 
    project: string | undefined, 
    onlyReferences: boolean | undefined
): AnyBuildOrder | undefined {

}
```

### enableCache
```ts
function enableCache(state: SolutionBuilderState) {

}
```

### disableCache
```ts
function disableCache(state: SolutionBuilderState) {
}
```

### clearProjectStatus
```ts
function clearProjectStatus(state: SolutionBuilderState, resolved: ResolvedConfigFilePath) {
    state.projectStatus.delete(resolved);
    state.diagnostics.delete(resolved);
}
```

### addProjToQueue
```ts
function addProjToQueue({ projectPendingBuild }: SolutionBuilderState, proj: ResolvedConfigFilePath, reloadLevel: ConfigFileProgramReloadLevel) {
    const value = projectPendingBuild.get(proj);
    if (value === undefined) {
        projectPendingBuild.set(proj, reloadLevel);
    }
    else if (value < reloadLevel) {
        projectPendingBuild.set(proj, reloadLevel);
    }
}
```

### setupInitialBuild
```ts
function setupInitialBuild(
    state: SolutionBuilderState, 
    cancellationToken: CancellationToken | undefined) {

}
```
### doneInvalidatedProject
```ts
function doneInvalidatedProject(
    state: SolutionBuilderState,
    projectPath: ResolvedConfigFilePath
) {
    state.projectPendingBuild.delete(projectPath);
    state.currentInvalidatedProject = undefined;
    return state.diagnostics.has(projectPath) ?
        ExitStatus.DiagnosticsPresent_OutputsSkipped :
        ExitStatus.Success;
}
```

### createUpdateOutputFileStampsProject
```ts
function createUpdateOutputFileStampsProject(
    state: SolutionBuilderState,
    project: ResolvedConfigFileName,
    projectPath: ResolvedConfigFilePath,
    config: ParsedCommandLine,
    buildOrder: readonly ResolvedConfigFileName[]
): UpdateOutputFileStampsProject {
}
```

### createBuildOrUpdateInvalidedProject
```ts
function createBuildOrUpdateInvalidedProject<T extends BuilderProgram>(
    kind: InvalidatedProjectKind.Build | InvalidatedProjectKind.UpdateBundle,
    state: SolutionBuilderState<T>,
    project: ResolvedConfigFileName,
    projectPath: ResolvedConfigFilePath,
    projectIndex: number,
    config: ParsedCommandLine,
    buildOrder: readonly ResolvedConfigFileName[],
): BuildInvalidedProject<T> | UpdateBundleProject<T> {
}
```

### needsBuild
### getNextInvalidatedProject
### listEmittedFile
### getOldProgram
### afterProgramCreate
### buildErrors
### updateModuleResolutionCache
### checkConfigFileUpToDateStatus
### getUpToDateStatusWorker
### getUpToDateStatus
### updateOutputTimestampsWorker
### updateOutputTimestamps
### queueReferencingProjects

### build
### clean
### invalidateProject
### scheduleBuildInvalidatedProject
### buildNextInvalidatedProject
### watchConfigFile
### isSameFile
### isOutputFile
### watchWildCardDirectories
### watchInputFiles
### startWatching
### createSolutionBuilderWorker
### relName

### reportStatus
### reportWatchStatus
### reportErrors
### reportAndStoreErrors
### reportParseConfigFileDiagnostic
### reportErrorSummary
### reportBuildQueue
### reportUpToDateStatus
### verboseReportProjectStatus