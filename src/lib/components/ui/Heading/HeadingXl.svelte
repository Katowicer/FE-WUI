<script lang="ts">
    let { children } = $props();
</script> 

<h1 class="scroll-m-20 text-4xl font-light tracking-tight lg:text-5xl">
    {@render children() }
</h1>
