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

    const maxAfstandInMeter = 289380; // grootste afstand die je kunt afleggen in belgie

    function laadPlaats() {
        huidigeGemeente = gemeentes[Math.floor(Math.random() * gemeentes.length)]
        dispatch("nieuweGemeente", huidigeGemeente);
    }

    function berekenScore(afstand) {
        return Math.round(449.972 - (449.972 / (1 + Math.exp(-(afstand-26000)/12500))))
    }
    
    onMount(async () => {
        if(browser) {
            gemeentes = (await import('$lib/gemeentes.json')).features;
            const leaflet = await import('leaflet');
            const Proj = await import("proj4leaflet");

            const gok_marker = new leaflet.Icon({iconUrl: gok_icoon, iconSize: [20, 20]})
            const plaats_marker = new leaflet.Icon({iconUrl: plaats_icoon, iconSize: [20, 20]})

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
                        weight: 0,
                        fillColor: '#efefef',
                        fillOpacity: 1
                    }
                });

            belgië.on("click", (event) => {
                console.log(event.latlng)
                let coords = huidigeGemeente.geometry.coordinates;
                let point = new leaflet.Point(coords[0], coords[1]);
                console.log(point)

                let plaatsLatLng = projectie.unproject(point);

                let eigenMarker = new leaflet.Marker(event.latlng, {icon: gok_marker})
                let plaatsMarker = new leaflet.Marker(plaatsLatLng, {icon: plaats_marker})

                eigenMarker.addTo(map)
                plaatsMarker.addTo(map)

                let polyline = new leaflet.Polyline([event.latlng, plaatsLatLng])

                polyline.addTo(map);

                dispatch("meerScore", {pluspunten: berekenScore(plaatsLatLng.distanceTo(event.latlng))})
                
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
    }
</style>
