## 类型接口

### Statistic
```ts
interface Statistic {
    name: string;
    value: string;
}
```
## 实例对象
### Base
```ts
let reportDiagnostic = createDiagnosticReporter(sys);
```
## 功能函数
## 内部函数
### countLines
> 统计代码行数
```ts
function countLines(program: Program): number {
    let count = 0;
    forEach(program.getSourceFiles(), file => {
        count += getLineStarts(file).length;
    });
    return count;
}
```

### updateReportDiagnostic
```ts
function updateReportDiagnostic(options: CompilerOptions | BuildOptions) {
    if (shouldBePretty(options)) {
        reportDiagnostic = createDiagnosticReporter(sys, /*pretty*/ true);
    }
}
```

### defaultIsPretty
```ts
function defaultIsPretty() {
    return !!sys.writeOutputIsTTY && sys.writeOutputIsTTY();
}
```

### shouldBePretty
```ts
function shouldBePretty(options: CompilerOptions | BuildOptions) {
    if (!options || typeof options.pretty === "undefined") {
        return defaultIsPretty();
    }
    return options.pretty;
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

### padRight
```ts
function padRight(s: string, length: number) {
    while (s.length < length) {
        s = s + " ";
    }

    return s;
}
```

### getOptionsForHelp
```ts
function getOptionsForHelp(commandLine: ParsedCommandLine) {
    return !!commandLine.options.all ?
        sort(optionDeclarations, (a, b) => compareStringsCaseInsensitive(a.name, b.name)) :
        filter(optionDeclarations.slice(), v => !!v.showInSimplifiedHelpView);
}
```

### executeCommandLineWorker
```ts
function executeCommandLineWorker(commandLine: ParsedCommandLine) {
    // tsc --build
    if (commandLine.options.build) {
        reportDiagnostic(createCompilerDiagnostic(Diagnostics.Option_build_must_be_the_first_command_line_argument));
        return sys.exit(ExitStatus.DiagnosticsPresent_OutputsSkipped);
    }

    let configFileName: string | undefined;
    // tsc --local
    if (commandLine.options.locale) {
        validateLocaleAndSetLanguage(commandLine.options.locale, sys, commandLine.errors);
    }

    if (commandLine.errors.length > 0) {
        commandLine.errors.forEach(reportDiagnostic);
        return sys.exit(ExitStatus.DiagnosticsPresent_OutputsSkipped);
    }

    // tsc --init 
    if (commandLine.options.init) {
        writeConfigFile(commandLine.options, commandLine.fileNames);
        return sys.exit(ExitStatus.Success);
    }


    // tsc --version
    if (commandLine.options.version) {
        printVersion();
        return sys.exit(ExitStatus.Success);
    }


    // tsc --help | tsc --all
    if (commandLine.options.help || commandLine.options.all) {
        printVersion();
        printHelp(getOptionsForHelp(commandLine));
        return sys.exit(ExitStatus.Success);
    }

    // tsc --project
    if (commandLine.options.project) {
    
    } else if (commandLine.fileNames.length === 0) {
        const searchPath = normalizePath(sys.getCurrentDirectory());
        configFileName = findConfigFile(searchPath, sys.fileExists);
    }

    // tsconfig.json 配置文件获取失败
    if (commandLine.fileNames.length === 0 && !configFileName) {
        printVersion();
        printHelp(getOptionsForHelp(commandLine));
        return sys.exit(ExitStatus.Success);
    }

    const commandLineOptions = commandLine.options;
    if (configFileName) {
        //配置文件解析
        const configParseResult = parseConfigFileWithSystem(configFileName, commandLineOptions, sys, reportDiagnostic)!; 

        // tsc --showConfig
        if (commandLineOptions.showConfig) {
            if (configParseResult.errors.length !== 0) {
                updateReportDiagnostic(configParseResult.options);
                configParseResult.errors.forEach(reportDiagnostic);
                return sys.exit(ExitStatus.DiagnosticsPresent_OutputsSkipped);
            }
            sys.write(JSON.stringify(convertToTSConfig(configParseResult, configFileName, sys), null, 4) + sys.newLine);
            return sys.exit(ExitStatus.Success);
        }

        updateReportDiagnostic(configParseResult.options);
        // tsc --watch
        if (isWatchSet(configParseResult.options)) {
            reportWatchModeWithoutSysSupport();
            createWatchOfConfigFile(configParseResult, commandLineOptions);
        }
        // tsc --incremental
        else if (isIncrementalCompilation(configParseResult.options)) {
            performIncrementalCompilation(configParseResult);
        }
        else {
            performCompilation(configParseResult.fileNames, configParseResult.projectReferences, configParseResult.options, getConfigFileParsingDiagnostics(configParseResult));
        }
    }else{
        if (commandLineOptions.showConfig) {
            sys.write(JSON.stringify(convertToTSConfig(commandLine, combinePaths(sys.getCurrentDirectory(), "tsconfig.json"), sys), null, 4) + sys.newLine);
            return sys.exit(ExitStatus.Success);
        }
        updateReportDiagnostic(commandLineOptions);
        // tsc --watch
        if (isWatchSet(commandLineOptions)) {
            reportWatchModeWithoutSysSupport();
            createWatchOfFilesAndCompilerOptions(commandLine.fileNames, commandLineOptions);
        }
        // tsc --incremental
        else if (isIncrementalCompilation(commandLineOptions)) {
            performIncrementalCompilation(commandLine);
        }
        else {
            performCompilation(commandLine.fileNames, /*references*/ undefined, commandLineOptions);
        }
    }
}
```

### reportWatchModeWithoutSysSupport
```ts
function reportWatchModeWithoutSysSupport() {
    if (!sys.watchFile || !sys.watchDirectory) {
        reportDiagnostic(createCompilerDiagnostic(Diagnostics.The_current_host_does_not_support_the_0_option, "--watch"));
        sys.exit(ExitStatus.DiagnosticsPresent_OutputsSkipped);
    }
}
```

### performBuildWorker
```ts
function performBuildWorker(buildOptions: BuildOptions, projects: string[], errors: Diagnostic[]) {
    ...
}
```

### performBuild
```ts
function performBuild(args: string[]) {
    ...
}
```

### createReportErrorSummary
```ts
function createReportErrorSummary(
    options: CompilerOptions | BuildOptions
): ReportEmitErrorSummary | undefined {

}
```

### performCompilation
```ts
function performCompilation(
    rootNames: string[], 
    projectReferences: readonly ProjectReference[] | undefined, 
    options: CompilerOptions, 
    configFileParsingDiagnostics?: readonly Diagnostic[]
) {

    const host = createCompilerHost(options);
    const currentDirectory = host.getCurrentDirectory();
    const getCanonicalFileName = createGetCanonicalFileName(host.useCaseSensitiveFileNames());

    changeCompilerHostLikeToUseCache(host, fileName => toPath(fileName, currentDirectory, getCanonicalFileName));
    enableStatistics(options);

    // ProgramOptions
    const programOptions: CreateProgramOptions = {
        rootNames,
        options,
        projectReferences,
        host,
        configFileParsingDiagnostics
    };
    // Program入口
    const program = createProgram(programOptions);
    // Emit生成文件
    const exitStatus = emitFilesAndReportErrorsAndGetExitStatus(
        program,
        reportDiagnostic,
        s => sys.write(s + sys.newLine),
        createReportErrorSummary(options)
    );
    // 统计报告
    reportStatistics(program);
    // 退出
    return sys.exit(exitStatus);
}
```

### performIncrementalCompilation
```ts
function performIncrementalCompilation(config: ParsedCommandLine) {
    const { options, fileNames, projectReferences } = config;
    enableStatistics(options);
    // 增量编译
    return sys.exit(ts.performIncrementalCompilation({
        rootNames: fileNames,
        options,
        configFileParsingDiagnostics: getConfigFileParsingDiagnostics(config),
        projectReferences,
        reportDiagnostic,
        reportErrorSummary: createReportErrorSummary(options),
        afterProgramEmitAndDiagnostics: builderProgram => reportStatistics(builderProgram.getProgram())
    }));
}
```

### updateCreateProgram
```ts
function updateCreateProgram<T extends BuilderProgram>(
    host: { createProgram: CreateProgram<T>; }
) {
}
```

### updateWatchCompilationHost
```ts
function updateWatchCompilationHost(
    watchCompilerHost: WatchCompilerHost<EmitAndSemanticDiagnosticsBuilderProgram>
) {
}
```

### createWatchStatusReporter
```ts
function createWatchStatusReporter(options: CompilerOptions | BuildOptions) {
    return ts.createWatchStatusReporter(sys, shouldBePretty(options));
}
```

### createWatchOfConfigFile
```ts
function createWatchOfConfigFile(
    configParseResult: ParsedCommandLine, 
    optionsToExtend: CompilerOptions
) {
    const watchCompilerHost = createWatchCompilerHostOfConfigFile(configParseResult.options.configFilePath!, optionsToExtend, sys, /*createProgram*/ undefined, reportDiagnostic, createWatchStatusReporter(configParseResult.options)); // TODO: GH#18217

    updateWatchCompilationHost(watchCompilerHost);
    watchCompilerHost.configFileParsingResult = configParseResult;
    createWatchProgram(watchCompilerHost);
}
```

### createWatchOfFilesAndCompilerOptions
```ts
function createWatchOfFilesAndCompilerOptions(rootFiles: string[], options: CompilerOptions) {
    const watchCompilerHost = createWatchCompilerHostOfFilesAndCompilerOptions(rootFiles, options, sys, /*createProgram*/ undefined, reportDiagnostic, createWatchStatusReporter(options));
    updateWatchCompilationHost(watchCompilerHost);
    createWatchProgram(watchCompilerHost);
}
```

### enableStatistics
```ts
function enableStatistics(compilerOptions: CompilerOptions) {
    if (compilerOptions.diagnostics || compilerOptions.extendedDiagnostics) {
        performance.enable();
    }
}
```

### reportStatistics
```ts
function reportStatistics(program: Program) {
}
```

### writeConfigFile
```ts
function writeConfigFile(options: CompilerOptions, fileNames: string[]) {
    const currentDirectory = sys.getCurrentDirectory();
    const file = normalizePath(combinePaths(currentDirectory, "tsconfig.json"));
    if (sys.fileExists(file)) {
        reportDiagnostic(createCompilerDiagnostic(Diagnostics.A_tsconfig_json_file_is_already_defined_at_Colon_0, file));
    }
    else {
        sys.writeFile(file, generateTSConfig(options, fileNames, sys.newLine));
        reportDiagnostic(createCompilerDiagnostic(Diagnostics.Successfully_created_a_tsconfig_json_file));
    }

    return;
}
```

### 运行入口
```ts
if (ts.Debug.isDebugging) {
    ts.Debug.enableDebugInfo();
}

if (ts.sys.tryEnableSourceMapsForHost && /^development$/i.test(ts.sys.getEnvironmentVariable("NODE_ENV"))) {
    ts.sys.tryEnableSourceMapsForHost();
}

if (ts.sys.setBlocking) {
    ts.sys.setBlocking();
}

ts.executeCommandLine(ts.sys.args);
```