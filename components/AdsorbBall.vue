<template>
  <div
    class="drag-ball"
    ref="adsorbBall"
    @touchstart.stop.prevent="touchstart"
    @touchmove.stop.prevent="touchmove"
    @touchend.stop.prevent="touchend"
    @click="click"
  >
    <div class="drag-content" ref="content">
      <slot name="content">{{ innerContent }}</slot>
    </div>
  </div>
</template>
<script>
export default {
  props: {
    innerPadding: {
      type: Number,
      default: 20
    },
    dragContentBackgroundColor: {
      type: String,
      default: "white"
    },
    innerContent: {
      type: String,
      default: "侧吸小球"
    }
  },
  data() {
    return {
      canDrag: false,
      dragContentWidth: 50,
      dragContentHeight: 50,
      inset: {
        left: 0,
        top: 0
      },
      move: {},
      position: {
        left: 0,
        top: 0
      },
      positionOld: {},
      startTime: null,
      endTime: null
    };
  },
  mounted() {
    this.$nextTick(() => {
      let c = this.$refs.content;
      this.dragContentWidth = c.clientWidth + this.innerPadding + "px";
      this.dragContentHeight = c.clientHeight + this.innerPadding + "px";
      this.adsorbBall.style.width = this.dragContentWidth;
      this.adsorbBall.style.height = this.dragContentHeight;
      this.adsorbBall.style.backgroundColor = this.dragContentBackgroundColor;
    });
  },
  methods: {
    click() {
      this.$emit('ballDidClick');
    },
    touchstart(e) {
      if (!this.canDrag) {
        this.startTime = e.timeStamp;
        this.positionOld = this.getPosition(this.adsorbBall);
        this.position = { ...this.positionOld };
        this.inset = {
          left: e.targetTouches[0].clientX - this.positionOld.left,
          top: e.targetTouches[0].clientY - this.positionOld.top
        };
        this.adsorbBall.style.borderRadius = "50%";
        this.canDrag = true;
      }
    },
    touchmove(e) {
      if (this.canDrag) {
        let left = e.targetTouches[0].clientX - this.inset.left;
        let top = e.targetTouches[0].clientY - this.inset.top;
        if (left < 0) {
          left = 0;
        } else if (left > window.innerWidth - this.adsorbBall.offsetWidth) {
          left = window.innerWidth - this.adsorbBall.offsetWidth;
        }
        if (top < 0) {
          top = 0;
        } else if (top > window.innerHeight - this.adsorbBall.offsetHeight) {
          top = window.innerHeight - this.adsorbBall.offsetHeight;
        }
        this.adsorbBall.style.left = left + "px";
        this.adsorbBall.style.top = top + "px";
        this.adsorbBall.style.width = this.dragContentWidth;
        this.adsorbBall.style.borderRaduis = "50%";
        this.adsorbBall.style.overflow = "hidden";
        this.move = {
          x: left - this.positionOld.left,
          y: top - this.positionOld.top
        };
        this.position = { left, top };
      }
    },
    touchend(e) {
      if (this.canDrag) {
        this.endTime = e.timeStamp;
        if ( this.endTime - this.startTime > 400 || Math.abs(this.move.x) > 2 || Math.abs(this.move.y) > 2) {
          if ( this.position.left + this.adsorbBall.offsetWidth / 2 > window.innerWidth / 2) {
            this.adsorbBall.style.left = window.innerWidth - this.adsorbBall.offsetWidth + "px";
            this.adsorbBall.style.borderRadius = "100px 0 0 100px";
          } else {
            this.adsorbBall.style.left = 0 + "px";
            this.adsorbBall.style.borderRadius = "0 100px 100px 0";
          }
        } else {
          this.$emit("click");
        }
        this.inset = {};
        this.move = {};
        this.position = {};
        this.canDrag = false;
      }
    },
    getPosition(source) {
      let left = source.offsetLeft;
      let top = source.offsetTop;
      let current = source.offsetParent;
      while (current != null) {
        left += current.offsetLeft;
        top += current.offsetTop;
        current = current.offsetParent;
      }
      return {
        left: left,
        top: top
      };
    }
  },
  computed: {
    adsorbBall() {
      return this.$refs.adsorbBall;
    }
  }
};
</script>
<style scoped>
.drag-ball {
  position: absolute;
  z-index: 9999;
  right: 0;
  top: 70%;
  width: 50px;
  height: 50px;
  border-radius: 100px 0px 0px 100px;
  overflow: hidden;
  display: flex;
  align-items: center;
  justify-content: center;
  user-select: none;
}
.drag-ball .drag-content {
  overflow-wrap: break-word;
  font-size: 14px;
  color: #fff;
  letter-spacing: 2px;
}
</style>

