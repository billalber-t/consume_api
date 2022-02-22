<template>
 <v-container>""
   <v-row>
     <v-form @submit.prevent="onSubmit">
       <v-col>
          <v-text-field
          label="Name"
          placeholder="Name"
          v-model="name"
          outlined>
          
          </v-text-field>
       </v-col>
      
        <v-col>
          <v-text-field
            label="Email"
            placeholder="Email"
            v-model="email"
            outlined>
          </v-text-field>
        </v-col>

        <v-col>
          <v-text-field
            label="Contact"
            placeholder="Phone Number"
            v-model="contact"
            outlined>
          </v-text-field>
        </v-col>
        <v-col>
         <input type="submit" value="Submit" class="button">
        </v-col>
     </v-form>
     
   </v-row>
   <!-- form data -->
    <v-row>
       <p>Name : {{ name }}</p> <br>
       <p>Email : {{ email }}</p><br>
       <p>Contact : {{ contact }}</p><br>
     </v-row>

   <!-- data table -->
   <v-row>
     <v-data-table
     :headers="headers"
     :items="friends"
  >
    
  </v-data-table>

   </v-row>
 </v-container>
    
</template>
<script>

export default {
  data(){
    return{
     
        name:'',
        email:'',
        contact:'',
      
      

      headers:[
        {
          text:"Name",
          align: 'start',
          sortable : false,
          value: 'name'
        },
        {
          text:"Email",
          sortable : false,
          value:'email'
        },
        {
          text:"Mobile Contact",
          value : 'contact'
        }
      ],

      friends:[
        {
          name:'Musyoki Brilliant',
          email:'musyoki@briliant',
          contact:'071557575719'
        },
         {
          name:'Byron Morris',
          email:'byronm@briliant',
          contact:'071557575719'
        },
         {
          name:'Denis Muya',
          email:'muyadenis@briliant',
          contact:'071557575719'
        }
      ]

      
    }
  },
  methods:{
      onSubmit(){
        let friend= {
         name: this.name,
         email: this.email,
         contact: this.contact
        }
        
        this.friends.push(friend)

      this.name = ''
      this.email = ''
      this.contact = ''
      }
    }
}
</script>