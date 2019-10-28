## 类型接口
### Push
```ts
export interface Push<T> {
    push(value: T): void;
}
```

### Resolved
```ts
interface Resolved {
    path: string;
    extension: Extension;
    packageId: PackageId | undefined;
    originalPath?: string | true;
}
```

### PathAndExtension
```ts
interface PathAndExtension {
    path: string;
    ext: Extension;
}
```

### Extensions
```ts
enum Extensions {
    TypeScript, /** '.ts', '.tsx', or '.d.ts' */
    JavaScript, /** '.js' or '.jsx' */
    Json,       /** '.json' */
    TSConfig,   /** '.json' with `tsconfig` used instead of `index` */
    DtsOnly /** Only '.d.ts' */
}
```

### PathAndPackageId
```ts
interface PathAndPackageId {
    readonly fileName: string;
    readonly packageId: PackageId | undefined;
}
```

### ModuleResolutionState
```ts
interface ModuleResolutionState {
    host: ModuleResolutionHost;
    compilerOptions: CompilerOptions;
    traceEnabled: boolean;
    failedLookupLocations: Push<string>;
}
```

### PackageJsonPathFields
```ts
interface PackageJsonPathFields {
    typings?: string;
    types?: string;
    typesVersions?: MapLike<MapLike<string[]>>;
    main?: string;
    tsconfig?: string;
}
```

### PackageJson
```ts
interface PackageJson extends PackageJsonPathFields {
    name?: string;
    version?: string;
}
```

### MatchingKeys
```ts
type MatchingKeys<TRecord, TMatch, K extends keyof TRecord = keyof TRecord> 
    = K extends (TRecord[K] extends TMatch 
                    ? K 
                    : never) 
                ? K : never;
```

### VersionPaths
```ts
interface VersionPaths {
    version: string;
    paths: MapLike<string[]>;
}
```


### ModuleResolutionCache
```ts
export interface ModuleResolutionCache extends NonRelativeModuleNameResolutionCache {
    getOrCreateCacheForDirectory(directoryName: string, redirectedReference?: ResolvedProjectReference): Map<ResolvedModuleWithFailedLookupLocations>;
    /*@internal*/ directoryToModuleNameMap: CacheWithRedirects<Map<ResolvedModuleWithFailedLookupLocations>>;
}
```

### NonRelativeModuleNameResolutionCache
```ts
export interface NonRelativeModuleNameResolutionCache {
    getOrCreateCacheForModuleName(nonRelativeModuleName: string, redirectedReference?: ResolvedProjectReference): PerModuleNameCache;
    /*@internal*/ moduleNameToDirectoryMap: CacheWithRedirects<PerModuleNameCache>;
}
```
### PerModuleNameCache
```ts
export interface PerModuleNameCache {
    get(directory: string): ResolvedModuleWithFailedLookupLocations | undefined;
    set(directory: string, result: ResolvedModuleWithFailedLookupLocations): void;
}
```

### CacheWithRedirects
```ts
export interface CacheWithRedirects<T> {
    ownMap: Map<T>;
    redirectsMap: Map<Map<T>>;
    getOrCreateMapOfCacheRedirects(redirectedReference: ResolvedProjectReference | undefined): Map<T>;
    clear(): void;
    setOwnOptions(newOptions: CompilerOptions): void;
    setOwnMap(newOwnMap: Map<T>): void;
}
```

### ResolutionKindSpecificLoader
```ts
type ResolutionKindSpecificLoader = (
    extensions: Extensions, 
    candidate: string, 
    onlyRecordFailures: boolean, 
    state: ModuleResolutionState) => Resolved | undefined;
```

### PackageJsonInfo
```ts
interface PackageJsonInfo {
    packageDirectory: string;
    packageJsonContent: PackageJsonPathFields;
    versionPaths: VersionPaths | undefined;
}
```

### SearchResult
```ts
type SearchResult<T> = { value: T | undefined } | undefined;
```

