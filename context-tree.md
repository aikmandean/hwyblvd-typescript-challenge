
A context tree
```ts
type User = { userId: bigint }
type Playlist = { playlistId: string, playlistName: string }
type Song = { songId: string, artist: string }
type AudioFile = { audioSrc: string }

declare const useSpecialSelector: ContextTree<[
    [User, {playlists: Playlist[]}],        // <- if provided a 'User', have access to user's 'playlists'
    [Playlist, {songs: Song[]}],            // <- if provided a 'Playlist', have access to its 'songs'
    [Song, AudioFile]                       // <- if provided a 'Song', have access to its 'audioSrc'
]>
```

Component tree
```tsx
function ListPlaylists(props: User) {
    const useStore = useSpecialSelector(props)

    const firstPlaylist = useStore(s => s.playlists[0])
    const errorExample = useStore(s => s.songs) // -- this errors since '<SLICE>.songs' isn't available yet.

    return <ListSongs {...props} {...firstPlaylist} />
}

function ListSongs(props: User & Playlist) {
    const useStore = useSpecialSelector(props)

    const songList = useStore(s => s.songs)
    const errorExample = useStore(s => s.audioSrc) // -- this errors since '<SLICE>.audioSrc' isn't available yet.

    return <PlaySong {...props} {...songList[0]} />
}

function PlaySong(props: User & Playlist & Song) {
    const useStore = useSpecialSelector(props)

    const audioSrc = useStore(s => s.audioSrc)

    return <audio src={audioSrc} />
}
```

Type library
```ts
type UnionToIntersection<U> = 
  (U extends any ? (k: U)=>void : never) extends ((k: infer I)=>void) ? I : never
  
  
type ContextSelector<
    U extends [{}, {}][]
> = 
    <T>(props: T) => <F extends (data: UnionToIntersection<{[K in keyof U]: T extends U[K][0] ? U[K][1] : never}[number]>) => unknown>(callback: F) => ReturnType<F>
type ContextTree<
    A extends any[],
    D extends [{}, {}][] = []
> =
    A extends [infer F, ...infer R] ?
        F extends [any, any] ? ContextTree<R, [...D, F]> : 
    ContextSelector<D> : ContextSelector<D>
```
