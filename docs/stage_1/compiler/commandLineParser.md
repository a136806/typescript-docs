## 类型接口
### OptionNameMap
```ts
export interface OptionNameMap {
    optionNameMap: Map<CommandLineOption>;
    shortOptionNames: Map<string>;
}
```

### OptionsBase
```ts
interface OptionsBase {
    [option: string]: CompilerOptionsValue | undefined;
}
```
 
### ParseCommandLineWorkerDiagnostics
```ts
type ParseCommandLineWorkerDiagnostics = [DiagnosticMessage, DiagnosticMessage];
```

### ParsedBuildCommand
```ts
export interface ParsedBuildCommand {
    buildOptions: BuildOptions;
    projects: string[];
    errors: Diagnostic[];
}
```

### DiagnosticReporter
```ts
export type DiagnosticReporter = (diagnostic: Diagnostic) => void;
```

### ConfigFileDiagnosticsReporter
```ts
export interface ConfigFileDiagnosticsReporter {
    onUnRecoverableConfigFileDiagnostic: DiagnosticReporter;
}
```

### ParseConfigFileHost
```ts
export interface ParseConfigFileHost extends ParseConfigHost, ConfigFileDiagnosticsReporter {
    getCurrentDirectory(): string;
}
```
 
### JsonConversionNotifier
```ts
interface JsonConversionNotifier {
    onSetValidOptionKeyValueInParent(parentOption: string, option: CommandLineOption, value: CompilerOptionsValue): void;

    onSetValidOptionKeyValueInRoot(key: string, keyNode: PropertyName, value: CompilerOptionsValue, valueNode: Expression): void;

    onSetUnknownOptionKeyValueInRoot(key: string, keyNode: PropertyName, value: CompilerOptionsValue, valueNode: Expression): void;
}
```

### TSConfig
```ts
export interface TSConfig {
    compilerOptions: CompilerOptions;
    compileOnSave: boolean | undefined;
    exclude?: readonly string[];
    files: readonly string[] | undefined;
    include?: readonly string[];
    references: readonly ProjectReference[] | undefined;
}
```

### ParsedTsconfig
```ts
export interface ParsedTsconfig {
    raw: any;
    options?: CompilerOptions;
    typeAcquisition?: TypeAcquisition;
    extendedConfigPath?: string;
}
```

### ExtendedConfigCacheEntry
```ts
export interface ExtendedConfigCacheEntry {
    extendedResult: TsConfigSourceFile;
    extendedConfig: ParsedTsconfig | undefined;
}
```

## 实例对象
### compileOnSaveCommandLineOption
```ts
export const compileOnSaveCommandLineOption: CommandLineOption = { name: "compileOnSave", type: "boolean" };
```

