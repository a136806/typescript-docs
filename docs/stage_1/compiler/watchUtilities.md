## 类型接口

### DirectoryStructureHost
```ts
export interface DirectoryStructureHost {
    fileExists(path: string): boolean;
    readFile(path: string, encoding?: string): string | undefined;

    directoryExists?(path: string): boolean;
    getDirectories?(path: string): string[];
    readDirectory?(path: string, extensions?: readonly string[], exclude?: readonly string[], include?: readonly string[], depth?: number): string[];
    realpath?(path: string): string;

    createDirectory?(path: string): void;
    writeFile?(path: string, data: string, writeByteOrderMark?: boolean): void;
}
```

### FileAndDirectoryExistence
```ts
interface FileAndDirectoryExistence {
    fileExists: boolean;
    directoryExists: boolean;
}
```

### CachedDirectoryStructureHost
```ts
export interface CachedDirectoryStructureHost extends DirectoryStructureHost {
    useCaseSensitiveFileNames: boolean;

    getDirectories(path: string): string[];
    readDirectory(path: string, extensions?: readonly string[], exclude?: readonly string[], include?: readonly string[], depth?: number): string[];

    addOrDeleteFileOrDirectory(fileOrDirectory: string, fileOrDirectoryPath: Path): FileAndDirectoryExistence | undefined;
    addOrDeleteFile(fileName: string, filePath: Path, eventKind: FileWatcherEventKind): void;
    clearCache(): void;
}
```

### MutableFileSystemEntries
```ts
interface MutableFileSystemEntries {
    readonly files: string[];
    readonly directories: string[];
}
```

### ConfigFileProgramReloadLevel
```ts
 export enum ConfigFileProgramReloadLevel {
    None,
    Partial,
    Full
}
```

### WildcardDirectoryWatcher
```ts
export interface WildcardDirectoryWatcher {
    watcher: FileWatcher;
    flags: WatchDirectoryFlags;
}
```

### WatchLogLevel
```ts
export enum WatchLogLevel {
    None,
    TriggerOnly,
    Verbose
}
```

### WatchFileHost
```ts
export interface WatchFileHost {
    watchFile(path: string, callback: FileWatcherCallback, pollingInterval?: number): FileWatcher;
}
```

### WatchDirectoryHost
```ts
export interface WatchDirectoryHost {
    watchDirectory(path: string, callback: DirectoryWatcherCallback, recursive?: boolean): FileWatcher;
}
```

### WatchFile
```ts
export type WatchFile<X, Y> = (
    host: WatchFileHost, 
    file: string, 
    callback: FileWatcherCallback, 
    pollingInterval: PollingInterval, 
    detailInfo1: X, 
    detailInfo2?: Y) => FileWatcher;
```

### FilePathWatcherCallback
```ts
export type FilePathWatcherCallback = (
    fileName: string, 
    eventKind: FileWatcherEventKind, 
    filePath: Path) => void;
```

### WatchFilePath
```ts
 export type WatchFilePath<X, Y> = (
     host: WatchFileHost, 
     file: string, 
     callback: FilePathWatcherCallback, 
     pollingInterval: PollingInterval, 
     path: Path, 
     detailInfo1: X, 
     detailInfo2?: Y) => FileWatcher;
```

### WatchDirectory
```ts
export type WatchDirectory<X, Y> = (
    host: WatchDirectoryHost, 
    directory: string, 
    callback: DirectoryWatcherCallback, 
    flags: WatchDirectoryFlags, 
    detailInfo1: X, 
    detailInfo2?: Y) => FileWatcher;
```

### WatchDirectory
```ts
export type WatchDirectory<X, Y> = (
    host: WatchDirectoryHost, 
    directory: string, 
    callback: DirectoryWatcherCallback, 
    flags: WatchDirectoryFlags, 
    detailInfo1: X, 
    detailInfo2?: Y) => FileWatcher;
```

