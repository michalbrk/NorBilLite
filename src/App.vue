<template>
<!-- Changing background image after re-loading -->
  <div :class="{ [`regular-background-${this.getRandomNumber()}`]: regularBackground,
                'blurred-background': blurredBackground,
                'black-background': blackBackground }">
    <v-app id="delete-background">
      <Appbar />
      <v-spacer v-if="spacer"></v-spacer>
      <v-main>
        <Search />
        <Gallery />
      </v-main>
    </v-app>
  </div>
</template>

<script>
import { EventBus } from '@/EventBus.js'
import Appbar from '@/components/Appbar'
import Search from '@/components/Search'
import Gallery from '@/components/Gallery'
export default {
  name: 'App',
  components: {
    Appbar,
    Search,
    Gallery
  },
  data(){
    return {
      spacer: true,
      regularBackground: true,
      blurredBackground: false,
      blackBackground: false
    }
  },
  mounted(){
    //listening to all events coming from components
    //and toggling display accordingly
    EventBus.$on('blur-background', () => {
      this.regularBackground = false
      this.blurredBackground = true
      this.spacer = false
    })
    EventBus.$on('large-image-open', () => {
      this.blurredBackground = false
      this.blackBackground = true
      this.spacer = true
    })
    EventBus.$on('large-image-closed', () => {
      this.blackBackground = false
      this.blurredBackground = true
      this.spacer = true
    })
  },
  beforeUpdate(){
    EventBus.$on('back-to-homepage', () => {
      this.regularBackground = true
      //Only moment when it is not certain if an
      //event been triggered with the large image
      //opened or not
      if(this.blurredBackground){
        this.blurredBackground = false
      } else if(this.blackBackground){
        this.blackBackground = false
      }
      this.spacer = true
    })
  },
  beforeDestroy(){
    EventBus.$off('blur-background')
    EventBus.$off('large-image-open')
    EventBus.$off('large-image-closed')
    EventBus.$off('back-to-homepage')
  },
  methods: {
    getRandomNumber(){
      return Math.floor(Math.random() * Math.floor(9))
    }
  }
};
</script>

<style lang="scss" scoped>
@mixin backgroundStyles {
  background-position: center;
  background-size: cover;
  background-repeat: no-repeat;
}
.regular-background-1 {
  background-image: url('assets/1.jpg');
  @include backgroundStyles;
}
.regular-background-2 {
  background-image: url('assets/2.jpg');
  @include backgroundStyles;
}
.regular-background-3 {
  background-image: url('assets/3.jpg');
  @include backgroundStyles;
}
.regular-background-4 {
  background-image: url('assets/4.jpg');
  @include backgroundStyles;
}
.regular-background-5 {
  background-image: url('assets/5.jpg');
  @include backgroundStyles;
}
.regular-background-6 {
  background-image: url('assets/6.jpg');
  @include backgroundStyles;
}
.regular-background-7 {
  background-image: url('assets/7.jpg');
  @include backgroundStyles;
}
.regular-background-8 {
  background-image: url('assets/8.jpg');
  @include backgroundStyles;
}
.blurred-background {
  background-image: url('assets/blurred.jpg');
  @include backgroundStyles;
}
.black-background {
  background: #000000;
}
#delete-background {
  background: none;
}
</style>