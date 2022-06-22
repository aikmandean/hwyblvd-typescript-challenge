# hwyblvd
Rules to the challenge: "Who am I?"
1. Created like global addresses.
2. Has a None type.
3. Uses "Uncapitalized" case when passed to functions.
4. Functions can take more than one.
5. Functions can be deconstructed into them.
6. Functions can take other functions using their deconstructor.

> Playground Day 2
  
```ts
type EntryCreate<N,T,M> = abstract new () => [N,T,M]
type EntryRead<A> = A extends abstract new () => infer NTM ? NTM : never
type LabeledEntryCreate<H extends string,NTM> = [[H, Uncapitalize<H>],NTM]
```
```ts
type ObjectObjectToIntersection<T extends {[x:string]:{}}> = UnionToIntersection<T[keyof T]>
```
```ts
type ParseLE<HNTM extends LabeledEntryCreate<string,["prop",any,any]>> = 
    HNTM extends [[string, infer LK], ["prop", infer T, any]] ? {[K in LK]: T} : never
```
```ts
declare function defineProp<T,M>(likeKind: T, optionalMetadata?: M): EntryCreate<"prop", T, M>

declare function of<T>(t:T): ObjectObjectToIntersection<{ [K in keyof T]: T[K] extends (a: infer P, ...args: any[]) => any ? P extends {} ? P : {} : ParseLE<LabeledEntryCreate<K,EntryRead<T[K]>>>}>
```
```ts
const Count = defineProp(0)
const Double = defineProp(0)

function PrintPair(props = of({Count, Double})) {}
function AddItsDouble(props = of({PrintPair})) {}
```
  
> Playground Day 1
  
```ts
type DefineParam<P> = ["param", P]
type DefineFunction<F> = ["function", F]
type DefineReturn<R> = ["return", R]
type DeclareName<N extends string,U> = [[N, Uncapitalize<N>],U]
type Keywords = "param" | "function" | "return"
```
```ts
type UnionToIntersection<T> = 
(T extends any ? (x: T) => any : never) extends 
  (x: infer R) => any ? R : never
type ObjectArrayToIntersection<T extends any[]> = UnionToIntersection<T[number]>
```
```ts
type CreateProps<NPS extends DeclareName<string, [Keywords, unknown]>[]>
  = ObjectArrayToIntersection<
    {[K in keyof NPS]: { [LK in NPS[K][0][1]]: NPS[K][1][1] }}
  >
```
```ts
type Count = DeclareName<"Count", DefineParam<number>>
type SetCount = DeclareName<"SetCount", DefineParam<(n: number) => void>>
function HelloWorld(props: CreateProps<[Count, SetCount]>) {
  props
}
```
```ts
const Count = class { 0 = Number }
const SetCount = class { 0 = (n = 1) => {} }
function HelloWorld(props = createProps({Count,SetCount})) {
  props
}
```
