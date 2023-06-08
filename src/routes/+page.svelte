<script>
 import LeafletMap from '$lib/LeafletMap.svelte';
</script>

<div class="p-6 flex flex-col">
    <h1 class="text-2xl font-bold mb-8">Woonplaatsgokker</h1>
    <div class="grow-1 flex flex-row">
        Waar ligt <b>Antwerpen</b>?
        <LeafletMap />
    </div>
</div>
