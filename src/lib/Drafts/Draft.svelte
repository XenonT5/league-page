<script>
  	import DataTable, { Head, Body, Row, Cell } from '@smui/data-table';
	import LinearProgress from '@smui/linear-progress';
    import { onMount } from 'svelte';
    import DraftRow from './DraftRow.svelte';
    import { cleanName } from '$lib/utils/helper'
    
    export let draftData, previous = false;

    const {draftOrder, draft, currentManagers, originalManagers, accuracy, draftType} = draftData;

    let progress = 0;
    let closed = false;

    onMount(loadAccuracy);

    function loadAccuracy() {
        if(!accuracy) return;
        let timer;
        progress = 0;
        closed = false;
        clearInterval(timer);
        timer = setInterval(() => {
            progress += 0.01;
            if (progress >= accuracy) {
                clearInterval(timer);
                if (progress >= 1) {
                    progress = 1;
                    closed = true;
                }
            }

        }, 100);
    }
</script>

<style>
    .accuracy {
        display: block;
        width: 80%;
        max-width: 800px;
        margin: 2em auto 3em;
    }

    .accuracyText {
        font-size: 0.7em;
        color: #666;
    }

    .disclaimer {
        font-style: italic;
        color: #888;
    }

    :global(.draftBoard) {
        display: block;
        width: 95%;
        margin: 2em auto 3em;
        overflow-x: auto;
    }

	:global(.draftTeam) {
        font-size: 0.8em;
		text-align: center;
		padding: 5px 0;
		background-color: rgb(245, 252, 255);
        white-space: break-spaces;
        line-height: 1em;
        height: 5em;
        vertical-align: initial;
	}

	:global(.draftBoard table) {
        border-collapse: collapse;
        table-layout: fixed;
        width: 100%;
        min-width: 1200px;
	}

    :global(.draftBoard td) {
        border-right: 1px solid #ddd;
        height: 7em;
        font-size: 0.7em;
    }

    :global(.draftBoard td:last-of-type) {
        border-right: none;
    }

	.avatar {
		border-radius: 50%;
        height: 30px;
        width: 30px;
        margin: 0.4em 0;
		border: 0.25px solid #777;
	}
	
	:global(.curDraftName) {
        color: #888;
        font-size: 0.7em;
        font-style: italic;
    }
</style>

{#if accuracy  && !closed}
    <div class="accuracy">
        <div class="accuracyText">
            Upcomig draft order accuracy: {parseInt(progress*100)}%
            <span class="disclaimer">(accuracy will improve as the regular season progresses)</span>
        </div>
        <LinearProgress {progress} {closed} />
    </div>
{/if}

<DataTable class="draftBoard">
    <Head>
        <Row>
            {#each draftOrder as draftPosition}
                {#if draftPosition}
                    <Cell class="draftTeam">
                        <img class="avatar" src="{originalManagers[draftPosition].avatar}" alt="{originalManagers[draftPosition].name} avatar"/>
                        <br />{originalManagers[draftPosition].name}{@html currentManagers && cleanName(currentManagers[draftPosition].name) != cleanName(originalManagers[draftPosition].name) ? `<br /><span class="curDraftName">(${currentManagers[draftPosition].name})</span>` : ''}
                    </Cell>
                {/if}
            {/each}
        </Row>
    </Head>
    <Body>
        {#each draft as draftRow, row}
            <DraftRow {draftRow} row={row + 1} {previous} {draftType} />
        {/each}
    </Body>
</DataTable>

