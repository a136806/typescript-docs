## 类型接口
## 实例对象
## 功能函数
### createGetSymbolWalker
```ts
export function createGetSymbolWalker(
    getRestTypeOfSignature: (sig: Signature) => Type,
    getTypePredicateOfSignature: (sig: Signature) => TypePredicate | undefined,
    getReturnTypeOfSignature: (sig: Signature) => Type,
    getBaseTypes: (type: Type) => Type[],
    resolveStructuredTypeMembers: (type: ObjectType) => ResolvedType,
    getTypeOfSymbol: (sym: Symbol) => Type,
    getResolvedSymbol: (node: Node) => Symbol,
    getIndexTypeOfStructuredType: (type: Type, kind: IndexKind) => Type | undefined,
    getConstraintOfTypeParameter: (typeParameter: TypeParameter) => Type | undefined,
    getFirstIdentifier: (node: EntityNameOrEntityNameExpression) => Identifier,
    getTypeArguments: (type: TypeReference) => readonly Type[]) {
    
    return getSymbolWalker;
    
    function getSymbolWalker(accept: (symbol: Symbol) => boolean = () => true): SymbolWalker {
        const visitedTypes: Type[] = []; // Sparse array from id to type
        const visitedSymbols: Symbol[] = []; // Sparse array from id to symbol

        return {
            walkType: type => {
                try {
                    visitType(type);
                    return { visitedTypes: getOwnValues(visitedTypes), visitedSymbols: getOwnValues(visitedSymbols) };
                }
                finally {
                    clear(visitedTypes);
                    clear(visitedSymbols);
                }
            },
            walkSymbol: symbol => {
                try {
                    visitSymbol(symbol);
                    return { visitedTypes: getOwnValues(visitedTypes), visitedSymbols: getOwnValues(visitedSymbols) };
                }
                finally {
                    clear(visitedTypes);
                    clear(visitedSymbols);
                }
            },
        };
    }

    function visitType(type: Type | undefined): void {}
    function visitTypeReference(type: TypeReference): void {}
    function visitTypeParameter(type: TypeParameter): void {}
    function visitUnionOrIntersectionType(type: UnionOrIntersectionType): void {}
    function visitIndexType(type: IndexType): void {}
    function visitIndexedAccessType(type: IndexedAccessType): void {}
    function visitMappedType(type: MappedType): void {}
    function visitSignature(signature: Signature): void {}
    function visitInterfaceType(interfaceT: InterfaceType): void {}
    function visitObjectType(type: ObjectType): void {}
    function visitSymbol(symbol: Symbol | undefined): boolean {}
}
```
## 内部函数