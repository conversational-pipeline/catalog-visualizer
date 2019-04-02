<template>
  <div id="find-item">
    <form action method="get" v-on:submit.prevent="onSubmit">
      <input type="text" name="labels" id="find-labels" required>
      <button type="submit">Get Item</button>
    </form>
    <div>
      <p v-if="isJsonNull(jsonValue)">
        Enter something...
      </p>
      <p v-else-if="isItemIdentified(jsonValue)">
        Item Identified: {{ jsonValue.itemIdentified.displayName }}
      </p>
      <p v-else>
        Requires more disambiguation with:
        <li v-for="item in remainingConcepts">{{ item }}</li>
      </p>
    </div>
    <vue-json-pretty id="json-viewer" v-bind:data="jsonValue"></vue-json-pretty>
  </div>
</template>

<script lang="ts">
const VueJsonPretty = require("vue-json-pretty");

export default {
  name: "FindItem",
  components: {
    "vue-json-pretty": VueJsonPretty.default
  },
  data() {
    return {
      jsonValue: null,
      remainingConcepts: []
    };
  },
  methods: {
    onSubmit(evt: any) {
      const labels = evt.target.labels.value;
      return fetch(`http://localhost:3000/search?labels=${labels}`)
        .then(res => res.json())
        .then(data => {
          this.jsonValue = data
          this.remainingConcepts = data.focusedLabelResults[0].result.remaining.map(r => r.conceptId)
        })
    },
    isItemIdentified(json: any): boolean {
      if (json === null) {
        return false
      }
      if (json.itemIdentified === null) {
        return false
      }
      // value exists, found unique item
      return true
    },
    isJsonNull(json: any): boolean {
      return json === null
    }
  }
};
</script>

<style scoped>
#json-viewer {
  text-align: left;
}
</style>
