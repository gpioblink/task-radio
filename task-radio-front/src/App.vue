<template>
  <div id="app" class="home">
    <div id="input">
      <p>
        調理時間: <input @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="prepareTimeMin"><input @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="prepareTimeSec">
      </p>
      <p>
        食事時間: <input @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="eatingTimeMin"><input @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="eatingTimeSec">
      </p>
      <p>
        片付け時間: <input @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="washingTimeMin"><input @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="washingTimeSec">
      </p>
      <p>
        <button type="button" name="button" v-on:click="setTimers">最終決定</button>
      </p>
    </div>
  </div>
</template>

<script>
import { Component, Vue } from 'vue-property-decorator';
import PongSound from '@/assets/pong.mp3';
import EatingSound from '@/assets/eating.mp3';
import PrepareSound from '@/assets/prepare.mp3';
import WashingSound from '@/assets/washing.mp3';
export default {
  name: 'notify-page',
  data() {
    return {
      prepareTimeMin: '',
      prepareTimeSec: '',
      eatingTimeMin: '',
      eatingTimeSec: '',
      washingTimeMin: '',
      washingTimeSec: '',
      bgm: new Audio()
    };
  },
  methods: {
    validate() { 
      this.prepareTimeMin = this.prepareTimeMin.replace(/\D/g, '');
      this.prepareTimeSec = this.prepareTimeSec.replace(/\D/g, '');
      this.eatingTimeMin = this.eatingTimeMin.replace(/\D/g, '');
      this.eatingTimeSec = this.eatingTimeSec.replace(/\D/g, '');
      this.washingTimeMin = this.washingTimeMin.replace(/\D/g, '');
      this.washingTimeSec = this.washingTimeSec.replace(/\D/g, '');
    }, 
    speak(text,sound) {
      const uttr = new SpeechSynthesisUtterance(text);
      console.log(text);
      //「日本人の声質」のvoiceオブジェクトを取得
      let voice = speechSynthesis.getVoices().find(function(voice){
        return voice.name === 'Google 日本語';
      });

      // 取得できた場合のみ適用する
      if(voice){
        uttr.voice = voice;
      }
      this.pauseBGM();
      this.playNewTaskSE();
      speechSynthesis.speak(uttr);
      setTimeout(this.changeBGM,2000,sound);
    },
    setTimers() {
      //SPAJAMのAPIは後回し: this.playMusic('https://webapi.aitalk.jp/webapi/v2/ttsget.php?username=spajam2019&password=LTMd8Ep8&speaker_name=nozomi&ext=mp3&text=%E4%BB%8A%E6%97%A5%E3%81%AF%E3%81%84%E3%81%84%E5%A4%A9%E6%B0%97%E3%81%A7%E3%81%99%E3%81%AD%E3%80%82&aaa=.mp3');
      this.setVoiceTimers();
    },
    setVoiceTimers() {
      const prepareTime = Number(this.prepareTimeMin*60+this.prepareTimeSec)*1000;
      const eatingTime = Number(this.eatingTimeMin*60+this.eatingTimeSec)*1000; 
      const washingTime = Number(this.washingTimeMin*60+this.washingTimeSec)*1000;
      setTimeout(this.speak , 0, "ごはんを作ろう！", PrepareSound);
      setTimeout(this.speak , prepareTime, "ごはんができたね！", EatingSound);
      setTimeout(this.speak , prepareTime+eatingTime, "おいしかったねー！さあ、お片付けの時間だよ", WashingSound);
      setTimeout(this.speak , prepareTime+eatingTime+washingTime, "片付け、上手にできたね！みんな、おつかれ様！！", "");
    },
    setCountdownTimers() {

    },
    playMusic(sound) {
      if(sound) {
        const audio = new Audio(sound);
        audio.play();
      }
    },
    playNewTaskSE() {
      const audio = new Audio(PongSound);
      audio.play();
    },
    changeBGM(sound) {
      this.bgm.pause();
      this.bgm.loop = true;
      this.bgm.currentTime = 0;
      this.bgm.src = sound;
      this.bgm.volume = 0.2;
      this.bgm.play();
    },
    pauseBGM(sound) {
      this.bgm.pause();
    }
  },
  created() {
  }
}
</script>



<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
</style>
