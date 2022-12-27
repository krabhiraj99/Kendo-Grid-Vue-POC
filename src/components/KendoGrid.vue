<template>
  <div class="main-div"> 
    <p class="selected-item">
      Selected Item:  {{selectedItem && selectedItem.id}} , {{selectedItem && selectedItem.name}}
    </p>
    <kendo-grid
      :resizable="true"
      :loader="isLoading"
      :data-items="getData"
      :columns="userColumns"
      :sort="sort"
      :sortable="true"
      :pageable="pageable"
      :skip="skip"
      :take="take"
      :total="total"
      :groupable="true"
      :group="group"
      :filter="filter"
      @filterchange="filterChange"
      @sortchange = "sortChangeHandler"
      @pagechange="pageChangeHandler"
      @groupchange="groupChangeHandler"
      :column-menu="columnMenu"
      :detail="cellTemplate"
      @expandchange="expandChange"
      :expand-field="'expanded'"
      :edit-field="'inEdit'"
      @itemchange="itemChange"  
      :selected-field="selectedField"
      @rowclick="onRowClick"
  
    >
   
    <!-- <template v-slot:username="{props}">
      <td class="bgRed">
        {{props.dataItem[props.field]}}
      </td>
    </template>
    <template v-slot:userId="{props}">
      <td class="bgGreen">
        {{props.dataItem[props.field]}}
      </td>
    </template>
    <template v-slot:name="{props}">
      <td class="bgYellow">
        {{props.dataItem[props.field]}}
      </td>
    </template><template v-slot:email="{props}">
      <td class="bgSky">
        {{props.dataItem[props.field]}}
      </td>
    </template>
    <template v-slot:phone="{props}">
      <td class="bgPink">
        {{props.dataItem[props.field]}}
      </td>
    </template>
    <template v-slot:website="{props}">
      <td class="bgViolet">
        {{props.dataItem[props.field]}}
      </td>
    </template> -->
  
    <template v-slot:myTemplate="{props}">
         <post-detail :data-item="props.dataItem"/> 
    </template>
    <template v-slot:editTemplate="{ props }">
        <custom
          :data-item="props.dataItem"
          @edit="edit"
          @save="save"
          @remove="remove"
          @cancel="cancel"
        />
      </template>
    </kendo-grid>
  </div>
</template>