## 实例对象
### Base
```ts
let typeScriptVersion: Version | undefined;
const nodeModulesAtTypes = combinePaths("node_modules", "@types");

const jsOnlyExtensions = [Extensions.JavaScript];
const tsExtensions = [Extensions.TypeScript, Extensions.JavaScript];
const tsPlusJsonExtensions = [...tsExtensions, Extensions.Json];
const tsconfigExtensions = [Extensions.TSConfig];
export const nodeModulesPathPart = "/node_modules/";
```
## 功能函数
### trace
```ts
export function trace(
    host: ModuleResolutionHost, 
    message: DiagnosticMessage, 
    ...args: any[]): void;
export function trace(host: ModuleResolutionHost): void {
    host.trace!(formatMessage.apply(undefined, arguments));
}
```

### isTraceEnabled
```ts
export function isTraceEnabled(
    compilerOptions: CompilerOptions, 
    host: ModuleResolutionHost): boolean {
    return !!compilerOptions.traceResolution && host.trace !== undefined;
}
```

### getPackageJsonTypesVersionsPaths
```ts
export function getPackageJsonTypesVersionsPaths(typesVersions: MapLike<MapLike<string[]>>) {
    if (!typeScriptVersion) typeScriptVersion = new Version(version);

    for (const key in typesVersions) {
        if (!hasProperty(typesVersions, key)) continue;

        const keyRange = VersionRange.tryParse(key);
        if (keyRange === undefined) {
            continue;
        }

        if (keyRange.test(typeScriptVersion)) {
            return { version: key, paths: typesVersions[key] };
        }
    }
}
```
### getEffectiveTypeRoots
```ts
export function getEffectiveTypeRoots(
    options: CompilerOptions, 
    host: GetEffectiveTypeRootsHost): string[] | undefined {
    
    if (options.typeRoots) {
        return options.typeRoots;
    }

    let currentDirectory: string | undefined;
    if (options.configFilePath) {
        currentDirectory = getDirectoryPath(options.configFilePath);
    }
    else if (host.getCurrentDirectory) {
        currentDirectory = host.getCurrentDirectory();
    }

    if (currentDirectory !== undefined) {
        return getDefaultTypeRoots(currentDirectory, host);
    }

}
```

### resolveTypeReferenceDirective
```ts
export function resolveTypeReferenceDirective(
    typeReferenceDirectiveName: string, 
    containingFile: string | undefined, 
    options: CompilerOptions, 
    host: ModuleResolutionHost, 
    redirectedReference?: ResolvedProjectReference): ResolvedTypeReferenceDirectiveWithFailedLookupLocations {
    ...
}
```

### getAutomaticTypeDirectiveNames
```ts
export function getAutomaticTypeDirectiveNames(
    options: CompilerOptions, 
    host: ModuleResolutionHost): string[] {
    ...
}
```

### createModuleResolutionCache
```ts
export function createModuleResolutionCache(currentDirectory: string, getCanonicalFileName: (s: string) => string, options?: CompilerOptions): ModuleResolutionCache {
    return createModuleResolutionCacheWithMaps(
        createCacheWithRedirects(options),
        createCacheWithRedirects(options),
        currentDirectory,
        getCanonicalFileName
    );
}
```

### createCacheWithRedirects
```ts
export function createCacheWithRedirects<T>(options?: CompilerOptions): CacheWithRedirects<T> {
    let ownMap: Map<T> = createMap();
    const redirectsMap: Map<Map<T>> = createMap();
    return {
        ownMap,
        redirectsMap,
        getOrCreateMapOfCacheRedirects,
        clear,
        setOwnOptions,
        setOwnMap
    };

    function setOwnOptions(newOptions: CompilerOptions) {
        options = newOptions;
    }

    function setOwnMap(newOwnMap: Map<T>) {
        ownMap = newOwnMap;
    }

    function getOrCreateMapOfCacheRedirects(redirectedReference: ResolvedProjectReference | undefined) {
        if (!redirectedReference) {
            return ownMap;
        }
        const path = redirectedReference.sourceFile.path;
        let redirects = redirectsMap.get(path);
        if (!redirects) {
            // Reuse map if redirected reference map uses same resolution
            redirects = !options || optionsHaveModuleResolutionChanges(options, redirectedReference.commandLine.options) ? createMap() : ownMap;
            redirectsMap.set(path, redirects);
        }
        return redirects;
    }

    function clear() {
        ownMap.clear();
        redirectsMap.clear();
    }
}
```

