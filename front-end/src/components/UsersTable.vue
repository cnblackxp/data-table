<template>
  <div>
    <h4>Users Table</h4>
    <Table tableName="users" class="usersTable"></Table>
  </div>
</template>

<script>
import Table from './Table.vue';
export default { 
  components: {
    Table
  }
}
</script>

<style>
  .usersTable table thead th {
    background-color: rgba(110, 187, 114, 0.308) !important;
  }
</style>
