

##
### SourceFileLike
```ts
export interface SourceFileLike {
    readonly text: string;
    lineMap?: readonly number[];
    /* @internal */
    getPositionOfLineAndCharacter?(line: number, character: number, allowEdits?: true): number;
}
```


### RedirectInfo
```ts
export interface RedirectInfo {
    /** Source file this redirects to. */
    readonly redirectTarget: SourceFile;
    /**
        * Source file for the duplicate package. This will not be used by the Program,
        * but we need to keep this around so we can watch for changes in underlying.
        */
    readonly unredirected: SourceFile;
}
```

### SourceFile
```ts
export interface SourceFile extends Declaration {
    kind: SyntaxKind.SourceFile;
    statements: NodeArray<Statement>;
    endOfFileToken: Token<SyntaxKind.EndOfFileToken>;

    fileName: string;
    /* @internal */ path: Path;
    text: string;
    /** Resolved path can be different from path property,
        * when file is included through project reference is mapped to its output instead of source
        * in that case resolvedPath = path to output file
        * path = input file's path
        */
    /* @internal */ resolvedPath: Path;
    /** Original file name that can be different from fileName,
        * when file is included through project reference is mapped to its output instead of source
        * in that case originalFileName = name of input file
        * fileName = output file's name
        */
    /* @internal */ originalFileName: string;

    /**
        * If two source files are for the same version of the same package, one will redirect to the other.
        * (See `createRedirectSourceFile` in program.ts.)
        * The redirect will have this set. The redirected-to source file will be in `redirectTargetsMap`.
        */
    /* @internal */ redirectInfo?: RedirectInfo;

    amdDependencies: readonly AmdDependency[];
    moduleName?: string;
    referencedFiles: readonly FileReference[];
    typeReferenceDirectives: readonly FileReference[];
    libReferenceDirectives: readonly FileReference[];
    languageVariant: LanguageVariant;
    isDeclarationFile: boolean;

    // this map is used by transpiler to supply alternative names for dependencies (i.e. in case of bundling)
    /* @internal */
    renamedDependencies?: ReadonlyMap<string>;

    /**
        * lib.d.ts should have a reference comment like
        *
        *  /// <reference no-default-lib="true"/>
        *
        * If any other file has this comment, it signals not to include lib.d.ts
        * because this containing file is intended to act as a default library.
        */
    hasNoDefaultLib: boolean;

    languageVersion: ScriptTarget;
    /* @internal */ scriptKind: ScriptKind;

    /**
        * The first "most obvious" node that makes a file an external module.
        * This is intended to be the first top-level import/export,
        * but could be arbitrarily nested (e.g. `import.meta`).
        */
    /* @internal */ externalModuleIndicator?: Node;
    // The first node that causes this file to be a CommonJS module
    /* @internal */ commonJsModuleIndicator?: Node;
    // JS identifier-declarations that are intended to merge with globals
    /* @internal */ jsGlobalAugmentations?: SymbolTable;

    /* @internal */ identifiers: Map<string>; // Map from a string to an interned string
    /* @internal */ nodeCount: number;
    /* @internal */ identifierCount: number;
    /* @internal */ symbolCount: number;

    // File-level diagnostics reported by the parser (includes diagnostics about /// references
    // as well as code diagnostics).
    /* @internal */ parseDiagnostics: DiagnosticWithLocation[];

    // File-level diagnostics reported by the binder.
    /* @internal */ bindDiagnostics: DiagnosticWithLocation[];
    /* @internal */ bindSuggestionDiagnostics?: DiagnosticWithLocation[];

    // File-level JSDoc diagnostics reported by the JSDoc parser
    /* @internal */ jsDocDiagnostics?: DiagnosticWithLocation[];

    // Stores additional file-level diagnostics reported by the program
    /* @internal */ additionalSyntacticDiagnostics?: readonly DiagnosticWithLocation[];

    // Stores a line map for the file.
    // This field should never be used directly to obtain line map, use getLineMap function instead.
    /* @internal */ lineMap: readonly number[];
    /* @internal */ classifiableNames?: ReadonlyUnderscoreEscapedMap<true>;
    // Stores a mapping 'external module reference text' -> 'resolved file name' | undefined
    // It is used to resolve module names in the checker.
    // Content of this field should never be used directly - use getResolvedModuleFileName/setResolvedModuleFileName functions instead
    /* @internal */ resolvedModules?: Map<ResolvedModuleFull | undefined>;
    /* @internal */ resolvedTypeReferenceDirectiveNames: Map<ResolvedTypeReferenceDirective | undefined>;
    /* @internal */ imports: readonly StringLiteralLike[];
    // Identifier only if `declare global`
    /* @internal */ moduleAugmentations: readonly (StringLiteral | Identifier)[];
    /* @internal */ patternAmbientModules?: PatternAmbientModule[];
    /* @internal */ ambientModuleNames: readonly string[];
    /* @internal */ checkJsDirective?: CheckJsDirective;
    /* @internal */ version: string;
    /* @internal */ pragmas: ReadonlyPragmaMap;
    /* @internal */ localJsxNamespace?: __String;
    /* @internal */ localJsxFactory?: EntityName;

    /*@internal*/ exportedModulesFromDeclarationEmit?: ExportedModulesFromDeclarationEmit;
}
```

