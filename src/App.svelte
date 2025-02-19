<script lang="ts">
    import HeadingM from "./lib/components/ui/Heading/HeadingM.svelte";
    import Navbar, {
        type TNavItem,
    } from "./lib/components/ui/Navbar/Navbar.svelte";

    import data from "../../accountings.json";
    import type { Accouting } from "./DataTable.svelte";
    import DataTable from "./DataTable.svelte";

    const routes: TNavItem[] = [{ route: "/emesse", title: "Emesse" }];
    const accountings: Accouting[] = data.accountings;
</script>

<div class="flex justify-evenly pt-2 align-middle">
    <HeadingM>Fatturazione Elettronica</HeadingM>
    <Navbar {routes} />
    <!-- Todo: routing? -->
</div>
<main>
    <DataTable {accountings} />
</main>
