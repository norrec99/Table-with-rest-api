<template>
  <Suspense>
    <BaseTable />
  </Suspense>
</template>

<script>
import BaseTable from '@/components/BaseTable.vue';

export default {
  name: 'App',
  components: {
    BaseTable
  }
};
</script>

<style></style>