### createModuleResolutionCacheWithMaps
```ts
export function createModuleResolutionCacheWithMaps(
    directoryToModuleNameMap: CacheWithRedirects<Map<ResolvedModuleWithFailedLookupLocations>>,
    moduleNameToDirectoryMap: CacheWithRedirects<PerModuleNameCache>,
    currentDirectory: string,
    getCanonicalFileName: GetCanonicalFileName): ModuleResolutionCache {
    ...
}
```

### resolveModuleNameFromCache
```ts
export function resolveModuleNameFromCache(
    moduleName: string, 
    containingFile: string, cache: ModuleResolutionCache): ResolvedModuleWithFailedLookupLocations | undefined {
    const containingDirectory = getDirectoryPath(containingFile);
    const perFolderCache = cache && cache.getOrCreateCacheForDirectory(containingDirectory);
    return perFolderCache && perFolderCache.get(moduleName);
}
```

### resolveModuleName
```ts
export function resolveModuleName(
    moduleName: string, 
    containingFile: string, 
    compilerOptions: CompilerOptions, 
    host: ModuleResolutionHost, 
    cache?: ModuleResolutionCache, 
    redirectedReference?: ResolvedProjectReference): ResolvedModuleWithFailedLookupLocations {
    ...
}
```

### resolveJSModule
```ts
export function resolveJSModule(
    moduleName: string, 
    initialDir: string, 
    host: ModuleResolutionHost): string {
    ...
}
```

### tryResolveJSModule
```ts
export function tryResolveJSModule(
    moduleName: string, 
    initialDir: string, 
    host: ModuleResolutionHost): string | undefined {
    const { resolvedModule } = tryResolveJSModuleWorker(moduleName, initialDir, host);
    return resolvedModule && resolvedModule.resolvedFileName;
}
```

### nodeModuleNameResolver
```ts
export function nodeModuleNameResolver(
    moduleName: string, 
    containingFile: string, 
    compilerOptions: CompilerOptions, 
    host: ModuleResolutionHost, 
    cache?: ModuleResolutionCache, 
    redirectedReference?: ResolvedProjectReference): ResolvedModuleWithFailedLookupLocations;

/* @internal */ 
export function nodeModuleNameResolver(
    moduleName: string, 
    containingFile: string, 
    compilerOptions: CompilerOptions, 
    host: ModuleResolutionHost, 
    cache?: ModuleResolutionCache, 
    redirectedReference?: ResolvedProjectReference, 
    lookupConfig?: boolean): ResolvedModuleWithFailedLookupLocations; 

export function nodeModuleNameResolver(
    moduleName: string, 
    containingFile: string, 
    compilerOptions: CompilerOptions, 
    host: ModuleResolutionHost, 
    cache?: ModuleResolutionCache, 
    redirectedReference?: ResolvedProjectReference, 
    lookupConfig?: boolean): ResolvedModuleWithFailedLookupLocations {

    return nodeModuleNameResolverWorker(moduleName, getDirectoryPath(containingFile), compilerOptions, host, cache, lookupConfig ? tsconfigExtensions : (compilerOptions.resolveJsonModule ? tsPlusJsonExtensions : tsExtensions), redirectedReference);

}

function nodeModuleNameResolverWorker(
    moduleName: string, 
    containingDirectory: string, 
    compilerOptions: CompilerOptions, 
    host: ModuleResolutionHost, 
    cache: ModuleResolutionCache | undefined, 
    extensions: Extensions[], 
    redirectedReference: ResolvedProjectReference | undefined): ResolvedModuleWithFailedLookupLocations {
    ...
}
```

### pathContainsNodeModules
```ts
export function pathContainsNodeModules(path: string): boolean {
    return stringContains(path, nodeModulesPathPart);
}
```

