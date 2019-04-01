<template>
  <div class="home">
    <FindItem />
  </div>
</template>

<script lang="ts">
import Vue from 'vue';
import FindItem from '@/components/FindItem.vue'; // @ is an alias to /src

export default Vue.extend({
  name: 'home',
  components: {
    FindItem,
  },
});
</script>
