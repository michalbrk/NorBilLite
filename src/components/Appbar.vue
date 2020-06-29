<template>
    <div>
        <v-app-bar app flat color="transparent">
            <v-avatar>
                <v-img 
                    @click="backToHomePage" 
                    contain 
                    src="@/assets/norSM.png">
                </v-img>
            </v-avatar>
        </v-app-bar>
    </div>
</template>

<script>
import { EventBus } from '@/EventBus.js'
export default {
  methods: {
    backToHomePage(){
        EventBus.$emit('back-to-homepage')
    }
  }
}
</script>