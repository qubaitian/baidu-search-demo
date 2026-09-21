<template>
  <div>
    <div class="search">
      <!-- logo部分 -->
      <img
        src="https://www.baidu.com/img/flexible/logo/pc/result.png"
        alt=""
        class="logo"
      />
      <!-- 搜索框部分 -->
      <div class="search-box">
        <!--  @keydown.up.prevent="handleUp"
          @keydown.down.prevent="handleDown"
          调用键盘上下键行为,加上.prevent是为了先取消键盘上下按键的默认行为-->
        <input
          type="text"
          v-model="kw"
          @keydown.up.prevent="handleUp"
          @keydown.down.prevent="handleDown"
          @keydown.enter="handleEnter"
        />
      </div>
      <div class="btn">百度一下</div>
    </div>
    <div class="info">
      <ul>
        <!-- @mouseenter="handleMouseEnter(index)" 鼠标移入事件 -->
         <!-- @click="handleClick(item)" 鼠标点击事件,拿到点击的联想词值,赋值给kw-->
        <li
          v-for="(item, index) in kwArr"
          @mouseenter="handleMouseEnter(index)"
          @click="handleClick(item)"
          :key="index"
          :class="{ cur: curIndex === index }"
        >
          {{ item }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
// 导入ref
import { ref, watch } from "vue";
// 定义搜索关键词kw,初始值为空字符串
const kw = ref("");
// 定义新的script标签
const newScript = ref(null);
// 定义旧的script标签
const oldScript = ref(null);
//关键字联想词数组
const kwArr = ref(null);
//定义当前选中的联想词kwArr索引
const curIndex = ref(-1);
// 实时监听kw关键字的变化
watch(kw, (newVal) => {
  // console.log(newVal);
  // 如果旧的script标签存在,则删除
  if (oldScript.value) {
    document.body.removeChild(oldScript.value);
    oldScript.value = null; //删完清空引用,避免再删一次
  }
  //「如果 newVal 是空字符串（或空），就清空联想词并 return」。
  if (!newVal) {
    kwArr.value = [];
    return;
  }
  newScript.value = document.createElement("script");
  // 设置script标签的src属性,是百度的接口地址
  newScript.value.src =
    "https://suggestion.baidu.com/su?cb=callback&wd=" + newVal;
  // 将新的script标签添加到body中
  document.body.appendChild(newScript.value);
  // 将新的script标签赋值给旧的script标签,目的是防止script标签重复上树
  oldScript.value = newScript.value;
  callbackFun();
});
function callbackFun() {
  window.callback = function (data) {
    kwArr.value = data.s;
  };
}
function handleUp() {
  curIndex.value--;
  //判断范围
  if (curIndex.value < -1) {
    curIndex.value = kwArr.value.length - 1;
  }
}
function handleDown() {
  curIndex.value++;
  //判断范围
  if (curIndex.value > kwArr.value.length - 1) {
    curIndex.value = -1;
  }
}
// 鼠标移入选中联想词,键盘上下键初始的curIndex等于鼠标移入的联想词的索引值,从此联想词开始上下键行为
function handleMouseEnter(num) {
  curIndex.value = num;
}
// window.open(地址, 窗口名),是让浏览器打开一个新页面。
//"_blank"在新标签页打开,当前模仿百度的页面还留着。不写或写成 "_self" 就会在当前页跳转。
//val是li标签中@click="handleClick(item)"传入的item值,即联想词值
function handleClick(val) {
  window.open("https://www.baidu.com/s?ie=utf-8&wd=" + val,"_blank");
}
// 回车键选中联想词,跳转到百度搜索页
function handleEnter() {
  const word =
    curIndex.value === -1 ? kw.value : kwArr.value[curIndex.value];
  if (!word) return;
  window.open("https://www.baidu.com/s?ie=utf-8&wd=" + word, "_blank");
}

</script>

<style scoped>
.search {
  display: flex;
  align-items: center;
}
.search .logo {
  width: 101px;
  height: 33px;
  margin-right: 10px;
  vertical-align: middle;
}
.search .search-box {
  display: inline-block;
  vertical-align: middle;
  width: 580px;
  height: 40px;
  border: 2px solid #c8c8c8;
  border-radius: 10px 0 0 10px;
  border-right: none;
  overflow: visible;
}
.search .search-box input {
  outline: 0;
  border: none;
  width: 484px;
  font: 16px/18px arial;
  padding: 10px 0 10px 14px;
  margin: 0;
  background: transparent;
}
.search .btn {
  display: inline-block;
  width: 108px;
  height: 44px;
  line-height: 44px;
  padding: 0;
  margin: 0;
  border: none;
  outline: 0;
  background-color: #4e6ef2;
  border-radius: 0 10px 10px 0;
  color: #fff;
  font-size: 17px;
  font-weight: 400;
  font-family: Arial, sans-serif;
  text-align: center;
  cursor: pointer;
  box-shadow: none;
}
.search .btn:hover,
.search .btn:active {
  background-color: #4662d9;
}
.info {
  padding-left: 80px;
  line-height: 30px;
  font-size: 14px;
}
.info ul li {
  list-style: none;
  cursor: pointer;
}
.info ul li.cur {
  color: #4e6ef2;
}
</style>
