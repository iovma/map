<script lang="ts">
    import { onMount } from "svelte"
    import { browser } from "$app/environment"

    import world from "../assets/world_min.svg"

    let mapElement: HTMLElement
    let map

    let worldElement: SVGElement

    onMount(async () => {
        if (browser) {
            const L = await import("leaflet")

            map = L.map(mapElement, {
                crs: L.CRS.Simple
            })
            L.imageOverlay(
                    world,
                    [[0, 0], [3840, 2160]]
                ).addTo(map)
            map.fitBounds([[0, 0], [3840, 2160]])
        } else { console.log("HO") }
    })
</script>

<style>
    @import "leaflet/dist/leaflet.css";
    .map {
        border: 1px solid black;
        height: 800px;
    }
</style>

<div class="map" bind:this={mapElement}/>
