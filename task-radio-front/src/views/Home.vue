<template>
  <div class="home">
    <div id="input">
      <p>
        調理時間: <input type="text" v-model="prepareTimeString">
        <br>
        <button type="button" >決定</button>
        <pre>{{prepareTimeString}}</pre>
      </p>
      <p>
        食事時間: <input type="text" v-model="eatingTimeString">
        <br>
        <button type="button">決定</button>
        <pre>{{eatingTimeString}}</pre>
      </p>
      <p>
        片付け時間:<input type="text" v-model="washingTimeString">
        <br>
        <button type="button">決定</button>
        <pre>{{washingTimeString}}</pre>
      </p>
      <p>
        <button type="button" name="button">最終決定</button>
      </p>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Vue } from 'vue-property-decorator';
//import HelloWorld from '@/components/HelloWorld.vue'; // @ is an alias to /src

@Component({
  components: {
    //HelloWorld
  },
  data() {
    return {
      prepareTimeString: '',
      eatingTimeString: '',
      washingTimeString: ''
    }
  }
})
export default class Home extends Vue {}
</script>
<style>

div.home{
	margin:0% 0% 0% 0%;
	padding:0% 0% 0% 0%;
	border:0%;
  /*background-image: url("background.jpg");*/
}
button{
  left: 25%;
  right: 25%;
	border-style: dotted;
  background-color: aliceblue;
  color: midnightblue;
	padding: 0% 0% 0% 0%;
}
button:hover {
    background-color: midnightblue;
    /* 文字色を白色に指定 */

    color: aliceblue;
}
input{
  border-radius: 1vh;
}
p{
  color: #009999;
}
</style>
