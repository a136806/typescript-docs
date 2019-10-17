## 功能说明

## 类型接口
### IterationUse
```ts
const enum IterationUse {
    AllowsSyncIterablesFlag = 1 << 0,
    AllowsAsyncIterablesFlag = 1 << 1,
    AllowsStringInputFlag = 1 << 2,
    ForOfFlag = 1 << 3,
    YieldStarFlag = 1 << 4,
    SpreadFlag = 1 << 5,
    DestructuringFlag = 1 << 6,

    // Spread, Destructuring, Array element assignment
    Element = AllowsSyncIterablesFlag,
    Spread = AllowsSyncIterablesFlag | SpreadFlag,
    Destructuring = AllowsSyncIterablesFlag | DestructuringFlag,

    ForOf = AllowsSyncIterablesFlag | AllowsStringInputFlag | ForOfFlag,
    ForAwaitOf = AllowsSyncIterablesFlag | AllowsAsyncIterablesFlag | AllowsStringInputFlag | ForOfFlag,

    YieldStar = AllowsSyncIterablesFlag | YieldStarFlag,
    AsyncYieldStar = AllowsSyncIterablesFlag | AllowsAsyncIterablesFlag | YieldStarFlag,

    GeneratorReturnType = AllowsSyncIterablesFlag,
    AsyncGeneratorReturnType = AllowsAsyncIterablesFlag,

}
```

### IterationTypeKind
```ts
const enum IterationTypeKind {
    Yield,
    Return,
    Next,
}
```

### IterationTypesResolver
```ts
interface IterationTypesResolver {
    iterableCacheKey: "iterationTypesOfAsyncIterable" | "iterationTypesOfIterable";
    iteratorCacheKey: "iterationTypesOfAsyncIterator" | "iterationTypesOfIterator";
    iteratorSymbolName: "asyncIterator" | "iterator";
    getGlobalIteratorType: (reportErrors: boolean) => GenericType;
    getGlobalIterableType: (reportErrors: boolean) => GenericType;
    getGlobalIterableIteratorType: (reportErrors: boolean) => GenericType;
    getGlobalGeneratorType: (reportErrors: boolean) => GenericType;
    resolveIterationType: (type: Type, errorNode: Node | undefined) => Type | undefined;
    mustHaveANextMethodDiagnostic: DiagnosticMessage;
    mustBeAMethodDiagnostic: DiagnosticMessage;
    mustHaveAValueDiagnostic: DiagnosticMessage;
}
```

### WideningKind
```ts
const enum WideningKind {
    Normal,
    GeneratorYield
}
```

