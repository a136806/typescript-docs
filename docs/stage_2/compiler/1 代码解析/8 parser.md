## 功能说明
> 文件语法解析

## 核心操作
### createSourceFile
> 解析指定文件到`SourceFile`
```ts
export function createSourceFile(
    fileName: string, 
    sourceText: string, 
    languageVersion: ScriptTarget, 
    setParentNodes = false, 
    scriptKind?: ScriptKind
): SourceFile {
    ....
}
```

### Parser.parseSourceFile
```ts
export function parseSourceFile(
    fileName: string, 
    sourceText: string, 
    languageVersion: ScriptTarget, 
    syntaxCursor: IncrementalParser.SyntaxCursor | undefined, 
    setParentNodes = false, 
    scriptKind?: ScriptKind
): SourceFile {
    ...
}
```

## 重要流程 
### parseStatement
-  `parseStatement`
    - `parseEmptyStatement`
    ```ts
    /* ; */
    function parseEmptyStatement(): Statement { 
        const node = <Statement>createNode(SyntaxKind.EmptyStatement);
        parseExpected(SyntaxKind.SemicolonToken);
        return finishNode(node);
    }

    export interface Statement extends Node {
        _statementBrand: any;
    }

    ```
    - `parseBlock`
    ```ts
    /* {...} */
    function parseBlock(ignoreMissingOpenBrace: boolean, diagnosticMessage?: DiagnosticMessage): Block {
        const node = <Block>createNode(SyntaxKind.Block);

        if (parseExpected(SyntaxKind.OpenBraceToken, diagnosticMessage) || ignoreMissingOpenBrace) {
            if (scanner.hasPrecedingLineBreak()) {
                node.multiLine = true;
            }

            node.statements = parseList(ParsingContext.BlockStatements, parseStatement);
            parseExpected(SyntaxKind.CloseBraceToken);
        }
        else {
            node.statements = createMissingList<Statement>();
        }

        return finishNode(node);
    }
    export interface Block extends Statement {
        kind: SyntaxKind.Block;
        statements: NodeArray<Statement>;
        /*@internal*/ multiLine?: boolean;
    }
    ```
    - `parseVariableStatement`
    ```ts
    /*var ...| let ...*/
    function parseVariableStatement(node: VariableStatement): VariableStatement {
        node.kind = SyntaxKind.VariableStatement;
        node.declarationList = parseVariableDeclarationList(/*inForStatementInitializer*/ false);
        parseSemicolon();
        return finishNode(node);
    }
    function parseVariableDeclarationList(inForStatementInitializer: boolean): VariableDeclarationList {
        const node = <VariableDeclarationList>createNode(SyntaxKind.VariableDeclarationList);

        switch (token()) {
            case SyntaxKind.VarKeyword:
                break;
            case SyntaxKind.LetKeyword:
                node.flags |= NodeFlags.Let;
                break;
            case SyntaxKind.ConstKeyword:
                node.flags |= NodeFlags.Const;
                break;
            default:
                Debug.fail();
        }

        nextToken();

        if (token() === SyntaxKind.OfKeyword && lookAhead(canFollowContextualOfKeyword)) {
            node.declarations = createMissingList<VariableDeclaration>();
        }else {
            const savedDisallowIn = inDisallowInContext();
            setDisallowInContext(inForStatementInitializer);

            node.declarations = parseDelimitedList(ParsingContext.VariableDeclarations,
                inForStatementInitializer ? parseVariableDeclaration : parseVariableDeclarationAllowExclamation);

            setDisallowInContext(savedDisallowIn);
        }

        return finishNode(node);
    }
    export interface VariableDeclarationList extends Node {
        kind: SyntaxKind.VariableDeclarationList;
        parent: VariableStatement | ForStatement | ForOfStatement | ForInStatement;
        declarations: NodeArray<VariableDeclaration>;
    }
    ```
    - `parseFunctionDeclaration`
    ```ts
    /*function...*/
    function parseFunctionDeclaration(node: FunctionDeclaration): FunctionDeclaration {
        node.kind = SyntaxKind.FunctionDeclaration;
        /* function */
        parseExpected(SyntaxKind.FunctionKeyword);
        /* * */
        node.asteriskToken = parseOptionalToken(SyntaxKind.AsteriskToken);
        node.name = hasModifier(node, ModifierFlags.Default) 
            ? parseOptionalIdentifier() 
            : parseIdentifier();

        /* yield */
        const isGenerator = node.asteriskToken ? SignatureFlags.Yield : SignatureFlags.None;
        /* async */
        const isAsync = hasModifier(node, ModifierFlags.Async) ? SignatureFlags.Await : SignatureFlags.None;

        fillSignature(SyntaxKind.ColonToken, isGenerator | isAsync, node);
        node.body = parseFunctionBlockOrSemicolon(isGenerator | isAsync, Diagnostics.or_expected);

        return finishNode(node);
    }
    ```
    - `parseClassDeclaration`
    ```ts
    /*
        class...
        class声明与class表达式
    */
    function parseClassDeclaration(node: ClassLikeDeclaration): ClassDeclaration {
        return <ClassDeclaration>parseClassDeclarationOrExpression(node, SyntaxKind.ClassDeclaration);
    }
    function parseClassDeclarationOrExpression(node: ClassLikeDeclaration, kind: ClassLikeDeclaration["kind"]): ClassLikeDeclaration {
        node.kind = kind;
        parseExpected(SyntaxKind.ClassKeyword);

        node.name = parseNameOfClassDeclarationOrExpression();
        node.typeParameters = parseTypeParameters();
        node.heritageClauses = parseHeritageClauses();

        if (parseExpected(SyntaxKind.OpenBraceToken)) {
            // ClassTail[Yield,Await] : (Modified) See 14.5
            //      ClassHeritage[?Yield,?Await]opt { ClassBody[?Yield,?Await]opt }
            node.members = parseClassMembers();
            parseExpected(SyntaxKind.CloseBraceToken);
        }
        else {
            node.members = createMissingList<ClassElement>();
        }

        return finishNode(node);
    }
    ```
    - `parseIfStatement`
    ```ts
    /*if...*/
    function parseIfStatement(): IfStatement {
        const node = <IfStatement>createNode(SyntaxKind.IfStatement);
        parseExpected(SyntaxKind.IfKeyword);
        parseExpected(SyntaxKind.OpenParenToken);
        node.expression = allowInAnd(parseExpression);
        parseExpected(SyntaxKind.CloseParenToken);
        node.thenStatement = parseStatement();
        node.elseStatement = parseOptional(SyntaxKind.ElseKeyword) ? parseStatement() : undefined;
        return finishNode(node);
    }

    /*
    if(expression){
        thenStatement;
    }else{
        elseStatement;
    }
    */
    export interface IfStatement extends Statement {
        kind: SyntaxKind.IfStatement;
        expression: Expression; 
        thenStatement: Statement;
        elseStatement?: Statement;
    }
    ```
    - `parseDoStatement`
    ```ts
    /*do...*/
    function parseDoStatement(): DoStatement {
        const node = <DoStatement>createNode(SyntaxKind.DoStatement);
        parseExpected(SyntaxKind.DoKeyword);
        node.statement = parseStatement();
        parseExpected(SyntaxKind.WhileKeyword);
        parseExpected(SyntaxKind.OpenParenToken);
        node.expression = allowInAnd(parseExpression);
        parseExpected(SyntaxKind.CloseParenToken);

        // From: https://mail.mozilla.org/pipermail/es-discuss/2011-August/016188.html
        // 157 min --- All allen at wirfs-brock.com CONF --- "do{;}while(false)false" prohibited in
        // spec but allowed in consensus reality. Approved -- this is the de-facto standard whereby
        //  do;while(0)x will have a semicolon inserted before x.
        parseOptional(SyntaxKind.SemicolonToken);
        return finishNode(node);
    }
    /*
    do{
        statement
    } while();
    */
    export interface DoStatement extends IterationStatement {
        kind: SyntaxKind.DoStatement;
        expression: Expression;
    }

    ```
    - `parseWhileStatement`
    ```ts
    /*while...*/
    function parseWhileStatement(): WhileStatement {
        const node = <WhileStatement>createNode(SyntaxKind.WhileStatement);
        /* while */
        parseExpected(SyntaxKind.WhileKeyword);
        /* ( */
        parseExpected(SyntaxKind.OpenParenToken);
        /*      expression */
        node.expression = allowInAnd(parseExpression);
        /* ) */
        parseExpected(SyntaxKind.CloseParenToken);

        node.statement = parseStatement();
        return finishNode(node);
    }
    /*
    while(expression){
        statement
    }
    */
    export interface WhileStatement extends IterationStatement {
        kind: SyntaxKind.WhileStatement;
        expression: Expression;
    }
    ```
    - `parseForOrForInOrForOfStatement`
    ```ts
    function parseForOrForInOrForOfStatement(): Statement {
        ... 
    }    
    ```
    - `parseBreakOrContinueStatement`

    ```ts
    /*break...|continue...*/
    function parseBreakOrContinueStatement(kind: SyntaxKind): BreakOrContinueStatement {
        const node = <BreakOrContinueStatement>createNode(kind);

        parseExpected(kind === SyntaxKind.BreakStatement ? SyntaxKind.BreakKeyword : SyntaxKind.ContinueKeyword);

        if (!canParseSemicolon()) {
            node.label = parseIdentifier();
        }

        parseSemicolon();
        return finishNode(node);
    }
    export interface BreakStatement extends Statement {
        kind: SyntaxKind.BreakStatement;
        label?: Identifier;
    }

    export interface ContinueStatement extends Statement {
        kind: SyntaxKind.ContinueStatement;
        label?: Identifier;
    }
    export type BreakOrContinueStatement = BreakStatement | ContinueStatement;
    ```
    - `parseReturnStatement`
    ```ts
    /*return...*/
    function parseReturnStatement(): ReturnStatement {
        const node = <ReturnStatement>createNode(SyntaxKind.ReturnStatement);
        /* return */
        parseExpected(SyntaxKind.ReturnKeyword);
        /* ...; */
        if (!canParseSemicolon()) {
            node.expression = allowInAnd(parseExpression);
        }
        /* ; */
        parseSemicolon();
        return finishNode(node);
    }
    export interface ReturnStatement extends Statement {
        kind: SyntaxKind.ReturnStatement;
        expression?: Expression;
    }
    ```
    - `parseWithStatement`
    ```ts
    /*with...*/
    function parseWithStatement(): WithStatement {
        const node = <WithStatement>createNode(SyntaxKind.WithStatement);
        /* with */
        parseExpected(SyntaxKind.WithKeyword);
        /* ( */
        parseExpected(SyntaxKind.OpenParenToken);
        /*   expression */
        node.expression = allowInAnd(parseExpression);
        /* ) */
        parseExpected(SyntaxKind.CloseParenToken);
        node.statement = doInsideOfContext(NodeFlags.InWithStatement, parseStatement);
        return finishNode(node);
    }
    export interface WithStatement extends Statement {
        kind: SyntaxKind.WithStatement;
        expression: Expression;
        statement: Statement;
    }
    ```
    - `parseSwitchStatement`
    ```ts
    /*switch*/
    function parseSwitchStatement(): SwitchStatement {
        const node = <SwitchStatement>createNode(SyntaxKind.SwitchStatement);
        /* switch */
        parseExpected(SyntaxKind.SwitchKeyword);
        /* ( */
        parseExpected(SyntaxKind.OpenParenToken);
        node.expression = allowInAnd(parseExpression);
        /* ) */
        parseExpected(SyntaxKind.CloseParenToken);
        /* case */
        const caseBlock = <CaseBlock>createNode(SyntaxKind.CaseBlock);
        /* { */
        parseExpected(SyntaxKind.OpenBraceToken);
        /*  ... */
        caseBlock.clauses = parseList(ParsingContext.SwitchClauses, parseCaseOrDefaultClause);
        /* } */
        parseExpected(SyntaxKind.CloseBraceToken);
        node.caseBlock = finishNode(caseBlock);
        return finishNode(node);
    }
    export interface SwitchStatement extends Statement {
        kind: SyntaxKind.SwitchStatement;
        expression: Expression;
        caseBlock: CaseBlock;
        possiblyExhaustive?: boolean;
    }
    export interface CaseClause extends Node {
        kind: SyntaxKind.CaseClause;
        parent: CaseBlock;
        expression: Expression;
        statements: NodeArray<Statement>;
    }
    export interface CaseBlock extends Node {
        kind: SyntaxKind.CaseBlock;
        parent: SwitchStatement;
        clauses: NodeArray<CaseOrDefaultClause>;
    }
    export interface DefaultClause extends Node {
        kind: SyntaxKind.DefaultClause;
        parent: CaseBlock;
        statements: NodeArray<Statement>;
    }
    export type CaseOrDefaultClause = CaseClause | DefaultClause;
    ```
    - `parseThrowStatement`
    ```ts
    /*throw...*/
    function parseThrowStatement(): ThrowStatement {
        const node = <ThrowStatement>createNode(SyntaxKind.ThrowStatement);
        parseExpected(SyntaxKind.ThrowKeyword);
        node.expression = scanner.hasPrecedingLineBreak() ? undefined : allowInAnd(parseExpression);
        parseSemicolon();
        return finishNode(node);
    }
    export interface ThrowStatement extends Statement {
        kind: SyntaxKind.ThrowStatement;
        expression?: Expression;
    }
    ```
    - `parseTryStatement`
    ```ts
    function parseTryStatement(): TryStatement {
        const node = <TryStatement>createNode(SyntaxKind.TryStatement);

        parseExpected(SyntaxKind.TryKeyword);
        node.tryBlock = parseBlock(/*ignoreMissingOpenBrace*/ false);
        node.catchClause = token() === SyntaxKind.CatchKeyword ? parseCatchClause() : undefined;

        if (!node.catchClause || token() === SyntaxKind.FinallyKeyword) {
            parseExpected(SyntaxKind.FinallyKeyword);
            node.finallyBlock = parseBlock(/*ignoreMissingOpenBrace*/ false);
        }

        return finishNode(node);
    }
    export interface TryStatement extends Statement {
        kind: SyntaxKind.TryStatement;
        tryBlock: Block;
        catchClause?: CatchClause;
        finallyBlock?: Block;
    }
    export interface CatchClause extends Node {
        kind: SyntaxKind.CatchClause;
        parent: TryStatement;
        variableDeclaration?: VariableDeclaration;
        block: Block;
    }
    ```
    - `parseDebuggerStatement`
    ```ts
    function parseDebuggerStatement(): Statement {
        const node = <Statement>createNode(SyntaxKind.DebuggerStatement);
        parseExpected(SyntaxKind.DebuggerKeyword);
        parseSemicolon();
        return finishNode(node);
    }
    ```
    - `parseDeclaration`
    ```ts
    /*
        @,async,inter,type,module,namespace,
        declare,const,enum,export,import,
        private,protected,public,abstract,static
        readonly,global
    */
    function parseDeclaration(): Statement {
        const modifiers = lookAhead(() => (parseDecorators(), parseModifiers()));
            
        const isAmbient = some(modifiers, isDeclareModifier);
        if (isAmbient) {
            const node = tryReuseAmbientDeclaration();
            if (node) {
                return node;
            }
        }

        const node = <Statement>createNodeWithJSDoc(SyntaxKind.Unknown);
        node.decorators = parseDecorators();
        node.modifiers = parseModifiers();
        if (isAmbient) {
            for (const m of node.modifiers!) {
                m.flags |= NodeFlags.Ambient;
            }
            return doInsideOfContext(NodeFlags.Ambient, () => parseDeclarationWorker(node));
        }
        else {
            return parseDeclarationWorker(node);
        }
    }
    ```
    - `parseExpressionOrLabeledStatement`
    ```ts
    function parseExpressionOrLabeledStatement(): ExpressionStatement | LabeledStatement {
        const node = <ExpressionStatement | LabeledStatement>createNodeWithJSDoc(SyntaxKind.Unknown);
        const expression = allowInAnd(parseExpression);
        
        if (expression.kind === SyntaxKind.Identifier && parseOptional(SyntaxKind.ColonToken)) {
            node.kind = SyntaxKind.LabeledStatement;
            (<LabeledStatement>node).label = <Identifier>expression;
            (<LabeledStatement>node).statement = parseStatement();
        }
        else {
            node.kind = SyntaxKind.ExpressionStatement;
            (<ExpressionStatement>node).expression = expression;
            parseSemicolon();
        }
        return finishNode(node);
    }
    ```
    - `parseExpression`
    ```ts
    function parseExpression(): Expression {
        const saveDecoratorContext = inDecoratorContext();
        if (saveDecoratorContext) {
            setDecoratorContext(/*val*/ false);
        }

        let expr = parseAssignmentExpressionOrHigher();
        let operatorToken: BinaryOperatorToken;
        while ((operatorToken = parseOptionalToken(SyntaxKind.CommaToken))) {
            expr = makeBinaryExpression(expr, operatorToken, parseAssignmentExpressionOrHigher());
        }

        if (saveDecoratorContext) {
            setDecoratorContext(/*val*/ true);
        }
        return expr;
    
    }
    ```
    - `parseAssignmentExpressionOrHigher`
    ```ts
    function parseAssignmentExpressionOrHigher(): Expression {
        /* yield */
        if (isYieldExpression()) {
            return parseYieldExpression();
        }

        /* arrowFunction */
        const arrowExpression = tryParseParenthesizedArrowFunctionExpression() || tryParseAsyncSimpleArrowFunctionExpression();
        if (arrowExpression) {
            return arrowExpression;
        }

        /* BinaryExpression */
        const expr = parseBinaryExpressionOrHigher(/*precedence*/ 0);

        /*  */
        if (expr.kind === SyntaxKind.Identifier && token() === SyntaxKind.EqualsGreaterThanToken) {
            return parseSimpleArrowFunctionExpression(<Identifier>expr);
        }

        /* left = expression */
        if (isLeftHandSideExpression(expr) && isAssignmentOperator(reScanGreaterToken())) {
            return makeBinaryExpression(expr, parseTokenNode(), parseAssignmentExpressionOrHigher());
        }

        return parseConditionalExpressionRest(expr);
    }
    ```

    - `parseYieldExpression`
    ```ts
    function parseYieldExpression(): YieldExpression {
        const node = <YieldExpression>createNode(SyntaxKind.YieldExpression);
        nextToken();

        if (!scanner.hasPrecedingLineBreak() &&
            (token() === SyntaxKind.AsteriskToken || isStartOfExpression())) {
            node.asteriskToken = parseOptionalToken(SyntaxKind.AsteriskToken);
            node.expression = parseAssignmentExpressionOrHigher();
            return finishNode(node);
        }
        else {
            return finishNode(node);
        }
    }
    ```

    - `tryParseParenthesizedArrowFunctionExpression`
    ```ts
    function tryParseParenthesizedArrowFunctionExpression(): Expression | undefined {
        const triState = isParenthesizedArrowFunctionExpression();
        if (triState === Tristate.False) {
            return undefined;
        }

        const arrowFunction = triState === Tristate.True
            ? parseParenthesizedArrowFunctionExpressionHead(/*allowAmbiguity*/ true)
            : tryParse(parsePossibleParenthesizedArrowFunctionExpressionHead);

        if (!arrowFunction) {
        
            return undefined;
        }

        const isAsync = hasModifier(arrowFunction, ModifierFlags.Async);
        const lastToken = token();
        arrowFunction.equalsGreaterThanToken = parseExpectedToken(SyntaxKind.EqualsGreaterThanToken);
        arrowFunction.body = (lastToken === SyntaxKind.EqualsGreaterThanToken || lastToken === SyntaxKind.OpenBraceToken)
            ? parseArrowFunctionExpressionBody(isAsync)
            : parseIdentifier();

        return finishNode(arrowFunction);
    }
    ```
    - `tryParseAsyncSimpleArrowFunctionExpression`
    ```ts
    function tryParseAsyncSimpleArrowFunctionExpression(): ArrowFunction | undefined {
        if (token() === SyntaxKind.AsyncKeyword) {
            if (lookAhead(isUnParenthesizedAsyncArrowFunctionWorker) === Tristate.True) {
                const asyncModifier = parseModifiersForArrowFunction();
                const expr = parseBinaryExpressionOrHigher(/*precedence*/ 0);
                return parseSimpleArrowFunctionExpression(<Identifier>expr, asyncModifier);
            }
        }
        return undefined;
    }
    ```
    - `parseBinaryExpressionOrHigher`
    ```ts
    function parseBinaryExpressionOrHigher(precedence: number): Expression {
        const leftOperand = parseUnaryExpressionOrHigher();
        return parseBinaryExpressionRest(precedence, leftOperand);
    }
    ```
    - `parseSimpleArrowFunctionExpression`
    ```ts
    function parseSimpleArrowFunctionExpression(identifier: Identifier, asyncModifier?: NodeArray<Modifier> | undefined): ArrowFunction {
        Debug.assert(token() === SyntaxKind.EqualsGreaterThanToken, "parseSimpleArrowFunctionExpression should only have been called if we had a =>");

        let node: ArrowFunction;
        if (asyncModifier) {
            node = <ArrowFunction>createNode(SyntaxKind.ArrowFunction, asyncModifier.pos);
            node.modifiers = asyncModifier;
        }
        else {
            node = <ArrowFunction>createNode(SyntaxKind.ArrowFunction, identifier.pos);
        }

        const parameter = <ParameterDeclaration>createNode(SyntaxKind.Parameter, identifier.pos);
        parameter.name = identifier;
        finishNode(parameter);

        node.parameters = createNodeArray<ParameterDeclaration>([parameter], parameter.pos, parameter.end);

        node.equalsGreaterThanToken = parseExpectedToken(SyntaxKind.EqualsGreaterThanToken);
        node.body = parseArrowFunctionExpressionBody(/*isAsync*/ !!asyncModifier);

        return addJSDocComment(finishNode(node));
    }
    ```
    - `parseConditionalExpressionRest`
    ```ts
    function parseConditionalExpressionRest(leftOperand: Expression): Expression {
        const questionToken = parseOptionalToken(SyntaxKind.QuestionToken);
        if (!questionToken) {
            return leftOperand;
        }

        const node = <ConditionalExpression>createNode(SyntaxKind.ConditionalExpression, leftOperand.pos);
        node.condition = leftOperand;
        node.questionToken = questionToken;
        node.whenTrue = doOutsideOfContext(disallowInAndDecoratorContext, parseAssignmentExpressionOrHigher);
        node.colonToken = parseExpectedToken(SyntaxKind.ColonToken);
        node.whenFalse = nodeIsPresent(node.colonToken)
            ? parseAssignmentExpressionOrHigher()
            : createMissingNode(SyntaxKind.Identifier, /*reportAtCurrentPosition*/ false, Diagnostics._0_expected, tokenToString(SyntaxKind.ColonToken));
        return finishNode(node);
    }
    ```

