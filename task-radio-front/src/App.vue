<template>
  <div id="app" class="home">
    <h1 id=title>たすくらぢお</h1>
    <div id="remain1">全体の残り時間: <br>{{remainTime}}</div>
    <div id="remain2">次の工程までの残り時間: <br>{{partRemainTime}}</div>
    <div id="input">
      <p class=wasabi>
				<div class=index id=index1>調理時間</div><div class=index id=box1><input class=m @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="prepareTimeMin"><p class=minute>分</p><input class=s @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="prepareTimeSec"><p class=second>秒</p></div>
			</p>
      <p class=wasabi>
        <div class=index id=index15>または </div>
		<div class=index id=box15>
        <select v-model="select">
          <option v-for="(recipe,key) in recipes" v-bind:value="recipe" v-bind:key="key">
              {{recipe.name}}
          </option>
        </select>
		</div>
      </p>
      <p class=wasabi>
				<div class=index id=index2>食事時間</div><div class=index id=box2><input class=m @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="eatingTimeMin"><p class=minute>分</p><input class=s @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="eatingTimeSec"><p class=second>秒</p></div>
			</p>
      <p class=wasabi>
				<div class=index id=index3>片付け時間</div><div class=index id=box3><input class=m @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="washingTimeMin"><p class=minute>分</p><input class=s @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="washingTimeSec"><p class=second>秒</p></div>
			</p>
			<p class=wasabi>
				<div class=buttonn><button id="saischu" type="button" name="button" v-on:click="setTimers">最終決定</button></div>
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
      partRemainTime: '00:00',
      partRemainTimeNum: 0,
      partCurrentTimerId: 0,
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
      setTimeout(this.setCountdownTimerPart , 0, prepareTime);
      setTimeout(this.speak , prepareTime, "ごはんができたね！", EatingSound);
      setTimeout(this.setCountdownTimerPart , prepareTime, eatingTime);
      setTimeout(this.speak , prepareTime+eatingTime, "おいしかったねー！さあ、お片付けの時間だよ", WashingSound);
      setTimeout(this.setCountdownTimerPart ,prepareTime+eatingTime, washingTime);
      // setTimeout(this.partRemainTime , 設定するまでにかかってる時間, 設定するタイマーの時間);
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
          setTimeout(this.setCountdownTimerPart, t, Number(process.time)*1000);
          this.setCountdownTimer(t);
          t += Number(process.time)*1000;
        }
      }
      const prepareTime = t;
      const eatingTime = Number(this.eatingTimeMin*60+this.eatingTimeSec)*1000; 
      const washingTime = Number(this.washingTimeMin*60+this.washingTimeSec)*1000;
      setTimeout(this.setCountdownTimerPart , prepareTime, "ごはんができたね！", EatingSound);
      this.setCountdownTimer(prepareTime);
      setTimeout(this.setCountdownTimerPart , prepareTime+eatingTime, "おいしかったねー！さあ、お片付けの時間だよ", WashingSound);
      this.setCountdownTimer(prepareTime+eatingTime);
      setTimeout(this.setCountdownTimerPart , prepareTime+eatingTime+washingTime, "片付け、上手にできたね！みんな、おつかれ様！！", "");
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
    setCountdownTimerPart(milisec) {
      console.log("次までの時間", milisec);
      clearInterval(this.partCurrentTimerId);
      const sec = milisec / 1000;
      this.partRemainTimeNum = sec;
      this.partCurrentTimerId = setInterval(this.countDownPart,1000);
    },
    countDownPart() {
      if(this.partRemainTimeNum > 0) {
        this.partRemainTimeNum -= 1;
        this.partRemainTime = this.calcTimeNum2TimeString(this.partRemainTimeNum);
      }else{
        clearInterval(this.partCurrentTimerId);
      }
    },
    calcTimeNum2TimeString(timeNum) {
      const min = parseInt(String(timeNum / 60));
      const sec = parseInt(String(timeNum % 60));
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
@font-face {
font-family: 'sh';
src: url('sh.ttf');
font-weight: normal;
font-style: normal;
}
#app {
	position: absolute;
	top: 0%;
	bottom: 0%;
	left: 0%;
	right: 0%;
	font-family: 'sh', Helvetica, Arial, sans-serif;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
	text-align: center;
	color: #2c3e50;
/*	background-image: url("background.jpg");*/
	background-size: cover;
	background-color: rgb(255, 214, 167);
	min-width: 100%;
	min-height: 100%;
}
#nav {
	pudding: 30px;/*プリン食べたい*/
}

