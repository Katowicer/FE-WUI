<script lang="ts">
    import HeadingM from "./lib/components/ui/Heading/HeadingM.svelte";
    import Navbar from "./lib/components/ui/Navbar/Navbar.svelte";
</script>

<div class="flex justify-evenly pt-2 align-middle">
    <HeadingM>Fatturazione Elettronica</HeadingM>
    <Navbar routes={[{ route: "/emesse", title: "Emesse" }]} /> <!-- Todo: routing? -->
</div>
