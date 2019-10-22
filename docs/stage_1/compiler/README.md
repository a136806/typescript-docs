# TypeScript源代码

## 结构
```
src\
    compiler\
        transforms\
        binder.ts                    ;3 Binder ASTNode生成Symbole
        builder.ts
        builderState.ts 
        checker.ts                   ;4 Check  ASTNode查找Symbole
        commandLineParser.ts
        core.ts                      ;0 Core   基础算法实现
        debug.ts
        emitter.ts                   ;7 Emitter 代码生成器
        factory.ts                   ;0 ASTNode 创建与编辑
        moduleNameResolver.ts
        moduleSpecifiers.ts
        parser.ts                    ;2 Parser
        path.ts                      ;0 Path    文件目录操作
        perLogger.ts               
        performance.ts
        program.ts                   ;8 Program 编译器入口
        resolutionCache.ts
        scanner.ts                   ;1 Scanner
        semver.ts
        sourcemap.ts
        symbolWalker.ts
        sys.ts
        transformer.ts               ;6 Transform 代码转换器
        tsbuild.ts
        types.ts                     ;0 Types    类型声明
        utilities.ts                 ;0 ASTNode  字段内容操作
        visitor.ts                   ;5 Visitor  遍历访问
        watch.ts
        watchUtilities.ts
```

## 相关资料
[深入typescript](http://basarat.gitbooks.io/typescript/content/docs/getting-started.html)
[typescriptSpec](https://github.com/microsoft/TypeScript/blob/master/doc/spec.md)

## 代码组织
> ts.config.json
```json
{
    "extends": "../tsconfig-base",
    "compilerOptions": {
        "outFile": "../../built/local/compiler.js"
    },

    "references": [
        { "path": "../shims" },
    ],

    "files": [
        // 基础算法类
        "core.ts",
        "debug.ts",
        "performance.ts",
        "perfLogger.ts",
        "semver.ts",

        // 代码解析类
        "types.ts",
        "sys.ts",
        "path.ts",
        "diagnosticInformationMap.generated.ts",
        "scanner.ts",
        "utilities.ts",
        "parser.ts",
        "commandLineParser.ts",
        "moduleNameResolver.ts",

        // 代码生成类
        "binder.ts",
        "symbolWalker.ts",
        "checker.ts",
        "factory.ts",
        "visitor.ts",
        "sourcemap.ts",
        "transformers/utilities.ts",
        "transformers/destructuring.ts",
        "transformers/ts.ts",
        "transformers/classFields.ts",
        "transformers/es2017.ts",
        "transformers/es2018.ts",
        "transformers/es2019.ts",
        "transformers/esnext.ts",
        "transformers/jsx.ts",
        "transformers/es2016.ts",
        "transformers/es2015.ts",
        "transformers/es5.ts",
        "transformers/generators.ts",
        "transformers/module/module.ts",
        "transformers/module/system.ts",
        "transformers/module/es2015.ts",
        "transformers/declarations/diagnostics.ts",
        "transformers/declarations.ts",
        "transformer.ts",
        "emitter.ts",
        "watchUtilities.ts",
        "program.ts",
        "builderState.ts",
        "builder.ts",
        "resolutionCache.ts",
        "moduleSpecifiers.ts",
        "watch.ts",
        "tsbuild.ts",
    ]
}
```