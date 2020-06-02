<template>
  <div class="container">
    <div id="game" v-if="isOver"></div>
    <div class="head clear">
        <div class="scoreShow">
            <span class="score">Score：</span>
            <span id="score">{{score}}</span>
        </div>
        <div class="selction" onclick="getModel(event)">
            <a rel="external nofollow" class="model" data-value="3" @click="btn">3X3</a>
            <a rel="external nofollow" class="model" data-value="4" @click="btn">4X4</a>
            <a rel="external nofollow" class="model" data-value="5" @click="btn">5X5</a>
            <a rel="external nofollow" class="model" data-value="6" @click="btn">6X6</a>
            <!-- <input type="text" id="model"> -->
            <!-- <button type="button" name="button" id="set">设置游戏</button> -->
        </div>
    </div>
    <div id="gridPanel"
    @touchstart="start"
    @touchmove="move">
        <div v-for="(row1,row) in boxs" :key="row" class="grid" style="padding-bottom: 6px;"
        :style="{height: num==3? 'calc(100% / 3)':num==4? 'calc(100% / 4)':num==5? 'calc(100% / 5)':'calc(100% / 6)'}">
            <div v-for="(cell1,cell) in row1" :key="cell" 
            style="margin-right: 6px;width: 100%;"
            class="cell">
                <!-- 该部分隐藏，出现数字时格子出现 -->
                <div :id="'c'+row+cell"
                v-if="cell1!=0"
                :class="'n'+cell1"
                style="font-size:60rpx;">
                    {{cell1}}
                </div>
            </div>
        </div>
    </div>
    <div id="gameover" v-if="isOver">
        <div class="kid" v-if="isWin">You win!</div>
        <div class="kid" v-else>GAME OVER!</div>
        <div class="over_score">Score: {{score}}</div>
        <a id="again" @click="gameStart">Try again</a>
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
        isWin:0,//游戏结束，判断是否胜利
        pageX:0,
        pageY:0,
        // moveNum:0,// 滑动方向数字 1上 2右 3下 4左
        agree:0,// 是否同意进行滑动方向确认
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
        this.init();
    },
    //初始化
    init() {
        var arr=[];
        var that=this;
        // 分数清零
        that.score = 0;
        // 重置格子及内容
        that.boxInit()
        // 结算弹窗初始化
        that.isOver=0;
        that.isWin=0;
        // 视图更新
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
    // 随机空白格子内随机数字
    boxRandom(){
        var that=this;
        var arr=that.boxs;
        // 方法改：start
        var blankArr=[]
        for(var r=0;r<that.ROW;r++){
            for(var c=0;c<that.CELL;c++){
                if(arr[r][c]==0){
                    var obj={r,c};
                    blankArr.push(obj);
                }
            }
        }
        var index=Math.floor(Math.random() *blankArr.length)
        var activeObj=blankArr[index]
        arr[activeObj.r][activeObj.c]=(Math.random() > 0.5) ? 4 : 2;
        // end

        /*
        // 递归 影响效率 pass
        var row = Math.floor(Math.random() * that.ROW);
        var cell = Math.floor(Math.random() * that.CELL);

        if (arr[row][cell] == 0) { //判断生成的随机数位置为0即不存在数字时，才随机生成2或4
            arr[row][cell] = (Math.random() > 0.5) ? 4 : 2;
        }else{
            that.boxRandom();
        }
        */
    },
    // 视图更新
    updateView(){
        // 更新视图 判断游戏是否结束
        var that=this;
        var arr=that.boxs;
        // 判断是否 通关
        for(var r=0;r<that.ROW;r++){
            for(var c=0;c<that.cell;c++){
                if (that.ROW == 3 && arr[r][c] == 1024) {
                    that.isOver = 1;
                    that.isWin = 1;
                } else if (that.ROW == 4 && arr[r][c] == 2048) {
                    that.isOver = 1;
                    that.isWin = 1;
                } else if (that.ROW == 5 && arr[r][c] == 4096) {
                    that.isOver = 1;
                    that.isWin = 1;
                } else if (that.ROW == 6 && arr[r][c] == 8192) {
                    that.isOver = 1;
                    that.isWin = 1;
                }
            }
        }
        // 判断是否 游戏失败
        if(that.isGameOver()){
            that.isOver = 1;
            that.isWin = 0;
        }
    },
    // 判断是否游戏失败
    isGameOver(){
        var that=this;
        var arr=that.boxs;
        for (var r = 0; r < that.ROW; r++) {
            for (var c = 0; c < that.CELL; c++) {
                if (arr[r][c] == 0) { //有0还不是gameover
                    return false;
                } else if (c != that.CELL - 1 && arr[r][c] == arr[r][c + 1]) { //从左往右 前一个和下一个不相等
                    return false;
                } else if (r != that.ROW - 1 && arr[r][c] == arr[r + 1][c]) { //从上往下 上一个和下一个不相等
                    return false;
                }
            }
        }
        return true;
    },
    start(e){
        this.pageX=e.pageX;
        this.pageY=e.pageY;
        this.agree=1;// 同意进行滑动
        // console.log("start",e.pageX,e.pageY);
    },
    move(e){
        // 如果agree为1，则根据移动的坐标确认移动的方向，当满足一次方向确认后agree改为0
        if(this.agree){
            var x=e.pageX-this.pageX
            var y=e.pageY-this.pageY
            if(x>40){
                // console.log('右');
                // this.moveNum=2;
                this.moveToRight();
                this.agree=0;
            }else if(x<-40){
                // console.log('左');
                // this.moveNum=4;
                this.moveToLeft();
                this.agree=0;
            }else{// 防止出现两个方向
                if(y>40){
                    // console.log('下');
                    // this.moveNum=3;
                    this.moveToDown();
                    this.agree=0;
                }else if(y<-40){
                    // console.log('上');
                    // this.moveNum=1;
                    this.moveToUp();
                    this.agree=0;
                }
            }
        }
    },
    //查找下一个不为0的数值的位置
    find(r, c, start, condition, direction,moveNum) {
        var that=this;
        var arr=this.boxs;
        if (moveNum == 1) { //向上按键 f++
                for (var f = start; f < condition; f += direction) {
                    if (arr[f][c] != 0) {
                        return f;
                    }
                }
        } else if(moveNum == 3){ //向下按键 f--
            for (var f = start; f >= condition; f += direction) {
                if (arr[f][c] != 0) {
                    return f;
                }
            }
        } else if(moveNum == 4){ //左按键 f++
            for (var f = start; f < condition; f += direction) {
                if (arr[r][f] != 0) {
                    return f;
                }
            }
        } else if(moveNum == 2){ //右按键 f--
            for (var f = start; f >= condition; f += direction) {
                if (arr[r][f] != 0) {
                    return f;
                }
            }
        }
        return null; //循环结束仍然没有找到！=0的数值，返回null
    },
    // 移动的总流程
    movePath(meth){
        var before = this.boxs.toString();//处理前的格子数据
        meth()//执行for循环函数，即移动的处理方法
        var after = this.boxs.toString();//处理后的格子数据
        if (before != after) { //前后对比，如果不同就添加一个随机数
            this.boxRandom();
            this.updateView();
        }
    },
    //左右移的处理
    dealToL_or_R(r,moveNum){
        var next, 
            that=this, 
            arr=this.boxs;
        if(moveNum==4){
            for (var c = 0; c < that.CELL; c++) {
                next = that.find(r, c, c + 1, that.CELL, 1, moveNum); //找出第一个不为0的位置
                if (next == null) break; //没有找到就返回
                //如果当前位置为0
                if (arr[r][c] == 0) {
                    arr[r][c] = arr[r][next]; //把找到的不为0的数值替换为当前位置的值
                    arr[r][next] = 0; //找到的位置清0
                    c--; //再次循环多一次，查看后面否有值与替换后的值相同，
                } else if (arr[r][c] == arr[r][next]) { //如果当前位置与找到的位置数值相等，则相加
                    arr[r][c] *= 2;
                    arr[r][next] = 0;
                    that.score += arr[r][c];
                }
            }
        }else if(moveNum==2){
            for (var c = that.CELL - 1; c >= 0; c--) {
                next = that.find(r, c, c - 1, 0, -1, moveNum); //找出第一个不为0的位置
                if (next == null) break; //没有找到就返回
                //如果当前位置为0
                if (arr[r][c] == 0) {
                    arr[r][c] = arr[r][next]; //把找到的不为0的数值替换为当前位置的值
                    arr[r][next] = 0; //找到的位置清0
                    c++; //再次循环多一次，查看后面否有值与替换后的值相同，
                } else if (arr[r][c] == arr[r][next]) { //如果当前位置与找到的位置数值相等，则相加
                    arr[r][c] *= 2;
                    arr[r][next] = 0;
                    that.score += arr[r][c];
                }
            }  
        }
    },
    //上下移的处理
    dealToU_or_D(c,moveNum){
        var next, 
            that=this, 
            arr=this.boxs;
        if(moveNum==1){
            for (var r = 0; r < that.ROW; r++) {
                next = that.find(r, c, r + 1, that.ROW, 1, moveNum); //找出第一个不为0的位置
                if (next == null) break;
                //如果当前位置为0
                if (arr[r][c] == 0) {
                    arr[r][c] = arr[next][c]; //把找到的不为0的数值替换为当前位置的值
                    arr[next][c] = 0; //找到的位置清0
                    r--; //再次循环多一次，查看后面否有值与替换后的值相同
                } else if (arr[r][c] == arr[next][c]) { //如果当前位置与找到的位置数值相等，则相加
                    arr[r][c] *= 2;
                    arr[next][c] = 0;
                    that.score += arr[r][c];
                }
            }
        }else if(moveNum==3){
            for (var r = that.ROW - 1; r >= 0; r--) {
                next = that.find(r, c, r - 1, 0, -1, moveNum); //找出第一个不为0的位置
                if (next == null) break;
                //如果当前位置为0
                if (arr[r][c] == 0) {
                    arr[r][c] = arr[next][c]; //把找到的不为0的数值替换为当前位置的值
                    arr[next][c] = 0; //找到的位置清0
                    r++; //再次循环多一次，查看后面否有值与替换后的值相同
                } else if (arr[r][c] == arr[next][c]) { //如果当前位置与找到的位置数值相等，则相加
                    arr[r][c] *= 2;
                    arr[next][c] = 0;
                    that.score += arr[r][c];
                }
            }  
        }
    },
    // 左移事件
    moveToLeft(){
        var that=this;
        this.movePath(()=>{
            for (var r = 0; r < that.ROW; r++) {
                // 左移处理函数
                that.dealToL_or_R(r,4)
            }
        })
    },
    // 右移事件
    moveToRight(){
        var that=this;
        this.movePath(()=>{
            for (var r = 0; r < that.ROW; r++) {
                // 右移处理函数
                that.dealToL_or_R(r,2)
            }
        })
    },
    // 上移事件
    moveToUp(){
        var that=this;
        this.movePath(()=>{
            for (var c = 0; c < that.CELL; c++) {
                // 上移处理函数
                that.dealToU_or_D(c,1)
            }
        })
    },
    // 下移事件
    moveToDown(){
        var that=this;
        this.movePath(()=>{
            for (var c = 0; c < that.CELL; c++) {
                // 下移处理函数
                that.dealToU_or_D(c,3)
            }
        })
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
    z-index: 4;
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
    color: #fff;
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
#gameover>.kid,
#gameover>.over_score{
    font-size: 25px;
    font-weight: bold;
}
#gameover>.kid{
    padding: 10% 5% 2%;

}
#gameover>.over_score{
    padding: 0 5% 12%;
}
#gameover>a {
    display: inline-block;
    width: 150px;
    height: 40px;
    border-radius: 10px;
    text-decoration: none;
    background-color: #9F8D77;
    color: white;
    font-size: 28px;
}
</style>
