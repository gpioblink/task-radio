<template>
  <div id="app" class="home">
    <div id="remain">全体の残り時間: {{remainTime}}</div>
    <div id="remain">次の工程までの残り時間: {{stepRemainTime}}</div>
    <div id="input">
      <p>
        調理時間: <input @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="prepareTimeMin"><input @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="prepareTimeSec">
        または 
        <select v-model="select">
          <option v-for="(recipe,key) in recipes" v-bind:value="recipe" v-bind:key="key">
              {{recipe.name}}
          </option>
        </select>
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
      select: { name: "未選択", processes: [] },
      remainTime: '00:00',
      remainTimeNum: 0,
      currentTimerId: 0,
      bgm: new Audio(),
      recipes: [
        { name: "未選択", processes: [] },
        { name: "カレー", processes: [ { time: 1200, text: "野菜を切りましょう!"}, {time: 1200, text: "肉・野菜を炒めましょう!"}, {time: 3000, text: "じっくり煮込みましょう！"},{time: 2000, text: "ルーを入れて仕上げです！！"}] },
        { name: "いりたまご", processes: [ { time: 100, text: "卵と牛乳とこしょうをまぜよう"}, { time: 180, text: "フライパンを温め、バターを落とそう"}, { time: 120, text: "生地を入れて混ぜてできあがり！！"}] },
      ]
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
      return new Promise(function(resolve) {
        // 非同期処理の完了コールバックとしてresolve関数を渡す
        setTimeout(resolve);
      });
    },
    setTimers() {
      //SPAJAMのAPIは後回し: this.playMusic('https://webapi.aitalk.jp/webapi/v2/ttsget.php?username=spajam2019&password=LTMd8Ep8&speaker_name=nozomi&ext=mp3&text=%E4%BB%8A%E6%97%A5%E3%81%AF%E3%81%84%E3%81%84%E5%A4%A9%E6%B0%97%E3%81%A7%E3%81%99%E3%81%AD%E3%80%82&aaa=.mp3');
      //this.setVoiceTimers();
      console.log(this.select);
      if(this.select == undefined || this.select.name == "未選択") this.setVoiceTimers();
      else this.setSequenceTimers(this.select);
    },
    setVoiceTimers() {
      const prepareTime = Number(this.prepareTimeMin*60+this.prepareTimeSec)*1000;
      const eatingTime = Number(this.eatingTimeMin*60+this.eatingTimeSec)*1000; 
      const washingTime = Number(this.washingTimeMin*60+this.washingTimeSec)*1000;
      setTimeout(this.speak , 0, "ごはんを作ろう！", PrepareSound);
      setTimeout(this.speak , prepareTime, "ごはんができたね！", EatingSound);
      setTimeout(this.speak , prepareTime+eatingTime, "おいしかったねー！さあ、お片付けの時間だよ", WashingSound);
      setTimeout(this.speak , prepareTime+eatingTime+washingTime, "片付け、上手にできたね！みんな、おつかれ様！！", "");
      this.setCountdownTimer(prepareTime+eatingTime+washingTime);
    },
    setSequenceTimers(recipe) {
      let t = 0;
      if(recipe){
        for(let process of recipe.processes){
          console.log(process);
          console.log(process.time);
          setTimeout(this.speak, t, process.text, PrepareSound);
          this.setCountdownTimer(t);
          t += Number(process.time)*1000;
        }
      }
      const prepareTime = t;
      const eatingTime = Number(this.eatingTimeMin*60+this.eatingTimeSec)*1000; 
      const washingTime = Number(this.washingTimeMin*60+this.washingTimeSec)*1000;
      setTimeout(this.speak , prepareTime, "ごはんができたね！", EatingSound);
      this.setCountdownTimer(prepareTime);
      setTimeout(this.speak , prepareTime+eatingTime, "おいしかったねー！さあ、お片付けの時間だよ", WashingSound);
      this.setCountdownTimer(prepareTime+eatingTime);
      setTimeout(this.speak , prepareTime+eatingTime+washingTime, "片付け、上手にできたね！みんな、おつかれ様！！", "");
      this.setCountdownTimer(prepareTime+eatingTime+washingTime);
    },
    setCountdownTimer(milisec) {
      clearInterval(this.currentTimerId);
      const sec = milisec / 1000;
      this.remainTimeNum = sec;
      this.currentTimerId = setInterval(this.countDown,1000);
    },
    countDown() {
      if(this.remainTimeNum > 0) {
        this.remainTimeNum -= 1;
        this.remainTime = this.calcTimeNum2TimeString(this.remainTimeNum);
      }else{
        clearInterval(this.currentTimerId);
      }
    },
    calcTimeNum2TimeString(timeNum) {
      const min = parseInt(String(timeNum / 60));
      const sec = timeNum % 60;
      return `${("00" + String( min )).substr(-2)}:${("00" + String( sec )).substr(-2)}`;
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
