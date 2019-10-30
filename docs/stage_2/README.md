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

## compiler文件解析部分
- 1 `createProgram`

## compiler文件生成部分
- 1 `emitFilesAndReportErrorsAndGetExitStatus`