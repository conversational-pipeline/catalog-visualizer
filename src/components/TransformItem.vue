<template>
  <div id="transform-item">
    <form action method="get" v-on:submit.prevent="onSubmit">
      <label for="item">Item Id:</label>
      <input type="text" name="item" id="item">
      <br>
      <span class="plus">+</span>
      <br>
      <label for="attributes">Attributes:</label>
      <input type="text" name="attributes" id="attributes">
      <br>
      <br>
      <button class="button" type="submit">Transform Item</button>
    </form>
    <!-- <div>{{ jsonValue }}</div> -->
    <div class="viewer">
      <vue-json-pretty id="json-viewer" :data="jsonValue"></vue-json-pretty>
      <div id="item-viewer">
        <div class="starting-item">{{ startingItem }}</div>
        <div>👇</div>
        <ul class="items-found">
          <li v-for="result in results">
            <div class="attribute">{{ result.label }}</div>
            <div>👇</div>
            <div class="item">{{ result.item.displayName}}</div>
          </li>
        </ul>
      </div>
    </div>
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
      startingItem: null,
      jsonValue: null,
      results: null
    };
  },
  methods: {
    onSubmit(evt: any) {
      const item = evt.target.item.value;
      this.startingItem = item;
      const attributes = evt.target.attributes.value;
      return fetch(
        `http://localhost:3000/transform?item=${item}&attributes=${attributes}`
      )
        .then(res => res.json())
        .then(data => {
          this.jsonValue = data;
          this.results = data.result;
        });
    }
  }
};
</script>

<style scoped>
input {
  border-radius: 4px;
  border: 1px rgba(0, 0, 0, 0.6) solid;
  background: white;
  padding: 4px;
  margin: 10px;
}

label[for="attributes"] {
  color: #4d27d6;
  font-weight: bolder;
}

#json-viewer {
  display: inline-block;
  text-align: left;
  flex: 2;
  border: solid 1px #000;
  padding: 10px;
}

.plus {
  font-size: 50px;
}

#transform-item form button {
  font-size: 20px;
}

.viewer {
  width: 100%;
  margin-top: 20px;
  display: flex;
}

#item-viewer {
  display: inline-block;
  flex: 3;
}

.starting-item {
  text-align: center;
  background: rgb(21, 129, 66);
  color: white;
  padding: 10px;
  margin: 10px auto;
  width: 250px;
  display: block;
}

.items-found {
  padding: 0;
}

.items-found li {
  list-style: none;
  padding: 0;
}

.items-found li:not(:last-child)::after {
  content: "👇";
}

.items-found li .attribute {
  color: white;
  margin: 0 auto;
  display: block;
  width: 100px;
  height: 100px;
  border-radius: 100px;
  background: #4d27d6;
  font-size: 16px;
  line-height: 100px;
}

.items-found li .item {
  background: rgb(21, 129, 66);
  color: white;
  padding: 10px;
  margin: 10px auto;
  width: 250px;
  display: block;
}

.items-found li:last-child .item {
  font-size: 25px;
  width: 400px;
  font-weight: bolder;
  padding: 20px 30px;
  background: rgb(3, 77, 34);
  border-radius: 10px;
}
</style>
