<template>
  <div class="game" @mousemove="position($event)">
    <div class="player"  :style="{top:posY+'px',left:posX+'px'}"></div>
  </div>
</template>

<script>
export default{
  data(){
    return{
      posX: null,
      posY: null,
    };
  },
  methods: {
    position(e){
      this.posX = e.offsetX;
      this.posY = e.offsetY;
    },
  }
}
</script>

<style scoped>
  .game{
    color: white;
    border: solid 1px white;
    height: 100%;
    width: 100%;
  }

  .game:hover{
    cursor: none;
  }

  .player{
    content: "";
    width: 15px;
    height: 15px;
    background-color: white;
    border-radius: 50%;
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
  }
</style>