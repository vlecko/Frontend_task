<template>
<div id="app">
  <Registration :default-values="defaultValues" />
</div>
</template>

<script>
import Registration from './components/Registration.vue'
export default {
  name: 'App',
  components: {
    Registration
  },
  data() {
    return {
      defaultValues: {
        public_account: 0,
        username: 'Dima',
        role: 2,
        email: 'Dima@gmail.com',
      }
    }
  }
}
</script>

<style>
body {
  background-color: #000;
  margin: 0;
  padding: 0;
}

h1 {
  margin: 0;
  padding: 0;
}

h3 {
  margin: 0;
  padding: 0;
}

#app {
  font-family: 'Montserrat', sans-serif, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}
</style>
