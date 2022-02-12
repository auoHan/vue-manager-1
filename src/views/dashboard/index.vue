<template>
  <div>
    <Card></Card>
    <Sale></Sale>
    <Observe></Observe>
  </div>
</template>

<script>

import Card from '@/views/dashboard/Card'
import Sale from '@/views/dashboard/Sale'
import Observe from '@/views/dashboard/Observe'
export default {
  name: 'Dashboard',
  components: { Observe, Sale, Card }
}
</script>

<style lang="scss" scoped>
.dashboard {
  &-container {
    margin: 30px;
  }
  &-text {
    font-size: 30px;
    line-height: 46px;
  }
}
</style>
