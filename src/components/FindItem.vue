<template>
  <div id="find-item">
    <select name="" id="">
      <option v-for="realm in realms" :key="realm" >
        {{ realm }}
      </option>
    </select>
    <form action method="get" v-on:submit.prevent="onSubmit">
      <input type="text" name="labels" id="find-labels" required>
      <button type="submit">Get Item</button>
    </form>
    <br />
    <div>
      <p v-if="isJsonNull(jsonValue)">Enter something...</p>
      <div class="identified" v-else-if="isItemIdentified(jsonValue)">
        <router-link :to="transformUrl">{{ jsonValue.itemIdentified.displayName }}</router-link>
      </div>
      <ul class="more-info" v-else>
        Requires more disambiguation with:
        <li v-for="(item) in remainingConcepts" :key="item">{{ item }}</li>
      </ul>
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
  beforeMount() {
    this.fetchRealms()
      .then(result => this.realms = result)
  },

  data() {
    return {
      realms: [],
      jsonValue: null,
      remainingConcepts: [],
      transformUrl: "/transform"
    };
  },
  methods: {
    jsonValue: null,
    remainingConcepts: [],
    fetchRealms() {
      return fetch('http://localhost:3000/health')
        .then(res => res.json())
        .then(result => result.realms)
    },
    transformUrl: "/transform",
    onSubmit(evt: any) {
      const labels = evt.target.labels.value;
      return fetch(`http://localhost:3000/search?labels=${labels}`)
        .then(res => res.json())
        .then(data => {
          this.jsonValue = data;
          if (data.itemIdentified) {
            this.transformUrl = `/transform?id=${data.itemIdentified.id}`;
          }
          this.remainingConcepts = data.focusedLabelResults[0].result.remaining.map(
            (r: any) => {
              const values: string[] = r.associations.map(
                (_: any) => _.conceptValue
              );
              const setArr = Array.from(new Set(values));
              return `${r.conceptId}: (${setArr.join(", ")})`;
            }
          );
        });
    },
    isItemIdentified(json: any): boolean {
      if (json === null) {
        return false;
      }
      if (json.itemIdentified === null) {
        return false;
      }
      // value exists, found unique item
      return true;
    },
    isJsonNull(json: any): boolean {
      return json === null;
    }
  }
};
</script>

<style scoped>
#json-viewer {
  text-align: left;
}

.identified {
  font-size: 25px;
  width: 400px;
  font-weight: bolder;
  padding: 20px 30px;
  background: rgb(3, 77, 34);
  border-radius: 10px;
  margin: 0 auto;
}

.identified router-link {
  color: white;
}
</style>
