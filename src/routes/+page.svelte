<script>
 import LeafletMap from '$lib/LeafletMap.svelte';

 let geselecteerdeGemeente = { properties: { namedut: "laden..." }};
 let score = 0;
 let laatsteScore = 0;
 let rondes = 0;
 let maxRondes = 10;
 $: plaatsnaam = geselecteerdeGemeente.properties.namedut;

 function nieuweGemeente(event) {
    geselecteerdeGemeente = event.detail;
    rondes++;
 }

 function reset() {
    score = 0;
    rondes = 0;
 }

 function meerScore(event) { laatsteScore = event.detail.pluspunten; score += event.detail.pluspunten }
</script>

<div class="p-6 flex flex-col">
    <h1 class="text-2xl font-bold mb-8">Woonplaatsgokker</h1>
    {#if rondes <= maxRondes}
    <div class="grow-1 flex flex-row justify-between">
        <div>
            <p>Waar ligt <b>{plaatsnaam}</b>?</p>
            <p>Dit is ronde {rondes} van de {maxRondes}.</p>
            <p>In de vorige ronden heb je {laatsteScore} punten verdiend.</p>
            <p>Je score is {score}.</p>
        </div>
        <LeafletMap on:nieuweGemeente={nieuweGemeente} on:meerScore={meerScore}/>
    </div>
    {:else}
        Je totale score is {score}.

        <button class="p-6 bg-emerald-500 rounded-md text-white shrink" on:click={reset}>nog een keer</button>
    {/if}
</div>
