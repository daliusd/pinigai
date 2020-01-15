<script>
    import { onMount } from 'svelte';
    import VirtualList from '@sveltejs/svelte-virtual-list';

    import Company from './Company.svelte';
    import CompanyHeader from './CompanyHeader.svelte';
    import PageSelector from './PageSelector.svelte';

    let indexData = [];
    let promiseIndex;
    let activeMonth;

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

    let totalWage;
    let totalInsured;

    $: {
        if (!sortColumn) {
            interData = data;
        } else {
            let copyData = [...data];
            copyData.sort((a, b) =>
                ['i', 'w', 'a3', 'm3', 'f3', 'l3', 's3', 'wi'].indexOf(sortColumn) !== -1
                    ? sortAsc
                        ? (a[sortColumn] || 0) - (b[sortColumn] || 0)
                        : (b[sortColumn] || 0) - (a[sortColumn] || 0)
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
        totalWage = interData.reduce((w, i) => i.w * i.i + w, 0);
        totalWage = Math.round(totalWage);
        totalInsured = interData.reduce((t, i) => i.i + t, 0);
    }

    $: pageNo = itemsPerPage ? 0 : -1;
    $: dataToShow = itemsPerPage
        ? interData.slice(pageNo * itemsPerPage, pageNo * itemsPerPage + itemsPerPage)
        : interData;

    async function getIndex() {
        const response = await fetch(`/data/index.json`);
        const json = await response.json();
        indexData = json.map(i => i.replace('.json', ''));
        activeMonth = indexData[indexData.length - 1];
        promise = getData(activeMonth);
    }

    async function getData(month) {
        const response = await fetch(`/data/${month}.json`);
        const json = await response.json();
        data = json.map((i, idx) => ({ ...i, idx }));
    }

    onMount(async function() {
        promiseIndex = getIndex();
    });

    async function changeMonth(month) {
        activeMonth = month;
        data = [];
        promise = getData(activeMonth);
    }

    function handleCompanyClick(company) {
        lastClickedCompany = company.n;
        nameFilter = company.n;
        econFilter = company.e || '';
        municipalityFilter = company.m;
    }
</script>

<style>
    h1 {
        padding: 0;
        margin: 0;
        margin-bottom: 1em;
        font-size: 1.5em;
    }

    .loading {
        color: #c88;
        font-weight: bold;
    }

    .months {
        margin-top: 1em;
        margin-bottom: 1em;
        display: flex;
        flex-wrap: wrap;
    }

    .months span.active {
        font-weight: bolder;
    }

    .months span:hover {
        color: #fcc;
    }

    .months span {
        padding: 0.5em;
    }

    .companies {
        margin: 10px;
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

<p>
    Čia galite peržiūrėti Lietuvos įmonių/kompanijų/įstaigų informaciją apie vidutinę algą ir apdraustųjų skaičių.
    Informaciją galite filtruoti pagal įstaigos pavadinimą, veiklos klasifikatorių arba regioną. Taip pat paspaudę ant
    stulpelio galite informaciją rūšiuoti. Pasirinkus įstaigą iš sąrašo jos informacija įdedama į filtravimo laukus.
</p>

{#await promiseIndex}
    <p class="loading">Palaukite, duomenys kraunami...</p>
{:then}
    <div class="months">
        {#each indexData as month}
            <span class={month === activeMonth ? 'active' : ''} on:click={() => changeMonth(month)}>{month}</span>
        {/each}
    </div>
{:catch error}
    <p style="color: red">{error.message}</p>
{/await}

<div>
    <strong>FUN</strong> stulpelyje paskaičiuota kokia galėtų būti daugiausiai uždirbančio žmogaus alga, jei organizacijoje/įmonėje jis toks būtų tik vienas. Dažniausiai tokių žmonių būna daugiau, taigi realiai daugiausiai uždirbančiojo alga tikėtina bus gerokai mažesnė. Galbūt retais atvejais tą atlyginimą ir atspėjau, bet iš esmės šio skaičiaus nepriimkite labai rimtai ir žiūrėkite kaip į smagų žaidimą.
</div>

<div class="filters">
    Filtrai:
    <input bind:value={nameFilter} placeholder="Pavadinimas" />
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

{#await promise}
    <p class="loading">Palaukite, duomenys kraunami...</p>
{:then}

    <div class="companies">
        <CompanyHeader bind:sortColumn bind:sortAsc />
        <VirtualList items={dataToShow} height="{windowHeight * 0.5}px" let:item>
            <Company
                company={item}
                active={lastClickedCompany ? lastClickedCompany === item.n : false}
                on:click={() => handleCompanyClick(item)} />
        </VirtualList>
    </div>

{:catch error}
    <p style="color: red">{error.message}</p>
{/await}

<div>
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
    <PageSelector total={interData.length} {itemsPerPage} bind:pageNo />
</div>

<div>
    <p>Čia rodoma apskaičiuota statistika pritaikius filtrus:</p>
    <p>Viso išleista algoms: {totalWage.toLocaleString().replace(/,/g, ' ')} €</p>
    <p>Visas apdraustųjų skaičius: {totalInsured.toLocaleString().replace(/,/g, ' ')}</p>
    <p>Įstaigų skaičius: {interData.length}</p>
    <p>
        Vidutinė alga: {Math.round(totalWage / totalInsured)
            .toLocaleString()
            .replace(/,/g, ' ')} €
    </p>
</div>

<div>
    Dalius Dobravolskas &copy; {new Date().getFullYear()}
    <a href="https://github.com/daliusd/pinigai">GitHub kodo repozitorija</a>
</div>
