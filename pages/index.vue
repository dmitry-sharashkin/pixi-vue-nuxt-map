<template>
  <div id="app">
    <p-application
      :width="width"
      :height="height"
      :backgroundColor="backgroundColor"
      :resolution="resolution"
      :skipHello="true"
    >
      <p-container
        :hitArea="true"
        :interactive="true"
        :events="['pointerdown']"
        @onpointerdown="pointerDown"
        :buttonMode="true"
        :backgroundColor="backgroundColor"
        :x="mapX"
        :y="mapY"
      >
        <p-sprite>
          <p-graphics :scaleX="scaleX" :scaleY="scaleY" :draw="draw" />
        </p-sprite>
      </p-container>
    </p-application>
    <!-- <div v-for="fr in fSegmentsMap" :key="fr.id">
      {{ `${fr.coordinates.x}  ${fr.coordinates.y}` }}
    </div> -->
  </div>
</template>

<script>
import { arr } from "../constanst/segments";
import { fSegments } from "../constanst/formated-segments.js";
import { fCities } from "../constanst/formated-cities.js";

import {
  PApplication,
  PText,
  PGraphics,
  PContainer,
  PSprite,
} from "vue-pixi-wrapper";
import { Loader } from "pixi.js";

export default {
  name: "IndexPage",
  components: {
    PApplication,
    PText,
    PContainer,
    PGraphics,
    PSprite,
  },
  data: function () {
    return {
      segmentsPos: [],
      width: innerWidth,
      height: innerHeight,
      scaleX: 1,
      scaleY: 1,
      backgroundColor: 0x000000,
      resolution: 1,
      mapX: 0,
      mapY: 0,
    };
  },
  created() {
    // this.mapSegments();
    // this.fSegmentsMap = fSegments;
    // console.log(fSegments);
    console.log(JSON.stringify(this.segmentsPos));
    const loader = new Loader();
    loader.load(this.onAssetsLoaded);
  },

  methods: {
    pointerDown(event) {
      this.scaleX += 0.1;
      this.scaleY += 0.1;

      // this.mapX += 100;
      // this.mapY += 100;
      console.log(event);
    },

    draw(g) {
      g.clear();

      for (let i = 0; i <= fSegments.length; i++) {
        const ar = fSegments[i];
        // console.log(ar?.coordinates);
        g.lineStyle(0);

        g.beginFill(0xfff000);
        g.drawCircle(ar?.coordinates.x * 5, ar?.coordinates.y * 5, 2);
        g.endFill();
        g.on("pointerdown", (event) => {
          console.log("clicked!");
        });
      }

      for (let i = 0; i <= fCities.length; i++) {
        const ar = fCities[i];
        // console.log(ar?.coordinates);
        g.lineStyle(0);
        g.beginFill(0xfffff0);
        g.drawCircle(
          // this.fSegmentsMap[i].coordinates.x,
          // this.fSegmentsMap[i].coordinates.y,

          ar?.coordinates.x * 5,
          ar?.coordinates.y * 5,
          5
        );
        g.endFill();
      }
    },

    mapSegments() {
      let symbolsMap = {
        A: 1,
        B: 2,
        C: 3,
        D: 4,
        E: 5,
        F: 6,
        G: 7,
        H: 8,
        I: 9,
        J: 10,
        K: 11,
        L: 12,
        M: 13,
        N: 14,
        O: 15,
        P: 16,
        Q: 17,
        R: 18,
        S: 19,
        T: 20,
        U: 21,
        V: 22,
        W: 23,
        X: 24,
        Y: 25,
        Z: 26,
      };
      this.segmentsPos = arr.map((s, index) => {
        if (index < 20) {
          return;
        }
        let yPos;
        let letr = s.coordinates.match(/[a-zA-Z]+/g);
        let num = s.coordinates.match(/\d+/g);
        num = num.join("");
        console.log(letr);
        let letrToNum = letr[0].split("");

        // let letrToNum = letr.map((l) => {
        //   let txt = l.split("");
        //   txt = txt.map((item) => {
        //     return +item.replace(
        //       /./gi,
        //       ($0) => symbolsMap[$0.toUpperCase()] || $0
        //     );
        //   });

        //   return txt;
        // });
        if (letrToNum.length === 2) {
          yPos = symbolsMap[letrToNum[0]] * 26 + symbolsMap[letrToNum[1]];
        } else {
          yPos = symbolsMap[letrToNum[0]];
        }

        console.log(yPos);

        s.coordinates = { y: yPos, x: +num };
        return s;
      });
    },
  },
};
</script>
<style>
body {
  margin: 0;
}
</style>
