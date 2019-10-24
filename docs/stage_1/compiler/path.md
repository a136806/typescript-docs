## 类型接口
## 实例对象
### Base
```ts
export const directorySeparator = "/";
const altDirectorySeparator = "\\";
const urlSchemeSeparator = "://";
const backslashRegExp = /\\/g;
const relativePathSegmentRegExp = /(^|\/)\.{0,2}($|\/)/;
```
## 功能函数

### isAnyDirectorySeparator
```ts
export function isAnyDirectorySeparator(charCode: number): boolean {
    return charCode === CharacterCodes.slash 
        || charCode === CharacterCodes.backslash;
}
```

### isUrl
```ts
export function isUrl(path: string) {
    return getEncodedRootLength(path) < 0;
}
```

### isRootedDiskPath
```ts
export function isRootedDiskPath(path: string) {
    return getEncodedRootLength(path) > 0;
}
```

### isDiskPathRoot
```ts
export function isDiskPathRoot(path: string) {
    const rootLength = getEncodedRootLength(path);
    return rootLength > 0 && rootLength === path.length;
}
```

### pathIsAbsolute
```ts
export function pathIsAbsolute(path: string): boolean {
    return getEncodedRootLength(path) !== 0;
}
```

### pathIsRelative
```ts
export function pathIsRelative(path: string): boolean {
    return /^\.\.?($|[\\/])/.test(path);
}
```

### hasExtension
```ts
export function hasExtension(fileName: string): boolean {
    return stringContains(getBaseFileName(fileName), ".");
}
```

### fileExtensionIs
```ts
export function fileExtensionIs(path: string, extension: string): boolean {
    return path.length > extension.length && endsWith(path, extension);
}
```

### fileExtensionIsOneOf
```ts
export function fileExtensionIsOneOf(path: string, extensions: readonly string[]): boolean {
    for (const extension of extensions) {
        if (fileExtensionIs(path, extension)) {
            return true;
        }
    }

    return false;
}
```

### hasTrailingDirectorySeparator
```ts
export function hasTrailingDirectorySeparator(path: string) {
    return path.length > 0 && isAnyDirectorySeparator(path.charCodeAt(path.length - 1));
}
```

### getRootLength
```ts
export function getRootLength(path: string) {
    const rootLength = getEncodedRootLength(path);
    return rootLength < 0 ? ~rootLength : rootLength;
}
```

### getDirectoryPath
```ts
export function getDirectoryPath(path: Path): Path;
export function getDirectoryPath(path: string): string;
export function getDirectoryPath(path: string): string {
    ...
}
```

### getBaseFileName
```ts
export function getBaseFileName(path: string): string;
export function getBaseFileName(path: string, extensions: string | readonly string[], ignoreCase: boolean): string;
export function getBaseFileName(path: string, extensions?: string | readonly string[], ignoreCase?: boolean) {
    ...
}
```

### getAnyExtensionFromPath
```ts
export function getAnyExtensionFromPath(path: string): string;
export function getAnyExtensionFromPath(path: string, extensions: string | readonly string[], ignoreCase: boolean): string;
export function getAnyExtensionFromPath(path: string, extensions?: string | readonly string[], ignoreCase?: boolean): string {
    ...
}
```

### getPathComponents
```ts
export function getPathComponents(path: string, currentDirectory = "") {
    path = combinePaths(currentDirectory, path);
    return pathComponents(path, getRootLength(path));
}
```

### getPathFromPathComponents
```ts
export function getPathFromPathComponents(pathComponents: readonly string[]) {
    if (pathComponents.length === 0) return "";

    const root = pathComponents[0] && ensureTrailingDirectorySeparator(pathComponents[0]);
    return root + pathComponents.slice(1).join(directorySeparator);
}
```

### normalizeSlashes
```ts
export function normalizeSlashes(path: string): string {
    return path.replace(backslashRegExp, directorySeparator);
}
```

### reducePathComponents
```ts
export function reducePathComponents(components: readonly string[]) {
    ...
}
```

### combinePaths
```ts
export function combinePaths(path: string, ...paths: (string | undefined)[]): string {
    ...
}
```

