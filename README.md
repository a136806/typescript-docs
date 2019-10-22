# TOC

TypeScript编译器源代码学习

## Compiler源码文件划分
> src/compiler/ts.config.json
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