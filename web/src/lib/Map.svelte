<script lang="ts">
    import { onMount } from "svelte"
    import { browser } from "$app/environment"

    import world from "../assets/world_min.svg"

    let mapElement: HTMLElement
    let map

    let lat: number
    let lng: number

    onMount(async () => {
        if (browser) {
            const L = await import("leaflet")
            await import("leaflet-editable")

            map = L.map(mapElement, {
                crs: L.CRS.EPSG4326,
                editable: true
            })
            L.imageOverlay(world, [[-90, -360], [90, 0]]).addTo(map)
            L.imageOverlay(world, [[-90, 0], [90, 360]]).addTo(map)
            L.imageOverlay(world, [[-90, 360], [90, 720]]).addTo(map)
            map.fitBounds([[-90, 0], [90, 360]])

            map.on("mousemove", ({latlng}) => {
                lat = latlng.lat
                lng = latlng.lng
            })
        } else { console.log("HO") }
    })

    const format = (n: number) =>
        n.toFixed(5).substring(0, 5)
    
</script>

<style>
    @import "leaflet/dist/leaflet.css";
    .map {
        border: 1px solid black;
        height: 800px;
    }
    :global(.leaflet-container) {
        background: #5e95b4;
    }
</style>

({format(lng || 0)}, {format(lat || 0)})
<div class="map" bind:this={mapElement}/>
