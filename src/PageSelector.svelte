<script>
    import { afterUpdate } from 'svelte';

    export let total;
    export let itemsPerPage;
    export let pageNo;

    let refs = [];

    afterUpdate(() => {
        let totalPages = Math.floor((total - 1) / itemsPerPage);
        let newRefs = [0];

        let bigStep = Math.floor(totalPages / 5);
        let nextPage = 0;

        while (nextPage + bigStep < pageNo) {
            nextPage += bigStep;
            newRefs.push(nextPage);
        }

        let near = bigStep / 2;
        while (near > 1) {
            let nextPotentialPage = pageNo - Math.floor(near);
            if (nextPotentialPage > nextPage && nextPotentialPage < pageNo) {
                nextPage = nextPotentialPage;
                newRefs.push(nextPage);
            }

            near /= 2;
        }

        if (pageNo !== 0) {
            newRefs.push(pageNo);
        }
        nextPage = pageNo;

        while (near < bigStep) {
            let nextPotentialPage = nextPage + Math.ceil(near);
            if (nextPotentialPage < totalPages) {
                nextPage = nextPotentialPage;
                newRefs.push(nextPage);
            }

            near *= 2;
        }

        while (nextPage + bigStep < totalPages) {
            nextPage += bigStep;
            newRefs.push(nextPage);
        }

        if (nextPage !== totalPages) {
            newRefs.push(totalPages);
        }

        refs = newRefs;
    });
</script>

<style>
    div {
        display: flex;
        flex-wrap: wrap;
    }

    span {
        margin: 5px;
    }

    span.active {
        font-weight: bold;
    }

    span:hover {
        color: #fcc;
    }
</style>

<div>
    <span>Puslapis:</span>
    {#each refs as ref}
        <span class={ref === pageNo ? 'active' : ''} on:click={() => (pageNo = ref)}>{ref}</span>
    {/each}
</div>
