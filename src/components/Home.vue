<template>
  <div class="home">
    <div class="bg-white dark:bg-gray-600">
      <h1 class="text-gray-900 dark:text-white text-4xl font-bold pt-4">JSON sorter</h1>
      <h2 class="text-gray-600 dark:text-gray-300 mt-8">
        input JSON:
      </h2>

      <div class="mt-4">
        <button
          @click="onPasteFromClipboardClicked"
          class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded inline-flex items-center"
        >
          Paste from clipboard.
        </button>
        <button
          @click="onLoadFromFileClicked"
          class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold ml-2  py-2 px-4 rounded inline-flex items-center"
        >
          Load from file.
        </button>
      </div>
      <textarea
        v-model="srcJSON"
        class="lines-number mt-3 p-2"
        data-type="note"
        cols="100"
        rows="21"
        placeholder="add multiple lines"
      ></textarea>

      <div class="mt-8">
        <button
          @click="onSortKeysNameClicked"
          class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-full"
        >
          Sort by key's name.
        </button>
        <button
          @click="onSortKeysValueClicked"
          class="bg-blue-500 hover:bg-blue-700 text-white font-bold ml-4 py-2 px-4 rounded-full"
        >
          Sort by key's value.
        </button>
        <button
          @click="onSortBothClicked"
          class="bg-blue-500 hover:bg-blue-700 text-white font-bold ml-4 py-2 px-4 rounded-full"
        >
          Sort by both.
        </button>
      </div>

      <hr class="mt-12" />
      <h2 class="text-gray-600 dark:text-gray-300 mt-8 mb-4">
        output JSON:
      </h2>
      <textarea v-model="distJSON" class="lines-number p-2" data-type="note" cols="100" rows="21"></textarea>

      <div class="mt-2 pb-12">
        <button
          @click="onCopyToClipboardClicked"
          class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold py-2 px-4 rounded inline-flex items-center"
        >
          Copy to clipboard.
        </button>
        <button
          @click="onSaveAsFileClicked"
          class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold ml-2 py-2 px-4 rounded inline-flex items-center"
        >
          <span>Save as file.</span>
        </button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Options, Vue } from "vue-class-component";
import _ from "lodash";
// import Hjson from "hjson";
// eslint-disable-next-line
const Hjson = require("hjson");

// const lines_number = require("@/assets/lines_number.min.js");

@Options({
  props: {
    msg: String,
  },
})
export default class Home extends Vue {
  msg!: string;

  srcJSON = "";
  distJSON = "";

  sortObject(src: any) {
    const sortedArray = _.sortBy(_.toPairs(src), (p) => {
      return p[0];
    });

    const sortedObject = Object.fromEntries(sortedArray);
    return sortedObject;
  }

  sortArrayByValueOfKey(src: any, key: string) {
    // const sortedArray = _.sortBy(src, (item) => {
    //   return item[key];
    // });
    const sortedArray = _.orderBy(src, key);

    return sortedArray;
  }

  onPasteFromClipboardClicked(): void {
    console.log("hoge");
    this.srcJSON = '[{b:1, "a": 0},{a:1,b:2}]';
  }

  onLoadFromFileClicked(): void {
    console.log("hoge");
  }

  onSortKeysNameClicked(): void {
    console.log(this.srcJSON);

    // const src = JSON.parse(this.srcJSON);
    const src = Hjson.parse(this.srcJSON);
    console.log(src);

    let sortedObject = src;
    if (_.isObject(src)) {
      sortedObject = this.sortObject(src);
    } else if (_.isArray(src)) {
      sortedObject = this.sortArrayByValueOfKey(src, "a");
    }

    console.log(sortedObject);
    this.distJSON = JSON.stringify(sortedObject, null, 2);
  }

  onSortKeysValueClicked(): void {
    console.log("hoge");
  }

  onSortBothClicked(): void {
    console.log("hoge");
  }

  onCopyToClipboardClicked(): void {
    console.log("hoge");
  }

  onSaveAsFileClicked(): void {
    console.log("hoge");
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
@import "../assets/lines_number.min.css";

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
