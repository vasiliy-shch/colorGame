<template>
    <div class="game">
      <div>
        <div class="game__counter">Раунд: {{ counter }}</div>
        <div v-show="error" class="game__counter_error">Игра законечна! Ваш результат: {{ counter }}</div>
      </div>
      <div class="game__levels">
        <label class="game__levelsTitle">Уровни сложности: </label>
        <div class="game__level">
          <input type="radio" name="level" value="1500" v-model="level">
          <label>Легкий</label>
        </div>
        <div class="game__level">
          <input type="radio" name="level" value="1000" v-model="level">
          <label>Средний</label>
        </div>
        <div class="game__level">
          <input type="radio" name="level" value="500" v-model="level">
          <label>Сложный</label>
        </div>
      </div>
      <div>
        <div class="game__field">
          <div 
            v-for="item in items"
            :key="item.id"
            class="game__color"
            :class = "[item.class,
              item.light ? 'light' : '']"
            @click="chooseColor(item)"
          >
          </div>
        </div>
        <button v-if="startButton" @click="start" class="game__button">Старт</button>
        
        <span class="game__counter_error" v-show="!level">Выберите уровень</span>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        game: null,
        startButton: true,
        time: null,
        counter: 0,
        answer: 0,
        error: false,
        level: false,
        isLightTime: 600,
        viewer: false,
        roadGame: [],
        items: [
          {
            id: 1,
            class: 'game__color_red',
            activeClass: 'game__color_red light',
            light: null,
            src: '/src/components/audio/1.mp3'
          },
          {
            id: 2,
            class: 'game__color_green',
            activeClass: 'game__color_red light',
            light: false,
            src: '/src/components/audio/2.mp3'
          },
          {
            id: 3,
            class: 'game__color_blue',
            activeClass: 'game__color_red light',
            light: false,
            src: '/src/components/audio/3.mp3'
          },
          {
            id: 4,
            class: 'game__color_orange',
            activeClass: 'game__color_red light',
            light: false,
            src: '/src/components/audio/4.mp3'
          }
        ],
      }
    },
    methods: {
      start() {
        if (this.error === false && this.level !== false && this.game === null) {
          this.viewer = false;
          this.startButton = false;
          this.counter += 1;
          this.time += Number(this.level);
          let timeCircleLight = this.isLightTime + Number(this.level);
          let allTimeCircle = this.time + (this.isLightTime * this.counter);
          this.game = setInterval(this.genColor, timeCircleLight);

          setTimeout(() => {
              clearInterval(this.game);
              this.viewer = true;
          }, allTimeCircle);
        }
        if (this.error === true) {
          this.counter = 0;
          this.answer = 0;
          this.roadGame = [];
          this.error = false;
          this.game = null;
          this.start()
        }
      },
      chooseColor(item) {
        if (this.viewer === true) {
          this.genAudio(item);
          let lastAnswer = this.roadGame.length-1;
          item.light = true;
          setTimeout(() => item.light = false, 100);
          if ( this.error === false && this.roadGame[this.answer] === item.id && lastAnswer === this.answer) {
            this.answer += 1;
            this.viewer = false;
            this.game = null;
            this.start();
          } else if (this.error === false && this.roadGame[this.answer] === item.id) {
            this.answer += 1;
          } else {
            this.viewer = false;
            this.error = true;
            this.time = null;
            this.startButton = true;
          }
        } else {
          return;
        }
      },
      genAudio(item) {
        let myAudio = new Audio(item.src);
        myAudio.play();
      },
      genColor() {
        let id = this.generateRandomId(1, 4);
        this.roadGame.push(id);
        this.items.forEach((item) => {
          if (item.id === id) {
            item.light = true;
            setTimeout(() => item.light = false, this.isLightTime);
            this.genAudio(item);
          }
        });
      },
      generateRandomId(min, max) {
        min = Math.ceil(min);
        max = Math.floor(max);
        return Math.floor(Math.random() * (max - min + 1) + min);
      }
    }
  }
  </script>
  
<style>
.game {
  display: flex;
  justify-content: space-evenly;
  padding: 72px 36px;
}
.game__counter {
  font-size: 25px;
  font-weight: 500;
  width: 150px;
}
.game__counter_error {
  color: red;
  margin-top: 20px;
  font-size: 20px;
}
.game__levels {
  display: flex;
  flex-direction: column;
}
.game__levelsTitle {
  font-size: 25px;
  font-weight: 500;
  margin-bottom: 20px;
}
.game__level {
  font-size: 20px;
}
.game__field {
  display: flex;
  flex-wrap: wrap;
  width: 200px;
  height: 200px;
}
.game__fieldRow {
  display: flex;
  box-sizing:content-box;
}
.game__color {
  width: 100px;
  height: 100px;
  cursor: pointer;
}
.game__color_red {
  background-color: red;
  border-radius: 100% 0 0 0;
  opacity: 0.6;
}
.game__color_red:hover {
  box-shadow: -1px -1px 0px  rgba(0,0,0,1);
}
.game__color_green {
  background-color: green;
  border-radius: 0 100% 0 0;
  opacity: 0.6;
}
.game__color_green:hover {
  box-shadow: 1px -1px 0px  rgba(0,0,0,1);
}
.game__color_blue {
  background-color: blue;
  border-radius: 0 0 0 100%;
  opacity: 0.6;
}
.game__color_blue:hover {
  box-shadow: -1px 1px 0px  rgba(0,0,0,1);
}
.game__color_orange {
  background-color: orange;
  border-radius: 0 0 100% 0;
  opacity: 0.6;
}
.game__color_orange:hover {
  box-shadow: 1px 1px 0px  rgba(0,0,0,1);
}
.game__button {
  width: 100%;
  height: 36px;
  margin-top: 30px;
  padding: 0 16px;
  font-size: 16px;
  text-align: center;
  box-sizing: border-box;
  color: white;
  border-radius: 8px;
  border: none;
  background-color: rgb(0, 119, 255);
  cursor: pointer;
}
.game__button:hover {
  opacity: 0.9;
}
.light {
  opacity: 1;
}
</style>