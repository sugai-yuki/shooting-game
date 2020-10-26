<template>
  <div class="start">
    <router-link :to="{name:'Game'}"><button class="start-btn">GAME START</button></router-link>
  </div>
</template>

<style scoped>
  .start-btn{
    background-color: #00b7c4;
    color: white;
    font-size: 22px;
    padding: 10px 20px;
    border-radius: 20px;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    border-style: none;
  }

  .start-btn:focus{
    outline: none;
  }
</style>