### parsePackageName
```ts
export function parsePackageName(moduleName: string): { packageName: string, rest: string } {
    let idx = moduleName.indexOf(directorySeparator);
    if (moduleName[0] === "@") {
        idx = moduleName.indexOf(directorySeparator, idx + 1);
    }
    return idx === -1 
        ? { packageName: moduleName, rest: "" } 
        : { packageName: moduleName.slice(0, idx), rest: moduleName.slice(idx + 1) };
}
```

### getTypesPackageName
```ts
export function getTypesPackageName(packageName: string): string {
    return `@types/${mangleScopedPackageName(packageName)}`;
}
```

### mangleScopedPackageName
```ts
export function mangleScopedPackageName(packageName: string): string {
    if (startsWith(packageName, "@")) {
        const replaceSlash = packageName.replace(directorySeparator, mangledScopedPackageSeparator);
        if (replaceSlash !== packageName) {
            return replaceSlash.slice(1); // Take off the "@"
        }
    }
    return packageName;
}
```

### getPackageNameFromTypesPackageName
```ts
export function getPackageNameFromTypesPackageName(mangledName: string): string {
    const withoutAtTypePrefix = removePrefix(mangledName, "@types/");
    if (withoutAtTypePrefix !== mangledName) {
        return unmangleScopedPackageName(withoutAtTypePrefix);
    }
    return mangledName;
}
```

### unmangleScopedPackageName
```ts
export function unmangleScopedPackageName(typesPackageName: string): string {
    return stringContains(typesPackageName, mangledScopedPackageSeparator) ?
        "@" + typesPackageName.replace(mangledScopedPackageSeparator, directorySeparator) :
        typesPackageName;
}
```

### classicNameResolver
```ts
export function classicNameResolver(
    moduleName: string, 
    containingFile: string, 
    compilerOptions: CompilerOptions, 
    host: ModuleResolutionHost, 
    cache?: NonRelativeModuleNameResolutionCache, 
    redirectedReference?: ResolvedProjectReference): ResolvedModuleWithFailedLookupLocations {
    ...
}
```

### loadModuleFromGlobalCache
```ts
export function loadModuleFromGlobalCache(
    moduleName: string, 
    projectName: string | undefined, 
    compilerOptions: CompilerOptions, 
    host: ModuleResolutionHost, 
    globalCache: string): ResolvedModuleWithFailedLookupLocations {
    ...
}
```


## 内部函数

### withPackageId
```ts
function withPackageId(
    packageInfo: PackageJsonInfo | undefined, 
    r: PathAndExtension | undefined): Resolved | undefined {
    ...    
}
```

### noPackageId
```ts
function noPackageId(
    r: PathAndExtension | undefined): Resolved | undefined {
    return withPackageId(/*packageInfo*/ undefined, r);
}
```

### removeIgnoredPackageId
```ts
function removeIgnoredPackageId(
    r: Resolved | undefined): PathAndExtension | undefined {
    if (r) {
        Debug.assert(r.packageId === undefined);
        return { path: r.path, ext: r.extension };
    }
}
```

### resolvedTypeScriptOnly
```ts
function resolvedTypeScriptOnly(resolved: Resolved | undefined): PathAndPackageId | undefined {
    if (!resolved) {
        return undefined;
    }
    Debug.assert(extensionIsTS(resolved.extension));
    return { fileName: resolved.path, packageId: resolved.packageId };
}
```

### createResolvedModuleWithFailedLookupLocations
```ts
function createResolvedModuleWithFailedLookupLocations(
    resolved: Resolved | undefined, 
    isExternalLibraryImport: boolean, 
    failedLookupLocations: string[]): ResolvedModuleWithFailedLookupLocations {
    return {
        resolvedModule: resolved && { resolvedFileName: resolved.path, originalPath: resolved.originalPath === true ? undefined : resolved.originalPath, extension: resolved.extension, isExternalLibraryImport, packageId: resolved.packageId },
        failedLookupLocations
    };
}
```

