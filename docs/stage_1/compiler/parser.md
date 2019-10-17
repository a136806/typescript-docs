## 功能说明

# (namespace:ts)

## 类型接口
### SignatureFlags
```ts
const enum SignatureFlags {
    None = 0,
    Yield = 1 << 0,
    Await = 1 << 1,
    Type  = 1 << 2,
    IgnoreMissingOpenBrace = 1 << 4,
    JSDoc = 1 << 5,
}
```
## 实例对象
> 解析中用到的对象
```ts
let NodeConstructor: new (kind: SyntaxKind, pos?: number, end?: number) => Node;
let TokenConstructor: new (kind: SyntaxKind, pos?: number, end?: number) => Node;
let IdentifierConstructor: new (kind: SyntaxKind, pos?: number, end?: number) => Node;
let SourceFileConstructor: new (kind: SyntaxKind, pos?: number, end?: number) => Node;
```
## 功能函数

### createNode
> 创建语法节点`Node`
```ts
export function createNode(kind: SyntaxKind, pos?: number, end?: number): Node {
    if (kind === SyntaxKind.SourceFile) {
        return new (SourceFileConstructor || (SourceFileConstructor = objectAllocator.getSourceFileConstructor()))(kind, pos, end);
    }
    else if (kind === SyntaxKind.Identifier) {
        return new (IdentifierConstructor || (IdentifierConstructor = objectAllocator.getIdentifierConstructor()))(kind, pos, end);
    }
    else if (!isNodeKind(kind)) {
        return new (TokenConstructor || (TokenConstructor = objectAllocator.getTokenConstructor()))(kind, pos, end);
    }
    else {
        return new (NodeConstructor || (NodeConstructor = objectAllocator.getNodeConstructor()))(kind, pos, end);
    }
}
```


### isJSDocLikeText
> `**/`字符串判断
```ts
export function isJSDocLikeText(text: string, start: number) {
    return text.charCodeAt(start + 1) === CharacterCodes.asterisk &&
        text.charCodeAt(start + 2) === CharacterCodes.asterisk &&
        text.charCodeAt(start + 3) !== CharacterCodes.slash;
}
```

### forEachChild
> 对节点的子节点进行回调处理
```ts
export function forEachChild<T>(
    node: Node, 
    cbNode: (node: Node) => T | undefined, 
    cbNodes?: (nodes: NodeArray<Node>) => T | undefined): T | undefined {
    ...
}
```

### createSourceFile
> 解析文件
```ts
export function createSourceFile(
    fileName: string, 
    sourceText: string, 
    languageVersion: ScriptTarget, 
    setParentNodes = false, 
    scriptKind?: ScriptKind): SourceFile {
        ..
}
```

### parseIsolatedEntityName
```ts
export function parseIsolatedEntityName(text: string, languageVersion: ScriptTarget): EntityName | undefined {
    return Parser.parseIsolatedEntityName(text, languageVersion);
}
```
 
### parseJsonText
```ts
export function parseJsonText(fileName: string, sourceText: string): JsonSourceFile {
    return Parser.parseJsonText(fileName, sourceText);
}
```

### isExternalModule
```ts
export function isExternalModule(file: SourceFile): boolean {
    return file.externalModuleIndicator !== undefined;
}
```

### updateSourceFile
```ts
export function updateSourceFile(sourceFile: SourceFile, newText: string, textChangeRange: TextChangeRange, aggressiveChecks = false): SourceFile {
    const newSourceFile = IncrementalParser.updateSourceFile(sourceFile, newText, textChangeRange, aggressiveChecks);
    newSourceFile.flags |= (sourceFile.flags & NodeFlags.PermanentlySetIncrementalFlags);
    return newSourceFile;
}
```
### parseIsolatedJSDocComment
```ts
export function parseIsolatedJSDocComment(content: string, start?: number, length?: number) {
    const result = Parser.JSDocParser.parseIsolatedJSDocComment(content, start, length);
    if (result && result.jsDoc) {
        
        Parser.fixupParentReferences(result.jsDoc);
    }

    return result;
}
```

### parseJSDocTypeExpressionForTests
```ts
export function parseJSDocTypeExpressionForTests(content: string, start?: number, length?: number) {
    return Parser.JSDocParser.parseJSDocTypeExpressionForTests(content, start, length);
}
```


## 内部函数


### visitNode
> 对节点进行回调
```ts
function visitNode<T>(cbNode: (node: Node) => T, node: Node | undefined): T | undefined {
    return node && cbNode(node);
}
```

