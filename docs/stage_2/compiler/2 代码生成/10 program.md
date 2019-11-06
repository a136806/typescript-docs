## 功能说明
> 代码解析及其结果

## 核心操作

### findConfigFile
> 指定目录下查找配置文件`tsconfig.json`

### createCompilerHost
> 创建编译器

### createProgram
> 创建代码解析结果
> 根据编译配置项 解析结果到`Program`
```ts
export function createProgram(
    rootNamesOrOptions: readonly string[] | CreateProgramOptions, 
    _options?: CompilerOptions, 
    _host?: CompilerHost, 
    _oldProgram?: Program, 
    _configFileParsingDiagnostics?: readonly Diagnostic[]
): Program {
}
```