### readPackageJsonField
```ts
function readPackageJsonField<TMatch, K extends MatchingKeys<PackageJson, string | undefined>>(
    jsonContent: PackageJson, 
    fieldName: K, 
    typeOfTag: "string", 
    state: ModuleResolutionState): PackageJson[K] | undefined;

function readPackageJsonField<K extends MatchingKeys<PackageJson, object | undefined>>(
    jsonContent: PackageJson,
    fieldName: K, 
    typeOfTag: "object", 
    state: ModuleResolutionState): PackageJson[K] | undefined;

function readPackageJsonField<K extends keyof PackageJson>(jsonContent: PackageJson, fieldName: K, typeOfTag: "string" | "object", state: ModuleResolutionState): PackageJson[K] | undefined {
    if (!hasProperty(jsonContent, fieldName)) {
        if (state.traceEnabled) {
            trace(state.host, Diagnostics.package_json_does_not_have_a_0_field, fieldName);
        }
        return;
    }
    const value = jsonContent[fieldName];
    if (typeof value !== typeOfTag || value === null) { // eslint-disable-line no-null/no-null
        if (state.traceEnabled) {
            // eslint-disable-next-line no-null/no-null
            trace(state.host, Diagnostics.Expected_type_of_0_field_in_package_json_to_be_1_got_2, fieldName, typeOfTag, value === null ? "null" : typeof value);
        }
        return;
    }
    return value;
}
```

### readPackageJsonPathField
```ts
function readPackageJsonPathField<K extends "typings" | "types" | "main" | "tsconfig">(
    jsonContent: PackageJson, 
    fieldName: K, 
    baseDirectory: string, 
    state: ModuleResolutionState): PackageJson[K] | undefined {
    ...
}
```

### readPackageJsonTypesFields
```ts
function readPackageJsonTypesFields(jsonContent: PackageJson, baseDirectory: string, state: ModuleResolutionState) {
    return readPackageJsonPathField(jsonContent, "typings", baseDirectory, state)
        || readPackageJsonPathField(jsonContent, "types", baseDirectory, state);
}
```

### readPackageJsonTSConfigField
```ts
function readPackageJsonTSConfigField(jsonContent: PackageJson, baseDirectory: string, state: ModuleResolutionState) {
    return readPackageJsonPathField(jsonContent, "tsconfig", baseDirectory, state);
}
```

### readPackageJsonMainField
```ts
function readPackageJsonMainField(jsonContent: PackageJson, baseDirectory: string, state: ModuleResolutionState) {
    return readPackageJsonPathField(jsonContent, "main", baseDirectory, state);
}
```

### readPackageJsonTypesVersionsField
```ts
function readPackageJsonTypesVersionsField(jsonContent: PackageJson, state: ModuleResolutionState) {
    const typesVersions = readPackageJsonField(jsonContent, "typesVersions", "object", state);
    if (typesVersions === undefined) return;

    if (state.traceEnabled) {
        trace(state.host, Diagnostics.package_json_has_a_typesVersions_field_with_version_specific_path_mappings);
    }

    return typesVersions;
}
```

### readPackageJsonTypesVersionPaths
```ts
function readPackageJsonTypesVersionPaths(
    jsonContent: PackageJson, 
    state: ModuleResolutionState): VersionPaths | undefined {
    ... 
}
```

### getDefaultTypeRoots
```ts
function getDefaultTypeRoots(
    currentDirectory: string, 
    host: { directoryExists?: (directoryName: string) => boolean }): string[] | undefined {
    ...
}
```

### tryLoadModuleUsingOptionalResolutionSettings
```ts
function tryLoadModuleUsingOptionalResolutionSettings(
    extensions: Extensions, 
    moduleName: string, 
    containingDirectory: string, 
    loader: ResolutionKindSpecificLoader,
    state: ModuleResolutionState): Resolved | undefined {
    ...
}
```

### tryLoadModuleUsingPathsIfEligible
```ts
function tryLoadModuleUsingPathsIfEligible(
    extensions: Extensions, 
    moduleName: string, 
    loader: ResolutionKindSpecificLoader, 
    state: ModuleResolutionState) {
    ...
}
```

