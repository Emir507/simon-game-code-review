<template>
  <div class="box" :class="{ animated: wrongClick }">
    <div
      class="box__block block-1"
      @click="playAudio(0)"
      :class="{
        'green-active': activeClasses[0],
        'block-1--active': !btnsLock,
      }"
    ></div>
    <div
      class="box__block block-2"
      @click="playAudio(1)"
      :class="{ 'cyan-active': activeClasses[1], 'block-2--active': !btnsLock }"
    ></div>
    <div
      class="box__block block-3"
      @click="playAudio(2)"
      :class="{ '$red-active': activeClasses[2], 'block-3--active': !btnsLock }"
    ></div>
    <div
      class="box__block block-4"
      @click="playAudio(3)"
      :class="{ 'blue-active': activeClasses[3], 'block-4--active': !btnsLock }"
    ></div>
    <div class="box__control-panel">
      <div class="control-panel__difficulty">
        <div>
          <div
            class="difficulty__btn"
            @click="gameLevel('easy')"
            :class="{ 'difficulty__btn-active': gameDifficulty.easy }"
          ></div>
          <div class="text">easy</div>
        </div>
        <div>
          <div
            class="difficulty__btn"
            @click="gameLevel('normal')"
            :class="{ 'difficulty__btn-active': gameDifficulty.normal }"
          ></div>
          <div class="text">normal</div>
        </div>
        <div>
          <div
            class="difficulty__btn"
            @click="gameLevel('hard')"
            :class="{ 'difficulty__btn-active': gameDifficulty.hard }"
          ></div>
          <div class="text">hard</div>
        </div>
      </div>
      <div class="control-panel__tools">
        <div>
          <div class="tools__count">{{ count }}</div>
          <div>count</div>
        </div>
        <div>
          <div class="tools__btn-start" @click="start"></div>
          <div>start</div>
        </div>
      </div>
      <div class="switch">
        off
        <div
          v-on:click="switchOn"
          class="switch-off"
          :class="{ 'switch-on': switcher }"
        >
          <div class="switcher"></div>
        </div>
        on
      </div>
    </div>
    <div v-if="modal" class="modal">
      <h1>Game Over</h1>
      <button @click="modal = false">OK</button>
    </div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      count: "",
      switcher: false,
      activeClasses: {
        0: false,
        1: false,
        2: false,
        3: false,
      },
      btnsLock: true,
      steps: [],
      playerSteps: [],
      sounds: [
        new Audio("./sounds/1.mp3"),
        new Audio("./sounds/2.mp3"),
        new Audio("./sounds/3.mp3"),
        new Audio("./sounds/4.mp3"),
      ],
      gameDifficulty: {
				easy: false,
				normal: false,
				hard: false,
			},
      gameSpeed: 1500,
      levelChange: false,
      errorSound: new Audio("./sounds/error.mp3"),
      wrongClick: false,
      modal: false,
    };
  },
  methods: {
    // switcher
    switchOn() {
			this.switcher = !this.switcher;
			switch (this.switcher) {
				case true:
					this.count = "- -";
					this.gameDifficulty.easy = true;
					this.gameSpeed = 1500;
					this.levelChange = true;
					this.btnsLock = true;
					break;
				case false: 
					this.count = "";
					this.steps = [];
					this.playerSteps = [];
					this.gameDifficulty.easy = false;
					this.gameDifficulty.normal = false;
					this.gameDifficulty.hard = false;
					break;
			}
    },
    // start button
    start() {
      if (this.switcher) {
        this.levelChange = false;
        this.count = "- -";
        this.steps = [];
        this.playerSteps = [];
        this.wrongClick = false;
        setTimeout(() => {
          this.addSteps();
          this.computerMoves();
        }, 500);
      } else {
        return;
      }
    },
    // player click the buttons
    playAudio(index) {
      if (this.btnsLock || !this.switcher) {
        return;
			}
      this.sounds[index].play();
      this.playerSteps.push(index);
      for (let i = 0; i < this.playerSteps.length; i++) {
        // if clicked wrong
        if (this.playerSteps[i] !== this.steps[i]) {
          this.wrongClick = true;
          this.errorSound.play();
          this.count = "- -";
          this.btnsLock = true;
          setTimeout(() => {
            this.modal = true;
            this.switchOn();
          }, 500);
          setTimeout(() => {
            this.start();
          }, 1000);
          return;
        }
      }
      // if clicked right
      if (this.steps.length === this.playerSteps.length) {
        this.addSteps();
        this.btnsLock = true;
        setTimeout(() => {
          this.computerMoves();
        }, 1000);
      }
    },

    computerMoves() {
      this.playerSteps = [];
      this.count = this.steps.length;

      function promise({ activeClasses, sounds, gameSpeed }, item) {
        return new Promise((resolve) => {
          activeClasses[item] = true;
          sounds[item].play();
          setTimeout(() => {
            activeClasses[item] = false;
            setTimeout(() => {
              resolve();
            }, gameSpeed); // gap between buttons
          }, 500); // button color lights on 500ms
        });
      }

      setTimeout(async () => {
					for (let item of this.steps) {
						if (this.switcher) {
							await promise(this, item);
						}
					}
					this.btnsLock = false;
      }, 1000);
    },
    addSteps() {
      let random = Math.floor(Math.random() * 4);
      this.steps.push(random);
    },
    gameLevel(difficulty) {
			const {
				levelChange,
			} = this;
			switch (levelChange && difficulty) {
				case 'easy':
					this.gameDifficulty.normal = false;
					this.gameDifficulty.hard = false;
					this.gameDifficulty[difficulty] = true;
					this.gameSpeed = 1500;
					break;
				case 'normal':
					this.gameDifficulty.easy = false;
					this.gameDifficulty.hard = false;
					this.gameDifficulty[difficulty] = true;
					this.gameSpeed = 1000;
					break;
				case 'hard':
					this.gameDifficulty.easy = false;
					this.gameDifficulty.normal = false;
					this.gameDifficulty[difficulty] = true;
					this.gameSpeed = 450;
					break;
				default:
					break;
			}
    },
  },
};
</script>

