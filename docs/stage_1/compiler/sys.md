## 类型接口
### Timeout
```ts
declare function setTimeout(handler: (...args: any[]) => void, timeout: number): any;
declare function clearTimeout(handle: any): void;
```

### FileWatcherEventKind
```ts
export enum FileWatcherEventKind {
    Created,
    Changed,
    Deleted
}
```
### FileWatcherCallback
```ts
export type FileWatcherCallback = (fileName: string, eventKind: FileWatcherEventKind) => void;
```

### DirectoryWatcherCallback
```ts
export type DirectoryWatcherCallback = (fileName: string) => void;
```

### WatchedFile
```ts
export interface WatchedFile {
    readonly fileName: string;
    readonly callback: FileWatcherCallback;
    mtime: Date;
}
```

### PollingInterval
```ts
export enum PollingInterval {
    High = 2000,
    Medium = 500,
    Low = 250
}
```

### HostWatchFile
```ts
export type HostWatchFile = (
    fileName: string, 
    callback: FileWatcherCallback, 
    pollingInterval: PollingInterval | undefined
    ) => FileWatcher;
```

### HostWatchDirectory
```ts
export type HostWatchDirectory = (
    fileName: string, 
    callback: DirectoryWatcherCallback, 
    recursive?: boolean) => FileWatcher;
```

### Levels
```ts
interface Levels {
    Low: number;
    Medium: number;
    High: number;
}
```

### RecursiveDirectoryWatcherHost
```ts
export interface RecursiveDirectoryWatcherHost {
    watchDirectory: HostWatchDirectory;
    useCaseSensitiveFileNames: boolean;
    getAccessibleSortedChildDirectories(path: string): readonly string[];
    directoryExists(dir: string): boolean;
    realpath(s: string): string;
}
```

### BufferEncoding
```ts
export type BufferEncoding = "ascii" | "utf8" | "utf-8" | "utf16le" | "ucs2" | "ucs-2" | "base64" | "latin1" | "binary" | "hex";
```

### NodeBuffer
```ts
interface NodeBuffer extends Uint8Array {
    constructor: any;
    write(str: string, encoding?: BufferEncoding): number;
    write(str: string, offset: number, encoding?: BufferEncoding): number;
    write(str: string, offset: number, length: number, encoding?: BufferEncoding): number;
    toString(encoding?: string, start?: number, end?: number): string;
    toJSON(): { type: "Buffer"; data: number[] };
    equals(otherBuffer: Uint8Array): boolean;
    compare(
        otherBuffer: Uint8Array,
        targetStart?: number,
        targetEnd?: number,
        sourceStart?: number,
        sourceEnd?: number
    ): number;
    copy(targetBuffer: Uint8Array, targetStart?: number, sourceStart?: number, sourceEnd?: number): number;
    slice(begin?: number, end?: number): Buffer;
    subarray(begin?: number, end?: number): Buffer;
    writeUIntLE(value: number, offset: number, byteLength: number): number;
    writeUIntBE(value: number, offset: number, byteLength: number): number;
    writeIntLE(value: number, offset: number, byteLength: number): number;
    writeIntBE(value: number, offset: number, byteLength: number): number;
    readUIntLE(offset: number, byteLength: number): number;
    readUIntBE(offset: number, byteLength: number): number;
    readIntLE(offset: number, byteLength: number): number;
    readIntBE(offset: number, byteLength: number): number;
    readUInt8(offset: number): number;
    readUInt16LE(offset: number): number;
    readUInt16BE(offset: number): number;
    readUInt32LE(offset: number): number;
    readUInt32BE(offset: number): number;
    readInt8(offset: number): number;
    readInt16LE(offset: number): number;
    readInt16BE(offset: number): number;
    readInt32LE(offset: number): number;
    readInt32BE(offset: number): number;
    readFloatLE(offset: number): number;
    readFloatBE(offset: number): number;
    readDoubleLE(offset: number): number;
    readDoubleBE(offset: number): number;
    reverse(): this;
    swap16(): Buffer;
    swap32(): Buffer;
    swap64(): Buffer;
    writeUInt8(value: number, offset: number): number;
    writeUInt16LE(value: number, offset: number): number;
    writeUInt16BE(value: number, offset: number): number;
    writeUInt32LE(value: number, offset: number): number;
    writeUInt32BE(value: number, offset: number): number;
    writeInt8(value: number, offset: number): number;
    writeInt16LE(value: number, offset: number): number;
    writeInt16BE(value: number, offset: number): number;
    writeInt32LE(value: number, offset: number): number;
    writeInt32BE(value: number, offset: number): number;
    writeFloatLE(value: number, offset: number): number;
    writeFloatBE(value: number, offset: number): number;
    writeDoubleLE(value: number, offset: number): number;
    writeDoubleBE(value: number, offset: number): number;
    readBigUInt64BE(offset?: number): bigint;
    readBigUInt64LE(offset?: number): bigint;
    readBigInt64BE(offset?: number): bigint;
    readBigInt64LE(offset?: number): bigint;
    writeBigInt64BE(value: bigint, offset?: number): number;
    writeBigInt64LE(value: bigint, offset?: number): number;
    writeBigUInt64BE(value: bigint, offset?: number): number;
    writeBigUInt64LE(value: bigint, offset?: number): number;
    fill(value: string | Uint8Array | number, offset?: number, end?: number, encoding?: BufferEncoding): this;
    indexOf(value: string | number | Uint8Array, byteOffset?: number, encoding?: BufferEncoding): number;
    lastIndexOf(value: string | number | Uint8Array, byteOffset?: number, encoding?: BufferEncoding): number;
    entries(): IterableIterator<[number, number]>;
    includes(value: string | number | Buffer, byteOffset?: number, encoding?: BufferEncoding): boolean;
    keys(): IterableIterator<number>;
    values(): IterableIterator<number>;
}
```

