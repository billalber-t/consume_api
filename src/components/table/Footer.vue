<template>
    <div>
        <v-footer   dense class="success white--text">
            <v-col
                class="text-center"
                cols="12"
                >
                {{ new Date().getFullYear() }} — <strong>CRUD TABLE</strong>
            </v-col>
        </v-footer>
    </div>
</template>
<script>
// import { defineComponent } from '@vue/composition-api'

export default {
    name:"Footer"
    
}
</script>
