<script lang="ts">
    /* Todo: costumize DataTable: https://vincjo.fr/datatables/docs/client/getting-started/intro */

    export type Accouting = {
        Id: string;
        Data: string;
        Mittente: string | null;
        Destinatario: string;
        TipoDocumento: string;
        Descrizione: string;
        Imponibile: number;
        Imposta: number;
        Importo: number;
    };

    let { accountings } = $props<{ accountings: Accouting[] }>();

    // import fatture from "$lib/data/fatture.json";
    import {
        TableHandler,
        Datatable,
        ThSort,
        ThFilter,
    } from "@vincjo/datatables";

    // import type { Accouting } from "$lib/types.ts";

    const availableRowsPerPage: number[] = [5, 10, 20, 50];

    /* Table and properties */
    const table = new TableHandler(accountings, {
        rowsPerPage: 20,
        highlight: true,
    });

    /* Searching properties */
    const search = table.createSearch();

    /* Row count */
    const { start, end, total } = $derived(table.rowCount);

    /* Column view */
    const view = table.createView([
        { index: 0, name: "Id" },
        { index: 1, name: "Data" },
        { index: 2, name: "Destinatario" },
        { index: 3, name: "Mittente" },
        { index: 4, name: "TipoDocumento" },
        { index: 5, name: "Descrizione" },
        { index: 6, name: "Imponibile" },
        { index: 7, name: "Imposta" },
        { index: 8, name: "Importo" },
    ]);

    /* Output formats */
    const dateFormatOptions: Intl.DateTimeFormatOptions = {
        month: "long",
        day: "2-digit",
        year: "numeric",
    };

    const dateLanguage = "it-IT";

    let euroFormat = Intl.NumberFormat("it-IT", {
        style: "currency",
        currency: "EUR",
    });

    /* Event handlers */
    function oninputSearch() {
        search.set();
    }
</script>

{#snippet tableRow({
    Id,
    Data,
    Destinatario,
    Mittente,
    TipoDocumento,
    Descrizione,
    Imponibile,
    Imposta,
    Importo,
}: Accouting)}
    <tr>
        <td>{Id}</td>
        <td
            >{new Date(Data).toLocaleDateString(
                dateLanguage,
                dateFormatOptions,
            )}</td
        >
        <td>{Destinatario}</td>
        <td>{Mittente}</td>
        <td>{TipoDocumento}</td>
        <td>{Descrizione}</td>
        <td>{euroFormat.format(Imponibile)}</td>
        <td>{euroFormat.format(Imposta)}</td>
        <td>{euroFormat.format(Importo)}</td>
    </tr>
{/snippet}

<Datatable {table}>
    <input type="text" bind:value={search.value} oninput={oninputSearch} />
    <table>
        <thead>
            <tr>
                <ThSort {table} field="Id">Id</ThSort>
                <ThSort {table} field="Data">Data</ThSort>
                <ThSort {table} field="Destinatario">Destinatario</ThSort>
                <ThSort {table} field="Mittente">Mittente</ThSort>
                <ThSort {table} field="TipoDocumento">TipoDocumento</ThSort>
                <ThSort {table} field="Descrizione">Descrizione</ThSort>
                <ThSort {table} field="Imponibile">Imponibile</ThSort>
                <ThSort {table} field="Imposta">Imposta</ThSort>
                <ThSort {table} field="Importo">Importo</ThSort>
            </tr>
            <tr>
                <ThFilter {table} field="Id" />
                <ThFilter {table} field="Data" />
                <ThFilter {table} field="Destinatario" />
                <ThFilter {table} field="Mittente" />
                <ThFilter {table} field="TipoDocumento" />
                <ThFilter {table} field="Descrizione" />
                <ThFilter {table} field="Imponibile" />
                <ThFilter {table} field="Imposta" />
                <ThFilter {table} field="Importo" />
            </tr>
        </thead>
        <tbody>
            {#each table.rows as fattura}
                {@render tableRow(fattura)}
            {/each}
        </tbody>
    </table>
    <footer>
        <select
            class="mx-2 px-2 border-1 border-blue-500"
            bind:value={table.rowsPerPage}
            onchange={() => table.setPage(1)}
        >
            {#each availableRowsPerPage as value}
                <option {value}>{value}</option>
            {/each}
        </select>
        <div>
            <p class="mx-2 py-2">Showing {start} to {end} of {total} rows</p>
            <div class="flex justify-start align-middle">
                {#each table.pagesWithEllipsis as page}
                    <button
                        class="mx-2 bg-transparent hover:bg-blue-500 text-blue-700 font-semibold hover:text-white py-2 px-4 border border-blue-500 hover:border-transparent rounded"
                        class:active={page === table.currentPage}
                        onclick={() => table.setPage(page)}
                        >{page ?? "..."}</button
                    >
                {/each}
            </div>
        </div>
    </footer>
</Datatable>
