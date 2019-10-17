

## 源代码
### DiagnosticMessage
```ts
export interface DiagnosticMessage {
    key: string;
    category: DiagnosticCategory;
    code: number;
    message: string;
    reportsUnnecessary?: {};
    /* @internal */
    elidedInCompatabilityPyramid?: boolean;
}
```

### DiagnosticMessageChain
```ts
export interface DiagnosticMessageChain {
    messageText: string;
    category: DiagnosticCategory;
    code: number;
    next?: DiagnosticMessageChain[];
}
```

### Diagnostic
```ts
export interface Diagnostic extends DiagnosticRelatedInformation {
    /** May store more in future. For now, this will simply be `true` to indicate when a diagnostic is an unused-identifier diagnostic. */
    reportsUnnecessary?: {};
    source?: string;
    relatedInformation?: DiagnosticRelatedInformation[];
}
```

### DiagnosticRelatedInformation
```ts
export interface DiagnosticRelatedInformation {
    category: DiagnosticCategory;
    code: number;
    file: SourceFile | undefined;
    start: number | undefined;
    length: number | undefined;
    messageText: string | DiagnosticMessageChain;
}
```

### DiagnosticWithLocation
```ts
export interface DiagnosticWithLocation extends Diagnostic {
    file: SourceFile;
    start: number;
    length: number;
}
```

### DiagnosticCategory
```ts
export enum DiagnosticCategory {
    Warning,
    Error,
    Suggestion,
    Message
}
```

### diagnosticCategoryName
```ts
export function diagnosticCategoryName(d: { category: DiagnosticCategory }, lowerCase = true): string {
    const name = DiagnosticCategory[d.category];
    return lowerCase ? name.toLowerCase() : name;
}
```

### DiagnosticCollection
```ts
export interface DiagnosticCollection {
    
    add(diagnostic: Diagnostic): void;

    lookup(diagnostic: Diagnostic): Diagnostic | undefined;

    
    getGlobalDiagnostics(): Diagnostic[];

    getDiagnostics(): Diagnostic[];
    getDiagnostics(fileName: string): DiagnosticWithLocation[];

    reattachFileDiagnostics(newFile: SourceFile): void;
}
```