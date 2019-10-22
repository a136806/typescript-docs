## 功能函数

### transformESNext
```ts
export function transformESNext(context: TransformationContext) {
    const {
        hoistVariableDeclaration,
    } = context;

    return chainBundle(transformSourceFile);

    function transformSourceFile(node: SourceFile) {
        if (node.isDeclarationFile) {
            return node;
        }
        return visitEachChild(node, visitor, context);
    }

    function visitor(node: Node): VisitResult<Node> {
        if ((node.transformFlags & TransformFlags.ContainsESNext) === 0) {
            return node;
        }
        switch (node.kind) {
            case SyntaxKind.PropertyAccessExpression:
            case SyntaxKind.ElementAccessExpression:
            case SyntaxKind.CallExpression:
                if (node.flags & NodeFlags.OptionalChain) {
                    const updated = visitOptionalExpression(node as OptionalChain, /*captureThisArg*/ false);
                    Debug.assertNotNode(updated, isSyntheticReference);
                    return updated;
                }
                return visitEachChild(node, visitor, context);
            case SyntaxKind.BinaryExpression:
                if ((<BinaryExpression>node).operatorToken.kind === SyntaxKind.QuestionQuestionToken) {
                    return transformNullishCoalescingExpression(<BinaryExpression>node);
                }
                return visitEachChild(node, visitor, context);
            default:
                return visitEachChild(node, visitor, context);
        }
    }

    function visitOptionalExpression(node: OptionalChain, captureThisArg: boolean): Expression {
    
    }
    function flattenChain(chain: OptionalChain) {
    }
    function visitNonOptionalExpression(node: Expression, captureThisArg: boolean): Expression {
    }
    function visitNonOptionalParenthesizedExpression(node: ParenthesizedExpression, captureThisArg: boolean): Expression {
    }
    function visitNonOptionalPropertyAccessExpression(node: PropertyAccessExpression, captureThisArg: boolean): Expression {
    }
    function visitNonOptionalElementAccessExpression(node: ElementAccessExpression, captureThisArg: boolean): Expression {
    }
    function visitNonOptionalCallExpression(node: CallExpression, captureThisArg: boolean): Expression {
    }


    function transformNullishCoalescingExpression(node: BinaryExpression) {
    }
    function createNotNullCondition(node: Expression) {}
}
```