### tryLoadModuleUsingRootDirs
```ts
function tryLoadModuleUsingRootDirs(
    extensions: Extensions, 
    moduleName: string, 
    containingDirectory: string, 
    loader: ResolutionKindSpecificLoader,
    state: ModuleResolutionState): Resolved | undefined {
    ...
}
```

### tryLoadModuleUsingBaseUrl
```ts
function tryLoadModuleUsingBaseUrl(
    extensions: Extensions, 
    moduleName: string, 
    loader: ResolutionKindSpecificLoader, 
    state: ModuleResolutionState): Resolved | undefined {
    ...
}
```

### tryResolveJSModuleWorker
```ts
function tryResolveJSModuleWorker(moduleName: string, initialDir: string, host: ModuleResolutionHost): ResolvedModuleWithFailedLookupLocations {
    return nodeModuleNameResolverWorker(moduleName, initialDir, { moduleResolution: ModuleResolutionKind.NodeJs, allowJs: true }, host, /*cache*/ undefined, jsOnlyExtensions, /*redirectedReferences*/ undefined);
}
```

### realPath
```ts
function realPath(
    path: string, 
    host: ModuleResolutionHost, 
    traceEnabled: boolean): string {
    ...
}
```

### nodeLoadModuleByRelativeName

```ts
function nodeLoadModuleByRelativeName(
    extensions: Extensions, 
    candidate: string, 
    onlyRecordFailures: boolean, 
    state: ModuleResolutionState, 
    considerPackageJson: boolean): Resolved | undefined {
    ...
}
```

### parseNodeModuleFromPath
```ts
function parseNodeModuleFromPath(resolved: PathAndExtension): string | undefined {
    ...
}
```

### moveToNextDirectorySeparatorIfAvailable
```ts
function moveToNextDirectorySeparatorIfAvailable(
    path: string, 
    prevSeparatorIndex: number): number {

    const nextSeparatorIndex = path.indexOf(directorySeparator, prevSeparatorIndex + 1);
    return nextSeparatorIndex === -1 ? prevSeparatorIndex : nextSeparatorIndex;
}
```

### loadModuleFromFileNoPackageId
```ts
function loadModuleFromFileNoPackageId(
    extensions: Extensions, 
    candidate: string, 
    onlyRecordFailures: boolean, 
    state: ModuleResolutionState): Resolved | undefined {

    return noPackageId(loadModuleFromFile(extensions, candidate, onlyRecordFailures, state));
}
```

### loadModuleFromFile
```ts
function loadModuleFromFile(
    extensions: Extensions, 
    candidate: string, 
    onlyRecordFailures: boolean, 
    state: ModuleResolutionState): PathAndExtension | undefined {
    ...
}
```

### tryAddingExtensions
```ts
function tryAddingExtensions(
    candidate: string, 
    extensions: Extensions, 
    onlyRecordFailures: boolean, 
    state: ModuleResolutionState): PathAndExtension | undefined {
    ...
}
```

### tryFile
```ts
function tryFile(
    fileName: string, 
    onlyRecordFailures: boolean, 
    state: ModuleResolutionState): string | undefined {
    ...
}    
```

### loadNodeModuleFromDirectory
```ts
function loadNodeModuleFromDirectory(
    extensions: Extensions, 
    candidate: string, 
    onlyRecordFailures: boolean, 
    state: ModuleResolutionState, 
    considerPackageJson = true) {
    const packageInfo = considerPackageJson 
        ? getPackageJsonInfo(candidate, onlyRecordFailures, state) 
        : undefined;
    const packageJsonContent = packageInfo && packageInfo.packageJsonContent;
    const versionPaths = packageInfo && packageInfo.versionPaths;

    return withPackageId(packageInfo, loadNodeModuleFromDirectoryWorker(extensions, candidate, onlyRecordFailures, state, packageJsonContent, versionPaths));
}
```


### getPackageJsonInfo
```ts
function getPackageJsonInfo(
    packageDirectory: string, 
    onlyRecordFailures: boolean, 
    state: ModuleResolutionState): PackageJsonInfo | undefined {
    ...
}
```