### libEntries
```ts
const libEntries: [string, string][] = [
    ["es5", "lib.es5.d.ts"],
    ["es6", "lib.es2015.d.ts"],
    ["es2015", "lib.es2015.d.ts"],
    ["es7", "lib.es2016.d.ts"],
    ["es2016", "lib.es2016.d.ts"],
    ["es2017", "lib.es2017.d.ts"],
    ["es2018", "lib.es2018.d.ts"],
    ["es2019", "lib.es2019.d.ts"],
    ["es2020", "lib.es2020.d.ts"],
    ["esnext", "lib.esnext.d.ts"],

    ["dom", "lib.dom.d.ts"],
    ["dom.iterable", "lib.dom.iterable.d.ts"],
    ["webworker", "lib.webworker.d.ts"],
    ["webworker.importscripts", ["lib.webworker.importscripts.d.ts"],
    ["scripthost", "lib.scripthost.d.ts"],


    ["es2015.core", "lib.es2015.core.d.ts"],
    ["es2015.collection", "lib.es2015.collection.d.ts"],
    ["es2015.generator", "lib.es2015.generator.d.ts"],
    ["es2015.iterable", "lib.es2015.iterable.d.ts"],
    ["es2015.promise", "lib.es2015.promise.d.ts"],
    ["es2015.proxy", "lib.es2015.proxy.d.ts"],
    ["es2015.reflect", "lib.es2015.reflect.d.ts"],
    ["es2015.symbol", "lib.es2015.symbol.d.ts"],
    ["es2015.symbol.wellknown", "lib.es2015.symbol.wellknown.d.ts"],
    ["es2016.array.include", "lib.es2016.array.include.d.ts"],
    ["es2017.object", "lib.es2017.object.d.ts"],
    ["es2017.sharedmemory", "lib.es2017.sharedmemory.d.ts"],
    ["es2017.string", "lib.es2017.string.d.ts"],
    ["es2017.intl", "lib.es2017.intl.d.ts"],
    ["es2017.typedarrays", "lib.es2017.typedarrays.d.ts"],
    ["es2018.asyncgenerator", "lib.es2018.asyncgenerator.d.ts"],
    ["es2018.asynciterable", "lib.es2018.asynciterable.d.ts"],
    ["es2018.intl", "lib.es2018.intl.d.ts"],
    ["es2018.promise", "lib.es2018.promise.d.ts"],
    ["es2018.regexp", "lib.es2018.regexp.d.ts"],
    ["es2019.array", "lib.es2019.array.d.ts"],
    ["es2019.object", "lib.es2019.object.d.ts"],
    ["es2019.string", "lib.es2019.string.d.ts"],
    ["es2019.symbol", "lib.es2019.symbol.d.ts"],
    ["es2020.string", "lib.es2020.string.d.ts"],
    ["es2020.symbol.wellknown", "lib.es2020.symbol.wellknown.d.ts"],
    ["esnext.array", "lib.es2019.array.d.ts"],
    ["esnext.symbol", "lib.es2019.symbol.d.ts"],
    ["esnext.asynciterable", "lib.es2018.asynciterable.d.ts"],
    ["esnext.intl", "lib.esnext.intl.d.ts"],
    ["esnext.bigint", "lib.esnext.bigint.d.ts"]
]
export const libs = libEntries.map(entry => entry[0]);
export const libMap = createMapFromEntries(libEntries);
export const commonOptionsWithBuild: CommandLineOption[] = [
    {
        name: "help",
        shortName: "h",
        type: "boolean",
        showInSimplifiedHelpView: true,
        category: Diagnostics.Command_line_Options,
        description: Diagnostics.Print_this_message,
    },
    {
        name: "help",
        shortName: "?",
        type: "boolean"
    },
    {
        name: "watch",
        shortName: "w",
        type: "boolean",
        showInSimplifiedHelpView: true,
        category: Diagnostics.Command_line_Options,
        description: Diagnostics.Watch_input_files,
    },
    {
        name: "preserveWatchOutput",
        type: "boolean",
        showInSimplifiedHelpView: false,
        category: Diagnostics.Command_line_Options,
        description: Diagnostics.Whether_to_keep_outdated_console_output_in_watch_mode_instead_of_clearing_the_screen,
    },
    {
        name: "listFiles",
        type: "boolean",
        category: Diagnostics.Advanced_Options,
        description: Diagnostics.Print_names_of_files_part_of_the_compilation
    },
    {
        name: "listEmittedFiles",
        type: "boolean",
        category: Diagnostics.Advanced_Options,
        description: Diagnostics.Print_names_of_generated_files_part_of_the_compilation
    },
    {
        name: "pretty",
        type: "boolean",
        showInSimplifiedHelpView: true,
        category: Diagnostics.Command_line_Options,
        description: Diagnostics.Stylize_errors_and_messages_using_color_and_context_experimental
    },

    {
        name: "traceResolution",
        type: "boolean",
        category: Diagnostics.Advanced_Options,
        description: Diagnostics.Enable_tracing_of_the_name_resolution_process
    },
    {
        name: "diagnostics",
        type: "boolean",
        category: Diagnostics.Advanced_Options,
        description: Diagnostics.Show_diagnostic_information
    },
    {
        name: "extendedDiagnostics",
        type: "boolean",
        category: Diagnostics.Advanced_Options,
        description: Diagnostics.Show_verbose_diagnostic_information
    },
    {
        name: "generateCpuProfile",
        type: "string",
        isFilePath: true,
        paramType: Diagnostics.FILE_OR_DIRECTORY,
        category: Diagnostics.Advanced_Options,
        description: Diagnostics.Generates_a_CPU_profile
    },
    {
        name: "incremental",
        shortName: "i",
        type: "boolean",
        category: Diagnostics.Basic_Options,
        description: Diagnostics.Enable_incremental_compilation,
        transpileOptionValue: undefined
    },
    {
        name: "locale",
        type: "string",
        category: Diagnostics.Advanced_Options,
        description: Diagnostics.The_locale_used_when_displaying_messages_to_the_user_e_g_en_us
    },
]
```

