## 功能说明

## 类型接口
### OutputFingerprint
```ts
interface OutputFingerprint {
    hash: string;
    byteOrderMark: boolean;
    mtime: Date;
}
```

### CompilerHostLikeForCache
```ts
interface CompilerHostLikeForCache {
    fileExists(fileName: string): boolean;
    readFile(fileName: string, encoding?: string): string | undefined;
    directoryExists?(directory: string): boolean;
    createDirectory?(directory: string): void;
    writeFile?: WriteFileCallback;
}
```

### FormatDiagnosticsHost
```ts
export interface FormatDiagnosticsHost {
    getCurrentDirectory(): string;
    getCanonicalFileName(fileName: string): string;
    getNewLine(): string;
}
```

### ForegroundColorEscapeSequences
```ts
export enum ForegroundColorEscapeSequences {
    Grey = "\u001b[90m",
    Red = "\u001b[91m",
    Yellow = "\u001b[93m",
    Blue = "\u001b[94m",
    Cyan = "\u001b[96m"
}
```

### DiagnosticCache
```ts
interface DiagnosticCache<T extends Diagnostic> {
    perFile?: Map<T[]>;
    allDiagnostics?: Diagnostic[];
}
```

### RefFile
```ts

```

### CompilerHostLike
```ts
interface CompilerHostLike {
    useCaseSensitiveFileNames(): boolean;
    getCurrentDirectory(): string;
    fileExists(fileName: string): boolean;
    readFile(fileName: string): string | undefined;
    readDirectory?(rootDir: string, extensions: readonly string[], excludes: readonly string[] | undefined, includes: readonly string[], depth?: number): string[];
    trace?(s: string): void;
    onUnRecoverableConfigFileDiagnostic?: DiagnosticReporter;
}
```

## 实例对象
```ts
const gutterStyleSequence = "\u001b[7m";
const gutterSeparator = " ";
const resetEscapeSequence = "\u001b[0m";
const ellipsis = "...";
const halfIndent = "  ";
const indent = "    ";
export const inferredTypesContainingFile = "__inferred type names__.ts";
```
## 功能函数
### findConfigFile
> 在特定目录下查找配置文件`tsconfig.json`
```ts
export function findConfigFile(searchPath: string, fileExists: (fileName: string) => boolean, configName = "tsconfig.json"): string | undefined {
    ...
}
```

### resolveTripleslashReference
```ts
export function resolveTripleslashReference(moduleName: string, containingFile: string): string {
    ...
}
```

### computeCommonSourceDirectoryOfFilenames
```ts
export function computeCommonSourceDirectoryOfFilenames(fileNames: string[], currentDirectory: string, getCanonicalFileName: GetCanonicalFileName): string {
    ...
}
```

### createCompilerHost
```ts
export function createCompilerHost(options: CompilerOptions, setParentNodes?: boolean): CompilerHost {
    return createCompilerHostWorker(options, setParentNodes);
}
```

### createCompilerHostWorker
```ts
export function createCompilerHostWorker(options: CompilerOptions, setParentNodes?: boolean, system = sys): CompilerHost {
    ..
}
```

### changeCompilerHostLikeToUseCache
```ts
export function changeCompilerHostLikeToUseCache(
    host: CompilerHostLikeForCache,
    toPath: (fileName: string) => Path,
    getSourceFile?: CompilerHost["getSourceFile"]
) {
    ...
}
```

### getPreEmitDiagnostics
```ts
export function getPreEmitDiagnostics(program: Program, sourceFile?: SourceFile, cancellationToken?: CancellationToken): readonly Diagnostic[];

/*@internal*/ 
export function getPreEmitDiagnostics(program: BuilderProgram, sourceFile?: SourceFile, cancellationToken?: CancellationToken): readonly Diagnostic[]; 

export function getPreEmitDiagnostics(program: Program | BuilderProgram, sourceFile?: SourceFile, cancellationToken?: CancellationToken): readonly Diagnostic[] {
    ...
}
```

### formatDiagnostics
```ts
export function formatDiagnostics(diagnostics: readonly Diagnostic[], host: FormatDiagnosticsHost): string {
    ...
}
export function formatDiagnostic(diagnostic: Diagnostic, host: FormatDiagnosticsHost): string {
    ...
}
```


### formatColorAndReset
```ts
export function formatColorAndReset(text: string, formatStyle: string) {
    return formatStyle + text + resetEscapeSequence;
}
```



### formatLocation
```ts
export function formatLocation(file: SourceFile, start: number, host: FormatDiagnosticsHost, color = formatColorAndReset) {
    ...
}
```

### formatDiagnosticsWithColorAndContext
```ts
export function formatDiagnosticsWithColorAndContext(diagnostics: readonly Diagnostic[], host: FormatDiagnosticsHost): string {
    ...
}
```

### flattenDiagnosticMessageText
```ts
export function flattenDiagnosticMessageText(diag: string | DiagnosticMessageChain | undefined, newLine: string, indent = 0): string {
    ...
}
```