### ExportedModulesFromDeclarationEmit
```ts
export type ExportedModulesFromDeclarationEmit = readonly Symbol[];
```

### Bundle
```ts
export interface Bundle extends Node {
    kind: SyntaxKind.Bundle;
    prepends: readonly (InputFiles | UnparsedSource)[];
    sourceFiles: readonly SourceFile[];
    /* @internal */ syntheticFileReferences?: readonly FileReference[];
    /* @internal */ syntheticTypeReferences?: readonly FileReference[];
    /* @internal */ syntheticLibReferences?: readonly FileReference[];
    /* @internal */ hasNoDefaultLib?: boolean;
}
```

### InputFiles
```ts
export interface InputFiles extends Node {
    kind: SyntaxKind.InputFiles;
    javascriptPath?: string;
    javascriptText: string;
    javascriptMapPath?: string;
    javascriptMapText?: string;
    declarationPath?: string;
    declarationText: string;
    declarationMapPath?: string;
    declarationMapText?: string;
    /*@internal*/ buildInfoPath?: string;
    /*@internal*/ buildInfo?: BuildInfo;
    /*@internal*/ oldFileOfCurrentEmit?: boolean;
}
```

### UnparsedSource
```ts
export interface UnparsedSource extends Node {
    kind: SyntaxKind.UnparsedSource;
    fileName: string;
    text: string;
    prologues: readonly UnparsedPrologue[];
    helpers: readonly UnscopedEmitHelper[] | undefined;

    // References and noDefaultLibAre Dts only
    referencedFiles: readonly FileReference[];
    typeReferenceDirectives: readonly string[] | undefined;
    libReferenceDirectives: readonly FileReference[];
    hasNoDefaultLib?: boolean;

    sourceMapPath?: string;
    sourceMapText?: string;
    syntheticReferences?: readonly UnparsedSyntheticReference[];
    texts: readonly UnparsedSourceText[];
    /*@internal*/ oldFileOfCurrentEmit?: boolean;
    /*@internal*/ parsedSourceMap?: RawSourceMap | false | undefined;
    // Adding this to satisfy services, fix later
    /*@internal*/
    getLineAndCharacterOfPosition(pos: number): LineAndCharacter;
}
```

### UnparsedSourceText
```ts
export type UnparsedSourceText 
    = UnparsedPrepend 
    | UnparsedTextLike;
```

### UnparsedNode
```ts
export type UnparsedNode = UnparsedPrologue | UnparsedSourceText | UnparsedSyntheticReference;
```

### UnparsedSection
```ts
export interface UnparsedSection extends Node {
    kind: SyntaxKind;
    data?: string;
    parent: UnparsedSource;
}
```

### UnparsedPrologue
```ts
export interface UnparsedPrologue extends UnparsedSection {
    kind: SyntaxKind.UnparsedPrologue;
    data: string;
    parent: UnparsedSource;
}
```

### UnparsedPrepend
```ts
export interface UnparsedPrepend extends UnparsedSection {
    kind: SyntaxKind.UnparsedPrepend;
    data: string;
    parent: UnparsedSource;
    texts: readonly UnparsedTextLike[];
}
```

### UnparsedTextLike
```ts
 export interface UnparsedTextLike extends UnparsedSection {
    kind: SyntaxKind.UnparsedText | SyntaxKind.UnparsedInternalText;
    parent: UnparsedSource;
}
```

### UnparsedSyntheticReference
```ts
export interface UnparsedSyntheticReference extends UnparsedSection {
    kind: SyntaxKind.UnparsedSyntheticReference;
    parent: UnparsedSource;
    /*@internal*/ section: BundleFileHasNoDefaultLib | BundleFileReference;
}
```

### JsonSourceFile
```ts
export interface JsonSourceFile extends SourceFile {
    statements: NodeArray<JsonObjectExpressionStatement>;
}
```

### TsConfigSourceFile
```ts
export interface TsConfigSourceFile extends JsonSourceFile {
    extendedSourceFiles?: string[];
}
```

### JsonMinusNumericLiteral
```ts
export interface JsonMinusNumericLiteral extends PrefixUnaryExpression {
    kind: SyntaxKind.PrefixUnaryExpression;
    operator: SyntaxKind.MinusToken;
    operand: NumericLiteral;
}
```

### JsonObjectExpressionStatement
```ts
export interface JsonObjectExpressionStatement extends ExpressionStatement {
    expression: (ObjectLiteralExpression 
        | ArrayLiteralExpression 
        | JsonMinusNumericLiteral 
        | NumericLiteral 
        | StringLiteral 
        | BooleanLiteral 
        | NullLiteral);
}
```

### JsFileExtensionInfo
```ts
export type JsFileExtensionInfo = FileExtensionInfo;
```

### FileExtensionInfo
```ts
export interface FileExtensionInfo {
    extension: string;
    isMixedContent: boolean;
    scriptKind?: ScriptKind;
}
```

### Extension
```ts
export const enum Extension {
    Ts = ".ts",
    Tsx = ".tsx",
    Dts = ".d.ts",
    Js = ".js",
    Jsx = ".jsx",
    Json = ".json",
    TsBuildInfo = ".tsbuildinfo"
}
```