### TypeFacts
```ts
const enum TypeFacts {
    None = 0,
    TypeofEQString = 1 << 0,      // typeof x === "string"
    TypeofEQNumber = 1 << 1,      // typeof x === "number"
    TypeofEQBigInt = 1 << 2,      // typeof x === "bigint"
    TypeofEQBoolean = 1 << 3,     // typeof x === "boolean"
    TypeofEQSymbol = 1 << 4,      // typeof x === "symbol"
    TypeofEQObject = 1 << 5,      // typeof x === "object"
    TypeofEQFunction = 1 << 6,    // typeof x === "function"
    TypeofEQHostObject = 1 << 7,  // typeof x === "xxx"
    TypeofNEString = 1 << 8,      // typeof x !== "string"
    TypeofNENumber = 1 << 9,      // typeof x !== "number"
    TypeofNEBigInt = 1 << 10,     // typeof x !== "bigint"
    TypeofNEBoolean = 1 << 11,     // typeof x !== "boolean"
    TypeofNESymbol = 1 << 12,     // typeof x !== "symbol"
    TypeofNEObject = 1 << 13,     // typeof x !== "object"
    TypeofNEFunction = 1 << 14,   // typeof x !== "function"
    TypeofNEHostObject = 1 << 15, // typeof x !== "xxx"
    EQUndefined = 1 << 16,        // x === undefined
    EQNull = 1 << 17,             // x === null
    EQUndefinedOrNull = 1 << 18,  // x === undefined / x === null
    NEUndefined = 1 << 19,        // x !== undefined
    NENull = 1 << 20,             // x !== null
    NEUndefinedOrNull = 1 << 21,  // x != undefined / x != null
    Truthy = 1 << 22,             // x
    Falsy = 1 << 23,              // !x
    All = (1 << 24) - 1,
    // The following members encode facts about particular kinds of types for use in the getTypeFacts function.
    // The presence of a particular fact means that the given test is true for some (and possibly all) values
    // of that kind of type.
    BaseStringStrictFacts = TypeofEQString | TypeofNENumber | TypeofNEBigInt | TypeofNEBoolean | TypeofNESymbol | TypeofNEObject | TypeofNEFunction | TypeofNEHostObject | NEUndefined | NENull | NEUndefinedOrNull,
    BaseStringFacts = BaseStringStrictFacts | EQUndefined | EQNull | EQUndefinedOrNull | Falsy,
    StringStrictFacts = BaseStringStrictFacts | Truthy | Falsy,
    StringFacts = BaseStringFacts | Truthy,
    EmptyStringStrictFacts = BaseStringStrictFacts | Falsy,
    EmptyStringFacts = BaseStringFacts,
    NonEmptyStringStrictFacts = BaseStringStrictFacts | Truthy,
    NonEmptyStringFacts = BaseStringFacts | Truthy,
    BaseNumberStrictFacts = TypeofEQNumber | TypeofNEString | TypeofNEBigInt | TypeofNEBoolean | TypeofNESymbol | TypeofNEObject | TypeofNEFunction | TypeofNEHostObject | NEUndefined | NENull | NEUndefinedOrNull,
    BaseNumberFacts = BaseNumberStrictFacts | EQUndefined | EQNull | EQUndefinedOrNull | Falsy,
    NumberStrictFacts = BaseNumberStrictFacts | Truthy | Falsy,
    NumberFacts = BaseNumberFacts | Truthy,
    ZeroNumberStrictFacts = BaseNumberStrictFacts | Falsy,
    ZeroNumberFacts = BaseNumberFacts,
    NonZeroNumberStrictFacts = BaseNumberStrictFacts | Truthy,
    NonZeroNumberFacts = BaseNumberFacts | Truthy,
    BaseBigIntStrictFacts = TypeofEQBigInt | TypeofNEString | TypeofNENumber | TypeofNEBoolean | TypeofNESymbol | TypeofNEObject | TypeofNEFunction | TypeofNEHostObject | NEUndefined | NENull | NEUndefinedOrNull,
    BaseBigIntFacts = BaseBigIntStrictFacts | EQUndefined | EQNull | EQUndefinedOrNull | Falsy,
    BigIntStrictFacts = BaseBigIntStrictFacts | Truthy | Falsy,
    BigIntFacts = BaseBigIntFacts | Truthy,
    ZeroBigIntStrictFacts = BaseBigIntStrictFacts | Falsy,
    ZeroBigIntFacts = BaseBigIntFacts,
    NonZeroBigIntStrictFacts = BaseBigIntStrictFacts | Truthy,
    NonZeroBigIntFacts = BaseBigIntFacts | Truthy,
    BaseBooleanStrictFacts = TypeofEQBoolean | TypeofNEString | TypeofNENumber | TypeofNEBigInt | TypeofNESymbol | TypeofNEObject | TypeofNEFunction | TypeofNEHostObject | NEUndefined | NENull | NEUndefinedOrNull,
    BaseBooleanFacts = BaseBooleanStrictFacts | EQUndefined | EQNull | EQUndefinedOrNull | Falsy,
    BooleanStrictFacts = BaseBooleanStrictFacts | Truthy | Falsy,
    BooleanFacts = BaseBooleanFacts | Truthy,
    FalseStrictFacts = BaseBooleanStrictFacts | Falsy,
    FalseFacts = BaseBooleanFacts,
    TrueStrictFacts = BaseBooleanStrictFacts | Truthy,
    TrueFacts = BaseBooleanFacts | Truthy,
    SymbolStrictFacts = TypeofEQSymbol | TypeofNEString | TypeofNENumber | TypeofNEBigInt | TypeofNEBoolean | TypeofNEObject | TypeofNEFunction | TypeofNEHostObject | NEUndefined | NENull | NEUndefinedOrNull | Truthy,
    SymbolFacts = SymbolStrictFacts | EQUndefined | EQNull | EQUndefinedOrNull | Falsy,
    ObjectStrictFacts = TypeofEQObject | TypeofEQHostObject | TypeofNEString | TypeofNENumber | TypeofNEBigInt | TypeofNEBoolean | TypeofNESymbol | TypeofNEFunction | NEUndefined | NENull | NEUndefinedOrNull | Truthy,
    ObjectFacts = ObjectStrictFacts | EQUndefined | EQNull | EQUndefinedOrNull | Falsy,
    FunctionStrictFacts = TypeofEQFunction | TypeofEQHostObject | TypeofNEString | TypeofNENumber | TypeofNEBigInt | TypeofNEBoolean | TypeofNESymbol | TypeofNEObject | NEUndefined | NENull | NEUndefinedOrNull | Truthy,
    FunctionFacts = FunctionStrictFacts | EQUndefined | EQNull | EQUndefinedOrNull | Falsy,
    UndefinedFacts = TypeofNEString | TypeofNENumber | TypeofNEBigInt | TypeofNEBoolean | TypeofNESymbol | TypeofNEObject | TypeofNEFunction | TypeofNEHostObject | EQUndefined | EQUndefinedOrNull | NENull | Falsy,
    NullFacts = TypeofEQObject | TypeofNEString | TypeofNENumber | TypeofNEBigInt | TypeofNEBoolean | TypeofNESymbol | TypeofNEFunction | TypeofNEHostObject | EQNull | EQUndefinedOrNull | NEUndefined | Falsy,
    EmptyObjectStrictFacts = All & ~(EQUndefined | EQNull | EQUndefinedOrNull),
    EmptyObjectFacts = All,
}
```
### TypeSystemEntity
```ts
type TypeSystemEntity = Node | Symbol | Type | Signature;
```

