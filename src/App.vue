<template>
	<div>
		<button @click="getSelectedRows()">Get Selected Rows</button>
		<ag-grid-vue style="height: 700px;"
			class="ag-theme-balham"
			:columnDefs="columnDefs"
			:rowData="rowData"
            :gridOptions="gridOptions"
            :autoGroupColumnDef="autoGroupColumnDef"
			rowSelection="multiple"
            rowDragManaged=true
			@grid-ready="onGridReady"
		>
		</ag-grid-vue>
	</div>
</template>

<script>
import {AgGridVue} from "ag-grid-vue";

export default {
	name: 'App',
	data() {
		return {
            gridOptions: null,
			columnDefs: null,
            rowData: null,
            gridApi: null,
            columnApi: null,
            autoGroupColumnDef: null,
		}
	},
	components: {
		AgGridVue
	},
	methods: {
        onGridReady(params) {
            this.gridApi = params.api;
            this.columnApi = params.columnApi;
        },
        getSelectedRows() {
            const selectedNodes = this.gridApi.getSelectedNodes();
            const selectedData = selectedNodes.map( node => node.data );
            const selectedDataStringPresentation = selectedData.map( node => node.make + ' ' + node.model).join(', ');
            alert(`Selected nodes: ${selectedDataStringPresentation}`);
        }
    },
	beforeMount() {
        this.gridOptions = {
            // rowStyle: {background: 'beige'},
            // rowClass: 'my-class',
            getRowClass: function (params) {
                if(params.node.rowIndex % 2 === 0) {
                    return 'my-class';
                }
            },
            rowHeight: 35,
        };
        this.defaultColDef = { resizable: true, filter: true };
		this.columnDefs = [
            {
                headerName: 'Users',
                children: [
                    {flex: 1, headerName: 'Name', field: 'name', rowDrag: true},
                    {flex: 1, headerName: 'Username', field: 'username'},
                    {flex: 1, headerName: 'Email', field: 'email'},
                    {flex: 1, headerName: 'City', field: 'address.city'},
                    {flex: 1, headerName: 'Phone', field: 'phone'},
                    {flex: 1, headerName: 'Company', field: 'company.name', sortable: true},
                ]
            }
        ];
        fetch('https://jsonplaceholder.typicode.com/users')
            .then(result => result.json())
            .then(rowData => this.rowData = rowData);
    },
}
</script>

<style lang="scss">
@import "../node_modules/ag-grid-community/dist/styles/ag-theme-balham.css";
@import "../node_modules/ag-grid-community/dist/styles/ag-grid.css";
// @import "../node_modules/ag-grid-community/src/styles/ag-grid.scss";
// @import "../node_modules/ag-grid-community/src/styles/ag-theme-balham/sass/ag-theme-balham.scss";

#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.my-class {
    background-color: rgba(antiquewhite, 0.2) !important;
}
.ag-row-hover {
    background-color: rgba(aqua, 0.2) !important;
}
.ag-column-hover {
    background-color: rgba(burlywood, 0.2);
}
</style>
