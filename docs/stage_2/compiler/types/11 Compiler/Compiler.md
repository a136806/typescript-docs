## 源代码

### ScriptReferenceHost
```ts
export interface ScriptReferenceHost {
    getCompilerOptions(): CompilerOptions;
    getSourceFile(fileName: string): SourceFile | undefined;
    getSourceFileByPath(path: Path): SourceFile | undefined;
    getCurrentDirectory(): string;
}
```

### ParseConfigHost
```ts
export interface ParseConfigHost {
    useCaseSensitiveFileNames: boolean;

    readDirectory(rootDir: string, extensions: readonly string[], excludes: readonly string[] | undefined, includes: readonly string[], depth?: number): readonly string[];

    /**
        * Gets a value indicating whether the specified path exists and is a file.
        * @param path The path to test.
        */
    fileExists(path: string): boolean;

    readFile(path: string): string | undefined;
    trace?(s: string): void;
}
```

### ResolvedConfigFileName
```ts
export type ResolvedConfigFileName = string & { _isResolvedConfigFileName: never };
```

### WriteFileCallback
```ts
export type WriteFileCallback = (
    fileName: string,
    data: string,
    writeByteOrderMark: boolean,
    onError?: (message: string) => void,
    sourceFiles?: readonly SourceFile[],
) => void;
```

### CancellationToken
```ts
export interface CancellationToken {
    isCancellationRequested(): boolean;

    /** @throws OperationCanceledException if isCancellationRequested is true */
    throwIfCancellationRequested(): void;
}
```

### RefFileKind
```ts
export enum RefFileKind {
    Import,
    ReferenceFile,
    TypeReferenceDirective
}
```

### RefFile
```ts
export interface RefFile {
    kind: RefFileKind;
    index: number;
    file: Path;
}
```

### Program
```ts
export interface Program extends ScriptReferenceHost {
    /**
        * Get a list of root file names that were passed to a 'createProgram'
        */
    getRootFileNames(): readonly string[];

    /**
        * Get a list of files in the program
        */
    getSourceFiles(): readonly SourceFile[];

    /**
        * Get a list of file names that were passed to 'createProgram' or referenced in a
        * program source file but could not be located.
        */
    /* @internal */
    getMissingFilePaths(): readonly Path[];
    /* @internal */
    getRefFileMap(): MultiMap<RefFile> | undefined;

    /**
        * Emits the JavaScript and declaration files.  If targetSourceFile is not specified, then
        * the JavaScript and declaration files will be produced for all the files in this program.
        * If targetSourceFile is specified, then only the JavaScript and declaration for that
        * specific file will be generated.
        *
        * If writeFile is not specified then the writeFile callback from the compiler host will be
        * used for writing the JavaScript and declaration files.  Otherwise, the writeFile parameter
        * will be invoked when writing the JavaScript and declaration files.
        */
    emit(targetSourceFile?: SourceFile, writeFile?: WriteFileCallback, cancellationToken?: CancellationToken, emitOnlyDtsFiles?: boolean, customTransformers?: CustomTransformers): EmitResult;
    /*@internal*/
    emit(targetSourceFile?: SourceFile, writeFile?: WriteFileCallback, cancellationToken?: CancellationToken, emitOnlyDtsFiles?: boolean, customTransformers?: CustomTransformers, forceDtsEmit?: boolean): EmitResult; // eslint-disable-line @typescript-eslint/unified-signatures

    getOptionsDiagnostics(cancellationToken?: CancellationToken): readonly Diagnostic[];
    getGlobalDiagnostics(cancellationToken?: CancellationToken): readonly Diagnostic[];
    getSyntacticDiagnostics(sourceFile?: SourceFile, cancellationToken?: CancellationToken): readonly DiagnosticWithLocation[];
    /** The first time this is called, it will return global diagnostics (no location). */
    getSemanticDiagnostics(sourceFile?: SourceFile, cancellationToken?: CancellationToken): readonly Diagnostic[];
    getDeclarationDiagnostics(sourceFile?: SourceFile, cancellationToken?: CancellationToken): readonly DiagnosticWithLocation[];
    getConfigFileParsingDiagnostics(): readonly Diagnostic[];
    /* @internal */ getSuggestionDiagnostics(sourceFile: SourceFile, cancellationToken?: CancellationToken): readonly DiagnosticWithLocation[];

    /**
        * Gets a type checker that can be used to semantically analyze source files in the program.
        */
    getTypeChecker(): TypeChecker;

    /* @internal */ getCommonSourceDirectory(): string;

    // For testing purposes only.  Should not be used by any other consumers (including the
    // language service).
    /* @internal */ getDiagnosticsProducingTypeChecker(): TypeChecker;
    /* @internal */ dropDiagnosticsProducingTypeChecker(): void;

    /* @internal */ getClassifiableNames(): UnderscoreEscapedMap<true>;

    getNodeCount(): number;
    getIdentifierCount(): number;
    getSymbolCount(): number;
    getTypeCount(): number;
    getRelationCacheSizes(): { assignable: number, identity: number, subtype: number };

    /* @internal */ getFileProcessingDiagnostics(): DiagnosticCollection;
    /* @internal */ getResolvedTypeReferenceDirectives(): Map<ResolvedTypeReferenceDirective | undefined>;
    isSourceFileFromExternalLibrary(file: SourceFile): boolean;
    isSourceFileDefaultLibrary(file: SourceFile): boolean;

    // For testing purposes only.
    /* @internal */ structureIsReused?: StructureIsReused;

    /* @internal */ getSourceFileFromReference(referencingFile: SourceFile | UnparsedSource, ref: FileReference): SourceFile | undefined;
    /* @internal */ getLibFileFromReference(ref: FileReference): SourceFile | undefined;

    /** Given a source file, get the name of the package it was imported from. */
    /* @internal */ sourceFileToPackageName: Map<string>;
    /** Set of all source files that some other source file redirects to. */
    /* @internal */ redirectTargetsMap: MultiMap<string>;
    /** Is the file emitted file */
    /* @internal */ isEmittedFile(file: string): boolean;

    /* @internal */ getResolvedModuleWithFailedLookupLocationsFromCache(moduleName: string, containingFile: string): ResolvedModuleWithFailedLookupLocations | undefined;

    getProjectReferences(): readonly ProjectReference[] | undefined;
    getResolvedProjectReferences(): readonly (ResolvedProjectReference | undefined)[] | undefined;
    /*@internal*/ getProjectReferenceRedirect(fileName: string): string | undefined;
    /*@internal*/ getResolvedProjectReferenceToRedirect(fileName: string): ResolvedProjectReference | undefined;
    /*@internal*/ forEachResolvedProjectReference<T>(cb: (resolvedProjectReference: ResolvedProjectReference | undefined, resolvedProjectReferencePath: Path) => T | undefined): T | undefined;
    /*@internal*/ getResolvedProjectReferenceByPath(projectReferencePath: Path): ResolvedProjectReference | undefined;
    /*@internal*/ isSourceOfProjectReferenceRedirect(fileName: string): boolean;
    /*@internal*/ getProgramBuildInfo?(): ProgramBuildInfo | undefined;
    /*@internal*/ emitBuildInfo(writeFile?: WriteFileCallback, cancellationToken?: CancellationToken): EmitResult;
    /*@internal*/ getProbableSymlinks(): ReadonlyMap<string>;
    }
```

### RedirectTargetsMap
```ts
export type RedirectTargetsMap = ReadonlyMap<readonly string[]>;
```

### ResolvedProjectReference
```ts
export interface ResolvedProjectReference {
    commandLine: ParsedCommandLine;
    sourceFile: SourceFile;
    references?: readonly (ResolvedProjectReference | undefined)[];
}
```

### StructureIsReused
```ts
export const enum StructureIsReused {
    Not         = 0,
    SafeModules = 1 << 0,
    Completely  = 1 << 1,
}
```

### CustomTransformerFactory
```ts
export type CustomTransformerFactory 
    = (context: TransformationContext) => CustomTransformer;
```

### CustomTransformer
```ts
export interface CustomTransformer {
    transformSourceFile(node: SourceFile): SourceFile;
    transformBundle(node: Bundle): Bundle;
}
```

### CustomTransformers
```ts
export interface CustomTransformers {
    /** Custom transformers to evaluate before built-in .js transformations. */
    before?: (TransformerFactory<SourceFile> | CustomTransformerFactory)[];
    /** Custom transformers to evaluate after built-in .js transformations. */
    after?: (TransformerFactory<SourceFile> | CustomTransformerFactory)[];
    /** Custom transformers to evaluate after built-in .d.ts transformations. */
    afterDeclarations?: (TransformerFactory<Bundle | SourceFile> | CustomTransformerFactory)[];
}
```

### EmitTransformers
```ts
export interface EmitTransformers {
    scriptTransformers: readonly TransformerFactory<SourceFile | Bundle>[];
    declarationTransformers: readonly TransformerFactory<SourceFile | Bundle>[];
}
```

### SourceMapSpan
```ts
export interface SourceMapSpan {
    /** Line number in the .js file. */
    emittedLine: number;
    /** Column number in the .js file. */
    emittedColumn: number;
    /** Line number in the .ts file. */
    sourceLine: number;
    /** Column number in the .ts file. */
    sourceColumn: number;
    /** Optional name (index into names array) associated with this span. */
    nameIndex?: number;
    /** .ts file (index into sources array) associated with this span */
    sourceIndex: number;
}
```

### SourceMapEmitResult
```ts
export interface SourceMapEmitResult {
    inputSourceFileNames: readonly string[];      // Input source file (which one can use on program to get the file), 1:1 mapping with the sourceMap.sources list
    sourceMap: RawSourceMap;
}
```

### ExitStatus
```ts
export enum ExitStatus {
    // Compiler ran successfully.  Either this was a simple do-nothing compilation (for example,
    // when -version or -help was provided, or this was a normal compilation, no diagnostics
    // were produced, and all outputs were generated successfully.
    Success = 0,

    // Diagnostics were produced and because of them no code was generated.
    DiagnosticsPresent_OutputsSkipped = 1,

    // Diagnostics were produced and outputs were generated in spite of them.
    DiagnosticsPresent_OutputsGenerated = 2,

    // When build skipped because passed in project is invalid
    InvalidProject_OutputsSkipped = 3,

    // When build is skipped because project references form cycle
    ProjectReferenceCycle_OutputsSkupped = 4,
}
```

### EmitResult
```ts
export interface EmitResult {
    emitSkipped: boolean;
    /** Contains declaration emit diagnostics */
    diagnostics: readonly Diagnostic[];
    emittedFiles?: string[]; // Array of files the compiler wrote to disk
    /* @internal */ sourceMaps?: SourceMapEmitResult[];  // Array of sourceMapData if compiler emitted sourcemaps
    /* @internal */ exportedModulesFromDeclarationEmit?: ExportedModulesFromDeclarationEmit;
}
```

### TypeCheckerHost
```ts
export interface TypeCheckerHost extends ModuleSpecifierResolutionHost {
    getCompilerOptions(): CompilerOptions;

    getSourceFiles(): readonly SourceFile[];
    getSourceFile(fileName: string): SourceFile | undefined;
    getResolvedTypeReferenceDirectives(): ReadonlyMap<ResolvedTypeReferenceDirective | undefined>;
    getProjectReferenceRedirect(fileName: string): string | undefined;
    isSourceOfProjectReferenceRedirect(fileName: string): boolean;

    readonly redirectTargetsMap: RedirectTargetsMap;
}
```