### visitNodes
> 对节点数组进行回调
```ts
function visitNodes<T>(
    cbNode: (node: Node) => T, 
    cbNodes: ((node: NodeArray<Node>) => T | undefined) | undefined,
    nodes: NodeArray<Node> | undefined): T | undefined {
    if (nodes) {
        if (cbNodes) {
            return cbNodes(nodes);
        }
        for (const node of nodes) {
            const result = cbNode(node);
            if (result) {
                return result;
            }
        }
    }
}
```


# (namespace:ts.Parser)

## 类型接口
## 实例对象
```ts
const scanner = createScanner(ScriptTarget.Latest, /*skipTrivia*/ true);
const disallowInAndDecoratorContext = NodeFlags.DisallowInContext | NodeFlags.DecoratorContext;

let NodeConstructor: new (kind: SyntaxKind, pos: number, end: number) => Node;
let TokenConstructor: new (kind: SyntaxKind, pos: number, end: number) => Node;
let IdentifierConstructor: new (kind: SyntaxKind, pos: number, end: number) => Node;
let SourceFileConstructor: new (kind: SyntaxKind, pos: number, end: number) => Node;


let sourceFile: SourceFile;
let parseDiagnostics: DiagnosticWithLocation[];
let syntaxCursor: IncrementalParser.SyntaxCursor | undefined;

let currentToken: SyntaxKind;
let sourceText: string;
let nodeCount: number;
let identifiers: Map<string>;
let identifierCount: number;

let parsingContext: ParsingContext;

let notParenthesizedArrow: Map<true> | undefined;
let contextFlags: NodeFlags;
let parseErrorBeforeNextFinishedNode = false;
```
## 功能函数
### parseSourceFile
```ts
export function parseSourceFile(
    fileName: string, 
    sourceText: string, 
    languageVersion: ScriptTarget, 
    syntaxCursor: IncrementalParser.SyntaxCursor | undefined, 
    setParentNodes = false, scriptKind?: ScriptKind): SourceFile {
        ...
}
```

### parseIsolatedEntityName
```ts
export function parseIsolatedEntityName(content: string, languageVersion: ScriptTarget): EntityName | undefined {
    ...
}
```

### parseJsonText
```ts
export function parseJsonText(fileName: string, sourceText: string, languageVersion: ScriptTarget = ScriptTarget.ES2015, syntaxCursor?: IncrementalParser.SyntaxCursor, setParentNodes?: boolean): JsonSourceFile {
    ...
}
```
### fixupParentReferences
```ts
export function fixupParentReferences(rootNode: Node) {
    ...
}
```
## 内部函数
### getLanguageVariant
```ts
function getLanguageVariant(scriptKind: ScriptKind) {
    // .tsx and .jsx files are treated as jsx language variant.
    return scriptKind === ScriptKind.TSX 
        || scriptKind === ScriptKind.JSX 
        || scriptKind === ScriptKind.JS 
        || scriptKind === ScriptKind.JSON 
            ? LanguageVariant.JSX  
            : LanguageVariant.Standard;
}
```
### initializeState
```ts
function initializeState(
    _sourceText: string, 
    languageVersion: ScriptTarget, 
    _syntaxCursor: IncrementalParser.SyntaxCursor | undefined, 
    scriptKind: ScriptKind) {
    ...
}
```

### clearState
```ts
function clearState() {
    // Clear out the text the scanner is pointing at, so it doesn't keep anything alive unnecessarily.
    scanner.setText("");
    scanner.setOnError(undefined);

    // Clear any data.  We don't want to accidentally hold onto it for too long.
    parseDiagnostics = undefined!;
    sourceFile = undefined!;
    identifiers = undefined!;
    syntaxCursor = undefined;
    sourceText = undefined!;
    notParenthesizedArrow = undefined!;
}
```

### parseSourceFileWorker
```ts
function parseSourceFileWorker(fileName: string, languageVersion: ScriptTarget, setParentNodes: boolean, scriptKind: ScriptKind): SourceFile {
    ...
}
```

### addJSDocComment
```ts
function addJSDocComment<T extends HasJSDoc>(node: T): T {
    Debug.assert(!node.jsDoc); // Should only be called once per node
    const jsDoc = mapDefined(getJSDocCommentRanges(node, sourceFile.text), comment => JSDocParser.parseJSDocComment(node, comment.pos, comment.end - comment.pos));
    if (jsDoc.length) node.jsDoc = jsDoc;
    return node;
}
```

