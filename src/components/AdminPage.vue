<!-- Admin.vue -->
<template>
  <v-container>
    <v-dialog v-model="showAddDialog" max-width="500">
      <v-card>
        <v-card-title>Add a new category</v-card-title>
        <v-card-text>
          <v-text-field
            v-model="newCategory"
            label="Category name"
          ></v-text-field>
        </v-card-text>
        <v-card-actions>
          <v-btn color="green" @click="addCategory">Add</v-btn>
          <v-btn @click="showAddDialog = false">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="showEditDialog" max-width="500">
      <v-card>
        <v-card-title>Edit category</v-card-title>
        <v-card-text>
          <v-text-field
            v-model="editedCategory.name"
            label="Category name"
          ></v-text-field>
        </v-card-text>
        <v-card-actions>
          <v-btn color="green" @click="save()">Save</v-btn>
          <v-btn @click="close()">Cancel</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-dialog v-model="showDeleteDialog" max-width="500">
      <v-card>
        <v-card-title
          >Are you sure you want to delete this category</v-card-title
        >
        <v-card-actions>
          <v-btn color="red" @click="confirmDelete()">Yes</v-btn>
          <v-btn @click="closeDelete()">No</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>

    <v-data-table :headers="headers" :items="categories">
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>Application</v-toolbar-title>
          <v-btn
            color="blue"
            prepend-icon="mdi-plus"
            variant="tonal"
            @click="showAddDialog = true"
            >Add a new category</v-btn
          >
        </v-toolbar>
      </template>
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon color="green" @click="editCategory(item)"> mdi-pencil </v-icon>
        <v-icon color="red" @click="deleteCategory(item)"> mdi-delete </v-icon>
      </template>
    </v-data-table>
  </v-container>
</template>

<script>
export default {
  data() {
    return {
      // Dialogs
      showAddDialog: false,
      showDeleteDialog: false,
      showEditDialog: false,

      // Table headers
      headers: [
        { title: "Id", align: "start", key: "id" },
        { title: "Name", key: "name" },
        { title: "Actions", key: "actions", sortable: false },
      ],

      categories: [],
      newCategory: "",
      editedIndex: -1,
      editedCategory: null,
    };
  },

  // Get the categories from the database at the start
  mounted() {
    if (localStorage.getItem("data")) {
      this.categories = JSON.parse(localStorage.getItem("data"));
    }
  },

  methods: {

    addCategory() {
      // Find the first available id
      let id = 1;
      let i = 0;
      while (i < this.categories.length) {
        if (this.categories[i].id === id) {
          id++;
          i = 0;
          continue;
        }
        i++;
      }

      // Create the new category
      this.editedCategory = {
        id: id,
        name: this.newCategory,
      };

      // Add the new category to the local storage
      this.categories.push(this.editedCategory);
      localStorage.setItem("data", JSON.stringify(this.categories));

      this.newCategory = "";
      this.close();
    },

    editCategory(category) {
      // Take a copy of the category and show the edit dialog
      this.editedIndex = this.categories.indexOf(category);
      this.editedCategory = Object.assign({}, category);
      this.showEditDialog = true;
    },

    deleteCategory(category) {
      // Take a copy of the category and show the delete dialog
      this.editedIndex = this.categories.indexOf(category);
      this.editedCategory = Object.assign({}, category);
      this.showDeleteDialog = true;
    },

    confirmDelete() {
      // Delete the category from the local storage
      this.categories.splice(this.editedIndex, 1);
      localStorage.setItem("data", JSON.stringify(this.categories));
      this.closeDelete();
    },

    closeDelete() {
      // Close the delete dialog and reset the edited category
      this.showDeleteDialog = false;
      this.$nextTick(() => {
        this.editedCategory = Object.assign({}, null);
        this.editedIndex = -1;
      });
    },

    close() {
      // Close the add or edit dialog and reset the edited category
      this.showAddDialog = false;
      this.showEditDialog = false;
      this.$nextTick(() => {
        this.editedCategory = Object.assign({}, null);
        this.editedIndex = -1;
      });
    },

    save() {
      // Save the edited category to the local storage
      Object.assign(this.categories[this.editedIndex], this.editedCategory);
      localStorage.setItem("data", JSON.stringify(this.categories));
      this.close();
    },
  },
};
</script>

<style scoped>
</style>
