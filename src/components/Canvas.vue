<template>
  <div class="canvas-main" id="canvas-main" @dragover="dragOver" @drop="drop($event)">
    <ul class="tips" v-show="showTip" id="tip">
      <li @click="edit">编辑</li>
      <li @click="remove">删除</li>
    </ul>
    <div class="save-btn" @click="saveProgress">保存</div>
  </div>
</template>

<script>
export default {
  name: "Canvas",
  data() {
    const vm = this;
    return {
      dom: null,
      showTip: false,
      nodeData: [],
      linkData: [],
      Common: {
        isSource: true,
        isTarget: true,
        maxConnections: -1,
        // 端点样式
        paintStyle: {
          stroke: "lightblue",
          fill: "lightblue",
          radius: 6,
          lineWidth: 2
        },
        hoverPaintStyle: { stroke: "blue" },
        // 连线样式
        connector: [
          "Flowchart",
          { gap: 10, cornerRadius: 5, alwaysRespectStubs: true }
        ],
        connectorStyle: {
          stroke: "lightgray",
          strokeWidth: 3
        },
        connectorOverlays: [
          ["Arrow", { width: 10, length: 10, location: 1 }],
          [
            "Custom",
            {
              create: vm.createLabel,
              location: 0.5
            }
          ]
        ]
      }
    };
  },
  props: {
    draggingItem: {
      required: true
    },
    type: {
      required: true,
      type: String
    }
  },
  methods: {
    saveProgress() {
        console.log(this.linkData)
        console.log(this.nodeData)
    },
    createEndPonit(id) {
      if (["start", "progress"].indexOf(this.type) >= 0) {
        this.$jsPlumb.addEndpoint(id, {
          anchors: "Bottom",
          ...this.Common
        });
      }
      if (["end", "progress"].indexOf(this.type) >= 0) {
        this.$jsPlumb.addEndpoint(id, {
          anchors: "Top",
          ...this.Common
        });
      }
      this.$jsPlumb.draggable(id);
    },
    createLabel(e) {
        // todo 有时会获取到的是端点的id，而不是标签的id
        // todo node里面要记录端点的id和位置
      let sourceId = e.sourceId
    //   let targetId = e.targetId
      let newDiv = document.createElement("div");
      let newInput = document.createElement("input");
      let newSpan = document.createElement("span");
      newDiv.setAttribute("class", "label-container");
      newDiv.setAttribute("target", e.target.id);
      newDiv.setAttribute("source", e.source.id);
      newInput.setAttribute("class", "label-input");
      newInput.setAttribute("value", "标签");
      newSpan.innerHTML = "标签";
      newDiv.onchange = e => {
        let node = this.linkData.find(data => data.source === sourceId )
        node.label = e.target.value
        newSpan.innerHTML = e.target.value;
      };
      newDiv.appendChild(newSpan);
      newDiv.appendChild(newInput);
      return newDiv;
    },
    dragOver(e) {
      e.preventDefault();
    },
    drop(e) {
      const id = "btn" + new Date().getTime();
      const dom = this.draggingItem.cloneNode(true);
      dom.style.position = "absolute";
      dom.style.top = e.offsetY + "px";
      dom.style.left = e.offsetX + "px";
      dom.id = id;
      dom.setAttribute("draggable", false);
      dom.oncontextmenu = e => {
        e.preventDefault();
      };
      dom.onmousedown = e => {
        if (e.button == 2) {
          this.dom = dom;
          this.showTips(e);
        }
      };
      document.getElementById("canvas-main").appendChild(dom);
      this.createEndPonit(id);
      this.nodeData.push({
        id: id,
        name: dom.innerHTML
      });
    },
    showTips(e) {
      this.showTip = true;
      document.getElementById("tip").style.left = e.clientX - 300 + 20 + "px";
      document.getElementById("tip").style.top = e.clientY + 22 + "px";
    },
    edit() {
      console.log(this.dom);
    },
    remove() {
      this.$jsPlumb.remove(this.dom.id);
      this.showTip = false;
    }
  },
  mounted() {
    this.$jsPlumb.ready(() => {
      this.$jsPlumb.setContainer("canvas-main");
      this.$jsPlumb.bind("beforeDrop", info => {
        return info.sourceId !== info.targetId;
      });
      this.$jsPlumb.bind("connection", data => {
        this.linkData.push({
          target: data.target.id,
          source: data.source.id,
          label: "标签"
        });
      });
    });
    document.body.onclick = () => {
      this.showTip = false;
    };
  }
};
</script>

<style scoped lang="scss">
.canvas-main {
  flex: 2;
  height: 100%;
  padding: 8px;
  position: relative;
  overflow: hidden;

  .tips {
    position: absolute;
    left: 0;
    top: -50px;
    z-index: 1000;
    display: block;
    width: 120px;
    border: 1px solid #ccc;
    padding: 0;
    margin: 0;
    background-color: white;

    li {
      display: block;
      width: 100%;
      line-height: 22px;
      text-align: center;
      padding: 0;
      margin: 0;
      border-bottom: 1px solid #ccc;
      cursor: pointer;

      &:last-child {
        border: none;
      }
    }
  }

  .save-btn {
    width: 60px;
    line-height: 32px;
    background-color: red;
    border-radius: 4px;
    border: none;
    color: white;
    position: absolute;
    top: 10px;
    right: 10px;
    text-align: center;
    cursor: pointer;
  }
}
.item {
  position: absolute;
  width: 200px;
  height: 200px;
  border: 1px solid #cccccc;
}
</style>
<style lang="scss">
.label-container {
  width: auto;
  background-color: rgba(255, 255, 255, 0);
  border: none;
  position: relative;
  font-size: 14px;
  font-weight: normal;
  text-align: center;
  line-height: 28px;

  &:hover {
    background-color: rgba(255, 255, 255, 1);
    border: 1px solid #cccc;
  }

  span {
    display: inline-block;
    min-width: 80px;
    height: 100%;
    color: rgba(0, 0, 0, 0);
  }

  .label-input {
    display: inline-block;
    width: 100%;
    height: 100%;
    position: absolute;
    left: 0;
    padding: 0;
    margin: 0;
    outline: none;
    border: none;
    text-align: center;
    background-color: rgba(255, 255, 255, 0);
  }
}
</style>