### optionDeclarations
```ts
export const optionDeclarations: CommandLineOption[] = [

]
```

### semanticDiagnosticsOptionDeclarations
```ts
export const semanticDiagnosticsOptionDeclarations: readonly CommandLineOption[] =
        optionDeclarations.filter(option => !!option.affectsSemanticDiagnostics);
```

### affectsEmitOptionDeclarations
```ts
export const affectsEmitOptionDeclarations: readonly CommandLineOption[] =
        optionDeclarations.filter(option => !!option.affectsEmit);
```

### moduleResolutionOptionDeclarations
```ts
export const moduleResolutionOptionDeclarations: readonly CommandLineOption[] =
        optionDeclarations.filter(option => !!option.affectsModuleResolution);
```

### sourceFileAffectingCompilerOptions
```ts
export const sourceFileAffectingCompilerOptions: readonly CommandLineOption[] = optionDeclarations.filter(option =>
        !!option.affectsSourceFile || !!option.affectsModuleResolution || !!option.affectsBindDiagnostics);
```

### transpileOptionValueCompilerOptions
```ts
export const transpileOptionValueCompilerOptions: readonly CommandLineOption[] = optionDeclarations.filter(option =>
        hasProperty(option, "transpileOptionValue"));
```

### buildOpts
```ts
export const buildOpts: CommandLineOption[] = []
```

### typeAcquisitionDeclarations
```ts
export const typeAcquisitionDeclarations: CommandLineOption[] = []
```

### defaultInitCompilerOptions
```ts
export const defaultInitCompilerOptions: CompilerOptions = {
    module: ModuleKind.CommonJS,
    target: ScriptTarget.ES5,
    strict: true,
    esModuleInterop: true,
    forceConsistentCasingInFileNames: true
};
```

### optionNameMapCache
```ts
let optionNameMapCache: OptionNameMap;
```

### _tsconfigRootOptions
```ts
let _tsconfigRootOptions: TsConfigOnlyOption;
```

### reg
```ts
const invalidTrailingRecursionPattern = /(^|\/)\*\*\/?$/;
const invalidDotDotAfterRecursiveWildcardPattern = /(^|\/)\*\*\/(.*\/)?\.\.($|\/)/;
const watchRecursivePattern = /\/[^/]*?[*?][^/]*\//;
const wildcardDirectoryPattern = /^[^*?]*(?=\/[^/]*[*?])/;
```

## 功能函数

### convertEnableAutoDiscoveryToEnable
```ts
export function convertEnableAutoDiscoveryToEnable(typeAcquisition: TypeAcquisition): TypeAcquisition {
    ...
}
```

### getOptionNameMap
```ts
export function getOptionNameMap(): OptionNameMap {
    return optionNameMapCache || (optionNameMapCache = createOptionNameMap(optionDeclarations));
}
```

### createOptionNameMap
```ts
export function createOptionNameMap(optionDeclarations: readonly CommandLineOption[]): OptionNameMap {
    const optionNameMap = createMap<CommandLineOption>();
    const shortOptionNames = createMap<string>();
    forEach(optionDeclarations, option => {
        optionNameMap.set(option.name.toLowerCase(), option);
        if (option.shortName) {
            shortOptionNames.set(option.shortName, option.name);
        }
    });

    return { optionNameMap, shortOptionNames };
}
```

### createCompilerDiagnosticForInvalidCustomType
```ts
export function createCompilerDiagnosticForInvalidCustomType(opt: CommandLineOptionOfCustomType): Diagnostic {
    return createDiagnosticForInvalidCustomType(opt, createCompilerDiagnostic);
}
```

### parseCustomTypeOption
```ts
export function parseCustomTypeOption(
    opt: CommandLineOptionOfCustomType, 
    value: string, 
    errors: Push<Diagnostic>) {

    return convertJsonOptionOfCustomType(opt, trimString(value || ""), errors);
}
```

### parseListTypeOption
```ts
export function parseListTypeOption(
    opt: CommandLineOptionOfListType, 
    value = "", errors: Push<Diagnostic>): (string | number)[] | undefined {
        ...
    }
```

