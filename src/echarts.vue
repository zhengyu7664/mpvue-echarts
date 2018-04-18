<template>
  <canvas
    v-if="canvasId"
    class="ec-canvas"
    :canvasId="canvasId"
    @touchstart="touchStart"
    @touchmove="touchMove"
    @touchend="touchEnd"
    @error="error">
  </canvas>
</template>

<script>
import WxCanvas from './wx-canvas';

let chart;
let ctx;

export default {
  props: {
    echarts: {
      required: true,
      type: Object,
      default() {
        return null;
      },
    },
    onInit: {
      required: true,
      type: Function,
      default: null,
    },
    canvasId: {
      type: String,
      default: 'ec-canvas',
    },
    lazyLoad: {
      type: Boolean,
      default: false,
    },
    disableTouch: {
      type: Boolean,
      default: false,
    },
  },
  onReady() {
    if (!this.echarts) {
      console.warn('组件需绑定 echarts 变量，例：<ec-canvas id="mychart-dom-bar" '
        + 'canvas-id="mychart-bar" :echarts="echarts"></ec-canvas>');
      return;
    }

    if (!this.lazyLoad) this.init();
  },
  methods: {
    init() {
      const version = wx.version.version.split('.').map(n => parseInt(n, 10));
      const isValid = version[0] > 1 || (version[0] === 1 && version[1] > 9)
        || (version[0] === 1 && version[1] === 9 && version[2] >= 91);
      if (!isValid) {
        console.error('微信基础库版本过低，需大于等于 1.9.91。'
          + '参见：https://github.com/ecomfe/echarts-for-weixin'
          + '#%E5%BE%AE%E4%BF%A1%E7%89%88%E6%9C%AC%E8%A6%81%E6%B1%82');
        return;
      }

      if (!this.onInit) {
        console.warn('请传入 onInit 函数进行初始化');
        return;
      }

      const { canvasId } = this;
      ctx = wx.createCanvasContext(canvasId);

      const canvas = new WxCanvas(ctx, canvasId);

      this.echarts.setCanvasCreator(() => canvas);

      const query = wx.createSelectorQuery();
      query.select('.ec-canvas').boundingClientRect((res) => {
        if (!res) {
          setTimeout(() => this.init(), 50);
          return;
        }
        chart = this.onInit(canvas, res.width, res.height);
      }).exec();
    },
    canvasToTempFilePath(opt) {
      const { canvasId } = this;

      ctx.draw(true, () => {
        wx.canvasToTempFilePath({
          canvasId,
          ...opt,
        });
      });
    },
    touchStart(e) {
      if (!this.disableTouch && chart && e.mp.touches.length > 0) {
        const touch = e.mp.touches[0];
        chart._zr.handler.dispatch('mousedown', {
          zrX: touch.x,
          zrY: touch.y,
        });
        chart._zr.handler.dispatch('mousemove', {
          zrX: touch.x,
          zrY: touch.y,
        });
      }
    },
    touchMove(e) {
      if (!this.disableTouch && chart && e.mp.touches.length > 0) {
        const touch = e.mp.touches[0];
        chart._zr.handler.dispatch('mousemove', {
          zrX: touch.x,
          zrY: touch.y,
        });
      }
    },
    touchEnd(e) {
      if (!this.disableTouch && chart) {
        const touch = e.changedTouches ? e.changedTouches[0] : {};
        chart._zr.handler.dispatch('mouseup', {
          zrX: touch.x,
          zrY: touch.y,
        });
        chart._zr.handler.dispatch('click', {
          zrX: touch.x,
          zrY: touch.y,
        });
      }
    },
  },
};
</script>

<style scoped>
.ec-canvas {
  width: 100%;
  height: 100%;
}
</style>
