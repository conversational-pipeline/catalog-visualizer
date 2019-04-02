<template>
  <div class="transform">
    <TransformItem />
  </div>
</template>

<script lang="ts">
import Vue from 'vue';
import TransformItem from '@/components/TransformItem.vue'; // @ is an alias to /src

export default Vue.extend({
  name: 'home',
  components: {
    TransformItem,
  },
});
</script>
