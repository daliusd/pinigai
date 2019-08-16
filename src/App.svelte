<script>
    import { onMount } from 'svelte';
    import VirtualList from '@sveltejs/svelte-virtual-list';

    import Company from './Company.svelte';
    import CompanyHeader from './CompanyHeader.svelte';

    let data = [];
    let dataToShow = [];
    let promise;

    let windowWidth;
    let windowHeight;

    async function getData(month) {
        const response = await fetch(`/data/${month}.json`);
        const json = await response.json();
        data = json.map((i, idx) => ({ ...i, idx }));
        dataToShow = data;
    }

    function sort(event) {
        const { column, asc } = event.detail;
        if (!column) {
            dataToShow = data;
        } else {
            let copyData = [...data];
            copyData.sort((a, b) =>
                column === 'i' || column === 'w'
                    ? asc
                        ? a[column] - b[column]
                        : b[column] - a[column]
                    : asc
                    ? (a[column] || '').localeCompare(b[column] || '')
                    : (b[column] || '').localeCompare(a[column] || ''),
            );
            copyData = copyData.map((i, idx) => ({ ...i, idx }));
            dataToShow = copyData;
        }
    }

    onMount(async function() {
        promise = getData('201906');
    });
</script>

<style>
    .companies {
        min-width: 400px;
    }
</style>

<svelte:window bind:innerWidth={windowWidth} bind:innerHeight={windowHeight} />

<h1>Pinigai</h1>

{#await promise}
    <p>Palaukite, duomenys kraunami...</p>
{:then}
    <div class="companies" style="height: {windowHeight - 200}px ">
        <CompanyHeader on:sort={sort} />
        <VirtualList items={dataToShow} let:item>
            <Company company={item} />
        </VirtualList>
    </div>
{:catch error}
    <p style="color: red">{error.message}</p>
{/await}