### loadNodeModuleFromDirectoryWorker
```ts
function loadNodeModuleFromDirectoryWorker(
    extensions: Extensions, 
    candidate: string, 
    onlyRecordFailures: boolean, 
    state: ModuleResolutionState, 
    jsonContent: PackageJsonPathFields | undefined, 
    versionPaths: VersionPaths | undefined): PathAndExtension | undefined {
    ...
}
```

### resolvedIfExtensionMatches
```ts
function resolvedIfExtensionMatches(extensions: Extensions, path: string): PathAndExtension | undefined {
    const ext = tryGetExtensionFromPath(path);
    return ext !== undefined && extensionIsOk(extensions, ext) ? { path, ext } : undefined;
}
```

### extensionIsOk
```ts
function extensionIsOk(extensions: Extensions, extension: Extension): boolean {
    switch (extensions) {
        case Extensions.JavaScript:
            return extension === Extension.Js || extension === Extension.Jsx;
        case Extensions.TSConfig:
        case Extensions.Json:
            return extension === Extension.Json;
        case Extensions.TypeScript:
            return extension === Extension.Ts || extension === Extension.Tsx || extension === Extension.Dts;
        case Extensions.DtsOnly:
            return extension === Extension.Dts;
    }
}
```

### loadModuleFromNearestNodeModulesDirectory
```ts
function loadModuleFromNearestNodeModulesDirectory(
    extensions: Extensions, 
    moduleName: string, 
    directory: string, 
    state: ModuleResolutionState, 
    cache: NonRelativeModuleNameResolutionCache | undefined, 
    redirectedReference: ResolvedProjectReference | undefined): SearchResult<Resolved> {
    ...
}    
```

### loadModuleFromNearestNodeModulesDirectoryTypesScope
```ts
function loadModuleFromNearestNodeModulesDirectoryTypesScope(
    moduleName: string, 
    directory: string, 
    state: ModuleResolutionState): SearchResult<Resolved> {
    ...
}
```

### loadModuleFromNearestNodeModulesDirectoryWorker
```ts
function loadModuleFromNearestNodeModulesDirectoryWorker(
    extensions: Extensions, 
    moduleName: string, 
    directory: string, 
    state: ModuleResolutionState, 
    typesScopeOnly: boolean, 
    cache: NonRelativeModuleNameResolutionCache | undefined, 
    redirectedReference: ResolvedProjectReference | undefined): SearchResult<Resolved> {
    ...
}
```

### loadModuleFromImmediateNodeModulesDirectory
```ts
function loadModuleFromImmediateNodeModulesDirectory(
    extensions: Extensions, 
    moduleName: string, 
    directory: string, 
    state: ModuleResolutionState, 
    typesScopeOnly: boolean): Resolved | undefined {
    ...
}
```

### loadModuleFromSpecificNodeModulesDirectory
```ts
function loadModuleFromSpecificNodeModulesDirectory(
    extensions: Extensions, 
    moduleName: string, 
    nodeModulesDirectory: string, 
    nodeModulesDirectoryExists: boolean, 
    state: ModuleResolutionState): Resolved | undefined {
    ...
}
```

### tryLoadModuleUsingPaths
```ts
function tryLoadModuleUsingPaths(
    extensions: Extensions, 
    moduleName: string, 
    baseDirectory: string, 
    paths: MapLike<string[]>, 
    loader: ResolutionKindSpecificLoader, 
    onlyRecordFailures: boolean, 
    state: ModuleResolutionState): SearchResult<Resolved> {
    ...
}        
```

### mangleScopedPackageNameWithTrace
```ts
function mangleScopedPackageNameWithTrace(
    packageName: string, 
    state: ModuleResolutionState): string {
    ...
}
```

### tryFindNonRelativeModuleNameInCache
```ts
function tryFindNonRelativeModuleNameInCache(
    cache: PerModuleNameCache | undefined, 
    moduleName: string, 
    containingDirectory: string, 
    state: ModuleResolutionState): SearchResult<Resolved> {
    ...
}
```

### toSearchResult
```ts
function toSearchResult<T>(value: T | undefined): SearchResult<T> {
    return value !== undefined ? { value } : undefined;
}
```