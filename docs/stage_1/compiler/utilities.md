## 类型接口

## 实例对象
### Base
```ts
export const resolvingEmptyArray: never[] = [] as never[];
export const emptyMap = createMap<never>() as ReadonlyMap<never> & ReadonlyPragmaMap;
export const emptyUnderscoreEscapedMap: ReadonlyUnderscoreEscapedMap<never> = emptyMap as ReadonlyUnderscoreEscapedMap<never>;

export const externalHelpersModuleNameText = "tslib";

export const defaultMaximumTruncationLength = 160;
const stringWriter = createSingleLineStringWriter();
export const fullTripleSlashReferencePathRegEx = /^(\/\/\/\s*<reference\s+path\s*=\s*)('|")(.+?)\2.*?\/>/;
const fullTripleSlashReferenceTypeReferenceDirectiveRegEx = /^(\/\/\/\s*<reference\s+types\s*=\s*)('|")(.+?)\2.*?\/>/;
export const fullTripleSlashAMDReferencePathRegEx = /^(\/\/\/\s*<amd-dependency\s+path\s*=\s*)('|")(.+?)\2.*?\/>/;
const defaultLibReferenceRegEx = /^(\/\/\/\s*<reference\s+no-default-lib\s*=\s*)('|")(.+?)\2\s*\/>/;

```


## 功能函数
### isExternalModuleNameRelative
```ts
export function isExternalModuleNameRelative(moduleName: string): boolean {
    return pathIsRelative(moduleName) || isRootedDiskPath(moduleName);
}
```
### sortAndDeduplicateDiagnostics
```ts
export function sortAndDeduplicateDiagnostics<T extends Diagnostic>(diagnostics: readonly T[]): SortedReadonlyArray<T> {
    return sortAndDeduplicate<T>(diagnostics, compareDiagnostics);
}
```

### getDeclarationOfKind
```ts

```
### createUnderscoreEscapedMap
### hasEntries
### createSymbolTable
### changesAffectModuleResolution
### optionsHaveModuleResolutionChanges
### findAncestor
### forEachAncestor
### forEachEntry
### forEachKey
### copyEntries
### arrayToSet
### cloneMap
### usingSingleLineStringWriter
### getFullWidth
### getResolvedModule
### setResolvedModule
### setResolvedTypeReferenceDirective
### projectReferenceIsEqualTo
### moduleResolutionIsEqualTo
### packageIdToString
### typeDirectiveIsEqualTo
### hasChangesInResolutions
### containsParseError
### getSourceFileOfNode
### isStatementWithLocals
### getStartPositionOfLine
### nodePosToString
### getEndLinePosition
### isFileLevelUniqueName
### nodeIsMissing
### nodeIsPresent
### insertStatementsAfterStandardPrologue
### insertStatementsAfterCustomPrologue
### insertStatementAfterStandardPrologue
### insertStatementAfterCustomPrologue
### isRecognizedTripleSlashComment
### isPinnedComment
### getTokenPosOfNode
### getNonDecoratorTokenPosOfNode
### getSourceTextOfNodeFromSourceFile
### getTextOfNodeFromSourceText
### getTextOfNode
### indexOfNode
### getEmitFlags
### getLiteralText
### getTextOfConstantValue
### makeIdentifierFromModuleName
### isBlockOrCatchScoped
### isCatchClauseVariableDeclarationOrBindingElement
### isAmbientModule
### isModuleWithStringLiteralName
### isNonGlobalAmbientModule
### isEffectiveModuleDeclaration
### isShorthandAmbientModuleSymbol
### isBlockScopedContainerTopLevel
### isGlobalScopeAugmentation
### isExternalModuleAugmentation
### isModuleAugmentationExternal
### getNonAugmentationDeclaration
### isEffectiveExternalModule
### isEffectiveStrictModeSourceFile
### isBlockScope
### isDeclarationWithTypeParameters
### isDeclarationWithTypeParameterChildren
### isAnyImportSyntax
### isLateVisibilityPaintedStatement
### isAnyImportOrReExport
### getEnclosingBlockScopeContainer
### declarationNameToString
### getNameFromIndexInfo
### getTextOfPropertyName
### entityNameToString
### createDiagnosticForNode
### createDiagnosticForNodeArray
### createDiagnosticForNodeInSourceFile
### createDiagnosticForNodeFromMessageChain
### getSpanOfTokenAtPosition
### getErrorSpanForNode
### isExternalOrCommonJsModule
### isJsonSourceFile
### isEnumConst
### isDeclarationReadonly
### isVarConst
### isLet
### isSuperCall
### isImportCall
### isImportMeta
### isLiteralImportTypeNode
### isPrologueDirective
### getLeadingCommentRangesOfNode
### getJSDocCommentRanges
### isPartOfTypeNode
### isChildOfNodeWithKind
### forEachReturnStatement
### forEachYieldExpression
### getRestParameterElementType
### getMembersOfDeclaration
### isVariableLike
### isVariableLikeOrAccessor
### isVariableDeclarationInVariableStatement
### isValidESSymbolDeclaration
### introducesArgumentsExoticObject
### unwrapInnermostStatementOfLabel
### isFunctionBlock
### isObjectLiteralMethod
### isObjectLiteralOrClassExpressionMethod

## 内部函数
###  createSingleLineStringWriter
```ts
function createSingleLineStringWriter(): EmitTextWriter {
    let str = "";
    const writeText: (text: string) => void = text => str += text;
    return {
        getText: () => str,
        write: writeText,
        rawWrite: writeText,
        writeKeyword: writeText,
        writeOperator: writeText,
        writePunctuation: writeText,
        writeSpace: writeText,
        writeStringLiteral: writeText,
        writeLiteral: writeText,
        writeParameter: writeText,
        writeProperty: writeText,
        writeSymbol: (s, _) => writeText(s),
        writeTrailingSemicolon: writeText,
        writeComment: writeText,
        getTextPos: () => str.length,
        getLine: () => 0,
        getColumn: () => 0,
        getIndent: () => 0,
        isAtStartOfLine: () => false,
        hasTrailingComment: () => false,
        hasTrailingWhitespace: () => !!str.length && isWhiteSpaceLike(str.charCodeAt(str.length - 1)),

        // Completely ignore indentation for string writers.  And map newlines to
        // a single space.
        writeLine: () => str += " ",
        increaseIndent: noop,
        decreaseIndent: noop,
        clear: () => str = "",
        trackSymbol: noop,
        reportInaccessibleThisError: noop,
        reportInaccessibleUniqueSymbolError: noop,
        reportPrivateInBaseOfClassExpression: noop,
    };
}
```

### packageIdIsEqual
### aggregateChildData
### insertStatementsAfterPrologue
### insertStatementAfterPrologue
### isAnyPrologueDirective
### isJSDocTypeExpressionOrChild
### isShorthandAmbientModule
### getErrorSpanForArrowFunction