<template>
  <div>
    <h4>Products Table</h4>
    <Table tableName="products" class="productsTable"></Table>
  </div>
</template>

<script>
import Table from './Table'
export default { 
  components: {
    Table
  }
}
</script>

<style>
  .productsTable table thead th {
    background-color: rgba(187, 110, 110, 0.308) !important;
  }
</style>
