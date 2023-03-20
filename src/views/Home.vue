<template>
  <div class="label-div">
    <label>SearchBy Name:</label><input v-model="searchTerm" />
    <label>SearchBy Family:</label><input v-model="searchTerm2" />
    <button class="new_fruit_btn" @click="onButtonClick($event)">new fruit</button>
  </div>

  <table-lite
    :is-static-mode="true"
    :is-loading="table.isLoading"
    :columns="table.columns"
    :rows="table.rows"
    :total="table.totalRecordCount"
    :sortable="table.sortable"
  ></table-lite>
</template>

<script>
import { defineComponent, reactive, ref, computed, createApp, watch, h } from "vue";
import TableLite from "vue3-table-lite";
import axios from "axios";

const searchTerm = ref(""); // Search text
const searchTerm2 = ref(""); // Search text
// Fake data
export default defineComponent({
  name: "App",
  components: { TableLite },
  methods: {
    onButtonClick() {
        const response = confirm("Do you want new fruit?");

        if (response) {
          axios.post('http://localhost:8080/fruit/create', {
            "genus": "Malus",
            "name": "Apple",
            "family": "Rosaceae",
            "order": "Rosales",
            "n_carbohydrates": 13.8,
            "n_protein": 0.3,
            "n_fat": 0.2,
            "n_calories": 52,
            "n_sugar": 10.4
          })
          .then(response => {
            alert("Send a message to admin that the new fruit has been added.");
          })
          .catch(error => {
            // Handle the error
          });
        } else {
            alert("Cancel was pressed");
        }
    }
  },
  setup() {
    const data = reactive({
      rows: [],
    });

    const doSearch = async (name = "", family = "") => {
      return await new Promise((resolve, reject) => {
        try {
          table.isLoading = true;
          axios
            .get("http://localhost:8080/fruit", {
              params: {
                name,
                family,
              },
            })
            .then((res) => {
              table.isLoading = false;
              resolve(res.data);
            });
        } catch (error) {
          console.log("Fetch error", error);
          reject();
        }
      });
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
      totalRecordCount: computed(() => {
        return table.rows.length;
      }),
      sortable: {
        order: "id",
        sort: "asc",
      },
    });

    doSearch().then((res) => {
      data.rows = res?.rows || [];
    });

    watch(
      () => [searchTerm.value, searchTerm2.value],
      ([value, value2]) => {
        doSearch(value, value2).then((res) => {
          data.rows = res?.rows || [];
        });
      }
    );

    return {
      searchTerm,
      searchTerm2,
      table,
    };
  },
});
</script>

<style scope>
  .label-div {
    margin: 20px;
    display: flex;
    grid-gap: 15px;
    align-items: center;
  }
  .new_fruit_btn {
    margin-left: auto;
    color: #fff;
    background-color: #007bff;
    border-color: #007bff;
    border: 1px solid transparent;
    padding: 0.375rem 0.75rem;
  }
</style>