### WatchCallback
```ts
type WatchCallback<T, U> = (
    fileName: string, 
    cbOptional?: T, 
    passThrough?: U) => void;
```
### AddWatch
```ts
type AddWatch<H, T, U, V> = (
    host: H, 
    file: string, 
    cb: WatchCallback<U, V>, 
    flags: T, 
    passThrough?: V, 
    detailInfo1?: undefined, 
    detailInfo2?: undefined) => FileWatcher;
```
### GetDetailWatchInfo
```ts
export type GetDetailWatchInfo<X, Y> = (
    detailInfo1: X, 
    detailInfo2: Y | undefined) => string;
```

### CreateFileWatcher
```ts
type CreateFileWatcher<H, T, U, V, X, Y> = (
    host: H, 
    file: string, 
    cb: WatchCallback<U, V>, 
    flags: T, 
    passThrough: V | undefined, 
    detailInfo1: X | undefined, 
    detailInfo2: Y | undefined, 
    addWatch: AddWatch<H, T, U, V>, 
    log: (s: string) => void, 
    watchCaption: string, 
    getDetailWatchInfo: GetDetailWatchInfo<X, Y> | undefined) => FileWatcher;
```

## 实例对象
## 功能函数

### createCachedDirectoryStructureHost
```ts
export function createCachedDirectoryStructureHost(
    host: DirectoryStructureHost, 
    currentDirectory: string, 
    useCaseSensitiveFileNames: boolean): CachedDirectoryStructureHost | undefined {
        ...
        return {
            useCaseSensitiveFileNames,
            fileExists,
            readFile: (path, encoding) => host.readFile(path, encoding),
            directoryExists: host.directoryExists && directoryExists,
            getDirectories,
            readDirectory,
            createDirectory: host.createDirectory && createDirectory,
            writeFile: host.writeFile && writeFile,
            addOrDeleteFileOrDirectory,
            addOrDeleteFile,
            clearCache,
            realpath: host.realpath && realpath
        };
        ...
    }
```

### updateMissingFilePathsWatch
```ts
export function updateMissingFilePathsWatch(
    program: Program,
    missingFileWatches: Map<FileWatcher>,
    createMissingFileWatch: (missingFilePath: Path) => FileWatcher,
) {
    const missingFilePaths = program.getMissingFilePaths();
    const newMissingFilePathMap = arrayToSet(missingFilePaths);
    mutateMap(
        missingFileWatches,
        newMissingFilePathMap,
        {
            createNewValue: createMissingFileWatch,
            onDeleteValue: closeFileWatcher
        }
    );
}
```

### updateWatchingWildcardDirectories
```ts
export function updateWatchingWildcardDirectories(
    existingWatchedForWildcards: Map<WildcardDirectoryWatcher>,
    wildcardDirectories: Map<WatchDirectoryFlags>,
    watchDirectory: (directory: string, flags: WatchDirectoryFlags) => FileWatcher
) {
    ...
}
```

### isEmittedFileOfProgram
```ts
export function isEmittedFileOfProgram(
    program: Program | undefined, file: string) {
    if (!program) {
        return false;
    }

    return program.isEmittedFile(file);
}
```

### getWatchFactory
```ts
export function getWatchFactory<X, Y = undefined>(
    watchLogLevel: WatchLogLevel, 
    log: (s: string) => void, 
    getDetailWatchInfo?: GetDetailWatchInfo<X, Y>): WatchFactory<X, Y> {

    return getWatchFactoryWith(watchLogLevel, log, getDetailWatchInfo, watchFile, watchDirectory);

}
```

### closeFileWatcherOf
```ts
export function closeFileWatcherOf<T extends { watcher: FileWatcher; }>(objWithWatcher: T) {
    objWithWatcher.watcher.close();
}
```

