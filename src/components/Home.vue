<template>
  <div class="home">
    <div class="bg-white dark:bg-gray-800">
      <h1 class="text-gray-900 dark:text-white">Dark mode is here!</h1>
      <br />
      <p class="text-gray-600 dark:text-gray-300">
        src JSON:
      </p>
      <textarea v-model="srcJSON" class="" cols="50" rows="21" placeholder="add multiple lines"></textarea>

      <div>
        <button @click="onSortClicked">sort</button>
      </div>

      <p class="text-gray-600 dark:text-gray-300">
        dist JSON:
      </p>
      <textarea v-model="distJSON" class="" cols="50" rows="21"></textarea>
    </div>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from "vue-class-component";
import _ from "lodash";
// import Hjson from "hjson";
// eslint-disable-next-line
var Hjson = require("hjson");

@Options({
  props: {
    msg: String,
  },
})
export default class Home extends Vue {
  msg!: string;

  srcJSON = '[{b:1, "a": 0},{a:1,b:2}]';
  distJSON = "";

  sortObject(src: any) {
    // temp = Object.keys(src).sort();
    const sortedArray = _.sortBy(_.toPairs(src), (p) => {
      return p[0];
    });

    const sortedObject = Object.fromEntries(sortedArray);
    return sortedObject;
  }

  sortArrayByKey(src: any, key: string) {
    const sortedArray = _.sortBy(src, (item) => {
      return item[key];
    });

    return sortedArray;
  }

  onSortClicked(): void {
    console.log(this.srcJSON);

    // const src = JSON.parse(this.srcJSON);
    const src = Hjson.parse(this.srcJSON);
    console.log(src);

    let sortedObject = src;
    if (_.isObject(src)) {
      sortedObject = this.sortObject(src);
    } else if (_.isArray(src)) {
      sortedObject = this.sortArrayByKey(src, "a");
    }

    console.log(sortedObject);
    this.distJSON = JSON.stringify(sortedObject, null, 2);
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
