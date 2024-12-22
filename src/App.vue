<template>
  <div v-if="displayTime !== ''" :class="timerClasses">
    <div class="circle" v-if="countdownTime < 10"></div>
    {{ displayTime }}
  </div>
  <template v-else>
    <div class="input-group">
      <input v-model="minutes" type="number" placeholder="Minutes" />
      <input v-model="seconds" type="number" placeholder="Seconds" />
    </div>
    <button @click="startCountdown()">Start</button>
  </template>
</template>

<script>
import { computed, ref } from "vue";

export default {
  name: "App",
  components: {},
  setup() {
    const minutes = ref("");
    const seconds = ref("");
    const countdownTime = ref(0);
    const displayTime = ref("");
    const timer = ref(null);

    const formatTime = computed(() => {
      const mins = Math.floor(countdownTime.value / 60);
      const secs = countdownTime.value % 60;
      if (mins) {
        return `${mins.toString().padStart(2, "0")}:${secs
          .toString()
          .padStart(2, "0")}`;
      } else {
        return secs;
      }
    });

    const timerClasses = computed(() => {
      let classes = ["countdown"];
      if (countdownTime.value < 60 && countdownTime.value >= 10)
        classes.push("countdown--minute");
      if (countdownTime.value < 10) classes.push("countdown--tensec");
      return classes;
    });

    function startCountdown() {
      const totalSeconds =
        parseInt(minutes.value || 0) * 60 + parseInt(seconds.value || 0);
      if (totalSeconds > 0) {
        countdownTime.value = totalSeconds;
        updateDisplay();
        timer.value = setInterval(() => {
          if (countdownTime.value > 0) {
            countdownTime.value -= 1;
            updateDisplay();
          } else {
            clearInterval(timer.value);
          }
        }, 1000);
      }
    }

    const updateDisplay = () => {
      displayTime.value = formatTime.value;
    };

    return {
      minutes,
      seconds,
      countdownTime,
      displayTime,
      timerClasses,
      startCountdown,
    };
  },
};
</script>

<style>
body {
  font-family: Arial, sans-serif;
  text-align: center;
  background-color: #282c34;
  color: white;
  margin: 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
}
.input-group {
  margin: 10px 0;
}
input {
  width: 80px;
  padding: 5px;
  margin-right: 10px;
  border: 2px solid #61dafb;
  border-radius: 5px;
  text-align: center;
  font-size: 1rem;
}
button {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  background-color: #61dafb;
  color: black;
  font-size: 1rem;
  cursor: pointer;
  transition: background-color 0.3s ease;
}
button:hover {
  background-color: #21a1f1;
}
.countdown {
  font-size: 15rem;
}
.countdown--minute {
  font-size: 25rem;
}
.countdown--tensec {
  font-size: 25rem;
  color: red;
}
.countdown--tensec .circle {
  position: absolute;
  top: 50%;
  left: 50%;
  width: 300px;
  height: 300px;
  border: 2rem solid red;
  border-radius: 50%;
  transform: translate(-50%, -50%);
  animation: pulsate 1s infinite ease-in-out;
}
@keyframes pulsate {
  0% {
    transform: translate(-50%, -50%) scale(1);
    opacity: 1;
  }
  50% {
    transform: translate(-50%, -50%) scale(1.5);
    opacity: 0.7;
  }
  100% {
    transform: translate(-50%, -50%) scale(1);
    opacity: 1;
  }
}
</style>
