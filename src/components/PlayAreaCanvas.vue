<template>
  <div class="display-block">
    <canvas id="play-area"></canvas>
  </div>
</template>

<script>
import { tween } from "femtotween";
import { SSCD } from "sscd";

export default {
  name: "PlayAreaCanvas",
  data: () => {
    return {
      cx: 100,
      cy: 75,
      mx: 150,
      my: 125,
      projectiles: []
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

      let headingLen = 2000;
      let headingRatio = headingLen / hLen;
      let headingXLen = xLen * headingRatio;
      let headingYLen = yLen * headingRatio;
      let headingX = this.cx + headingXLen;
      let headingY = this.cy + headingYLen;

      return {
        x: turretX,
        y: turretY,
        hx: headingX,
        hy: headingY
      };
    }
  },
  methods: {
    draw(ctx) {
      ctx.beginPath();
      ctx.arc(this.cx, this.cy, 20, 0, 2 * Math.PI);
      ctx.moveTo(this.cx, this.cy);
      ctx.lineTo(this.turret.x, this.turret.y);
      this.projectiles.forEach(e => {
        ctx.fillRect(e.x, e.y, 2, 2);
      });
      ctx.stroke();
    },
    gameLoop() {
      const self = this;

      let canvas = document.getElementById("play-area");
      canvas.width = window.innerWidth - 100;
      canvas.height = window.innerHeight - 100;

      let cw = canvas.width;
      let ch = canvas.height;
      let ctx = canvas.getContext("2d");
      let fps = 60;
      let interval = 1000 / fps;
      let lastTime = new Date().getTime();
      let currentTime = 0;
      let delta = 0;

      const gameLoop = () => {
        window.requestAnimationFrame(gameLoop);

        currentTime = new Date().getTime();
        delta = currentTime - lastTime;

        if (delta > interval) {
          ctx.clearRect(0, 0, cw, cw);
          self.projectiles.forEach( async e => {
            let vector = await self.updateProjectiles(e.x,e.y,e.target_x,e.target_y)
            e.x = vector.x
            e.y = vector.y
          })
          self.draw(ctx);
          lastTime = currentTime - (delta % interval);
        }
      };

      gameLoop();
    },
    getMousePos(e, canvas) {
      let rect = canvas.getBoundingClientRect();
      let scaleX = canvas.width / rect.width;
      let scaleY = canvas.height / rect.height;

      this.mx = (e.clientX - rect.left) * scaleX;
      this.my = (e.clientY - rect.top) * scaleY;
    },
    shoot(e) {
      let projectile = {
        start_time: null,
        x: null,
        y: null,
        target_x: null,
        target_y: null
      };
      projectile.start_time = Date.now();
      projectile.x = this.turret.x;
      projectile.y = this.turret.y;
      projectile.target_x = this.mx + 1000;
      projectile.target_y = this.my + 1000;

      console.log(projectile);

      this.projectiles.push(projectile);
    },
    updateProjectiles(curr_x, curr_y, t_x, t_y) {
      let x_diff = t_x - curr_x;
      let y_diff = t_y - curr_y;

      let vector_length = Math.sqrt(x_diff*x_diff + y_diff*y_diff)
      let normalised_vector_x = x_diff / vector_length
      let normalised_vector_y = y_diff / vector_length

      let normalised_vector = {
        x: normalised_vector_x,
        y: normalised_vector_y
      }

      return normalised_vector
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
    // setInterval(() => {
    //   this.updateProjectiles();
    // }, 3000);
    let canvas = document.getElementById("play-area");
    canvas.addEventListener("mousemove", e => {
      this.getMousePos(e, canvas);
    });
    this.gameLoop();
    canvas.addEventListener("mousedown", e => {
      this.shoot(e);
    });
    window.addEventListener("keydown", e => {
      this.move(e);
    });
  }
};
</script>

<style scoped>
.display-block {
  width: 100%;
  height: 100vh;
  position: relative;
}
canvas {
  position: absolute;
  top: 0;
  bottom: 0;
  right: 0;
  left: 0;
  margin: auto;
  background-color: tomato;
}
</style>
