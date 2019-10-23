## 类型接口
### PerfLogger
```ts
type PerfLogger = typeof import("@microsoft/typescript-etw");
```
## 实例对象 
```ts
const nullLogger: PerfLogger = {
    logEvent: noop,
    logErrEvent: noop,
    logPerfEvent: noop,
    logInfoEvent: noop,
    logStartCommand: noop,
    logStopCommand: noop,
    logStartUpdateProgram: noop,
    logStopUpdateProgram: noop,
    logStartUpdateGraph: noop,
    logStopUpdateGraph: noop,
    logStartResolveModule: noop,
    logStopResolveModule: noop,
    logStartParseSourceFile: noop,
    logStopParseSourceFile: noop,
    logStartReadFile: noop,
    logStopReadFile: noop,
    logStartBindFile: noop,
    logStopBindFile: noop,
    logStartScheduledOperation: noop,
    logStopScheduledOperation: noop,
};

let etwModule;
try {
    // require() will throw an exception if the module is not installed
    // It may also return undefined if not installed properly
    etwModule = require("@microsoft/typescript-etw");
}
catch (e) {
    etwModule = undefined;
}

export const perfLogger: 
    PerfLogger = etwModule && etwModule.logEvent 
        ? etwModule 
        : nullLogger;

const args = typeof process === "undefined" ? [] : process.argv;
perfLogger.logInfoEvent(`Starting TypeScript v${versionMajorMinor} with command line: ${JSON.stringify(args)}`);
```

## 功能函数
## 内部函数