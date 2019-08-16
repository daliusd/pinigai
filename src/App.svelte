<script>
    import { onMount } from 'svelte';
    import VirtualList from '@sveltejs/svelte-virtual-list';

    import Company from './Company.svelte';

    let data = [];
    let promise;

    async function getData(month) {
        const response = await fetch(`/data/${month}.json`);
        const json = await response.json();
        data = json;
    }

    onMount(async function() {
        promise = getData('201906');
    });
</script>

<style>
    .companies {
        height: 100%;
        min-width: 400px;
    }
</style>

<h1>Pinigai</h1>

{#await promise}
    <p>Palaukite, duomenys kraunami...</p>
{:then}
    <div class="companies">
        <VirtualList items={data} let:item>
            <Company company={item} />
        </VirtualList>
    </div>
{:catch error}
    <p style="color: red">{error.message}</p>
{/await}
