<template>
  <div class="table">
    <v-data-table
      outlined
      :headers="headers"
      :items="desserts"
      sort-by="calories"
      class="elevation-3 ma-4"
    >
      <template v-slot:top>
        <v-toolbar flat>
          <v-toolbar-title>My CRUD</v-toolbar-title>
          <v-divider class="mx-4" inset vertical></v-divider>
          <v-spacer></v-spacer>
          <v-dialog v-model="dialog" max-width="500px">
            <template v-slot:activator="{ on, attrs }">
              <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">
                New Item
              </v-btn>
            </template>
            <v-card>
              <v-card-title>
                <span class="text-h5">NEW ITEM</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="newItem.name"
                        label="Dessert name"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="newItem.calories"
                        label="Calories"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="newItem.fat"
                        label="Fat (g)"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="newItem.carbs"
                        label="Carbs (g)"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="newItem.protein"
                        label="Protein (g)"
                      ></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="close">
                  Cancel
                </v-btn>
                <v-btn color="blue darken-1" text @click="create">
                  Create
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-if="dialogEdit" v-model="dialogEdit" max-width="500px">
            <!-- <template v-slot:activator="{ on, attrs }">
              <v-btn color="primary" dark class="mb-2" v-bind="attrs" v-on="on">
                New Item
              </v-btn> 
            </template> -->
            <v-card>
              <v-card-title>
                <span class="text-h5">EDIT ITEM</span>
              </v-card-title>

              <v-card-text>
                <v-container>
                  <v-row>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        value = "this item"
                        v-model="editedItem.name"
                        label="Dessert name"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.calories"
                        label="Calories"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.fat"
                        label="Fat (g)"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.carbs"
                        label="Carbs (g)"
                      ></v-text-field>
                    </v-col>
                    <v-col cols="12" sm="6" md="4">
                      <v-text-field
                        v-model="editedItem.protein"
                        label="Protein (g)"
                      ></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-text>

              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="closeEdit">
                  Cancel
                </v-btn>
                <v-btn color="blue darken-1" text @click="update">
                  Update
                </v-btn>
              </v-card-actions>
            </v-card>
          </v-dialog>
          <v-dialog v-model="dialogDelete" max-width="500px">
            <v-card>
              <v-card-title class="text-h5"
                >Are you sure you want to delete this item?</v-card-title
              >
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn color="blue darken-1" text @click="closeDelete"
                  >Cancel</v-btn
                >
                <v-btn
                  color="blue darken-1"
                  text
                  @click="deleteItemConfirm(item)"
                  >OK</v-btn
                >
                <v-spacer></v-spacer>
              </v-card-actions>
            </v-card>
          </v-dialog>
        </v-toolbar>
      </template>
      <template v-slot:[`item.actions`]="{ item }">
        <v-icon small class="mr-2" @click="editItem(item)"> mdi-pencil </v-icon>
        <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
      </template>
      <template v-slot:no-data>
        <v-btn color="primary" @click="initialize"> Reset </v-btn>
      </template>
    </v-data-table>
  </div>
</template>
<script>
import axios from "axios";
export default {
  name: "table_table",

  data: () => ({
    dialog: false,
    dialogDelete: false,
    dialogEdit: false,
    _id: "",

    headers: [
      {
        text: "Dessert (100g serving)",
        align: "start",
        sortable: false,
        value: "name",
      },
      { text: "Calories", value: "calories" },
      { text: "Fat (g)", value: "fat" },
      { text: "Carbs (g)", value: "carbs" },
      { text: "Protein (g)", value: "protein" },
      { text: "Actions", value: "actions", sortable: false },
    ],
    desserts: [],
    editedIndex: -1,
    newItem: {
      name: "",
      calories: 0,
      fat: 0,
      carbs: 0,
      protein: 0,
    },
    editedItem: {
      name: "",
      calories: 0,
      fat: 0,
      carbs: 0,
      protein: 0,
      _id:'',
    },
    defaultItem: {
      name: "",
      calories: 0,
      fat: 0,
      carbs: 0,
      protein: 0,
    },
  }),

  computed: {
    formTitle() {
      return this.editedIndex === -1 ? "New Item" : "Edit Item";
    },
  },

  watch: {
    dialog(val) {
      val || this.close();
    },
    dialogDelete(val) {
      val || this.closeDelete();
    },
    dialogEdit(val){
      val || this.closeEdit();
    },
  },

  created() {
    this.initialize();
  },

  methods: {
    initialize() {
      axios
        .get(
          "https://crudcrud.com/api/de1fa029199a47f995837c4bf8f7af92/desserts/"
        )
        .then((response) => {
          console.log("GET desserts SUCCESSFUL");
          this.desserts = response.data.reverse();
        });
    },

    editItem(item) {

      // this.editedIndex = this.desserts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      this.dialog = false;
      this.dialogEdit = true;
      console.log("GOT to the EDITED ITEMS point")
      console.log(this.editedItem);
    //   editedItem: {
    //   name: "",
    //   calories: 0,
    //   fat: 0,
    //   carbs: 0,
    //   protein: 0,
    // }
    // this.editedItem.name = item.name
    // this.editedItem.calories = item.calories
    // this.editedItem.fat = item.fat
    // this.editedItem.carbs = item.carbs
    // this.editedItem.protein = item.protein
      
    },
    update(){
        
        console.log(this.editedItem)
        
        axios 
         .put(`https://crudcrud.com/api/de1fa029199a47f995837c4bf8f7af92/desserts/${this.editedItem._id}`,
          {name: this.editedItem.name, calories: this.editedItem.calories,fat: this.editedItem.fat,carbs: this.editedItem.carbs, protein: this.editedItem.protein})
         .then((response)=>{
           console.log(response)  
           this.initialize();

         })
         .catch((error)=>{
           console.log("ERROR in the EDITED "+ error)
         })

         this.dialogEdit = false
        
    },

    deleteItem(item) {
      this.editedIndex = this.desserts.indexOf(item);
      this.editedItem = Object.assign({}, item);
      axios
        .delete(
          `https://crudcrud.com/api/de1fa029199a47f995837c4bf8f7af92/desserts/${item._id}`
        )
        .then((response) => {
          console.log(response.data);
          this.initialize();
        })
        .catch((error) => {
          console.log("ERROR in DELETE REQUEST" + error.response);
          console.log(item);
        });

      // this.dialogDelete = true
    },


    // deleteItemConfirm (item) {
    //   console.log(item)

    //   axios
    //   .delete(`https://crudcrud.com/api/de1fa029199a47f995837c4bf8f7af92/desserts/${item._id}`)
    //   .then((response)=>{
    //       console.log(response.data)
    //   }
    //   )
    //   .catch((error)=>{
    //     console.log("ERROR in DELETE REQUEST" + error.response);
    //     console.log(item);
    //   })
    //   // this.desserts.splice(this.editedIndex, 1)
    //   this.closeDelete()

    // },

    close() {
      this.dialog = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },

    closeDelete() {
      this.dialogDelete = false;
      this.$nextTick(() => {
        this.editedItem = Object.assign({}, this.defaultItem);
        this.editedIndex = -1;
      });
    },
     closeEdit() {
      this.dialogEdit = false;
    },

    create() {
      axios
        .post(
          "https://crudcrud.com/api/de1fa029199a47f995837c4bf8f7af92/desserts/",
          this.newItem
        )
        .then((response) => {
          console.log("PUT Request successfull");
          console.log(response);
          this.initialize();
        })
        .catch((error) => {
          console.log("ERROR in the PUT REQUEST" + error.response);
        });

      this.close();
    },
  },
};
</script>
