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
        padding: 0.5em;
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
    .wage3 {
        width: 100px;
    }
    .econ {
        width: 300px;
    }
    .municipality {
        width: 200px;
    }

    .active {
        background-color: #ccf;
    }
</style>

<div class={company.idx % 2 === 0 ? 'odd' : 'even'} class:active on:click>
    <span class="name">{company.n}</span>
    <span class="insured">{company.i}</span>
    <span class="wage">{company.w}</span>
    <span class="wage3">{company.a3 || ''}</span>
    <span class="wage3">{company.m3 || ''}</span>
    <span class="wage3">{company.f3 || ''}</span>
    <span class="wage3">{company.l3 || ''}</span>
    <span class="wage3">{company.s3 || ''}</span>
    <span class="wage3">{company.wi || ''}</span>
    <span class="econ">{company.e || ''}</span>
    <span class="municipality">{company.m}</span>
</div>
