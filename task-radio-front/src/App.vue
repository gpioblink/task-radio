<template>
	<div id="app" class="home">
		<h1 id=title>たすくらぢお</h1>
		<div id="input">
			<p class=wasabi>
				<div class=index id=index1>調理時間</div><div class=index id=box1><input class=m id=input1 @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="prepareTimeMin">分<input class=s id=input1 @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="prepareTimeSec">秒</div>
			</p>
			<p class=wasabi>
				<div class=index id=index2>食事時間</div><div class=index id=box2><input class=m id=input2 @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="eatingTimeMin">分<input class=s id=input2 @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="eatingTimeSec">秒</div>
			</p>
			<p class=wasabi>
				<div class=index id=index3>片付け時間</div><div class=index id=box3><input class=m id=input3 @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="washingTimeMin">分<input class=s id=input3 @input="validate" type="tel" maxlength="2" pattern="^[0-9]+$" v-model="washingTimeSec">秒</div>
			</p>
			<p class=wasabi>
				<button type="button" name="button" v-on:click="setTimers">最終決定</button>
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
			prepareTimeMin: '',
			prepareTimeSec: '',
			eatingTimeMin: '',
			eatingTimeSec: '',
			washingTimeMin: '',
			washingTimeSec: ''
		};
	},
	methods: {
		validate() { 
			this.prepareTimeMin = this.prepareTimeMin.replace(/\D/g, '');
			this.prepareTimeSec = this.prepareTimeMin.replace(/\D/g, '');
			this.eatingTimeMin = this.prepareTimeMin.replace(/\D/g, '');
			this.eatingTimeSec = this.prepareTimeMin.replace(/\D/g, '');
			this.washingTimeMin = this.prepareTimeMin.replace(/\D/g, '');
			this.washingTimeSec = this.prepareTimeMin.replace(/\D/g, '');
		}, 
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
			console.log(this.prepareTimeString);
			console.log(this.eatingTimeString);
			console.log(this.washingTimeString);
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
	border-radius: 5vh;
	background: rgb(255, 255, 185);
	font-family: 'sh';
}
div#index1{
	top: 15%;
	bottom: 75%;
}

div#index2{
	top: 45%;
	bottom: 45%;
}

div#index3{
	top: 75%;
	bottom: 15%;
}
#box1{

}
p.wasabi{
	width: 50%;
	align: center;
	background: aqua;
}
input{
	height: 100%;
}
input:hover{
	height: 150%;
	transition: height 0.3s linear 0;
}
input.m{
	position: relative;
	left: 0%;
	border-radius: 5vh;
}
input.s{
	position: relative;
	border-radius: 5vh;
}
#box1{
	top: 25%;
	bottom: 65%;
}
#box2{
	top: 55%;
	bottom: 35%;
}
#box3{
	top: 85%;
	bottom: 5%;
}
</style>