#nav a {
	font-weight: bold;
	color: #2c3e50;
}

#nav a.router-link-exact-active {
	color: #42b983;
}
h1{
	position: absolute;
	margin: 0%;
	padding: 3%;
	border: 0%;
	top: 0%;
	left: 0%;
	right: 0%;
	background: linear-gradient(darkorange,rgb(255, 214, 167));
}
div.index{
	position: absolute;
	border: 10%;
	left: 10%;
	right: 10%;
	font-size:8vh;
	border-color: rgb(255, 128, 128);
	border-style:dotted;
	border-radius: 8vh;
	background: rgb(255, 255, 185);
	font-family: 'sh';
}
div.buttonn{
	position: absolute;
	border:0%;
	top:95%;
	bottom:0%;
	left:10%;
	right:10%;
	border-style:dotted;
	border-radius: 8vh;
	font-family: 'sh', Helvetica, Arial, sans-serif;
}
div#index1{
	background: rgb(200, 200, 80);
	top: 35%;
	bottom: 55%;
	right:50%;
	border-radius: 5vh 0% 0% 5vh;
	font-size:4vh;
	padding:2%;
}
div#remain1{
	position: absolute;
	top:20%;
	bottom:65%;
	left:10%;
	font-size: 2vh;
}
div#remain2{
	position: absolute;
	top:20%;
	bottom:65%;
	right:10%;
	font-size: 2vh;
}
div#index15{
	background: rgb(200, 200, 80);
	position: absolute;
	top: 35%;
	bottom: 55%;
	left:50%;
	border-radius: 0% 5vh 5vh 0%;
	font-size: 7vh;
}

div#index2{
	background: rgb(200, 200, 80);
	top: 55%;
	bottom: 35%;
	font-size: 7vh;
}

div#index3{
	background: rgb(200, 200, 80);
	top: 75%;
	bottom: 15%;
	font-size: 7vh;
}
p.wasabi{
	width: 50%;
	align: center;
	background: aqua;
}
input{
	height: 100%;
	width: 20%;
	border-style:none;
	border-radius: 5vh;
}
input:hover{
	background: orange;
	transition: background 0.3s linear 0;
}
input.m{
	position: absolute;
	left: 10%;
	right: 70%;
}
input.s{
	position: absolute;
	left: 50%;
	right: 30%;
}
p.minute{
	position: absolute;
	top: -100%;
	left: 33%;
	right: 50%;
}
p.second{
	position: absolute;
	top: -100%;
	left:73%;
	right: 10%;
}
div#box1{
	top: 45%;
	bottom: 45%;
	right:50%;
	border-radius: 5vh 0% 0% 5vh;
}
div#box15{
	position: absolute;
	top: 45%;
	bottom: 45%;
	left:50%;
	border-radius: 0% 5vh 5vh 0%;
}
div#box2{
	top: 65%;
	bottom: 25%;
}
div#box3{
	top: 85%;
	bottom: 5%;
}
button#saischu{
	background: rgb(200, 100, 80);
	position: absolute;
	top:0%;
	bottom:0%;
	left:0%;
	right:0%;
	background:rgba(0,0,0,0);
	border-style:none;
	/*transform: rotatez(10deg);*/
}
</style>
