<template>
  <div>
    <v-container v-if="isVisibleTextField">
      <v-row justify="center">
        <v-col cols="10" sm="6" md="4" class="mb-0 pb-0">
          <v-dialog v-model="APIKeyDialog" persistent :fullscreen="$vuetify.breakpoint.xsOnly" max-width="30vw">
            <v-card>
              <v-card-title>
                Please provide the authentication key
              </v-card-title>
              <v-card-actions>
                <v-container>
                  <v-row>
                    <v-col>
                      <v-text-field dense :rules="[rules.match]"></v-text-field>
                    </v-col>
                  </v-row>
                </v-container>
              </v-card-actions>
              <v-card-text>*If missing, reach out to gudrunn.andersen@yahoo.com</v-card-text>
            </v-card>
          </v-dialog>
          <v-dialog v-if="isVisibleRadioButtons" :max-width="$vuetify.breakpoint.smAndUp ? '30vw' : '60vw'" scrollable>
            <template v-slot:activator="{ on }">
              <v-row justify="center">
                <v-btn color="error" x-large v-on="on">
                  <v-icon dark left>fas fa-search</v-icon>
                  Choose attraction
                </v-btn>
              </v-row>
            </template>
            <v-card>
              <v-card-title>Select search term</v-card-title>
              <v-divider></v-divider>
              <v-card-text>
                <v-list dense>
                  <v-list-item-group color="primary">
                    <v-list-item v-for="(item, index) in attractions" :key="index" @click="mainInputHandler(item.value)">
                      <v-list-item-icon>
                        <v-icon small>fas fa-check-circle</v-icon>
                      </v-list-item-icon>
                      <v-list-item-content>
                        <v-list-item-title v-text="item.value"></v-list-item-title>
                      </v-list-item-content>
                    </v-list-item>
                  </v-list-item-group>
                </v-list>
              </v-card-text>
            </v-card>
          </v-dialog>
        </v-col>
      </v-row>
      <v-row v-if="isVisibleAPIError" justify="center">
          <v-col cols="10" sm="6" md="4" class="mt-0 pt-0">
          <v-alert type="error">
            {{ warningMessage }}
          </v-alert>
        </v-col>
      </v-row>
    </v-container> 
  </div>
</template>

<script>
import axios from 'axios'
import { EventBus } from '@/EventBus.js'
import { Attractions } from '@/Data.js'

export default {
  data(){
    return {
      APIKeyDialog: null,
      warningMessage: 'Ops! Our bad, try again later. ;)',
      APIKey: '12945024-089af957dfe72c50765f6cd0e',
      attractions: Attractions,
      isVisibleRadioButtons: false,
      isVisibleTextField: true,
      isVisibleAPIError: false,
      rules: {
        match: authValue => {
          if(String(authValue) == this.APIKey){
            this.isVisibleRadioButtons = true
            this.APIKeyDialog = false
            return true
          } else{
            return 'Invalid API Key'
          }
        }
      }
    }
  },
  mounted(){
    this.APIKeyDialog = true
  },
  beforeUpdate(){
    EventBus.$on('back-to-homepage', () => {
      this.isVisibleTextField = true
    })
  },
  beforeDestroy(){
    EventBus.$off('back-to-homepage')
    localStorage.clear()
  },
  methods: {
    async mainInputHandler(valueFromListItem){
      if(!JSON.parse(localStorage.getItem(`${valueFromListItem}`))){
        let galleryItems = await this.getImage(valueFromListItem)
        this.isVisibleTextField = false
        EventBus.$emit('blur-background')
        EventBus.$emit('value-to-display', galleryItems.data.hits)
        localStorage.setItem(`${valueFromListItem}`, JSON.stringify(galleryItems.data.hits))
      } else {
        this.isVisibleTextField = false
        EventBus.$emit('blur-background')
        EventBus.$emit('value-to-display', JSON.parse(localStorage.getItem(`${valueFromListItem}`)))
      }
    },
    getImage(searchTerm){
      let URL = `https://pixabay.com/api/?key=${this.APIKey}&q=${searchTerm}&image_type=photo`
      let responsefromAPI = null
      try {
        responsefromAPI = axios.get(URL)
      } catch (error) {
        this.isVisibleAPIError = true
        this.errorFromAPI = error
      }
      return responsefromAPI
    }
  }
}
</script>