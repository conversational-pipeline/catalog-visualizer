<template>
    <div id="find-item">
        <form action="" method="get" v-on:submit.prevent="onSubmit">
            <input type="text" name="labels" id="find-labels">
            <button type="submit">Get Item</button>
        </form>
        <!-- <div>{{ jsonValue }}</div> -->
        <vue-json-pretty id="json-viewer"
            :data="jsonValue">
            </vue-json-pretty>
    </div>
</template>

<script lang="ts">
const VueJsonPretty  = require('vue-json-pretty')

export default {
    name: 'FindItem',
    components: {
        'vue-json-pretty': VueJsonPretty.default
    },
    data () {
        return {
            jsonValue: null
        }
    },
    methods: {
        onSubmit(evt: any) {
            const labels = evt.target.labels.value
            return fetch(`http://localhost:3000/search?labels=${labels}`)
                .then(res => res.json())
                .then(data => {
                    this.jsonValue = data
                })
        }
    }
}
</script>

<style scoped>
#json-viewer {
    text-align: left;
}
</style>