### parseCommandLine
```ts
export function parseCommandLine(commandLine: readonly string[], readFile?: (path: string) => string | undefined): ParsedCommandLine {
    return parseCommandLineWorker(getOptionNameMap, [
        Diagnostics.Unknown_compiler_option_0,
        Diagnostics.Compiler_option_0_expects_an_argument
    ], commandLine, readFile);
}
```

### getOptionFromName
```ts
export function getOptionFromName(optionName: string, allowShort?: boolean): CommandLineOption | undefined {
    return getOptionDeclarationFromName(getOptionNameMap, optionName, allowShort);
}
```

### parseBuildCommand
```ts
export function parseBuildCommand(args: string[]): ParsedBuildCommand {
}
```

### printVersion
```ts
export function printVersion() {
    sys.write(getDiagnosticText(Diagnostics.Version_0, version) + sys.newLine);
}
```

### printHelp
```ts
export function printHelp(optionsList: readonly CommandLineOption[], syntaxPrefix = "") {

}
```

### getParsedCommandLineOfConfigFile
```ts
export function getParsedCommandLineOfConfigFile(
    configFileName: string,
    optionsToExtend: CompilerOptions,
    host: ParseConfigFileHost,
    extendedConfigCache?: Map<ExtendedConfigCacheEntry>
): ParsedCommandLine | undefined {
    ...
}
```

### readConfigFile
```ts
export function readConfigFile(fileName: string, readFile: (path: string) => string | undefined): { config?: any; error?: Diagnostic } {
    const textOrDiagnostic = tryReadFile(fileName, readFile);
    return isString(textOrDiagnostic) ? parseConfigFileTextToJson(fileName, textOrDiagnostic) : { config: {}, error: textOrDiagnostic };
}
```

### parseConfigFileTextToJson
```ts
export function parseConfigFileTextToJson(fileName: string, jsonText: string): { config?: any; error?: Diagnostic } {
    const jsonSourceFile = parseJsonText(fileName, jsonText);
    return {
        config: convertToObject(jsonSourceFile, jsonSourceFile.parseDiagnostics),
        error: jsonSourceFile.parseDiagnostics.length ? jsonSourceFile.parseDiagnostics[0] : undefined
    };
}
```

### readJsonConfigFile
```ts
export function readJsonConfigFile(fileName: string, readFile: (path: string) => string | undefined): TsConfigSourceFile {
    const textOrDiagnostic = tryReadFile(fileName, readFile);
    return isString(textOrDiagnostic) ? parseJsonText(fileName, textOrDiagnostic) : <TsConfigSourceFile>{ parseDiagnostics: [textOrDiagnostic] };
}
```

### convertToObject
```ts
export function convertToObject(sourceFile: JsonSourceFile, errors: Push<Diagnostic>): any {
    return convertToObjectWorker(sourceFile, errors, /*returnValue*/ true, /*knownRootOptions*/ undefined, /*jsonConversionNotifier*/ undefined);
}
```

### convertToObjectWorker
```ts
export function convertToObjectWorker(
    sourceFile: JsonSourceFile,
    errors: Push<Diagnostic>,
    returnValue: boolean,
    knownRootOptions: CommandLineOption | undefined,
    jsonConversionNotifier: JsonConversionNotifier | undefined): any {
    
} 
```


### convertToTSConfig
```ts
export function convertToTSConfig(
    configParseResult: ParsedCommandLine, 
    configFileName: string, 
    host: { 
        getCurrentDirectory(): string, useCaseSensitiveFileNames: boolean 
    }): TSConfig {
    ...
}    
```

### generateTSConfig
```ts
export function generateTSConfig(
    options: CompilerOptions, fileNames: readonly string[], newLine: string): string {
        ...
    }
```

### parseJsonConfigFileContent
```ts
export function parseJsonConfigFileContent(json: any, host: ParseConfigHost, basePath: string, existingOptions?: CompilerOptions, configFileName?: string, resolutionStack?: Path[], extraFileExtensions?: readonly FileExtensionInfo[], extendedConfigCache?: Map<ExtendedConfigCacheEntry>): ParsedCommandLine {
    return parseJsonConfigFileContentWorker(json, /*sourceFile*/ undefined, host, basePath, existingOptions, configFileName, resolutionStack, extraFileExtensions, extendedConfigCache);
}
```

