<template>
  <div class="flex flex-col min-h-screen">
    <div><p class="text-2xl font-bold py-6">Form Templates</p></div>
    <div
      class="py-6 flex flex-col lg:flex-row justify-between items-center mb-5"
    >
      <div class="w-[25%]">
        <v-text-field
          :loading="loading"
          density="comfortable"
          variant="solo"
          label="Search templates"
          prepend-inner-icon="mdi-magnify"
          single-line
          hide-details
          class=""
          @click:append-inner=""
        ></v-text-field>
      </div>
      <v-dialog v-model="dialog" persistent width="600" class="p-6">
        <template v-slot:activator="{ props }">
          <v-btn
            prepend-icon="mdi-plus"
            size="large"
            class="bg-primary"
            v-bind="props"
          >
            Create new template
          </v-btn>
        </template>
        <v-container>
          <v-card class="p-6">
            <v-card-title>
              <div class="flex justify-between items-center py-4">
                <div class="text-h5">Create New Template</div>

                <div>
                  <v-btn
                    class="shadow-none outline-none border-none bg-gray-50"
                    variant="icon"
                    icon="$close"
                    @click="dialog = false"
                  >
                  </v-btn>
                </div>
              </div>
            </v-card-title>
            <v-card-text>
              <v-row>
                <v-col cols="12">
                  <label class="text-xl font-medium">Template Name</label>
                  <v-text-field
                    variant="outlined"
                    type="text"
                    class="mt-2"
                    required
                    placeholder="Input Name"
                  ></v-text-field>
                </v-col>
                <v-col cols="12">
                  <label class="text-xl font-medium">Description</label>
                  <v-textarea
                    variant="outlined"
                    type="text"
                    class="mt-2"
                    required
                    placeholder="Type message here"
                  ></v-textarea>
                </v-col>
              </v-row>
            </v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-card-text>
                <v-btn
                  class="bg-primary px-6 rounded-lg"
                  variant="text"
                  size="large"
                  @click="dialog = false"
                >
                  Create
                </v-btn>
              </v-card-text>
            </v-card-actions>
          </v-card>
        </v-container>
      </v-dialog>
    </div>
    <v-data-table
      class="px-4 sm:px-6 lg:pl-72"
      v-model:items-per-page="itemsPerPage"
      :headers="headers"
      :items-length="totalItems"
      :items="serverItems"
      :loading="loading"
      item-value="name"
      :fixed-footer="false"
      @update:options="loadItems"
    ></v-data-table>
  </div>
</template>
<script>
const desserts = [
  {
    name: "Form 1",
    modifiedBy: "Veronica Waltz",
    dateCreated: "12.08.2022, 11:33 AM",
    lastModfied: "12.08.2022, 11:33 AM",
  },
  {
    name: "Form 2",
    modifiedBy: "Veronica Waltz",
    dateCreated: "12.08.2022, 11:33 AM",
    lastModfied: "12.08.2022, 11:33 AM",
  },
];

const openModal = () => {
  console.log("this is modal");
};
const FakeAPI = {
  async fetch({ page, itemsPerPage, sortBy }) {
    return new Promise((resolve) => {
      setTimeout(() => {
        const start = (page - 1) * itemsPerPage;
        const end = start + itemsPerPage;
        const items = desserts.slice();

        if (sortBy.length) {
          const sortKey = sortBy[0].key;
          const sortOrder = sortBy[0].order;
          items.sort((a, b) => {
            const aValue = a[sortKey];
            const bValue = b[sortKey];
            return sortOrder === "desc" ? bValue - aValue : aValue - bValue;
          });
        }

        const paginated = items.slice(start, end);

        resolve({ items: paginated, total: items.length });
      }, 500);
    });
  },
};

export default {
  data: () => ({
    itemsPerPage: 5,
    dialog: false,
    headers: [
      {
        title: "name",
        align: "start",
        sortable: false,
        key: "name",
      },
      { title: "Modified By", key: "modifiedBy", align: "start" },
      { title: "Date Created", key: "dateCreated", align: "start" },
      { title: "Last Modfied", key: "lastModfied", align: "start" },
    ],
    serverItems: [],
    loading: true,
    totalItems: 0,
  }),
  methods: {
    loadItems({ page, itemsPerPage, sortBy }) {
      this.loading = true;
      FakeAPI.fetch({ page, itemsPerPage, sortBy }).then(({ items, total }) => {
        this.serverItems = items;
        this.totalItems = total;
        this.loading = false;
      });
    },
  },
};
</script>

<style scoped>
.v-data-table-footer {
  visibility: hidden;
}
</style>
