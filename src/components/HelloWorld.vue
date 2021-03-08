<template>
  <div class="hello">
    <model-fbx backgroundColor="pink" @on-load="loadSuccess()" :position="{ x: 0, y: 0, z: 0 }" src="static/QTZSB.FBX">
      <!-- <model-fbx src="static/dancing.fbx"></model-fbx> -->
    </model-fbx>
  </div>
</template>

<script>
import { ModelFbx } from "vue-3d-model";
export default {
  name: "HelloWorld",
  components: {
    ModelFbx,
  },
  data() {
    return {
      msg: "Welcome to Your Vue.js App",
    };
  },
  methods:{
    loadSuccess(event){
      console.log(event)
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
.hello {
  height: 1000px;
  background: opacity;
}
</style>