### Buffer
```ts
interface Buffer extends NodeBuffer { }
```

### System
```ts
export interface System {
    args: string[];
    newLine: string;
    useCaseSensitiveFileNames: boolean;
    write(s: string): void;
    writeOutputIsTTY?(): boolean;
    readFile(path: string, encoding?: string): string | undefined;
    getFileSize?(path: string): number;
    writeFile(path: string, data: string, writeByteOrderMark?: boolean): void;

    watchFile?(path: string, callback: FileWatcherCallback, pollingInterval?: number): FileWatcher;
    watchDirectory?(path: string, callback: DirectoryWatcherCallback, recursive?: boolean): FileWatcher;
    resolvePath(path: string): string;
    fileExists(path: string): boolean;
    directoryExists(path: string): boolean;
    createDirectory(path: string): void;
    getExecutingFilePath(): string;
    getCurrentDirectory(): string;
    getDirectories(path: string): string[];
    readDirectory(path: string, extensions?: readonly string[], exclude?: readonly string[], include?: readonly string[], depth?: number): string[];
    getModifiedTime?(path: string): Date | undefined;
    setModifiedTime?(path: string, time: Date): void;
    deleteFile?(path: string): void;
   
    createHash?(data: string): string;
    createSHA256Hash?(data: string): string;
    getMemoryUsage?(): number;
    exit(exitCode?: number): void;
    /*@internal*/ enableCPUProfiler?(path: string, continuation: () => void): boolean;
    /*@internal*/ disableCPUProfiler?(continuation: () => void): boolean;
    realpath?(path: string): string;
    /*@internal*/ getEnvironmentVariable(name: string): string;
    /*@internal*/ tryEnableSourceMapsForHost?(): void;
    /*@internal*/ debugMode?: boolean;
    setTimeout?(callback: (...args: any[]) => void, ms: number, ...args: any[]): any;
    clearTimeout?(timeoutId: any): void;
    clearScreen?(): void;
    /*@internal*/ setBlocking?(): void;
    base64decode?(input: string): string;
    base64encode?(input: string): string;
    /*@internal*/ bufferFrom?(input: string, encoding?: string): Buffer;
    // For testing
    /*@internal*/ now?(): Date;
    /*@internal*/ require?(baseDir: string, moduleName: string): RequireResult;
}
```

### FileWatcher
```ts
export interface FileWatcher {
    close(): void;
}
```

### DirectoryWatcher
```ts
interface DirectoryWatcher extends FileWatcher {
    referenceCount: number;
}
```