### TypeChecker
```ts
export interface TypeChecker {
    getTypeOfSymbolAtLocation(symbol: Symbol, node: Node): Type;
    getDeclaredTypeOfSymbol(symbol: Symbol): Type;
    getPropertiesOfType(type: Type): Symbol[];
    getPropertyOfType(type: Type, propertyName: string): Symbol | undefined;
    /* @internal */ getTypeOfPropertyOfType(type: Type, propertyName: string): Type | undefined;
    getIndexInfoOfType(type: Type, kind: IndexKind): IndexInfo | undefined;
    getSignaturesOfType(type: Type, kind: SignatureKind): readonly Signature[];
    getIndexTypeOfType(type: Type, kind: IndexKind): Type | undefined;
    getBaseTypes(type: InterfaceType): BaseType[];
    getBaseTypeOfLiteralType(type: Type): Type;
    getWidenedType(type: Type): Type;
    /* @internal */
    getPromisedTypeOfPromise(promise: Type, errorNode?: Node): Type | undefined;
    getReturnTypeOfSignature(signature: Signature): Type;
    /**
        * Gets the type of a parameter at a given position in a signature.
        * Returns `any` if the index is not valid.
        */
    /* @internal */ getParameterType(signature: Signature, parameterIndex: number): Type;
    getNullableType(type: Type, flags: TypeFlags): Type;
    getNonNullableType(type: Type): Type;
    /* @internal */ getNonOptionalType(type: Type): Type;
    /* @internal */ isNullableType(type: Type): boolean;
    getTypeArguments(type: TypeReference): readonly Type[];

    // TODO: GH#18217 `xToDeclaration` calls are frequently asserted as defined.
    /** Note that the resulting nodes cannot be checked. */
    typeToTypeNode(type: Type, enclosingDeclaration?: Node, flags?: NodeBuilderFlags): TypeNode | undefined;
    /* @internal */ typeToTypeNode(type: Type, enclosingDeclaration?: Node, flags?: NodeBuilderFlags, tracker?: SymbolTracker): TypeNode | undefined; // eslint-disable-line @typescript-eslint/unified-signatures
    /** Note that the resulting nodes cannot be checked. */
    signatureToSignatureDeclaration(signature: Signature, kind: SyntaxKind, enclosingDeclaration?: Node, flags?: NodeBuilderFlags): SignatureDeclaration & {typeArguments?: NodeArray<TypeNode>} | undefined;
    /* @internal */ signatureToSignatureDeclaration(signature: Signature, kind: SyntaxKind, enclosingDeclaration?: Node, flags?: NodeBuilderFlags, tracker?: SymbolTracker): SignatureDeclaration & {typeArguments?: NodeArray<TypeNode>} | undefined; // eslint-disable-line @typescript-eslint/unified-signatures
    /** Note that the resulting nodes cannot be checked. */
    indexInfoToIndexSignatureDeclaration(indexInfo: IndexInfo, kind: IndexKind, enclosingDeclaration?: Node, flags?: NodeBuilderFlags): IndexSignatureDeclaration | undefined;
    /* @internal */ indexInfoToIndexSignatureDeclaration(indexInfo: IndexInfo, kind: IndexKind, enclosingDeclaration?: Node, flags?: NodeBuilderFlags, tracker?: SymbolTracker): IndexSignatureDeclaration | undefined; // eslint-disable-line @typescript-eslint/unified-signatures
    /** Note that the resulting nodes cannot be checked. */
    symbolToEntityName(symbol: Symbol, meaning: SymbolFlags, enclosingDeclaration?: Node, flags?: NodeBuilderFlags): EntityName | undefined;
    /** Note that the resulting nodes cannot be checked. */
    symbolToExpression(symbol: Symbol, meaning: SymbolFlags, enclosingDeclaration?: Node, flags?: NodeBuilderFlags): Expression | undefined;
    /** Note that the resulting nodes cannot be checked. */
    symbolToTypeParameterDeclarations(symbol: Symbol, enclosingDeclaration?: Node, flags?: NodeBuilderFlags): NodeArray<TypeParameterDeclaration> | undefined;
    /** Note that the resulting nodes cannot be checked. */
    symbolToParameterDeclaration(symbol: Symbol, enclosingDeclaration?: Node, flags?: NodeBuilderFlags): ParameterDeclaration | undefined;
    /** Note that the resulting nodes cannot be checked. */
    typeParameterToDeclaration(parameter: TypeParameter, enclosingDeclaration?: Node, flags?: NodeBuilderFlags): TypeParameterDeclaration | undefined;

    getSymbolsInScope(location: Node, meaning: SymbolFlags): Symbol[];
    getSymbolAtLocation(node: Node): Symbol | undefined;
    getSymbolsOfParameterPropertyDeclaration(parameter: ParameterDeclaration, parameterName: string): Symbol[];
    /**
        * The function returns the value (local variable) symbol of an identifier in the short-hand property assignment.
        * This is necessary as an identifier in short-hand property assignment can contains two meaning: property name and property value.
        */
    getShorthandAssignmentValueSymbol(location: Node): Symbol | undefined;
    getExportSpecifierLocalTargetSymbol(location: ExportSpecifier): Symbol | undefined;
    /**
        * If a symbol is a local symbol with an associated exported symbol, returns the exported symbol.
        * Otherwise returns its input.
        * For example, at `export type T = number;`:
        *     - `getSymbolAtLocation` at the location `T` will return the exported symbol for `T`.
        *     - But the result of `getSymbolsInScope` will contain the *local* symbol for `T`, not the exported symbol.
        *     - Calling `getExportSymbolOfSymbol` on that local symbol will return the exported symbol.
        */
    getExportSymbolOfSymbol(symbol: Symbol): Symbol;
    getPropertySymbolOfDestructuringAssignment(location: Identifier): Symbol | undefined;
    getTypeOfAssignmentPattern(pattern: AssignmentPattern): Type;
    getTypeAtLocation(node: Node): Type;
    getTypeFromTypeNode(node: TypeNode): Type;

    signatureToString(signature: Signature, enclosingDeclaration?: Node, flags?: TypeFormatFlags, kind?: SignatureKind): string;
    typeToString(type: Type, enclosingDeclaration?: Node, flags?: TypeFormatFlags): string;
    symbolToString(symbol: Symbol, enclosingDeclaration?: Node, meaning?: SymbolFlags, flags?: SymbolFormatFlags): string;
    typePredicateToString(predicate: TypePredicate, enclosingDeclaration?: Node, flags?: TypeFormatFlags): string;

    /* @internal */ writeSignature(signature: Signature, enclosingDeclaration?: Node, flags?: TypeFormatFlags, kind?: SignatureKind, writer?: EmitTextWriter): string;
    /* @internal */ writeType(type: Type, enclosingDeclaration?: Node, flags?: TypeFormatFlags, writer?: EmitTextWriter): string;
    /* @internal */ writeSymbol(symbol: Symbol, enclosingDeclaration?: Node, meaning?: SymbolFlags, flags?: SymbolFormatFlags, writer?: EmitTextWriter): string;
    /* @internal */ writeTypePredicate(predicate: TypePredicate, enclosingDeclaration?: Node, flags?: TypeFormatFlags, writer?: EmitTextWriter): string;

    getFullyQualifiedName(symbol: Symbol): string;
    getAugmentedPropertiesOfType(type: Type): Symbol[];
    getRootSymbols(symbol: Symbol): readonly Symbol[];
    getContextualType(node: Expression): Type | undefined;
    /* @internal */ getContextualTypeForObjectLiteralElement(element: ObjectLiteralElementLike): Type | undefined;
    /* @internal */ getContextualTypeForArgumentAtIndex(call: CallLikeExpression, argIndex: number): Type | undefined;
    /* @internal */ getContextualTypeForJsxAttribute(attribute: JsxAttribute | JsxSpreadAttribute): Type | undefined;
    /* @internal */ isContextSensitive(node: Expression | MethodDeclaration | ObjectLiteralElementLike | JsxAttributeLike): boolean;

    /**
        * returns unknownSignature in the case of an error.
        * returns undefined if the node is not valid.
        * @param argumentCount Apparent number of arguments, passed in case of a possibly incomplete call. This should come from an ArgumentListInfo. See `signatureHelp.ts`.
        */
    getResolvedSignature(node: CallLikeExpression, candidatesOutArray?: Signature[], argumentCount?: number): Signature | undefined;
    /* @internal */ getResolvedSignatureForSignatureHelp(node: CallLikeExpression, candidatesOutArray?: Signature[], argumentCount?: number): Signature | undefined;
    /* @internal */ getExpandedParameters(sig: Signature): readonly Symbol[];
    /* @internal */ hasEffectiveRestParameter(sig: Signature): boolean;
    getSignatureFromDeclaration(declaration: SignatureDeclaration): Signature | undefined;
    isImplementationOfOverload(node: SignatureDeclaration): boolean | undefined;
    isUndefinedSymbol(symbol: Symbol): boolean;
    isArgumentsSymbol(symbol: Symbol): boolean;
    isUnknownSymbol(symbol: Symbol): boolean;
    /* @internal */ getMergedSymbol(symbol: Symbol): Symbol;

    getConstantValue(node: EnumMember | PropertyAccessExpression | ElementAccessExpression): string | number | undefined;
    isValidPropertyAccess(node: PropertyAccessExpression | QualifiedName | ImportTypeNode, propertyName: string): boolean;
    /** Exclude accesses to private properties or methods with a `this` parameter that `type` doesn't satisfy. */
    /* @internal */ isValidPropertyAccessForCompletions(node: PropertyAccessExpression | ImportTypeNode | QualifiedName, type: Type, property: Symbol): boolean;
    /** Follow all aliases to get the original symbol. */
    getAliasedSymbol(symbol: Symbol): Symbol;
    /** Follow a *single* alias to get the immediately aliased symbol. */
    /* @internal */ getImmediateAliasedSymbol(symbol: Symbol): Symbol | undefined;
    getExportsOfModule(moduleSymbol: Symbol): Symbol[];
    /** Unlike `getExportsOfModule`, this includes properties of an `export =` value. */
    /* @internal */ getExportsAndPropertiesOfModule(moduleSymbol: Symbol): Symbol[];
    getJsxIntrinsicTagNamesAt(location: Node): Symbol[];
    isOptionalParameter(node: ParameterDeclaration): boolean;
    getAmbientModules(): Symbol[];

    tryGetMemberInModuleExports(memberName: string, moduleSymbol: Symbol): Symbol | undefined;
    /**
        * Unlike `tryGetMemberInModuleExports`, this includes properties of an `export =` value.
        * Does *not* return properties of primitive types.
        */
    /* @internal */ tryGetMemberInModuleExportsAndProperties(memberName: string, moduleSymbol: Symbol): Symbol | undefined;
    getApparentType(type: Type): Type;
    /* @internal */ getSuggestionForNonexistentProperty(name: Identifier | string, containingType: Type): string | undefined;
    /* @internal */ getSuggestionForNonexistentSymbol(location: Node, name: string, meaning: SymbolFlags): string | undefined;
    /* @internal */ getSuggestionForNonexistentExport(node: Identifier, target: Symbol): string | undefined;
    getBaseConstraintOfType(type: Type): Type | undefined;
    getDefaultFromTypeParameter(type: Type): Type | undefined;

    /* @internal */ getAnyType(): Type;
    /* @internal */ getStringType(): Type;
    /* @internal */ getNumberType(): Type;
    /* @internal */ getBooleanType(): Type;
    /* @internal */ getFalseType(fresh?: boolean): Type;
    /* @internal */ getTrueType(fresh?: boolean): Type;
    /* @internal */ getVoidType(): Type;
    /* @internal */ getUndefinedType(): Type;
    /* @internal */ getNullType(): Type;
    /* @internal */ getESSymbolType(): Type;
    /* @internal */ getNeverType(): Type;
    /* @internal */ getOptionalType(): Type;
    /* @internal */ getUnionType(types: Type[], subtypeReduction?: UnionReduction): Type;
    /* @internal */ createArrayType(elementType: Type): Type;
    /* @internal */ getElementTypeOfArrayType(arrayType: Type): Type | undefined;
    /* @internal */ createPromiseType(type: Type): Type;

    /* @internal */ isTypeAssignableTo(source: Type, target: Type): boolean;
    /* @internal */ createAnonymousType(symbol: Symbol, members: SymbolTable, callSignatures: Signature[], constructSignatures: Signature[], stringIndexInfo: IndexInfo | undefined, numberIndexInfo: IndexInfo | undefined): Type;
    /* @internal */ createSignature(
        declaration: SignatureDeclaration,
        typeParameters: TypeParameter[] | undefined,
        thisParameter: Symbol | undefined,
        parameters: Symbol[],
        resolvedReturnType: Type,
        typePredicate: TypePredicate | undefined,
        minArgumentCount: number,
        hasRestParameter: boolean,
        hasLiteralTypes: boolean,
    ): Signature;
    /* @internal */ createSymbol(flags: SymbolFlags, name: __String): TransientSymbol;
    /* @internal */ createIndexInfo(type: Type, isReadonly: boolean, declaration?: SignatureDeclaration): IndexInfo;
    /* @internal */ isSymbolAccessible(symbol: Symbol, enclosingDeclaration: Node | undefined, meaning: SymbolFlags, shouldComputeAliasToMarkVisible: boolean): SymbolAccessibilityResult;
    /* @internal */ tryFindAmbientModuleWithoutAugmentations(moduleName: string): Symbol | undefined;

    /* @internal */ getSymbolWalker(accept?: (symbol: Symbol) => boolean): SymbolWalker;

    // Should not be called directly.  Should only be accessed through the Program instance.
    /* @internal */ getDiagnostics(sourceFile?: SourceFile, cancellationToken?: CancellationToken): Diagnostic[];
    /* @internal */ getGlobalDiagnostics(): Diagnostic[];
    /* @internal */ getEmitResolver(sourceFile?: SourceFile, cancellationToken?: CancellationToken): EmitResolver;

    /* @internal */ getNodeCount(): number;
    /* @internal */ getIdentifierCount(): number;
    /* @internal */ getSymbolCount(): number;
    /* @internal */ getTypeCount(): number;
    /* @internal */ getRelationCacheSizes(): { assignable: number, identity: number, subtype: number };

    /* @internal */ isArrayType(type: Type): boolean;
    /* @internal */ isTupleType(type: Type): boolean;
    /* @internal */ isArrayLikeType(type: Type): boolean;
    /* @internal */ getObjectFlags(type: Type): ObjectFlags;

    /**
        * True if `contextualType` should not be considered for completions because
        * e.g. it specifies `kind: "a"` and obj has `kind: "b"`.
        */
    /* @internal */ isTypeInvalidDueToUnionDiscriminant(contextualType: Type, obj: ObjectLiteralExpression | JsxAttributes): boolean;
    /**
        * For a union, will include a property if it's defined in *any* of the member types.
        * So for `{ a } | { b }`, this will include both `a` and `b`.
        * Does not include properties of primitive types.
        */
    /* @internal */ getAllPossiblePropertiesOfTypes(type: readonly Type[]): Symbol[];
    /* @internal */ resolveName(name: string, location: Node, meaning: SymbolFlags, excludeGlobals: boolean): Symbol | undefined;
    /* @internal */ getJsxNamespace(location?: Node): string;

    /**
        * Note that this will return undefined in the following case:
        *     // a.ts
        *     export namespace N { export class C { } }
        *     // b.ts
        *     <<enclosingDeclaration>>
        * Where `C` is the symbol we're looking for.
        * This should be called in a loop climbing parents of the symbol, so we'll get `N`.
        */
    /* @internal */ getAccessibleSymbolChain(symbol: Symbol, enclosingDeclaration: Node | undefined, meaning: SymbolFlags, useOnlyExternalAliasing: boolean): Symbol[] | undefined;
    /* @internal */ getTypePredicateOfSignature(signature: Signature): TypePredicate | undefined;
    /**
        * An external module with an 'export =' declaration resolves to the target of the 'export =' declaration,
        * and an external module with no 'export =' declaration resolves to the module itself.
        */
    /* @internal */ resolveExternalModuleSymbol(symbol: Symbol): Symbol;
    /** @param node A location where we might consider accessing `this`. Not necessarily a ThisExpression. */
    /* @internal */ tryGetThisTypeAt(node: Node, includeGlobalThis?: boolean): Type | undefined;
    /* @internal */ getTypeArgumentConstraint(node: TypeNode): Type | undefined;

    /**
        * Does *not* get *all* suggestion diagnostics, just the ones that were convenient to report in the checker.
        * Others are added in computeSuggestionDiagnostics.
        */
    /* @internal */ getSuggestionDiagnostics(file: SourceFile, cancellationToken?: CancellationToken): readonly DiagnosticWithLocation[];

    /**
        * Depending on the operation performed, it may be appropriate to throw away the checker
        * if the cancellation token is triggered. Typically, if it is used for error checking
        * and the operation is cancelled, then it should be discarded, otherwise it is safe to keep.
        */
    runWithCancellationToken<T>(token: CancellationToken, cb: (checker: TypeChecker) => T): T;

    /* @internal */ getLocalTypeParametersOfClassOrInterfaceOrTypeAlias(symbol: Symbol): readonly TypeParameter[] | undefined;
}
```

