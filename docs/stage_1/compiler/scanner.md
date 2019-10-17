

## 类型接口
### ErrorCallback
```ts
export type ErrorCallback = (message: DiagnosticMessage, length: number) => void;
```
### Scanner
> Scanner接口类型
```ts
export interface Scanner {
    getStartPos(): number;
    getToken(): SyntaxKind;
    getTextPos(): number;
    getTokenPos(): number;
    getTokenText(): string;
    getTokenValue(): string;
    hasUnicodeEscape(): boolean;
    hasExtendedUnicodeEscape(): boolean;
    hasPrecedingLineBreak(): boolean;
    isIdentifier(): boolean;
    isReservedWord(): boolean;
    isUnterminated(): boolean;
    /* @internal */
    getTokenFlags(): TokenFlags;
    reScanGreaterToken(): SyntaxKind;
    reScanSlashToken(): SyntaxKind;
    reScanTemplateToken(): SyntaxKind;
    scanJsxIdentifier(): SyntaxKind;
    scanJsxAttributeValue(): SyntaxKind;
    reScanJsxToken(): JsxTokenSyntaxKind;
    reScanLessThanToken(): SyntaxKind;
    reScanQuestionToken(): SyntaxKind;
    scanJsxToken(): JsxTokenSyntaxKind;
    scanJsDocToken(): JSDocSyntaxKind;
    scan(): SyntaxKind;
    getText(): string;
    setText(text: string | undefined, start?: number, length?: number): void;
    setOnError(onError: ErrorCallback | undefined): void;
    setScriptTarget(scriptTarget: ScriptTarget): void;
    setLanguageVariant(variant: LanguageVariant): void;
    setTextPos(textPos: number): void;
    /* @internal */
    setInJSDocType(inType: boolean): void;
    lookAhead<T>(callback: () => T): T;
    scanRange<T>(start: number, length: number, callback: () => T): T;
    tryScan<T>(callback: () => T): T;
}
```

