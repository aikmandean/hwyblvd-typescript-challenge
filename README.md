# hwyblvd
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