### UnionReduction
```ts
export const enum UnionReduction {
    None = 0,
    Literal,
    Subtype
}
```

### NodeBuilderFlags
```ts
export const enum NodeBuilderFlags {
    None                                    = 0,
    // Options
    NoTruncation                            = 1 << 0,   // Don't truncate result
    WriteArrayAsGenericType                 = 1 << 1,   // Write Array<T> instead T[]
    GenerateNamesForShadowedTypeParams      = 1 << 2,   // When a type parameter T is shadowing another T, generate a name for it so it can still be referenced
    UseStructuralFallback                   = 1 << 3,   // When an alias cannot be named by its symbol, rather than report an error, fallback to a structural printout if possible
    ForbidIndexedAccessSymbolReferences     = 1 << 4,   // Forbid references like `I["a"]["b"]` - print `typeof I.a<x>.b<y>` instead
    WriteTypeArgumentsOfSignature           = 1 << 5,   // Write the type arguments instead of type parameters of the signature
    UseFullyQualifiedType                   = 1 << 6,   // Write out the fully qualified type name (eg. Module.Type, instead of Type)
    UseOnlyExternalAliasing                 = 1 << 7,   // Only use external aliases for a symbol
    SuppressAnyReturnType                   = 1 << 8,   // If the return type is any-like, don't offer a return type.
    WriteTypeParametersInQualifiedName      = 1 << 9,
    MultilineObjectLiterals                 = 1 << 10,  // Always write object literals across multiple lines
    WriteClassExpressionAsTypeLiteral       = 1 << 11,  // Write class {} as { new(): {} } - used for mixin declaration emit
    UseTypeOfFunction                       = 1 << 12,  // Build using typeof instead of function type literal
    OmitParameterModifiers                  = 1 << 13,  // Omit modifiers on parameters
    UseAliasDefinedOutsideCurrentScope      = 1 << 14,  // Allow non-visible aliases

    // Error handling
    AllowThisInObjectLiteral                = 1 << 15,
    AllowQualifedNameInPlaceOfIdentifier    = 1 << 16,
    AllowAnonymousIdentifier                = 1 << 17,
    AllowEmptyUnionOrIntersection           = 1 << 18,
    AllowEmptyTuple                         = 1 << 19,
    AllowUniqueESSymbolType                 = 1 << 20,
    AllowEmptyIndexInfoType                 = 1 << 21,

    // Errors (cont.)
    AllowNodeModulesRelativePaths           = 1 << 26,
    /* @internal */ DoNotIncludeSymbolChain = 1 << 27,    // Skip looking up and printing an accessible symbol chain

    IgnoreErrors = AllowThisInObjectLiteral | AllowQualifedNameInPlaceOfIdentifier | AllowAnonymousIdentifier | AllowEmptyUnionOrIntersection | AllowEmptyTuple | AllowEmptyIndexInfoType | AllowNodeModulesRelativePaths,

    // State
    InObjectTypeLiteral                     = 1 << 22,
    InTypeAlias                             = 1 << 23,    // Writing type in type alias declaration
    InInitialEntityName                     = 1 << 24,    // Set when writing the LHS of an entity name or entity name expression
    InReverseMappedType                     = 1 << 25,
}
```

### TypeFormatFlags
```ts
export const enum TypeFormatFlags {
    None                                    = 0,
    NoTruncation                            = 1 << 0,  // Don't truncate typeToString result
    WriteArrayAsGenericType                 = 1 << 1,  // Write Array<T> instead T[]
    // hole because there's a hole in node builder flags
    UseStructuralFallback                   = 1 << 3,   // When an alias cannot be named by its symbol, rather than report an error, fallback to a structural printout if possible
    // hole because there's a hole in node builder flags
    WriteTypeArgumentsOfSignature           = 1 << 5,  // Write the type arguments instead of type parameters of the signature
    UseFullyQualifiedType                   = 1 << 6,  // Write out the fully qualified type name (eg. Module.Type, instead of Type)
    // hole because `UseOnlyExternalAliasing` is here in node builder flags, but functions which take old flags use `SymbolFormatFlags` instead
    SuppressAnyReturnType                   = 1 << 8,  // If the return type is any-like, don't offer a return type.
    // hole because `WriteTypeParametersInQualifiedName` is here in node builder flags, but functions which take old flags use `SymbolFormatFlags` for this instead
    MultilineObjectLiterals                 = 1 << 10, // Always print object literals across multiple lines (only used to map into node builder flags)
    WriteClassExpressionAsTypeLiteral       = 1 << 11, // Write a type literal instead of (Anonymous class)
    UseTypeOfFunction                       = 1 << 12, // Write typeof instead of function type literal
    OmitParameterModifiers                  = 1 << 13, // Omit modifiers on parameters

    UseAliasDefinedOutsideCurrentScope      = 1 << 14, // For a `type T = ... ` defined in a different file, write `T` instead of its value, even though `T` can't be accessed in the current scope.

    // Error Handling
    AllowUniqueESSymbolType                 = 1 << 20, // This is bit 20 to align with the same bit in `NodeBuilderFlags`

    // TypeFormatFlags exclusive
    AddUndefined                            = 1 << 17, // Add undefined to types of initialized, non-optional parameters
    WriteArrowStyleSignature                = 1 << 18, // Write arrow style signature

    // State
    InArrayType                             = 1 << 19, // Writing an array element type
    InElementType                           = 1 << 21, // Writing an array or union element type
    InFirstTypeArgument                     = 1 << 22, // Writing first type argument of the instantiated type
    InTypeAlias                             = 1 << 23, // Writing type in type alias declaration

    /** @deprecated */ WriteOwnNameForAnyLike  = 0,  // Does nothing

    NodeBuilderFlagsMask = NoTruncation | WriteArrayAsGenericType | UseStructuralFallback | WriteTypeArgumentsOfSignature |
        UseFullyQualifiedType | SuppressAnyReturnType | MultilineObjectLiterals | WriteClassExpressionAsTypeLiteral |
        UseTypeOfFunction | OmitParameterModifiers | UseAliasDefinedOutsideCurrentScope | AllowUniqueESSymbolType | InTypeAlias,
}

```

### SymbolFormatFlags

```ts
export const enum SymbolFormatFlags {
None = 0x00000000,

// Write symbols's type argument if it is instantiated symbol
// eg. class C<T> { p: T }   <-- Show p as C<T>.p here
//     var a: C<number>;
//     var p = a.p; <--- Here p is property of C<number> so show it as C<number>.p instead of just C.p
WriteTypeParametersOrArguments = 0x00000001,

// Use only external alias information to get the symbol name in the given context
// eg.  module m { export class c { } } import x = m.c;
// When this flag is specified m.c will be used to refer to the class instead of alias symbol x
UseOnlyExternalAliasing = 0x00000002,

// Build symbol name using any nodes needed, instead of just components of an entity name
AllowAnyNodeKind = 0x00000004,

// Prefer aliases which are not directly visible
UseAliasDefinedOutsideCurrentScope = 0x00000008,

// Skip building an accessible symbol chain
/* @internal */ DoNotIncludeSymbolChain = 0x00000010,
}
```

### SymbolWalker
```ts
export interface SymbolWalker {
    /** Note: Return values are not ordered. */
    walkType(root: Type): { visitedTypes: readonly Type[], visitedSymbols: readonly Symbol[] };
    /** Note: Return values are not ordered. */
    walkSymbol(root: Symbol): { visitedTypes: readonly Type[], visitedSymbols: readonly Symbol[] };
}
```

### SymbolWriter
```ts

interface SymbolWriter extends SymbolTracker {
    writeKeyword(text: string): void;
    writeOperator(text: string): void;
    writePunctuation(text: string): void;
    writeSpace(text: string): void;
    writeStringLiteral(text: string): void;
    writeParameter(text: string): void;
    writeProperty(text: string): void;
    writeSymbol(text: string, symbol: Symbol): void;
    writeLine(): void;
    increaseIndent(): void;
    decreaseIndent(): void;
    clear(): void;
}
```

### SymbolAccessibility
```ts
export const enum SymbolAccessibility {
    Accessible,
    NotAccessible,
    CannotBeNamed
}
```

### SyntheticSymbolKind
```ts
export const enum SyntheticSymbolKind {
    UnionOrIntersection,
    Spread
}
```

### EmitResolver
```ts
export interface EmitResolver {
    hasGlobalName(name: string): boolean;
    getReferencedExportContainer(node: Identifier, prefixLocals?: boolean): SourceFile | ModuleDeclaration | EnumDeclaration | undefined;
    getReferencedImportDeclaration(node: Identifier): Declaration | undefined;
    getReferencedDeclarationWithCollidingName(node: Identifier): Declaration | undefined;
    isDeclarationWithCollidingName(node: Declaration): boolean;
    isValueAliasDeclaration(node: Node): boolean;
    isReferencedAliasDeclaration(node: Node, checkChildren?: boolean): boolean;
    isTopLevelValueImportEqualsWithEntityName(node: ImportEqualsDeclaration): boolean;
    getNodeCheckFlags(node: Node): NodeCheckFlags;
    isDeclarationVisible(node: Declaration | AnyImportSyntax): boolean;
    isLateBound(node: Declaration): node is LateBoundDeclaration;
    collectLinkedAliases(node: Identifier, setVisibility?: boolean): Node[] | undefined;
    isImplementationOfOverload(node: FunctionLike): boolean | undefined;
    isRequiredInitializedParameter(node: ParameterDeclaration): boolean;
    isOptionalUninitializedParameterProperty(node: ParameterDeclaration): boolean;
    isExpandoFunctionDeclaration(node: FunctionDeclaration): boolean;
    getPropertiesOfContainerFunction(node: Declaration): Symbol[];
    createTypeOfDeclaration(declaration: AccessorDeclaration | VariableLikeDeclaration | PropertyAccessExpression, enclosingDeclaration: Node, flags: NodeBuilderFlags, tracker: SymbolTracker, addUndefined?: boolean): TypeNode | undefined;
    createReturnTypeOfSignatureDeclaration(signatureDeclaration: SignatureDeclaration, enclosingDeclaration: Node, flags: NodeBuilderFlags, tracker: SymbolTracker): TypeNode | undefined;
    createTypeOfExpression(expr: Expression, enclosingDeclaration: Node, flags: NodeBuilderFlags, tracker: SymbolTracker): TypeNode | undefined;
    createLiteralConstValue(node: VariableDeclaration | PropertyDeclaration | PropertySignature | ParameterDeclaration, tracker: SymbolTracker): Expression;
    isSymbolAccessible(symbol: Symbol, enclosingDeclaration: Node | undefined, meaning: SymbolFlags | undefined, shouldComputeAliasToMarkVisible: boolean): SymbolAccessibilityResult;
    isEntityNameVisible(entityName: EntityNameOrEntityNameExpression, enclosingDeclaration: Node): SymbolVisibilityResult;
    // Returns the constant value this property access resolves to, or 'undefined' for a non-constant
    getConstantValue(node: EnumMember | PropertyAccessExpression | ElementAccessExpression): string | number | undefined;
    getReferencedValueDeclaration(reference: Identifier): Declaration | undefined;
    getTypeReferenceSerializationKind(typeName: EntityName, location?: Node): TypeReferenceSerializationKind;
    isOptionalParameter(node: ParameterDeclaration): boolean;
    moduleExportsSomeValue(moduleReferenceExpression: Expression): boolean;
    isArgumentsLocalBinding(node: Identifier): boolean;
    getExternalModuleFileFromDeclaration(declaration: ImportEqualsDeclaration | ImportDeclaration | ExportDeclaration | ModuleDeclaration | ImportTypeNode): SourceFile | undefined;
    getTypeReferenceDirectivesForEntityName(name: EntityNameOrEntityNameExpression): string[] | undefined;
    getTypeReferenceDirectivesForSymbol(symbol: Symbol, meaning?: SymbolFlags): string[] | undefined;
    isLiteralConstDeclaration(node: VariableDeclaration | PropertyDeclaration | PropertySignature | ParameterDeclaration): boolean;
    getJsxFactoryEntity(location?: Node): EntityName | undefined;
    getAllAccessorDeclarations(declaration: AccessorDeclaration): AllAccessorDeclarations;
    getSymbolOfExternalModuleSpecifier(node: StringLiteralLike): Symbol | undefined;
    isBindingCapturedByNode(node: Node, decl: VariableDeclaration | BindingElement): boolean;
    getDeclarationStatementsForSourceFile(node: SourceFile, flags: NodeBuilderFlags, tracker: SymbolTracker, bundled?: boolean): Statement[] | undefined;
}
```