### parseJsonSourceFileConfigFileContent
```ts
export function parseJsonSourceFileConfigFileContent(sourceFile: TsConfigSourceFile, host: ParseConfigHost, basePath: string, existingOptions?: CompilerOptions, configFileName?: string, resolutionStack?: Path[], extraFileExtensions?: readonly FileExtensionInfo[], extendedConfigCache?: Map<ExtendedConfigCacheEntry>): ParsedCommandLine {
    return parseJsonConfigFileContentWorker(/*json*/ undefined, sourceFile, host, basePath, existingOptions, configFileName, resolutionStack, extraFileExtensions, extendedConfigCache);
}
```

### setConfigFileInOptions
```ts
export function setConfigFileInOptions(options: CompilerOptions, configFile: TsConfigSourceFile | undefined) {
    if (configFile) {
        Object.defineProperty(options, "configFile", { enumerable: false, writable: false, value: configFile });
    }
}
```

### canJsonReportNoInutFiles
```ts
export function canJsonReportNoInutFiles(raw: any) {
    return !hasProperty(raw, "files") && !hasProperty(raw, "references");
}
```

### updateErrorForNoInputFiles
```ts
export function updateErrorForNoInputFiles(result: ExpandResult, configFileName: string, configFileSpecs: ConfigFileSpecs, configParseDiagnostics: Diagnostic[], canJsonReportNoInutFiles: boolean) {
    const existingErrors = configParseDiagnostics.length;
    if (shouldReportNoInputFiles(result, canJsonReportNoInutFiles)) {
        configParseDiagnostics.push(getErrorForNoInputFiles(configFileSpecs, configFileName));
    }
    else {
        filterMutate(configParseDiagnostics, error => !isErrorNoInputFiles(error));
    }
    return existingErrors !== configParseDiagnostics.length;
}
```

### convertCompilerOptionsFromJson
```ts
export function convertCompilerOptionsFromJson(jsonOptions: any, basePath: string, configFileName?: string): { options: CompilerOptions, errors: Diagnostic[] } {
    const errors: Diagnostic[] = [];
    const options = convertCompilerOptionsFromJsonWorker(jsonOptions, basePath, errors, configFileName);
    return { options, errors };
}
```

### convertTypeAcquisitionFromJson
```ts
export function convertTypeAcquisitionFromJson(jsonOptions: any, basePath: string, configFileName?: string): { options: TypeAcquisition, errors: Diagnostic[] } {
    const errors: Diagnostic[] = [];
    const options = convertTypeAcquisitionFromJsonWorker(jsonOptions, basePath, errors, configFileName);
    return { options, errors };
}
```

### getFileNamesFromConfigSpecs
```ts
export function getFileNamesFromConfigSpecs(
    spec: ConfigFileSpecs, 
    basePath: string, 
    options: CompilerOptions, 
    host: ParseConfigHost, 
    extraFileExtensions: readonly FileExtensionInfo[] = []): ExpandResult {
    ...
}
```

### convertCompilerOptionsForTelemetry
```ts
 export function convertCompilerOptionsForTelemetry(opts: CompilerOptions): CompilerOptions {
     ...
 }
```

## 内部函数

### createDiagnosticForInvalidCustomType
```ts
function createDiagnosticForInvalidCustomType(
    opt: CommandLineOptionOfCustomType, 
    createDiagnostic: (message: DiagnosticMessage, arg0: string, arg1: string) => Diagnostic):Diagnostic {
    ...
}
```

### parseCommandLineWorker
```ts
function parseCommandLineWorker(
    getOptionNameMap: () => OptionNameMap,
    [unknownOptionDiagnostic, optionTypeMismatchDiagnostic]: ParseCommandLineWorkerDiagnostics,
    commandLine: readonly string[],
    readFile?: (path: string) => string | undefined) {

    const options = {} as OptionsBase;
    const fileNames: string[] = [];
    const errors: Diagnostic[] = [];

    parseStrings(commandLine);
    return {
        options,
        fileNames,
        errors
    };

    function parseStrings(args: readonly string[]) {}
    function parseResponseFile(fileName: string) {}

}
```

### getOptionDeclarationFromName
```ts
function getOptionDeclarationFromName(getOptionNameMap: () => OptionNameMap, optionName: string, allowShort = false): CommandLineOption | undefined {
    ...
}
```

