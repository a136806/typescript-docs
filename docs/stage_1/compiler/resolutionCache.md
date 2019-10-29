## 类型接口
### ResolutionCache
```ts
export interface ResolutionCache {
    startRecordingFilesWithChangedResolutions(): void;
    finishRecordingFilesWithChangedResolutions(): Path[] | undefined;

    resolveModuleNames(
        moduleNames: string[], 
        containingFile: string, 
        reusedNames: string[] | undefined, 
        redirectedReference?: ResolvedProjectReference
    ): (ResolvedModuleFull | undefined)[];
    getResolvedModuleWithFailedLookupLocationsFromCache(
        moduleName: string, 
        containingFile: string
    ): CachedResolvedModuleWithFailedLookupLocations | undefined;
    resolveTypeReferenceDirectives(
        typeDirectiveNames: string[], 
        containingFile: string, 
        redirectedReference?: ResolvedProjectReference
    ): (ResolvedTypeReferenceDirective | undefined)[];

    invalidateResolutionOfFile(filePath: Path): void;
    removeResolutionsOfFile(filePath: Path): void;
    removeResolutionsFromProjectReferenceRedirects(filePath: Path): void;
    setFilesWithInvalidatedNonRelativeUnresolvedImports(filesWithUnresolvedImports: Map<readonly string[]>): void;
    createHasInvalidatedResolution(forceAllFilesAsInvalidated?: boolean): HasInvalidatedResolution;

    startCachingPerDirectoryResolution(): void;
    finishCachingPerDirectoryResolution(): void;

    updateTypeRootsWatch(): void;
    closeTypeRootsWatch(): void;

    clear(): void;
}
```

### ResolutionWithFailedLookupLocations
```ts
interface ResolutionWithFailedLookupLocations {
    readonly failedLookupLocations: readonly string[];
    isInvalidated?: boolean;
    refCount?: number;
}
```

### ResolutionWithResolvedFileName
```ts
interface ResolutionWithResolvedFileName {
    resolvedFileName: string | undefined;
}
```

### CachedResolvedModuleWithFailedLookupLocations
```ts
interface CachedResolvedModuleWithFailedLookupLocations 
    extends ResolvedModuleWithFailedLookupLocations, 
    ResolutionWithFailedLookupLocations {
}
```

### CachedResolvedTypeReferenceDirectiveWithFailedLookupLocations
```ts
interface CachedResolvedTypeReferenceDirectiveWithFailedLookupLocations 
    extends ResolvedTypeReferenceDirectiveWithFailedLookupLocations, ResolutionWithFailedLookupLocations {
}
```

### ResolutionCacheHost
```ts
export interface ResolutionCacheHost extends ModuleResolutionHost {
    toPath(fileName: string): Path;
    getCanonicalFileName: GetCanonicalFileName;
    getCompilationSettings(): CompilerOptions;
    watchDirectoryOfFailedLookupLocation(directory: string, cb: DirectoryWatcherCallback, flags: WatchDirectoryFlags): FileWatcher;
    onInvalidatedResolution(): void;
    watchTypeRootsDirectory(directory: string, cb: DirectoryWatcherCallback, flags: WatchDirectoryFlags): FileWatcher;
    onChangedAutomaticTypeDirectiveNames(): void;
    getCachedDirectoryStructureHost(): CachedDirectoryStructureHost | undefined;
    projectName?: string;
    getGlobalCache?(): string | undefined;
    globalCacheResolutionModuleName?(externalModuleName: string): string;
    writeLog(s: string): void;
    maxNumberOfFilesToIterateForInvalidation?: number;
    getCurrentProgram(): Program | undefined;
    fileIsOpen(filePath: Path): boolean;
}
```

### DirectoryWatchesOfFailedLookup
```ts
interface DirectoryWatchesOfFailedLookup {
    watcher: FileWatcher;
    refCount: number;
    nonRecursive?: boolean;
}
```

###  DirectoryOfFailedLookupWatch
```ts
interface DirectoryOfFailedLookupWatch {
    dir: string;
    dirPath: Path;
    nonRecursive?: boolean;
}
```

### GetResolutionWithResolvedFileName
```ts
type GetResolutionWithResolvedFileName<
    T extends ResolutionWithFailedLookupLocations = ResolutionWithFailedLookupLocations, R extends ResolutionWithResolvedFileName = ResolutionWithResolvedFileName
    > 
    = (resolution: T) => R | undefined;
```

## 实例对象
### Base
```ts
export const maxNumberOfFilesToIterateForInvalidation = 256;
```
## 功能函数
### isPathIgnored
```ts
export function isPathIgnored(path: Path) {
    return some(ignoredPaths, searchPath => stringContains(path, searchPath));
}
```

### canWatchDirectory
```ts
export function canWatchDirectory(dirPath: Path) {
    ...
}
```

### createResolutionCache
```ts
export function createResolutionCache(
    resolutionHost: ResolutionCacheHost, 
    rootDirForResolution: string | undefined, 
    logChangesWhenResolvingModule: boolean): ResolutionCache {
    ...    
}
```

## 内部函数
