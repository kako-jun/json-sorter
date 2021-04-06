<template>
  <div class="flex flex-col min-h-screen">
    <main class="flex-grow">
      <Home></Home>
    </main>
    <footer class="bg-gray-900">
      <p class="p-2 text-center text-gray-300">
        Copyright © kako-jun
        <!-- <fa icon="github-square" type="fab"></fa> -->
        <!-- <fa icon="git-alt" type="fab" class="h-4 w-4 inline"></fa> -->
        <a href="https://github.com/kako-jun/json-sorter" target="_blank"
          ><fa icon="home" class="h-4 w-4 inline"></fa
        ></a>
        <a href="https://twitter.com/kako_jun_42" target="_blank"
          ><fa icon="twitter" type="fab" class="ml-1 h-4 w-4 inline"></fa
        ></a>
      </p>
    </footer>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from "vue-class-component";
import Home from "./components/Home.vue";

@Options({
  components: {
    Home,
  },
})
export default class App extends Vue {}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