### resolvePath
```ts
export function resolvePath(path: string, ...paths: (string | undefined)[]): string {
    return normalizePath(some(paths) ? combinePaths(path, ...paths) : normalizeSlashes(path));
}
```

### resolvePath
```ts
export function resolvePath(path: string, ...paths: (string | undefined)[]): string {
    return normalizePath(some(paths) ? combinePaths(path, ...paths) : normalizeSlashes(path));
}
```

### getNormalizedPathComponents
```ts
export function getNormalizedPathComponents(path: string, currentDirectory: string | undefined) {
    return reducePathComponents(getPathComponents(path, currentDirectory));
}
```

### getNormalizedAbsolutePath
```ts
export function getNormalizedAbsolutePath(fileName: string, currentDirectory: string | undefined) {
    return getPathFromPathComponents(getNormalizedPathComponents(fileName, currentDirectory));
}
```

### normalizePath
```ts
export function normalizePath(path: string): string {
    path = normalizeSlashes(path);
    const normalized = getPathFromPathComponents(reducePathComponents(getPathComponents(path)));
    return normalized && hasTrailingDirectorySeparator(path) ? ensureTrailingDirectorySeparator(normalized) : normalized;
}
```

### getNormalizedAbsolutePathWithoutRoot
```ts
export function getNormalizedAbsolutePathWithoutRoot(fileName: string, currentDirectory: string | undefined) {
    return getPathWithoutRoot(getNormalizedPathComponents(fileName, currentDirectory));
}
```

### toPath
```ts
export function toPath(fileName: string, basePath: string | undefined, getCanonicalFileName: (path: string) => string): Path {
    const nonCanonicalizedPath = isRootedDiskPath(fileName)
        ? normalizePath(fileName)
        : getNormalizedAbsolutePath(fileName, basePath);
    return <Path>getCanonicalFileName(nonCanonicalizedPath);
}
```

### normalizePathAndParts
```ts
export function normalizePathAndParts(path: string): { path: string, parts: string[] } {
    path = normalizeSlashes(path);
    const [root, ...parts] = reducePathComponents(getPathComponents(path));
    if (parts.length) {
        const joinedParts = root + parts.join(directorySeparator);
        return { path: hasTrailingDirectorySeparator(path) ? ensureTrailingDirectorySeparator(joinedParts) : joinedParts, parts };
    }
    else {
        return { path: root, parts };
    }
}
```

### removeTrailingDirectorySeparator
```ts
export function removeTrailingDirectorySeparator(path: Path): Path;
export function removeTrailingDirectorySeparator(path: string): string;
export function removeTrailingDirectorySeparator(path: string) {
    if (hasTrailingDirectorySeparator(path)) {
        return path.substr(0, path.length - 1);
    }

    return path;
}
```

### ensureTrailingDirectorySeparator
```ts
export function ensureTrailingDirectorySeparator(path: Path): Path;
export function ensureTrailingDirectorySeparator(path: string): string;
export function ensureTrailingDirectorySeparator(path: string) {
    if (!hasTrailingDirectorySeparator(path)) {
        return path + directorySeparator;
    }

    return path;
}
```

### ensurePathIsNonModuleName
```ts
export function ensurePathIsNonModuleName(path: string): string {
    return !pathIsAbsolute(path) && !pathIsRelative(path) ? "./" + path : path;
}
```

### changeAnyExtension
```ts
export function changeAnyExtension(path: string, ext: string): string;
export function changeAnyExtension(path: string, ext: string, extensions: string | readonly string[], ignoreCase: boolean): string;
export function changeAnyExtension(path: string, ext: string, extensions?: string | readonly string[], ignoreCase?: boolean) {
    const pathext = extensions !== undefined && ignoreCase !== undefined ? getAnyExtensionFromPath(path, extensions, ignoreCase) : getAnyExtensionFromPath(path);
    return pathext ? path.slice(0, path.length - pathext.length) + (startsWith(ext, ".") ? ext : "." + ext) : path;
}
```

### comparePathsCaseSensitive
```ts
export function comparePathsCaseSensitive(a: string, b: string) {
    return comparePathsWorker(a, b, compareStringsCaseSensitive);
}
```

