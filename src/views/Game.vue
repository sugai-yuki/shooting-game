<template>
  <div class="game" ref="game" @mousemove="getPlayerNewPos($event)">
    <div class="player"  :style="{top:playerPosY+'px',left:playerPosX+'px'}" @mousemove.stop @click="newPlayerBullet()"></div>
    <div class="player-bullets bullets" v-for="(bullet, index) in playerBullets" :key="'bullet'+index" :style="{top:bullet.bulletPosY+'px',left:bullet.bulletPosX+'px'}" @mousemove.stop>{{bullet.cont}}</div>
    <div class="enemy" v-for="(enemy, index) in enemies" :key="'enemy'+index" :style="{top:enemy.enemyPosY+'px',left:enemy.enemyPosX+'px',background:enemy.color}" @mousemove.stop>{{enemies.cont}}</div>
    <div class="enemies-bullets bullets" v-for="(enemyBullet, index) in enemiesBullets" :key="'enemyBullet'+index" :style="{top:enemyBullet.enemyBulletPosY+'px',left:enemyBullet.enemyBulletPosX+'px'}" :class="enemyBullet.tilt" @mousemove.stop>{{enemyBullet.cont}}</div>
    <div class="score">SCORE:{{score}}</div>
  </div>
</template>

<script>
export default{
  data(){
    return{
      playerPosX: null,
      playerPosY: null,
      playerBullets: [],
      enemies: [],
      enemiesBullets: [],
      score: 0,
      levelCount: 0, 
      handleBulletsSet: null,
      newEnemyTypeAXSet: null,
      changeEnemyTypeAXtoBSet: null,
      newEnemyTypeAYSet: null,
      newEnemyTypeBSet: null,
      newEnemyTypeCSet: null,
      handleEnemiesSet: null,
      newEnemiesBulletSet: null,
    };
  },
  methods: {
    getPlayerNewPos(e){
      this.playerPosX = e.offsetX;
      this.playerPosY = e.offsetY;
    },
    playerInitialPos(){
      var game = this.$refs.game;
      var gameRect = game.getBoundingClientRect();
      //初期位置にいても当たり判定をつけるため
      this.playerPosX = gameRect.width / 2;
      this.playerPosY = gameRect.height / 2;
    },
    newPlayerBullet(){
      const newPlayerBulletData = {
        cont: "",
        speed: -5,
        bulletPosY: null,
        bulletPosX: null,
      };
      newPlayerBulletData.bulletPosY = this.playerPosY;
      newPlayerBulletData.bulletPosX = this.playerPosX;
      this.playerBullets.push(newPlayerBulletData);
    },
    getPlayerBulletsNewPos(){
      this.playerBullets.forEach((bullet, index) => {
        bullet.bulletPosY += bullet.speed;
        this.playerBulletGoOffScreen(bullet, index);
      });
    },
    playerBulletGoOffScreen(bullet, index){
        if(bullet.bulletPosY < 0){
          this.playerBullets.splice(index,1);
        }
    },
    getEnemiesBulletsNewPos(){
      this.enemiesBullets.forEach((bullet, index) => {
        if(bullet.direction === 'straight'){
          bullet.enemyBulletPosY += bullet.speed;
        }
        if(bullet.direction === 'rightDiagonal'){
          bullet.enemyBulletPosY += bullet.speed;
          bullet.enemyBulletPosX += bullet.speed;
        }
        if(bullet.direction === 'leftDiagonal'){
          bullet.enemyBulletPosY += bullet.speed;
          bullet.enemyBulletPosX -= bullet.speed;
        }
        if(bullet.direction === 'right'){
          bullet.enemyBulletPosX += bullet.speed;
        }
        if(bullet.direction === 'left'){
          bullet.enemyBulletPosX -= bullet.speed;
        }
        this.enemiesBullletGoOffScreen(bullet, index);
        this.hitPlayer(bullet);
      });
    },
    enemiesBullletGoOffScreen(bullet, index){
      var game = this.$refs.game;
      var gameRect = game.getBoundingClientRect();
      var rightBorder = gameRect.left / 4 + gameRect.width;
      var leftBorder = -20;
      var lowerBorder = gameRect.height + 20;
      if(bullet.enemyBulletPosY > lowerBorder || bullet.enemyBulletPosX < leftBorder || bullet.enemyBulletPosX > rightBorder){
          this.enemiesBullets.splice(index,1);
        }
    },
    hitPlayer(bullet){
      if(Math.abs(bullet.enemyBulletPosY - this.playerPosY) <= 12 && Math.abs(bullet.enemyBulletPosX - this.playerPosX) <= 9.5){
          this.$store.commit('scoreResult', this.score);
          this.$router.push({name: "Result"});
        }
    },
    handleBullets(){
      this.getPlayerBulletsNewPos();
      this.getEnemiesBulletsNewPos();
    },
    newEnemyTypeAX(){
      if(this.levelCount == 0 && this.score >= 3000){
          this.changeEnemyTypeAXtoB();
          return 0;
        }
      this.newEnemyTypeAXR();
      this.newEnemyTypeAXL();
    },
    changeEnemyTypeAXtoB(){
      clearInterval(this.newEnemyTypeAXSet);
      setTimeout(this.newEnemyTypeB, 0)
      this.changeEnemyTypeAXtoBSet = setInterval(this.newEnemyTypeB, 5000);
      this.levelCount += 1;
    },
    newEnemyTypeAY(){
      if(this.score >= 500){
        this.newEnemyTypeAYD();
        this.newEnemyTypeAYD();
      }
      if(this.score >= 700){
        this.newEnemyTypeAYD();
      }
    },
    newEnemyTypeAXR(){
      const newEnemyTypeAXR = {
        cont: "",
        type: 'A',
        move: 'XR',
        color: 'green',
        speed: 2,
        enemyPosY: Math.floor(Math.random() * 190) + 10,
        enemyPosX: -20,
        score: 100,
      }
      this.enemies.push(newEnemyTypeAXR);
    },
    newEnemyTypeAXL(){
      const newEnemyTypeAXL = {
        cont: "",
        type: 'A',
        move: 'XL',
        color: 'green',
        speed: 2,
        enemyPosY: Math.floor(Math.random() * 190) + 10,
        enemyPosX: null,
        score: 100,
      }
      var game = this.$refs.game;
      var gameRect = game.getBoundingClientRect();
      newEnemyTypeAXL.enemyPosX = gameRect.width + 20;
      this.enemies.push(newEnemyTypeAXL);
    },
    newEnemyTypeAYD(){
      const newEnemyTypeAYD = {
        cont: "",
        type: 'A',
        move: 'YD',
        color: 'green',
        speed: 2,
        enemyPosY: -20,
        enemyPosX: null,
        score: 100,
      }
      var game = this.$refs.game;
      var gameRect = game.getBoundingClientRect();
      newEnemyTypeAYD.enemyPosX = Math.floor(Math.random() * gameRect.width);
      this.enemies.push(newEnemyTypeAYD);
    },
    newEnemyTypeB(){
      if(this.score >= 1000){
        this.newEnemyTypeBXR();
        this.newEnemyTypeBXL();
      }
    },
    newEnemyTypeBXR(){
      const newEnemyTypeBXR = {
        cont: "",
        type: 'B',
        move: 'XR',
        color: 'yellow',
        speed: 2,
        enemyPosY: Math.floor(Math.random() * 90) + 10,
        enemyPosX: -20,
        score: 300,
      }
      this.enemies.push(newEnemyTypeBXR);
    },
    newEnemyTypeBXL(){
      const newEnemyTypeBXL = {
        cont: "",
        type: 'B',
        move: 'XL',
        color: 'yellow',
        speed: 2,
        enemyPosY: Math.floor(Math.random() * 90) + 10,
        enemyPosX: null,
        score: 300,
      }
      var game = this.$refs.game;
      var gameRect = game.getBoundingClientRect();
      newEnemyTypeBXL.enemyPosX = gameRect.width + 20;
      this.enemies.push(newEnemyTypeBXL);
    },
    newEnemyTypeC(){
      if(this.score >= 2000){
        this.newEnemyTypeCXR();
        this.newEnemyTypeCXL();
      }
    },
    newEnemyTypeCXR(){
      const newEnemyTypeBXR = {
        cont: "",
        type: 'C',
        move: 'XR',
        color: 'red',
        speed: 3,
        enemyPosY: Math.floor(Math.random() * 190) + 10,
        enemyPosX: -20,
        score: 500,
      }
      this.enemies.push(newEnemyTypeBXR);
    },
    newEnemyTypeCXL(){
      const newEnemyTypeBXL = {
        cont: "",
        type: 'C',
        move: 'XL',
        color: 'red',
        speed: 3,
        enemyPosY: Math.floor(Math.random() * 190) + 10,
        enemyPosX: null,
        score: 500,
      }
      var game = this.$refs.game;
      var gameRect = game.getBoundingClientRect();
      newEnemyTypeBXL.enemyPosX = gameRect.width + 20;
      this.enemies.push(newEnemyTypeBXL);
    },
    getEnemyNewPos(enemy){
      if(enemy.move === 'XR'){
        enemy.enemyPosX += enemy.speed;
      }
      if(enemy.move === 'XL'){
        enemy.enemyPosX -= enemy.speed;
      }
      if(enemy.move === 'YD'){
        enemy.enemyPosY += enemy.speed;
      }
    },
    goOffScreenEnemy(enemy, index){
      var game = this.$refs.game;
      var gameRect = game.getBoundingClientRect();
      var rightBorder = gameRect.left / 4 + gameRect.width;
      var leftBorder = -20;
      var lowerBorder = gameRect.height + 20;
      if(enemy.enemyPosX > rightBorder || enemy.enemyPosX < leftBorder || enemy.enemyPosY > lowerBorder){
        this.enemies.splice(index,1);
      }
    },
    hitEnemy(enemy, index){
      this.playerBullets.forEach((bullet, indexB) => {
          if(Math.abs(enemy.enemyPosY - bullet.bulletPosY) <= 15 && Math.abs(enemy.enemyPosX - bullet.bulletPosX) <= 12.5){
            this.enemies.splice(index,1);
            this.playerBullets.splice(indexB,1);
            this.score += enemy.score;
          }
        });
    },
    enemyHitPlayer(enemy){
      if(Math.abs(enemy.enemyPosY - this.playerPosY) <= 17 && Math.abs(enemy.enemyPosX - this.playerPosX) <= 17){
          this.$store.commit('scoreResult', this.score);
          this.$router.push({name: "Result"});
        }
    },
    handleEnemies(){
      this.enemies.forEach((enemy, index) => {
        this.getEnemyNewPos(enemy);
        this.goOffScreenEnemy(enemy, index);
        this.hitEnemy(enemy, index);
        this.enemyHitPlayer(enemy);
      });
    },
    newEnemiesBullet(){
      this.enemies.forEach((enemy) =>{
        this.newEnemiesStraightBullet(enemy);
        if(enemy.type === 'B' || enemy.type === 'C'){
          this.newEnemiesRightDiagonalBullet(enemy);
          this.newEnemiesLeftDiagonalBullet(enemy);
        }
        if(enemy.type === 'C'){
          this.newEnemiesRightBullet(enemy);
          this.newEnemiesLeftBullet(enemy);
        }
      });
    },
    newEnemiesStraightBullet(enemy){
      const newEnemiesBulletData = {
          cont: "",
          speed: 4,
          enemyBulletPosY: null,
          enemyBulletPosX: null,
          direction: 'straight',
        }
        if(enemy.move === 'YD'){
          newEnemiesBulletData.speed = 6;
        }
        this.enemiesBulletInitialPos(enemy, newEnemiesBulletData);
        this.enemiesBullets.push(newEnemiesBulletData);
    },
    newEnemiesRightDiagonalBullet(enemy){
      const newEnemiesBulletData = {
          cont: "",
          speed: 2 * Math.sqrt(2),
          enemyBulletPosY: null,
          enemyBulletPosX: null,
          direction: 'rightDiagonal',
          tilt: 'right_diagonal_bullet',
        }
        this.enemiesBulletInitialPos(enemy, newEnemiesBulletData);
        this.enemiesBullets.push(newEnemiesBulletData);
    },
    newEnemiesLeftDiagonalBullet(enemy){
      const newEnemiesBulletData = {
          cont: "",
          speed: 2 * Math.sqrt(2),
          enemyBulletPosY: null,
          enemyBulletPosX: null,
          direction: 'leftDiagonal',
          tilt: 'left_diagonal_bullet',
        }
        this.enemiesBulletInitialPos(enemy, newEnemiesBulletData);
        this.enemiesBullets.push(newEnemiesBulletData);
    },
    newEnemiesRightBullet(enemy){
      const newEnemiesBulletData = {
          cont: "",
          speed: 6,
          enemyBulletPosY: null,
          enemyBulletPosX: null,
          direction: 'right',
          tilt: 'right_bullet'
        }
        this.enemiesBulletInitialPos(enemy, newEnemiesBulletData);
        this.enemiesBullets.push(newEnemiesBulletData);
    },
    newEnemiesLeftBullet(enemy){
      const newEnemiesBulletData = {
          cont: "",
          speed: 6,
          enemyBulletPosY: null,
          enemyBulletPosX: null,
          direction: 'left',
          tilt: 'left_bullet'
        }
        this.enemiesBulletInitialPos(enemy, newEnemiesBulletData);
        this.enemiesBullets.push(newEnemiesBulletData);
    },
    enemiesBulletInitialPos(enemy, BulletData){
      if(enemy.move === 'XR'){
        BulletData.enemyBulletPosY = enemy.enemyPosY + 10;
        // 右側に動いているenemyが弾を真ん中から撃っているように見せるため
        BulletData.enemyBulletPosX = enemy.enemyPosX + 4;
        if(enemy.speed == 3){
          BulletData.enemyBulletPosX += 8;
        } 
      }
      if(enemy.move === 'XL'){
        BulletData.enemyBulletPosY = enemy.enemyPosY + 10;
        // 左側に動いているenemyが弾を真ん中から撃っているように見せるため
        BulletData.enemyBulletPosX = enemy.enemyPosX - 4;
        if(enemy.speed == 3){
          BulletData.enemyBulletPosX -= 8;
        } 
      }
      if(enemy.move === 'YD'){
        BulletData.enemyBulletPosY = enemy.enemyPosY + 10;
        BulletData.enemyBulletPosX = enemy.enemyPosX; 
      }
    },
  },
  mounted() {
    this.playerInitialPos();
    this.handleBulletsSet = setInterval(this.handleBullets, 34);
    // 最初の一体だけ早めに出現させるため
    setTimeout(this.newEnemyTypeAX, 500);
    this.newEnemyTypeAXSet = setInterval(this.newEnemyTypeAX, 5000);
    this.newEnemyTypeAYSet = setInterval(this.newEnemyTypeAY, 5000);
    this.newEnemyTypeBSet = setInterval(this.newEnemyTypeB, 8000);
    this.newEnemyTypeCSet = setInterval(this.newEnemyTypeC, 10000);
    this.handleEnemiesSet = setInterval(this.handleEnemies, 34);
    this.newEnemiesBulletSet = setInterval(this.newEnemiesBullet, 700);
  },
  destroyed() {
    clearInterval(this.handleBulletsSet);
    clearInterval(this.newEnemyTypeAXSet);
    clearInterval(this.changeEnemyTypeAXtoBSet);
    clearInterval(this.newEnemyTypeAYSet);
    clearInterval(this.newEnemyTypeBSet);
    clearInterval(this.newEnemyTypeCSet);
    clearInterval(this.handleEnemiesSet);
    clearInterval(this.newEnemiesBulletSet);
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
    transform: translate(-50%, -50%);
  }

  .player-bullets{
    background-color: white;
  }

  .enemy{
    width: 20px;
    height: 20px;
    border-radius: 50%;
    position: absolute;
    transform: translate(-50%, -50%);
  }

  .enemies-bullets{
    background-color: #f8df70;
  }

  .right_diagonal_bullet{
    transform: translate(-50%, -50%) rotate(-45deg);
  }

  .left_diagonal_bullet{
    transform: translate(-50%, -50%) rotate(45deg);
  }

  .right_bullet{
    transform: translate(-50%, -50%) rotate(-90deg);
  }

  .left_bullet{
    transform: translate(-50%, -50%) rotate(90deg);
  }
</style>