<script lang="ts">
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
        selectBy: "Id",
    });

    /* Searching properties */
    const search = table.createSearch();

    /* Row count */
    const { start, end, total } = $derived(table.rowCount);

    /* Column view */
    const view = table.createView([
        { index: 0, name: "Id", isFrozen: true },
        { index: 1, name: "Data" },
        { index: 2, name: "Destinatario" },
        { index: 3, name: "Mittente", isFrozen: true },
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

    const ImponibileComplessivo = $derived(
        table.createCalculation("Imponibile").sum(),
    );
    const ImpostaComplessiva = $derived(
        table.createCalculation("Imposta").sum(),
    );
    const ImportoComplessivo = $derived(
        table.createCalculation("Importo").sum(),
    );

    const csv = table.createCSV();
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
    <td>{@html Id}</td>
    <td
        >{@html new Date(Data).toLocaleDateString(
            dateLanguage,
            dateFormatOptions,
        )}</td
    >
    <td>{@html Destinatario}</td>
    <td>{@html Mittente}</td>
    <td>{@html TipoDocumento}</td>
    <td>{@html Descrizione}</td>
    <td>{@html euroFormat.format(Imponibile)}</td>
    <td>{@html euroFormat.format(Imposta)}</td>
    <td>{@html euroFormat.format(Importo)}</td>
{/snippet}

<div class="flex justify-start align-middle gap-10">
    <!-- Left side panel -->
        <aside
            class="flex flex-col h-full bg-gray-100 p-4 rounded-lg shadow-md w-64 gap-5"
        >
            <!-- Toggle collumns checkboxes -->
            <div id="collumns-toggle">
                {#each view.columns as column}
                    <div class="flex items-center space-x-2 mb-2">
                        <input
                            type="checkbox"
                            checked={column.isVisible}
                            onchange={() => column.toggle()}
                            class="w-4 h-4 rounded border-gray-300 focus:ring-blue-500"
                        />
                        <button
                            class="text-gray-700 hover:bg-sky-200"
                            onclick={() => column.toggle()()}
                            >{column.name}</button
                        >
                    </div>
                {/each}
            </div>

            <!-- Utilities -->
            <div class="flex flex-col h-full justify-center gap-5">
                <!-- Download CSV -->
                <button
                    class="bg-transparent hover:bg-blue-500 text-blue-700 font-semibold hover:text-white py-2 px-4 border border-blue-500 hover:border-transparent rounded"
                    onclick={() => csv.download("accoutings.csv")}
                    >Download as CSV</button
                >
                <!-- Rows -->
                <div class="align-middle justify-between flex gap-2">
                    <p>Rows:</p>
                    <select
                        class="border-1 border-blue-500"
                        bind:value={table.rowsPerPage}
                        onchange={() => table.setPage(1)}
                    >
                        {#each availableRowsPerPage as value}
                            <option {value}>{value}</option>
                        {/each}
                    </select>
                </div>
            </div>
        </aside>

    <Datatable {table}>
        <input type="text" bind:value={search.value} oninput={oninputSearch} />
        <table>
            <thead>
                <tr>
                    <td></td>
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
                    <td class="mx-5 flex gap-2">
                        <input
                            type="checkbox"
                            checked={table.isAllSelected}
                            onclick={() => table.selectAll()}
                        />
                        <p>All</p>
                    </td>
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
                {#each table.rows as accounting}
                    <tr class:active={table.selected.includes(accounting.Id)}>
                        <td>
                            <input
                                type="checkbox"
                                checked={table.selected.includes(accounting.Id)}
                                onclick={() => table.select(accounting.Id)}
                            />
                        </td>
                        {@render tableRow(accounting)}
                    </tr>
                {/each}
                <tr>
                    <td></td>
                    <td></td>
                    <td></td>
                    <td></td>
                    <td></td>
                    <td></td>
                    <td></td>
                    <td>{euroFormat.format(ImponibileComplessivo)}</td>
                    <td>{euroFormat.format(ImpostaComplessiva)}</td>
                    <td>{euroFormat.format(ImportoComplessivo)}</td>
                </tr>
            </tbody>
        </table>

        <!-- Pagination -->
        <footer>
            <div>
                <p class="mx-2 py-2">
                    Showing {start} to {end} of {total} rows
                </p>
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
</div>
