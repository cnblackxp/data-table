<template>
  <div id="app" class="container">
    <h1 class='display-4'>Vue Data Table Component with Express as Backend</h1>
    <p>
      <router-link to="/users">Users Table</router-link> <br>
      <router-link to="/products">Products Table</router-link>
    </p>
    <router-view></router-view>
  </div>
</template>

<script>
export default {
  name: 'app',
}
</script>

<style>
</style>
