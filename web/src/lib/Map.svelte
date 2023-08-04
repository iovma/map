<script>
    const window = globalThis

    import { LeafletMap, ImageOverlay } from "svelte-leafletjs"

    let leafletMap
</script>

<LeafletMap
    bind:this={leafletMap}
    options={{
        center: [0, 0],
        zoom: 1,
    }}
>
    <ImageOverlay
        imageUrl="temp/5282/world.png"
        bounds={[[0,0],[3840,2160]]}
    />
</LeafletMap>