### createSourceFile
```ts
function createSourceFile(
    fileName: string, 
    languageVersion: ScriptTarget, 
    scriptKind: ScriptKind, 
    isDeclarationFile: boolean): SourceFile {
    ...
}
```

### setContextFlag
```ts
function setContextFlag(val: boolean, flag: NodeFlags) {
    if (val) {
        contextFlags |= flag;
    }
    else {
        contextFlags &= ~flag;
    }
}
```
### setDisallowInContext
```ts
function setDisallowInContext(val: boolean) {
    setContextFlag(val, NodeFlags.DisallowInContext);
}
```
### setYieldContext
```ts
function setYieldContext(val: boolean) {
    setContextFlag(val, NodeFlags.YieldContext);
}
```
### setDecoratorContext
```ts
function setDecoratorContext(val: boolean) {
    setContextFlag(val, NodeFlags.DecoratorContext);
}
```
### setAwaitContext
```ts
function setAwaitContext(val: boolean) {
    setContextFlag(val, NodeFlags.AwaitContext);
}
```

### doOutsideOfContext
```ts
function doOutsideOfContext<T>(context: NodeFlags, func: () => T): T {
    const contextFlagsToClear = context & contextFlags;
    if (contextFlagsToClear) {
        setContextFlag(/*val*/ false, contextFlagsToClear);
        const result = func();
        setContextFlag(/*val*/ true, contextFlagsToClear);
        return result;
    }
    return func();
}
```

### doInsideOfContext
```ts
function doInsideOfContext<T>(context: NodeFlags, func: () => T): T {
    const contextFlagsToSet = context & ~contextFlags;
    if (contextFlagsToSet) {
        
        setContextFlag(/*val*/ true, contextFlagsToSet);
        const result = func();
        
        setContextFlag(/*val*/ false, contextFlagsToSet);
        return result;
    }

    return func();
}
```

### allowInAnd
```ts
function allowInAnd<T>(func: () => T): T {
    return doOutsideOfContext(NodeFlags.DisallowInContext, func);
}
```

### disallowInAnd
```ts
function disallowInAnd<T>(func: () => T): T {
    return doInsideOfContext(NodeFlags.DisallowInContext, func);
}
```

### doInYieldContext
```ts
function doInYieldContext<T>(func: () => T): T {
    return doInsideOfContext(NodeFlags.YieldContext, func);
}
```

### doInDecoratorContext
```ts
function doInDecoratorContext<T>(func: () => T): T {
    return doInsideOfContext(NodeFlags.DecoratorContext, func);
}
```

### doInAwaitContext
```ts
function doInAwaitContext<T>(func: () => T): T {
    return doInsideOfContext(NodeFlags.AwaitContext, func);
}
```

### doOutsideOfAwaitContext
```ts
function doOutsideOfAwaitContext<T>(func: () => T): T {
    return doOutsideOfContext(NodeFlags.AwaitContext, func);
}
```

### doInYieldAndAwaitContext
```ts
function doInYieldAndAwaitContext<T>(func: () => T): T {
    return doInsideOfContext(NodeFlags.YieldContext | NodeFlags.AwaitContext, func);
}
```

### inContext
```ts
function inContext(flags: NodeFlags) {
    return (contextFlags & flags) !== 0;
}
```

### inYieldContext
```ts
function inYieldContext() {
    return inContext(NodeFlags.YieldContext);
}
```

### inDisallowInContext
```ts
function inDisallowInContext() {
    return inContext(NodeFlags.DisallowInContext);
}
```

### inDecoratorContext
```ts
function inDecoratorContext() {
    return inContext(NodeFlags.DecoratorContext);
}
```

### inAwaitContext
```ts
function inAwaitContext() {
    return inContext(NodeFlags.AwaitContext);
}
```

### parseErrorAtCurrentToken
```ts
function parseErrorAtCurrentToken(message: DiagnosticMessage, arg0?: any): void {
    parseErrorAt(scanner.getTokenPos(), scanner.getTextPos(), message, arg0);
}
```

### parseErrorAtPosition
```ts
function parseErrorAtPosition(start: number, length: number, message: DiagnosticMessage, arg0?: any): void {
    const lastError = lastOrUndefined(parseDiagnostics);
    if (!lastError || start !== lastError.start) {
        parseDiagnostics.push(createFileDiagnostic(sourceFile, start, length, message, arg0));
    }

    parseErrorBeforeNextFinishedNode = true;
}
```

