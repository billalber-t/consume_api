<template>
     <v-app>
    
 <Header />
      
    
   

    <v-main>
      <router-view />
    </v-main>

<Footer />
  </v-app>
</template>
<script>
import Header from  '../components/table/Header.vue'
import Footer from  '../components/table/Footer.vue'

export default {
    name:"table_view",
    components:{
        Header,
        Footer
    }
  
}
</script>
