---
sidebar_position: 2
---

# 前端Linq

为了方便前端代码使用，我们封装了一套前端Linq库。
```ts

data.where(x => x.iEType == IeType.AdvisoryFee);
data.sum(x => x.value);




    add(element: T): void;
    map<K extends keyof T>(key: K): Array<T[K]>;
    addRange(elements: T[]): void;
    aggregate<U>(accumulator: (accum: U, value?: T | undefined, index?: number | undefined, list?: T[] | undefined) => any, initialValue?: U | undefined): U;
    all(predicate: (value?: T | undefined, index?: number | undefined, list?: T[] | undefined) => boolean): boolean;
    any(): boolean;
    clear(): void;
    any(predicate: (value?: T | undefined, index?: number | undefined, list?: T[] | undefined) => boolean): boolean;
    any(predicate?: any);
    average(): number;
    average(transform: (value?: T | undefined, index?: number | undefined, list?: T[] | undefined) => any): number;
    average(transform?: any);
    contains(element: T): boolean;
    count(): number;
    count(predicate: (value?: T | undefined, index?: number | undefined, list?: T[] | undefined) => boolean): number;
    count(predicate?: any);
    defaultIfEmpty(defaultValue?: T | undefined): T[];
    distinct(): T[];
    distinctBy(keySelector: (key: T) => string | number): T[];
    elementAt(index: number): T;
    toArray(): T[];
    elementAtOrDefault(index: number): T;
    except(source: T[]): T[];
    first(): T;
    first(predicate: (value?: T | undefined, index?: number | undefined, list?: T[] | undefined) => boolean): T;
    first(predicate?: any);
    firstOrDefault(defaultValue?: Partial<T>): T;
    firstOrDefault(predicate: (value?: T | undefined, index?: number | undefined, list?: T[] | undefined) => boolean, defaultValue?: Partial<T>): T;
    firstOrDefault(predicate?: any, defaultValue?: Partial<T>);
    groupBy<TResult = T>(grouper: (key: T) => string | number, mapper?: ((element: T) => TResult) | undefined): { [key: string]: TResult[] };
    groupJoin<U, R>(list: List<U>, key1: (k: T) => any, key2: (k: U) => any, result: (first: T, second: List<U>) => any): R[];
    // indexOf(element: T): number;
    insert(index: number, element: T): void | Error;
    intersect(source: T[]): T[];
    linqJoin<U, R>(list: List<U>, key1: (key: T) => any, key2: (key: U) => any, result: (first: T, second: U) => R): R[];
    last(): T;
    last(predicate: (value?: T | undefined, index?: number | undefined, list?: T[] | undefined) => boolean): T;
    last(predicate?: any);
    lastOrDefault(): T;
    lastOrDefault(predicate: (value?: T | undefined, index?: number | undefined, list?: T[] | undefined) => boolean): T;
    lastOrDefault(predicate?: any);
    max(): number;
    max(selector: (value: T, index: number, array: T[]) => number): number;
    max(selector?: any);
    min(): number;
    min(selector: (value: T, index: number, array: T[]) => number): number;
    min(selector?: any);
    ofType<U>($type: any): List<U>;
    orderBy(keySelector: (key: T) => any, comparer?: ((a: T, b: T) => number) | undefined): T[];
    orderByDescending(keySelector: (key: T) => any, comparer?: ((a: T, b: T) => number) | undefined): T[];
    thenBy(keySelector: (key: T) => any): T[];
    thenByDescending(keySelector: (key: T) => any): T[];
    remove(element: T): boolean;
    removeAll(predicate: (value?: T | undefined, index?: number | undefined, list?: T[] | undefined) => boolean): T[];
    removeAt(index: number): void;
    // select<TOut>(selector: (element: T, index: number) => TOut): List<TOut>;
    selectMany<TOut extends any[]>(selector: (element: T, index: number) => TOut): TOut;
    sequenceEqual(list: T[]): boolean;
    single(predicate?: ((value?: T | undefined, index?: number | undefined, list?: T[] | undefined) => boolean) | undefined): T;
    singleOrDefault(predicate?: ((value?: T | undefined, index?: number | undefined, list?: T[] | undefined) => boolean) | undefined): T;
    skip(amount: number): T[];
    skipWhile(predicate: (value?: T | undefined, index?: number | undefined, list?: T[] | undefined) => boolean): T[];
    sum(): number;
    sum(transform: (value?: T | undefined, index?: number | undefined, list?: T[] | undefined) => number): number;
    sum(transform?: any);
    take(amount: number): T[];
    takeWhile(predicate: (value?: T | undefined, index?: number | undefined, list?: T[] | undefined) => boolean): T[];
    // toArray(): T[];
    toDictionary<TKey>(key: (key: T) => TKey): Map<string, any>;
    toDictionary<TKey, TValue>(key: (key: T) => TKey, value: (value: T) => TValue): Map<TKey, T | TValue>;
    toDictionary(key: any, value?: any);
    // toList(): T[];
    toLookup<TResult>(keySelector: (key: T) => string | number, elementSelector: (element: T) => TResult): { [key: string]: TResult[] };
    union(list: T[]): T[];
    where(predicate: (value?: T | undefined, index?: number | undefined, list?: T[] | undefined) => boolean): T[];
    zip<U, TOut>(list: U[], result: (first: T, second: U) => TOut): TOut[];
    flatMapDeep<U>(this: this, iteratee: (value: T, index: number, array: T[]) => U[], thisArg?: any): U[];
```
