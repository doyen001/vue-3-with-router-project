<template>
  <div class="home">
    <h1>Your favorite fruits</h1>

    <table-lite
      :is-static-mode="false"
      :is-loading="table.isLoading"
      :columns="table.columns"
      :rows="table.rows"
      :total="table.totalRecordCount"
      :sortable="table.sortable"
      :pageSize="table.pageSize"
    ></table-lite>
  </div>
</template>

<script>
// @ is an alias to /src
import { defineComponent, reactive, ref, computed, watch } from "vue";
import axios from "axios";
import TableLite from "vue3-table-lite";

export default defineComponent({
  name: "Home",
  components: {
    TableLite,
  },
  setup() {
    const searchTerm = ref(""); // Search text

    const data = reactive({
      rows: [],
    });

    /**
     * Get server data request
     */
    const myRequest = async (name = "", family = "", perPage = 5, page = 1) => {
      try {
        table.isLoading = true;
        const response = await axios.get("http://localhost:8080/fruit", {
          params: {
            name,
            family,
            "per-page": perPage,
            page,
          },
        });

        table.isLoading = false;
        return response.data;
      } catch (error) {
        console.log("Fetch error", error);
      }
    };

    // Table config
    const table = reactive({
      isLoading: false,
      columns: [
        {
          label: "ID",
          field: "id",
          width: "3%",
          sortable: true,
          isKey: true,
        },
        {
          label: "Genus",
          field: "genus",
          width: "10%",
          sortable: true,
        },
        {
          label: "Name",
          field: "name",
          width: "10%",
          sortable: true,
        },
        {
          label: "Family",
          field: "family",
          width: "10%",
          sortable: true,
        },
        {
          label: "Order",
          field: "order",
          width: "10%",
          sortable: true,
        },
        {
          label: "Carbohydrates",
          field: "n_carbohydrates",
          width: "10%",
          sortable: true,
        },
        {
          label: "Protein",
          field: "n_protein",
          width: "10%",
          sortable: true,
        },
        {
          label: "Fat",
          field: "n_fat",
          width: "10%",
          sortable: true,
        },
        {
          label: "Calories",
          field: "n_calories",
          width: "10%",
          sortable: true,
        },
        {
          label: "Sugar",
          field: "n_sugar",
          width: "10%",
          sortable: true,
        },
      ],
      rows: computed(() => {
        return data.rows;
      }),
      pageSize: computed(() => {
        return 5;
      }),
      totalRecordCount: computed(() => {
        return table.rows.length;
      }),
      sortable: {
        order: "id",
        sort: "asc",
      },
    });

    /**
     * Use vue.js watch to watch your state's change
     */
    watch(
      () => searchTerm.value,
      (val) => {
        myRequest(val).then((newData) => {
          data.page = newData?.page || {};
          data.rows = newData?.rows || [];
        });
      }
    );

    // Get data on first rendering
    myRequest("").then((newData) => {
      data.page = newData?.page || {};
      data.rows = newData?.rows || [];
    });

    return {
      searchTerm,
      table,
    };
  },
});
</script>