## 实例对象
###  textToKeywordObj
> 关键词字符
```ts
const textToKeywordObj: MapLike<KeywordSyntaxKind> = {
    abstract: SyntaxKind.AbstractKeyword,
    any: SyntaxKind.AnyKeyword,
    as: SyntaxKind.AsKeyword,
    asserts: SyntaxKind.AssertsKeyword,
    bigint: SyntaxKind.BigIntKeyword,
    boolean: SyntaxKind.BooleanKeyword,
    break: SyntaxKind.BreakKeyword,
    case: SyntaxKind.CaseKeyword,
    catch: SyntaxKind.CatchKeyword,
    class: SyntaxKind.ClassKeyword,
    continue: SyntaxKind.ContinueKeyword,
    const: SyntaxKind.ConstKeyword,
    ["" + "constructor"]: SyntaxKind.ConstructorKeyword,
    debugger: SyntaxKind.DebuggerKeyword,
    declare: SyntaxKind.DeclareKeyword,
    default: SyntaxKind.DefaultKeyword,
    delete: SyntaxKind.DeleteKeyword,
    do: SyntaxKind.DoKeyword,
    else: SyntaxKind.ElseKeyword,
    enum: SyntaxKind.EnumKeyword,
    export: SyntaxKind.ExportKeyword,
    extends: SyntaxKind.ExtendsKeyword,
    false: SyntaxKind.FalseKeyword,
    finally: SyntaxKind.FinallyKeyword,
    for: SyntaxKind.ForKeyword,
    from: SyntaxKind.FromKeyword,
    function: SyntaxKind.FunctionKeyword,
    get: SyntaxKind.GetKeyword,
    if: SyntaxKind.IfKeyword,
    implements: SyntaxKind.ImplementsKeyword,
    import: SyntaxKind.ImportKeyword,
    in: SyntaxKind.InKeyword,
    infer: SyntaxKind.InferKeyword,
    instanceof: SyntaxKind.InstanceOfKeyword,
    interface: SyntaxKind.InterfaceKeyword,
    is: SyntaxKind.IsKeyword,
    keyof: SyntaxKind.KeyOfKeyword,
    let: SyntaxKind.LetKeyword,
    module: SyntaxKind.ModuleKeyword,
    namespace: SyntaxKind.NamespaceKeyword,
    never: SyntaxKind.NeverKeyword,
    new: SyntaxKind.NewKeyword,
    null: SyntaxKind.NullKeyword,
    number: SyntaxKind.NumberKeyword,
    object: SyntaxKind.ObjectKeyword,
    package: SyntaxKind.PackageKeyword,
    private: SyntaxKind.PrivateKeyword,
    protected: SyntaxKind.ProtectedKeyword,
    public: SyntaxKind.PublicKeyword,
    readonly: SyntaxKind.ReadonlyKeyword,
    require: SyntaxKind.RequireKeyword,
    global: SyntaxKind.GlobalKeyword,
    return: SyntaxKind.ReturnKeyword,
    set: SyntaxKind.SetKeyword,
    static: SyntaxKind.StaticKeyword,
    string: SyntaxKind.StringKeyword,
    super: SyntaxKind.SuperKeyword,
    switch: SyntaxKind.SwitchKeyword,
    symbol: SyntaxKind.SymbolKeyword,
    this: SyntaxKind.ThisKeyword,
    throw: SyntaxKind.ThrowKeyword,
    true: SyntaxKind.TrueKeyword,
    try: SyntaxKind.TryKeyword,
    type: SyntaxKind.TypeKeyword,
    typeof: SyntaxKind.TypeOfKeyword,
    undefined: SyntaxKind.UndefinedKeyword,
    unique: SyntaxKind.UniqueKeyword,
    unknown: SyntaxKind.UnknownKeyword,
    var: SyntaxKind.VarKeyword,
    void: SyntaxKind.VoidKeyword,
    while: SyntaxKind.WhileKeyword,
    with: SyntaxKind.WithKeyword,
    yield: SyntaxKind.YieldKeyword,
    async: SyntaxKind.AsyncKeyword,
    await: SyntaxKind.AwaitKeyword,
    of: SyntaxKind.OfKeyword,
};
```

