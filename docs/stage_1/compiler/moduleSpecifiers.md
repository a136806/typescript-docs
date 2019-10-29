## 类型接口
### RelativePreference
```ts
const enum RelativePreference { Relative, NonRelative, Auto }
```

### Ending
```ts
const enum Ending { Minimal, Index, JsExtension }
```

### Preferences
```ts
interface Preferences {
    readonly relativePreference: RelativePreference;
    readonly ending: Ending;
}
```

### Info
```ts
interface Info {
    readonly getCanonicalFileName: GetCanonicalFileName;
    readonly sourceDirectory: Path;
}
```

### NodeModulePathParts
```ts
interface NodeModulePathParts {
    readonly topLevelNodeModulesIndex: number;
    readonly topLevelPackageNameIndex: number;
    readonly packageRootIndex: number;
    readonly fileNameIndex: number;
}
```
## 实例对象
## 功能函数
### updateModuleSpecifier
```ts
export function updateModuleSpecifier(
    compilerOptions: CompilerOptions,
    importingSourceFileName: Path,
    toFileName: string,
    host: ModuleSpecifierResolutionHost,
    files: readonly SourceFile[],
    redirectTargetsMap: RedirectTargetsMap,
    oldImportSpecifier: string,
): string | undefined {

    const res = getModuleSpecifierWorker(compilerOptions, importingSourceFileName, toFileName, host, files, redirectTargetsMap, getPreferencesForUpdate(compilerOptions, oldImportSpecifier));

    if (res === oldImportSpecifier) return undefined;
    return res;
}
```

### getModuleSpecifier
```ts
export function getModuleSpecifier(
    compilerOptions: CompilerOptions,
    importingSourceFile: SourceFile,
    importingSourceFileName: Path,
    toFileName: string,
    host: ModuleSpecifierResolutionHost,
    files: readonly SourceFile[],
    preferences: UserPreferences = {},
    redirectTargetsMap: RedirectTargetsMap,
): string {
    return getModuleSpecifierWorker(compilerOptions, importingSourceFileName, toFileName, host, files, redirectTargetsMap, getPreferences(preferences, compilerOptions, importingSourceFile));
}
```

### getNodeModulesPackageName
```ts
export function getNodeModulesPackageName(
    compilerOptions: CompilerOptions,
    importingSourceFileName: Path,
    nodeModulesFileName: string,
    host: ModuleSpecifierResolutionHost,
    files: readonly SourceFile[],
    redirectTargetsMap: RedirectTargetsMap,
): string | undefined {
    ...
}
```

### getModuleSpecifiers
```ts
export function getModuleSpecifiers(
    moduleSymbol: Symbol,
    compilerOptions: CompilerOptions,
    importingSourceFile: SourceFile,
    host: ModuleSpecifierResolutionHost,
    files: readonly SourceFile[],
    userPreferences: UserPreferences,
    redirectTargetsMap: RedirectTargetsMap,
): readonly string[] {

}
```

### countPathComponents
```ts
export function countPathComponents(path: string): number {
    let count = 0;
    for (let i = startsWith(path, "./") ? 2 : 0; i < path.length; i++) {
        if (path.charCodeAt(i) === CharacterCodes.slash) count++;
    }
    return count;
}
```

###
```ts

```

## 内部函数

### getPreferences
```ts
function getPreferences(
    { importModuleSpecifierPreference, importModuleSpecifierEnding }: UserPreferences, compilerOptions: CompilerOptions,
     importingSourceFile: SourceFile
): Preferences {

}
```

### getPreferencesForUpdate
```ts
function getPreferencesForUpdate(
    compilerOptions: CompilerOptions, 
    oldImportSpecifier: string
): Preferences {
    return {
        relativePreference: isExternalModuleNameRelative(oldImportSpecifier) ? RelativePreference.Relative : RelativePreference.NonRelative,
        ending: hasJSOrJsonFileExtension(oldImportSpecifier) ?
            Ending.JsExtension :
            getEmitModuleResolutionKind(compilerOptions) !== ModuleResolutionKind.NodeJs || endsWith(oldImportSpecifier, "index") ? Ending.Index : Ending.Minimal,
    };
}
```

### getModuleSpecifierWorker
```ts
function getModuleSpecifierWorker(
    compilerOptions: CompilerOptions,
    importingSourceFileName: Path,
    toFileName: string,
    host: ModuleSpecifierResolutionHost,
    files: readonly SourceFile[],
    redirectTargetsMap: RedirectTargetsMap,
    preferences: Preferences
): string {
    const info = getInfo(importingSourceFileName, host);
    
    const modulePaths = getAllModulePaths(files, importingSourceFileName, toFileName, info.getCanonicalFileName, host, redirectTargetsMap);
    
    return firstDefined(
        modulePaths, 
        moduleFileName => tryGetModuleNameAsNodeModule(moduleFileName, info, host, compilerOptions)
        ) || getLocalModuleSpecifier(toFileName, info, compilerOptions, preferences);
}
```

### getInfo
```ts
function getInfo(
    importingSourceFileName: Path, 
    host: ModuleSpecifierResolutionHost
): Info {
    const getCanonicalFileName = createGetCanonicalFileName(host.useCaseSensitiveFileNames ? host.useCaseSensitiveFileNames() : true);
    
    const sourceDirectory = getDirectoryPath(importingSourceFileName);
    return { getCanonicalFileName, sourceDirectory };
}
```

