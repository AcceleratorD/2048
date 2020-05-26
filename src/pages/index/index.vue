<template>
  <div class="container">
    <div id="game" v-if="isOver"></div>
    <div class="head clear">
        <div class="scoreShow">
            <span class="score">Score：</span>
            <span id="score">0</span>
        </div>
        <div class="selction" onclick="getModel(event)">
            <a rel="external nofollow" class="model" data-value="3" @click="btn">3X3</a>
            <a rel="external nofollow" class="model" data-value="4" @click="btn">4X4</a>
            <a rel="external nofollow" class="model" data-value="5" @click="btn">5X5</a>
            <a rel="external nofollow" class="model" data-value="6" @click="btn">6X6</a>
        </div>
    </div>
    <div id="gridPanel">
        <div v-for="(row1,row) in boxs" :key="row" class="grid" style="padding-bottom: 6px;"
        :style="{height: num==3? 'calc(100% / 3)':num==4? 'calc(100% / 4)':num==5? 'calc(100% / 5)':'calc(100% / 6)'}">
            <div v-for="(cell1,cell) in row1" :key="cell" 
            style="margin-right: 6px;width: 100%;"
            class="cell">
                <!-- 该部分隐藏，出现随机对应的格子 -->
                <div :id="'c'+row+cell"
                v-if="cell1!=0"
                :class="'n'+cell1"
                style="font-size:60rpx;">
                <!-- 'c'+row+cell==sid -->
                    {{cell1}}
                </div>
            </div>
        </div>
    </div>
    <div id="gameover" v-if="isOver">
        game over 结算面板
    </div>
  </div>
</template>

<script>
import index1 from '../../utils/index'
export default {
  data () {
    return {
        num: 4,//3*3 4*4 5*5 6*6选项 3/4/5/6
        boxs:[
            [0,0,0,0],
            [0,0,0,0],
            [0,0,0,0],
        ],
        ROW:4,
        CELL:4,
        r: 0,//行
        c: 0,//列
        f: 0, //r行 c列 f查找的下一位置
        keyCd: 0,
        score: 0,//分数
        createEle: 0, //是否需要创建元素
        isOver:0,//游戏是否结束，结算框是否显示
    }
  },
  computed:{

  },
  mounted () {
    // var now=new Date();
    // console.log(now,'123')
    // console.log(index1.formatTime(now),'111')
    this.gameStart();
  },
  methods:{
    //切换选项3*3 4*4 5*5 6*6
    btn(even){
        var that=this;
        var value=parseInt(even.currentTarget.dataset.value);
        if(that.num!=value){
            that.num=that.ROW=that.CELL=value;
            that.boxInit()
        }
    },
    //游戏开始
    gameStart() {
        var that=this;
        that.init();
    },
    //初始化
    init() {
        var arr=[];
        var that=this;
        // 分数清零
        that.score = 0;
        // 重置格子及内容
        that.boxInit()

        that.isOver=0


        that.updateView();
    },
    // 格子初始化
    boxInit(){
        var that=this;
        var boxs=[];
        for(var r=0;r<that.ROW;r++){
            var arr=[];
            for(var c=0;c<that.CELL;c++){
                arr.push(0)
            }
            boxs.push(arr);
        }
        that.boxs=boxs;
        // 开始游戏随机生成两个数
        that.boxRandom()
        that.boxRandom()
    },
    // 格子内数字随机
    boxRandom(){
        var that=this;
        var row = Math.floor(Math.random() * that.ROW);
        var cell = Math.floor(Math.random() * that.CELL);
        var arr=that.boxs;
        if (arr[row][cell] == 0) { //判断生成的随机数位置为0即不存在数字时，才随机生成2或4
            arr[row][cell] = (Math.random() > 0.5) ? 4 : 2;
        }else{
            that.boxRandom();
        }
    },
    // 视图更新
    updateView(){
        // 更新视图 判断游戏是否结束
        console.log('updateView',this.isOver)
    },
  },
  
}
</script>

<style scoped>
#game {
    position: absolute;
    left: 0px;
    top: 0px;
    right: 0px;
    bottom: 0px;
    background-color: #9DA5C3;
    opacity: 0.5;
    z-index: 1;
}

.head {
    width: 95%;
    margin: 0 auto;
    font-size: 23px;
}

.clear:after {
    content: "";
    display: table;
    clear: both;
}
.scoreShow {
    color: #bbada0;
    text-align: left;
}

.scoreShow>.score{
  color: #eee4da;
}

.selction{
  display: flex;
  justify-content: space-between;
  margin: 10rpx 0 15rpx;
}

.model {
    text-decoration: none;
    color: white;
    background-color: #bbada0;
    font-size: 20px;
    padding: 8rpx 25rpx;
    border-radius: 10px;
}

#gridPanel {
    width: 360px;
    height: 360px;
    margin: 0 auto;
    background-color: #bbada0;
    border-radius: 10px;
    position: relative;
    z-index: 1;
    box-sizing: border-box;
}
.grid{
    display: flex;
    box-sizing: border-box;
}
#gridPanel>.grid:first-child{
    padding-top: 6px;
}
.cell{
    background-color: #ccc0b3;
    border-radius: 6px;
    overflow: hidden;
}
.grid>.cell:first-child{
    margin-left: 6px;
}
.cell>div{
    width: 100%;
    height: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
}
/* .grid,
.cell {
    width: 100px;
    height: 100px;
    border-radius: 6px;
}

.grid {
    background-color: #ccc0b3;
    float: left;
    margin: 16px 0 0 16px;
}

.cell {
    position: absolute;
    font-size: 60px;
    text-align: center;
    line-height: 100px;
    color: #fff;
} */

.n2 {
    background-color: #eee3da
}

.n4 {
    background-color: #ede0c8
}

.n8 {
    background-color: #f2b179
}

.n16 {
    background-color: #f59563
}

.n32 {
    background-color: #f67c5f
}

.n64 {
    background-color: #f65e3b
}

.n128 {
    background-color: #edcf72
}

.n256 {
    background-color: #edcc61
}

.n512 {
    background-color: #9c0
}

.n1024 {
    background-color: #33b5e5
}

.n2048 {
    background-color: #09c
}

.n4096 {
    background-color: #a6c
}

.n8192 {
    background-color: #93c
}

.n2,
.n4 {
    color: #776e65
}

#gameover {
    /* width: 60%; */
    /* display: none; */
    position: fixed;
    left: 0;
    right: 0;
    margin: 0 auto;
    left: 50%;
    right: 50%;
    top: 148px;
    width: 220px;
    height: 200px;
    border-radius: 10px;
    background-color: white;
    margin-left: -110px;
    text-align: center;
    z-index: 5;
}

#gameover>a {
    display: inline-block;
    width: 170px;
    height: 50px;
    border-radius: 10px;
    text-decoration: none;
    background-color: #9F8D77;
    color: white;
    font-size: 36px;
}
</style>