## 实例对象
### Base
```ts
export const missingFileModifiedTime = new Date(0);
const defaultChunkLevels: Levels = { Low: 32, Medium: 64, High: 256 };
export let unchangedPollThresholds = createPollingIntervalBasedLevels(defaultChunkLevels);
export const ignoredPaths = ["/node_modules/.", "/.git", "/.#"];
export let sysLog: (s: string) => void = noop;

declare const require: any;
declare const process: any;
declare const global: any;
declare const __filename: string;
declare const __dirname: string;


declare const ChakraHost: {
    args: string[];
    currentDirectory: string;
    executingFile: string;
    newLine?: string;
    useCaseSensitiveFileNames?: boolean;
    echo(s: string): void;
    quit(exitCode?: number): void;
    fileExists(path: string): boolean;
    deleteFile(path: string): boolean;
    getModifiedTime(path: string): Date;
    setModifiedTime(path: string, time: Date): void;
    directoryExists(path: string): boolean;
    createDirectory(path: string): void;
    resolvePath(path: string): string;
    readFile(path: string): string | undefined;
    writeFile(path: string, contents: string): void;
    getDirectories(path: string): string[];
    readDirectory(path: string, extensions?: readonly string[], basePaths?: readonly string[], excludeEx?: string, includeFileEx?: string, includeDirEx?: string): string[];
    watchFile?(path: string, callback: FileWatcherCallback): FileWatcher;
    watchDirectory?(path: string, callback: DirectoryWatcherCallback, recursive?: boolean): FileWatcher;
    realpath(path: string): string;
    getEnvironmentVariable?(name: string): string;
};

export let sys: System = (() => {
    
    const byteOrderMarkIndicator = "\uFEFF";
    function getNodeSystem(): System {}
    function getChakraSystem(): System {}

    let sys: System | undefined;
    if (typeof ChakraHost !== "undefined") {
        sys = getChakraSystem();
    }
    else if (typeof process !== "undefined" && process.nextTick && !process.browser && typeof require !== "undefined") {
        // process and process.nextTick checks if current environment is node-like
        // process.browser check excludes webpack and browserify
        sys = getNodeSystem();
    }
    if (sys) {
        // patch writefile to create folder before writing the file
        patchWriteFileEnsuringDirectory(sys);
    }
    return sys!;
})();

    if (sys && sys.getEnvironmentVariable) {
        setCustomPollingValues(sys);
        Debug.currentAssertionLevel = /^development$/i.test(sys.getEnvironmentVariable("NODE_ENV"))
            ? AssertionLevel.Normal
            : AssertionLevel.None;
    }
    if (sys && sys.debugMode) {
        Debug.isDebugging = true;
    }
}
```

## 功能函数
### generateDjb2Hash
```ts
export function generateDjb2Hash(data: string): string {
    let acc = 5381;
    for (let i = 0; i < data.length; i++) {
        acc = ((acc << 5) + acc) + data.charCodeAt(i);
    }
    return acc.toString();
}
```
### setStackTraceLimit
```ts
export function setStackTraceLimit() {
    if ((Error as any).stackTraceLimit < 100) { 
        (Error as any).stackTraceLimit = 100;
    }
}
```
### setCustomPollingValues
```ts
export function setCustomPollingValues(system: System) {
    ...
}
```

### createDynamicPriorityPollingWatchFile
```ts
export function createDynamicPriorityPollingWatchFile(
    host: { getModifiedTime: System["getModifiedTime"]; setTimeout: System["setTimeout"]; })
    : HostWatchFile {
        ...
}
```

### createSingleFileWatcherPerName
```ts
export function createSingleFileWatcherPerName(
        watchFile: HostWatchFile,
        useCaseSensitiveFileNames: boolean
    ): HostWatchFile {
    ...
}
```
### onWatchedFileStat
```ts
export function onWatchedFileStat(watchedFile: WatchedFile, modifiedTime: Date): boolean {
    ...
}
```

### getFileWatcherEventKind
```ts
export function getFileWatcherEventKind(oldTime: number, newTime: number) {
    return oldTime === 0
        ? FileWatcherEventKind.Created
        : newTime === 0
            ? FileWatcherEventKind.Deleted
            : FileWatcherEventKind.Changed;
}
```

### createRecursiveDirectoryWatcher
```ts
export function createRecursiveDirectoryWatcher(host: RecursiveDirectoryWatcherHost): (directoryName: string, callback: DirectoryWatcherCallback) => FileWatcher {
    ...
}
```

### patchWriteFileEnsuringDirectory
```ts
export function patchWriteFileEnsuringDirectory(sys: System) {
    ...
}
```

### getNodeMajorVersion
```ts
export function getNodeMajorVersion(): number | undefined {
    if (typeof process === "undefined") {
        return undefined;
    }
    const version: string = process.version;
    if (!version) {
        return undefined;
    }
    const dot = version.indexOf(".");
    if (dot === -1) {
        return undefined;
    }
    return parseInt(version.substring(1, dot));
}
```

## 内部函数

### createPollingIntervalBasedLevels
```ts
function createPollingIntervalBasedLevels(levels: Levels) {
    return {
        [PollingInterval.Low]: levels.Low,
        [PollingInterval.Medium]: levels.Medium,
        [PollingInterval.High]: levels.High
    };
}
```