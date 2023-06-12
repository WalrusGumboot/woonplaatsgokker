<script>
  import "../app.css";
</script>

<svelte:head>
  <link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"
        integrity="sha256-p4NxAoJBhIIN+hmNHrzRCf9tD/miZyoHS5obTRR9BMY="
        crossorigin=""/>
</svelte:head>

<div class="p-6 flex flex-col">
    <div class=" mb-8 p-6 rounded-md bg-white shadow-md flex flex-row justify-between">
        <a class="text-2xl font-bold" href="/">woonplaatsgokker</a>
        <a class="underline text-xl" href="/over">over dit spel</a>
    </div>
    <slot />
</div>
