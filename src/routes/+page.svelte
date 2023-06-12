<script>
 import LeafletMap from '$lib/LeafletMap.svelte';

 let geselecteerdeGemeente = { properties: { namedut: "laden..." }};
 let score = 0;
 let laatsteStats = { score: 0, afstand: 0, tonen: false };
 let rondes = 0;
 let maxRondes = 10;
 $: plaatsnaam = geselecteerdeGemeente.properties.namedut;
 let resetKaartFn;

 function nieuweGemeente(event) {
    geselecteerdeGemeente = event.detail;
    rondes++;
 }

 function reset() {
    score = 0;
    rondes = 0;
    laatsteStats = { score: 0, afstand: 0, tonen: false };
    resetKaartFn();
 }

 function meerScore(event) {
     laatsteStats.score = event.detail.pluspunten;
     laatsteStats.afstand = event.detail.afstand;
     laatsteStats.tonen = true;
     score += event.detail.pluspunten;
 }


</script>

<div class="grow-1 flex flex-row justify-between">
    <div>
        {#if rondes <= maxRondes}
        <p>Waar ligt <b>{plaatsnaam}</b>?</p>
        <p>Dit is ronde {rondes} van de {maxRondes}.</p>
        <p>Je score is {score}.</p>

        {#if laatsteStats.tonen}
            <div class="bg-white rounded-md shadow-md p-4 my-4">
                <p>Je vorige gok zat er {laatsteStats.afstand} kilometer naast. Dat leverde je {laatsteStats.score} {laatsteStats.score == 1 ? 'punt' : 'punten'} op!</p>
            </div>
        {/if}

        {:else}
        <p class="text-xl">Je totale score is <b>{score}</b>.</p>
        <button class="p-6 bg-emerald-500 rounded-md text-white shrink text-xl font-bold hover:bg-emerald-800 transition-all" on:click={reset}>nog een keer</button>
        {/if}
    </div>
    <LeafletMap on:nieuweGemeente={nieuweGemeente} on:meerScore={meerScore} bind:resetKaart={resetKaartFn} actief={rondes <= maxRondes}/>
</div>
