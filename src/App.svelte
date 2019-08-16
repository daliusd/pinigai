<script>
    import { onMount } from 'svelte';
    import VirtualList from '@sveltejs/svelte-virtual-list';

    import Company from './Company.svelte';
    import CompanyHeader from './CompanyHeader.svelte';
    import PageSelector from './PageSelector.svelte';

    let data = [];
    let sortedData = [];
    let dataToShow = [];
    let promise;

    let windowWidth;
    let windowHeight;

    let itemsPerPage = 0;
    let sortColumn;
    let sortAsc;

    $: {
        if (!sortColumn) {
            sortedData = data;
        } else {
            let copyData = [...data];
            copyData.sort((a, b) =>
                sortColumn === 'i' || sortColumn === 'w'
                    ? sortAsc
                        ? a[sortColumn] - b[sortColumn]
                        : b[sortColumn] - a[sortColumn]
                    : sortAsc
                    ? (a[sortColumn] || '').localeCompare(b[sortColumn] || '')
                    : (b[sortColumn] || '').localeCompare(a[sortColumn] || ''),
            );
            copyData = copyData.map((i, idx) => ({ ...i, idx }));
            sortedData = copyData;
        }
    }

    $: pageNo = itemsPerPage ? 0 : -1;
    $: dataToShow = itemsPerPage
        ? sortedData.slice(pageNo * itemsPerPage, pageNo * itemsPerPage + itemsPerPage)
        : sortedData;

    async function getData(month) {
        const response = await fetch(`/data/${month}.json`);
        const json = await response.json();
        data = json.map((i, idx) => ({ ...i, idx }));
    }

    onMount(async function() {
        promise = getData('201906');
    });
</script>

<style>
    .companies {
        min-width: 400px;
    }

    label {
        display: inline-block;
    }
</style>

<svelte:window bind:innerWidth={windowWidth} bind:innerHeight={windowHeight} />

<h1>Pinigai</h1>

{#await promise}
    <p>Palaukite, duomenys kraunami...</p>
{:then}
    <label>
        <input type="radio" bind:group={itemsPerPage} value={0} />
        Nepuslapiuoti
    </label>

    <label>
        <input type="radio" bind:group={itemsPerPage} value={10} />
        10
    </label>

    <label>
        <input type="radio" bind:group={itemsPerPage} value={20} />
        20
    </label>

    <label>
        <input type="radio" bind:group={itemsPerPage} value={50} />
        50
    </label>

    <label>
        <input type="radio" bind:group={itemsPerPage} value={100} />
        100
    </label>
    {#if itemsPerPage}
        <PageSelector total={data.length} {itemsPerPage} bind:pageNo />
    {/if}

    <div class="companies" style="height: {windowHeight - 200}px ">
        <CompanyHeader bind:sortColumn bind:sortAsc />
        <VirtualList items={dataToShow} let:item>
            <Company company={item} />
        </VirtualList>
    </div>
{:catch error}
    <p style="color: red">{error.message}</p>
{/await}