### getDiagnosticText
```ts
function getDiagnosticText(_message: DiagnosticMessage, ..._args: any[]): string {
    const diagnostic = createCompilerDiagnostic.apply(undefined, arguments);
    return <string>diagnostic.messageText;
}
```

### tryReadFile
```ts
function tryReadFile(fileName: string, readFile: (path: string) => string | undefined): string | Diagnostic {
    let text: string | undefined;
    try {
        text = readFile(fileName);
    }
    catch (e) {
        return createCompilerDiagnostic(Diagnostics.Cannot_read_file_0_Colon_1, fileName, e.message);
    }
    return text === undefined ? createCompilerDiagnostic(Diagnostics.The_specified_path_does_not_exist_Colon_0, fileName) : text;
}
```

### commandLineOptionsToMap
```ts
function commandLineOptionsToMap(options: readonly CommandLineOption[]) {
    return arrayToMap(options, option => option.name);
}
```

### getTsconfigRootOptionsMap
```ts
function getTsconfigRootOptionsMap() {
    if (_tsconfigRootOptions === undefined) {
        _tsconfigRootOptions = {
            name: undefined!, // should never be needed since this is root
            type: "object",
            elementOptions: commandLineOptionsToMap([
                {
                    name: "compilerOptions",
                    type: "object",
                    elementOptions: commandLineOptionsToMap(optionDeclarations),
                    extraKeyDiagnosticMessage: Diagnostics.Unknown_compiler_option_0
                },
                {
                    name: "typingOptions",
                    type: "object",
                    elementOptions: commandLineOptionsToMap(typeAcquisitionDeclarations),
                    extraKeyDiagnosticMessage: Diagnostics.Unknown_type_acquisition_option_0
                },
                {
                    name: "typeAcquisition",
                    type: "object",
                    elementOptions: commandLineOptionsToMap(typeAcquisitionDeclarations),
                    extraKeyDiagnosticMessage: Diagnostics.Unknown_type_acquisition_option_0
                },
                {
                    name: "extends",
                    type: "string"
                },
                {
                    name: "references",
                    type: "list",
                    element: {
                        name: "references",
                        type: "object"
                    }
                },
                {
                    name: "files",
                    type: "list",
                    element: {
                        name: "files",
                        type: "string"
                    }
                },
                {
                    name: "include",
                    type: "list",
                    element: {
                        name: "include",
                        type: "string"
                    }
                },
                {
                    name: "exclude",
                    type: "list",
                    element: {
                        name: "exclude",
                        type: "string"
                    }
                },
                compileOnSaveCommandLineOption
            ])
        };
    }
    return _tsconfigRootOptions;
}
```

### getCompilerOptionValueTypeString
```ts
function getCompilerOptionValueTypeString(option: CommandLineOption) {
    return option.type === "list" ?
        "Array" :
        isString(option.type) ? option.type : "string";
}
```

### isCompilerOptionsValue
```ts
function isCompilerOptionsValue(option: CommandLineOption | undefined, value: any): value is CompilerOptionsValue {
    ...
}
```

### filterSameAsDefaultInclude
### matchesSpecs
### getCustomTypeMapOfCommandLineOption
### getNameOfCompilerOptionValue
### serializeCompilerOptions
### isNullOrUndefined
### directoryOfCombinedPath
### parseJsonConfigFileContentWorker
### isErrorNoInputFiles
### getErrorForNoInputFiles
### shouldReportNoInputFiles
### isSuccessfulParsedTsconfig
### parseConfig
### parseOwnConfigOfJson
### parseOwnConfigOfJsonSourceFile
### getExtendsConfigPath
### getExtendedConfig
### convertCompileOnSaveOptionFromJson
### getDefaultCompilerOptions
### convertCompilerOptionsFromJsonWorker
### getDefaultTypeAcquisition
### convertTypeAcquisitionFromJsonWorker
### convertOptionsFromJson
### convertJsonOption
### normalizeOptionValue
### normalizeNonListOptionValue
### convertJsonOptionOfCustomType
### convertJsonOptionOfListType
### trimString
### matchFileNames
### validateSpecs
### specToDiagnostic
### getWildcardDirectories
### getWildcardDirectoryFromSpec
### hasFileWithHigherPriorityExtension
### removeWildcardFilesWithLowerPriorityExtension
### getOptionValueWithEmptyStrings