## 内部函数
### getWatchFactoryWith
```ts
function getWatchFactoryWith<X, Y = undefined>(
    watchLogLevel: WatchLogLevel, 
    log: (s: string) => void, 
    getDetailWatchInfo: GetDetailWatchInfo<X, Y> | undefined,
    watchFile: (host: WatchFileHost, file: string, callback: FileWatcherCallback, watchPriority: PollingInterval) => FileWatcher,
    watchDirectory: (host: WatchDirectoryHost, directory: string, callback: DirectoryWatcherCallback, flags: WatchDirectoryFlags) => FileWatcher): WatchFactory<X, Y> {
    ...
}
```

### watchFile
```ts
function watchFile(
    host: WatchFileHost, 
    file: string, 
    callback: FileWatcherCallback, 
    pollingInterval: PollingInterval): FileWatcher {
    return host.watchFile(file, callback, pollingInterval);
}
```

### watchDirectory
```ts
function watchDirectory(
    host: WatchDirectoryHost, 
    directory: string, 
    callback: DirectoryWatcherCallback, 
    flags: WatchDirectoryFlags): FileWatcher {
    return host.watchDirectory(directory, callback, (flags & WatchDirectoryFlags.Recursive) !== 0);
}
```

### getCreateFileWatcher
```ts
function getCreateFileWatcher<H, T, U, V, X, Y>(
    watchLogLevel: WatchLogLevel, 
    addWatch: AddWatch<H, T, U, V>): CreateFileWatcher<H, T, U, V, X, Y> {
    switch (watchLogLevel) {
        case WatchLogLevel.None:
            return addWatch;
        case WatchLogLevel.TriggerOnly:
            return createFileWatcherWithTriggerLogging;
        case WatchLogLevel.Verbose:
            return addWatch === <any>watchDirectory ? createDirectoryWatcherWithLogging : createFileWatcherWithLogging;
    }
}
```

### createFileWatcherWithLogging
```ts
function createFileWatcherWithLogging<H, T, U, V, X, Y>(
    host: H, 
    file: string, 
    cb: WatchCallback<U, V>, 
    flags: T, 
    passThrough: V | undefined, 
    detailInfo1: X | undefined, 
    detailInfo2: Y | undefined, 
    addWatch: AddWatch<H, T, U, V>, 
    log: (s: string) => void, 
    watchCaption: string, 
    getDetailWatchInfo: GetDetailWatchInfo<X, Y> | undefined): FileWatcher {

}
```

### createDirectoryWatcherWithLogging
```ts
function createDirectoryWatcherWithLogging<H, T, U, V, X, Y>(
    host: H, 
    file: string, 
    cb: WatchCallback<U, V>, 
    flags: T, 
    passThrough: V | undefined, 
    detailInfo1: X | undefined, 
    detailInfo2: Y | undefined, 
    addWatch: AddWatch<H, T, U, V>, 
    log: (s: string) => void, 
    watchCaption: string, 
    getDetailWatchInfo: GetDetailWatchInfo<X, Y> | undefined): FileWatcher {

}
```

### createFileWatcherWithTriggerLogging
```ts
function createFileWatcherWithTriggerLogging<H, T, U, V, X, Y>(
    host: H, 
    file: string, 
    cb: WatchCallback<U, V>, 
    flags: T, 
    passThrough: V | undefined, 
    detailInfo1: X | undefined, 
    detailInfo2: Y | undefined, 
    addWatch: AddWatch<H, T, U, V>, 
    log: (s: string) => void, 
    watchCaption: string, 
    getDetailWatchInfo: GetDetailWatchInfo<X, Y> | undefined): FileWatcher {

}
```

### getWatchInfo
```ts
function getWatchInfo<T, X, Y>(
     file: string, 
     flags: T, 
     detailInfo1: X, 
     detailInfo2: Y | undefined, 
     getDetailWatchInfo: GetDetailWatchInfo<X, Y> | undefined) {
    return `WatchInfo: ${file} ${flags} ${getDetailWatchInfo ? getDetailWatchInfo(detailInfo1, detailInfo2) : detailInfo2 === undefined ? detailInfo1 : `${detailInfo1} ${detailInfo2}`}`;
}
```