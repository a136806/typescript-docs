## 功能说明
## 源代码

### TextRange
> 
```ts
export interface TextRange {
    pos: number;
    end: number;
}
```

### FileReference
```ts
export interface FileReference extends TextRange {
    fileName: string;
}
```

### CheckJsDirective
```ts
export interface CheckJsDirective extends TextRange {
    enabled: boolean;
}
```

### CommentKind
```ts
 export type CommentKind 
    = SyntaxKind.SingleLineCommentTrivia 
    | SyntaxKind.MultiLineCommentTrivia;
```

### CommentRange
```ts
export interface CommentRange extends TextRange {
    hasTrailingNewLine?: boolean;
    kind: CommentKind;
}
```


### SynthesizedComment
```ts
export interface SynthesizedComment extends CommentRange {
    text: string;
    pos: -1;
    end: -1;
}
```