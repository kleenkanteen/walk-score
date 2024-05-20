<template>
    <div class="heading flex items-center justify-center">
      <h1 class="text-xl font-bold">This is my map App</h1>
      <button class="btn btn-accent">Button</button>
    </div>
  </template>
  
  <script>
  export default {
    name: 'Navbar'
  }
  </script>
  
  <style scoped>
  .heading {
    margin: 0;
    padding: 0px;
    background-color: black;
    color: white;
  }
  
  .heading > h1 {
    padding: 20px;
    margin: 0;
  }
  </style>