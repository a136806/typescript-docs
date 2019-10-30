# tsc 流程

## `tsc.ts`
- 1 `executeCommandLine`              命令行入口     (tsc.ts)
    - `parseCommandLine`              命令行解析(compiler/comandLinerParser.ts)
- 2 `executeCommandLineWorker`        命令行操作处理    (tsc.ts)
    - `tsc --xx`处理
- 3.2 `performIncrementalCompilation`                增量编译入口   (tsc.ts)
    - `ts.performIncrementalCompilation`             增量编译(compiler/watch.ts) 
        - `createIncrementalProgram`                 文件解析  (compiler/watch.ts)
        - `emitFilesAndReportErrorsAndGetExitStatus` 文件生成  (compiler/watch.ts)
- 3.3 `performCompilation`                           普通编译入口 (tsc.ts)
    - `createProgram`                                文件解析  (compiler/program.ts)
    - `emitFilesAndReportErrorsAndGetExitStatus`     文件生成  (compiler/watch.ts)

##  program初始化
- 1 `createProgram`                                      (compiler/program.ts) 

##  emit代码生成
- 1   watch
    - `emitFilesAndReportErrorsAndGetExitStatus`         (compiler/watch.ts)
    - `emitFilesAndReportErrors`                         (compiler/watch.ts)   
- 2   program
    - `program.emit()`                                   (compiler/program.ts) 
    - `emitWorker()`                                     (compiler/program.ts) 
- 3   emitter
    - `emitFiles()`                                      (compiler/emitter.ts)
    - `getTransformers`                                  (compiler/transformer.ts)   
    - `forEachEmittedFile`                               (compiler/emitter.ts)           
    - `emitSourceFileOrBundle`                           (compiler/emitter.ts)   
        - `emitJsFileOrBundle`                           (compiler/emitter.ts)
            - `transformNodes`                           (compiler/transformer.ts)(4)
            - `createPrinter`                            (compiler/emitter.ts)   
        - `emitDeclarationFileOrBundle`                  (compiler/emitter.ts)
            - `transformNodes`                           (compiler/transformer.ts)
            - `createPrinter`                            (compiler/emitter.ts)(4)
        - `emitBuildInfo`                                (compiler/emitter.ts)
            - `getProgramBuildInfo`                      (compiler/program.ts)   
            - `writeFile`                                (compiler/program.ts)
- 4  transformer  
    - `getTransformers`                                  (compiler/transformer.ts)  
        - `getScriptTransformers`                        (compiler/transformer.ts)
        - `getDeclarationTransformers`                   (compiler/transformer.ts)
    - `transformNodes`                                   (compiler/transformer.ts)
    - `transformRoot`                                    (compiler/transformer.ts)
    - `transformation`                                   (compiler/transformer.ts)

- 5 getScriptTransformers
    - `customTransformers.before`
    - `transformTypeScript`
    - `transformClassFields`
    - `transformJsx`
    - `transformESNext`
    - `transformES2019`
    - `transformES2018`
    - `transformES2017`
    - `transformES2016`
    - `transformES2015`
    - `transformGenerators`
    - `transformES5`
    - `customTransformers.after`
    getDeclarationTransformers
    -  `transformDeclarations`

- 6  transformers\es2015.ts
    - `transformES2015`
    - `chainBundle(transformSourceFile)`
    - `transformSourceFileOrBundle`
    - `transformSourceFile`
    - `visitSourceFile`
- 7  visitSourceFile       
    - `visitNodes`
    - `updateSourceFileNode`    
