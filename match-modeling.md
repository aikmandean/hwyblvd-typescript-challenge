
Declarations
```ts
const Walk = declareMatchVariant("beWalked", {
  yes: { niceWalk: true },
  no: { meanWalk: true }
})
const Pat = declareMatchVariant("bePat", {
  yes: { nicePats: true },
  no: { meanPats: true }
})
const [animalAny, animalCan, animalType] = declareMatch([{}, Walk, Pat], {
  cat:    [{}, Walk.no, Pat.yes],
  dog:    [{}, Walk.yes,  Pat.yes],
  possum: [{}, Walk.no, Pat.yes],
  snake:  [{}, Walk.no, Pat.no]
})
type Animal = typeof animalType.$
type Cat = typeof animalType.cat
```

Usage
```ts
const t2 = animalCan.beWalked({
  no: x => x.meanWalk,
  yes: x => x
})
  t2
//^?
const t3 = animalAny({ as: "cat", meanWalk: true, nicePats: false })({
  cat: x => x,
})
const t4 = animalAny({} as any)({
  
})
```

Type library
```ts
type $<T = string, V = true> = {$: T, $$: {[KT in T]: V}}
type Y = any extends string ? 1 : 0
type Tpl$<I,T = [never]> =
  T extends [never] ?
    I extends readonly [infer T, ...infer I] ? Tpl$<I, T> : "Error A" :
    [T, I]
type Def$<A> = 
  A[1] extends readonly [infer F, ...infer R] ?
    {$:any,$$:any} extends F ? A : Def$<[A[0], [...R, $<F["$"]>]]> :
  "Error: B"
type Proto$<K extends string, U> = 
  U extends $<K> ? <F extends {[KU in Exclude<keyof U, "$"|"$$">]: (u: U[KU]) => unknown}>(f: F) => ReturnType<F[keyof F]> : never
type Clean$<I> = 
  I extends [infer F, ...infer R] ?
    F extends {$$:any} ? Clean$<[...R, {[KF in Exclude<keyof F, "$"|"$$">]: F[KF]}]> : I :
  "Error: B"
type Build$<KM, M> = {as: KM} & Tpl$<M[KM]>[0] & UnionToIntersection<Clean$<Tpl$<M[KM]>[1]>[number]>
type UnionToIntersection<U> = 
  (U extends any ? (k: U)=>void : never) extends ((k: infer I)=>void) ? I : never

declare function declareMatchVariant
  <KV extends string, M extends {}>(kv: KV, v: M): 
    {[KM in keyof M]: M[KM] & $<KV,KM> } & $<KV>
declare function declareMatch<const A, MK extends string, const M extends {[k: MK]: [{}, ...Def$<Tpl$<A>>[1]]}>(a: A, m: M):
  [
    (<MS extends {[KM in keyof M]: Build$<KM, M>}[keyof M]>(ms: MS) => <F extends {[KM in Extract<keyof M, MS["as"]>]: (x: Build$<KM, M>) => unknown}>(f: F) => unknown)
  , 
    {[KA in Def$<Tpl$<A>>[1][number]["$"]]: Proto$<KA, Tpl$<A>[1][number]>}
  ,
    {$:{[KM in keyof M]: Build$<KM, M>}[keyof M]} & {[KM in keyof M]: Build$<KM, M>}
  ]
```
