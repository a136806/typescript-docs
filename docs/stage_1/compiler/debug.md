## 功能说明
## 类型接口
### ExtendedDebugModule
```ts
interface ExtendedDebugModule {
    init(_ts: typeof ts): void;
    formatControlFlowGraph(flowNode: FlowNode): string;
}
```
## 实例对象
```ts
export let currentAssertionLevel = AssertionLevel.None;
export let isDebugging = false;
let isDebugInfoEnabled = false;
let extendedDebugModule: ExtendedDebugModule | undefined;
```

### assertEachNode
### assertNode
### assertNotNode
### assertOptionalNode
### assertOptionalToken
### assertMissingNode

## 功能函数

### shouldAssert
```ts
export function shouldAssert(level: AssertionLevel): boolean {
    return currentAssertionLevel >= level;
}
```
### fail
```ts
export function fail(message?: string, stackCrawlMark?: AnyFunction): never {
    debugger;
    const e = new Error(message ? `Debug Failure. ${message}` : "Debug Failure.");

    if ((<any>Error).captureStackTrace) {
        (<any>Error).captureStackTrace(e, stackCrawlMark || fail);
    }

    throw e;
}
```
### assert
```ts
export function assert(
    expression: boolean, 
    message?: string, 
    verboseDebugInfo?: string | (() => string), 
    stackCrawlMark?: AnyFunction): void {
    if (!expression) {
        if (verboseDebugInfo) {
            message += "\r\nVerbose Debug Information: " + 
                (typeof verboseDebugInfo === "string" 
                    ? verboseDebugInfo 
                    : verboseDebugInfo()
                );
        }
        fail(message 
            ? "False expression: " + message 
            : "False expression.", stackCrawlMark || assert
        );
    }
}
```

### assertEqual
```ts
export function assertEqual<T>(a: T, b: T, msg?: string, msg2?: string): void {
    if (a !== b) {
        const message = msg 
                            ? msg2 
                                ? `${msg} ${msg2}`
                                : msg 
                            : "";
        fail(`Expected ${a} === ${b}. ${message}`);
    }
}
```

### assertLessThan
```ts
export function assertLessThan(a: number, b: number, msg?: string): void {
    if (a >= b) {
        fail(`Expected ${a} < ${b}. ${msg || ""}`);
    }
}
```

### assertLessThanOrEqual
```ts
export function assertLessThanOrEqual(a: number, b: number): void {
    if (a > b) {
        fail(`Expected ${a} <= ${b}`);
    }
}
```
### assertGreaterThanOrEqual
```ts
export function assertGreaterThanOrEqual(a: number, b: number): void {
    if (a < b) {
        fail(`Expected ${a} >= ${b}`);
    }
}
```

### assertDefined
### assertEachDefined
### assertNever
### getFunctionName
### formatSymbol
```ts
export function formatSymbol(symbol: Symbol): string {
    return `{ 
        name: ${unescapeLeadingUnderscores(symbol.escapedName)}; 
        flags: ${formatSymbolFlags(symbol.flags)}; 
        declarations: ${map(symbol.declarations, node => formatSyntaxKind(node.kind))} 
    }`;
}

```

### formatEnum
### getEnumMembers
### formatSyntaxKind
### formatNodeFlags
### formatModifierFlags
### formatTransformFlags
### formatEmitFlags
### formatSymbolFlags
### formatTypeFlags
### formatObjectFlags
### failBadSyntaxKind

### printControlFlowGraph
### formatControlFlowGraph
### attachFlowNodeDebugInfo
### enableDebugInfo


## 内部函数
#### extendedDebug