<template>
  <v-container>
    <v-row>
      <!-- if create is true -->
      <v-form v-if="create" @submit.prevent="onSubmit">
        <v-card-title class="font-weight-boldpa-4 h3"
          >Create friend</v-card-title
        >
        <v-col>
          <v-text-field label="Name" placeholder="Name" v-model="name" outlined>
          </v-text-field>
        </v-col>

        <v-col>
          <v-text-field
            label="Email"
            placeholder="Email"
            v-model="email"
            outlined
          >
          </v-text-field>
        </v-col>

        <v-col>
          <v-text-field
            label="Contact"
            placeholder="Phone Number"
            v-model="contact"
            outlined
          >
          </v-text-field>
        </v-col>
        <v-col>
          <v-btn type="submit"> Submit </v-btn>
        </v-col>
      </v-form>
      <v-form v-if="edit" @submit.prevent="onSubmitEdit">
        <v-card-title class="font-weight-boldpa-4 h3">Edit friend</v-card-title>
        <v-col>
          <v-text-field
            label="Name"
            placeholder="Name"
            v-model="editedItem.name"
            outlined
          >
          </v-text-field>
        </v-col>

        <v-col>
          <v-text-field
            label="Email"
            placeholder="Email"
            v-model="editedItem.email"
            outlined
          >
          </v-text-field>
        </v-col>

        <v-col>
          <v-text-field
            label="Contact"
            placeholder="Phone Number"
            v-model="editedItem.contact"
            outlined
          >
          </v-text-field>
        </v-col>
        <v-col>
          
          <v-btn type="submit" @click="onSubmitEdit">Edit</v-btn>
        </v-col>
      </v-form>
    </v-row>

    <v-row>
      <p>Name : {{ name }}</p>
      <br />
      <p>Email : {{ email }}</p>
      <br />
      <p>Contact : {{ contact }}</p>
      <br />
    </v-row>

    <!-- data table -->
    <v-row>
      <v-data-table :headers="headers" :items="friends">
        <!--  // commented on v-dialog //-->
        <!-- <v-dialog v-model="dialog" max-width="500px">
          <v-form @submit.prevent="onSubmit">
            <v-col>
              <v-text-field
                label="Name"
                placeholder="Name"
                v-model="name"
                outlined
              >
              </v-text-field>
            </v-col>

            <v-col>
              <v-text-field
                label="Email"
                placeholder="Email"
                v-model="email"
                outlined
              >
              </v-text-field>
            </v-col>

            <v-col>
              <v-text-field
                label="Contact"
                placeholder="Phone Number"
                v-model="contact"
                outlined
              >
              </v-text-field>
            </v-col>
            <v-col>
              <input type="submit" value="Submit" class="button" />
            </v-col>
          </v-form>
        </v-dialog> -->

        <!-- // commented out on v-dialog dialogoDelete //-->
        <!-- <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5"
              >Are you sure you want to delete this item?</v-card-title
            >
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="closeDelete"
                >Cancel</v-btn
              >
              <v-btn color="blue darken-1" text @click="deleteItemConfirm"
                >OK</v-btn
              >
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog> -->
        <template v-slot:[`item.actions`]="{ item }">
          <v-icon small class="mr-2" @click="editItem(item)">
            mdi-pencil
          </v-icon>
          <v-icon small @click="deleteItem(item)"> mdi-delete </v-icon>
        </template>
      </v-data-table>
    </v-row>
  </v-container>
</template>
<script>
import axios from "axios";

export default {
  data() {
    return {
      name: "",
      email: "",
      contact: "",

      dialog: false,
      dialogDelete: false,

      headers: [
        {
          text: "Name",
          align: "start",
          sortable: false,
          value: "name",
        },
        {
          text: "Email",
          sortable: false,
          value: "email",
        },
        {
          text: "Mobile Contact",
          value: "contact",
        },
        {
          text: "Actions",
          value: "actions",
        },
      ],

      friends: [],
      create: false,
      edit: false,
      editedIndex: -1,
      editedItem: {
        contact: "",
        email: "",
        name: "",
        _id: "",
      },
    };
  },
  created() {
    this.getFriends();
  },

  methods: {
    onSubmit() {
      axios
        .post(
          "https://crudcrud.com/api/f042c65fa06e4a4c9dfd1464689e8675/friends",
          { name: this.name, email: this.email, contact: this.contact }
        )
        .then(console.log("POST request successful"))
        .catch((error) => {
          console.log("ERROR in the POST Request" + error.response);
        });
      // this.friends.push(friend)

      this.getFriends();

      this.name = "";
      this.email = "";
      this.contact = "";
    },

    onSubmitEdit() {
      let item = this.editedItem;
      let item_id = item._id
      console.log(item);
      axios
        .put(
          `https://crudcrud.com/api/f042c65fa06e4a4c9dfd1464689e8675/friends/${item_id}`,
          {
            name: this.editedItem.name,
            email: this.editedItem.email,
            contact: this.editedItem.contact,
          }
        )
        .then(console.log("PUT  request successful"))
        .catch((error) => {
          console.log("ERROR in the PUT Request" + error.response);
        });
      this.getFriends();
      this.edit = false
      this.create = true;
    },
    getFriends() {
      axios
        .get(
          "https://crudcrud.com/api/f042c65fa06e4a4c9dfd1464689e8675/friends"
        )
        .then((response) => {
          console.log(response.data);
          this.friends = response.data.reverse();
        })
        .catch((error) => {
          console.log("There was an error" + error.response);
        });

      this.create = true;
    },

    editItem(item) {
      // this.editedIndex = this.friends.indexOf(item);
      this.editedItem = item;
      this.edit = true;
      this.create = false;
    },

    deleteItem(item) {
      this.create = false;
      this.edit = false;
      let item_id = item._id;
      axios
        .delete(
          `https://crudcrud.com/api/f042c65fa06e4a4c9dfd1464689e8675/friends/${item_id}`
        )
        .then((response) => {
          // let i = this.friends.map(data => data.id).indexOf(this.friends)
          // this.friends.splice(i , 1)

          console.log(response);
          console.log("Delete request SUCCESSFULL");
        })
        .catch((error) => {
          console.log(
            "There was an ERROR in the DELETE request" + error.response
          );
        });

      this.getFriends();
    },

    // putRequest(){
    //   axios
    //   .put("https://crudcrud.com/api/4de0a27813314629a2c46cf121aa8b21/friends//`${friends.id}`",{
    //     name : 'name',
    //     date : 'date'
    //   })

    // }
  },
};
</script>
