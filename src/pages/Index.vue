<template>
  <q-page class="flex flex-center potions column items-center justify-center">
    <div v-if="images" class="hidden" v-for="n in images" :key="n">
      <img :src="n" alt="" />
    </div>
    <q-btn
      color="blue-grey-10"
      unelevated
      v-show="deg < 360"
      label="Abrir Tábua de Corte"
      @click="cut = !cut"
    />

    <q-btn
      color="blue-grey-10"
      label="Tentar novamente"
      unelevated
      v-show="deg > 360"
      @click="again()"
    />
    <div class="row items-center justify-center">
      <div>
        <q-btn
          :dense="$q.screen.width < 500"
          color="amber-3"
          class="amber3"
          icon="rotate_left"
          v-show="deg < 360"
          flat
          round
          @click="roll('y')"
        />
      </div>
      <div
        class="cauldron row items-center self center justify-center"
        :style="cauldron(2)"
      >
        <div
          class="fluid row items-center justify-center drop-zone"
          :style="filter"
          @drop="dropped()"
          @dragover.prevent
          :draggable="false"
          @dragenter.prevent
        >
          <img
            :draggable="false"
            :src="fluid"
            :class="rotate"
            :style="fluidSize"
          />
        </div>
      </div>
      <q-btn
        color="amber-3"
        class="amber3"
        icon="rotate_right"
        v-show="deg < 360"
        flat
        :dense="$q.screen.width < 500"
        round
        @click="roll('x')"
      />
    </div>

    <div class="row no-wrap full-width justify-center items-center">
      <q-btn
        color="amber-3"
        class="amber3"
        icon="navigate_before"
        flat
        dense
        round
        @click="scroll(false)"
      />

      <div class="all-items row">
        <Draggable
          class="row  no-wrap"
          :list="items"
          @start="dragging = true"
          @end="dragging = false"
          chosenClass="chosen"
          dragClass="drag"
          ghostClass="ghost"
        >
          <div
            draggable
            v-for="i in items"
            :key="i.id"
            @dragstart="getItem(i.fluid)"
            @dragend="drop()"
          >
            <img :src="getFile(i.file)" height="80" class="mx-4" />
          </div>
        </Draggable>
      </div>
      <q-btn
        color="amber-3"
        class="amber3"
        icon="navigate_next"
        flat
        dense
        round
        @click="scroll(true)"
      />
    </div>
    <q-dialog v-model="cut" persistent>
      <Table :items="items" @hide="cut = false" @new="newItem" />
    </q-dialog>
  </q-page>
</template>