### loadWithLocalCache
```ts
export function loadWithLocalCache<T>(names: string[], 
    containingFile: string, 
    redirectedReference: ResolvedProjectReference | undefined, 
    loader: (name: string, containingFile: string, redirectedReference: ResolvedProjectReference | undefined) => T): T[]  {
        ...
    }
```

### isProgramUptoDate
```ts
export function isProgramUptoDate(
    program: Program | undefined,
    rootFileNames: string[],
    newOptions: CompilerOptions,
    getSourceVersion: (path: Path) => string | undefined,
    fileExists: (fileName: string) => boolean,
    hasInvalidatedResolution: HasInvalidatedResolution,
    hasChangedAutomaticTypeDirectiveNames: boolean,
    projectReferences: readonly ProjectReference[] | undefined
): boolean {
    ...
}
```

### getConfigFileParsingDiagnostics
```ts
export function getConfigFileParsingDiagnostics(configFileParseResult: ParsedCommandLine): readonly Diagnostic[] {
    ...
}
```

### createProgram
```ts
export function createProgram(
    createProgramOptions: CreateProgramOptions): Program;
export function createProgram(
    rootNames: readonly string[],
    options: CompilerOptions, 
    host?: CompilerHost, 
    oldProgram?: Program, 
    configFileParsingDiagnostics?: 
    readonly Diagnostic[]): Program;
export function createProgram(
        rootNamesOrOptions: readonly string[] | CreateProgramOptions, 
        _options?: CompilerOptions, 
        _host?: CompilerHost,
        _oldProgram?: Program, 
        _configFileParsingDiagnostics?: 
        readonly Diagnostic[]): Program {
    ...
}
```

### parseConfigHostFromCompilerHostLike
```ts
export function parseConfigHostFromCompilerHostLike(host: CompilerHostLike, directoryStructureHost: DirectoryStructureHost = host): ParseConfigFileHost {
    ...
}
```

### createPrependNodes
```ts
export function createPrependNodes(
    projectReferences: readonly ProjectReference[] | undefined, 
    getCommandLine: (ref: ProjectReference, index: number) => ParsedCommandLine | undefined, readFile: (path: string) => string | undefined) {
    ...
}
```

### resolveProjectReferencePath
```ts
export function resolveProjectReferencePath(ref: ProjectReference): ResolvedConfigFileName;
    /** @deprecated */ export function resolveProjectReferencePath(host: ResolveProjectReferencePathHost, ref: ProjectReference): ResolvedConfigFileName;
export function resolveProjectReferencePath(hostOrRef: ResolveProjectReferencePathHost | ProjectReference, ref?: ProjectReference): ResolvedConfigFileName {
        ...
}
```

### getResolutionDiagnostic
```ts
export function getResolutionDiagnostic(options: CompilerOptions, { extension }: ResolvedModuleFull): DiagnosticMessage | undefined {
    ...
}
```

## 内部函数

### getCategoryFormat
```ts
function getCategoryFormat(category: DiagnosticCategory): ForegroundColorEscapeSequences {
    switch (category) {
        case DiagnosticCategory.Error: return ForegroundColorEscapeSequences.Red;
        case DiagnosticCategory.Warning: return ForegroundColorEscapeSequences.Yellow;
        case DiagnosticCategory.Suggestion: return Debug.fail("Should never get an Info diagnostic on the command line.");
        case DiagnosticCategory.Message: return ForegroundColorEscapeSequences.Blue;
    }
}
```
### padLeft
```ts
function padLeft(s: string, length: number) {
    while (s.length < length) {
        s = " " + s;
    }
    return s;
}
```

### formatCodeSpan
```ts
function formatCodeSpan(file: SourceFile, start: number, length: number, indent: string, squiggleColor: ForegroundColorEscapeSequences, host: FormatDiagnosticsHost) {
    ...
}
```
### shouldProgramCreateNewSourceFiles 
```ts
function shouldProgramCreateNewSourceFiles(program: Program | undefined, newOptions: CompilerOptions): boolean {
    ...
}
```

### createCreateProgramOptions
```ts
function createCreateProgramOptions(rootNames: readonly string[], options: CompilerOptions, host?: CompilerHost, oldProgram?: Program, configFileParsingDiagnostics?: readonly Diagnostic[]): CreateProgramOptions {
    return {
        rootNames,
        options,
        host,
        oldProgram,
        configFileParsingDiagnostics
    };
}
```
### getModuleNames
```ts
function getModuleNames({ imports, moduleAugmentations }: SourceFile): string[] {
    const res = imports.map(i => i.text);
    for (const aug of moduleAugmentations) {
        if (aug.kind === SyntaxKind.StringLiteral) {
            res.push(aug.text);
        }
        // Do nothing if it's an Identifier; we don't need to do module resolution for `declare global`.
    }
    return res;
}
```