<script>
import axios from "axios";
import "@progress/kendo-theme-default/dist/all.css";
import { Grid } from "@progress/kendo-vue-grid";
import { process, orderBy, filterBy } from "@progress/kendo-data-query";
import Vue from 'vue';
import PostDetail from './PostDetail.vue';
import CommandCell from "./CommandCell.vue";
export default {
  name: "KendoGrid",
  components: {
    "kendo-grid": Grid,
    "post-detail": PostDetail,
    "custom": CommandCell,
  },
  data() {
    return {
      selectedField:"selected",
      selectedID: 1,
      cellTemplate: 'myTemplate',
      isLoading:false,
      editId: null,
      radioGroupValue: 'row',
      columnMenu:true,
      userData: [],
      finalUserData: [],
      groupingData: [],
      skip: 0,
      take: 5,
      total: undefined,
      perPageSize: 5,
      group: [],
      filter:null,
      // sort: [{ field: "id", dir: "asc" }],
      sort:[],
      basicData: [],
      userColumns: [
        { field: "id", title: "User Id", width: "100px" , editable: false,},
        { field: "name", title: "Name", minWidth: "170px"},
        { field: "username", title: "User Name", width: "130px" },
        { field: "email", title: "Email", minWidth: "200px" },
        { field: "phone", title: "Phone", width: "180px" },
        { field: "website", title: "Website", minWidth: "120px"  },
        {cell: 'editTemplate', width: '180px'},
      ],
    };
  },
  methods: {
    
    async getUsers() {
      this.isLoading = true;
      const postData = await axios({
        method: "GET",
        url: `https://jsonplaceholder.typicode.com/users?_start=${this.skip}&_limit=${this.take}`,
        headers: { contentType: "application/json" },
      });
      this.total = postData.headers["x-total-count"]
        ? Number(postData.headers["x-total-count"])
        : 0;

      this.userData = postData.data;
      
      // this.basicData = postData.data.map((currData) => {
      //   return Object.assign(currData, {  expanded: true });
      // });
      this.basicData = this.userData;
      this.finalUserData = this.userData;
      this.groupingData = this.userData;
      this.isLoading = false;
      this.selectedID = this.skip+1;


      // selected: false,

      // this.callProcessFunction();
    },

    // async updateUsers() {
    //   const postData = await axios({
    //     method: "GET",
    //     url: `https://jsonplaceholder.typicode.com/users?_start=${this.skip}&_limit=${this.take}`,
    //     headers: { contentType: "application/json" },
    //   });
    //   this.total = postData.headers["x-total-count"]
    //     ? Number(postData.headers["x-total-count"])
    //     : 0;

    //   console.log("postData", postData.data);
    //   this.basicData = postData.data.map((currData) => {
    //     return Object.assign(currData, { selected: false, expanded: false });
    //   });
    //   this.userData=postData.data; 
    // },
    // callProcessFunction() {
    //   this.userData = process(this.userData, {
    //     skip: this.skip,
    //     take: this.take,
    //     group: this.group,
    //     sort:this.sort,
    //   });
    // },


    sortChangeHandler(e) {
      console.log("sort", e);
      this.sort = e.sort;
      this.finalUserData = orderBy(this.finalUserData, this.sort);
    },
   filterChange(e){
      this.filter = e.filter;
      this.finalUserData =  filterBy(this.finalUserData, this.filter);
    },
    pageChangeHandler(e) {
      console.log("page hand>>", e);
      this.skip = e.page.skip;
      this.take = e.event.value === 'All' ? this.total : e.page.take;
      this.perPageSize = this.take;
      this.getUsers();
    },
    groupData(){
        this.groupingData = process(this.finalUserData, {
          group: this.group,
        })

    },
    groupChangeHandler(e){
      this.group = e.group;
      this.groupData();
      console.log("group event>> ", e);
    },

    expandChange: function (event) {
      console.log("Expand Event>>", event);
      console.log("user in expand>> ", this.finalUserData);
            // event.dataItem[event.target.$options.propsData.expandField] = !event.dataItem.expanded;
              Vue.set(
                    event.dataItem,
                    event.target.$options.propsData.expandField,
                    event.dataItem.expanded === undefined ? false : !event.dataItem.expanded
                  );
        },

    

        update(data, item, remove) {
            let updated;
            let index = data.findIndex(p => item.id && p.id === item.id);
            if (index >= 0) {
                updated = Object.assign({}, item);
                data[index] = updated;
            } else {
                let id = 1;
                this.basicData.forEach(p => { if (p.id) { id = Math.max(p.id + 1, id); } });
                updated = Object.assign({}, item, { id: id 
                });
                data.unshift(updated);
                index = 0;
            }

            if (remove) {
                data = data.splice(index, 1);
            }
            return data[index];
        },

    remove(e) {
      e.dataItem.inEdit = undefined;
      this.update(this.finalUserData, e.dataItem, true);
      // this.update(sampleProducts, e.dataItem, true);
      this.finalUserData = this.finalUserData.slice();
    },

    edit (e) {
      let index = this.finalUserData.findIndex(p => p.id === e.dataItem.id);
      let updated = Object.assign({},this.finalUserData[index], {inEdit:true});
      this.finalUserData.splice(index, 1, updated);
      // this.finalUserData = this.userData;
      console.log("event in edit>>", e);
    },

    save: function (e) {
      e.dataItem.inEdit = undefined;
      // const data = this.finalUserData.slice();
      //  e.dataItem.ProductID = this.update(sampleProducts, e.dataItem).ProductID;
    },

    cancel(e) {
      console.log("inside cancel");
            if (e.dataItem.id) {
                let index = this.finalUserData.findIndex(p => p.id === e.dataItem.id);
                let updateDataIndex = this.basicData.findIndex(p => p.id === e.dataItem.id);
                let updated = Object.assign(this.basicData[updateDataIndex], {'inEdit': undefined});
                this.finalUserData.splice(index, 1, updated);
            } else {
              let index = this.finalUserData.findIndex(p => JSON.stringify(e.dataItem) === JSON.stringify(p)); 
              this.finalUserData.splice(index, 1);
            }
        },

    itemChange(e) {
            if (e.dataItem.id) {
              let index = this.finalUserData.findIndex(p => p.id === e.dataItem.id);
              let updated = Object.assign({},this.finalUserData[index], {[e.field]:e.value});
              this.finalUserData.splice(index, 1, updated);
            } else {
              e.dataItem[e.field] = e.value;
            }
    },
    onRowClick (event) {
            this.selectedID = event.dataItem.id;

        },

    // ------------------------------- *************************** -----------------------------------------**********************-------

    //CODE FOR ENABLE EDITING

    // itemChange(e){
    //   console.log("event in itemchange>>", e);

    //   const data = this.finalUserData.slice();
    //   const index = data.findIndex(d => d.id === e.dataItem.id);
    //   data[index] = {...data[index], [e.field]: e.value};
    //   this.finalUserData = data;
    // },

    // rowClick(e){
    //   console.log("rowclick>>",e);
    //   this.editId = e.dataItem.id;
    //   e.dataItem.inEdit = "true";
        
    // }
    
    // ------------------------------- *************************** -----------------------------------------**********************-------

    // createAppState(dataState){
    //     console.log("datastate>>>",dataState);
    //     this.group= dataState.group;
    //     this.take = dataState.take;
    //     this.skip = dataState.skip;
    //     this.sort = dataState.sort;
    //     this.callProcessFunction();

    // },
    // dataStateChange(e){
    //     this.createAppState(e.data);
    // },
  },
  watch: {
      skip: function(val){
        this.skip = val;
      },

      take: function(val){
        this.take = val;
      },
      
      userData: function(data){
        this.userData = data;
        console.log("userData in watch>> ", data);
      },

  },

  computed: {


    // ------------------------------- *************************** -----------------------------------------**********************-------

    //GET DATA FOR EDITING


    getData: function(){  
      this.groupData();
      return this.groupingData;
      },

      selectedItem () {
            return this.finalUserData.find((item) => item.id === this.selectedID);
        },
    // ------------------------------- *************************** -----------------------------------------**********************-------

    
    pageable(){
      return {
        buttonCount: 5,
        info: true,
        type: 'numeric',
        pageSizes: [2,4,5,6,8, 'All'],
        previousNext: true,
        pageSizeValue: this.perPageSize,
      };
    }
  },

  created() {
    this.getUsers();
  },
};
</script>

<style scoped>
 .bgRed{
  background-color : rgba(249, 213, 8, 0.32);
 }
 .bgGreen{
  background-color : rgba(165, 240, 119, 0.32);
 }
 .bgYellow{
  background-color : rgba(241, 236, 111, 0.32);
 }
 .bgSky{
  background-color : rgba(121, 241, 241, 0.32);
 }
 .bgPink{
  background-color : rgba(241, 111, 226, 0.32);
 }
 .bgViolet{
  background-color : rgba(217, 111, 241, 0.32);
 }

 .k-grouping-row .k-command-cell{
  display:none;
 }
 .selected-item{
  margin: 15px 0 ;
  text-align: center;
  font-weight: 500;
  color: blueviolet;
 }

 .main-div{
  border: 1px solid lightgray;
  margin: 10px;
  padding-bottom: 10px;
  border-radius: 15px;
  background-color: #FAFAFA;

 }
</style>