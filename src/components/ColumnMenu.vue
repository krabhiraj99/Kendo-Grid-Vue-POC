<template>
    <div>
      <!-- Template for sorting on column -->
      <GridColumnMenuSort
        :column="column"
        :sortable="sortable"
        :sort="sort"
        @closemenu="closeMenu"
        @sortchange="sortChange"
      ></GridColumnMenuSort>
  
      <GridColumnMenuFilter
        :column="column"
        :filterable="filterable"
        :filter="filter"
        @filterfocus="handleFocus"
        @closemenu="closeMenu"
        @expandchange="expandChange"
        @filterchange="filterChange"
      >
      </GridColumnMenuFilter>
  
      <!-- Template for column hide/show -->
      <GridColumnMenuItemGroup>
        <GridColumnMenuItem
          :title="'Columns'"
          :icon-class="'k-i-columns'"
          @menuitemclick="onMenuItemClick"
        />
        <GridColumnMenuItemContent :show="columnsExpanded">
          <div class="k-column-list-wrapper">
            <form @submit="onSubmit" @reset="onReset">
              <div class="k-column-list">
                <div
                  :key="idx"
                  class="k-column-list-item"
                  v-for="(column, idx) in currentColumns"
                >
                  <span v-if="column.title != ' '">
                    <input
                      :id="'column-visiblity-show-' + idx"
                      :class="'k-checkbox k-checkbox-md k-rounded-md'"
                      :type="'checkbox'"
                      :readOnly="true"
                      :disabled="!column.hidden && oneVisibleColumn"
                      :checked="!column.hidden"
                      @click="onToggleColumn(idx)"
                    />
                    <label
                      :for="'column-visiblity-show-' + idx"
                      :class="'k-checkbox-label'"
                      :style="{ userSelect: 'none' }"
                    >
                      {{ column.title }}
                    </label>
                  </span>
                </div>
              </div>
              <div class="k-columnmenu-actions">
                <kbutton type="reset">Reset</kbutton>
                <kbutton :theme-color="'primary'">Save</kbutton>
              </div>
            </form>
          </div>
        </GridColumnMenuItemContent>
      </GridColumnMenuItemGroup>
    </div>
  </template>
  <script>
  import {
    GridColumnMenuFilterVue2,
    GridColumnMenuSort,
    GridColumnMenuItemGroup,
    GridColumnMenuItemContent,
    GridColumnMenuItem,
    filterGroupByField,
  } from "@progress/kendo-vue-grid";
  import { Button } from "@progress/kendo-vue-buttons";
  
  export default {
    props: {
      column: Object,
      sortable: [Boolean, Object],
      sort: {
        type: Array,
      },
      filter: Object,
      filterable: Boolean,
      columns: Array,
    },
    data() {
      return {
        currentColumns: [],
        columnsExpanded: false,
        filterExpanded: false,
        filterValue: "",
        columnResult: [],
        value: "",
      };
    },
  
    components: {
      GridColumnMenuSort: GridColumnMenuSort,
      GridColumnMenuFilter: GridColumnMenuFilterVue2,
      GridColumnMenuItemGroup: GridColumnMenuItemGroup,
      GridColumnMenuItemContent: GridColumnMenuItemContent,
      GridColumnMenuItem: GridColumnMenuItem,
      kbutton: Button,
    },
  
    computed: {
      oneVisibleColumn() {
        return this.currentColumns.filter((c) => !c.hidden).length === 1;
      },
    },
    mounted() {
      this.currentColumns = this.columns;
      
     
    },
    methods: {
      handleFocus(e) {
        this.$emit("contentfocus", e);
      },
      onToggleColumn(id) {
        this.currentColumns = this.currentColumns.map((column, idx) => {
          return idx === id ? { ...column, hidden: !column.hidden } : column;
        });
      },
      onReset(event) {
        event.preventDefault();
        const allColumns = this.columns.map((col) => {
          return {
            ...col,
            hidden: false,
          };
        });
  
        this.currentColumns = allColumns;
        this.onSubmit();
      },
      onSubmit(event) {
        if (event) {
          event.preventDefault();
        }
        this.$emit("columnssubmit", this.currentColumns);
        this.$emit("closemenu");
      },
      onMenuItemClick() {
        const value = !this.columnsExpanded;
        this.columnsExpanded = value;
        this.filterExpanded = value ? false : this.filterExpanded;
      },
      onFilterExpandChange(value) {
        this.filterExpanded = value;
        this.columnsExpanded = value ? false : this.columnsExpanded;
      },
      expandChange() {
        this.$emit("expandchange");
      },
      closeMenu() {
        this.$emit("closemenu");
      },
      filterChange(newDescriptor, e) {
  
        // Styling column menu icon when filtering is applied
        const isColumnActive = filterGroupByField(e.field, newDescriptor);
        const changedColumn = this.columns.find(
          (column) => column.field === e.field
        );
  
        if (changedColumn) {
          changedColumn.headerClassName = isColumnActive ? "active" : "";
        }
  
        // Emit filter event for column menu filtering to work.
        this.$emit("filterchange", newDescriptor, e);
      },
  
  
      sortChange(newDescriptor, e) {
  
        this.$emit("onSortChange", newDescriptor, e);
      },
   
    },
  };
  </script>
  