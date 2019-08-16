<script>
    import { createEventDispatcher } from 'svelte';

    export let sortColumn = undefined;
    export let sortAsc = undefined;

    function setSortColumn(column) {
        if (!sortColumn || sortColumn !== column) {
            sortColumn = column;
            sortAsc = true;
        } else if (sortAsc) {
            sortAsc = false;
        } else {
            sortColumn = undefined;
            sortAsc = undefined;
        }
    }
</script>

<style>
    div {
        background-color: #ffaa40;
    }

    span {
        display: inline-block;
        font-weight: bolder;
        padding-bottom: 1em;
        padding-top: 1em;
        user-select: none;
        -webkit-touch-callout: none;
        -webkit-user-select: none;
        -khtml-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
    }

    .name {
        width: 40%;
    }
    .insured {
        width: 10%;
    }
    .wage {
        width: 10%;
    }
    .econ {
        width: 20%;
    }
    .municipality {
        width: 18%;
    }
</style>

<div>
    <span on:click={() => setSortColumn('n')} class="name">
        Kompanija {sortColumn === 'n' ? (sortAsc ? '▲' : '▼') : ''}
    </span>
    <span on:click={() => setSortColumn('i')} class="insured">
        Apd. {sortColumn === 'i' ? (sortAsc ? '▲' : '▼') : ''}
    </span>
    <span on:click={() => setSortColumn('w')} class="wage">Alga {sortColumn === 'w' ? (sortAsc ? '▲' : '▼') : ''}</span>
    <span on:click={() => setSortColumn('e')} class="econ">
        Veikla {sortColumn === 'e' ? (sortAsc ? '▲' : '▼') : ''}
    </span>
    <span on:click={() => setSortColumn('m')} class="municipality">
        Regionas {sortColumn === 'm' ? (sortAsc ? '▲' : '▼') : ''}
    </span>
</div>
