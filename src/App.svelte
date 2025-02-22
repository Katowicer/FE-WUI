<script lang="ts">
    import HeadingM from "./lib/components/ui/Heading/HeadingM.svelte";
    import Navbar, {
        type TNavItem,
    } from "./lib/components/ui/Navbar/Navbar.svelte";

    import ricevute from "./lib/data/mr.json" assert { type: "json" };
    import emesse from "./lib/data/me.json" assert { type: "json" };

    import type { Accouting } from "./DataTable.svelte";
    import DataTable from "./DataTable.svelte";

    const routes: TNavItem[] = [
        { id: "R", title: "Ricevute" },
        { id: "E", title: "Emesse" },
    ];

    let selected = $state(routes[0].id);

    let accountingsR: Accouting[] = ricevute;
    let accountingsE: Accouting[] = emesse;
</script>

<div class="flex justify-evenly pt-2 align-middle">
    <HeadingM>Fatturazione Elettronica</HeadingM>
    <Navbar {routes} bind:selected />
</div>
<main>
    {#if selected == "E"}
        <DataTable accountings={accountingsE} />
    {:else if selected == "R"}
        <DataTable accountings={accountingsR} />
    {/if}
</main>
