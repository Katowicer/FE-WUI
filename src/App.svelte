<script lang="ts">
    import HeadingM from "./lib/components/ui/Heading/HeadingM.svelte";
    import Navbar, {
        type TNavItem,
    } from "./lib/components/ui/Navbar/Navbar.svelte";

    import data from "./lib/data/accoutings_ricevute_test.json";
    import type { Accouting } from "./DataTable.svelte";
    import DataTable from "./DataTable.svelte";

    const routes: TNavItem[] = [
        { id: "R", title: "Ricevute" },
        { id: "E", title: "Emesse" },
    ];

    let selected = $state(routes[0].id);
    const accountings: Accouting[] = data.accountings;
</script>

<div class="flex justify-evenly pt-2 align-middle">
    <HeadingM>Fatturazione Elettronica</HeadingM>
    <Navbar {routes} bind:selected />
</div>
<main>
    {#if selected == "E"}
        <div class="flex justify-center align-middle">
            <p class="text-red-500 text-2xl">Work in progeress!</p>
        </div>
    {:else if selected == "R"}
        <DataTable {accountings} />
    {/if}
</main>
