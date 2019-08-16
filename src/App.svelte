<script>
    import { onMount } from 'svelte';
    import VirtualList from '@sveltejs/svelte-virtual-list';

    import Company from './Company.svelte';
    import CompanyHeader from './CompanyHeader.svelte';
    import PageSelector from './PageSelector.svelte';

    let data = [];
    let interData = [];
    let dataToShow = [];
    let promise;

    let windowWidth;
    let windowHeight;

    let itemsPerPage = 0;
    let sortColumn;
    let sortAsc;

    let lastClickedCompany;
    let nameFilter = '';
    let econFilter = '';
    let municipalityFilter = '';

    $: {
        if (!sortColumn) {
            interData = data;
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
            interData = copyData;
        }

        if (nameFilter) {
            let filterLower = nameFilter.toLowerCase();
            interData = interData.filter(f => f.n.toLowerCase().indexOf(filterLower) !== -1);
        }
        if (econFilter) {
            let filterLower = econFilter.toLowerCase();
            interData = interData.filter(f => f.e && f.e.toLowerCase().indexOf(filterLower) !== -1);
        }
        if (municipalityFilter) {
            let filterLower = municipalityFilter.toLowerCase();
            interData = interData.filter(f => f.m.toLowerCase().indexOf(filterLower) !== -1);
        }
        interData = interData.map((i, idx) => ({ ...i, idx }));
    }

    $: pageNo = itemsPerPage ? 0 : -1;
    $: dataToShow = itemsPerPage
        ? interData.slice(pageNo * itemsPerPage, pageNo * itemsPerPage + itemsPerPage)
        : interData;

    async function getData(month) {
        const response = await fetch(`/data/${month}.json`);
        const json = await response.json();
        data = json.map((i, idx) => ({ ...i, idx }));
    }

    onMount(async function() {
        promise = getData('201906');
    });

    function handleCompanyClick(company) {
        lastClickedCompany = company.n;
        nameFilter = company.n;
        econFilter = company.e || '';
        municipalityFilter = company.m;
    }
</script>

<style>
    .companies {
        min-width: 400px;
    }

    label {
        display: inline-block;
    }

    .filters input {
        padding-right: 40px;
    }

    .filters button {
        margin-left: -40px;
        width: 40px;
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

    <div class="filters">
        Filtrai:
        <input bind:value={nameFilter} placeholder="Vardas" />
        {#if nameFilter}
            <button on:click={() => (nameFilter = '')}>❌</button>
        {/if}
        <input bind:value={econFilter} placeholder="Veikla" />
        {#if econFilter}
            <button on:click={() => (econFilter = '')}>❌</button>
        {/if}
        <input bind:value={municipalityFilter} placeholder="Regionas" />
        {#if municipalityFilter}
            <button on:click={() => (municipalityFilter = '')}>❌</button>
        {/if}
    </div>

    <div class="companies" style="height: {windowHeight - 300}px ">
        <CompanyHeader bind:sortColumn bind:sortAsc />
        <VirtualList items={dataToShow} let:item>
            <Company
                company={item}
                active={lastClickedCompany ? lastClickedCompany === item.n : false}
                on:click={() => handleCompanyClick(item)} />
        </VirtualList>
    </div>
{:catch error}
    <p style="color: red">{error.message}</p>
{/await}