## 核心操作
### createNode
- `createNode`
```ts
function createNode(kind: SyntaxKind, pos?: number): Node {
    nodeCount++;
    const p = pos! >= 0 ? pos! : scanner.getStartPos();
    return isNodeKind(kind) || kind === SyntaxKind.Unknown 
        ? new NodeConstructor(kind, p, p)  /* 高级Node节点 */
            :  kind === SyntaxKind.Identifier   
                ? new IdentifierConstructor(kind, p, p) /* Identifier变量标志符节点 */
                : new TokenConstructor(kind, p, p);     /* Token特殊字符节点 */
}
- `constructor`
function initializeState(_sourceText: string, languageVersion: ScriptTarget, _syntaxCursor: IncrementalParser.SyntaxCursor | undefined, scriptKind: ScriptKind) {
    NodeConstructor = objectAllocator.getNodeConstructor();
    TokenConstructor = objectAllocator.getTokenConstructor();
    IdentifierConstructor = objectAllocator.getIdentifierConstructor();
    SourceFileConstructor = objectAllocator.getSourceFileConstructor();
}

```
- `objectAllocator`
```ts
export interface ObjectAllocator {
    getNodeConstructor(): new (kind: SyntaxKind, pos?: number, end?: number) => Node;
    getTokenConstructor(): new <TKind extends SyntaxKind>(kind: TKind, pos?: number, end?: number) => Token<TKind>;
    getIdentifierConstructor(): new (kind: SyntaxKind.Identifier, pos?: number, end?: number) => Identifier;
    getSourceFileConstructor(): new (kind: SyntaxKind.SourceFile, pos?: number, end?: number) => SourceFile;
    getSymbolConstructor(): new (flags: SymbolFlags, name: __String) => Symbol;
    getTypeConstructor(): new (checker: TypeChecker, flags: TypeFlags) => Type;
    getSignatureConstructor(): new (checker: TypeChecker) => Signature;
    getSourceMapSourceConstructor(): new (fileName: string, text: string, skipTrivia?: (pos: number) => number) => SourceMapSource;
}
export let objectAllocator: ObjectAllocator = {
    getNodeConstructor: () => <any>Node,
    getTokenConstructor: () => <any>Node,
    getIdentifierConstructor: () => <any>Node,
    getSourceFileConstructor: () => <any>Node,
    getSymbolConstructor: () => <any>Symbol,
    getTypeConstructor: () => <any>Type,
    getSignatureConstructor: () => <any>Signature,
    getSourceMapSourceConstructor: () => <any>SourceMapSource,
};
```
### parseExpected
```ts
function parseExpected(
    kind: SyntaxKind, 
    diagnosticMessage?: DiagnosticMessage, 
    shouldAdvance = true
): boolean {
    if (token() === kind) {
        if (shouldAdvance) {
            nextToken();
        }
        return true;
    }

    // Report specific message if provided with one.  Otherwise, report generic fallback message.
    if (diagnosticMessage) {
        parseErrorAtCurrentToken(diagnosticMessage);
    }
    else {
        parseErrorAtCurrentToken(Diagnostics._0_expected, tokenToString(kind));
    }
    return false;
}
```
### parsexx
> 组成节点解析
### finishNode
```ts
/**/
function finishNode<T extends Node>(node: T, end?: number): T {
    // end
    node.end = end === undefined ? scanner.getStartPos() : end;

    // flags
    if (contextFlags) {
        node.flags |= contextFlags;
    }

    // flags
    if (parseErrorBeforeNextFinishedNode) {
        parseErrorBeforeNextFinishedNode = false;
        node.flags |= NodeFlags.ThisNodeHasError;
    }

    return node;
}
```

