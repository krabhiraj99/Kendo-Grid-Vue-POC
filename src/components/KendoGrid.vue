<template>
  <div class="main-div"> 
    
    <kendo-grid
      :style="{height:'calc(100vh - 30px)'}"
      :resizable="true"
      :loader="isLoading"
      :data-items="getData"
      :columns="userColumns"
      :sort="sort"
      :sortable="true"
      :groupable="true"
      :group="group"
      :column-menu="true"
      :filter="filter"

      @datastatechange="dataStateChange"
    >
    <template v-slot:columnMenuTemplate="{ props }">
          <ColumnMenu
            :column="props.column"
            :filterable="props.filterable"
            :filter="props.filter"
            :sortable="props.sortable"
            :sort="props.sort"
            :columns="userColumns"
            @onSortChange="(e) => onSortchange(e)"
            @filterchange="(e) => filterchange(e)"
            @closemenu="(e) => props.onClosemenu(e)"
            @contentfocus="(e) => props.onContentfocus(e)"
            @columnssubmit="onColumnsSubmit"
          >
          </ColumnMenu>
        </template>
    
    </kendo-grid>
   
  </div>
</template>

<script>
import "@progress/kendo-theme-default/dist/all.css";
import { Grid } from "@progress/kendo-vue-grid";
import { process} from "@progress/kendo-data-query";
import ColumnMenu from "./ColumnMenu.vue" ;
import items from "../../items";
export default {
  name: "KendoGrid",
  components: {
    "kendo-grid": Grid,
    ColumnMenu,
  
  },
  data() {
    return {
      cellTemplate: 'myTemplate',
      isLoading:false,
      userData: items,
      finalUserData: items,
      groupingData: items,
      group: [],
      filter:null,
      sort:[],
      basicData: items,
      result: [],


      userColumns: [
        { field: "CFRT", title: "CFRT", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "ASDF", title: "ASDF", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "GHJK", title: "GHJK", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "QWER", title: "QWER", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "TYUI", title: "TYUI", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "OPLK", title: "OPLK", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "JHGF", title: "JHGF", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "FDSA", title: "FDSA", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "ZXCV", title: "ZXCV", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "BNML", title: "BNML", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "TYRE", title: "TYRE", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "KJHG", title: "KJHG", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "QERY", title: "QERY", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "YIOA", title: "YIOA", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "ADGJ", title: "ADGJ", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "GJKL", title: "GJKL", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "ASGH", title: "ASGH", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "SAX1", title: "SAX1", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "SAX2", title: "SAX2", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "SAX3", title: "SAX3", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "SAX4", title: "SAX4", width:"150px", columnMenu:"columnMenuTemplate"},
        { field: "SAX5", title: "SAX5", width:"150px", columnMenu:"columnMenuTemplate"}, 
      ],
    };
  },
  methods: {

    loadGrid() {
      this.isLoading=true;
    this.result = process(this.finalUserData, {
      sort: this.sort,
      group: this.group,
      filter: this.filter,

    });
    setTimeout(() => {
      this.isLoading=false;
    },0);

  },
    
    dataStateChange(event) {
      this.isLoading=true;
      this.group = event.data.group;
      this.sort = event.data.sort;
      this.filter = event.data.filter;
      this.loadGrid();
    },
    onColumnsSubmit(columnsState) {
      this.userColumns = columnsState;
    },
    filterchange(data) {
      this.filter = data;
    },
    onSortchange(data) {
      this.sort = data;
    },
  },

  computed: {
    getData: function(){  
      this.loadGrid();
      return this.result;
      },
  },

 
};
</script>

<style scoped>

 .k-grouping-row .k-command-cell{
  display:none;
 }


 .main-div{
  border: 1px solid lightgray;
  margin: 10px;
  padding-bottom: 10px;
  border-radius: 15px;
  background-color: #FAFAFA;

 }
</style>