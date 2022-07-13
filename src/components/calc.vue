<template>
  <div class="container ">
    <div class="calc">
      <div class="result" ref="result" style="grid-area: res">
        {{equation}}
      </div>

      <button style="grid-area: ac" @click="clear">AC</button>
      <button style="grid-area: add" @click="append('+')">+</button>
      <button style="grid-area: sub" @click="append('-')">-</button>
      <button style="grid-area: mul" @click="append('x')">x</button>
      <button style="grid-area: div" @click="append('÷')">÷</button>
      <button style="grid-area: equal" @click="calculate">=</button>
      <button style="grid-area: plus-minus" @click="calculateToggle">±</button>
      <button style="grid-area: per" @click="calculatePer">%</button>

      <button style="grid-area: num-1" @click="append(1)">1</button>
      <button style="grid-area: num-2" @click="append(2)">2</button>
      <button style="grid-area: num-3" @click="append(3)">3</button>
      <button style="grid-area: num-4" @click="append(4)">4</button>
      <button style="grid-area: num-5" @click="append(5)">5</button>
      <button style="grid-area: num-6" @click="append(6)">6</button>
      <button style="grid-area: num-7" @click="append(7)">7</button>
      <button style="grid-area: num-8" @click="append(8)">8</button>
      <button style="grid-area: num-9" @click="append(9)">9</button>
      <button style="grid-area: num-0" @click="append(0)">0</button>

      <button  style="grid-area: dot" @click="append('.')">.</button>
    </div>
  </div>
</template>

<script>
  import $ from 'jquery'
  export default {
    name: "calc",
    data(){
      return{
        equation:'0',
        isDecimalAdded:false,
        isOperatorAdded:false,
        isStarted:false
      }
    },
    methods:{
      //判断character是否是加减乘除
      isOperator(character){
        return ['+','-','x','÷'].indexOf(character) > -1
      },
      //点击数字或运算符时
      append(character){
        this.scrollUpdate()
        //  start
        if(this.equation === '0' && !this.isOperator(character)){
          if(character === '.'){
            this.equation += '' + character
            this.isDecimalAdded = true
          }else{
            this.equation = '' + character
          }
          this.isStarted = true
          return
        }

        //if number or '.'
        if(!this.isOperator(character)){
          //之前有过小数点，不能重复输入
          if (character === '.' && this.isDecimalAdded){
            return
          }

          //之前没有小数点，可以输入
          if(character === '.' && !this.isDecimalAdded){
            this.isDecimalAdded = true
            this.isOperatorAdded = true
            this.equation += '' + character
            return
          }else {
            this.isOperatorAdded = false
            this.isDecimalAdded = false
            this.equation += '' + character
          }
        }

        //if operator
        if(this.isOperator(character) && !this.isOperatorAdded){
          this.equation += '' + character
          this.isDecimalAdded = false
          this.isOperatorAdded = true
        }


      },
      //点击等于符号的时候
      calculate(){
        let result = this.equation.replace(new RegExp('x','g'),'*')
          .replace(new RegExp('÷','g'),'/')
        this.equation = parseFloat(eval(result).toFixed(9)).toString()
        this.isDecimalAdded = true
        this.isOperatorAdded = false
        this.scrollUpdate()
      },
      //点击±时
      calculateToggle(){
        if(this.isOperatorAdded || !this.isStarted){
          return
        }
        this.equation = this.equation + 'x -1'
        this.calculate()
      },
      //点击%时
      calculatePer(){
        if(this.isOperatorAdded || !this.isStarted){
          return
        }
        this.equation = this.equation + 'x 0.01'
        this.calculate()
      },
      //点击AC时
      clear(){
        this.equation = '0',
        this.isDecimalAdded = false,
        this.isOperatorAdded = false,
        this.isStarted = false
      },
      //将溢出部分的滚动条移动到最右部
      scrollUpdate(){
        // console.log(this.$refs.result.scrollWidth);
        this.$refs.result.scrollLeft = this.$refs.result.scrollWidth
        // console.log(this.$refs.result.scrollLeft);
        // $('.result').scrollLeft($('.result')[0].scrollWidth)
      }
    }
  }
</script>

<style scoped>
  .container{
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background-color: #eeeeee;
  }

  .calc{
    --button-width:80px;
    --button-height:80px;
    display: grid;
    grid-template-areas:
            "res res res res"
            "ac plus-minus per div"
            "num-7 num-8 num-9 mul"
            "num-4 num-5 num-6 sub"
            "num-1 num-2 num-3 add"
            "num-0 num-0 dot equal";
    grid-template-columns: repeat(4,var(--button-width));
    grid-template-rows: repeat(6,var(--button-height));

    box-shadow: -8px -8px 16px -10px rgba(255,255,255,1),
                8px 8px 16px -10px rgba(0,0,0,.15);
    padding: 24px;
    border-radius: 20px;
  }

  .calc button{
    margin: 8px;
    padding: 0;
    border: 0;
    display: block;
    outline: none;
    border-radius: calc(var(--button-width)/2);
    font-size: 24px;
    font-weight: normal;
    color: #999999;
    background: linear-gradient(135deg,rgba(230,230,230,1) 0%,
    rgba(246,246,246,1) 100%);
    box-shadow: -4px -4px 10px -8px rgba(255,255,255,1),
                4px 4px 10px -8px rgba(0,0,0,.3);
  }

  .calc button:active{
    box-shadow: -4px -4px 10px -8px rgba(255,255,255,1),
    4px 4px 10px -8px rgba(0,0,0,.3) inset;
  }

  .result{
    text-align: right;
    line-height: var(--button-height);
    font-size: 48px;
    /*margin: 0 10px;*/
    /*30px是为了将最后的数字显示出来、需要与滚动条的display配合使用*/
    padding: 0 30px 0 0;
    color: #666666;
    overflow-x: auto;
    overflow-y:hidden;
    white-space: nowrap;
  }

  ::-webkit-scrollbar
  {
    /*因为有滚动条无法固定到最右侧的bug，无奈设置成不显示*/
    display: none;
    width:6px;
    height:6px;
    background-color:#F5F5F5;
  }
  /*定义滚动条轨道
   内阴影+圆角*/
  ::-webkit-scrollbar-track
  {
    -webkit-box-shadow:inset 0 0 3px rgba(0,0,0,0.3);
    border-radius:3px;
    background-color:#F5F5F5;
  }
  /*定义滑块
   内阴影+圆角*/
  ::-webkit-scrollbar-thumb
  {
    border-radius:3px;
    -webkit-box-shadow:inset 0 0 3px rgba(0,0,0,.3);
    background-color:#555;
  }
</style>