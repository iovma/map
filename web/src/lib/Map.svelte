<script lang="ts">
    import { onMount } from "svelte"
    import { browser } from "$app/environment"

    import world from "../assets/world_min.svg"

    import worldPolygon from "../assets/5282.geo.json"

    let mapElement: HTMLElement
    let map: L.Map

    let lat: number = 0
    let lng: number = 0

    let moveLat: number = 0
    let moveLng: number = 0

    const mod = (n: number, m: number) => ((n % m) + m) % m

    onMount(async () => {
        if (browser) {
            const L = await import("leaflet")
            await import("@geoman-io/leaflet-geoman-free")

            map = L.map(mapElement, {
                crs: L.CRS.EPSG4326,
                editable: true
            })
            L.imageOverlay(world, [[-90, -360], [90, 0]]).addTo(map)
            L.imageOverlay(world, [[-90, 0], [90, 360]]).addTo(map)
            L.imageOverlay(world, [[-90, 360], [90, 720]]).addTo(map)
            map.fitBounds([[-90, 0], [90, 360]])
            map.setZoom(1)

            map.pm.addControls({  
                position: 'topleft',  
                editMode: false,  
            })

            const polygon = L.geoJSON(
                worldPolygon as any,
                {
                    onEachFeature(feature, layer) {
                        layer.on({
                            click(e) {
                                // @ts-ignore
                                layer.pm.enable()
                                map.on("preclick", () => {
                                    // @ts-ignore
                                    layer.pm.disable()
                                })
                            }
                        })
                    }
                 }
            ).addTo(map)

            map.on("click", ({latlng}) => {
                lat = latlng.lat
                lng = mod(latlng.lng, 360)
            })
            map.on("mousemove", ({latlng}) => {
                moveLat = latlng.lat
                moveLng = mod(latlng.lng, 360)
            })
        } else { console.log("HO") }
    })

    const format = (n: number) =>
        n.toFixed(5).substring(0, 5)

    const convert = (c: number) => {
        const doh = Math.floor(c)
        const bun = Math.floor((c - doh) * 60)
        const cho = ((c - doh) * 60 - bun) * 60
        return {doh, bun, cho}
    }

    const toDeg = (lat_: number, lng_: number) => {
        const lat = convert(lat_)
        let result =  ``
            +`${String(Math.abs(lat.doh)).padStart(2)}° `
            +`${String(lat.bun).padStart(2)}’ `
            +`${format(lat.cho)}” `
            + `${lat_ > 0 ? "U" : "K" }, `
        if (lng_ < 180) {
            const lng = convert(lng_)
            result += ``
                +`${String(lng.doh).padStart(3)}° `
                +`${String(lng.bun).padStart(2)}’ `
                +`${format(lng.cho)}” `
                + `N`
        } else {
            const lng = convert(360-lng_)
            result += ``
                +`${String(lng.doh).padStart(3)}° `
                +`${String(lng.bun).padStart(2)}’ `
                +`${format(lng.cho)}” `
                + `V`
        }
            
        return result
    }
    
</script>

<style>
    @import "leaflet/dist/leaflet.css";
    @import "@geoman-io/leaflet-geoman-free/dist/leaflet-geoman.css";
    .map {
        border: 1px solid black;
        height: 800px;
    }
    :global(.leaflet-container) {
        background: #5e95b4;
    }
    latlong {
        white-space: pre;
        font-family: monospace;
        font-size: 2rem;
        font-weight: 600;
    }
    code {
        background: #eef;
        padding: 5px;
    }
    hbox {
        display: flex;
        flex-direction: row;
        align-items: center;
        gap: 2rem;
    }
    vbox {
        display: flex;
        flex-direction: column;
    }
</style>

<hbox>
<code>
{"{{"}틀:지도 크롭/경위도
<br/>|lat={(mod(180+lng, 360)-180).toFixed(1)}
<br/>|long={lat.toFixed(1)}
<br/>|size=2
<br/>|width=400
<br/>|height=300
<br/>{"}}"}
</code>
    <vbox>
        <latlong style="color:black">{toDeg(lat, lng)}</latlong>
        <latlong style="color:grey">{toDeg(moveLat, moveLng)}</latlong>
        <br/>지도 클릭해 좌표 저장 / 국경선 클릭해 편집
    </vbox>
</hbox>

<div class="map" bind:this={mapElement}/>
