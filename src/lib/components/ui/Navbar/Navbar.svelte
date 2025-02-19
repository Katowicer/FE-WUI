<script lang="ts">
    export type TNavItem = { title: string; route: string };
    let { routes } = $props<{ routes: TNavItem[] }>();
</script>

{#snippet NavItem({ title, route }: TNavItem)}
    <li>
        <a href={route} class="text-gray-600 hover:text-blue-500">{title}</a>
    </li>
{/snippet}

<nav class="flex justify-center align-middle bg-white shadow-md p-4">
    <ul class="flex space-x-6">
        {#each routes as route}
            {@render NavItem(route)}
        {/each}
    </ul>
</nav>
