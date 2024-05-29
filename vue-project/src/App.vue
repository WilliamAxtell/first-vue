<script setup>
  import MyComponent from './components/Todo.vue';

</script>

<template>
  <MyComponent>
    <template v-slot:holiday>
      <header>
          <h1>Who's Out Today?</h1>
      </header>
    </template>
    <template v-slot:home>
      <header class="second-header">
          <h1>Who's Working From Home?</h1>
      </header>
    </template>
  </MyComponent>
</template>

<style scoped>
  header {
      background-color: #009CA6;
      color: white;
      padding: 0.5rem 1rem;
      text-align: center;
      margin: 1rem;
      border: 0.2rem solid black;
      border-radius: 1rem;
  }

  h1 {
      font-size: 32px;
      margin: 0;
  }

  .second-header {
      margin-top: 3rem;
  }
</style>