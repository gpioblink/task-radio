<template>
  <div id="app" class="home">
    <div id="input">
      <p>
        調理時間: <input type="time" step="1" v-model="prepareTimeString">
      </p>
      <p>
        食事時間: <input type="time" step="1" v-model="eatingTimeString">
      </p>
      <p>
        片付け時間:<input type="time" step="1" v-model="washingTimeString">
      </p>
      <p>
        <button type="button" name="button">最終決定</button>
      </p>
    </div>
  </div>
</template>

<script>
import { Component, Vue } from 'vue-property-decorator';

export default {
  name: 'notify-page',
  data() {
    return {
      prepareTimeString: '',
      eatingTimeString: '',
      washingTimeString: ''
    };
  },
  methods: {
    speak(text) {
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
      speechSynthesis.speak(uttr);
    },
    setTimers() {
      //SPAJAMのAPIは後回し: this.playMusic('https://webapi.aitalk.jp/webapi/v2/ttsget.php?username=spajam2019&password=LTMd8Ep8&speaker_name=nozomi&ext=mp3&text=%E4%BB%8A%E6%97%A5%E3%81%AF%E3%81%84%E3%81%84%E5%A4%A9%E6%B0%97%E3%81%A7%E3%81%99%E3%81%AD%E3%80%82&aaa=.mp3');
      setTimeout(this.speak ,1000, "ごはんを炊こう！");
      setTimeout(this.speak ,5000, "ごはんができたよ");
      setTimeout(this.speak ,10000, "おいしかったねー");
    },
    playMusic(sound) {
      if(sound) {
        var audio = new Audio(sound);
        audio.play();
      }
    }
  },
  created() {
    this.setTimers();
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
