<script>
    import { onMount, onDestroy, createEventDispatcher } from 'svelte';
    import { browser } from '$app/environment';

    const dispatch = createEventDispatcher();

    import gok_icoon from "$lib/gok_icoon.png";
    import plaats_icoon from "$lib/plaats_icoon.png";

    import grens from '$lib/grens.json';
    let gemeentes;
    
    let mapElement;
    let map;
    
    let huidigeGemeente;
    let gezieneGemeentes = [];

    let leaflet;

    let waargenomenStront;

    export let actief = true;

    const maxAfstandInMeter = 289380; // grootste afstand die je kunt afleggen in belgie

    function laadPlaats() {
        let nieuweIdx = -1;

        do {
            nieuweIdx = Math.floor(Math.random() * gemeentes.length);
        } while (gezieneGemeentes.includes(nieuweIdx));



        huidigeGemeente = gemeentes[nieuweIdx];
        gezieneGemeentes.push(nieuweIdx);
        dispatch("nieuweGemeente", huidigeGemeente);
    }

    function berekenScore(afstand) {
        return Math.round(449.972 - (449.972 / (1 + Math.exp(-(afstand-26000)/12500))))
    }

    export const resetKaart = () => {
        waargenomenStront.clearLayers();
        gezieneGemeentes = [];
        laadPlaats();
    }
    
    onMount(async () => {
        if(browser) {
            gemeentes = (await import('$lib/gemeentes.json')).features;
            leaflet = await import('leaflet');
            const Proj = await import("proj4leaflet");

            waargenomenStront = new leaflet.LayerGroup();


            const gok_marker = new leaflet.Icon({iconUrl: gok_icoon, iconSize: [16, 16]})
            const plaats_marker = new leaflet.Icon({iconUrl: plaats_icoon, iconSize: [16, 16]})

            laadPlaats()

            const projectie = new Proj.CRS("EPSG:3812", "+proj=lcc +lat_0=50.797815 +lon_0=4.35921583333333 +lat_1=49.8333333333333 +lat_2=51.1666666666667 +x_0=649328 +y_0=665262 +ellps=GRS80 +towgs84=0,0,0,0,0,0,0 +units=m +no_defs")

            map = leaflet.map(mapElement, {
                center: [50.641111, 4.668056],
                zoom: 8,
                attributionControl: false,
                zoomControl: false,
                dragging: false,
                boxZoom: false,
                doubleClickZoom: false,
                scrollWheelZoom: false,
                touchZoom: false,
            });
            let belgië = leaflet.geoJSON(grens, {
                    style: {
                        color: "#3e3e3e",
                        weight: 1,
                        fillColor: '#ffffff',
                        fillOpacity: 1
                    }
                });

            waargenomenStront.addTo(map)

            belgië.on("click", (event) => {
                if (!actief) {return;}


                let coords = huidigeGemeente.geometry.coordinates;
                let point = new leaflet.Point(coords[0], coords[1]);

                let plaatsLatLng = projectie.unproject(point);

                let eigenMarker = new leaflet.Marker(event.latlng, {icon: gok_marker, interactive: false})
                let plaatsMarker = new leaflet.Marker(plaatsLatLng, {icon: plaats_marker, interactive: false})

                eigenMarker.addTo(waargenomenStront)
                plaatsMarker.addTo(waargenomenStront)

                let polyline = new leaflet.Polyline([event.latlng, plaatsLatLng], {interactive: false})

                polyline.addTo(waargenomenStront);

                let afstand = plaatsLatLng.distanceTo(event.latlng);

                dispatch("meerScore", {
                    pluspunten: berekenScore(afstand),
                    afstand: (afstand/1000).toFixed(2)
                })
                
                laadPlaats()
            })
            
            belgië.addTo(map);
        }
    });

    onDestroy(async () => {
        if(map) {
            console.log('Unloading Leaflet map.');
            map.remove();
        }
    });
</script>


<main>
    <div bind:this={mapElement} class="h-16 p-32"></div>
</main>

<style>
    @import 'leaflet/dist/leaflet.css';
    main div {
        width: 800px;
        height: 800px;
        background: none !important;
    }
</style>