<style lang='scss'>
$width: 70%;
$black: black;
$red: red;
.box {
  height: 400px;
  width: 400px;
  display: grid;
  grid-template: 1fr 1fr / 1fr 1fr;
  position: relative;
  box-shadow: 0 0 20px $black;
  border-radius: 50%;
}
.animated {
  animation: shakeX 0.5s linear;
}
.box__block {
  border: 20px solid $black;
}
.block-1 {
  background-color: darkgreen;
  border-top-left-radius: 100%;
}
.block-2 {
  background-color: darkcyan;
  border-top-right-radius: 100%;
}
.block-3 {
  background-color: darkred;
  border-bottom-left-radius: 100%;
}
.block-4 {
  background-color: darkblue;
  border-bottom-right-radius: 100%;
}
.green-active,
.block-1--active:active {
  background-color: green;
}
.cyan-active,
.block-2--active:active {
  background-color: cyan;
}
.red-active,
.block-3--active:active {
  background-color: $red;
}
.blue-active,
.block-4--active:active {
  background-color: blue;
}
.box__control-panel {
  position: absolute;
  width: 200px;
  height: 200px;
  background-color: #fff;
  border: 5px solid $black;
  transform: translate(-50%, -50%);
  top: 50%;
  left: 50%;
  border-radius: 50%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-evenly;
}
.switch {
  display: flex;
  align-items: center;
}
.switch-off {
  width: 46px;
  height: 20px;
  margin: 0 5px;
  background-color: $black;
  display: flex;
  align-items: center;
  padding: 0 2px;
  border-radius: 5px;
}
.switcher {
  width: 20px;
  height: 15px;
  background-color: $red;
  border-radius: 3px;
}
.switch-on {
  justify-content: flex-end;
}
.tools__count {
  width: 35px;
  height: 20px;
  background-color: #000;
  border-radius: 5px;
  color: $red;
  text-align: center;
  font-weight: bold;
  font-size: 23px;
  line-height: 20px;
  margin: 0 auto 5px;
}
.tools__btn-start {
  width: 20px;
  height: 20px;
  background-color: $red;
  border-radius: 50%;
  border: 1px solid $black;
  box-shadow: 0px 3px 1px 2px;
  margin: 0 auto 5px;
}
.tools__btn-start:active {
  transform: translateY(1px);
  box-shadow: 0px 2px 1px 2px;
}
.control-panel__tools {
  display: flex;
  justify-content: space-around;
  width: $width;
}
.control-panel__difficulty {
  display: flex;
  justify-content: space-around;
  width: $width;
}

.difficulty__btn {
  width: 20px;
  height: 20px;
  background-color: $black;
  border-radius: 50%;
  border: 4px solid $black;
  margin: 0 auto;
}
.difficulty__btn-active {
  background-color: $red;
}

.modal {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
  font-size: 36px;
  color: #fff;
}
@keyframes shakeX {
  from,
  to {
    -webkit-transform: translate3d(0, 0, 0);
    transform: translate3d(0, 0, 0);
  }

  10%,
  30%,
  50%,
  70%,
  90% {
    -webkit-transform: translate3d(-5px, 0, 0);
    transform: translate3d(-5px, 0, 0);
  }

  20%,
  40%,
  60%,
  80% {
    -webkit-transform: translate3d(5px, 0, 0);
    transform: translate3d(5px, 0, 0);
  }
}
</style>