## 重要操作
### forEachChild
> 遍历子节点
```ts
export function forEachChild<T>(
    node: Node, 
    cbNode: (node: Node) => T | undefined, 
    cbNodes?: (nodes: NodeArray<Node>) => T | undefined
): T | undefined {
    if (!node || node.kind <= SyntaxKind.LastToken) {
        return;
    }


    switch (node.kind) {
        case SyntaxKind.QualifiedName:
            return visitNode(cbNode, (<QualifiedName>node).left) ||
                visitNode(cbNode, (<QualifiedName>node).right);
        case SyntaxKind.TypeParameter:
            return visitNode(cbNode, (<TypeParameterDeclaration>node).name) ||
                visitNode(cbNode, (<TypeParameterDeclaration>node).constraint) ||
                visitNode(cbNode, (<TypeParameterDeclaration>node).default) ||
                visitNode(cbNode, (<TypeParameterDeclaration>node).expression);
        case SyntaxKind.ShorthandPropertyAssignment:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<ShorthandPropertyAssignment>node).name) ||
                visitNode(cbNode, (<ShorthandPropertyAssignment>node).questionToken) ||
                visitNode(cbNode, (<ShorthandPropertyAssignment>node).exclamationToken) ||
                visitNode(cbNode, (<ShorthandPropertyAssignment>node).equalsToken) ||
                visitNode(cbNode, (<ShorthandPropertyAssignment>node).objectAssignmentInitializer);
        case SyntaxKind.SpreadAssignment:
            return visitNode(cbNode, (<SpreadAssignment>node).expression);
        case SyntaxKind.Parameter:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<ParameterDeclaration>node).dotDotDotToken) ||
                visitNode(cbNode, (<ParameterDeclaration>node).name) ||
                visitNode(cbNode, (<ParameterDeclaration>node).questionToken) ||
                visitNode(cbNode, (<ParameterDeclaration>node).type) ||
                visitNode(cbNode, (<ParameterDeclaration>node).initializer);
        case SyntaxKind.PropertyDeclaration:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<PropertyDeclaration>node).name) ||
                visitNode(cbNode, (<PropertyDeclaration>node).questionToken) ||
                visitNode(cbNode, (<PropertyDeclaration>node).exclamationToken) ||
                visitNode(cbNode, (<PropertyDeclaration>node).type) ||
                visitNode(cbNode, (<PropertyDeclaration>node).initializer);
        case SyntaxKind.PropertySignature:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<PropertySignature>node).name) ||
                visitNode(cbNode, (<PropertySignature>node).questionToken) ||
                visitNode(cbNode, (<PropertySignature>node).type) ||
                visitNode(cbNode, (<PropertySignature>node).initializer);
        case SyntaxKind.PropertyAssignment:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<PropertyAssignment>node).name) ||
                visitNode(cbNode, (<PropertyAssignment>node).questionToken) ||
                visitNode(cbNode, (<PropertyAssignment>node).initializer);
        case SyntaxKind.VariableDeclaration:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<VariableDeclaration>node).name) ||
                visitNode(cbNode, (<VariableDeclaration>node).exclamationToken) ||
                visitNode(cbNode, (<VariableDeclaration>node).type) ||
                visitNode(cbNode, (<VariableDeclaration>node).initializer);
        case SyntaxKind.BindingElement:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<BindingElement>node).dotDotDotToken) ||
                visitNode(cbNode, (<BindingElement>node).propertyName) ||
                visitNode(cbNode, (<BindingElement>node).name) ||
                visitNode(cbNode, (<BindingElement>node).initializer);
        case SyntaxKind.FunctionType:
        case SyntaxKind.ConstructorType:
        case SyntaxKind.CallSignature:
        case SyntaxKind.ConstructSignature:
        case SyntaxKind.IndexSignature:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNodes(cbNode, cbNodes, (<SignatureDeclaration>node).typeParameters) ||
                visitNodes(cbNode, cbNodes, (<SignatureDeclaration>node).parameters) ||
                visitNode(cbNode, (<SignatureDeclaration>node).type);
        case SyntaxKind.MethodDeclaration:
        case SyntaxKind.MethodSignature:
        case SyntaxKind.Constructor:
        case SyntaxKind.GetAccessor:
        case SyntaxKind.SetAccessor:
        case SyntaxKind.FunctionExpression:
        case SyntaxKind.FunctionDeclaration:
        case SyntaxKind.ArrowFunction:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<FunctionLikeDeclaration>node).asteriskToken) ||
                visitNode(cbNode, (<FunctionLikeDeclaration>node).name) ||
                visitNode(cbNode, (<FunctionLikeDeclaration>node).questionToken) ||
                visitNode(cbNode, (<FunctionLikeDeclaration>node).exclamationToken) ||
                visitNodes(cbNode, cbNodes, (<FunctionLikeDeclaration>node).typeParameters) ||
                visitNodes(cbNode, cbNodes, (<FunctionLikeDeclaration>node).parameters) ||
                visitNode(cbNode, (<FunctionLikeDeclaration>node).type) ||
                visitNode(cbNode, (<ArrowFunction>node).equalsGreaterThanToken) ||
                visitNode(cbNode, (<FunctionLikeDeclaration>node).body);
        case SyntaxKind.TypeReference:
            return visitNode(cbNode, (<TypeReferenceNode>node).typeName) ||
                visitNodes(cbNode, cbNodes, (<TypeReferenceNode>node).typeArguments);
        case SyntaxKind.TypePredicate:
            return visitNode(cbNode, (<TypePredicateNode>node).assertsModifier) ||
                visitNode(cbNode, (<TypePredicateNode>node).parameterName) ||
                visitNode(cbNode, (<TypePredicateNode>node).type);
        case SyntaxKind.TypeQuery:
            return visitNode(cbNode, (<TypeQueryNode>node).exprName);
        case SyntaxKind.TypeLiteral:
            return visitNodes(cbNode, cbNodes, (<TypeLiteralNode>node).members);
        case SyntaxKind.ArrayType:
            return visitNode(cbNode, (<ArrayTypeNode>node).elementType);
        case SyntaxKind.TupleType:
            return visitNodes(cbNode, cbNodes, (<TupleTypeNode>node).elementTypes);
        case SyntaxKind.UnionType:
        case SyntaxKind.IntersectionType:
            return visitNodes(cbNode, cbNodes, (<UnionOrIntersectionTypeNode>node).types);
        case SyntaxKind.ConditionalType:
            return visitNode(cbNode, (<ConditionalTypeNode>node).checkType) ||
                visitNode(cbNode, (<ConditionalTypeNode>node).extendsType) ||
                visitNode(cbNode, (<ConditionalTypeNode>node).trueType) ||
                visitNode(cbNode, (<ConditionalTypeNode>node).falseType);
        case SyntaxKind.InferType:
            return visitNode(cbNode, (<InferTypeNode>node).typeParameter);
        case SyntaxKind.ImportType:
            return visitNode(cbNode, (<ImportTypeNode>node).argument) ||
                visitNode(cbNode, (<ImportTypeNode>node).qualifier) ||
                visitNodes(cbNode, cbNodes, (<ImportTypeNode>node).typeArguments);
        case SyntaxKind.ParenthesizedType:
        case SyntaxKind.TypeOperator:
            return visitNode(cbNode, (<ParenthesizedTypeNode | TypeOperatorNode>node).type);
        case SyntaxKind.IndexedAccessType:
            return visitNode(cbNode, (<IndexedAccessTypeNode>node).objectType) ||
                visitNode(cbNode, (<IndexedAccessTypeNode>node).indexType);
        case SyntaxKind.MappedType:
            return visitNode(cbNode, (<MappedTypeNode>node).readonlyToken) ||
                visitNode(cbNode, (<MappedTypeNode>node).typeParameter) ||
                visitNode(cbNode, (<MappedTypeNode>node).questionToken) ||
                visitNode(cbNode, (<MappedTypeNode>node).type);
        case SyntaxKind.LiteralType:
            return visitNode(cbNode, (<LiteralTypeNode>node).literal);
        case SyntaxKind.ObjectBindingPattern:
        case SyntaxKind.ArrayBindingPattern:
            return visitNodes(cbNode, cbNodes, (<BindingPattern>node).elements);
        case SyntaxKind.ArrayLiteralExpression:
            return visitNodes(cbNode, cbNodes, (<ArrayLiteralExpression>node).elements);
        case SyntaxKind.ObjectLiteralExpression:
            return visitNodes(cbNode, cbNodes, (<ObjectLiteralExpression>node).properties);
        case SyntaxKind.PropertyAccessExpression:
            return visitNode(cbNode, (<PropertyAccessExpression>node).expression) ||
                visitNode(cbNode, (<PropertyAccessExpression>node).questionDotToken) ||
                visitNode(cbNode, (<PropertyAccessExpression>node).name);
        case SyntaxKind.ElementAccessExpression:
            return visitNode(cbNode, (<ElementAccessExpression>node).expression) ||
                visitNode(cbNode, (<ElementAccessExpression>node).questionDotToken) ||
                visitNode(cbNode, (<ElementAccessExpression>node).argumentExpression);
        case SyntaxKind.CallExpression:
        case SyntaxKind.NewExpression:
            return visitNode(cbNode, (<CallExpression>node).expression) ||
                visitNode(cbNode, (<CallExpression>node).questionDotToken) ||
                visitNodes(cbNode, cbNodes, (<CallExpression>node).typeArguments) ||
                visitNodes(cbNode, cbNodes, (<CallExpression>node).arguments);
        case SyntaxKind.TaggedTemplateExpression:
            return visitNode(cbNode, (<TaggedTemplateExpression>node).tag) ||
                visitNode(cbNode, (<TaggedTemplateExpression>node).questionDotToken) ||
                visitNodes(cbNode, cbNodes, (<TaggedTemplateExpression>node).typeArguments) ||
                visitNode(cbNode, (<TaggedTemplateExpression>node).template);
        case SyntaxKind.TypeAssertionExpression:
            return visitNode(cbNode, (<TypeAssertion>node).type) ||
                visitNode(cbNode, (<TypeAssertion>node).expression);
        case SyntaxKind.ParenthesizedExpression:
            return visitNode(cbNode, (<ParenthesizedExpression>node).expression);
        case SyntaxKind.DeleteExpression:
            return visitNode(cbNode, (<DeleteExpression>node).expression);
        case SyntaxKind.TypeOfExpression:
            return visitNode(cbNode, (<TypeOfExpression>node).expression);
        case SyntaxKind.VoidExpression:
            return visitNode(cbNode, (<VoidExpression>node).expression);
        case SyntaxKind.PrefixUnaryExpression:
            return visitNode(cbNode, (<PrefixUnaryExpression>node).operand);
        case SyntaxKind.YieldExpression:
            return visitNode(cbNode, (<YieldExpression>node).asteriskToken) ||
                visitNode(cbNode, (<YieldExpression>node).expression);
        case SyntaxKind.AwaitExpression:
            return visitNode(cbNode, (<AwaitExpression>node).expression);
        case SyntaxKind.PostfixUnaryExpression:
            return visitNode(cbNode, (<PostfixUnaryExpression>node).operand);
        case SyntaxKind.BinaryExpression:
            return visitNode(cbNode, (<BinaryExpression>node).left) ||
                visitNode(cbNode, (<BinaryExpression>node).operatorToken) ||
                visitNode(cbNode, (<BinaryExpression>node).right);
        case SyntaxKind.AsExpression:
            return visitNode(cbNode, (<AsExpression>node).expression) ||
                visitNode(cbNode, (<AsExpression>node).type);
        case SyntaxKind.NonNullExpression:
            return visitNode(cbNode, (<NonNullExpression>node).expression);
        case SyntaxKind.MetaProperty:
            return visitNode(cbNode, (<MetaProperty>node).name);
        case SyntaxKind.ConditionalExpression:
            return visitNode(cbNode, (<ConditionalExpression>node).condition) ||
                visitNode(cbNode, (<ConditionalExpression>node).questionToken) ||
                visitNode(cbNode, (<ConditionalExpression>node).whenTrue) ||
                visitNode(cbNode, (<ConditionalExpression>node).colonToken) ||
                visitNode(cbNode, (<ConditionalExpression>node).whenFalse);
        case SyntaxKind.SpreadElement:
            return visitNode(cbNode, (<SpreadElement>node).expression);
        case SyntaxKind.Block:
        case SyntaxKind.ModuleBlock:
            return visitNodes(cbNode, cbNodes, (<Block>node).statements);
        case SyntaxKind.SourceFile:
            return visitNodes(cbNode, cbNodes, (<SourceFile>node).statements) ||
                visitNode(cbNode, (<SourceFile>node).endOfFileToken);
        case SyntaxKind.VariableStatement:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<VariableStatement>node).declarationList);
        case SyntaxKind.VariableDeclarationList:
            return visitNodes(cbNode, cbNodes, (<VariableDeclarationList>node).declarations);
        case SyntaxKind.ExpressionStatement:
            return visitNode(cbNode, (<ExpressionStatement>node).expression);
        case SyntaxKind.IfStatement:
            return visitNode(cbNode, (<IfStatement>node).expression) ||
                visitNode(cbNode, (<IfStatement>node).thenStatement) ||
                visitNode(cbNode, (<IfStatement>node).elseStatement);
        case SyntaxKind.DoStatement:
            return visitNode(cbNode, (<DoStatement>node).statement) ||
                visitNode(cbNode, (<DoStatement>node).expression);
        case SyntaxKind.WhileStatement:
            return visitNode(cbNode, (<WhileStatement>node).expression) ||
                visitNode(cbNode, (<WhileStatement>node).statement);
        case SyntaxKind.ForStatement:
            return visitNode(cbNode, (<ForStatement>node).initializer) ||
                visitNode(cbNode, (<ForStatement>node).condition) ||
                visitNode(cbNode, (<ForStatement>node).incrementor) ||
                visitNode(cbNode, (<ForStatement>node).statement);
        case SyntaxKind.ForInStatement:
            return visitNode(cbNode, (<ForInStatement>node).initializer) ||
                visitNode(cbNode, (<ForInStatement>node).expression) ||
                visitNode(cbNode, (<ForInStatement>node).statement);
        case SyntaxKind.ForOfStatement:
            return visitNode(cbNode, (<ForOfStatement>node).awaitModifier) ||
                visitNode(cbNode, (<ForOfStatement>node).initializer) ||
                visitNode(cbNode, (<ForOfStatement>node).expression) ||
                visitNode(cbNode, (<ForOfStatement>node).statement);
        case SyntaxKind.ContinueStatement:
        case SyntaxKind.BreakStatement:
            return visitNode(cbNode, (<BreakOrContinueStatement>node).label);
        case SyntaxKind.ReturnStatement:
            return visitNode(cbNode, (<ReturnStatement>node).expression);
        case SyntaxKind.WithStatement:
            return visitNode(cbNode, (<WithStatement>node).expression) ||
                visitNode(cbNode, (<WithStatement>node).statement);
        case SyntaxKind.SwitchStatement:
            return visitNode(cbNode, (<SwitchStatement>node).expression) ||
                visitNode(cbNode, (<SwitchStatement>node).caseBlock);
        case SyntaxKind.CaseBlock:
            return visitNodes(cbNode, cbNodes, (<CaseBlock>node).clauses);
        case SyntaxKind.CaseClause:
            return visitNode(cbNode, (<CaseClause>node).expression) ||
                visitNodes(cbNode, cbNodes, (<CaseClause>node).statements);
        case SyntaxKind.DefaultClause:
            return visitNodes(cbNode, cbNodes, (<DefaultClause>node).statements);
        case SyntaxKind.LabeledStatement:
            return visitNode(cbNode, (<LabeledStatement>node).label) ||
                visitNode(cbNode, (<LabeledStatement>node).statement);
        case SyntaxKind.ThrowStatement:
            return visitNode(cbNode, (<ThrowStatement>node).expression);
        case SyntaxKind.TryStatement:
            return visitNode(cbNode, (<TryStatement>node).tryBlock) ||
                visitNode(cbNode, (<TryStatement>node).catchClause) ||
                visitNode(cbNode, (<TryStatement>node).finallyBlock);
        case SyntaxKind.CatchClause:
            return visitNode(cbNode, (<CatchClause>node).variableDeclaration) ||
                visitNode(cbNode, (<CatchClause>node).block);
        case SyntaxKind.Decorator:
            return visitNode(cbNode, (<Decorator>node).expression);
        case SyntaxKind.ClassDeclaration:
        case SyntaxKind.ClassExpression:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<ClassLikeDeclaration>node).name) ||
                visitNodes(cbNode, cbNodes, (<ClassLikeDeclaration>node).typeParameters) ||
                visitNodes(cbNode, cbNodes, (<ClassLikeDeclaration>node).heritageClauses) ||
                visitNodes(cbNode, cbNodes, (<ClassLikeDeclaration>node).members);
        case SyntaxKind.InterfaceDeclaration:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<InterfaceDeclaration>node).name) ||
                visitNodes(cbNode, cbNodes, (<InterfaceDeclaration>node).typeParameters) ||
                visitNodes(cbNode, cbNodes, (<ClassDeclaration>node).heritageClauses) ||
                visitNodes(cbNode, cbNodes, (<InterfaceDeclaration>node).members);
        case SyntaxKind.TypeAliasDeclaration:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<TypeAliasDeclaration>node).name) ||
                visitNodes(cbNode, cbNodes, (<TypeAliasDeclaration>node).typeParameters) ||
                visitNode(cbNode, (<TypeAliasDeclaration>node).type);
        case SyntaxKind.EnumDeclaration:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<EnumDeclaration>node).name) ||
                visitNodes(cbNode, cbNodes, (<EnumDeclaration>node).members);
        case SyntaxKind.EnumMember:
            return visitNode(cbNode, (<EnumMember>node).name) ||
                visitNode(cbNode, (<EnumMember>node).initializer);
        case SyntaxKind.ModuleDeclaration:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<ModuleDeclaration>node).name) ||
                visitNode(cbNode, (<ModuleDeclaration>node).body);
        case SyntaxKind.ImportEqualsDeclaration:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<ImportEqualsDeclaration>node).name) ||
                visitNode(cbNode, (<ImportEqualsDeclaration>node).moduleReference);
        case SyntaxKind.ImportDeclaration:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<ImportDeclaration>node).importClause) ||
                visitNode(cbNode, (<ImportDeclaration>node).moduleSpecifier);
        case SyntaxKind.ImportClause:
            return visitNode(cbNode, (<ImportClause>node).name) ||
                visitNode(cbNode, (<ImportClause>node).namedBindings);
        case SyntaxKind.NamespaceExportDeclaration:
            return visitNode(cbNode, (<NamespaceExportDeclaration>node).name);

        case SyntaxKind.NamespaceImport:
            return visitNode(cbNode, (<NamespaceImport>node).name);
        case SyntaxKind.NamedImports:
        case SyntaxKind.NamedExports:
            return visitNodes(cbNode, cbNodes, (<NamedImportsOrExports>node).elements);
        case SyntaxKind.ExportDeclaration:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<ExportDeclaration>node).exportClause) ||
                visitNode(cbNode, (<ExportDeclaration>node).moduleSpecifier);
        case SyntaxKind.ImportSpecifier:
        case SyntaxKind.ExportSpecifier:
            return visitNode(cbNode, (<ImportOrExportSpecifier>node).propertyName) ||
                visitNode(cbNode, (<ImportOrExportSpecifier>node).name);
        case SyntaxKind.ExportAssignment:
            return visitNodes(cbNode, cbNodes, node.decorators) ||
                visitNodes(cbNode, cbNodes, node.modifiers) ||
                visitNode(cbNode, (<ExportAssignment>node).expression);
        case SyntaxKind.TemplateExpression:
            return visitNode(cbNode, (<TemplateExpression>node).head) || visitNodes(cbNode, cbNodes, (<TemplateExpression>node).templateSpans);
        case SyntaxKind.TemplateSpan:
            return visitNode(cbNode, (<TemplateSpan>node).expression) || visitNode(cbNode, (<TemplateSpan>node).literal);
        case SyntaxKind.ComputedPropertyName:
            return visitNode(cbNode, (<ComputedPropertyName>node).expression);
        case SyntaxKind.HeritageClause:
            return visitNodes(cbNode, cbNodes, (<HeritageClause>node).types);
        case SyntaxKind.ExpressionWithTypeArguments:
            return visitNode(cbNode, (<ExpressionWithTypeArguments>node).expression) ||
                visitNodes(cbNode, cbNodes, (<ExpressionWithTypeArguments>node).typeArguments);
        case SyntaxKind.ExternalModuleReference:
            return visitNode(cbNode, (<ExternalModuleReference>node).expression);
        case SyntaxKind.MissingDeclaration:
            return visitNodes(cbNode, cbNodes, node.decorators);
        case SyntaxKind.CommaListExpression:
            return visitNodes(cbNode, cbNodes, (<CommaListExpression>node).elements);

        case SyntaxKind.JsxElement:
            return visitNode(cbNode, (<JsxElement>node).openingElement) ||
                visitNodes(cbNode, cbNodes, (<JsxElement>node).children) ||
                visitNode(cbNode, (<JsxElement>node).closingElement);
        case SyntaxKind.JsxFragment:
            return visitNode(cbNode, (<JsxFragment>node).openingFragment) ||
                visitNodes(cbNode, cbNodes, (<JsxFragment>node).children) ||
                visitNode(cbNode, (<JsxFragment>node).closingFragment);
        case SyntaxKind.JsxSelfClosingElement:
        case SyntaxKind.JsxOpeningElement:
            return visitNode(cbNode, (<JsxOpeningLikeElement>node).tagName) ||
                visitNodes(cbNode, cbNodes, (<JsxOpeningLikeElement>node).typeArguments) ||
                visitNode(cbNode, (<JsxOpeningLikeElement>node).attributes);
        case SyntaxKind.JsxAttributes:
            return visitNodes(cbNode, cbNodes, (<JsxAttributes>node).properties);
        case SyntaxKind.JsxAttribute:
            return visitNode(cbNode, (<JsxAttribute>node).name) ||
                visitNode(cbNode, (<JsxAttribute>node).initializer);
        case SyntaxKind.JsxSpreadAttribute:
            return visitNode(cbNode, (<JsxSpreadAttribute>node).expression);
        case SyntaxKind.JsxExpression:
            return visitNode(cbNode, (node as JsxExpression).dotDotDotToken) ||
                visitNode(cbNode, (node as JsxExpression).expression);
        case SyntaxKind.JsxClosingElement:
            return visitNode(cbNode, (<JsxClosingElement>node).tagName);

        case SyntaxKind.OptionalType:
        case SyntaxKind.RestType:
        case SyntaxKind.JSDocTypeExpression:
        case SyntaxKind.JSDocNonNullableType:
        case SyntaxKind.JSDocNullableType:
        case SyntaxKind.JSDocOptionalType:
        case SyntaxKind.JSDocVariadicType:
            return visitNode(cbNode, (<OptionalTypeNode | RestTypeNode | JSDocTypeExpression | JSDocTypeReferencingNode>node).type);
        case SyntaxKind.JSDocFunctionType:
            return visitNodes(cbNode, cbNodes, (<JSDocFunctionType>node).parameters) ||
                visitNode(cbNode, (<JSDocFunctionType>node).type);
        case SyntaxKind.JSDocComment:
            return visitNodes(cbNode, cbNodes, (<JSDoc>node).tags);
        case SyntaxKind.JSDocParameterTag:
        case SyntaxKind.JSDocPropertyTag:
            return visitNode(cbNode, (node as JSDocTag).tagName) ||
                ((node as JSDocPropertyLikeTag).isNameFirst
                    ? visitNode(cbNode, (<JSDocPropertyLikeTag>node).name) ||
                        visitNode(cbNode, (<JSDocPropertyLikeTag>node).typeExpression)
                    : visitNode(cbNode, (<JSDocPropertyLikeTag>node).typeExpression) ||
                        visitNode(cbNode, (<JSDocPropertyLikeTag>node).name));
        case SyntaxKind.JSDocAuthorTag:
            return visitNode(cbNode, (node as JSDocTag).tagName);
        case SyntaxKind.JSDocAugmentsTag:
            return visitNode(cbNode, (node as JSDocTag).tagName) ||
                visitNode(cbNode, (<JSDocAugmentsTag>node).class);
        case SyntaxKind.JSDocTemplateTag:
            return visitNode(cbNode, (node as JSDocTag).tagName) ||
                visitNode(cbNode, (<JSDocTemplateTag>node).constraint) ||
                visitNodes(cbNode, cbNodes, (<JSDocTemplateTag>node).typeParameters);
        case SyntaxKind.JSDocTypedefTag:
            return visitNode(cbNode, (node as JSDocTag).tagName) ||
                ((node as JSDocTypedefTag).typeExpression &&
                    (node as JSDocTypedefTag).typeExpression!.kind === SyntaxKind.JSDocTypeExpression
                    ? visitNode(cbNode, (<JSDocTypedefTag>node).typeExpression) ||
                        visitNode(cbNode, (<JSDocTypedefTag>node).fullName)
                    : visitNode(cbNode, (<JSDocTypedefTag>node).fullName) ||
                        visitNode(cbNode, (<JSDocTypedefTag>node).typeExpression));
        case SyntaxKind.JSDocCallbackTag:
            return visitNode(cbNode, (node as JSDocTag).tagName) ||
                visitNode(cbNode, (node as JSDocCallbackTag).fullName) ||
                visitNode(cbNode, (node as JSDocCallbackTag).typeExpression);
        case SyntaxKind.JSDocReturnTag:
        case SyntaxKind.JSDocTypeTag:
        case SyntaxKind.JSDocThisTag:
        case SyntaxKind.JSDocEnumTag:
            return visitNode(cbNode, (node as JSDocTag).tagName) ||
                visitNode(cbNode, (node as JSDocReturnTag | JSDocTypeTag | JSDocThisTag | JSDocEnumTag).typeExpression);
        case SyntaxKind.JSDocSignature:
            return forEach((<JSDocSignature>node).typeParameters, cbNode) ||
                forEach((<JSDocSignature>node).parameters, cbNode) ||
                visitNode(cbNode, (<JSDocSignature>node).type);
        case SyntaxKind.JSDocTypeLiteral:
            return forEach((node as JSDocTypeLiteral).jsDocPropertyTags, cbNode);
        case SyntaxKind.JSDocTag:
        case SyntaxKind.JSDocClassTag:
            return visitNode(cbNode, (node as JSDocTag).tagName);
        case SyntaxKind.PartiallyEmittedExpression:
            return visitNode(cbNode, (<PartiallyEmittedExpression>node).expression);
    }
}
```
