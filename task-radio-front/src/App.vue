<template>
  <div id="app">
    <div id="nav">
      <router-link to="/">Home</router-link> |
      <router-link to="/about">About</router-link>
    </div>
    <router-view/>
    <button v-on:click="speak('ごはんが完成したよ！')">ああああああ</button>
  </div>
</template>

<script>
export default {
  name: 'notify-page',
  data() {
    return {
    };
  },
  methods: {
    speak(text) {
      const uttr = new SpeechSynthesisUtterance(text);
      console.log("時間です！");
      //「イギリス人男性風の声質」のvoiceオブジェクトを取得
      let voice = speechSynthesis.getVoices().find(function(voice){
        return voice.name === 'Google 日本語';
      });

      // 取得できた場合のみ適用する
      if(voice){
        uttr.voice = voice;
      }
      speechSynthesis.speak(uttr);
    }
  },
  created() {
    //window.speechSynthesis.onvoiceschanged = (e) => { this.speak("時間になりましたー"); 
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