### NodeCheckFlags
```ts
export const enum NodeCheckFlags {
    TypeChecked                         = 0x00000001,  // Node has been type checked
    LexicalThis                         = 0x00000002,  // Lexical 'this' reference
    CaptureThis                         = 0x00000004,  // Lexical 'this' used in body
    CaptureNewTarget                    = 0x00000008,  // Lexical 'new.target' used in body
    SuperInstance                       = 0x00000100,  // Instance 'super' reference
    SuperStatic                         = 0x00000200,  // Static 'super' reference
    ContextChecked                      = 0x00000400,  // Contextual types have been assigned
    AsyncMethodWithSuper                = 0x00000800,  // An async method that reads a value from a member of 'super'.
    AsyncMethodWithSuperBinding         = 0x00001000,  // An async method that assigns a value to a member of 'super'.
    CaptureArguments                    = 0x00002000,  // Lexical 'arguments' used in body
    EnumValuesComputed                  = 0x00004000,  // Values for enum members have been computed, and any errors have been reported for them.
    LexicalModuleMergesWithClass        = 0x00008000,  // Instantiated lexical module declaration is merged with a previous class declaration.
    LoopWithCapturedBlockScopedBinding  = 0x00010000,  // Loop that contains block scoped variable captured in closure
    ContainsCapturedBlockScopeBinding   = 0x00020000,  // Part of a loop that contains block scoped variable captured in closure
    CapturedBlockScopedBinding          = 0x00040000,  // Block-scoped binding that is captured in some function
    BlockScopedBindingInLoop            = 0x00080000,  // Block-scoped binding with declaration nested inside iteration statement
    ClassWithBodyScopedClassBinding     = 0x00100000,  // Decorated class that contains a binding to itself inside of the class body.
    BodyScopedClassBinding              = 0x00200000,  // Binding to a decorated class inside of the class's body.
    NeedsLoopOutParameter               = 0x00400000,  // Block scoped binding whose value should be explicitly copied outside of the converted loop
    AssignmentsMarked                   = 0x00800000,  // Parameter assignments have been marked
    ClassWithConstructorReference       = 0x01000000,  // Class that contains a binding to its constructor inside of the class body.
    ConstructorReferenceInClass         = 0x02000000,  // Binding to a class constructor inside of the class's body.
}
```

### NodeLinks
```ts
export interface NodeLinks {
    flags: NodeCheckFlags;           // Set of flags specific to Node
    resolvedType?: Type;              // Cached type of type node
    resolvedEnumType?: Type;          // Cached constraint type from enum jsdoc tag
    resolvedSignature?: Signature;    // Cached signature of signature node or call expression
    resolvedSymbol?: Symbol;          // Cached name resolution result
    resolvedIndexInfo?: IndexInfo;    // Cached indexing info resolution result
    effectsSignature?: Signature;     // Signature with possible control flow effects
    enumMemberValue?: string | number;  // Constant value of enum member
    isVisible?: boolean;              // Is this node visible
    containsArgumentsReference?: boolean; // Whether a function-like declaration contains an 'arguments' reference
    hasReportedStatementInAmbientContext?: boolean;  // Cache boolean if we report statements in ambient context
    jsxFlags: JsxFlags;              // flags for knowing what kind of element/attributes we're dealing with
    resolvedJsxElementAttributesType?: Type;  // resolved element attributes type of a JSX openinglike element
    resolvedJsxElementAllAttributesType?: Type;  // resolved all element attributes type of a JSX openinglike element
    hasSuperCall?: boolean;           // recorded result when we try to find super-call. We only try to find one if this flag is undefined, indicating that we haven't made an attempt.
    superCall?: SuperCall;  // Cached first super-call found in the constructor. Used in checking whether super is called before this-accessing
    switchTypes?: Type[];             // Cached array of switch case expression types
    jsxNamespace?: Symbol | false;          // Resolved jsx namespace symbol for this node
    contextFreeType?: Type;          // Cached context-free type used by the first pass of inference; used when a function's return is partially contextually sensitive
    deferredNodes?: Map<Node>; // Set of nodes whose checking has been deferred
    capturedBlockScopeBindings?: Symbol[]; // Block-scoped bindings captured beneath this part of an IterationStatement
    outerTypeParameters?: TypeParameter[];  // Outer type parameters of anonymous object type
    instantiations?: Map<Type>;         // Instantiations of generic type alias (undefined if non-generic)
    isExhaustive?: boolean;           // Is node an exhaustive switch statement
}
```

### ModuleResolutionKind
```ts
export enum ModuleResolutionKind {
    Classic  = 1,
    NodeJs   = 2
}
```

### PluginImport
```ts
export interface PluginImport {
    name: string;
}
```

### ProjectReference
```ts
export interface ProjectReference {
    /** A normalized path on disk */
    path: string;
    /** The path as the user originally wrote it */
    originalPath?: string;
    /** True if the output of this reference should be prepended to the output of this project. Only valid for --outFile compilations */
    prepend?: boolean;
    /** True if it is intended that this reference form a circularity */
    circular?: boolean;
}
```

### CompilerOptionsValue
```ts
export type CompilerOptionsValue 
    = string | number | boolean 
    | (string | number)[] | string[] 
    | MapLike<string[]> | PluginImport[] | ProjectReference[] 
    | null | undefined;
```

### CompilerOptions
```ts
export interface CompilerOptions {
    /*@internal*/ all?: boolean;
    allowJs?: boolean;
    /*@internal*/ allowNonTsExtensions?: boolean;
    allowSyntheticDefaultImports?: boolean;
    allowUmdGlobalAccess?: boolean;
    allowUnreachableCode?: boolean;
    allowUnusedLabels?: boolean;
    alwaysStrict?: boolean;  // Always combine with strict property
    baseUrl?: string;
    /** An error if set - this should only go through the -b pipeline and not actually be observed */
    /*@internal*/
    build?: boolean;
    charset?: string;
    checkJs?: boolean;
    /* @internal */ configFilePath?: string;
    /** configFile is set as non enumerable property so as to avoid checking of json source files */
    /* @internal */ readonly configFile?: TsConfigSourceFile;
    declaration?: boolean;
    declarationMap?: boolean;
    emitDeclarationOnly?: boolean;
    declarationDir?: string;
    /* @internal */ diagnostics?: boolean;
    /* @internal */ extendedDiagnostics?: boolean;
    disableSizeLimit?: boolean;
    disableSourceOfProjectReferenceRedirect?: boolean;
    downlevelIteration?: boolean;
    emitBOM?: boolean;
    emitDecoratorMetadata?: boolean;
    experimentalDecorators?: boolean;
    forceConsistentCasingInFileNames?: boolean;
    /*@internal*/generateCpuProfile?: string;
    /*@internal*/help?: boolean;
    importHelpers?: boolean;
    /*@internal*/init?: boolean;
    inlineSourceMap?: boolean;
    inlineSources?: boolean;
    isolatedModules?: boolean;
    jsx?: JsxEmit;
    keyofStringsOnly?: boolean;
    lib?: string[];
    /*@internal*/listEmittedFiles?: boolean;
    /*@internal*/listFiles?: boolean;
    locale?: string;
    mapRoot?: string;
    maxNodeModuleJsDepth?: number;
    module?: ModuleKind;
    moduleResolution?: ModuleResolutionKind;
    newLine?: NewLineKind;
    noEmit?: boolean;
    /*@internal*/noEmitForJsFiles?: boolean;
    noEmitHelpers?: boolean;
    noEmitOnError?: boolean;
    noErrorTruncation?: boolean;
    noFallthroughCasesInSwitch?: boolean;
    noImplicitAny?: boolean;  // Always combine with strict property
    noImplicitReturns?: boolean;
    noImplicitThis?: boolean;  // Always combine with strict property
    noStrictGenericChecks?: boolean;
    noUnusedLocals?: boolean;
    noUnusedParameters?: boolean;
    noImplicitUseStrict?: boolean;
    noLib?: boolean;
    noResolve?: boolean;
    out?: string;
    outDir?: string;
    outFile?: string;
    paths?: MapLike<string[]>;
    /*@internal*/ plugins?: PluginImport[];
    preserveConstEnums?: boolean;
    preserveSymlinks?: boolean;
    /* @internal */ preserveWatchOutput?: boolean;
    project?: string;
    /* @internal */ pretty?: boolean;
    reactNamespace?: string;
    jsxFactory?: string;
    composite?: boolean;
    incremental?: boolean;
    tsBuildInfoFile?: string;
    removeComments?: boolean;
    rootDir?: string;
    rootDirs?: string[];
    skipLibCheck?: boolean;
    skipDefaultLibCheck?: boolean;
    sourceMap?: boolean;
    sourceRoot?: string;
    strict?: boolean;
    strictFunctionTypes?: boolean;  // Always combine with strict property
    strictBindCallApply?: boolean;  // Always combine with strict property
    strictNullChecks?: boolean;  // Always combine with strict property
    strictPropertyInitialization?: boolean;  // Always combine with strict property
    stripInternal?: boolean;
    suppressExcessPropertyErrors?: boolean;
    suppressImplicitAnyIndexErrors?: boolean;
    /* @internal */ suppressOutputPathCheck?: boolean;
    target?: ScriptTarget; // TODO: GH#18217 frequently asserted as defined
    traceResolution?: boolean;
    resolveJsonModule?: boolean;
    types?: string[];
    /** Paths used to compute primary types search locations */
    typeRoots?: string[];
    /*@internal*/ version?: boolean;
    /*@internal*/ watch?: boolean;
    esModuleInterop?: boolean;
    /* @internal */ showConfig?: boolean;
    useDefineForClassFields?: boolean;

    [option: string]: CompilerOptionsValue | TsConfigSourceFile | undefined;
}
```

### TypeAcquisition
```ts
export interface TypeAcquisition {
    /**
        * @deprecated typingOptions.enableAutoDiscovery
        * Use typeAcquisition.enable instead.
        */
    enableAutoDiscovery?: boolean;
    enable?: boolean;
    include?: string[];
    exclude?: string[];
    [option: string]: string[] | boolean | undefined;
}
```

### ModuleKind
```ts
export enum ModuleKind {
    None = 0,
    CommonJS = 1,
    AMD = 2,
    UMD = 3,
    System = 4,

    // NOTE: ES module kinds should be contiguous to more easily check whether a module kind is *any* ES module kind.
    //       Non-ES module kinds should not come between ES2015 (the earliest ES module kind) and ESNext (the last ES
    //       module kind).
    ES2015 = 5,
    ESNext = 99
}
```

###  NewLineKind
```ts
export const enum NewLineKind {
    CarriageReturnLineFeed = 0,
    LineFeed = 1
}
```

### LineAndCharacter
```ts
export interface LineAndCharacter {
    /** 0-based. */
    line: number;
    /*
        * 0-based. This value denotes the character position in line and is different from the 'column' because of tab characters.
        */
    character: number;
}
```

### ScriptKind
```ts
export const enum ScriptKind {
    Unknown = 0,
    JS = 1,
    JSX = 2,
    TS = 3,
    TSX = 4,
    External = 5,
    JSON = 6,
    
    Deferred = 7
}
```