### comparePathsCaseInsensitive
```ts
export function comparePathsCaseInsensitive(a: string, b: string) {
    return comparePathsWorker(a, b, compareStringsCaseInsensitive);
}
```

### comparePaths
```ts
export function comparePaths(a: string, b: string, ignoreCase?: boolean): Comparison;
export function comparePaths(a: string, b: string, currentDirectory: string, ignoreCase?: boolean): Comparison;
export function comparePaths(a: string, b: string, currentDirectory?: string | boolean, ignoreCase?: boolean) {
    if (typeof currentDirectory === "string") {
        a = combinePaths(currentDirectory, a);
        b = combinePaths(currentDirectory, b);
    }
    else if (typeof currentDirectory === "boolean") {
        ignoreCase = currentDirectory;
    }
    return comparePathsWorker(a, b, getStringComparer(ignoreCase));
}
```

###  containsPath
```ts
export function containsPath(parent: string, child: string, ignoreCase?: boolean): boolean;
export function containsPath(parent: string, child: string, currentDirectory: string, ignoreCase?: boolean): boolean;
export function containsPath(parent: string, child: string, currentDirectory?: string | boolean, ignoreCase?: boolean) {
    if (typeof currentDirectory === "string") {
        parent = combinePaths(currentDirectory, parent);
        child = combinePaths(currentDirectory, child);
    }
    else if (typeof currentDirectory === "boolean") {
        ignoreCase = currentDirectory;
    }
    if (parent === undefined || child === undefined) return false;
    if (parent === child) return true;
    const parentComponents = reducePathComponents(getPathComponents(parent));
    const childComponents = reducePathComponents(getPathComponents(child));
    if (childComponents.length < parentComponents.length) {
        return false;
    }

    const componentEqualityComparer = ignoreCase ? equateStringsCaseInsensitive : equateStringsCaseSensitive;
    for (let i = 0; i < parentComponents.length; i++) {
        const equalityComparer = i === 0 ? equateStringsCaseInsensitive : componentEqualityComparer;
        if (!equalityComparer(parentComponents[i], childComponents[i])) {
            return false;
        }
    }

    return true;
}
```

### startsWithDirectory
```ts
export function startsWithDirectory(
    fileName: string, 
    directoryName: string, 
    getCanonicalFileName: GetCanonicalFileName): boolean {
    const canonicalFileName = getCanonicalFileName(fileName);
    const canonicalDirectoryName = getCanonicalFileName(directoryName);
    return startsWith(canonicalFileName, canonicalDirectoryName + "/") || startsWith(canonicalFileName, canonicalDirectoryName + "\\");
}
```

### getPathComponentsRelativeTo
```ts
export function getPathComponentsRelativeTo(
    from: string, 
    to: string, 
    stringEqualityComparer: (a: string, b: string) => boolean, 
    getCanonicalFileName: GetCanonicalFileName) {
    ...
}
```

### getRelativePathFromDirectory
```ts
export function getRelativePathFromDirectory(
    from: string, 
    to: string, 
    ignoreCase: boolean): string;
export function getRelativePathFromDirectory(
    fromDirectory: string, 
    to: string, 
    getCanonicalFileName: GetCanonicalFileName): string;
export function getRelativePathFromDirectory(
    fromDirectory: string, 
    to: string, 
    getCanonicalFileNameOrIgnoreCase: GetCanonicalFileName | boolean) {
    
    Debug.assert((getRootLength(fromDirectory) > 0) === (getRootLength(to) > 0), "Paths must either both be absolute or both be relative");
    const getCanonicalFileName = typeof getCanonicalFileNameOrIgnoreCase === "function" ? getCanonicalFileNameOrIgnoreCase : identity;
    const ignoreCase = typeof getCanonicalFileNameOrIgnoreCase === "boolean" ? getCanonicalFileNameOrIgnoreCase : false;
    const pathComponents = getPathComponentsRelativeTo(fromDirectory, to, ignoreCase ? equateStringsCaseInsensitive : equateStringsCaseSensitive, getCanonicalFileName);
    return getPathFromPathComponents(pathComponents);
}

```

