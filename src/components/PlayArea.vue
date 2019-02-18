<template>
  <div class="three">
    <div>
      <svg viewBox="0 0 1000 1000" id="svg">
        <circle :cx="cx" :cy="cy" r="20" stroke="white" stroke-width="3" id="circle"></circle>
        <line
          :x1="cx"
          :y1="cy"
          :x2="turret.x"
          :y2="turret.y"
          pathLength="40"
          style="stroke:rgb(255,0,0);stroke-width:2"
        ></line>
      </svg>
    </div>
  </div>
</template>

<script>
import { tween } from "femtotween";
import { SSCD } from "sscd";

export default {
  name: "PlayArea",
  data: () => {
    return {
      cx: 500,
      cy: 500,
      mx: 550,
      my: 550
    };
  },
  computed: {
    turret: function() {
      let xLen = this.mx - this.cx;
      let yLen = this.my - this.cy;
      let hLen = Math.sqrt(Math.pow(xLen, 2) + Math.pow(yLen, 2));
      let segLen = 25;
      let ratio = segLen / hLen;
      let segXLen = xLen * ratio;
      let segYLen = yLen * ratio;
      let turretX = this.cx + segXLen;
      let turretY = this.cy + segYLen;

      return {
        x: turretX,
        y: turretY
      };
    }
  },
  methods: {
    getMousePos(e) {
      let pt = svg.createSVGPoint();

      pt.x = e.clientX;
      pt.y = e.clientY;
      let points = pt.matrixTransform(svg.getScreenCTM().inverse());

      this.mx = points.x;
      this.my = points.y;
    },
    shoot(e) {
      let svg = document.querySelector("svg");
      let rect = document.createElementNS("http://www.w3.org/2000/svg", "rect");
      rect.setAttribute("x", this.cx);
      rect.setAttribute("y", this.cy);
      rect.setAttribute("width", 50);
      rect.setAttribute("height", 50);
      rect.setAttribute(
        "style",
        "fill:blue;stroke:pink;stroke-width:5;opacity:0.5"
      );
      svg.appendChild(rect);
      
    },
    move(e) {
      let speed = 3;
      switch (e.keyCode) {
        case 87:
          tween(
            this.cy,
            this.cy + speed,
            v => {
              this.cy--;
            },
            {
              time: 500
            }
          );
          break;
        case 83:
          tween(
            this.cy,
            this.cy - speed,
            v => {
              this.cy++;
            },
            {
              time: 500
            }
          );
          break;
        case 65:
          tween(
            this.cx,
            this.cx + speed,
            v => {
              this.cx--;
            },
            {
              time: 500
            }
          );
          break;
        case 68:
          tween(
            this.cx,
            this.cx - speed,
            v => {
              this.cx++;
            },
            {
              time: 500
            }
          );
          break;

        default:
          break;
      }
    }
  },
  watch: {},
  mounted() {
    window.addEventListener("mousedown", e => {
      console.log(e);
      this.shoot(e)
    });
    window.addEventListener("keydown", e => {
      this.move(e);
    });
    let svg = document.querySelector("svg");
    svg.addEventListener("mousemove", e => {
      this.getMousePos(e);
    });
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
svg {
  position: fixed;
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
}
.three {
  margin-top: 10px;
  width: 100%;
}
</style>