<script>
import { Loading } from "quasar";
import Draggable from "vuedraggable";
import Table from "../components/Table";
export default {
  name: "PageIndex",
  components: {
    Table,
    Draggable
  },
  data() {
    return {
      items2: [],
      images: [],
      liquidRegistry: [11, 12, 28, 18, 10, 9, 8],
      cut: false,
      dragging: false,
      position: false,
      velocity: "n",
      selected: 4,
      mix: 4,
      deg: -50,
      items: [
        {
          name: "Planta",
          id: 28,
          file: "p1",
          fluid: 28,
          tools: {
            cutelo: false,
            pilao: false
          }
        },

        {
          name: "Planta",
          id: 3,
          file: "f39",
          fluid: 18,
          tools: {
            cutelo: { id: 8, name: "cutelo", file: "r399", fluid: 11 },
            pilao: { id: 9, name: "pilao", file: "r398", fluid: 12 }
          }
        },
        {
          name: "Planta",
          id: 1,
          file: "r1",
          fluid: 10,
          tools: {
            cutelo: { id: 6, name: "cutelo", file: "cf4", fluid: 11 },
            pilao: { id: 7, name: "pilao", file: "cf44", fluid: 12 }
          }
        },
        {
          name: "Planta",
          id: 0,
          file: "c66",
          fluid: 9,
          tools: {
            cutelo: false,
            pilao: { id: 5, name: "pilao", file: "d01", fluid: 12 }
          }
        },

        {
          name: "Planta",
          id: 2,
          file: "r48",
          fluid: 18,
          tools: {
            cutelo: false,
            pilao: false
          }
        }
      ]
    };
  },
  methods: {
    loadImages() {
      this.images = this.liquidRegistry.map(value => {
        return require("assets/numbers/" + value + ".gif");
      });
    },

    newItem(value) {
      this.items.push(value);
    },
    finish() {
      this.mix = 22;
      const song = require("assets/songs/dropped.mp3");
      const audio = new Audio(song);
      audio.play();
      this.$q.notify({
        position: "top",
        type: "positive",
        message: `Porção completa, será que deu certo?`
      });
    },
    again() {
      this.deg = 0;
      this.mix = 4;
    },
    getFile(file) {
      const image = require("assets/items/" + file + ".png");
      return image;
    },

    getItem(f) {
      const song = require("assets/songs/get.ogg");
      const audio = new Audio(song);
      this.selected = f;
      audio.play();
    },
    async dropped() {
      console.log("dropped");

      const song = require("assets/songs/bolha.mp3");
      const audio = new Audio(song);
      this.mix = this.selected;
      this.deg = this.deg + 50;
      audio.play();
    },

    drop() {
      const song = require("assets/songs/drop.ogg");
      const audio = new Audio(song);
      audio.play();
    },

    roll(value) {
      let seconds = 2000;
      if (this.velocity === "s") {
        seconds = 3000;
      } else if (this.velocity === "f") {
        seconds = 1000;
      }
      this.position = value;
      setTimeout(() => {
        this.position = false;
      }, seconds);
    },

    cauldron(number) {
      const image = require("assets/caldeirao/" + number + ".png");
      const style = `
        width: ${this.width}px;
        height: ${this.height}px;
        background: url("${image}");
        background-size: ${this.width}px ${this.height}px;
        background-repeat: no-repeat;
        background-position: center;
      `;
      return style;
    },
    scroll(value) {
      const scroll = document.querySelector(".all-items");
      if (value === true) {
        scroll.scrollLeft += 45;
        event.preventDefault();
      } else {
        console.log(value);

        scroll.scrollLeft -= 45;
        event.preventDefault();
      }
    }
  },

  computed: {
    width() {
      return this.height;
    },
    height() {
      const width = this.$q.screen.width;
      if (width >= 1024) {
        return this.$q.screen.height * 0.7;
      } else if (width >= 768) {
        return this.$q.screen.height * 0.6;
      } else if (width > 560) {
        return this.$q.screen.height * 0.5;
      } else return this.$q.screen.height * 0.4;
    },
    rotate() {
      const type = {
        xs: "rotate-x-slow",
        xn: "rotate-x-normal",
        xf: "rotate-x-fast",
        ys: "rotate-y-slow",
        yn: "rotate-y-normal",
        yf: "rotate-y-fast"
      };
      return type[this.position + this.velocity];
    },
    fluidSize() {
      const style = `
        width: ${this.width * 0.45}px;
        height: ${this.height * 0.45}px;
      `;
      return style;
    },
    filter() {
      const n = this.deg;
      if (this.selected != 28) {
        if (n < 360) {
          return `filter: hue-rotate(${n}deg);`;
        } else {
          this.finish();
        }
      }
    },

    fluid() {
      let number = this.mix;
      const image = require("assets/numbers/" + number + ".gif");
      return image;
    }
  },
  mounted() {
    Loading.show();
    this.loadImages();
  },
  watch: {
    images(value) {
      if (value.length >= 7) {
        setInterval(function() {
          Loading.hide();
        }, 5000);
      }
    }
  }
};
</script>
<style lang="stylus" scoped>
* {
  margin: 0px;
  color: white;
  padding: 0px;
  outline: none;
  border: none;
}
.potions {
  width: 100vw;
  height: 100vh;
  background-size: 70% auto;
  background-color: #131a24;
  background-position: center center;
  background-repeat: no-repeat;
}

.fluid img {
  margin-top: 5px;
  border-radius: 50%;
}

.item {
  transform: translate(0, 0);
}



.all-items{
  overflow: auto;
  max-width: 70%;
  padding: 20px 0px;
}

::-webkit-scrollbar-track {
    background-color: #131a24;
    border-radius: 10px;

}
::-webkit-scrollbar {
    width: 10px;
    height: 10px;
    background: #131a24;
    border-radius: 10px;

}
::-webkit-scrollbar-thumb {
    background:#131a24;
    border: 3px solid #131a24;
    border-radius: 10px;
}

.chosen {
  opacity: 1;
}

.drag{
   opacity: 1;

}

.ghost {
  opacity: 1;
}
.rotate-x-slow {
  animation: loading-x 3s linear;
  @keyframes loading-x {
    0% {
      transform: rotate(0);
    }
    100% {
      transform: rotate(360deg);
    }
  }
}

.amber3{
  width: 30px;
  height: 30px;
}

.rotate-x-normal {
  animation: loading-x 2s linear;
  @keyframes loading-x {
    0% {
      transform: rotate(0);
    }
    100% {
      transform: rotate(360deg);
    }
  }
}

.rotate-x-fast {
  animation: loading-x 1s linear;
  @keyframes loading-x {
    0% {
      transform: rotate(0);
    }
    100% {
      transform: rotate(360deg);
    }
  }
}

.rotate-y-slow {
  animation: loading-y 3s linear;
  @keyframes loading-y {
    0% {
      transform: rotate(0);
    }
    100% {
      transform: rotate(-360deg);
    }
  }
}

.rotate-y-normal {
  animation: loading-y 2s linear;
  @keyframes loading-y {
    0% {
      transform: rotate(0);
    }
    100% {
      transform: rotate(-360deg);
    }
  }
}

.rotate-y-fast {
  animation: loading-y 1s linear;
  @keyframes loading-y {
    0% {
      transform: rotate(0);
    }
    100% {
      transform: rotate(-360deg);
    }
  }
}
</style>
