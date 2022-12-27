<template>
  <kendo-grid
    style="height: 240px"
    :columns="postColumns"
    :loader="isLoading"
    :data-items="slicedPost"
    :resizable="true"
    :total="total"
    :row-height="15"
    :page-size="15"
    :scrollable="'virtual'"
    :skip="skip"
    :sort="sort"
    :sortable="true"
    @sortchange="sortChangeHandler"
    @pagechange="pageChange"
    :column-menu="columnMenu"
  >
  </kendo-grid>
</template>

<script>
import axios from "axios";
import { Grid } from "@progress/kendo-vue-grid";
import { orderBy } from "@progress/kendo-data-query";

export default {
  components: {
    "kendo-grid": Grid,
  },
  name: "PostDetail",
  props: {
    dataItem: Object,
  },

  data() {
    return {
      sort: [],
      isLoading: false,
      userId: undefined,
      posts: undefined,
      slicedPost: [],
      columnMenu:true,
      total: 0,
      skip: 0,
      fields: ["id", "title", "body"],
      postColumns: [
        { field: "id", title: "Id", width:"75px" },
        { field: "title", title: "Post Title", width:"375px"  },
        { field: "body", title: "Post" },
      ],
    };
  },

  methods: {
    pageChange(event) {
      console.log("pagechangeevent >> ", event);
      this.skip = event.page.skip;
      this.slicedPost = this.posts.slice(this.skip, this.skip + 15);
    },

    sortChangeHandler(e) {
      console.log("sort", e);
      this.sort = e.sort;
      this.slicedPost = orderBy(this.slicedPost, this.sort);
    },
  },

  async created() {
    this.isLoading = true;
    this.userId = this.dataItem.id;
    await axios
      .get(`https://jsonplaceholder.typicode.com/posts?userId=${this.userId}`)
      .then((res) => {
        this.total = res.data?.length;
        this.posts = res.data;
      });
    console.log("Posts in created >> ", this.posts);
    this.slicedPost = this.posts.slice(this.skip, this.skip + 15);
    this.isLoading = false;
  },
};
</script>
