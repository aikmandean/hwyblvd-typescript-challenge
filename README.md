# hwyblvd
Rules to the challenge: "Who am I?"
1. Created like global addresses.
2. Has a None type.
3. Uses "Uncapitalized" case when passed to functions.
4. Functions can take more than one.
5. Functions can be deconstructed into them.
6. Functions can take other functions using their deconstructor.
7. They can be nullified.
8. They can be optional.
9. Functions may or may not include deconstruction behavior.
  
> Playground Day 3
  
```ts
type IsOptional = {[s:symbol]:"Optional"}
type DefineProp<T,M> = M extends {default:boolean} ? T & IsOptional : T
```
```ts
type A = DefineProp<1,{}>
type B = DefineProp<1,{ default: true }>
```
  
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
  
Separate params from functions by creating pair boxes. 
Pairs are a good way to keep track of any underlying metadata. 
Arrays of pairs holding mixed type pairs can recover organization.
Two layers of pair boxes make sense, because data is supplied in 
two discrete steps. The end result is a NPS, a labelled content 
that is a param, function, or return.
```ts
type DefineParam<P> = ["param", P]
type DefineFunction<F> = ["function", F]
type DefineReturn<R> = ["return", R]
type DeclareName<N extends string,U> = [[N, Uncapitalize<N>],U]
type Keywords = "param" | "function" | "return"
```
Some helper types for changing the names of an object's properties.
```ts
type UnionToIntersection<T> = 
(T extends any ? (x: T) => any : never) extends 
  (x: infer R) => any ? R : never
type ObjectArrayToIntersection<T extends any[]> = UnionToIntersection<T[number]>
```
Convert the NPS into a props object for React.
```ts
type CreateProps<NPS extends DeclareName<string, [Keywords, unknown]>[]>
  = ObjectArrayToIntersection<
    {[K in keyof NPS]: { [LK in NPS[K][0][1]]: NPS[K][1][1] }}
  >
```
Show how it can be done using TypeScript
```ts
type Count = DeclareName<"Count", DefineParam<number>>
type SetCount = DeclareName<"SetCount", DefineParam<(n: number) => void>>
function HelloWorld(props: CreateProps<[Count, SetCount]>) {
  props
}
```
Show how it can be using JavaScript
  
Using classes as the closest to solve for the None type 
the challenge demands. No type exists to provide a solution 
to composable functions.
```ts
const Count = class { 0 = Number }
const SetCount = class { 0 = (n = 1) => {} }
function HelloWorld(props = createProps({Count,SetCount})) {
  props
}
```
