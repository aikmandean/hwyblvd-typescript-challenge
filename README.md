# hwyblvd
Rules to the challenge: "Who am I?"
1. Created like global addresses.
2. Has a None type.
3. Uses "Uncapitalized" case when passed to functions.
4. Functions can take more than one.
5. Functions can be deconstructed into them.
6. Functions can take other functions using their deconstructor.

> Playground
  
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