### parseErrorAt
```ts
function parseErrorAt(start: number, end: number, message: DiagnosticMessage, arg0?: any): void {
    parseErrorAtPosition(start, end - start, message, arg0);
}
```
### parseErrorAtRange
```ts
function parseErrorAtRange(range: TextRange, message: DiagnosticMessage, arg0?: any): void {
    parseErrorAt(range.pos, range.end, message, arg0);
}
```
### scanError
```ts
function scanError(message: DiagnosticMessage, length: number): void {
    parseErrorAtPosition(scanner.getTextPos(), length, message);
}
```
### getNodePos
```ts
function getNodePos(): number {
    return scanner.getStartPos();
}
```

### token
```ts
function token(): SyntaxKind {
    return currentToken;
}
```

### nextTokenWithoutCheck
```ts
function nextTokenWithoutCheck() {
    return currentToken = scanner.scan();
}
```

### nextToken
```ts
function nextToken(): SyntaxKind {
    // if the keyword had an escape
    if (isKeyword(currentToken) && (scanner.hasUnicodeEscape() || scanner.hasExtendedUnicodeEscape())) {
        // issue a parse error for the escape
        parseErrorAt(scanner.getTokenPos(), scanner.getTextPos(), Diagnostics.Keywords_cannot_contain_escape_characters);
    }
    return nextTokenWithoutCheck();
}
```

### nextTokenJSDoc
```ts
function nextTokenJSDoc(): JSDocSyntaxKind {
    return currentToken = scanner.scanJsDocToken();
}
```

### reScanGreaterToken
```ts
function reScanGreaterToken(): SyntaxKind {
    return currentToken = scanner.reScanGreaterToken();
}
```

### reScanSlashToken
```ts
function reScanSlashToken(): SyntaxKind {
    return currentToken = scanner.reScanSlashToken();
}
```

### reScanTemplateToken
```ts
function reScanTemplateToken(): SyntaxKind {
    return currentToken = scanner.reScanTemplateToken();
}
```

### reScanLessThanToken
```ts
function reScanLessThanToken(): SyntaxKind {
    return currentToken = scanner.reScanLessThanToken();
}
```

### scanJsxIdentifier
```ts
function scanJsxIdentifier(): SyntaxKind {
    return currentToken = scanner.scanJsxIdentifier();
}
```

### scanJsxText
```ts
function scanJsxText(): SyntaxKind {
    return currentToken = scanner.scanJsxToken();
}
```

### scanJsxAttributeValue
```ts
function scanJsxAttributeValue(): SyntaxKind {
    return currentToken = scanner.scanJsxAttributeValue();
}
```

### speculationHelper
```ts
function speculationHelper<T>(callback: () => T, isLookAhead: boolean): T {
    ...
}
```

### lookAhead
```ts
function lookAhead<T>(callback: () => T): T {
    return speculationHelper(callback, /*isLookAhead*/ true);
}
```

### tryParse
```ts
function tryParse<T>(callback: () => T): T {
    return speculationHelper(callback, /*isLookAhead*/ false);
}
```

### isIdentifier
```ts
function isIdentifier(): boolean {
    if (token() === SyntaxKind.Identifier) {
        return true;
    }
    if (token() === SyntaxKind.YieldKeyword && inYieldContext()) {
        return false;
    }
    if (token() === SyntaxKind.AwaitKeyword && inAwaitContext()) {
        return false;
    }
    return token() > SyntaxKind.LastReservedWord;
}
```

### parseExpected
```ts
function parseExpected(kind: SyntaxKind, diagnosticMessage?: DiagnosticMessage, shouldAdvance = true): boolean {
    if (token() === kind) {
        if (shouldAdvance) {
            nextToken();
        }
        return true;
    }

    if (diagnosticMessage) {
        parseErrorAtCurrentToken(diagnosticMessage);
    }
    else {
        parseErrorAtCurrentToken(Diagnostics._0_expected, tokenToString(kind));
    }
    return false;
}
```

### parseExpectedJSDoc
```ts
function parseExpectedJSDoc(kind: JSDocSyntaxKind) {
    if (token() === kind) {
        nextTokenJSDoc();
        return true;
    }
    parseErrorAtCurrentToken(Diagnostics._0_expected, tokenToString(kind));
    return false;
}
```

### parseOptional
```ts
function parseOptional(t: SyntaxKind): boolean {
    if (token() === t) {
        nextToken();
        return true;
    }
    return false;
}
```