### textToKeyword
```ts
const textToKeyword = createMapFromTemplate(textToKeywordObj);
```
### textToToken
```ts
const textToToken = createMapFromTemplate<SyntaxKind>({
    ...textToKeywordObj,
    "{": SyntaxKind.OpenBraceToken,
    "}": SyntaxKind.CloseBraceToken,
    "(": SyntaxKind.OpenParenToken,
    ")": SyntaxKind.CloseParenToken,
    "[": SyntaxKind.OpenBracketToken,
    "]": SyntaxKind.CloseBracketToken,
    ".": SyntaxKind.DotToken,
    "...": SyntaxKind.DotDotDotToken,
    ";": SyntaxKind.SemicolonToken,
    ",": SyntaxKind.CommaToken,
    "<": SyntaxKind.LessThanToken,
    ">": SyntaxKind.GreaterThanToken,
    "<=": SyntaxKind.LessThanEqualsToken,
    ">=": SyntaxKind.GreaterThanEqualsToken,
    "==": SyntaxKind.EqualsEqualsToken,
    "!=": SyntaxKind.ExclamationEqualsToken,
    "===": SyntaxKind.EqualsEqualsEqualsToken,
    "!==": SyntaxKind.ExclamationEqualsEqualsToken,
    "=>": SyntaxKind.EqualsGreaterThanToken,
    "+": SyntaxKind.PlusToken,
    "-": SyntaxKind.MinusToken,
    "**": SyntaxKind.AsteriskAsteriskToken,
    "*": SyntaxKind.AsteriskToken,
    "/": SyntaxKind.SlashToken,
    "%": SyntaxKind.PercentToken,
    "++": SyntaxKind.PlusPlusToken,
    "--": SyntaxKind.MinusMinusToken,
    "<<": SyntaxKind.LessThanLessThanToken,
    "</": SyntaxKind.LessThanSlashToken,
    ">>": SyntaxKind.GreaterThanGreaterThanToken,
    ">>>": SyntaxKind.GreaterThanGreaterThanGreaterThanToken,
    "&": SyntaxKind.AmpersandToken,
    "|": SyntaxKind.BarToken,
    "^": SyntaxKind.CaretToken,
    "!": SyntaxKind.ExclamationToken,
    "~": SyntaxKind.TildeToken,
    "&&": SyntaxKind.AmpersandAmpersandToken,
    "||": SyntaxKind.BarBarToken,
    "?": SyntaxKind.QuestionToken,
    "??": SyntaxKind.QuestionQuestionToken,
    "?.": SyntaxKind.QuestionDotToken,
    ":": SyntaxKind.ColonToken,
    "=": SyntaxKind.EqualsToken,
    "+=": SyntaxKind.PlusEqualsToken,
    "-=": SyntaxKind.MinusEqualsToken,
    "*=": SyntaxKind.AsteriskEqualsToken,
    "**=": SyntaxKind.AsteriskAsteriskEqualsToken,
    "/=": SyntaxKind.SlashEqualsToken,
    "%=": SyntaxKind.PercentEqualsToken,
    "<<=": SyntaxKind.LessThanLessThanEqualsToken,
    ">>=": SyntaxKind.GreaterThanGreaterThanEqualsToken,
    ">>>=": SyntaxKind.GreaterThanGreaterThanGreaterThanEqualsToken,
    "&=": SyntaxKind.AmpersandEqualsToken,
    "|=": SyntaxKind.BarEqualsToken,
    "^=": SyntaxKind.CaretEqualsToken,
    "@": SyntaxKind.AtToken,
    "`": SyntaxKind.BacktickToken
});
```

### unicode
```ts
const unicodeES3IdentifierStart = [
    ...
]
const unicodeES3IdentifierPart = [
    ...
]
const unicodeES5IdentifierStart = [
    ...
]
const unicodeES5IdentifierPart = [
    ...
]
const unicodeESNextIdentifierStart = [
    ...
]
const unicodeESNextIdentifierPart = [
    ...
]
``` 

## tokenStrings
```ts
const tokenStrings = makeReverseMap(textToToken);
```

### mergeConflictMarkerLength
```ts
const mergeConflictMarkerLength = "<<<<<<<".length;
```

### shebangTriviaRegex
```ts
const shebangTriviaRegex = /^#!.*/;
```
## 功能函数
### tokenIsIdentifierOrKeyword
> 关键字和标志符判断
```ts
export function tokenIsIdentifierOrKeyword(token: SyntaxKind): boolean {
    return token >= SyntaxKind.Identifier;
}
```
### tokenToString
```ts
export function tokenToString(t: SyntaxKind): string | undefined {
    return tokenStrings[t];
}
```
### stringToToken
```ts
export function stringToToken(s: string): SyntaxKind | undefined {
    return textToToken.get(s);
}
```

### computeLineStarts
```ts
export function computeLineStarts(text: string): number[] {
}
```

### getPositionOfLineAndCharacter
```ts
export function getPositionOfLineAndCharacter(sourceFile: SourceFileLike, line: number, character: number): number;
/* @internal */
export function getPositionOfLineAndCharacter(sourceFile: SourceFileLike, line: number, character: number, allowEdits?: true): number; // eslint-disable-line @typescript-eslint/unified-signatures
export function getPositionOfLineAndCharacter(sourceFile: SourceFileLike, line: number, character: number, allowEdits?: true): number {
    ...
}
```

### computePositionOfLineAndCharacter
```ts
export function computePositionOfLineAndCharacter(lineStarts: readonly number[], line: number, character: number, debugText?: string, allowEdits?: true): number {
    ...
}
```

### getLineStarts
```ts
export function getLineStarts(sourceFile: SourceFileLike): readonly number[] {
    return sourceFile.lineMap || (sourceFile.lineMap = computeLineStarts(sourceFile.text));
}
```

### computeLineAndCharacterOfPosition
```ts
export function computeLineAndCharacterOfPosition(lineStarts: readonly number[], position: number): LineAndCharacter {
    ...
}
```
### getLineAndCharacterOfPosition
```ts
export function getLineAndCharacterOfPosition(sourceFile: SourceFileLike, position: number): LineAndCharacter {
```

### isWhiteSpaceLike
```ts
export function isWhiteSpaceLike(ch: number): boolean {
    return isWhiteSpaceSingleLine(ch) || isLineBreak(ch);
}
```

### isWhiteSpaceSingleLine
```ts
 export function isWhiteSpaceSingleLine(ch: number): boolean {
    return ch === CharacterCodes.space ||
        ch === CharacterCodes.tab ||
        ch === CharacterCodes.verticalTab ||
        ch === CharacterCodes.formFeed ||
        ch === CharacterCodes.nonBreakingSpace ||
        ch === CharacterCodes.nextLine ||
        ch === CharacterCodes.ogham ||
        ch >= CharacterCodes.enQuad && ch <= CharacterCodes.zeroWidthSpace ||
        ch === CharacterCodes.narrowNoBreakSpace ||
        ch === CharacterCodes.mathematicalSpace ||
        ch === CharacterCodes.ideographicSpace ||
        ch === CharacterCodes.byteOrderMark;
}
```

### isLineBreak
```ts
export function isLineBreak(ch: number): boolean {
     return ch === CharacterCodes.lineFeed ||
            ch === CharacterCodes.carriageReturn ||
            ch === CharacterCodes.lineSeparator ||
            ch === CharacterCodes.paragraphSeparator;
}
```

### isDigit
```ts
function isDigit(ch: number): boolean {
    return ch >= CharacterCodes._0 && ch <= CharacterCodes._9;
}
```

### isOctalDigit
```ts
export function isOctalDigit(ch: number): boolean {
    return ch >= CharacterCodes._0 && ch <= CharacterCodes._7;
}
```

### couldStartTrivia
```ts
export function couldStartTrivia(text: string, pos: number): boolean {
    // Keep in sync with skipTrivia
    const ch = text.charCodeAt(pos);
    switch (ch) {
        case CharacterCodes.carriageReturn:
        case CharacterCodes.lineFeed:
        case CharacterCodes.tab:
        case CharacterCodes.verticalTab:
        case CharacterCodes.formFeed:
        case CharacterCodes.space:
        case CharacterCodes.slash:
            // starts of normal trivia
            // falls through
        case CharacterCodes.lessThan:
        case CharacterCodes.bar:
        case CharacterCodes.equals:
        case CharacterCodes.greaterThan:
            // Starts of conflict marker trivia
            return true;
        case CharacterCodes.hash:
            // Only if its the beginning can we have #! trivia
            return pos === 0;
        default:
            return ch > CharacterCodes.maxAsciiCharacter;
    }
}
```
### skipTrivia
```ts
export function skipTrivia(text: string, pos: number, stopAfterLineBreak?: boolean, stopAtComments = false): number {
    ...
}
```

### isShebangTrivia
### scanShebangTrivia

### forEachLeadingCommentRange
```ts
export function forEachLeadingCommentRange<U>(text: string, pos: number, cb: (pos: number, end: number, kind: CommentKind, hasTrailingNewLine: boolean) => U): U | undefined;
export function forEachLeadingCommentRange<T, U>(text: string, pos: number, cb: (pos: number, end: number, kind: CommentKind, hasTrailingNewLine: boolean, state: T) => U, state: T): U | undefined;
export function forEachLeadingCommentRange<T, U>(text: string, pos: number, cb: (pos: number, end: number, kind: CommentKind, hasTrailingNewLine: boolean, state: T) => U, state?: T): U | undefined {
    return iterateCommentRanges(/*reduce*/ false, text, pos, /*trailing*/ false, cb, state);
}
```

### forEachTrailingCommentRange
```ts
export function forEachTrailingCommentRange<U>(text: string, pos: number, cb: (pos: number, end: number, kind: CommentKind, hasTrailingNewLine: boolean) => U): U | undefined;
export function forEachTrailingCommentRange<T, U>(text: string, pos: number, cb: (pos: number, end: number, kind: CommentKind, hasTrailingNewLine: boolean, state: T) => U, state: T): U | undefined;
export function forEachTrailingCommentRange<T, U>(text: string, pos: number, cb: (pos: number, end: number, kind: CommentKind, hasTrailingNewLine: boolean, state: T) => U, state?: T): U | undefined {
    return iterateCommentRanges(/*reduce*/ false, text, pos, /*trailing*/ true, cb, state);
}
```

### reduceEachLeadingCommentRange
```ts
export function reduceEachLeadingCommentRange<T, U>(text: string, pos: number, cb: (pos: number, end: number, kind: CommentKind, hasTrailingNewLine: boolean, state: T, memo: U) => U, state: T, initial: U) {
    return iterateCommentRanges(/*reduce*/ true, text, pos, /*trailing*/ false, cb, state, initial);
}
```

### reduceEachTrailingCommentRange
```ts
export function reduceEachTrailingCommentRange<T, U>(text: string, pos: number, cb: (pos: number, end: number, kind: CommentKind, hasTrailingNewLine: boolean, state: T, memo: U) => U, state: T, initial: U) {
    return iterateCommentRanges(/*reduce*/ true, text, pos, /*trailing*/ true, cb, state, initial);
}
```
### getLeadingCommentRanges
```ts
export function getLeadingCommentRanges(text: string, pos: number): CommentRange[] | undefined {
    return reduceEachLeadingCommentRange(text, pos, appendCommentRange, /*state*/ undefined, /*initial*/ undefined);
}
```
### getTrailingCommentRanges
```ts
export function getTrailingCommentRanges(text: string, pos: number): CommentRange[] | undefined {
    return reduceEachTrailingCommentRange(text, pos, appendCommentRange, /*state*/ undefined, /*initial*/ undefined);
}
```
### getShebang
```ts
export function getShebang(text: string): string | undefined {
    const match = shebangTriviaRegex.exec(text);
    if (match) {
        return match[0];
    }
}
```

### isIdentifierStart
```ts
export function isIdentifierStart(ch: number, languageVersion: ScriptTarget | undefined): boolean {
    return ch >= CharacterCodes.A && ch <= CharacterCodes.Z 
        || ch >= CharacterCodes.a && ch <= CharacterCodes.z 
        || ch === CharacterCodes.$ 
        || ch === CharacterCodes._ 
        || ch > CharacterCodes.maxAsciiCharacter && isUnicodeIdentifierStart(ch, languageVersion);
}
```
### isIdentifierPart
```ts
export function isIdentifierPart(ch: number, languageVersion: ScriptTarget | undefined): boolean {
    return ch >= CharacterCodes.A && ch <= CharacterCodes.Z 
        || ch >= CharacterCodes.a && ch <= CharacterCodes.z 
        || ch >= CharacterCodes._0 && ch <= CharacterCodes._9 
        || ch === CharacterCodes.$ || ch === CharacterCodes._ 
        || ch > CharacterCodes.maxAsciiCharacter && isUnicodeIdentifierPart(ch, languageVersion);
}
```

### isIdentifierText
```ts
export function isIdentifierText(name: string, languageVersion: ScriptTarget | undefined): boolean {
    let ch = codePointAt(name, 0);
    if (!isIdentifierStart(ch, languageVersion)) {
        return false;
    }

    for (let i = charSize(ch); i < name.length; i += charSize(ch)) {
        if (!isIdentifierPart(ch = codePointAt(name, i), languageVersion)) {
            return false;
        }
    }

    return true;
}
```

###  createScanner
> 创建Scanner
```ts
export function createScanner(languageVersion: ScriptTarget,
    skipTrivia: boolean,
    languageVariant = LanguageVariant.Standard,
    textInitial?: string,
    onError?: ErrorCallback,
    start?: number,
    length?: number): Scanner {
        let text = textInitial!;

        // Current position (end position of text of current token)
        let pos: number;


        // end of text
        let end: number;

        // Start position of whitespace before current token
        let startPos: number;

        // Start position of text of current token
        let tokenPos: number;

        let token: SyntaxKind;
        let tokenValue!: string;
        let tokenFlags: TokenFlags;

        let inJSDocType = 0;

        setText(text, start, length);

        const scanner: Scanner = {
            getStartPos: () => startPos,
            getTextPos: () => pos,
            getToken: () => token,
            getTokenPos: () => tokenPos,
            getTokenText: () => text.substring(tokenPos, pos),
            getTokenValue: () => tokenValue,
            hasUnicodeEscape: () => (tokenFlags & TokenFlags.UnicodeEscape) !== 0,
            hasExtendedUnicodeEscape: () => (tokenFlags & TokenFlags.ExtendedUnicodeEscape) !== 0,
            hasPrecedingLineBreak: () => (tokenFlags & TokenFlags.PrecedingLineBreak) !== 0,
            isIdentifier: () => token === SyntaxKind.Identifier || token > SyntaxKind.LastReservedWord,
            isReservedWord: () => token >= SyntaxKind.FirstReservedWord && token <= SyntaxKind.LastReservedWord,
            isUnterminated: () => (tokenFlags & TokenFlags.Unterminated) !== 0,
            getTokenFlags: () => tokenFlags,
            reScanGreaterToken,
            reScanSlashToken,
            reScanTemplateToken,
            scanJsxIdentifier,
            scanJsxAttributeValue,
            reScanJsxToken,
            reScanLessThanToken,
            reScanQuestionToken,
            scanJsxToken,
            scanJsDocToken,
            scan,
            getText,
            setText,
            setScriptTarget,
            setLanguageVariant,
            setOnError,
            setTextPos,
            setInJSDocType,
            tryScan,
            lookAhead,
            scanRange,
        };
    ...
}
```

## 内部函数
### lookupInUnicodeMap
```ts
function lookupInUnicodeMap(code: number, map: readonly number[]): boolean {
    ...
}
```

### isUnicodeIdentifierStart
```ts
export function isUnicodeIdentifierStart(code: number, languageVersion: ScriptTarget | undefined) {
    ...
}
``` 
### isUnicodeIdentifierPart
```ts
function isUnicodeIdentifierPart(code: number, languageVersion: ScriptTarget | undefined) {
    ...
}
```

### makeReverseMap
```ts
 function makeReverseMap(source: Map<number>): string[] {
    const result: string[] = [];
    source.forEach((value, name) => {
        result[value] = name;
    });
    return result;
}
```

### isConflictMarkerTrivia
```ts
function isConflictMarkerTrivia(text: string, pos: number) {
}
```

### scanConflictMarkerTrivia
```ts
function scanConflictMarkerTrivia(text: string, pos: number, error?: (diag: DiagnosticMessage, pos?: number, len?: number) => void) {
    ...
}
```

### iterateCommentRanges
```ts
function iterateCommentRanges<T, U>(reduce: boolean, text: string, pos: number, trailing: boolean, cb: (pos: number, end: number, kind: CommentKind, hasTrailingNewLine: boolean, state: T, memo: U | undefined) => U, state: T, initial?: U): U | undefined {
    ...
}
```

### appendCommentRange
```ts
function appendCommentRange(pos: number, end: number, kind: CommentKind, hasTrailingNewLine: boolean, _state: any, comments: CommentRange[]) {
    if (!comments) {
        comments = [];
    }

    comments.push({ kind, pos, end, hasTrailingNewLine });
    return comments;
}
```

### codePointAt
```ts
const codePointAt: (s: string, i: number) => number = (String.prototype as any).codePointAt ? (s, i) => (s as any).codePointAt(i) : function codePointAt(str, i): number {
```

### charSize
```ts
function charSize(ch: number) {
    if (ch >= 0x10000) {
        return 2;
    }
    return 1;
}
```