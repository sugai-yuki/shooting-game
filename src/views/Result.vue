<template>
  <div class="result">
    <p class="score">SCORE: {{$store.state.score}}</p>
    <button class="restart-btn" @click="gameRestart">RESTART</button>
  </div>
</template>

<script>
export default {
  methods: {
    gameRestart(){
      this.$router.push({name: "Game"});
    }
  }
};
</script>

<style scoped>
  .score{
    color: white;
    position: absolute;
    font-size: 36px;
    font-weight: bold;
    top: 40%;
    left: 50%;
    transform: translate(-50%, -50%);
  }

  .restart-btn{
    background-color: #00b7c4;
    color: white;
    font-size: 22px;
    padding: 10px 20px;
    border-radius: 20px;
    position: absolute;
    top: 70%;
    left: 50%;
    transform: translate(-50%, -50%);
    border-style: none;
  }

  .restart-btn:focus{
    outline: none;
  }
</style>