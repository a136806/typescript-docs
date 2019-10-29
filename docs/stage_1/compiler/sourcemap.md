## 类型接口
### SourceMapGeneratorOptions
```ts
export interface SourceMapGeneratorOptions {
    extendedDiagnostics?: boolean;
}
```

### LineInfo
```ts
export interface LineInfo {
    getLineCount(): number;
    getLineText(line: number): string;
}

```

### MappingsDecoder
```ts
export interface MappingsDecoder extends Iterator<Mapping> {
    readonly pos: number;
    readonly error: string | undefined;
    readonly state: Required<Mapping>;
}
```

### Mapping
```ts
export interface Mapping {
    generatedLine: number;
    generatedCharacter: number;
    sourceIndex?: number;
    sourceLine?: number;
    sourceCharacter?: number;
    nameIndex?: number;
}
```

### SourceMapping
```ts
export interface SourceMapping extends Mapping {
    sourceIndex: number;
    sourceLine: number;
    sourceCharacter: number;
}
```

### MappedPosition
```ts
interface MappedPosition {
    generatedPosition: number;
    source: string | undefined;
    sourceIndex: number | undefined;
    sourcePosition: number | undefined;
    nameIndex: number | undefined;
}
```

### SourceMappedPosition
```ts
interface SourceMappedPosition extends MappedPosition {
    source: string;
    sourceIndex: number;
    sourcePosition: number;
}
```

## 实例对象
### Base
```ts
const sourceMapCommentRegExp = /^\/\/[@#] source[M]appingURL=(.+)\s*$/;
const whitespaceOrMapCommentRegExp = /^\s*(\/\/[@#] .*)?$/;
export const identitySourceMapConsumer: DocumentPositionMapper = {
    getSourcePosition: identity,
    getGeneratedPosition: identity
};
```
## 功能函数

### createSourceMapGenerator
```ts
export function createSourceMapGenerator(
    host: EmitHost, 
    file: string, 
    sourceRoot: string, 
    sourcesDirectoryPath: string, 
    generatorOptions: SourceMapGeneratorOptions): SourceMapGenerator {

    ...
    return {
        getSources: () => rawSources,
        addSource,
        setSourceContent,
        addName,
        addMapping,
        appendSourceMap,
        toJSON,
        toString: () => JSON.stringify(toJSON())
    };    
    ...
}
```

### getLineInfo
```ts
export function getLineInfo(
    text: string, 
    lineStarts: readonly number[]): LineInfo {

    return {
        getLineCount: () => lineStarts.length,
        getLineText: line => text.substring(lineStarts[line], lineStarts[line + 1])
    };
}
```

### tryGetSourceMappingURL
```ts
export function tryGetSourceMappingURL(lineInfo: LineInfo) {
    ...
}
```

### isRawSourceMap
```ts
export function isRawSourceMap(x: any): x is RawSourceMap {
    ...
}
```

### tryParseRawSourceMap
```ts
export function tryParseRawSourceMap(text: string) {
    ...
}
```

###  decodeMappings
```ts
export function decodeMappings(mappings: string): MappingsDecoder {
    ...
}
```

### sameMapping
```ts
export function sameMapping<T extends Mapping>(left: T, right: T) {
    ...
}
```

### isSourceMapping
```ts
export function isSourceMapping(mapping: Mapping): mapping is SourceMapping {
    ...
}
```

### createDocumentPositionMapper
```ts
export function createDocumentPositionMapper(
    host: DocumentPositionMapperHost, 
    map: RawSourceMap, 
    mapPath: string): DocumentPositionMapper {
    
    
    return {
        getSourcePosition,
        getGeneratedPosition
    };
    
    function processMapping(mapping: Mapping): MappedPosition {}
    function getSourceMappings(sourceIndex: number) {}
    function getGeneratedMappings() {}
    function getGeneratedPosition(loc: DocumentPosition): DocumentPosition {}
    function getSourcePosition(loc: DocumentPosition): DocumentPosition {}

}
```

## 内部函数
### isStringOrNull
```ts
function isStringOrNull(x: any) {
    return typeof x === "string" || x === null;
}
```

### base64FormatEncode
```ts
function base64FormatEncode(value: number) {
    return value >= 0 && value < 26 ? CharacterCodes.A + value :
        value >= 26 && value < 52 ? CharacterCodes.a + value - 26 :
        value >= 52 && value < 62 ? CharacterCodes._0 + value - 52 :
        value === 62 ? CharacterCodes.plus :
        value === 63 ? CharacterCodes.slash :
        Debug.fail(`${value}: not a base64 value`);
}
```

### base64FormatDecode
```ts
function base64FormatDecode(ch: number) {
    return ch >= CharacterCodes.A && ch <= CharacterCodes.Z ? ch - CharacterCodes.A :
        ch >= CharacterCodes.a && ch <= CharacterCodes.z ? ch - CharacterCodes.a + 26 :
        ch >= CharacterCodes._0 && ch <= CharacterCodes._9 ? ch - CharacterCodes._0 + 52 :
        ch === CharacterCodes.plus ? 62 :
        ch === CharacterCodes.slash ? 63 :
        -1;
}
```

### base64VLQFormatEncode
```ts
function base64VLQFormatEncode(inValue: number) {
    ...
}    
```

### isSourceMappedPosition
```ts
function isSourceMappedPosition(value: MappedPosition): value is SourceMappedPosition {
    return value.sourceIndex !== undefined
        && value.sourcePosition !== undefined;
}
```

### sameMappedPosition
```ts
function sameMappedPosition(left: MappedPosition, right: MappedPosition) {
    return left.generatedPosition === right.generatedPosition
        && left.sourceIndex === right.sourceIndex
        && left.sourcePosition === right.sourcePosition;
}
```

### compareSourcePositions
```ts
function compareSourcePositions(left: SourceMappedPosition, right: SourceMappedPosition) {
    // Compares sourcePosition without comparing sourceIndex
    // since the mappings are grouped by sourceIndex
    Debug.assert(left.sourceIndex === right.sourceIndex);
    return compareValues(left.sourcePosition, right.sourcePosition);
}
```

### compareGeneratedPositions
```ts
function compareGeneratedPositions(left: MappedPosition, right: MappedPosition) {
    return compareValues(left.generatedPosition, right.generatedPosition);
}
```

### getSourcePositionOfMapping
```ts
function getSourcePositionOfMapping(value: SourceMappedPosition) {
    return value.sourcePosition;
}
```

### getGeneratedPositionOfMapping
```ts
function getGeneratedPositionOfMapping(value: MappedPosition) {
    return value.generatedPosition;
}
```