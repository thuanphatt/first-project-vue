<template>
  <div id="app" style="margin-bottom: 40px">
    <section class="row">
      <div class="small-6 columns">
        <h1 class="text-center">YOU</h1>
        <img src="./assets/img/chibi.png" alt="" />

        <div class="healthbar">
          <div
            class="healthbar text-center"
            style="
               {
                background-color: red;
                color: white;
                margin: 0;
                background-color: red;
              }
            "
            :style="{ width: playerHealth + '%' }"
          >
            {{ playerHealth }}
          </div>
        </div>
      </div>
      <div class="small-6 columns">
        <h1 class="text-center">MONSTER</h1>
        <img src="./assets/img/monster.png" alt="" />

        <div class="healthbar">
          <div
            class="healthbar text-center"
            style="
               {
                 {
                  background-color: red;
                  color: white;
                  margin: 0;
                  background-color: red;
                }
              }
            "
            :style="{ width: mosterHealth + '%' }"
          >
            {{ mosterHealth }}
          </div>
        </div>
      </div>
    </section>
    <section class="row controls" v-if="!gameIsRunning">
      <div class="small-12 columns">
        <button id="start-game" @click="onStartGame">START NEW GAME</button>
      </div>
    </section>
    <section class="row controls" v-else>
      <div class="small-12 columns">
        <button id="attack" @click="attack">ATTACK</button>
        <button id="special-attack" @click="specialAttack">
          SPECIAL ATTACK
        </button>
        <button id="heal" @click="heal">HEAL</button>
        <button id="give-up" @click="giveUp">GIVE UP</button>
      </div>
    </section>
    <section class="row log" v-if="turns.length > 0">
      <div class="small-12 columns">
        <ul>
          <li
            v-for="(turn, index) in turns"
            :key="index"
            :class="{
              'player-turn': turn.isPlayer,
              'monster-turn': !turn.isPlayer,
            }"
          >
            {{ turn.textLog }}
          </li>
        </ul>
      </div>
    </section>
  </div>
</template>

<script>
export default {
  data() {
    return {
      playerHealth: 100,
      mosterHealth: 100,
      gameIsRunning: false,
      turns: [],
    };
  },
  methods: {
    onStartGame() {
      this.gameIsRunning = true;
      this.playerHealth = 100;
      this.mosterHealth = 100;
      this.turns = [];
    },
    attack() {
      // Check options
      if (this.checkPlayerOption()) {
        return;
      }
      // Moster
      var damage = this.inputDamage(4, 10);
      this.mosterHealth -= damage;
      this.turns.unshift({
        isPlayer: true,
        textLog: `Player hits Moster for ${damage}`,
      });
      // Player
      this.mosterAttacks();
      this.checkPlayerOption();
    },
    specialAttack() {
      // Check options
      if (this.checkPlayerOption()) {
        return;
      }
      // Moster
      var damage = this.inputDamage(10, 20);
      this.mosterHealth -= damage;
      this.turns.unshift({
        isPlayer: true,
        textLog: `Player hits Moster for ${damage}`,
      });
      // Player
      this.mosterAttacks();
    },
    heal() {
      // Player
      if (this.playerHealth > 70) {
        return false;
      } else if (this.playerHealth <= 60) {
        this.playerHealth += 10;
      } else {
        this.playerHealth = 70;
      }
      this.turns.unshift({
        isPlayer: true,
        textLog: "Player heal for 10",
      });
      // Moster
      this.mosterAttacks();
    },
    giveUp() {
      this.gameIsRunning = false;
      this.turns = [];
      this.playerHealth = 100;
      this.mosterHealth = 100;
      alert("You lost");
    },
    mosterAttacks() {
      var damage = this.inputDamage(5, 12);
      this.playerHealth -= damage;
      this.turns.unshift({
        isPlayer: false,
        textLog: `Moster hits player for ${damage}`,
      });
      this.checkPlayerOption();
    },
    inputDamage(minDamage, maxDamage) {
      return Math.max(Math.floor(Math.random() * maxDamage) + 1, minDamage);
    },
    checkPlayerOption() {
      if (this.mosterHealth <= 0) {
        if (confirm("You won! New game?")) {
          this.onStartGame();
        } else {
          this.gameIsRunning = false;
        }
        return true;
      } else if (this.playerHealth <= 0) {
        if (confirm("You lost! New game?")) {
          this.onStartGame();
        } else {
          this.gameIsRunning = false;
        }
        return true;
      }
      return;
    },
  },
};
</script>

<style>
img {
  height: 500px;
  width: 100%;
}
</style>
