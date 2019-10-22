## 功能函数
### chainBundle
```ts
export function chainBundle(transformSourceFile: (x: SourceFile) => SourceFile): (x: SourceFile | Bundle) => SourceFile | Bundle {
    return transformSourceFileOrBundle;

    function transformSourceFileOrBundle(node: SourceFile | Bundle) {
        return node.kind === SyntaxKind.SourceFile ? transformSourceFile(node) : transformBundle(node);
    }

    function transformBundle(node: Bundle) {
        return createBundle(map(node.sourceFiles, transformSourceFile), node.prepends);
    }
}
```