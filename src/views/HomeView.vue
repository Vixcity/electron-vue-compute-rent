<template>
  <div class="home">
    <div class="container">
      <div class="block">
        <span class="demonstration">年</span>
        <el-date-picker v-model="value3" type="year" placeholder="选择年">
        </el-date-picker>
      </div>
    </div>
    <img alt="Vue logo" src="../assets/logo.png" />
    <HelloWorld msg="Welcome to Your Vue.js App" />
  </div>
</template>

<script>
// @ is an alias to /src
import HelloWorld from "@/components/HelloWorld.vue";

export default {
  name: "HomeView",
  components: {
    HelloWorld,
  },
};
</script>