### getLocalModuleSpecifier
```ts
function getLocalModuleSpecifier(
    moduleFileName: string, 
    { getCanonicalFileName, sourceDirectory }: Info, 
    compilerOptions: CompilerOptions, 
    { ending, relativePreference }: Preferences
): string {

}
```

### usesJsExtensionOnImports
```ts
function usesJsExtensionOnImports({ imports }: SourceFile): boolean {
    return firstDefined(imports, ({ text }) => pathIsRelative(text) ? hasJSOrJsonFileExtension(text) : undefined) || false;
}
```

### numberOfDirectorySeparators
```ts
function numberOfDirectorySeparators(str: string) {
    const match = str.match(/\//g);
    return match ? match.length : 0;
}
```

### comparePathsByNumberOfDirectrorySeparators
```ts
function comparePathsByNumberOfDirectrorySeparators(a: string, b: string) {
    return compareValues(
        numberOfDirectorySeparators(a),
        numberOfDirectorySeparators(b)
    );
}
```

### getAllModulePaths
```ts
function getAllModulePaths(
    files: readonly SourceFile[], 
    importingFileName: string, 
    importedFileName: string, 
    getCanonicalFileName: GetCanonicalFileName,
    host: ModuleSpecifierResolutionHost, 
    redirectTargetsMap: RedirectTargetsMap): readonly string[] {

}
```

### tryGetModuleNameFromAmbientModule
```ts
function tryGetModuleNameFromAmbientModule(
    moduleSymbol: Symbol): string | undefined {
    const decl = find(moduleSymbol.declarations,
        d => isNonGlobalAmbientModule(d) && (!isExternalModuleAugmentation(d) || !isExternalModuleNameRelative(getTextOfIdentifierOrLiteral(d.name)))
    ) as (ModuleDeclaration & { name: StringLiteral }) | undefined;

    if (decl) {
        return decl.name.text;
    }
}
```

### tryGetModuleNameFromPaths
```ts
function tryGetModuleNameFromPaths(
    relativeToBaseUrlWithIndex: string, 
    relativeToBaseUrl: string, 
    paths: MapLike<readonly string[]>
): string | undefined {

}
```

### tryGetModuleNameFromRootDirs
```ts
function tryGetModuleNameFromRootDirs(
    rootDirs: readonly string[], 
    moduleFileName: string, 
    sourceDirectory: string, 
    getCanonicalFileName: (file: string) => string, 
    ending: Ending, 
    compilerOptions: CompilerOptions
): string | undefined {
    ...
}
```

### tryGetModuleNameAsNodeModule
```ts
function tryGetModuleNameAsNodeModule(
    moduleFileName: string, 
    { getCanonicalFileName, sourceDirectory }: Info, 
    host: ModuleSpecifierResolutionHost, 
    options: CompilerOptions,
     packageNameOnly?: boolean
): string | undefined {
    ...
}
```

### tryGetAnyFileFromPath
```ts
function tryGetAnyFileFromPath(
    host: ModuleSpecifierResolutionHost, 
    path: string
) {
    ...
}
```

### getNodeModulePathParts
```ts
function getNodeModulePathParts(
    fullPath: string
): NodeModulePathParts | undefined {

}
```

### getPathRelativeToRootDirs
```ts
function getPathRelativeToRootDirs(
    path: string, 
    rootDirs: readonly string[], 
    getCanonicalFileName: GetCanonicalFileName
): string | undefined {
    
    return firstDefined(rootDirs, rootDir => {
        const relativePath = getRelativePathIfInDirectory(path, rootDir, getCanonicalFileName)!; // TODO: GH#18217
        return isPathRelativeToParent(relativePath) ? undefined : relativePath;
    });
}
```

### removeExtensionAndIndexPostFix
```ts
function removeExtensionAndIndexPostFix(
    fileName: string, 
    ending: Ending, 
    options: CompilerOptions
): string {
    if (fileExtensionIs(fileName, Extension.Json)) return fileName;
    const noExtension = removeFileExtension(fileName);

    switch (ending) {
        case Ending.Minimal:
            return removeSuffix(noExtension, "/index");
        case Ending.Index:
            return noExtension;
        case Ending.JsExtension:
            return noExtension + getJSExtensionForFile(fileName, options);
        default:
            return Debug.assertNever(ending);
    }
}
```

### getJSExtensionForFile
```ts
function getJSExtensionForFile(fileName: string, options: CompilerOptions): Extension {
    const ext = extensionFromPath(fileName);
    switch (ext) {
        case Extension.Ts:
        case Extension.Dts:
            return Extension.Js;
        case Extension.Tsx:
            return options.jsx === JsxEmit.Preserve ? Extension.Jsx : Extension.Js;
        case Extension.Js:
        case Extension.Jsx:
        case Extension.Json:
            return ext;
        case Extension.TsBuildInfo:
            return Debug.fail(`Extension ${Extension.TsBuildInfo} is unsupported:: FileName:: ${fileName}`);
        default:
            return Debug.assertNever(ext);
    }
}
```

### getRelativePathIfInDirectory
```ts
function getRelativePathIfInDirectory(
    path: string, 
    directoryPath: string, 
    getCanonicalFileName: GetCanonicalFileName
): string | undefined {
    const relativePath = getRelativePathToDirectoryOrUrl(directoryPath, path, directoryPath, getCanonicalFileName, /*isAbsolutePathAnUrl*/ false);
    return isRootedDiskPath(relativePath) ? undefined : relativePath;
}
```

### isPathRelativeToParent
```ts
function isPathRelativeToParent(path: string): boolean {
    return startsWith(path, "..");
}
```