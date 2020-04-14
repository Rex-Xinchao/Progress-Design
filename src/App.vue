<template>
  <div id="app">
    <Editor @dragChange="dragChange" />
    <Canvas :draggingItem="draggingItem" :type="type" />
  </div>
</template>

<script>
import Canvas from "./components/Canvas.vue";
import Editor from "./components/Editor.vue";

export default {
  name: "App",
  components: {
    Canvas,
    Editor
  },
  data() {
    return {
      draggingItem: {},
      type: ""
    };
  },
  methods: {
    dragChange(data, type) {
      this.draggingItem = data;
      this.type = type;
    }
  }
};
</script>

<style lang="scss">
html,
body,
#app {
  width: 100%;
  height: 100%;
  padding: 0;
  margin: 0;
}

#app {
  display: flex;
  flex-wrap: nowrap;
  justify-items: center;
  align-items: center;
}

h3 {
  margin: 40px 0 0;
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

.clone-item {
  text-align: center;
  width: 80px;
  height: 32px;
  box-sizing: border-box;
  line-height: 32px;
  border: 1px solid #cccccc;
  border-radius: 4px;
  cursor: pointer;
}
</style>
