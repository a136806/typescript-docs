# Api

## tsc命令行
>ts编译

## CompilerApi使用
> 普通代码 <-> ASTNode的转换操作(工具代码)
- 术语 
    普通代码  编译器无关的ts代码
    工具代码  编译器相关的ts代码,可以看做特殊的普通代码
    ASTNode  语法树节点

- code-block-writer             普通源代码 -> 普通源代码    
- ts-morph                      源代码 -> AST        AST -> AST         
- ts-factory-code-generator                         AST -> 工具代码

### ts-node
> typescript的命令行执行
```
npm install -g ts-node
npm install -g typescript
ts-node script.ts
```
[github](https://github.com/TypeStrong/ts-node)

### code-block-writer
> ts源代码生成器
[官网]
[github](https://github.com/dsherret/code-block-writer)

### ts-morph
> ts的源代码到ASTNode
> AstNode查看与操作
[官网](https://ts-morph.com/)
[github](https://github.com/dsherret/ts-morph)

### ts-factory-code-generator
> ts的源代码转ASTNode组装
[github](https://github.com/dsherret/ts-factory-code-generator-generator)

### ast-viewer
[官网](https://ts-ast-viewer.com/#)
[github](https://github.com/dsherret/ts-ast-viewer)


### ts-creator
[官网](https://ts-creator.js.org/)
[github](https://github.com/HearTao/ts-creator)
## emitterApi使用
> 代码生成器

