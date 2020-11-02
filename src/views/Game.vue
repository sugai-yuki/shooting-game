<template>
  <div class="game" ref="game" @mousemove="myMove($event)">
    <div class="player"  :style="{top:myPosY+'px',left:myPosX+'px'}" @mousemove.stop @click="newMyBullet()"></div>
    <div class="bullets" v-for="(bullet, index) in selfBullets" :key="'bullet'+index" :style="{top:bullet.bulletPosY+'px',left:bullet.bulletPosX+'px'}" @mousemove.stop>{{bullet.cont}}</div>
    <div class="enemy" v-for="(enemy, index) in enemies" :key="'enemy'+index" :style="{top:enemy.enemyPosY+'px',left:enemy.enemyPosX+'px'}" @mousemove.stop>{{enemies.cont}}</div>
    <div class="enemies-bullets bullets" v-for="(enemyBullet, index) in enemyBullets" :key="'enemyBullet'+index" :style="{top:enemyBullet.enemyBulletPosY+'px',left:enemyBullet.enemyBulletPosX+'px'}" @mousemove.stop>{{enemyBullet.cont}}</div>
    <div class="score">SCORE:{{score}}</div>
  </div>
</template>

<script>
export default{
  data(){
    return{
      myPosX: null,
      myPosY: null,
      selfBullets: [],
      enemies: [],
      enemyBullets: [],
      score: 0,
      bulletMoveSet: null,
      enemyAppearSet: null,
      enemyMoveSet: null,
      enemyBulletSet: null,
    };
  },
  methods: {
    myMove(e){
      this.myPosX = e.offsetX;
      this.myPosY = e.offsetY;
      this.enemyBullets.forEach((enemyBullet) => {
        if(Math.abs(enemyBullet.enemyBulletPosY - this.myPosY) <= 12.5 && Math.abs(enemyBullet.enemyBulletPosX - this.myPosX) <= 20){
          this.$router.push({name: "Result"});
        }
      });
    },
    newMyBullet(){
      const newMyBulletData = {
        cont: "",
        speed: -5,
        bulletPosY: null,
        bulletPosX: null,
      };
      newMyBulletData.bulletPosY = this.myPosY;
      newMyBulletData.bulletPosX = this.myPosX;
      this.selfBullets.push(newMyBulletData);
    },
    bulletMove(){
      this.selfBullets.forEach((bullets, index) => {
        bullets.bulletPosY += bullets.speed;
        if(bullets.bulletPosY < 0){
          this.selfBullets.splice(index,1);
        }
      });
      this.enemyBullets.forEach((bullets, index) => {
        bullets.enemyBulletPosY += bullets.speed;
        if(bullets.enemyBulletPosY > 420){
          this.enemyBullets.splice(index,1);
        }
        if(Math.abs(bullets.enemyBulletPosY - this.myPosY) <= 12.5 && Math.abs(bullets.enemyBulletPosX - this.myPosX) <= 10){
          this.$store.commit('scoreResult', this.score);
          this.$router.push({name: "Result"});
        }
      });
    },
    enemyAppear(){
      const newEnemy = {
        cont: "",
        speed: 2,
        enemyPosY: Math.floor(Math.random() * 190) + 10,
        enemyPosX: -20,
        score: 100,
      }
      this.enemies.push(newEnemy);
    },
    enemyMove(){
      this.enemies.forEach((enemy, index) => {
        enemy.enemyPosX += enemy.speed;
        var game = this.$refs.game;
        var gameRect = game.getBoundingClientRect();
        var borderRight = gameRect.left / 4 + gameRect.width;
        if(enemy.enemyPosX > borderRight){
          this.enemies.splice(index,1);
        }
        this.selfBullets.forEach((bullets, indexB) => {
          if(Math.abs(enemy.enemyPosY - bullets.bulletPosY) <= 15 && Math.abs(enemy.enemyPosX - bullets.bulletPosX) <= 12.5){
            this.enemies.splice(index,1);
            this.selfBullets.splice(indexB,1);
            this.score += enemy.score;
          }
        });
      });
    },
    enemyBullet(){
      this.enemies.forEach((enemy) =>{
        const newEnemyBulletData = {
          cont: "",
          speed: 4,
          enemyBulletPosY: null,
          enemyBulletPosX: null,
        }
        newEnemyBulletData.enemyBulletPosY = enemy.enemyPosY;
        newEnemyBulletData.enemyBulletPosX = enemy.enemyPosX;
        this.enemyBullets.push(newEnemyBulletData);
      });
    },
  },
  mounted() {
    var game = this.$refs.game;
    var gameRect = game.getBoundingClientRect();
    //初期位置にいても当たり判定をつけるため
    this.myPosX = gameRect.width / 2;
    this.myPosY = gameRect.height / 2;
    this.bulletMoveSet = setInterval(this.bulletMove, 34);
    // 最初の一体だけ早めに出現させるため
    setTimeout(this.enemyAppear, 1000);
    this.enemyAppearSet = setInterval(this.enemyAppear, 3000);
    this.enemyMoveSet = setInterval(this.enemyMove, 34);
    this.enemyBulletSet = setInterval(this.enemyBullet, 500);
  },
  destroyed() {
    clearInterval(this.bulletMoveSet);
    clearInterval(this.enemyAppearSet);
    clearInterval(this.enemyMoveSet);
    clearInterval(this.enemyBulletSet);
  },
};
</script>

<style scoped>
  .game{
    color: white;
    height: 100%;
    width: 100%;
  }

  .game:hover{
    cursor: none;
  }

  .player{
    width: 15px;
    height: 15px;
    background-color: white;
    border-radius: 50%;
    position: absolute;
    transform: translate(-50%, -50%);
  }

  .bullets{
    position: absolute;
    width: 5px;
    height: 10px;
    background-color: white;
    transform: translate(-50%, -50%);
  }

  .enemy{
    width: 20px;
    height: 20px;
    background-color: red;
    border-radius: 50%;
    position: absolute;
    transform: translate(-50%, -50%);
  }

  .enemies-bullets{
    background-color: #f8df70;
  }
</style>