### TypeSystemPropertyName
```ts
const enum TypeSystemPropertyName {
    Type,
    ResolvedBaseConstructorType,
    DeclaredType,
    ResolvedReturnType,
    ImmediateBaseConstraint,
    EnumTagType,
    JSDocTypeReference,
}
```
### CheckMode
```ts
const enum CheckMode {
    Normal = 0,                     // Normal type checking
    Contextual = 1 << 0,            // Explicitly assigned contextual type, therefore not cacheable
    Inferential = 1 << 1,           // Inferential typing
    SkipContextSensitive = 1 << 2,  // Skip context sensitive function expressions
    SkipGenericFunctions = 1 << 3,  // Skip single signature generic functions
    IsForSignatureHelp = 1 << 4,    // Call resolution for purposes of signature help
}
```

### ContextFlags
```ts
const enum ContextFlags {
    None = 0,
    Signature = 1 << 0,  // Obtaining contextual signature
    NoConstraints = 1 << 1,  // Don't obtain type variable constraints
}
```
### AccessFlags
```ts
const enum AccessFlags {
    None = 0,
    NoIndexSignatures = 1 << 0,
    Writing = 1 << 1,
    CacheSymbol = 1 << 2,
    NoTupleBoundsCheck = 1 << 3,
}
```
### CallbackCheck
```ts
const enum CallbackCheck {
    None,
    Bivariant,
    Strict,
}
```
### MappedTypeModifiers
```ts
const enum MappedTypeModifiers {
    IncludeReadonly = 1 << 0,
    ExcludeReadonly = 1 << 1,
    IncludeOptional = 1 << 2,
    ExcludeOptional = 1 << 3,
}
```
### ExpandingFlags
```ts
const enum ExpandingFlags {
    None = 0,
    Source = 1,
    Target = 1 << 1,
    Both = Source | Target,
}
```
### MembersOrExportsResolutionKind
```ts
const enum MembersOrExportsResolutionKind {
    resolvedExports = "resolvedExports",
    resolvedMembers = "resolvedMembers"
}
```
### UnusedKind
```ts
const enum UnusedKind {
    Local,
    Parameter,
}
```
### AddUnusedDiagnostic
```ts
type AddUnusedDiagnostic = (containingNode: Node, type: UnusedKind, diagnostic: DiagnosticWithLocation) => void;

```
### isNotOverloadAndNotAccessor
```ts
const isNotOverloadAndNotAccessor = and(isNotOverload, isNotAccessor);
```