### ScriptTarget
```ts
export const enum ScriptTarget {
    ES3 = 0,
    ES5 = 1,
    ES2015 = 2,
    ES2016 = 3,
    ES2017 = 4,
    ES2018 = 5,
    ES2019 = 6,
    ES2020 = 7,
    ESNext = 99,
    JSON = 100,
    Latest = ESNext,
}
```

### LanguageVariant
```ts
export const enum LanguageVariant {
    Standard,
    JSX
}
```

### ParsedCommandLine
```ts
export interface ParsedCommandLine {
    options: CompilerOptions;
    typeAcquisition?: TypeAcquisition;
    fileNames: string[];
    projectReferences?: readonly ProjectReference[];
    raw?: any;
    errors: Diagnostic[];
    wildcardDirectories?: MapLike<WatchDirectoryFlags>;
    compileOnSave?: boolean;
    /* @internal */ configFileSpecs?: ConfigFileSpecs;
}
```

### WatchDirectoryFlags
```ts
export const enum WatchDirectoryFlags {
    None = 0,
    Recursive = 1 << 0,
}
```

### ConfigFileSpecs
```ts
export interface ConfigFileSpecs {
    filesSpecs: readonly string[] | undefined;
    /**
        * Present to report errors (user specified specs), validatedIncludeSpecs are used for file name matching
        */
    includeSpecs?: readonly string[];
    /**
        * Present to report errors (user specified specs), validatedExcludeSpecs are used for file name matching
        */
    excludeSpecs?: readonly string[];
    validatedIncludeSpecs?: readonly string[];
    validatedExcludeSpecs?: readonly string[];
    wildcardDirectories: MapLike<WatchDirectoryFlags>;
}
```

### ExpandResult
```ts
export interface ExpandResult {
    fileNames: string[];
    wildcardDirectories: MapLike<WatchDirectoryFlags>;
    /* @internal */ spec: ConfigFileSpecs;
}
```

### RequireResult
```ts
export type RequireResult<T = {}> =
    | { module: T, modulePath?: string, error: undefined }
    | { module: undefined, modulePath?: undefined, error: { stack?: string, message?: string } };
```

### CreateProgramOptions
```ts
export interface CreateProgramOptions {
    rootNames: readonly string[];
    options: CompilerOptions;
    projectReferences?: readonly ProjectReference[];
    host?: CompilerHost;
    oldProgram?: Program;
    configFileParsingDiagnostics?: readonly Diagnostic[];
}
```

### CommandLineOptionBase
```ts
export interface CommandLineOptionBase {
    name: string;
    type: "string" | "number" | "boolean" | "object" | "list" | Map<number | string>;    // a value of a primitive type, or an object literal mapping named values to actual values
    isFilePath?: boolean;                                   // True if option value is a path or fileName
    shortName?: string;                                     // A short mnemonic for convenience - for instance, 'h' can be used in place of 'help'
    description?: DiagnosticMessage;                        // The message describing what the command line switch does
    paramType?: DiagnosticMessage;                          // The name to be used for a non-boolean option's parameter
    isTSConfigOnly?: boolean;                               // True if option can only be specified via tsconfig.json file
    isCommandLineOnly?: boolean;
    showInSimplifiedHelpView?: boolean;
    category?: DiagnosticMessage;
    strictFlag?: true;                                      // true if the option is one of the flag under strict
    affectsSourceFile?: true;                               // true if we should recreate SourceFiles after this option changes
    affectsModuleResolution?: true;                         // currently same effect as `affectsSourceFile`
    affectsBindDiagnostics?: true;                          // true if this affects binding (currently same effect as `affectsSourceFile`)
    affectsSemanticDiagnostics?: true;                      // true if option affects semantic diagnostics
    affectsEmit?: true;                                     // true if the options affects emit
    transpileOptionValue?: boolean | undefined;             // If set this means that the option should be set to this value when transpiling
}
```

### CommandLineOptionOfPrimitiveType
```ts
export interface CommandLineOptionOfPrimitiveType extends CommandLineOptionBase {
    type: "string" | "number" | "boolean";
}
```

### CommandLineOptionOfCustomType
```ts
export interface CommandLineOptionOfCustomType extends CommandLineOptionBase {
    type: Map<number | string>;  // an object literal mapping named values to actual values
}
```

### TsConfigOnlyOption
```ts
export interface TsConfigOnlyOption extends CommandLineOptionBase {
    type: "object";
    elementOptions?: Map<CommandLineOption>;
    extraKeyDiagnosticMessage?: DiagnosticMessage;
}
```

### CommandLineOptionOfListType
```ts
export interface CommandLineOptionOfListType extends CommandLineOptionBase {
    type: "list";
    element: CommandLineOptionOfCustomType | CommandLineOptionOfPrimitiveType | TsConfigOnlyOption;
}
```

### CommandLineOption
```ts
export type CommandLineOption 
    = CommandLineOptionOfCustomType
    | CommandLineOptionOfPrimitiveType
    | TsConfigOnlyOption 
    | CommandLineOptionOfListType;
```

### ModuleResolutionHost
```ts
export interface ModuleResolutionHost {
    // TODO: GH#18217 Optional methods frequently used as non-optional

    fileExists(fileName: string): boolean;
    // readFile function is used to read arbitrary text files on disk, i.e. when resolution procedure needs the content of 'package.json'
    // to determine location of bundled typings for node module
    readFile(fileName: string): string | undefined;
    trace?(s: string): void;
    directoryExists?(directoryName: string): boolean;
    /**
        * Resolve a symbolic link.
        * @see https://nodejs.org/api/fs.html#fs_fs_realpathsync_path_options
        */
    realpath?(path: string): string;
    getCurrentDirectory?(): string;
    getDirectories?(path: string): string[];
}
```

### ResolvedModule
```ts
export interface ResolvedModule {
    /** Path of the file the module was resolved to. */
    resolvedFileName: string;
    /** True if `resolvedFileName` comes from `node_modules`. */
    isExternalLibraryImport?: boolean;
}
```

### ResolvedModuleFull
```ts
export interface ResolvedModuleFull extends ResolvedModule {
    /* @internal */
    readonly originalPath?: string;
    /**
        * Extension of resolvedFileName. This must match what's at the end of resolvedFileName.
        * This is optional for backwards-compatibility, but will be added if not provided.
        */
    extension: Extension;
    packageId?: PackageId;
}
```

### PackageId
```ts
export interface PackageId {
    /**
        * Name of the package.
        * Should not include `@types`.
        * If accessing a non-index file, this should include its name e.g. "foo/bar".
        */
    name: string;
    /**
        * Name of a submodule within this package.
        * May be "".
        */
    subModuleName: string;
    /** Version of the package, e.g. "1.2.3" */
    version: string;
}

```

### ResolvedModuleWithFailedLookupLocations
```ts
export interface ResolvedModuleWithFailedLookupLocations {
    readonly resolvedModule: ResolvedModuleFull | undefined;
    /* @internal */
    readonly failedLookupLocations: readonly string[];
}
```

### ResolvedTypeReferenceDirective
```ts
export interface ResolvedTypeReferenceDirective {
    // True if the type declaration file was found in a primary lookup location
    primary: boolean;
    // The location of the .d.ts file we located, or undefined if resolution failed
    resolvedFileName: string | undefined;
    packageId?: PackageId;
    /** True if `resolvedFileName` comes from `node_modules`. */
    isExternalLibraryImport?: boolean;
}
```

### ResolvedTypeReferenceDirectiveWithFailedLookupLocations
```ts
export interface ResolvedTypeReferenceDirectiveWithFailedLookupLocations {
    readonly resolvedTypeReferenceDirective: ResolvedTypeReferenceDirective | undefined;
    readonly failedLookupLocations: readonly string[];
}
```

### HasInvalidatedResolution
```ts
export type HasInvalidatedResolution = (sourceFile: Path) => boolean;
```

### CompilerHost
```ts
export interface CompilerHost extends ModuleResolutionHost {
    getSourceFile(fileName: string, languageVersion: ScriptTarget, onError?: (message: string) => void, shouldCreateNewSourceFile?: boolean): SourceFile | undefined;
    getSourceFileByPath?(fileName: string, path: Path, languageVersion: ScriptTarget, onError?: (message: string) => void, shouldCreateNewSourceFile?: boolean): SourceFile | undefined;
    getCancellationToken?(): CancellationToken;
    getDefaultLibFileName(options: CompilerOptions): string;
    getDefaultLibLocation?(): string;
    writeFile: WriteFileCallback;
    getCurrentDirectory(): string;
    getCanonicalFileName(fileName: string): string;
    useCaseSensitiveFileNames(): boolean;
    getNewLine(): string;
    readDirectory?(rootDir: string, extensions: readonly string[], excludes: readonly string[] | undefined, includes: readonly string[], depth?: number): string[];

    /*
        * CompilerHost must either implement resolveModuleNames (in case if it wants to be completely in charge of
        * module name resolution) or provide implementation for methods from ModuleResolutionHost (in this case compiler
        * will apply built-in module resolution logic and use members of ModuleResolutionHost to ask host specific questions).
        * If resolveModuleNames is implemented then implementation for members from ModuleResolutionHost can be just
        * 'throw new Error("NotImplemented")'
        */
    resolveModuleNames?(moduleNames: string[], containingFile: string, reusedNames: string[] | undefined, redirectedReference: ResolvedProjectReference | undefined, options: CompilerOptions): (ResolvedModule | undefined)[];
    /**
        * This method is a companion for 'resolveModuleNames' and is used to resolve 'types' references to actual type declaration files
        */
    resolveTypeReferenceDirectives?(typeReferenceDirectiveNames: string[], containingFile: string, redirectedReference: ResolvedProjectReference | undefined, options: CompilerOptions): (ResolvedTypeReferenceDirective | undefined)[];
    getEnvironmentVariable?(name: string): string | undefined;
    /* @internal */ onReleaseOldSourceFile?(oldSourceFile: SourceFile, oldOptions: CompilerOptions, hasSourceFileByPath: boolean): void;
    /* @internal */ hasInvalidatedResolution?: HasInvalidatedResolution;
    /* @internal */ hasChangedAutomaticTypeDirectiveNames?: boolean;
    createHash?(data: string): string;
    getParsedCommandLine?(fileName: string): ParsedCommandLine | undefined;
    /* @internal */ setResolvedProjectReferenceCallbacks?(callbacks: ResolvedProjectReferenceCallbacks): void;
    /* @internal */ useSourceOfProjectReferenceRedirect?(): boolean;

    // TODO: later handle this in better way in builder host instead once the api for tsbuild finalizes and doesn't use compilerHost as base
    /*@internal*/createDirectory?(directory: string): void;
    /*@internal*/getSymlinks?(): ReadonlyMap<string>;
}
```

### SourceOfProjectReferenceRedirect
```ts
export type SourceOfProjectReferenceRedirect = string | true;
```

### ResolvedProjectReferenceCallbacks
```ts
export interface ResolvedProjectReferenceCallbacks {
    getSourceOfProjectReferenceRedirect(fileName: string): SourceOfProjectReferenceRedirect | undefined;
    forEachResolvedProjectReference<T>(cb: (resolvedProjectReference: ResolvedProjectReference | undefined, resolvedProjectReferencePath: Path) => T | undefined): T | undefined;
}
```

