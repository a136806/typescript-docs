## 功能说明
> 创建与更新语法节点

## create
### createNodeArray
```ts
export function createNodeArray<T extends Node>(
    elements?: readonly T[], 
    hasTrailingComma?: boolean
): NodeArray<T> {
    if (!elements || elements === emptyArray) {
        elements = [];
    }
    else if (isNodeArray(elements)) {
        return elements;
    }

    const array = <NodeArray<T>>elements;
    array.pos = -1;
    array.end = -1;
    array.hasTrailingComma = hasTrailingComma;
    return array;
}
```
### createLiteral
```ts
export function createLiteral(value: string | number | PseudoBigInt | boolean | StringLiteral | NoSubstitutionTemplateLiteral | NumericLiteral | Identifier, isSingleQuote?: boolean): PrimaryExpression {
    if (typeof value === "number") {
        return createNumericLiteral(value + "");
    }
    // eslint-disable-next-line no-in-operator
    if (typeof value === "object" && "base10Value" in value) { // PseudoBigInt
        return createBigIntLiteral(pseudoBigIntToString(value) + "n");
    }
    if (typeof value === "boolean") {
        return value ? createTrue() : createFalse();
    }
    if (isString(value)) {
        const res = createStringLiteral(value);
        if (isSingleQuote) res.singleQuote = true;
        return res;
    }
    return createLiteralFromNode(value);
}
```

## update

## Node