### parseOptionalToken
```ts
function parseOptionalToken<TKind extends SyntaxKind>(t: TKind): Token<TKind>;
function parseOptionalToken(t: SyntaxKind): Node | undefined {
    if (token() === t) {
        return parseTokenNode();
    }
    return undefined;
}
```

### parseOptionalTokenJSDoc
```ts
function parseOptionalTokenJSDoc<TKind extends JSDocSyntaxKind>(t: TKind): Token<TKind>;
function parseOptionalTokenJSDoc(t: JSDocSyntaxKind): Node | undefined {
    if (token() === t) {
        return parseTokenNodeJSDoc();
    }
    return undefined;
}
```

### parseExpectedToken
```ts
function parseExpectedToken<TKind extends SyntaxKind>(t: TKind, diagnosticMessage?: DiagnosticMessage, arg0?: any): Token<TKind>;
function parseExpectedToken(t: SyntaxKind, diagnosticMessage?: DiagnosticMessage, arg0?: any): Node {
    return parseOptionalToken(t) ||
        createMissingNode(t, /*reportAtCurrentPosition*/ false, diagnosticMessage || Diagnostics._0_expected, arg0 || tokenToString(t));
}
```

### parseExpectedTokenJSDoc
```ts
function parseExpectedTokenJSDoc<TKind extends JSDocSyntaxKind>(t: TKind): Token<TKind>;
function parseExpectedTokenJSDoc(t: JSDocSyntaxKind): Node {
    return parseOptionalTokenJSDoc(t) ||
        createMissingNode(t, /*reportAtCurrentPosition*/ false, Diagnostics._0_expected, tokenToString(t));
}
```

### parseTokenNode
```ts
function parseTokenNode<T extends Node>(): T {
    const node = <T>createNode(token());
    nextToken();
    return finishNode(node);
}
```

### parseTokenNodeJSDoc
```ts
function parseTokenNodeJSDoc<T extends Node>(): T {
    const node = <T>createNode(token());
    nextTokenJSDoc();
    return finishNode(node);
}
```

### canParseSemicolon
```ts
function canParseSemicolon() {
    if (token() === SyntaxKind.SemicolonToken) {
        return true;
    }

    return token() === SyntaxKind.CloseBraceToken || token() === SyntaxKind.EndOfFileToken || scanner.hasPrecedingLineBreak();
}
```
### parseSemicolon
```ts
function parseSemicolon(): boolean {
if (canParseSemicolon()) {
    if (token() === SyntaxKind.SemicolonToken) {
            nextToken();
        }
        return true;
    }
    else {
        return parseExpected(SyntaxKind.SemicolonToken);
    }
}
```

### createNode
```ts
function createNode(kind: SyntaxKind, pos?: number): Node {
    nodeCount++;
    const p = pos! >= 0 ? pos! : scanner.getStartPos();
    return isNodeKind(kind) || kind === SyntaxKind.Unknown ? new NodeConstructor(kind, p, p) :
        kind === SyntaxKind.Identifier ? new IdentifierConstructor(kind, p, p) :
        new TokenConstructor(kind, p, p);
}
```

