# hwyblvd
    
Updates to the challenge: "Who am I?"
1. Created in batches.
2. Has a None type.
3. [No] Uses "Uncapitalized" case when passed to functions.
4. Functions can take more than one.
5. Functions can be deconstructed into them.
6. Functions can take other functions using their deconstructor.
7. [No] They can be nullified.
8. They can be optional.
9. Functions may or may not include deconstruction behavior. 
10. Supports editor go-to-reference, sticky comments, global rename.

> Playground Day 4

```ts
const {
    count,
    label
} = defineProps({
    /** example comment is "sticky", so all users of count see the same comment */
    count: 0,
    label: ""
})
label.label

const {
    doubleCount
} = defineStore({
    /** sticky comments work even for advanced cases when fully replacing the type */
    doubleCount: f(props => {
        return props.count * 2
    }, count)
})
doubleCount.doubleCount


type PropExpansion<T extends {[propNames: string]: unknown}> =
    { [K in keyof T]: Pick<T, K>  }
type StoreExpansion<T extends {[funcNames: string]: (props: any) => any}> =
    { [K in keyof T]: { [K1 in K]: ReturnType<T[K]> } }
type Func<T,F> = F

type UnionToIntersection<T> = 
    (T extends any ? (x: T) => any : never) extends 
        (x: infer R) => any ? R : never


declare function defineProps<T extends {[propNames: string]: unknown}>
    (props: T): PropExpansion<T>
declare function defineStore<T extends {[funcNames: string]: any}>
    (props: T): StoreExpansion<T>
declare function f<T extends {[propsNames: string]: unknown}[],F extends (props: UnionToIntersection<T[number]>) => any>
    (func: F, ...propDefs: T): F    
```  
  
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
Primitives can store flags in symbol storage. Not possible at runtime, 
but able to be passed as prop types.
```ts
type T = {[s:symbol]: "T"}
type F = {[s:symbol]: "F"}

type A = 1
type B = 1 & F
type C = 1 & T
type D = 1 & T & F

type IsTrue<L> = L extends T ? L : "NOT TRUE"

type AT = IsTrue<A>
type BT = IsTrue<B>
type CT = IsTrue<C>
type DT = IsTrue<D>
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
Functions distinguished from other props. Their own prop requirements 
are deconstructed into individual parts.
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

