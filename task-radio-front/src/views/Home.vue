<template>
  <div class="home">
    <div id="input">
      <p>
        調理時間: <input type="text" v-model="prepareTimeString">
        <button type="button" >決定</button>
        <pre>{{prepareTimeString}}</pre>
      </p>
      <p>
        食事時間: <input type="text" v-model="eatingTimeString">
        <button type="button">決定</button>
        <pre>{{eatingTimeString}}</pre>
      </p>
      <p>
        片付け時間:<input type="text" v-model="washingTimeString">
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
