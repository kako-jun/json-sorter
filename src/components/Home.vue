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

        <label
          for="upload"
          class="bg-gray-300 hover:bg-gray-400 text-gray-800 font-bold ml-2 py-2 px-4 rounded inline-flex items-center cursor-pointer"
        >
          <span class="glyphicon glyphicon-folder-open" aria-hidden="true">
            Load from file.
          </span>
          <input type="file" id="upload" style="display:none" @change="onLoadFromFileClicked" />
        </label>
      </div>
      <textarea
        v-model="srcJSON"
        class="lines-number mt-3 p-2"
        data-type="note"
        cols="100"
        rows="21"
        placeholder="{ key1: value1, key2: value2 }"
      ></textarea>

      <div class="mt-8">
        <div>
          <label class="inline-flex items-center">
            <input v-model="sortingKeysInObjectByNameEnabled" type="checkbox" class="form-checkbox h-6 w-6" />
            <span class="ml-2 text-gray-300">Sort keys in object by name.</span>
          </label>
        </div>
        <div>
          <label class="inline-flex items-center">
            <input v-model="sortingArrayByKeyValueEnabled" type="checkbox" class="form-checkbox h-6 w-6" />
            <span class="ml-2 text-gray-300">Sort array by key's value.</span>
            <input
              v-model="keyNames"
              :disabled="sortingArrayByKeyValueEnabled ? disabled : ''"
              type="email"
              class="form-input ml-2 p-2 rounded"
              placeholder="key_name1, key_name2, ..."
            />
          </label>
        </div>
        <div class="mt-4">
          <span class="text-gray-300">Indent Type:</span>
        </div>
        <div>
          <label class="inline-flex items-center">
            <input v-model="indentType" type="radio" class="form-radio" name="indentType" value="2spaces" />
            <span class="ml-2 text-gray-300">2 spaces</span>
          </label>
          <label class="inline-flex items-center ml-6">
            <input v-model="indentType" type="radio" class="form-radio" name="indentType" value="4spaces" />
            <span class="ml-2 text-gray-300">4 spaces</span>
          </label>
          <label class="inline-flex items-center ml-6">
            <input v-model="indentType" type="radio" class="form-radio" name="indentType" value="1tab" />
            <span class="ml-2 text-gray-300">1 tab</span>
          </label>
        </div>
      </div>
      <div class="mt-8">
        <button
          @click="onSortClicked"
          class="bg-blue-500 hover:bg-blue-700 text-white ml-4 py-2 px-4 rounded-full text-xl"
        >
          Sort.
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
// eslint-disable-next-line
const Hjson = require("hjson");
// import Hjson from "hjson";

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
  sortingKeysInObjectByNameEnabled = false;
  sortingArrayByKeyValueEnabled = false;
  keyNames = "";
  indentType = "2spaces";

  sortKeysInObjectByName(src: any): any {
    const sortedArray = _.sortBy(_.toPairs(src), (p) => {
      return p[0];
    });

    const sortedObject = Object.fromEntries(sortedArray);
    return sortedObject;
  }

  sortArrayByKeyValue(src: any, keyNames: string[]): any {
    const sortedArray = _.orderBy(src, keyNames);
    return sortedArray;
  }

  sortObject(
    src: any,
    sortingKeysInObjectByNameEnabled: boolean,
    sortingArrayByKeyValueEnabled: boolean,
    keyNames: string[]
  ): any {
    let sortedObject = src;
    if (sortingKeysInObjectByNameEnabled) {
      sortedObject = this.sortKeysInObjectByName(src);
    }

    const sortedArray = _.map(_.toPairs(sortedObject), (p) => {
      const key = p[0];
      const value = p[1];
      if (_.isArray(value)) {
        return [key, this.sortArray(value, sortingKeysInObjectByNameEnabled, sortingArrayByKeyValueEnabled, keyNames)];
      } else if (_.isObject(value)) {
        return [key, this.sortObject(value, sortingKeysInObjectByNameEnabled, sortingArrayByKeyValueEnabled, keyNames)];
      } else {
        return [key, value];
      }
    });

    sortedObject = Object.fromEntries(sortedArray);
    return sortedObject;
  }

  sortArray(
    src: any,
    sortingKeysInObjectByNameEnabled: boolean,
    sortingArrayByKeyValueEnabled: boolean,
    keyNames: string[]
  ): any {
    let sortedArray = src;
    if (sortingArrayByKeyValueEnabled) {
      sortedArray = this.sortArrayByKeyValue(src, keyNames);
    }

    sortedArray = _.map(sortedArray, (item: any) => {
      if (_.isArray(item)) {
        return this.sortArray(item, sortingKeysInObjectByNameEnabled, sortingArrayByKeyValueEnabled, keyNames);
      } else if (_.isObject(item)) {
        return this.sortObject(item, sortingKeysInObjectByNameEnabled, sortingArrayByKeyValueEnabled, keyNames);
      } else {
        return item;
      }
    });

    return sortedArray;
  }

  sort(
    src: any,
    sortingKeysInObjectByNameEnabled: boolean,
    sortingArrayByKeyValueEnabled: boolean,
    keyNames: string[]
  ): any {
    console.log("src", src);
    console.log("sortingKeysInObjectByNameEnabled", sortingKeysInObjectByNameEnabled);
    console.log("sortingArrayByKeyValueEnabled", sortingArrayByKeyValueEnabled);
    console.log("keyNames", keyNames);

    let sorted = src;
    if (_.isArray(src)) {
      sorted = this.sortArray(src, sortingKeysInObjectByNameEnabled, sortingArrayByKeyValueEnabled, keyNames);
    } else if (_.isObject(src)) {
      sorted = this.sortObject(src, sortingKeysInObjectByNameEnabled, sortingArrayByKeyValueEnabled, keyNames);
    } else {
      // console.log("else");
    }

    return sorted;
  }

  download(url: string): void {
    const a = document.createElement("a");
    a.href = url;
    a.download = url;
    document.body.appendChild(a);
    a.click();
    document.body.removeChild(a);
  }

  // eslint-disable-next-line
  async onPasteFromClipboardClicked() {
    if (navigator.clipboard) {
      const text = await navigator.clipboard.readText();
      this.srcJSON = text;
    }
  }

  onLoadFromFileClicked(event: any): void {
    let file = event.target.files[0],
      name = file.name,
      size = file.size,
      type = file.type,
      errors = "";

    //上限サイズは3MB
    if (size > 4 * 1000 * 1000) {
      errors += "ファイルサイズは4MBまでです。\n";
    }

    //拡張子は .json のみ許可
    if (type != "application/json" && type != "application/hjson") {
      errors += ".json、.hjsonファイルのみサポートしています。\n";
    }

    if (errors) {
      //errorsが存在する場合は内容をalert
      alert(errors);
      //valueを空にしてリセットする
      event.currentTarget.value = "";
    }

    const fileReader = new FileReader();

    fileReader.addEventListener("load", (e: any) => {
      // console.log(e.target.result);
      this.srcJSON = e.target.result;
    });

    fileReader.readAsText(event.target.files[0]);
  }

  onSortClicked(): void {
    console.log("this.srcJSON", this.srcJSON);
    console.log("this.sortingKeysInObjectByNameEnabled", this.sortingKeysInObjectByNameEnabled);
    console.log("this.sortingArrayByKeyValueEnabled", this.sortingArrayByKeyValueEnabled);
    console.log("this.keyNames", this.keyNames);
    console.log("this.indentType", this.indentType);

    const src = Hjson.parse(this.srcJSON);
    const keyNames = _.uniq(this.keyNames.replaceAll(" ", "").split(","));

    const sortedObject = this.sort(
      src,
      this.sortingKeysInObjectByNameEnabled,
      this.sortingArrayByKeyValueEnabled,
      keyNames
    );

    console.log(sortedObject);

    switch (this.indentType) {
      case "2spaces":
        this.distJSON = JSON.stringify(sortedObject, null, 2);
        break;
      case "4spaces":
        this.distJSON = JSON.stringify(sortedObject, null, 4);
        break;
      case "1tab":
        this.distJSON = JSON.stringify(sortedObject, null, "\t");
        break;
    }
  }

  onCopyToClipboardClicked(): void {
    if (navigator.clipboard) {
      navigator.clipboard.writeText(this.distJSON);
    }
  }

  onSaveAsFileClicked(): void {
    const MIME_TYPE = "application/json";

    const blob = new Blob([this.distJSON], { type: MIME_TYPE });
    // window.location.href = window.URL.createObjectURL(blob);
    this.download(window.URL.createObjectURL(blob));
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