### TransformFlags
```ts
export const enum TransformFlags {
    None = 0,

    // Facts
    // - Flags used to indicate that a node or subtree contains syntax that requires transformation.
    ContainsTypeScript = 1 << 0,
    ContainsJsx = 1 << 1,
    ContainsESNext = 1 << 2,
    ContainsES2019 = 1 << 3,
    ContainsES2018 = 1 << 4,
    ContainsES2017 = 1 << 5,
    ContainsES2016 = 1 << 6,
    ContainsES2015 = 1 << 7,
    ContainsGenerator = 1 << 8,
    ContainsDestructuringAssignment = 1 << 9,

    // Markers
    // - Flags used to indicate that a subtree contains a specific transformation.
    ContainsTypeScriptClassSyntax = 1 << 10, // Decorators, Property Initializers, Parameter Property Initializers
    ContainsLexicalThis = 1 << 11,
    ContainsRestOrSpread = 1 << 12,
    ContainsObjectRestOrSpread = 1 << 13,
    ContainsComputedPropertyName = 1 << 14,
    ContainsBlockScopedBinding = 1 << 15,
    ContainsBindingPattern = 1 << 16,
    ContainsYield = 1 << 17,
    ContainsHoistedDeclarationOrCompletion = 1 << 18,
    ContainsDynamicImport = 1 << 19,
    ContainsClassFields = 1 << 20,

    // Please leave this as 1 << 29.
    // It is the maximum bit we can set before we outgrow the size of a v8 small integer (SMI) on an x86 system.
    // It is a good reminder of how much room we have left
    HasComputedFlags = 1 << 29, // Transform flags have been computed.

    // Assertions
    // - Bitmasks that are used to assert facts about the syntax of a node and its subtree.
    AssertTypeScript = ContainsTypeScript,
    AssertJsx = ContainsJsx,
    AssertESNext = ContainsESNext,
    AssertES2019 = ContainsES2019,
    AssertES2018 = ContainsES2018,
    AssertES2017 = ContainsES2017,
    AssertES2016 = ContainsES2016,
    AssertES2015 = ContainsES2015,
    AssertGenerator = ContainsGenerator,
    AssertDestructuringAssignment = ContainsDestructuringAssignment,

    // Scope Exclusions
    // - Bitmasks that exclude flags from propagating out of a specific context
    //   into the subtree flags of their container.
    OuterExpressionExcludes = HasComputedFlags,
    PropertyAccessExcludes = OuterExpressionExcludes,
    NodeExcludes = PropertyAccessExcludes,
    ArrowFunctionExcludes = NodeExcludes | ContainsTypeScriptClassSyntax | ContainsBlockScopedBinding | ContainsYield | ContainsHoistedDeclarationOrCompletion | ContainsBindingPattern | ContainsObjectRestOrSpread,
    FunctionExcludes = NodeExcludes | ContainsTypeScriptClassSyntax | ContainsLexicalThis | ContainsBlockScopedBinding | ContainsYield | ContainsHoistedDeclarationOrCompletion | ContainsBindingPattern | ContainsObjectRestOrSpread,
    ConstructorExcludes = NodeExcludes | ContainsLexicalThis | ContainsBlockScopedBinding | ContainsYield | ContainsHoistedDeclarationOrCompletion | ContainsBindingPattern | ContainsObjectRestOrSpread,
    MethodOrAccessorExcludes = NodeExcludes | ContainsLexicalThis | ContainsBlockScopedBinding | ContainsYield | ContainsHoistedDeclarationOrCompletion | ContainsBindingPattern | ContainsObjectRestOrSpread,
    PropertyExcludes = NodeExcludes | ContainsLexicalThis,
    ClassExcludes = NodeExcludes | ContainsTypeScriptClassSyntax | ContainsComputedPropertyName,
    ModuleExcludes = NodeExcludes | ContainsTypeScriptClassSyntax | ContainsLexicalThis | ContainsBlockScopedBinding | ContainsHoistedDeclarationOrCompletion,
    TypeExcludes = ~ContainsTypeScript,
    ObjectLiteralExcludes = NodeExcludes | ContainsTypeScriptClassSyntax | ContainsComputedPropertyName | ContainsObjectRestOrSpread,
    ArrayLiteralOrCallOrNewExcludes = NodeExcludes | ContainsRestOrSpread,
    VariableDeclarationListExcludes = NodeExcludes | ContainsBindingPattern | ContainsObjectRestOrSpread,
    ParameterExcludes = NodeExcludes,
    CatchClauseExcludes = NodeExcludes | ContainsObjectRestOrSpread,
    BindingPatternExcludes = NodeExcludes | ContainsRestOrSpread,

    // Propagating flags
    // - Bitmasks for flags that should propagate from a child
    PropertyNamePropagatingFlags = ContainsLexicalThis,

    // Masks
    // - Additional bitmasks
}
```
### SourceMapSource
```ts
export interface SourceMapSource {
    fileName: string;
    text: string;
    /* @internal */ lineMap: readonly number[];
    skipTrivia?: (pos: number) => number;
}
```
### SourceMapRange
```ts
export interface SourceMapRange extends TextRange {
    source?: SourceMapSource;
}
```

### EmitFlags
```ts
export const enum EmitFlags {
    None = 0,
    SingleLine = 1 << 0,                    // The contents of this node should be emitted on a single line.
    AdviseOnEmitNode = 1 << 1,              // The printer should invoke the onEmitNode callback when printing this node.
    NoSubstitution = 1 << 2,                // Disables further substitution of an expression.
    CapturesThis = 1 << 3,                  // The function captures a lexical `this`
    NoLeadingSourceMap = 1 << 4,            // Do not emit a leading source map location for this node.
    NoTrailingSourceMap = 1 << 5,           // Do not emit a trailing source map location for this node.
    NoSourceMap = NoLeadingSourceMap | NoTrailingSourceMap, // Do not emit a source map location for this node.
    NoNestedSourceMaps = 1 << 6,            // Do not emit source map locations for children of this node.
    NoTokenLeadingSourceMaps = 1 << 7,      // Do not emit leading source map location for token nodes.
    NoTokenTrailingSourceMaps = 1 << 8,     // Do not emit trailing source map location for token nodes.
    NoTokenSourceMaps = NoTokenLeadingSourceMaps | NoTokenTrailingSourceMaps, // Do not emit source map locations for tokens of this node.
    NoLeadingComments = 1 << 9,             // Do not emit leading comments for this node.
    NoTrailingComments = 1 << 10,           // Do not emit trailing comments for this node.
    NoComments = NoLeadingComments | NoTrailingComments, // Do not emit comments for this node.
    NoNestedComments = 1 << 11,
    HelperName = 1 << 12,                   // The Identifier refers to an *unscoped* emit helper (one that is emitted at the top of the file)
    ExportName = 1 << 13,                   // Ensure an export prefix is added for an identifier that points to an exported declaration with a local name (see SymbolFlags.ExportHasLocal).
    LocalName = 1 << 14,                    // Ensure an export prefix is not added for an identifier that points to an exported declaration.
    InternalName = 1 << 15,                 // The name is internal to an ES5 class body function.
    Indented = 1 << 16,                     // Adds an explicit extra indentation level for class and function bodies when printing (used to match old emitter).
    NoIndentation = 1 << 17,                // Do not indent the node.
    AsyncFunctionBody = 1 << 18,
    ReuseTempVariableScope = 1 << 19,       // Reuse the existing temp variable scope during emit.
    CustomPrologue = 1 << 20,               // Treat the statement as if it were a prologue directive (NOTE: Prologue directives are *not* transformed).
    NoHoisting = 1 << 21,                   // Do not hoist this declaration in --module system
    HasEndOfDeclarationMarker = 1 << 22,    // Declaration has an associated NotEmittedStatement to mark the end of the declaration
    Iterator = 1 << 23,                     // The expression to a `yield*` should be treated as an Iterator when down-leveling, not an Iterable.
    NoAsciiEscaping = 1 << 24,              // When synthesizing nodes that lack an original node or textSourceNode, we want to write the text on the node with ASCII escaping substitutions.
    /*@internal*/ TypeScriptClassWrapper = 1 << 25, // The node is an IIFE class wrapper created by the ts transform.
    /*@internal*/ NeverApplyImportHelper = 1 << 26, // Indicates the node should never be wrapped with an import star helper (because, for example, it imports tslib itself)
}
```
### EmitNode
```ts
export interface EmitNode {
    annotatedNodes?: Node[];                 // Tracks Parse-tree nodes with EmitNodes for eventual cleanup.
    flags: EmitFlags;                        // Flags that customize emit
    leadingComments?: SynthesizedComment[];  // Synthesized leading comments
    trailingComments?: SynthesizedComment[]; // Synthesized trailing comments
    commentRange?: TextRange;                // The text range to use when emitting leading or trailing comments
    sourceMapRange?: SourceMapRange;         // The text range to use when emitting leading or trailing source mappings
    tokenSourceMapRanges?: (SourceMapRange | undefined)[]; // The text range to use when emitting source mappings for tokens
    constantValue?: string | number;         // The constant value of an expression
    externalHelpersModuleName?: Identifier;  // The local name for an imported helpers module
    externalHelpers?: boolean;
    helpers?: EmitHelper[];                  // Emit helpers for the node
    startsOnNewLine?: boolean;               // If the node should begin on a new line
}
```

### EmitHelper
```ts
export interface EmitHelper {
    readonly name: string;                                          // A unique name for this helper.
    readonly scoped: boolean;                                       // Indicates whether the helper MUST be emitted in the current scope.
    readonly text: string | ((node: EmitHelperUniqueNameCallback) => string);  // ES3-compatible raw script text, or a function yielding such a string
    readonly priority?: number;                                     // Helpers with a higher priority are emitted earlier than other helpers on the node.
}
```

### UnscopedEmitHelper
```ts
export interface UnscopedEmitHelper extends EmitHelper {
    readonly scoped: false;                                         // Indicates whether the helper MUST be emitted in the current scope.
    /* @internal */
    readonly importName?: string;                                   // The name of the helper to use when importing via `--importHelpers`.
    readonly text: string;                                          // ES3-compatible raw script text, or a function yielding such a string
}
```

### UniqueNameHandler
```ts
export type UniqueNameHandler = (baseName: string, checkFn?: (name: string) => boolean, optimistic?: boolean) => string;
```

### EmitHelperUniqueNameCallback
```ts
export type EmitHelperUniqueNameCallback = (name: string) => string;
```

### ExternalEmitHelpers
```ts
export const enum ExternalEmitHelpers {
    Extends = 1 << 0,               // __extends (used by the ES2015 class transformation)
    Assign = 1 << 1,                // __assign (used by Jsx and ESNext object spread transformations)
    Rest = 1 << 2,                  // __rest (used by ESNext object rest transformation)
    Decorate = 1 << 3,              // __decorate (used by TypeScript decorators transformation)
    Metadata = 1 << 4,              // __metadata (used by TypeScript decorators transformation)
    Param = 1 << 5,                 // __param (used by TypeScript decorators transformation)
    Awaiter = 1 << 6,               // __awaiter (used by ES2017 async functions transformation)
    Generator = 1 << 7,             // __generator (used by ES2015 generator transformation)
    Values = 1 << 8,                // __values (used by ES2015 for..of and yield* transformations)
    Read = 1 << 9,                  // __read (used by ES2015 iterator destructuring transformation)
    Spread = 1 << 10,               // __spread (used by ES2015 array spread and argument list spread transformations)
    SpreadArrays = 1 << 11,         // __spreadArrays (used by ES2015 array spread and argument list spread transformations)
    Await = 1 << 12,                // __await (used by ES2017 async generator transformation)
    AsyncGenerator = 1 << 13,       // __asyncGenerator (used by ES2017 async generator transformation)
    AsyncDelegator = 1 << 14,       // __asyncDelegator (used by ES2017 async generator yield* transformation)
    AsyncValues = 1 << 15,          // __asyncValues (used by ES2017 for..await..of transformation)
    ExportStar = 1 << 16,           // __exportStar (used by CommonJS/AMD/UMD module transformation)
    MakeTemplateObject = 1 << 17,   // __makeTemplateObject (used for constructing template string array objects)
    FirstEmitHelper = Extends,
    LastEmitHelper = MakeTemplateObject,

    // Helpers included by ES2015 for..of
    ForOfIncludes = Values,

    // Helpers included by ES2017 for..await..of
    ForAwaitOfIncludes = AsyncValues,

    // Helpers included by ES2017 async generators
    AsyncGeneratorIncludes = Await | AsyncGenerator,

    // Helpers included by yield* in ES2017 async generators
    AsyncDelegatorIncludes = Await | AsyncDelegator | AsyncValues,

    // Helpers included by ES2015 spread
    SpreadIncludes = Read | Spread,
}
```

### EmitHint
```ts
export const enum EmitHint {
    SourceFile,          // Emitting a SourceFile
    Expression,          // Emitting an Expression
    IdentifierName,      // Emitting an IdentifierName
    MappedTypeParameter, // Emitting a TypeParameterDeclaration inside of a MappedTypeNode
    Unspecified,         // Emitting an otherwise unspecified node
    EmbeddedStatement,   // Emitting an embedded statement
}
```

### EmitHost
```ts
export interface EmitHost extends ScriptReferenceHost, ModuleSpecifierResolutionHost {
    getSourceFiles(): readonly SourceFile[];
    useCaseSensitiveFileNames(): boolean;
    getCurrentDirectory(): string;

    isSourceFileFromExternalLibrary(file: SourceFile): boolean;
    getResolvedProjectReferenceToRedirect(fileName: string): ResolvedProjectReference | undefined;
    getLibFileFromReference(ref: FileReference): SourceFile | undefined;

    getCommonSourceDirectory(): string;
    getCanonicalFileName(fileName: string): string;
    getNewLine(): string;

    isEmitBlocked(emitFileName: string): boolean;

    getPrependNodes(): readonly (InputFiles | UnparsedSource)[];

    writeFile: WriteFileCallback;
    getProgramBuildInfo(): ProgramBuildInfo | undefined;
    getSourceFileFromReference: Program["getSourceFileFromReference"];
    readonly redirectTargetsMap: RedirectTargetsMap;
}
```

