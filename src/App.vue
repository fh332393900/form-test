<template>
  <main>
    <div class="form">
      <FormTest />
    </div>
    
  </main>
</template>

<script setup lang="ts">
import FormTest from './components/FormTest/index.vue'
</script>

<style scoped>
main {
  width: 100%;
  height: 100%;
  padding: 50px 24px;
  display: flex;
  justify-content: center;
}
.form {
  width: 50%;
  padding: 12px;
  background: rgb(238, 238, 238);
  border-radius: 10px;
}
</style>
