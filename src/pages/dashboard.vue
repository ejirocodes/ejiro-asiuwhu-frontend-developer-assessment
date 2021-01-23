<template >
  <div class="dashboard">
    <h1>Event Attendees</h1>
    <pagination v-model="page" :records="100" :per-page="x25" />
  </div>
</template>
<script>

import MyPagination from '../components/pagination.vue'

export default {
  data() {
    return {
      page: 1,
    };
  },
   options: {
        template: MyPagination    
    }   
  
};
</script>
<style>
</style>