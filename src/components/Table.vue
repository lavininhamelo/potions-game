<template>
  <div class="cut  column justify-center items-center">
    <div style="height: 40px">
      <q-btn
        color="blue-grey-10"
        style="margin-bottom: 16px"
        unelevated
        v-if="tableItem"
        label="Cortar"
        @click="cut()"
      />
    </div>
    <Draggable
      :list="tools"
      @start="dragging = true"
      @end="dragging = false"
      chosenClass="chosen"
      ghostClass="ghost"
      class="item row items-center justify-center full-width"
    >
      <div
        @dragstart="getTool(i)"
        @dragend="dropItem()"
        draggable
        v-for="i in tools"
        :key="i.id"
      >
        <img :src="item(i.name)" height="80" style="margin-left: 8px" />
      </div>
    </Draggable>
    <div
      class="table row items-center"
      @drop="onDropTable()"
      @dragover.prevent
      @dragenter.prevent
      :style="table"
    >
      <div class="tool row items-center justify-center">
        <div
          v-if="tool"
          draggable
          @dragend="
            tool = false;
            selectedTool = false;
          "
        >
          <img v-if="tool" :src="item(tool.name)" height="80" />
        </div>
      </div>

      <Draggable
        :list="items"
        @start="dragging = true"
        @end="dragging = false"
        chosenClass="chosen"
        ghostClass="ghost"
        class="item row items-center justify-center"
      >
        <div
          v-if="tableItem"
          draggable
          @dragend="
            tableItem = false;
            selectedtableItem = false;
          "
        >
          <img v-if="tableItem.file" :src="item(tableItem.file)" height="80" />
        </div>
      </Draggable>
      <div class="resultado items-center justify-center">
        <div v-if="resu" draggable @dragend="resu = false">
          <img v-if="resu" :src="item(resu.file)" height="80" />
        </div>
      </div>
    </div>
    <Draggable
      class="row items-center"
      :list="items"
      @start="dragging = true"
      @end="dragging = false"
      chosenClass="chosen"
      ghostClass="ghost"
    >
      <div draggable v-for="i in items" :key="i.id" @dragstart="getItem(i)">
        <img :src="item(i.file)" height="80" />
      </div>
    </Draggable>
    <q-btn
      color="blue-grey-10"
      class="q-mb-lg"
      unelevated
      label="Fechar Tábua de Corte"
      @click="$emit('hide')"
    />
  </div>
</template>

<script>
import Draggable from "vuedraggable";

export default {
  name: "PageIndex",
  props: {
    items: {
      type: Array,
      required: true
    }
  },
  components: {
    Draggable
  },
  data() {
    return {
      tools: [
        { id: 0, name: "cutelo" },
        { id: 1, name: "pilao" }
      ],
      dragging: true,
      selected: 3,
      selectedTool: false,
      selectedtableItem: false,
      tool: false,
      resu: false,
      tableItem: false
    };
  },
  methods: {
    inList(id) {
      let index = this.items.findIndex(val => val.id === id);
      if (index < 0) {
        return false;
      } else {
        return true;
      }
    },

    item(file) {
      const image = require("assets/items/" + file + ".png");
      return image;
    },
    getItem(f) {
      const song = require("assets/songs/get.ogg");
      const audio = new Audio(song);
      this.selectedtableItem = f;
      audio.play();
    },
    getTool(f) {
      const song = require("assets/songs/get.ogg");
      const audio = new Audio(song);
      this.selectedTool = f;
      audio.play();
    },
    dropItem() {
      const song = require("assets/songs/drop.ogg");
      const audio = new Audio(song);
      audio.play();
    },
    async onDropTable() {
      const song = require("assets/songs/wood.wav");
      const audio = new Audio(song);
      this.tableItem = this.selectedtableItem;
      this.tool = this.selectedTool;
      audio.play();
    },
    cut() {
      console.log(this.tableItem.tools[this.tool.name]);
      if (!this.tableItem.tools || !this.tableItem.tools[this.tool.name]) {
        this.$q.notify({
          position: "bottom",
          color: "orange",
          message: `Que diabos você está fazendo?`
        });
      } else {
        this.resu = this.tableItem.tools[this.tool.name];
        if (!this.inList(this.resu.id)) {
          this.$emit("new", this.resu);
        }
      }

      this.tableItem = false;
      this.tool = false;
      this.selectedTool = false;
      this.selectedtableItem = false;
      const song = require("assets/songs/knife.ogg");
      const audio = new Audio(song);
      audio.play();
    }
  },
  computed: {
    table() {
      const image = require("assets/items/tabua.png");
      return `background: url("${image}");`;
    }
  }
};
</script>
<style lang="scss" scoped>
* {
  margin: 0px;
  color: white;
  padding: 0px;
  outline: none;
  border: none;
}

.table {
  padding: 10px;
  height: 170px;
  width: 270px;
  background-size: contain !important;
  background-position: center center !important;
  background-repeat: no-repeat !important;
}

.cut {
  width: 100vw;
  height: 100vh;
  background-color: #131a24;
}
.tool {
  width: 80px;
  height: 80px;
  margin-left: 2px;
}

.items {
  margin-bottom: 16px;
}

.item {
  width: 80px;
  height: 80px;
  margin-left: 2px;
}
.resultado {
  width: 80px;
  height: 80px;
}
</style>
