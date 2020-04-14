<template>
  <div class="editor-main">
    <p>节点类型选择：</p>
    <div class="choose-container">
      <div
        class="clone-item"
        v-for="(item, index) in cloneList"
        :key="index"
        draggable="true"
        @dragstart="dragStart($event, item.type)"
        @dragover="dragOver"
      >{{item.name}}</div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Editor",
  data() {
    return {
      cloneList: [
        {
          name: "开始",
          type: "start"
        },
        {
          name: "结束",
          type: "end"
        },
        {
          name: "过程1",
          type: "progress"
        },
        {
          name: "过程2",
          type: "progress"
        },
        {
          name: "过程3",
          type: "progress"
        },
        {
          name: "过程4",
          type: "progress"
        }
      ]
    };
  },
  methods: {
    dragStart(e, type) {
      this.$emit("dragChange", e.target, type);
    },
    dragOver(e) {
      e.preventDefault();
    }
  }
};
</script>

<style scoped lang="scss">
.editor-main {
  flex-basis: 300px;
  height: 100%;
  border-right: 1px solid #ccc;

  .choose-container {
    display: flex;
    flex-wrap: wrap;
    width: 100%;
    justify-content: center;

    .clone-item {
      flex-basis: 80px;
      margin: 8px;
    }
  }
}
</style>