### convertToRelativePath
```ts
export function convertToRelativePath(
    absoluteOrRelativePath: string,
    basePath: string, 
    getCanonicalFileName: (path: string) => string): string {

    return !isRootedDiskPath(absoluteOrRelativePath)
        ? absoluteOrRelativePath
        : getRelativePathToDirectoryOrUrl(basePath, absoluteOrRelativePath, basePath, getCanonicalFileName, /*isAbsolutePathAnUrl*/ false);
}
```

### getRelativePathFromFile
```ts
export function getRelativePathFromFile(
    from: string, 
    to: string, 
    getCanonicalFileName: GetCanonicalFileName) {
    return ensurePathIsNonModuleName(getRelativePathFromDirectory(getDirectoryPath(from), to, getCanonicalFileName));
}
```

### getRelativePathToDirectoryOrUrl
```ts
export function getRelativePathToDirectoryOrUrl(directoryPathOrUrl: string, relativeOrAbsolutePath: string, currentDirectory: string, getCanonicalFileName: GetCanonicalFileName, isAbsolutePathAnUrl: boolean) {
    const pathComponents = getPathComponentsRelativeTo(
        resolvePath(currentDirectory, directoryPathOrUrl),
        resolvePath(currentDirectory, relativeOrAbsolutePath),
        equateStringsCaseSensitive,
        getCanonicalFileName
    );

    const firstComponent = pathComponents[0];
    if (isAbsolutePathAnUrl && isRootedDiskPath(firstComponent)) {
        const prefix = firstComponent.charAt(0) === directorySeparator ? "file://" : "file:///";
        pathComponents[0] = prefix + firstComponent;
    }

    return getPathFromPathComponents(pathComponents);
}
```

### forEachAncestorDirectory
```ts
export function forEachAncestorDirectory<T>(directory: Path, callback: (directory: Path) => T | undefined): T | undefined;
export function forEachAncestorDirectory<T>(directory: string, callback: (directory: string) => T | undefined): T | undefined;
export function forEachAncestorDirectory<T>(directory: Path, callback: (directory: Path) => T | undefined): T | undefined {
    while (true) {
        const result = callback(directory);
        if (result !== undefined) {
            return result;
        }

        const parentPath = getDirectoryPath(directory);
        if (parentPath === directory) {
            return undefined;
        }

        directory = parentPath;
    }
}
```

## 内部函数

### isVolumeCharacter
```ts
function isVolumeCharacter(charCode: number) {
    return (charCode >= CharacterCodes.a && charCode <= CharacterCodes.z) 
    || (charCode >= CharacterCodes.A && charCode <= CharacterCodes.Z);
}
```

### getFileUrlVolumeSeparatorEnd
```ts
function getFileUrlVolumeSeparatorEnd(url: string, start: number) {
    const ch0 = url.charCodeAt(start);
    if (ch0 === CharacterCodes.colon) return start + 1;
    if (ch0 === CharacterCodes.percent && url.charCodeAt(start + 1) === CharacterCodes._3) {
        const ch2 = url.charCodeAt(start + 2);
        if (ch2 === CharacterCodes.a || ch2 === CharacterCodes.A) return start + 3;
    }
    return -1;
}
```

### getEncodedRootLength
```ts
function getEncodedRootLength(path: string): number {
    ...
}
```

### tryGetExtensionFromPath
```ts
function tryGetExtensionFromPath(path: string, extension: string, stringEqualityComparer: (a: string, b: string) => boolean) {
    ...
}
```

### getAnyExtensionFromPathWorker 
```ts
function getAnyExtensionFromPathWorker(path: string, extensions: string | readonly string[], stringEqualityComparer: (a: string, b: string) => boolean) {
    ...
}
```

### pathComponents
```ts
function pathComponents(path: string, rootLength: number) {
    const root = path.substring(0, rootLength);
    const rest = path.substring(rootLength).split(directorySeparator);
    if (rest.length && !lastOrUndefined(rest)) rest.pop();
    return [root, ...rest];
}
```

### getPathWithoutRoot
```ts
function getPathWithoutRoot(pathComponents: readonly string[]) {
    if (pathComponents.length === 0) return "";
    return pathComponents.slice(1).join(directorySeparator);
}
```

### comparePathsWorker
```ts
function comparePathsWorker(a: string, b: string, componentComparer: (a: string, b: string) => Comparison) {
    ...
}
```
