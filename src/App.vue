<template>
  <button @click="isHidden=!isHidden">{{ !isHidden ? 'Проверить франшизу' : 'Показать форму' }}</button>
  <div v-if="isHidden">
    <franchise/>
  </div>
  <div v-if="!isHidden">
    <Form/>
  </div>
</template>

<script>

import Form from "@/components/Form";
import Franchise from "@/components/Franchise";

export default {
  name: 'App',
  components: {
    Franchise,
    Form

  },
  data() {
    return {
      isHidden: false
    }
  }
}

</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;

}
</style>
