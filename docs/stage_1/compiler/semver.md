## 类型接口

### Version
```ts
export class Version {
    static readonly zero = new Version(0, 0, 0);

    readonly major: number;
    readonly minor: number;
    readonly patch: number;
    readonly prerelease: readonly string[];
    readonly build: readonly string[];

    constructor(text: string);
    constructor(major: number, minor?: number, patch?: number, prerelease?: string, build?: string);
    constructor(major: number | string, minor = 0, patch = 0, prerelease = "", build = "") {
        ...
    }
    static tryParse(text: string) {}
    compareTo(other: Version | undefined) {}
    increment(field: "major" | "minor" | "patch") {}
    toString() {}
}
```

### VersionRange
```ts
export class VersionRange {
}
```

### Comparator
```ts
interface Comparator {
    readonly operator: "<" | "<=" | ">" | ">=" | "=";
    readonly operand: Version;
}
```
## 实例对象
### Base
```ts
const versionRegExp = /^(0|[1-9]\d*)(?:\.(0|[1-9]\d*)(?:\.(0|[1-9]\d*)(?:\-([a-z0-9-.]+))?(?:\+([a-z0-9-.]+))?)?)?$/i;
const prereleaseRegExp = /^(?:0|[1-9]\d*|[a-z-][a-z0-9-]*)(?:\.(?:0|[1-9]\d*|[a-z-][a-z0-9-]*))*$/i;
const buildRegExp = /^[a-z0-9-]+(?:\.[a-z0-9-]+)*$/i;
const numericIdentifierRegExp = /^(0|[1-9]\d*)$/;

const logicalOrRegExp = /\s*\|\|\s*/g;
const whitespaceRegExp = /\s+/g;
const partialRegExp = /^([xX*0]|[1-9]\d*)(?:\.([xX*0]|[1-9]\d*)(?:\.([xX*0]|[1-9]\d*)(?:-([a-z0-9-.]+))?(?:\+([a-z0-9-.]+))?)?)?$/i;
const hyphenRegExp = /^\s*([a-z0-9-+.*]+)\s+-\s+([a-z0-9-+.*]+)\s*$/i;
const rangeRegExp = /^\s*(~|\^|<|<=|>|>=|=)?\s*([a-z0-9-+.*]+)$/i;
```
## 功能函数
## 内部函数
### tryParseComponents
```ts
function tryParseComponents(text: string) {}
```
### comparePrerelaseIdentifiers
```ts
function comparePrerelaseIdentifiers(
    left: readonly string[],
    right: readonly string[]) {}
```

### parseRange
```ts
function parseRange(text: string) {}
```

### parsePartial
```ts
function parsePartial(text: string) {}
```

### parseHyphen
```ts
function parseHyphen(
    left: string, 
    right: string, 
    comparators: Comparator[]) {}
```

### parseComparator
```ts
function parseComparator(
    operator: string, 
    text: string, 
    comparators: Comparator[]) {}
```