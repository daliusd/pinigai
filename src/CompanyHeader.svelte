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
        padding: 0.5em;
        user-select: none;
        -webkit-touch-callout: none;
        -webkit-user-select: none;
        -khtml-user-select: none;
        -moz-user-select: none;
        -ms-user-select: none;
    }

    span:hover {
        background-color: #cc8820;
    }

    .name {
        width: 300px;
    }
    .insured {
        width: 70px;
    }
    .wage {
        width: 100px;
    }
    .econ {
        width: 300px;
    }
    .municipality {
        width: 200px;
    }
</style>

<div>
    <span on:click={() => setSortColumn('n')} class="name">
        Kompanija {sortColumn === 'n' ? (sortAsc ? '▲' : '▼') : ''}
    </span>
    <span on:click={() => setSortColumn('i')} class="insured" title="Apdraustųjų skaičius">
        Apd. {sortColumn === 'i' ? (sortAsc ? '▲' : '▼') : ''}
    </span>
    <span on:click={() => setSortColumn('w')} class="wage" title="Vidutinė alga">
        Alga {sortColumn === 'w' ? (sortAsc ? '▲' : '▼') : ''}
    </span>
    <span on:click={() => setSortColumn('e')} class="econ">
        Veikla {sortColumn === 'e' ? (sortAsc ? '▲' : '▼') : ''}
    </span>
    <span on:click={() => setSortColumn('m')} class="municipality">
        Regionas {sortColumn === 'm' ? (sortAsc ? '▲' : '▼') : ''}
    </span>
</div>
