<script lang="ts">
    let { children } = $props();
</script> 

<h1 class="text-2xl font-light tracking-tight lg:text-3xl">
    {@render children() }
</h1>