### DeclarationMeaning
```ts
const enum DeclarationMeaning {
    GetAccessor = 1,
    SetAccessor = 2,
    PropertyAssignment = 4,
    Method = 8,
    GetOrSetAccessor = GetAccessor | SetAccessor,
    PropertyAssignmentOrMethod = PropertyAssignment | Method,
}
```

### DeclarationSpaces
```ts
const enum DeclarationSpaces {
    None = 0,
    ExportValue = 1 << 0,
    ExportType = 1 << 1,
    ExportNamespace = 1 << 2,
}
```

## 实例对象
### Base
```ts
const ambientModuleSymbolRegex = /^".+"$/;

let nextSymbolId = 1;
let nextNodeId = 1;
let nextMergeId = 1;
let nextFlowId = 1;
```

### Map
```ts
const typeofEQFacts: ReadonlyMap<TypeFacts> = createMapFromTemplate({
    string: TypeFacts.TypeofEQString,
    number: TypeFacts.TypeofEQNumber,
    bigint: TypeFacts.TypeofEQBigInt,
    boolean: TypeFacts.TypeofEQBoolean,
    symbol: TypeFacts.TypeofEQSymbol,
    undefined: TypeFacts.EQUndefined,
    object: TypeFacts.TypeofEQObject,
    function: TypeFacts.TypeofEQFunction
});

const typeofNEFacts: ReadonlyMap<TypeFacts> = createMapFromTemplate({
    string: TypeFacts.TypeofNEString,
    number: TypeFacts.TypeofNENumber,
    bigint: TypeFacts.TypeofNEBigInt,
    boolean: TypeFacts.TypeofNEBoolean,
    symbol: TypeFacts.TypeofNESymbol,
    undefined: TypeFacts.NEUndefined,
    object: TypeFacts.TypeofNEObject,
    function: TypeFacts.TypeofNEFunction
});
```

## 功能函数
### getNodeId
```ts
export function getNodeId(node: Node): number {
    if (!node.id) {
        node.id = nextNodeId;
        nextNodeId++;
    }
    return node.id;
}
```

### getSymbolId
```ts
export function getSymbolId(symbol: Symbol): number {
    if (!symbol.id) {
        symbol.id = nextSymbolId;
        nextSymbolId++;
    }

    return symbol.id;
}
```

### isInstantiatedModule
```ts
export function isInstantiatedModule(node: ModuleDeclaration, preserveConstEnums: boolean) {
    const moduleState = getModuleInstanceState(node);
    return moduleState === ModuleInstanceState.Instantiated ||
        (preserveConstEnums && moduleState === ModuleInstanceState.ConstEnumOnly);
}
```