### TransformationContext
```ts
export interface TransformationContext {
    /*@internal*/ getEmitResolver(): EmitResolver;
    /*@internal*/ getEmitHost(): EmitHost;

    /** Gets the compiler options supplied to the transformer. */
    getCompilerOptions(): CompilerOptions;

    /** Starts a new lexical environment. */
    startLexicalEnvironment(): void;

    /** Suspends the current lexical environment, usually after visiting a parameter list. */
    suspendLexicalEnvironment(): void;

    /** Resumes a suspended lexical environment, usually before visiting a function body. */
    resumeLexicalEnvironment(): void;

    /** Ends a lexical environment, returning any declarations. */
    endLexicalEnvironment(): Statement[] | undefined;

    /** Hoists a function declaration to the containing scope. */
    hoistFunctionDeclaration(node: FunctionDeclaration): void;

    /** Hoists a variable declaration to the containing scope. */
    hoistVariableDeclaration(node: Identifier): void;

    /** Records a request for a non-scoped emit helper in the current context. */
    requestEmitHelper(helper: EmitHelper): void;

    /** Gets and resets the requested non-scoped emit helpers. */
    readEmitHelpers(): EmitHelper[] | undefined;

    /** Enables expression substitutions in the pretty printer for the provided SyntaxKind. */
    enableSubstitution(kind: SyntaxKind): void;

    /** Determines whether expression substitutions are enabled for the provided node. */
    isSubstitutionEnabled(node: Node): boolean;

    /**
        * Hook used by transformers to substitute expressions just before they
        * are emitted by the pretty printer.
        *
        * NOTE: Transformation hooks should only be modified during `Transformer` initialization,
        * before returning the `NodeTransformer` callback.
        */
    onSubstituteNode: (hint: EmitHint, node: Node) => Node;

    /**
        * Enables before/after emit notifications in the pretty printer for the provided
        * SyntaxKind.
        */
    enableEmitNotification(kind: SyntaxKind): void;

    /**
        * Determines whether before/after emit notifications should be raised in the pretty
        * printer when it emits a node.
        */
    isEmitNotificationEnabled(node: Node): boolean;

    /**
        * Hook used to allow transformers to capture state before or after
        * the printer emits a node.
        *
        * NOTE: Transformation hooks should only be modified during `Transformer` initialization,
        * before returning the `NodeTransformer` callback.
        */
    onEmitNode: (hint: EmitHint, node: Node, emitCallback: (hint: EmitHint, node: Node) => void) => void;

    /* @internal */ addDiagnostic(diag: DiagnosticWithLocation): void;
}
```

### TransformationResult
```ts
export interface TransformationResult<T extends Node> {
    
    transformed: T[];
    diagnostics?: DiagnosticWithLocation[];
    substituteNode(hint: EmitHint, node: Node): Node;
    emitNodeWithNotification(hint: EmitHint, node: Node, emitCallback: (hint: EmitHint, node: Node) => void): void;
    dispose(): void;
}
```

### TransformerFactory
```ts
export type TransformerFactory<T extends Node> = (context: TransformationContext) => Transformer<T>;
```

### Transformer
```ts
export type Transformer<T extends Node> = (node: T) => T;
```

### Visitor
```ts
export type Visitor = (node: Node) => VisitResult<Node>;
```

### VisitResult
```ts
export type VisitResult<T extends Node> = T | T[] | undefined;
```

### Printer
```ts
export interface Printer {
    printNode(hint: EmitHint, node: Node, sourceFile: SourceFile): string;
    
    printList<T extends Node>(format: ListFormat, list: NodeArray<T>, sourceFile: SourceFile): string;
    
    printFile(sourceFile: SourceFile): string;
    
    printBundle(bundle: Bundle): string;
    /*@internal*/ writeNode(hint: EmitHint, node: Node, sourceFile: SourceFile | undefined, writer: EmitTextWriter): void;
    /*@internal*/ writeList<T extends Node>(format: ListFormat, list: NodeArray<T> | undefined, sourceFile: SourceFile | undefined, writer: EmitTextWriter): void;
    /*@internal*/ writeFile(sourceFile: SourceFile, writer: EmitTextWriter, sourceMapGenerator: SourceMapGenerator | undefined): void;
    /*@internal*/ writeBundle(bundle: Bundle, writer: EmitTextWriter, sourceMapGenerator: SourceMapGenerator | undefined): void;
    /*@internal*/ bundleFileInfo?: BundleFileInfo;
}
```

### BundleFileSectionKind
```ts
export const enum BundleFileSectionKind {
    Prologue = "prologue",
    EmitHelpers = "emitHelpers",
    NoDefaultLib = "no-default-lib",
    Reference = "reference",
    Type = "type",
    Lib = "lib",
    Prepend = "prepend",
    Text = "text",
    Internal = "internal",
    // comments?
}
```

### BundleFileSectionBase
```ts
export interface BundleFileSectionBase extends TextRange {
    kind: BundleFileSectionKind;
    data?: string;
}
```

### BundleFilePrologue
```ts
export interface BundleFilePrologue extends BundleFileSectionBase {
    kind: BundleFileSectionKind.Prologue;
    data: string;
}
```

### BundleFileEmitHelpers
```ts
export interface BundleFileEmitHelpers extends BundleFileSectionBase {
    kind: BundleFileSectionKind.EmitHelpers;
    data: string;
}
```

### BundleFileHasNoDefaultLib
```ts
export interface BundleFileHasNoDefaultLib extends BundleFileSectionBase {
    kind: BundleFileSectionKind.NoDefaultLib;
}
```

### BundleFileReference
```ts
export interface BundleFileReference extends BundleFileSectionBase {
    kind: BundleFileSectionKind.Reference | BundleFileSectionKind.Type | BundleFileSectionKind.Lib;
    data: string;
}
```

### BundleFilePrepend
```ts
export interface BundleFilePrepend extends BundleFileSectionBase {
    kind: BundleFileSectionKind.Prepend;
    data: string;
    texts: BundleFileTextLike[];
}
```

### BundleFileTextLikeKind
```ts
export type BundleFileTextLikeKind 
    = BundleFileSectionKind.Text 
    | BundleFileSectionKind.Internal
    ;
```

### BundleFileTextLike
```ts
export interface BundleFileTextLike extends BundleFileSectionBase {
    kind: BundleFileTextLikeKind;
}
```

### BundleFileSection
```ts
export type BundleFileSection =
    BundleFilePrologue
    | BundleFileEmitHelpers
    | BundleFileHasNoDefaultLib
    | BundleFileReference
    | BundleFilePrepend
    | BundleFileTextLike;
```

### BundleFileSection
```ts
export type BundleFileSection =
    BundleFilePrologue
    | BundleFileEmitHelpers
    | BundleFileHasNoDefaultLib
    | BundleFileReference
    | BundleFilePrepend
    | BundleFileTextLike;
```

### SourceFilePrologueDirectiveExpression
```ts
export interface SourceFilePrologueDirectiveExpression extends TextRange {
    text: string;
}
```

### SourceFilePrologueDirective
```ts
export interface SourceFilePrologueDirective extends TextRange {
    expression: SourceFilePrologueDirectiveExpression;
}
```

### SourceFilePrologueInfo
```ts
export interface SourceFilePrologueInfo {
    file: number;
    text: string;
    directives: SourceFilePrologueDirective[];
}
```

### SourceFileInfo
```ts
export interface SourceFileInfo {
    // List of helpers in own source files emitted if no prepend is present
    helpers?: string[];
    prologues?: SourceFilePrologueInfo[];
}
```

### BundleFileInfo
```ts
export interface BundleFileInfo {
    sections: BundleFileSection[];
    sources?: SourceFileInfo;
}
```

### BundleBuildInfo
```ts
export interface BundleBuildInfo {
    js?: BundleFileInfo;
    dts?: BundleFileInfo;
    commonSourceDirectory: string;
    sourceFiles: readonly string[];
}
```

### BuildInfo
```ts
export interface BuildInfo {
    bundle?: BundleBuildInfo;
    program?: ProgramBuildInfo;
    version: string;
}
```

### PrintHandlers
```ts
export interface PrintHandlers {
    hasGlobalName?(name: string): boolean;
    onEmitNode?(hint: EmitHint, node: Node | undefined, emitCallback: (hint: EmitHint, node: Node | undefined) => void): void;
    substituteNode?(hint: EmitHint, node: Node): Node;
    /*@internal*/ onEmitSourceMapOfNode?: (hint: EmitHint, node: Node, emitCallback: (hint: EmitHint, node: Node) => void) => void;
    /*@internal*/ onEmitSourceMapOfToken?: (node: Node | undefined, token: SyntaxKind, writer: (s: string) => void, pos: number, emitCallback: (token: SyntaxKind, writer: (s: string) => void, pos: number) => number) => number;
    /*@internal*/ onEmitSourceMapOfPosition?: (pos: number) => void;
    /*@internal*/ onSetSourceFile?: (node: SourceFile) => void;
    /*@internal*/ onBeforeEmitNodeArray?: (nodes: NodeArray<any> | undefined) => void;
    /*@internal*/ onAfterEmitNodeArray?: (nodes: NodeArray<any> | undefined) => void;
    /*@internal*/ onBeforeEmitToken?: (node: Node) => void;
    /*@internal*/ onAfterEmitToken?: (node: Node) => void;
}
```

### PrinterOptions
```ts
export interface PrinterOptions {
    removeComments?: boolean;
    newLine?: NewLineKind;
    omitTrailingSemicolon?: boolean;
    noEmitHelpers?: boolean;
    /*@internal*/ module?: CompilerOptions["module"];
    /*@internal*/ target?: CompilerOptions["target"];
    /*@internal*/ sourceMap?: boolean;
    /*@internal*/ inlineSourceMap?: boolean;
    /*@internal*/ inlineSources?: boolean;
    /*@internal*/ extendedDiagnostics?: boolean;
    /*@internal*/ onlyPrintJsDocStyle?: boolean;
    /*@internal*/ neverAsciiEscape?: boolean;
    /*@internal*/ writeBundleFileInfo?: boolean;
    /*@internal*/ recordInternalSection?: boolean;
    /*@internal*/ stripInternal?: boolean;
    /*@internal*/ relativeToBuildInfo?: (path: string) => string;
}
```

### RawSourceMap
```ts
export interface RawSourceMap {
    version: 3;
    file: string;
    sourceRoot?: string | null;
    sources: string[];
    sourcesContent?: (string | null)[] | null;
    mappings: string;
    names?: string[] | null;
}
```
### SourceMapGenerator
```ts
export interface SourceMapGenerator {
    getSources(): readonly string[];
    /**
        * Adds a source to the source map.
        */
    addSource(fileName: string): number;
    /**
        * Set the content for a source.
        */
    setSourceContent(sourceIndex: number, content: string | null): void;
    /**
        * Adds a name.
        */
    addName(name: string): number;
    /**
        * Adds a mapping without source information.
        */
    addMapping(generatedLine: number, generatedCharacter: number): void;
    /**
        * Adds a mapping with source information.
        */
    addMapping(generatedLine: number, generatedCharacter: number, sourceIndex: number, sourceLine: number, sourceCharacter: number, nameIndex?: number): void;
    /**
        * Appends a source map.
        */
    appendSourceMap(generatedLine: number, generatedCharacter: number, sourceMap: RawSourceMap, sourceMapPath: string, start?: LineAndCharacter, end?: LineAndCharacter): void;
    /**
        * Gets the source map as a `RawSourceMap` object.
        */
    toJSON(): RawSourceMap;
    /**
        * Gets the string representation of the source map.
        */
    toString(): string;
}
```

### DocumentPositionMapperHost
```ts
export interface DocumentPositionMapperHost {
    getSourceFileLike(fileName: string): SourceFileLike | undefined;
    getCanonicalFileName(path: string): string;
    log(text: string): void;
}
```

### DocumentPositionMapper
```ts
export interface DocumentPositionMapper {
    getSourcePosition(input: DocumentPosition): DocumentPosition;
    getGeneratedPosition(input: DocumentPosition): DocumentPosition;
}
```

### DocumentPosition
```ts
export interface DocumentPosition {
    fileName: string;
    pos: number;
}
```

### EmitTextWriter
```ts
export interface EmitTextWriter extends SymbolWriter {
    write(s: string): void;
    writeTrailingSemicolon(text: string): void;
    writeComment(text: string): void;
    getText(): string;
    rawWrite(s: string): void;
    writeLiteral(s: string): void;
    getTextPos(): number;
    getLine(): number;
    getColumn(): number;
    getIndent(): number;
    isAtStartOfLine(): boolean;
    hasTrailingComment(): boolean;
    hasTrailingWhitespace(): boolean;
    getTextPosWithWriteLine?(): number;
}
```

### GetEffectiveTypeRootsHost
```ts
export interface GetEffectiveTypeRootsHost {
    directoryExists?(directoryName: string): boolean;
    getCurrentDirectory?(): string;
}
```

### ModuleSpecifierResolutionHost
```ts
export interface ModuleSpecifierResolutionHost extends GetEffectiveTypeRootsHost {
    useCaseSensitiveFileNames?(): boolean;
    fileExists?(path: string): boolean;
    readFile?(path: string): string | undefined;
    /* @internal */
    getProbableSymlinks?(files: readonly SourceFile[]): ReadonlyMap<string>;
}
```

### SymbolTracker
```ts
export interface SymbolTracker {
    trackSymbol?(symbol: Symbol, enclosingDeclaration: Node | undefined, meaning: SymbolFlags): void;
    reportInaccessibleThisError?(): void;
    reportPrivateInBaseOfClassExpression?(propertyName: string): void;
    reportInaccessibleUniqueSymbolError?(): void;
    reportLikelyUnsafeImportRequiredError?(specifier: string): void;
    moduleResolverHost?: ModuleSpecifierResolutionHost & { getSourceFiles(): readonly SourceFile[], getCommonSourceDirectory(): string };
    trackReferencedAmbientModule?(decl: ModuleDeclaration, symbol: Symbol): void;
    trackExternalModuleSymbolOfImportTypeNode?(symbol: Symbol): void;
}
```

