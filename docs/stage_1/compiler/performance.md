## 类型接口
### Timer
```ts
export interface Timer {
    enter(): void;
    exit(): void;
}
```
## 实例对象
```ts
declare const performance: { now?(): number } | undefined;

export const timestamp = 
    typeof performance !== "undefined" && performance.now 
        ? () => performance.now!() : Date.now 
            ? Date.now 
            : () => +(new Date());

declare const onProfilerEvent: { (markName: string): void; profiler: boolean; };


const profilerEvent: (markName: string) => void = typeof onProfilerEvent === "function" && onProfilerEvent.profiler === true ? onProfilerEvent : () => { /*empty*/ };

let enabled = false;
let profilerStart = 0;
let counts: Map<number>;
let marks: Map<number>;
let measures: Map<number>;
export const nullTimer: Timer = { enter: noop, exit: noop };

```
## 功能函数
### createTimerIf
```ts
export function createTimerIf(condition: boolean, measureName: string, startMarkName: string, endMarkName: string) {
    return condition ? createTimer(measureName, startMarkName, endMarkName) : nullTimer;
}
```

### createTimer
```ts
export function createTimer(measureName: string, startMarkName: string, endMarkName: string): Timer {
    let enterCount = 0;
    return {
        enter,
        exit
    };

    function enter() {
        if (++enterCount === 1) {
            mark(startMarkName);
        }
    }

    function exit() {
        if (--enterCount === 0) {
            mark(endMarkName);
            measure(measureName, startMarkName, endMarkName);
        }
        else if (enterCount < 0) {
            Debug.fail("enter/exit count does not match.");
        }
    }
}
```

### mark
```ts
export function mark(markName: string) {
    if (enabled) {
        marks.set(markName, timestamp());
        counts.set(markName, (counts.get(markName) || 0) + 1);
        profilerEvent(markName);
    }
}
```

### measure
```ts
export function measure(measureName: string, startMarkName?: string, endMarkName?: string) {
    if (enabled) {
        const end = endMarkName && marks.get(endMarkName) || timestamp();
        const start = startMarkName && marks.get(startMarkName) || profilerStart;
        measures.set(measureName, (measures.get(measureName) || 0) + (end - start));
    }
}
```

### getCount
```ts
export function getCount(markName: string) {
    return counts && counts.get(markName) || 0;
}
```

### getDuration
```ts
export function getDuration(measureName: string) {
    return measures && measures.get(measureName) || 0;
}
```

### forEachMeasure
```ts
export function forEachMeasure(cb: (measureName: string, duration: number) => void) {
    measures.forEach((measure, key) => {
        cb(key, measure);
    });
}
```

### enable
```ts
export function enable() {
    counts = createMap<number>();
    marks = createMap<number>();
    measures = createMap<number>();
    enabled = true;
    profilerStart = timestamp();
}
```

### disable
```ts
export function disable() {
    enabled = false;
}
```
## 内部函数