<template>
    <div>
        <div :style="styles" @touchstart.stop="adjustStart($event)" @touchmove.stop="adjustIng" @touchend.stop="adjustEnd"></div>
    </div>
</template>

<script>

    export default {
        data(){
            return{
                isScale: false,
                isDrag: false,
                styles: '',
                actived: 1,
                value1: '',
                limitHourValue: '',
                hourList: ['11', '12', '13', '14', '17', '18', '19', '20', '21'],
                // 手指A
                fingerA: {
                    startX: 0,
                    startY: 0,
                    endX: 0,
                    endY: 0
                },
                // 手指B
                fingerB: {
                    startX: 0,
                    startY: 0,
                    endX: 0,
                    endY: 0
                },
                // 调整数据
                move: {
                    x: 0,
                    y: 0,
                    temX: 0,
                    temY: 0,
                    scale: 1,
                    temScale: 1,
                    allDeg: 0,
                    temDeg: 0
                },
                // 默认样式
                imgPosition: {
                    left: 0,
                    top: 0,
                    width: 0,
                    height: 0
                }
            }
        },
        created(){
            // 键盘放大缩小
            let that = this;
            document.onkeydown=function(e){
                e = event || window.event
                if(e.keyCode==38){
                    that.show(1.5);
                }else if(e.keyCode==40){
                    that.show(1);
                }else if(e.keyCode==27){
                    that.show(1);
                }
            }
        },
        methods:{
            zoomPage(e){
                  e = e || window.event;  
                if (e.wheelDelta) {  //判断浏览器IE，谷歌滑轮事件               
                    if (e.wheelDelta > 0) { //当滑轮向上滚动时  
                        this.styles = "transform:scale(1.5)";
                    }  
                    if (e.wheelDelta < 0) { //当滑轮向下滚动时  
                        this.styles = "transform:scale(1)";
                    }  
                } 
            },
            show:function(num){
                this.styles = "transform:scale(" + num + ")";
            },
            adjustStart1 (e) {
                e.preventDefault()
                let event = e.touches
                if (event.length === 2) {
                    this.styles = 'transform: scale(2)'
                }
            },
            // 调整开始
            adjustStart (e) {
                let event = e.touches
                this.fingerA.startX = event[0].pageX
                this.fingerA.startY = event[0].pageY
                // 移动
                if (event.length === 1) {
                    this.isDrag = true
                    this.isScale = false
                    // 缩放
                } else if (event.length === 2) {
                    this.isScale = true
                    this.isDrag = false
                    this.fingerB.startX = event[1].pageX
                    this.fingerB.startY = event[1].pageY
                }
            },
            // 调整中，移动或缩放
            adjustIng (e) {
                let event = e.touches
                this.fingerA.endX = event[0].pageX
                this.fingerA.endY = event[0].pageY
                // 移动
                if (this.isDrag) {
                    // 本次移动距离要加上之前移动的距离
                    this.move.x = this.fingerA.endX - this.fingerA.startX + this.move.temX
                    this.move.y = this.fingerA.endY - this.fingerA.startY + this.move.temY
                    this.styles = `transform: translate(${this.move.x}px,${this.move.y}px) scale(${this.move.scale})`
                } else if (this.isScale) {
                    // 缩放
                    this.fingerB.endX = event[1].pageX
                    this.fingerB.endY = event[1].pageY
                    // 两手指间距离
                    let distanceStart = Math.sqrt(
                    Math.pow(this.fingerA.startX - this.fingerB.startX, 2) + Math.pow(this.fingerA.startY - this.fingerB.startY, 2)
                    )
                    let distanceEnd = Math.sqrt(
                    Math.pow(this.fingerA.endX - this.fingerB.endX, 2) + Math.pow(this.fingerA.endY - this.fingerB.endY, 2)
                    )
                    this.move.scale = (distanceEnd / distanceStart) * this.move.temScale
                    // 向量叉乘，求出旋转方向及角度
                    // 开始两个手指的向量
                    var vector1 = new this.Vector(
                    this.fingerA.startX,
                    this.fingerA.startY,
                    this.fingerB.startX,
                    this.fingerB.startY
                    )
                    // 结束时两个手指的向量
                    var vector2 = new this.Vector(
                    this.fingerA.endX,
                    this.fingerA.endY,
                    this.fingerB.endX,
                    this.fingerB.endY
                    )
                    var cos = this.calculateVM(vector1, vector2)
                    var angle = (Math.acos(cos) * 180) / Math.PI
                    var direction = this.calculateVC(vector1, vector2)
                    this.move.allDeg = direction * angle + this.move.temDeg
                    this.styles = `transform: translate(${this.move.x}px,${this.move.y}px) scale(${this.move.scale})`
                }
            },
            // 调整结束
            adjustEnd (e) {
                this.move.temX = this.move.x
                this.move.temY = this.move.y
                this.move.temScale = this.move.scale
                this.move.temDeg = this.move.allDeg
                this.isDrag = false
                this.isScale = false
            },
        }
    }
</script>

<style lang="less" scoped>
.center-contant{
    height: 70vh;
    overflow: hidden;
}
</style>