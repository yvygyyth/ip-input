<template>
  <div>输入框</div>
  <a-input 
  @keydown="onkeydown"
  @input="onInput"
  id="ipInput"
  class="ipInput" 
  v-model:value="value"
  autofocus
  ref="myInput"
  placeholder="Lazy usage" 
  />
  <a-alert v-show="showParagraph==1" message="Info Text" type="info" />
  <a-alert v-show="showParagraph==2" message="Success Text" type="success" />
  <a-alert v-show="showParagraph==3" message="Warning Text" type="warning" />
  <a-alert v-show="showParagraph==4" message="Error Text" type="error" />
</template>

<script setup>
import { watch,ref,onMounted } from 'vue'
const value = ref('...')
const myInput = ref(null)

let params = /^((25[0-5]|2[0-4]\d|((1\d{2})|([1-9]?\d)))\.){3}(25[0-5]|2[0-4]\d|((1\d{2})|([1-9]?\d)))$/
let param = /^(25[0-5]|2[0-4]\d|((1\d{2})|([1-9]?\d)))$/
// import {AInput} from 'ant-design-vue'
// 获取.的位置
const getPlace = (str) =>{
  let dPlaceArr = []
  Array.from(str).map((item,index) => {
    if(item === '.'){
      dPlaceArr.push(index)
    }
  })
  // console.log(dPlaceArr)
  return dPlaceArr
}
// 确定位置
function findIndexOrLast(arr, num) {
  for (let i = 0; i < arr.length; i++) {
    if (num <= arr[i]) {
      return i; // 返回第一个小于等于 num 的元素的下标
    }
  }
  return arr.length; // 如果没有小于等于 num 的元素，返回数组长度
}

// 验证
const patternY = (i) =>{
  if(param.test(arr[i]) == false){
      console.log(`第${i+1}个数组格式错误`)
  }else if(arr[i]!='' && Number(arr[i]) > 255){
    // 获取.的位置
    dPlaceArr = getPlace(str)
    myInput.value.setSelectionRange(dPlaceArr+1,dPlaceArr+1)
  }
}
// 插入
String.prototype.insertStr = function(start, index, insertStr){
   const ary = this.split('');		// 转化为数组
   ary.splice(start, index, insertStr);	// 使用数组方法插入字符串
   return ary.join('');				// 拼接成字符串后输出
}
// 改变显示框
const showParagraph = ref(1);
const showTab = (tabNumber) => {
  showParagraph.value = tabNumber;
};



// 在 onMounted 钩子中执行特定的操作
onMounted(() => {
  console.log('mount',myInput.value)
  myInput.value.setSelectionRange(0,0)
});

watch(value, (newValue, oldValue) => {
  const elementInput = document.getElementById('ipInput')
  // 位置
  let locationS = elementInput.selectionStart
  // console.log(myInput.value)
  // console.log(`新值：${newValue}, 旧值：${oldValue}, 光标位置：${locationS.selectionStart},二${myInput.value.selectionStart}`);
  let arrNew = newValue.split('.')
  let arrOld = oldValue.split('.')
  let Dlo = getPlace(newValue)
  let i = findIndexOrLast(Dlo, locationS)
  // console.log(arr)
  // 提示
  if(!newValue.match(params)){
    showTab(1)
  }
  else if(showParagraph.value == 4){
    showTab(3)
  }
  // 字间距
  elementInput.style.letterSpacing = 500/(newValue.length+6)+'px'

  if(arrNew[i]  ==  ''){

  }
  else if(!arrNew[i].match(param) && arrOld[i].match(param)){
    value.value = oldValue
    // console.log('保留旧值')
    setTimeout(() => {
    // 在异步回调中获取最新的值
      elementInput.setSelectionRange(locationS, locationS)
    }, 0);
  }
  else if(newValue.match(params)){
    // 提示
    showTab(2)
  }
});

// 输入验证
const onInput = function(event){
  // 获取输入框的值
  let str = value.value
  let start = event.target.selectionStart
  let end = event.target.selectionEnd
  // 使用正则表达式匹配非数字字符
  const nonNumericPattern = /[^0-9\.]/g;
  // console.log(start)
  // 如果找到非数字字符，移除它们
  if (str.match(nonNumericPattern)) {
    // 过滤掉非数字字符
    str = str.replace(nonNumericPattern, '');
    value.value = str;
    // 提示
    showTab(4)
    setTimeout(() => {
    // 在异步回调中获取最新的值
      elementInput.setSelectionRange(start,end)
    }, 0);
  }
  
}

// 回车移动位置
const onkeydown = function(event){
  let str = value.value
  let start = event.target.selectionStart
  let end = event.target.selectionEnd

  
  if(event.key === 'Enter'){ 
    // 获取光标位置
    let guangBiao_arr = getPlace(str)
    for(let i =0;i<guangBiao_arr.length;i++){
      if(guangBiao_arr[i] > start){
        myInput.value.setSelectionRange(guangBiao_arr[i],guangBiao_arr[i])
        break
      }else if(start == guangBiao_arr[guangBiao_arr.length-1]){
        myInput.value.setSelectionRange(-1,-1)
      }else if(start == str.length){
        myInput.value.setSelectionRange(guangBiao_arr[0],guangBiao_arr[0])
      }
    }
  }
  // 删除不掉.
  else if(event.key === 'Backspace'||event.key === 'Delete'){
    let num = str.match(/\./g)
    let cha = 3 - num.length
    let xuanZhong = str.substring(start, end)


    if(xuanZhong.includes('.')){
      event.preventDefault()
      str = str.replace(xuanZhong,xuanZhong.replace(/\d+/g, ''))
      value.value = str
      setTimeout(() => {
      //在异步回调中获取最新的值
        myInput.value.setSelectionRange(start, start)
      }, 0);
    }
    else if(str[end-1]=='.'){
      setTimeout(() => {
      //在异步回调中获取最新的值
        myInput.value.setSelectionRange(start-1, start-1)
      }, 0);
      event.preventDefault()
    }
    else if(cha > 0){
      event.preventDefault()
      for(let i = 0;i < cha;i++){
        value.value = str.insertStr(start,0,'.')
        str = value.value
      }
    }
  }
  else if(event.key === '.'){
    event.preventDefault()
  }
}; 


// window.addEventListener('keydown', (event) => {
//   // 获取按下的键码和键名
//   const keyName = event.key;
//   // 输出到控制台
//   console.log(`Key Name "${keyName}"`);
// });

</script>

<style lang="less" scoped>
.ipInput{
  width: 500px;
  letter-spacing:80px;
  text-align: center;
}
</style>