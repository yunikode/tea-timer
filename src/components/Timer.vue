<template>
  <div class="container has-text-centered visible" id="timer" v-show="showTimer">

    <h1 class="title">{{title}}</h1>
    <div id="timer">
      <span id="minutes">{{minutes}}</span>
      <span id="middle">:</span>
      <span id="seconds">{{seconds}}</span>
    </div>
    <div id="buttons">
      <button id="start" class="button is-dark is-large" v-if="!timer" @click="startTimer">
        <i class="far fa-play-circle"></i>
      </button>
      <button id="stop" class="button is-dark is-large" v-if="timer" @click="stopTimer">
        <i class="far fa-pause-circle"></i>
      </button>
      <button id="reset" class="button is-dark is-large" v-if="resetButton" @click="resetTimer">
        <i class="fas fa-undo"></i>
      </button>
      <button id="reset" class="button is-dark is-large" v-if="playingAlarm" @click="stopAlarm">
        <i class="fas fa-stop"></i>
      </button>
      <button id="select" class="button is-dark is-large" v-if="!timer" @click="selectTea">
        <i class="far fa-list-alt"></i>
      </button>
    </div>

</div>

</template>

<script>
import {
  eventBus
} from '../main'
import {Howl, Howler} from 'howler'

const beep = new Howl({
  src: ['../../static/beep.mp3']
})

export default {
  name: 'Timer',
  data() {
    return {
      teatime: null,
      playingAlarm: false,
      timer: null,
      totalTime: (this.teatime * 60),
      resetButton: false,
      title: 'Let the countdown begin',
      showTimer: true

    }
  },
  methods: {
    startTimer: function() {
      this.timer = setInterval(() => this.countdown(), 1000)
      this.resetButton = true
    },
    stopTimer: function() {
      clearInterval(this.timer)
      this.timer = null
      this.resetButton = true
    },
    resetTimer: function() {
      this.totalTime = (this.teatime * 60)
      clearInterval(this.timer)
      this.timer = null
      this.resetButton = false
    },
    selectTea: function() {
      this.showTimer = false
      document.getElementById('timer').classList.toggle('visible')

      eventBus.$emit('showList')
    },
    padTime: function(time) {
      return (time < 10 ? '0' : '') + time
    },
    playAlarm: function() {
      beep.play()
      this.playingAlarm = true
    },
    stopAlarm: function() {
      beep.stop()
      this.playingAlarm = false
    },
    debugga: function(payload) {
      console.log(payload)
    },
    countdown: function() {
      if (this.totalTime >= 1) {
        this.totalTime--
      } else {
        this.totalTime = 0
        this.playAlarm()
        this.resetTimer()
      }
    }
  },
  created() {
    this.resetTimer()
    // eventBus.$on('showTimer', payload => console.log(payload))
    eventBus.$on('showTimer', data => {console.log(data)

      document.getElementById('timer').classList.toggle('visible')

      this.teatime = data.time
      this.title = data.name
      this.showTimer = true
      this.resetTimer()
    })
  },
  computed: {
    minutes: function() {
      const minutes = Math.floor(this.totalTime / 60)
      return this.padTime(minutes)
    },
    seconds: function() {
      const seconds = this.totalTime - (this.minutes * 60)
      return this.padTime(seconds)
    }
  },

}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
#message {
  color: #ddd;
  font-size: 50px;
  margin-bottom: 20px;
}

#timer {
  font-size: 200px;
  line-height: 1;
  margin-bottom: 40px;
}

@-webkit-keyframes fadeIn { from { opacity:0; } to { opacity:1; } }
@-moz-keyframes fadeIn { from { opacity:0; } to { opacity:1; } }
@keyframes fadeIn { from { opacity:0; } to { opacity:1; } }

.visible {
  opacity:0;  /* make things invisible upon start */
  -webkit-animation:fadeIn ease-in 1;  /* call our keyframe named fadeIn, use animattion ease-in and repeat it only 1 time */
  -moz-animation:fadeIn ease-in 1;
  animation:fadeIn ease-in 1;

  -webkit-animation-fill-mode:forwards;  /* this makes sure that after animation is done we remain at the last keyframe value (opacity: 1)*/
  -moz-animation-fill-mode:forwards;
  animation-fill-mode:forwards;

  -webkit-animation-duration:.5s;
  -moz-animation-duration:.5s;
  animation-duration:.5s;
}


</style>