### createNodeWithJSDoc
### createNodeArray
### finishNode
### createMissingNode
### internIdentifier
### createIdentifier
### parseIdentifier
### parseIdentifierName
### isLiteralPropertyName
### parsePropertyNameWorker
### parsePropertyName
### parseComputedPropertyName
### parseContextualModifier
### nextTokenIsOnSameLineAndCanFollowModifier
### nextTokenCanFollowModifier
### parseAnyContextualModifier
### canFollowModifier
### nextTokenCanFollowDefaultKeyword
### isListElement 
### isValidHeritageClauseObjectLiteral
### nextTokenIsIdentifier
### nextTokenIsIdentifierOrKeyword
### nextTokenIsIdentifierOrKeywordOrGreaterThan
### isHeritageClauseExtendsOrImplementsKeyword
### nextTokenIsStartOfExpression
### nextTokenIsStartOfType
### isListTerminator
### isVariableDeclaratorListTerminator
### isInSomeParsingContext
### parseList
### parseListElement
### currentNode
### consumeNode
### isReusableParsingContext
### canReuseNode
### isReusableClassMember
### isReusableSwitchClause
### isReusableStatement
### isReusableEnumMember
### isReusableTypeMember
### isReusableVariableDeclaration
### isReusableParameter
### abortParsingListOrMoveToNextToken
### parsingContextErrors
### parseDelimitedList
### getExpectedCommaDiagnostic
### createMissingList
### isMissingList
### parseBracketedList
### parseEntityName
### createQualifiedName
### parseRightSideOfDot
### parseTemplateExpression
### parseTemplateSpan
### parseLiteralNode
### parseTemplateHead
### parseTemplateMiddleOrTemplateTail
### parseLiteralLikeNode
### parseTypeReference
### typeHasArrowFunctionBlockingParseError
### parseThisTypePredicate
### parseThisTypeNode
### parseJSDocAllType
### parseJSDocNonNullableType
### parseJSDocUnknownOrNullableType
### parseJSDocFunctionType
### parseJSDocParameter
### parseJSDocType
### parseTypeQuery
### parseTypeParameter
### parseTypeParameters
### parseParameterType
### isStartOfParameter
### parseParameter
### fillSignature
### shouldParseReturnType
### parseParameterList
### parseTypeMemberSemicolon
### parseSignatureMember
### isIndexSignature
### isUnambiguouslyIndexSignature
### parseIndexSignatureDeclaration
### parsePropertyOrMethodSignature
### isTypeMemberStart
### parseTypeMember
### nextTokenIsOpenParenOrLessThan
### nextTokenIsDot
### nextTokenIsOpenParenOrLessThanOrDot
### parseTypeLiteral
### parseObjectTypeMembers
### isStartOfMappedType
### parseMappedTypeParameter
### parseMappedType
### parseTupleElementType
### parseTupleType
### parseParenthesizedType
### parseFunctionOrConstructorType
### parseKeywordAndNoDot
### parseLiteralTypeNode
### isStartOfTypeOfImportType
### parseImportType
### nextTokenIsNumericOrBigIntLiteral
### parseNonArrayType
### isStartOfType
### isStartOfParenthesizedOrFunctionType
### parsePostfixTypeOrHigher
### createPostfixType
### parseTypeOperator
### parseInferType
### parseTypeOperatorOrHigher
### parseUnionOrIntersectionType
### parseIntersectionTypeOrHigher
### parseUnionTypeOrHigher
### isStartOfFunctionType
### skipParameterStart
### isUnambiguouslyStartOfFunctionType
### parseTypeOrTypePredicate
### parseTypePredicatePrefix
### parseAssertsTypePredicate
### parseType
### parseTypeWorker
### parseTypeAnnotation
### isStartOfLeftHandSideExpression
### isStartOfExpression
### isStartOfExpressionStatement
### parseExpression
### parseInitializer
### parseAssignmentExpressionOrHigher
### nextTokenIsIdentifierOnSameLine
### parseYieldExpression
### parseSimpleArrowFunctionExpression
### tryParseParenthesizedArrowFunctionExpression
### isParenthesizedArrowFunctionExpression
### isParenthesizedArrowFunctionExpressionWorker
### parsePossibleParenthesizedArrowFunctionExpressionHead
### tryParseAsyncSimpleArrowFunctionExpression
### isUnParenthesizedAsyncArrowFunctionWorker
### parseParenthesizedArrowFunctionExpressionHead
### parseArrowFunctionExpressionBody
### parseConditionalExpressionRest
### parseBinaryExpressionOrHigher
### isInOrOfKeyword
### parseBinaryExpressionRest
### isBinaryOperator
### makeBinaryExpression
### makeAsExpression
### parsePrefixUnaryExpression
### parseDeleteExpression
### parseTypeOfExpression
### parseVoidExpression
### isAwaitExpression
### parseAwaitExpression
### parseUnaryExpressionOrHigher
### parseSimpleUnaryExpression
### isUpdateExpression
### parseUpdateExpression
### parseLeftHandSideExpressionOrHigher
### parseMemberExpressionOrHigher
### parseSuperExpression
### parseJsxElementOrSelfClosingElementOrFragment
### parseJsxText
### parseJsxChild
### parseJsxChildren
### parseJsxAttributes
### parseJsxOpeningOrSelfClosingElementOrOpeningFragment
### parseJsxElementName
### parseJsxExpression
### 

# (namespace:ts.IncrementalParser)

## 类型接口
## 功能函数
```ts
export function updateSourceFile(sourceFile: SourceFile, newText: string, textChangeRange: TextChangeRange, aggressiveChecks: boolean): SourceFile {
    ...
}
```

## 内部函数
### moveElementEntirelyPastChangeRange
```ts
function moveElementEntirelyPastChangeRange(element: IncrementalElement, isArray: boolean, delta: number, oldText: string, newText: string, aggressiveChecks: boolean) {
    ...
}
```