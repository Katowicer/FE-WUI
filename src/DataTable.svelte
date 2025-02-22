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

    /* Table and properties */
    const table = $state(
        new TableHandler(accountings, {
            rowsPerPage: 20,
            highlight: true,
            selectBy: "Id",
        }),
    );

    /* Searching properties */
    const search = table.createSearch();

    /* Row count */
    const { start, end, total } = $derived(table.rowCount);

    /* Column view */
    const view = table.createView([
        { index: 1, name: "Id" },
        { index: 2, name: "Data" },
        { index: 3, name: "Destinatario" },
        { index: 4, name: "Mittente" },
        { index: 5, name: "TipoDocumento" },
        { index: 6, name: "Descrizione" },
        { index: 7, name: "Imponibile" },
        { index: 8, name: "Imposta" },
        { index: 9, name: "Importo" },
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

    const availableRowsPerPage: number[] = [
        5,
        10,
        20,
        50,
        table.rowCount.total,
    ];
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
    <td
        ><a href={`HTML/${Id}.html`} aria-labelledby="html"
            ><svg
                xmlns="http://www.w3.org/2000/svg"
                width="16"
                height="16"
                fill="currentColor"
                class="bi bi-file-earmark-fill"
                viewBox="0 0 16 16"
            >
                <path
                    d="M4 0h5.293A1 1 0 0 1 10 .293L13.707 4a1 1 0 0 1 .293.707V14a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V2a2 2 0 0 1 2-2m5.5 1.5v2a1 1 0 0 0 1 1h2z"
                />
            </svg></a
        ></td
    >

    <td
        ><a href={`XML/${Id}.xml`} aria-labelledby="xml">
            <svg
                xmlns="http://www.w3.org/2000/svg"
                width="16"
                height="16"
                fill="currentColor"
                class="bi bi-filetype-xml"
                viewBox="0 0 16 16"
            >
                <path
                    fill-rule="evenodd"
                    d="M14 4.5V14a2 2 0 0 1-2 2v-1a1 1 0 0 0 1-1V4.5h-2A1.5 1.5 0 0 1 9.5 3V1H4a1 1 0 0 0-1 1v9H2V2a2 2 0 0 1 2-2h5.5zM3.527 11.85h-.893l-.823 1.439h-.036L.943 11.85H.012l1.227 1.983L0 15.85h.861l.853-1.415h.035l.85 1.415h.908l-1.254-1.992zm.954 3.999v-2.66h.038l.952 2.159h.516l.946-2.16h.038v2.661h.715V11.85h-.8l-1.14 2.596h-.025L4.58 11.85h-.806v3.999zm4.71-.674h1.696v.674H8.4V11.85h.791z"
                />
            </svg></a
        ></td
    >

    <td
        ><a href={`PDF/${Id}.pdf`} aria-labelledby="pdf"
            >
<svg
    xmlns="http://www.w3.org/2000/svg"
    width="16"
    height="16"
    fill="currentColor"
    class="bi bi-file-pdf-fill"
    viewBox="0 0 16 16"
>
    <path
        d="M4 0h5.293a1 1 0 0 1 .707.293L13.707 4a1 1 0 0 1 .293.707V14a2 2 0 0 1-2 2H4a2 2 0 0 1-2-2V2a2 2 0 0 1 2-2m5.5 1.5v2a1 1 0 0 0 1 1h2z"
    />
    <text
        x="5"
        y="12"
        font-size="5"
        font-weight="bold"
        fill="white"
    >
        PDF
    </text>
</svg>
           </a
        ></td
    >
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
                        onchange={() => {
                            column.toggle();
                        }}
                        class="w-4 h-4 rounded border-gray-300 focus:ring-blue-500"
                    />
                    <button
                        class="text-gray-700 hover:bg-sky-200"
                        onclick={() => {
                            column.toggle();
                        }}>{column.name}</button
                    >
                </div>
            {/each}
        </div>

        <!-- Utilities -->
        <div class="flex flex-col h-full justify-center gap-5">
            <div>
                <div class="w-full max-w-sm">
                    <div class="relative">
                        <input
                            type="text"
                            placeholder="Filter"
                            bind:value={search.value}
                            oninput={oninputSearch}
                            class="w-full px-4 py-2 text-gray-900 border rounded-lg shadow-sm focus:outline-none focus:ring-2 focus:ring-blue-500 focus:border-blue-500 border-gray-300"
                        />
                        <svg
                            class="absolute right-3 top-2.5 h-5 w-5 text-gray-400"
                            xmlns="http://www.w3.org/2000/svg"
                            fill="none"
                            viewBox="0 0 24 24"
                            stroke="currentColor"
                        >
                            <path
                                stroke-linecap="round"
                                stroke-linejoin="round"
                                stroke-width="2"
                                d="M21 21l-4.35-4.35M16.5 10.5a6 6 0 1 0-12 0 6 6 0 0 0 12 0z"
                            />
                        </svg>
                    </div>
                </div>
            </div>

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

        <!-- Complessivi contabili -->
        <div class="flex flex-col h-full justify-between gap-2">
            <div class="flex justify-between">
                <p class="text-sm text-gray-700 font-medium">Imponibile:</p>
                <p class="text-sm text-gray-700 font-medium">
                    {euroFormat.format(ImponibileComplessivo)}
                </p>
            </div>
            <div class="flex justify-between">
                <p class="text-sm text-gray-700 font-medium">Imposta:</p>
                <p class="text-sm text-gray-700 font-medium">
                    {euroFormat.format(ImpostaComplessiva)}
                </p>
            </div>
            <div class="flex justify-between">
                <p class="text-sm text-gray-700 font-medium">Importo:</p>
                <p class="text-sm text-gray-700 font-medium">
                    {euroFormat.format(ImportoComplessivo)}
                </p>
            </div>
        </div>
    </aside>

    <!-- Table -->
    <Datatable {table}>
        <table>
            <thead>
                <tr>
                    <td></td>
                    <ThSort {table} field={"Id"}>Id</ThSort>
                    <ThSort {table} field={"Data"}>Data</ThSort>
                    <ThSort {table} field={"Destinatario"}>Destinatario</ThSort>
                    <ThSort {table} field={"Mittente"}>Mittente</ThSort>
                    <ThSort {table} field={"TipoDocumento"}
                        >TipoDocumento</ThSort
                    >
                    <ThSort {table} field={"Descrizione"}>Descrizione</ThSort>
                    <ThSort {table} field={"Imponibile"}>Imponibile</ThSort>
                    <ThSort {table} field={"Imposta"}>Imposta</ThSort>
                    <ThSort {table} field={"Importo"}>Importo</ThSort>
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
                    <ThFilter {table} field={"Id"} />
                    <ThFilter {table} field={"Data"} />
                    <ThFilter {table} field={"Destinatario"} />
                    <ThFilter {table} field={"Mittente"} />
                    <ThFilter {table} field={"TipoDocumento"} />
                    <ThFilter {table} field={"Descrizione"} />
                    <ThFilter {table} field={"Imponibile"} />
                    <ThFilter {table} field={"Imposta"} />
                    <ThFilter {table} field={"Importo"} />
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
