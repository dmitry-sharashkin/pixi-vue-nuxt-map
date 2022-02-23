<template>
  <div
    id="app"
    ref="app"
    @wheel="onScroll"
    @mousedown="pointerDown"
    @mouseup="pointerUp"
    @mousemove="pointerMove"
    class="connections"
  >
    <canvas id="pixi"></canvas>
  </div>
</template>

<script>
import { BlurFilter } from "@pixi/filter-blur";
import { Container } from "@pixi/display";
import { Graphics } from "@pixi/graphics";
import { Sprite } from "@pixi/sprite";

import _ from "lodash";

import { fSegments } from "../constanst/formated-segments.js";

import * as PIXI from "pixi.js";

export default {
  data() {
    return {
      rotation: 0,
      viewContainer: {},
      scaleX: 1,
      scaleY: 1,
      mapX: 0,
      mapY: 0,
      zoomStep: 0.1,
      clientPos: {
        x: null,
        y: null,
      },
      app: null,
    };
  },

  mounted() {
    this.app = document.getElementById("app");

    this.drawPixi();
  },

  methods: {
    onScroll(event) {
      if (event.deltaY < 0) {
        this.scaleX += this.zoomStep;
        this.scaleY += this.zoomStep;
        console.log(this.scaleX);
        console.log(this.scaleY);
        return;
      }
      this.scaleX -= this.zoomStep;
      this.scaleY -= this.zoomStep;
      console.log(this.scaleX);
      console.log(this.scaleY);
    },

    pointerDown(event) {
      this.clientPos.x = event.clientX;
      this.clientPos.y = event.clientY;

      this.dragging = true;
    },
    pointerUp(event) {
      this.dragging = false;
    },

    throttleMove: _.throttle(function (event) {
      this.pointerMove(event);
    }, 10),
    pointerMove(event) {
      if (this.dragging) {
        this.mapX = event.clientX - this.clientPos.x;
        this.mapY = event.clientY - this.clientPos.y;

        // this.app.style.top = this.mapY + "px";
        // this.app.style.left = this.mapX + "px";
      }
    },

    drawPixi() {
      let canvas = document.getElementById("pixi");

      const app = new PIXI.Application({
        width: window.innerWidth,
        height: window.innerHeight,
        antialias: true,
        // transparent: true,
        view: canvas,
      });

      const baseTexture = PIXI.Texture.from(
        "https://s3-us-west-2.amazonaws.com/s.cdpn.io/693612/IaUrttj.png"
      );

      const cordFactor = 5;
      const halfCordFactor = cordFactor / 2;

      let mapContainer = new Container();

      let baseX = 0;
      let baseY = 0;

      fSegments.forEach(function (item) {
        let x = item.coordinates.x * cordFactor + baseX;
        let y = item.coordinates.y * cordFactor + baseY;
        let sprite = new PIXI.Sprite(baseTexture);

        sprite.width = 5;
        sprite.height = 5;
        let container = new Container();
        // Adds a sprite as a child to this container. As a result, the sprite will be rendered whenever the container
        // is rendered.
        container.addChild(sprite);
        container.x = x;
        container.y = y;
        // Blurs whatever is rendered by the container
        // container.filters = [new BlurFilter()];
        // Only the contents within a circle at the center should be rendered onto the screen.

        // container.mask = new Graphics()
        //   .beginFill(0x000fff)
        //   .drawCircle(
        //     sprite.width / 2 + x,
        //     sprite.height / 2 + y,
        //     halfCordFactor
        //   );
        mapContainer.addChild(container);
      });
      app.ticker.add((delta) => {
        // rotate the container!
        // use delta to create frame-independent transform
        mapContainer.scale.x = this.scaleX;
        mapContainer.scale.y = this.scaleY;
        if (this.dragging) {
          mapContainer.x = this.mapX;
          mapContainer.y = this.mapY;
        }
      });

      const brt = new PIXI.BaseRenderTexture(
        300,
        300,
        PIXI.SCALE_MODES.LINEAR,
        1
      );
      const rt = new PIXI.RenderTexture(brt);

      const spr = new PIXI.Sprite(rt);

      app.stage.addChild(spr);
      console.log(mapContainer);
      app.stage.addChild(mapContainer);
    },
  },
};
</script>

<style>
body {
  margin: 0;
  padding: 0;
  position: relative;
}
#app {
}
</style>
