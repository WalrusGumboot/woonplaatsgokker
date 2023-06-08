<script>
    import { onMount, onDestroy } from 'svelte';
    import { browser } from '$app/environment';

    let mapElement;
    let map;

    onMount(async () => {
        if(browser) {
            const leaflet = await import('leaflet');

            map = leaflet.map(mapElement, {
                center: [50.641111, 4.668056],
                zoom: 9,
                attributionControl: false,
                zoomControl: false,
                dragging: false,
                boxZoom: false,
                doubleClickZoom: false,
                scrollWheelZoom: false,
                touchZoom: false,
            });

            // Leaflet.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
            //     attribution: '&copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors'
            // }).addTo(map);
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
    <div bind:this={mapElement}></div>
</main>

<style>
    @import 'leaflet/dist/leaflet.css';
    main div {
        width: 800px;
        height: 800px;
    }
</style>