### TextSpan
```ts
export interface TextSpan {
    start: number;
    length: number;
}
```

### TextChangeRange
```ts
export interface TextChangeRange {
    span: TextSpan;
    newLength: number;
}
```

### ListFormat
```ts
export const enum ListFormat {
    None = 0,

    // Line separators
    SingleLine = 0,                 // Prints the list on a single line (default).
    MultiLine = 1 << 0,             // Prints the list on multiple lines.
    PreserveLines = 1 << 1,         // Prints the list using line preservation if possible.
    LinesMask = SingleLine | MultiLine | PreserveLines,

    // Delimiters
    NotDelimited = 0,               // There is no delimiter between list items (default).
    BarDelimited = 1 << 2,          // Each list item is space-and-bar (" |") delimited.
    AmpersandDelimited = 1 << 3,    // Each list item is space-and-ampersand (" &") delimited.
    CommaDelimited = 1 << 4,        // Each list item is comma (",") delimited.
    AsteriskDelimited = 1 << 5,     // Each list item is asterisk ("\n *") delimited, used with JSDoc.
    DelimitersMask = BarDelimited | AmpersandDelimited | CommaDelimited | AsteriskDelimited,

    AllowTrailingComma = 1 << 6,    // Write a trailing comma (",") if present.

    // Whitespace
    Indented = 1 << 7,              // The list should be indented.
    SpaceBetweenBraces = 1 << 8,    // Inserts a space after the opening brace and before the closing brace.
    SpaceBetweenSiblings = 1 << 9,  // Inserts a space between each sibling node.

    // Brackets/Braces
    Braces = 1 << 10,                // The list is surrounded by "{" and "}".
    Parenthesis = 1 << 11,          // The list is surrounded by "(" and ")".
    AngleBrackets = 1 << 12,        // The list is surrounded by "<" and ">".
    SquareBrackets = 1 << 13,       // The list is surrounded by "[" and "]".
    BracketsMask = Braces | Parenthesis | AngleBrackets | SquareBrackets,

    OptionalIfUndefined = 1 << 14,  // Do not emit brackets if the list is undefined.
    OptionalIfEmpty = 1 << 15,      // Do not emit brackets if the list is empty.
    Optional = OptionalIfUndefined | OptionalIfEmpty,

    // Other
    PreferNewLine = 1 << 16,        // Prefer adding a LineTerminator between synthesized nodes.
    NoTrailingNewLine = 1 << 17,    // Do not emit a trailing NewLine for a MultiLine list.
    NoInterveningComments = 1 << 18, // Do not emit comments between each node

    NoSpaceIfEmpty = 1 << 19,       // If the literal is empty, do not add spaces between braces.
    SingleElement = 1 << 20,

    // Precomputed Formats
    Modifiers = SingleLine | SpaceBetweenSiblings | NoInterveningComments,
    HeritageClauses = SingleLine | SpaceBetweenSiblings,
    SingleLineTypeLiteralMembers = SingleLine | SpaceBetweenBraces | SpaceBetweenSiblings,
    MultiLineTypeLiteralMembers = MultiLine | Indented | OptionalIfEmpty,

    TupleTypeElements = CommaDelimited | SpaceBetweenSiblings | SingleLine,
    UnionTypeConstituents = BarDelimited | SpaceBetweenSiblings | SingleLine,
    IntersectionTypeConstituents = AmpersandDelimited | SpaceBetweenSiblings | SingleLine,
    ObjectBindingPatternElements = SingleLine | AllowTrailingComma | SpaceBetweenBraces | CommaDelimited | SpaceBetweenSiblings | NoSpaceIfEmpty,
    ArrayBindingPatternElements = SingleLine | AllowTrailingComma | CommaDelimited | SpaceBetweenSiblings | NoSpaceIfEmpty,
    ObjectLiteralExpressionProperties = PreserveLines | CommaDelimited | SpaceBetweenSiblings | SpaceBetweenBraces | Indented | Braces | NoSpaceIfEmpty,
    ArrayLiteralExpressionElements = PreserveLines | CommaDelimited | SpaceBetweenSiblings | AllowTrailingComma | Indented | SquareBrackets,
    CommaListElements = CommaDelimited | SpaceBetweenSiblings | SingleLine,
    CallExpressionArguments = CommaDelimited | SpaceBetweenSiblings | SingleLine | Parenthesis,
    NewExpressionArguments = CommaDelimited | SpaceBetweenSiblings | SingleLine | Parenthesis | OptionalIfUndefined,
    TemplateExpressionSpans = SingleLine | NoInterveningComments,
    SingleLineBlockStatements = SpaceBetweenBraces | SpaceBetweenSiblings | SingleLine,
    MultiLineBlockStatements = Indented | MultiLine,
    VariableDeclarationList = CommaDelimited | SpaceBetweenSiblings | SingleLine,
    SingleLineFunctionBodyStatements = SingleLine | SpaceBetweenSiblings | SpaceBetweenBraces,
    MultiLineFunctionBodyStatements = MultiLine,
    ClassHeritageClauses = SingleLine,
    ClassMembers = Indented | MultiLine,
    InterfaceMembers = Indented | MultiLine,
    EnumMembers = CommaDelimited | Indented | MultiLine,
    CaseBlockClauses = Indented | MultiLine,
    NamedImportsOrExportsElements = CommaDelimited | SpaceBetweenSiblings | AllowTrailingComma | SingleLine | SpaceBetweenBraces | NoSpaceIfEmpty,
    JsxElementOrFragmentChildren = SingleLine | NoInterveningComments,
    JsxElementAttributes = SingleLine | SpaceBetweenSiblings | NoInterveningComments,
    CaseOrDefaultClauseStatements = Indented | MultiLine | NoTrailingNewLine | OptionalIfEmpty,
    HeritageClauseTypes = CommaDelimited | SpaceBetweenSiblings | SingleLine,
    SourceFileStatements = MultiLine | NoTrailingNewLine,
    Decorators = MultiLine | Optional,
    TypeArguments = CommaDelimited | SpaceBetweenSiblings | SingleLine | AngleBrackets | Optional,
    TypeParameters = CommaDelimited | SpaceBetweenSiblings | SingleLine | AngleBrackets | Optional,
    Parameters = CommaDelimited | SpaceBetweenSiblings | SingleLine | Parenthesis,
    IndexSignatureParameters = CommaDelimited | SpaceBetweenSiblings | SingleLine | Indented | SquareBrackets,
    JSDocComment = MultiLine | AsteriskDelimited,
}
```

### PragmaKindFlags
```ts
export const enum PragmaKindFlags {
    None            =      0,
    TripleSlashXML  = 1 << 0,
    SingleLine      = 1 << 1,
    MultiLine       = 1 << 2,
    All = TripleSlashXML | SingleLine | MultiLine,
    Default = All,
}
```

### PragmaArgumentSpecification
```ts
interface PragmaArgumentSpecification<TName extends string> {
    name: TName; 
    optional?: boolean;
    captureSpan?: boolean;
}
```

### PragmaDefinition
```ts
export interface PragmaDefinition<T1 extends string = string, T2 extends string = string, T3 extends string = string, T4 extends string = string> {
    args?:
        | readonly [PragmaArgumentSpecification<T1>]
        | readonly [PragmaArgumentSpecification<T1>, PragmaArgumentSpecification<T2>]
        | readonly [PragmaArgumentSpecification<T1>, PragmaArgumentSpecification<T2>, PragmaArgumentSpecification<T3>]
        | readonly [PragmaArgumentSpecification<T1>, PragmaArgumentSpecification<T2>, PragmaArgumentSpecification<T3>, PragmaArgumentSpecification<T4>];
    // If not present, defaults to PragmaKindFlags.Default
    kind?: PragmaKindFlags;
}
```

### commentPragmas
```ts
export const commentPragmas = {
    "reference": {
        args: [
            { name: "types", optional: true, captureSpan: true },
            { name: "lib", optional: true, captureSpan: true },
            { name: "path", optional: true, captureSpan: true },
            { name: "no-default-lib", optional: true }
        ],
        kind: PragmaKindFlags.TripleSlashXML
    },
    "amd-dependency": {
        args: [{ name: "path" }, { name: "name", optional: true }],
        kind: PragmaKindFlags.TripleSlashXML
    },
    "amd-module": {
        args: [{ name: "name" }],
        kind: PragmaKindFlags.TripleSlashXML
    },
    "ts-check": {
        kind: PragmaKindFlags.SingleLine
    },
    "ts-nocheck": {
        kind: PragmaKindFlags.SingleLine
    },
    "jsx": {
        args: [{ name: "factory" }],
        kind: PragmaKindFlags.MultiLine
    },
} as const;
```

### PragmaArgTypeMaybeCapture
```ts
type PragmaArgTypeMaybeCapture<TDesc> 
    = TDesc extends {captureSpan: true} ? {value: string, pos: number, end: number} : string;
```

### PragmaArgTypeOptional
```ts
type PragmaArgTypeOptional<TDesc, TName extends string> =
    TDesc extends {optional: true}
        ? {[K in TName]?: PragmaArgTypeMaybeCapture<TDesc>}
        : {[K in TName]: PragmaArgTypeMaybeCapture<TDesc>};
```

### UnionToIntersection
```ts
type UnionToIntersection<U> =
            (U extends any 
                ? (k: U) => void 
                : never) 
        extends (
            (k: infer I) => void) 
                ? I 
                : never;
```

### ArgumentDefinitionToFieldUnion
```ts
 type ArgumentDefinitionToFieldUnion<T extends readonly PragmaArgumentSpecification<any>[]> = {
        [K in keyof T]: PragmaArgTypeOptional<T[K], T[K] extends {name: infer TName} ? TName extends string ? TName : never : never>
    }[Extract<keyof T, number>]; 
```

### PragmaArgumentType
```ts
type PragmaArgumentType<KPrag extends keyof ConcretePragmaSpecs> =
        ConcretePragmaSpecs[KPrag] extends { args: readonly PragmaArgumentSpecification<any>[] }
            ? UnionToIntersection<ArgumentDefinitionToFieldUnion<ConcretePragmaSpecs[KPrag]["args"]>>
            : never;
```

### ConcretePragmaSpecs
```ts
type ConcretePragmaSpecs = typeof commentPragmas;
```

### PragmaPseudoMap
```ts
export type PragmaPseudoMap = {[K in keyof ConcretePragmaSpecs]: {arguments: PragmaArgumentType<K>, range: CommentRange}};
```

### PragmaPseudoMapEntry
```ts
export type PragmaPseudoMapEntry = {[K in keyof PragmaPseudoMap]: {name: K, args: PragmaPseudoMap[K]}}[keyof PragmaPseudoMap];
```

### ReadonlyPragmaMap
```ts
export interface ReadonlyPragmaMap extends ReadonlyMap<PragmaPseudoMap[keyof PragmaPseudoMap] | PragmaPseudoMap[keyof PragmaPseudoMap][]> {
    get<TKey extends keyof PragmaPseudoMap>(key: TKey): PragmaPseudoMap[TKey] | PragmaPseudoMap[TKey][];

    forEach(action: <TKey extends keyof PragmaPseudoMap>(value: PragmaPseudoMap[TKey] | PragmaPseudoMap[TKey][], key: TKey) => void): void;
}
```

### PragmaMap
```ts
export interface PragmaMap extends Map<PragmaPseudoMap[keyof PragmaPseudoMap] | PragmaPseudoMap[keyof PragmaPseudoMap][]>, ReadonlyPragmaMap {
    set<TKey extends keyof PragmaPseudoMap>(key: TKey, value: PragmaPseudoMap[TKey] | PragmaPseudoMap[TKey][]): this;
    get<TKey extends keyof PragmaPseudoMap>(key: TKey): PragmaPseudoMap[TKey] | PragmaPseudoMap[TKey][];
    forEach(action: <TKey extends keyof PragmaPseudoMap>(value: PragmaPseudoMap[TKey] | PragmaPseudoMap[TKey][], key: TKey) => void): void;
}
```

### UserPreferences
```ts
export interface UserPreferences {
    readonly disableSuggestions?: boolean;
    readonly quotePreference?: "auto" | "double" | "single";
    readonly includeCompletionsForModuleExports?: boolean;
    readonly includeCompletionsWithInsertText?: boolean;
    readonly importModuleSpecifierPreference?: "relative" | "non-relative";
    /** Determines whether we import `foo/index.ts` as "foo", "foo/index", or "foo/index.js" */
    readonly importModuleSpecifierEnding?: "minimal" | "index" | "js";
    readonly allowTextChangesInNewFiles?: boolean;
    readonly providePrefixAndSuffixTextForRename?: boolean;
}
```

### PseudoBigInt
```ts
export interface PseudoBigInt {
    negative: boolean;
    base10Value: string;
}
```