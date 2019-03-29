<template>
  <div
    class="test"
    ref="testDivDom"
    style=" position: relative;left: 100px;top: 100px;border:1px dashed black;width: 500px;height: 500px;overflow: hidden"
  >
    <div
      class="div_img"
      @mousedown="dragImg"
      ref="dragImgDom"
      @mousewheel="fnWheel"
      style="width: 1000px;top: 100px;height: 1000px;position: absolute;left: 100px;"
    ></div>
  </div>
</template>

<script>
export default {
  name: "helloWorld",

  data() {
    return {
      mouseLeft: 0,
      mouseTop: 0,
      curX: 0,
      curY: 0,
      dragFlag: false,
      wheelFlag: null,
      oldWidth: 0,
      oldHeight: 0,
      scaleX: 1,
      scaleY: 1,
      newWidth: 0,
      newHeight: 0,
      x: 0,
      y: 0,
      bgX: 0,
      bgY: 0,
      ww: null,
      wh: null,
      imgw: null,
      imgh: null,
      scaleSize: null,
      testDivDom: null,
      dragImgDom: null,
      w: null,
      h: null,
      i: null
    }
  },

  mounted() {
    this.testDivDom = this.$refs.testDivDom;
    this.dragImgDom = this.$refs.dragImgDom;
    this.ww = parseInt(this.testDivDom.style.width);
    this.wh = parseInt(this.testDivDom.style.height);
    this.imgw = parseInt(this.dragImgDom.style.width);
    this.imgh = parseInt(this.dragImgDom.style.height);
    if (this.ww / this.wh < this.imgw / this.imgh) {
      this.w = this.ww;
      this.h = (this.imgh * this.ww) / this.imgw;
      this.bgX = 0;
      this.bgY = -(this.h - this.wh) / 2;
      this.scaleSize = this.ww / this.imgw;
    } else {
      this.w = (this.imgw * this.wh) / this.imgh;
      this.h = this.wh;
      this.bgX = -(this.w - this.ww) / 2;
      this.bgY = 100;
      this.scaleSize = this.wh / this.imgh;
    }
    this.dragImgDom.style.width = this.w + "px";
    this.dragImgDom.style.height = this.h + "px";
    this.dragImgDom.style.left = this.bgX + "px";
    this.dragImgDom.style.top = this.bgY + "px";
  },

  methods: {
    getOffset(o) {
      var left = 0;
      var top = 0;
      while (o != null && o !== document.body) {
        top += o.offsetTop;
        left += o.offsetLeft;
        o = o.offsetParent;
      }
      return {
        left: left,
        top: top
      };
    },
    dragImg(e) {
      this.dragFlag = true;
      this.mouseLeft = e.clientX - parseInt(this.$refs.dragImgDom.offsetLeft);
      this.mouseTop = e.clientY - parseInt(this.$refs.dragImgDom.offsetTop);
    //   鼠标按下移动时
      document.onmousemove = e => {
        if (this.dragFlag) {
          this.curX = e.clientX - this.mouseLeft;
          this.curY = e.clientY - this.mouseTop;
          this.$refs.dragImgDom.style.left = this.curX + "px";
          this.$refs.dragImgDom.style.top = this.curY + "px";
          console.log(this.curX);
        }
      };
    //   鼠标松开弹起
      document.onmouseup = () => {
        this.dragFlag = false;
      };
    },
    fnWheel(e) {
      // 思路：以鼠标为中心实现滚动的话，那么将会鼠标在背景图片（注意我这里是用背景图片的，不是img的）中滚动的时候的位置比率是不会变的
      if (e.wheelDelta > 0) {
        this.wheelFlag = true;
      } else {
        this.wheelFlag = false;
      }
      this.oldWidth = this.$refs.dragImgDom.offsetWidth;
      this.oldHeight = this.$refs.dragImgDom.offsetHeight;

      this.mouseLeft =
        e.clientX -
        this.$refs.dragImgDom.offsetLeft -
        parseInt(this.$refs.testDivDom.offsetLeft);

      this.mouseTop =
        e.clientY -
        this.$refs.dragImgDom.offsetTop -
        parseInt(this.$refs.testDivDom.offsetTop);

      // 分别计算出scaleX,scaleY的倍数
      this.scaleX = this.mouseLeft / this.oldWidth;
      this.scaleY = this.mouseTop / this.oldHeight;

      if (this.wheelFlag) {
        this.$refs.dragImgDom.style.width =
          this.$refs.dragImgDom.offsetWidth * 1.05 + "px";

        this.$refs.dragImgDom.style.height =
          this.$refs.dragImgDom.offsetHeight * 1.05 + "px";

        if (parseInt(this.$refs.dragImgDom.style.width) > 2500) {
          this.$refs.dragImgDom.style.width = "2500px";
        }

        if (parseInt(this.$refs.dragImgDom.style.height) > 1500) {
          this.$refs.dragImgDom.style.height = "1500px";
        }
      } else {
        this.$refs.dragImgDom.style.width =
          this.$refs.dragImgDom.offsetWidth * 0.95 + "px";

        this.$refs.dragImgDom.style.height =
          this.$refs.dragImgDom.offsetHeight * 0.95 + "px";

        if (parseInt(this.$refs.dragImgDom.style.width) < 70) {
          this.$refs.dragImgDom.style.width = "70px";
        }

        if (parseInt(this.$refs.dragImgDom.style.height) < 70) {
          this.$refs.dragImgDom.style.height = "70px";
        }
      }

      // 鼠标为中心的开始点,如果去掉这个将会以左上角开始滚动图片

      this.newWidth = this.$refs.dragImgDom.offsetWidth;
      this.newHeight = this.$refs.dragImgDom.offsetHeight;

      this.$refs.dragImgDom.style.left =
        this.$refs.dragImgDom.offsetLeft -
        this.scaleX * (this.newWidth - this.oldWidth) +
        "px";

      this.$refs.dragImgDom.style.top =
        this.$refs.dragImgDom.offsetTop -
        this.scaleY * (this.newHeight - this.oldHeight) +
        "px";
    }
  }
};
</script>

<style scoped>
.div_img {
  background: url("../../assets/SBCB.png") center/100% 100%;
}
</style>