### createTypeChecker
```ts
export function createTypeChecker(host: TypeCheckerHost, produceDiagnostics: boolean): TypeChecker {
    const getPackagesSet: () => Map<true> = memoize(() => {
        const set = createMap<true>();
        host.getSourceFiles().forEach(sf => {
            if (!sf.resolvedModules) return;

            forEachEntry(sf.resolvedModules, r => {
                if (r && r.packageId) set.set(r.packageId.name, true);
            });
        });
        return set;
    });

    ...

    const checker: TypeChecker = {
        getNodeCount: () => sum(host.getSourceFiles(), "nodeCount"),
        getIdentifierCount: () => sum(host.getSourceFiles(), "identifierCount"),
        getSymbolCount: () => sum(host.getSourceFiles(), "symbolCount") + symbolCount,
        getTypeCount: () => typeCount,
        getRelationCacheSizes: () => ({
            assignable: assignableRelation.size,
            identity: identityRelation.size,
            subtype: subtypeRelation.size,
        }),
        isUndefinedSymbol: symbol => symbol === undefinedSymbol,
        isArgumentsSymbol: symbol => symbol === argumentsSymbol,
        isUnknownSymbol: symbol => symbol === unknownSymbol,
        getMergedSymbol,
        getDiagnostics,
        getGlobalDiagnostics,
        getTypeOfSymbolAtLocation: (symbol, location) => {
            location = getParseTreeNode(location);
            return location ? getTypeOfSymbolAtLocation(symbol, location) : errorType;
        },
        getSymbolsOfParameterPropertyDeclaration: (parameterIn, parameterName) => {
            const parameter = getParseTreeNode(parameterIn, isParameter);
            if (parameter === undefined) return Debug.fail("Cannot get symbols of a synthetic parameter that cannot be resolved to a parse-tree node.");
            return getSymbolsOfParameterPropertyDeclaration(parameter, escapeLeadingUnderscores(parameterName));
        },
        getDeclaredTypeOfSymbol,
        getPropertiesOfType,
        getPropertyOfType: (type, name) => getPropertyOfType(type, escapeLeadingUnderscores(name)),
        getTypeOfPropertyOfType: (type, name) => getTypeOfPropertyOfType(type, escapeLeadingUnderscores(name)),
        getIndexInfoOfType,
        getSignaturesOfType,
        getIndexTypeOfType,
        getBaseTypes,
        getBaseTypeOfLiteralType,
        getWidenedType,
        getTypeFromTypeNode: nodeIn => {
            const node = getParseTreeNode(nodeIn, isTypeNode);
            return node ? getTypeFromTypeNode(node) : errorType;
        },
        getParameterType: getTypeAtPosition,
        getPromisedTypeOfPromise,
        getReturnTypeOfSignature,
        isNullableType,
        getNullableType,
        getNonNullableType,
        getNonOptionalType: removeOptionalTypeMarker,
        getTypeArguments,
        typeToTypeNode: nodeBuilder.typeToTypeNode,
        indexInfoToIndexSignatureDeclaration: nodeBuilder.indexInfoToIndexSignatureDeclaration,
        signatureToSignatureDeclaration: nodeBuilder.signatureToSignatureDeclaration,
        symbolToEntityName: nodeBuilder.symbolToEntityName,
        symbolToExpression: nodeBuilder.symbolToExpression,
        symbolToTypeParameterDeclarations: nodeBuilder.symbolToTypeParameterDeclarations,
        symbolToParameterDeclaration: nodeBuilder.symbolToParameterDeclaration,
        typeParameterToDeclaration: nodeBuilder.typeParameterToDeclaration,
        getSymbolsInScope: (location, meaning) => {
            location = getParseTreeNode(location);
            return location ? getSymbolsInScope(location, meaning) : [];
        },
        getSymbolAtLocation: node => {
            node = getParseTreeNode(node);
            return node ? getSymbolAtLocation(node) : undefined;
        },
        getShorthandAssignmentValueSymbol: node => {
            node = getParseTreeNode(node);
            return node ? getShorthandAssignmentValueSymbol(node) : undefined;
        },
        getExportSpecifierLocalTargetSymbol: nodeIn => {
            const node = getParseTreeNode(nodeIn, isExportSpecifier);
            return node ? getExportSpecifierLocalTargetSymbol(node) : undefined;
        },
        getExportSymbolOfSymbol(symbol) {
            return getMergedSymbol(symbol.exportSymbol || symbol);
        },
        getTypeAtLocation: node => {
            node = getParseTreeNode(node);
            return node ? getTypeOfNode(node) : errorType;
        },
        getTypeOfAssignmentPattern: nodeIn => {
            const node = getParseTreeNode(nodeIn, isAssignmentPattern);
            return node && getTypeOfAssignmentPattern(node) || errorType;
        },
        getPropertySymbolOfDestructuringAssignment: locationIn => {
            const location = getParseTreeNode(locationIn, isIdentifier);
            return location ? getPropertySymbolOfDestructuringAssignment(location) : undefined;
        },
        signatureToString: (signature, enclosingDeclaration, flags, kind) => {
            return signatureToString(signature, getParseTreeNode(enclosingDeclaration), flags, kind);
        },
        typeToString: (type, enclosingDeclaration, flags) => {
            return typeToString(type, getParseTreeNode(enclosingDeclaration), flags);
        },
        symbolToString: (symbol, enclosingDeclaration, meaning, flags) => {
            return symbolToString(symbol, getParseTreeNode(enclosingDeclaration), meaning, flags);
        },
        typePredicateToString: (predicate, enclosingDeclaration, flags) => {
            return typePredicateToString(predicate, getParseTreeNode(enclosingDeclaration), flags);
        },
        writeSignature: (signature, enclosingDeclaration, flags, kind, writer) => {
            return signatureToString(signature, getParseTreeNode(enclosingDeclaration), flags, kind, writer);
        },
        writeType: (type, enclosingDeclaration, flags, writer) => {
            return typeToString(type, getParseTreeNode(enclosingDeclaration), flags, writer);
        },
        writeSymbol: (symbol, enclosingDeclaration, meaning, flags, writer) => {
            return symbolToString(symbol, getParseTreeNode(enclosingDeclaration), meaning, flags, writer);
        },
        writeTypePredicate: (predicate, enclosingDeclaration, flags, writer) => {
            return typePredicateToString(predicate, getParseTreeNode(enclosingDeclaration), flags, writer);
        },
        getAugmentedPropertiesOfType,
        getRootSymbols,
        getContextualType: nodeIn => {
            const node = getParseTreeNode(nodeIn, isExpression);
            return node ? getContextualType(node) : undefined;
        },
        getContextualTypeForObjectLiteralElement: nodeIn => {
            const node = getParseTreeNode(nodeIn, isObjectLiteralElementLike);
            return node ? getContextualTypeForObjectLiteralElement(node) : undefined;
        },
        getContextualTypeForArgumentAtIndex: (nodeIn, argIndex) => {
            const node = getParseTreeNode(nodeIn, isCallLikeExpression);
            return node && getContextualTypeForArgumentAtIndex(node, argIndex);
        },
        getContextualTypeForJsxAttribute: (nodeIn) => {
            const node = getParseTreeNode(nodeIn, isJsxAttributeLike);
            return node && getContextualTypeForJsxAttribute(node);
        },
        isContextSensitive,
        getFullyQualifiedName,
        getResolvedSignature: (node, candidatesOutArray, argumentCount) =>
            getResolvedSignatureWorker(node, candidatesOutArray, argumentCount, CheckMode.Normal),
        getResolvedSignatureForSignatureHelp: (node, candidatesOutArray, argumentCount) =>
            getResolvedSignatureWorker(node, candidatesOutArray, argumentCount, CheckMode.IsForSignatureHelp),
        getExpandedParameters,
        hasEffectiveRestParameter,
        getConstantValue: nodeIn => {
            const node = getParseTreeNode(nodeIn, canHaveConstantValue);
            return node ? getConstantValue(node) : undefined;
        },
        isValidPropertyAccess: (nodeIn, propertyName) => {
            const node = getParseTreeNode(nodeIn, isPropertyAccessOrQualifiedNameOrImportTypeNode);
            return !!node && isValidPropertyAccess(node, escapeLeadingUnderscores(propertyName));
        },
        isValidPropertyAccessForCompletions: (nodeIn, type, property) => {
            const node = getParseTreeNode(nodeIn, isPropertyAccessExpression);
            return !!node && isValidPropertyAccessForCompletions(node, type, property);
        },
        getSignatureFromDeclaration: declarationIn => {
            const declaration = getParseTreeNode(declarationIn, isFunctionLike);
            return declaration ? getSignatureFromDeclaration(declaration) : undefined;
        },
        isImplementationOfOverload: node => {
            const parsed = getParseTreeNode(node, isFunctionLike);
            return parsed ? isImplementationOfOverload(parsed) : undefined;
        },
        getImmediateAliasedSymbol,
        getAliasedSymbol: resolveAlias,
        getEmitResolver,
        getExportsOfModule: getExportsOfModuleAsArray,
        getExportsAndPropertiesOfModule,
        getSymbolWalker: createGetSymbolWalker(
            getRestTypeOfSignature,
            getTypePredicateOfSignature,
            getReturnTypeOfSignature,
            getBaseTypes,
            resolveStructuredTypeMembers,
            getTypeOfSymbol,
            getResolvedSymbol,
            getIndexTypeOfStructuredType,
            getConstraintOfTypeParameter,
            getFirstIdentifier,
            getTypeArguments,
        ),
        getAmbientModules,
        getJsxIntrinsicTagNamesAt,
        isOptionalParameter: nodeIn => {
            const node = getParseTreeNode(nodeIn, isParameter);
            return node ? isOptionalParameter(node) : false;
        },
        tryGetMemberInModuleExports: (name, symbol) => tryGetMemberInModuleExports(escapeLeadingUnderscores(name), symbol),
        tryGetMemberInModuleExportsAndProperties: (name, symbol) => tryGetMemberInModuleExportsAndProperties(escapeLeadingUnderscores(name), symbol),
        tryFindAmbientModuleWithoutAugmentations: moduleName => {
            // we deliberately exclude augmentations
            // since we are only interested in declarations of the module itself
            return tryFindAmbientModule(moduleName, /*withAugmentations*/ false);
        },
        getApparentType,
        getUnionType,
        isTypeAssignableTo: (source, target) => {
            return isTypeAssignableTo(source, target);
        },
        createAnonymousType,
        createSignature,
        createSymbol,
        createIndexInfo,
        getAnyType: () => anyType,
        getStringType: () => stringType,
        getNumberType: () => numberType,
        createPromiseType,
        createArrayType,
        getElementTypeOfArrayType,
        getBooleanType: () => booleanType,
        getFalseType: (fresh?) => fresh ? falseType : regularFalseType,
        getTrueType: (fresh?) => fresh ? trueType : regularTrueType,
        getVoidType: () => voidType,
        getUndefinedType: () => undefinedType,
        getNullType: () => nullType,
        getESSymbolType: () => esSymbolType,
        getNeverType: () => neverType,
        getOptionalType: () => optionalType,
        isSymbolAccessible,
        getObjectFlags,
        isArrayType,
        isTupleType,
        isArrayLikeType,
        isTypeInvalidDueToUnionDiscriminant,
        getAllPossiblePropertiesOfTypes,
        getSuggestionForNonexistentProperty: (node, type) => getSuggestionForNonexistentProperty(node, type),
        getSuggestionForNonexistentSymbol: (location, name, meaning) => getSuggestionForNonexistentSymbol(location, escapeLeadingUnderscores(name), meaning),
        getSuggestionForNonexistentExport: (node, target) => getSuggestionForNonexistentExport(node, target),
        getBaseConstraintOfType,
        getDefaultFromTypeParameter: type => type && type.flags & TypeFlags.TypeParameter ? getDefaultFromTypeParameter(type as TypeParameter) : undefined,
        resolveName(name, location, meaning, excludeGlobals) {
            return resolveName(location, escapeLeadingUnderscores(name), meaning, /*nameNotFoundMessage*/ undefined, /*nameArg*/ undefined, /*isUse*/ false, excludeGlobals);
        },
        getJsxNamespace: n => unescapeLeadingUnderscores(getJsxNamespace(n)),
        getAccessibleSymbolChain,
        getTypePredicateOfSignature,
        resolveExternalModuleSymbol,
        tryGetThisTypeAt: (node, includeGlobalThis) => {
            node = getParseTreeNode(node);
            return node && tryGetThisTypeAt(node, includeGlobalThis);
        },
        getTypeArgumentConstraint: nodeIn => {
            const node = getParseTreeNode(nodeIn, isTypeNode);
            return node && getTypeArgumentConstraint(node);
        },
        getSuggestionDiagnostics: (file, ct) => {
            if (skipTypeChecking(file, compilerOptions, host)) {
                return emptyArray;
            }

            let diagnostics: DiagnosticWithLocation[] | undefined;
            try {
                // Record the cancellation token so it can be checked later on during checkSourceElement.
                // Do this in a finally block so we can ensure that it gets reset back to nothing after
                // this call is done.
                cancellationToken = ct;

                // Ensure file is type checked
                checkSourceFile(file);
                Debug.assert(!!(getNodeLinks(file).flags & NodeCheckFlags.TypeChecked));

                diagnostics = addRange(diagnostics, suggestionDiagnostics.getDiagnostics(file.fileName));
                if (!file.isDeclarationFile && (!unusedIsError(UnusedKind.Local) || !unusedIsError(UnusedKind.Parameter))) {
                    addUnusedDiagnostics();
                }
                return diagnostics || emptyArray;
            }
            finally {
                cancellationToken = undefined;
            }

            function addUnusedDiagnostics() {
                checkUnusedIdentifiers(getPotentiallyUnusedIdentifiers(file), (containingNode, kind, diag) => {
                    if (!containsParseError(containingNode) && !unusedIsError(kind)) {
                        (diagnostics || (diagnostics = [])).push({ ...diag, category: DiagnosticCategory.Suggestion });
                    }
                });
            }
        },

        runWithCancellationToken: (token, callback) => {
            try {
                cancellationToken = token;
                return callback(checker);
            }
            finally {
                cancellationToken = undefined;
            }
        },

        getLocalTypeParametersOfClassOrInterfaceOrTypeAlias,
    };

    ...

}
```
## 内部函数
### isNotAccessor
```ts
function isNotAccessor(declaration: Declaration): boolean {
    // Accessors check for their own matching duplicates, and in contexts where they are valid, there are already duplicate identifier checks
    return !isAccessor(declaration);
}
```
### isNotOverload
```ts
function isNotOverload(declaration: Declaration): boolean {
    return (declaration.kind !== SyntaxKind.FunctionDeclaration && declaration.kind !== SyntaxKind.MethodDeclaration) ||
            !!(declaration as FunctionDeclaration).body;
}
```
### isDeclarationNameOrImportPropertyName
```ts
function isDeclarationNameOrImportPropertyName(name: Node): boolean {
    switch (name.parent.kind) {
        case SyntaxKind.ImportSpecifier:
        case SyntaxKind.ExportSpecifier:
            return isIdentifier(name);
        default:
            return isDeclarationName(name);
    }
}
```

### isSomeImportDeclaration
```ts
function isSomeImportDeclaration(decl: Node): boolean {
    switch (decl.kind) {
        case SyntaxKind.ImportClause: // For default import
        case SyntaxKind.ImportEqualsDeclaration:
        case SyntaxKind.NamespaceImport:
        case SyntaxKind.ImportSpecifier: // For rename import `x as y`
            return true;
        case SyntaxKind.Identifier:
            // For regular import, `decl` is an Identifier under the ImportSpecifier.
            return decl.parent.kind === SyntaxKind.ImportSpecifier;
        default:
            return false;
    }
}
```

### getIterationTypesKeyFromIterationTypeKind
```ts
function getIterationTypesKeyFromIterationTypeKind(typeKind: IterationTypeKind) {
    switch (typeKind) {
        case IterationTypeKind.Yield: return "yieldType";
        case IterationTypeKind.Return: return "returnType";
        case IterationTypeKind.Next: return "nextType";
    }
}
```