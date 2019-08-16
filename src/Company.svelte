<script>
    export let company;
</script>

<style>
    .name {
        display: inline-block;
        width: 40%;
    }
    .insured {
        display: inline-block;
        width: 10%;
    }
    .salary {
        display: inline-block;
        width: 10%;
    }
    .category {
        display: inline-block;
        width: 38%;
    }
</style>

<div>
    <span class="name">{company.n}</span>
    <span class="insured">{company.i}</span>
    <span class="salary">{company.w}</span>
    <span class="category">{company.m}</span>
</div>
