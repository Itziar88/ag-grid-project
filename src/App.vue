<template>
    <div style="height: 100%">
        <div style="margin-bottom: 5px;">
            <button @click="fillLarge">Fill 100%</button>
            <button @click="fillMedium">Fill 60%</button>
            <button @click="fillExact">Exactly 400 x 400 pixels</button>
        </div>
        <div class="test-container" style="height: 100%">
            <div class="test-header">
                Selection:
                <span id="selectedRows"></span>
            </div>
            <ag-grid-vue :style="{width, height}" class="ag-theme-balham" id="myGrid"
                :gridOptions="gridOptions"
                @grid-ready="onGridReady"
                :columnDefs="columnDefs"
                :rowSelection="rowSelection"
                :rowData="rowData"
                :rowMultiSelectWithClick="true"
                @selection-changed="onSelectionChanged"
            >
            </ag-grid-vue>
        </div>
    </div>
</template>

<script>
import {AgGridVue} from "ag-grid-vue";

export default {
	name: 'App',
	data() {
		return {
            gridOptions: null,
            gridApi: null,
            columnApi: null,
            columnDefs: null,
            rowSelection: null,
            rowData: null,
            height: '100%',
            width: '100%'
		}
	},
	components: {
		AgGridVue
	},
	beforeMount() {
        this.gridOptions = { floatingFilter: true };
        this.columnDefs = [
            {
                headerName: "Athlete",
                field: "athlete",
                width: 150,
                filter: "agTextColumnFilter",
                filterParams: {
                    resetButton: true,
                    applyButton: true,
                    debounceMs: 200
                }
            },
            {
                headerName: "Age",
                field: "age",
                width: 90,
                filter: "agNumberColumnFilter",
                filterParams: {
                filterOptions: [
                        'lessThan',
                        {
                            displayKey: 'lessThanWithNulls',
                            displayName: 'Less Than with Nulls',
                            test: function(filterValue, cellValue) {
                                return cellValue == null || cellValue < filterValue;
                            }
                        },
                        'greaterThan',
                        {
                            displayKey: 'greaterThanWithNulls',
                            displayName: 'Greater Than with Nulls',
                            test: function(filterValue, cellValue) {
                                return cellValue == null || cellValue > filterValue;
                            }
                        }
                    ]
                }
            },
            {
                headerName: "Country",
                field: "country",
                width: 120,
                filter: true,
            },
            {
                headerName: "Year",
                field: "year",
                width: 90
            },
            {
                headerName: "Date",
                field: "date",
                width: 110
            },
            {
                headerName: "Sport",
                field: "sport",
                width: 110,
                filter: "agTextColumnFilter",
                filterParams: {
                    filterOptions: [
                        "contains",
                        {
                            displayKey: "startsA",
                            displayName: 'Starts With "A"',
                            test: (filterValue, cellValue) => {
                                return cellValue != null && cellValue.indexOf("a") == 0;
                            },
                            hideFilterInput: true
                        },
                        {
                            displayKey: "startsB",
                            displayName: 'Starts With "N"',
                            test: function(filterValue, cellValue) {
                                return cellValue != null && cellValue.indexOf("n") == 0;
                            },
                            hideFilterInput: true
                        }
                    ]
                }
            },
            {
                headerName: "Gold",
                field: "gold",
                width: 100
            },
            {
                headerName: "Silver",
                field: "silver",
                width: 100
            },
            {
                headerName: "Bronze",
                field: "bronze",
                width: 100
            },
            {
                headerName: "Total",
                field: "total",
                width: 100
            }
        ];
        // this.rowSelection = "single";
        this.rowSelection = "multiple";
    },
    mounted() {
        this.gridApi = this.gridOptions.api;
        this.gridColumnApi = this.gridOptions.columnApi;
    },
    methods: {
        fillLarge() {
            console.log("here");
            this.setWidthAndHeight('100%', '100%');
        },
        fillMedium() {
          this.setWidthAndHeight('60%', '60%');
        },
        fillExact() {
          this.setWidthAndHeight('400px', '400px');
        },
        setWidthAndHeight(width, height) {
          this.width = width;
          this.height = height;
        },
        onSelectionChanged() {
            var selectedRows = this.gridApi.getSelectedRows();
            document.querySelector("#selectedRows").innerHTML = selectedRows.length === 1 ? selectedRows[0].athlete : "";
        },
        onGridReady(params) {
            const httpRequest = new XMLHttpRequest();
            const updateData = data => {
                this.rowData = data;
            };
            httpRequest.open(
                "GET",
                "https://raw.githubusercontent.com/ag-grid/ag-grid/master/packages/ag-grid-docs/src/olympicWinnersSmall.json"
            );
            httpRequest.send();
            httpRequest.onreadystatechange = () => {
                if (httpRequest.readyState === 4 && httpRequest.status === 200) {
                    updateData(JSON.parse(httpRequest.responseText));
                }
            };
        }
    },
}
</script>

<style lang="scss">
@import "../node_modules/ag-grid-community/dist/styles/ag-theme-balham.css";
@import "../node_modules/ag-grid-community/dist/styles/ag-grid.css";

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
html {
    width: 100%;
    height: 100%;
}
body {
    height: 100%;
}

</style>
