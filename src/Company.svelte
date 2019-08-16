<script>
    export let company;
    export let active;
</script>

<style>
    .odd {
        background-color: #fff;
    }

    .even {
        background-color: #eee;
    }

    div:hover {
        background-color: #fcc;
    }

    span {
        display: inline-block;
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

    .active {
        background-color: #ccf;
    }
</style>

<div class={company.idx % 2 === 0 ? 'odd' : 'even'} class:active on:click>
    <span class="name">{company.n}</span>
    <span class="insured">{company.i}</span>
    <span class="wage">{company.w}</span>
    <span class="econ">{company.e || ''}</span>
    <span class="municipality">{company.m}</span>
</div>
