<template>
  <div class="wrapper" :style="'width:'+horizontol+'px;height:'+vertivcal+'px;'">
      <TransitionGroup :css="false" @before-enter="onBeforeEnter" @enter="onEnter" @leave="onLeave">
          <li class="li" v-for="item in activeArr" :key="item.actionId" :data-index="item.actionId">
              <xmp class="text" disabled :style="doStyle(item)">{{ item.itemContent }}</xmp>
          </li>
      </TransitionGroup>
  </div>
</template>

<script setup>
  import gsap from 'gsap'
  import {ref, watch, onMounted, defineProps} from 'vue'

  const props = defineProps({
      //数据池
      boxData: {
          type: Array,
          default: () => []
      },
      //容器宽
      horizontol: {
          type: Number,
          default: 400
      },
      //容器高
      vertivcal: {
          type: Number,
          default: 40
      },
      //无用
      boardWidth: {
          type: Number,
          default: 0
      },
      //无用
      boardHeight: {
          type: Number,
          default: 0
      }
  });

  //数据池索引
  const index = ref(0);
  //运动数据池
  const activeArr = ref([]);
  //整理-》可用数据池
  const mockData = ref([]);
  /**
   * @author wangze
   * @time 20230917
   * @description 监听数据变化，处理数据，动画开始
   * */
  watch(
      () => props.boxData,
      (newValue) => {
          if (newValue.length) doData(newValue);
      })
  /**
   * @author wangze
   * @time 20230917
   * @description 挂载执行
   * */
  onMounted(() => {
      if (props.boxData.length) doData(props.boxData);
  })

  /**
   * @author wangze
   * @time 20230917
   * @description 整理数据为可用数据
   * */
  function doData(newValue) {
      let arr = [];
      newValue.forEach((item, index) => {
          if (index === props.boxData.length - 1) {
              item.to = props.boxData[0].inouttypeName;
          } else {
              item.to = props.boxData[index + 1].inouttypeName;
          }
          arr.push(item);
      });
      mockData.value = arr;
      init()
  }

  /**
   * @author wangze
   * @time 20230917
   * @description 初始化调用添加元素FUN
   * */
  function init() {
      insertFunActive()
  }

  /**
   * @author wangze
   * @time 20230917
   * @description 添加运动元素
   * */
  function insertFunActive() {
      if (mockData.value.length) activeArr.value.push(mockData.value[index.value])
  }

  /**
   * @author wangze
   * @time 20230917
   * @description 删除运动元素
   * */
  function delateFunActive() {
      activeArr.value.splice(0, 1)
  }

  /**
   * @author wangze
   * @time 20230917
   * @description 在元素被插入到 DOM 之前被调用,用这个来设置元素的 "enter-from" 状态
   * */
  function onBeforeEnter(el) {
      el.setAttribute('to', mockData.value[index.value].to);
      domOnBeforeEnter(el, mockData.value[index.value].inouttypeName);
  }

  /**
   * @author wangze
   * @time 20230917
   * @description 在元素被插入到 DOM 之后的下一帧被调用,用这个来开始进入动画
   * */
  function onEnter(el, done) {
      domOnEnter(el, mockData.value[index.value].inouttypeName, done)
      let timer;
      clearInterval(timer);
      timer = setTimeout(() => {
          index.value++;
          if (activeArr.value.length === 1) delateFunActive();
          if (index.value == mockData.value.length) index.value = 0;
          insertFunActive();
      }, 5000 + mockData.value[index.value].stay * 10)

  }

  /**
   * @author wangze
   * @time 20230917
   * @description 在离开过渡开始时调用，用这个来开始离开动画
   * */
  function onLeave(el, done) {
      const to = el.getAttribute('to')
      domOnLeave(el, to, done)
  }

  /**
   * @author wangze
   * @time 20230917
   * @description 元素准备
   * */
  function domOnBeforeEnter(el, val) {
      switch (val) {
          case '上移': {
              // el.style.y = 100;
              el.style.transform = `translate(0, ${props.vertivcal}px)`;
              break
          }
          case '下移': {
              // el.style.y = -50;
              el.style.transform = `translate(0, -${props.vertivcal}px)`;
              break
          }
          case '左移': {
              // el.style.x = 900;
              el.style.transform = `translate(${props.horizontol}px, 0)`;
              break
          }
          case '右移': {
              // el.style.x = -900;
              el.style.transform = `translate(-${props.horizontol}px, 0)`;
              break
          }
      }
  }

  /**
   * @author wangze
   * @time 20230917
   * @description 元素进入
   * */
  function domOnEnter(el, val, done) {
      switch (val) {
          case '上移': {
              gsap.to(el, {
                  duration: 5,
                  ease: "none",
                  y: 0,
                  onComplete: done
              })
              break
          }
          case '下移': {
              gsap.to(el, {
                  duration: 5,
                  ease: "none",
                  y: 0,
                  onComplete: done
              })
              break
          }
          case '左移': {
              gsap.to(el, {
                  duration: 5,
                  ease: "none",
                  x: 0,
                  onComplete: done
              })
              break
          }
          case '右移': {
              gsap.to(el, {
                  duration: 5,
                  ease: "none",
                  x: 0,
                  onComplete: done
              })
              break
          }
      }
  }

  /**
   * @author wangze
   * @time 20230917
   * @description 元素离开
   * */
  function domOnLeave(el, val, done) {
      switch (val) {
          case '上移': {
              gsap.to(el, {
                  duration: 5,
                  ease: "none",
                  y: -props.vertivcal,
                  onComplete: done
              })
              break
          }
          case '下移': {
              gsap.to(el, {
                  duration: 5,
                  ease: "none",
                  y: props.vertivcal,
                  onComplete: done
              })
              break
          }
          case '左移': {
              gsap.to(el, {
                  duration: 5,
                  ease: "none",
                  x: -props.horizontol,
                  onComplete: done
              })
              break
          }
          case '右移': {
              gsap.to(el, {
                  duration: 5,
                  ease: "none",
                  x: props.horizontol,
                  onComplete: done
              })
              break
          }
      }
  }

  /**
   * @author wangze
   * @time 20230917
   * @description 文本元素样式处理
   * */
  function doStyle(currentData) {
      const fontColorRgb = currentData.fontColorRgb;
      const color = `color:rgb(${fontColorRgb.slice(0, 3)},${fontColorRgb.slice(3, 6)},${fontColorRgb.slice(6, 9)})`;//文字颜色
      const fontSize = 'font-size:' + currentData.fontSize + 'px';//文字字体大小
      const lineHeight = 'line-height:' + currentData.fontSize + 'px';//文字字体大小

      const fontFamily = 'font-family:' + currentData.fontTypeName;//文字字体
      //文字距离屏幕的定位
      const top = 'margin-top:' + currentData.starty + 'px';
      const left = 'margin-left:' + currentData.startx + 'px';

      const fontBgColorRgb = currentData.backgroundcolorRgb;
      const backgroundColor = `rgb(${fontBgColorRgb.slice(0, 3)},${fontBgColorRgb.slice(3, 6)},${fontBgColorRgb.slice(6, 9)})`;//屏幕背景色
      return color + ';' + fontSize + ';' + lineHeight + ';' + fontFamily + ';' + top + ';' + left + ';' + backgroundColor + ';';
  }

</script>

<style lang="scss">
  html,
  body {
      width: 100%;
      height: 100%;
      background-color: aqua;
  }

  .wrapper {
      width: 900px;
      height: 50px;
      position: relative;
      background-color: black;
      overflow: hidden;

      .li {
          width: 100%;
          height: 100%;
          position: absolute;
          list-style: none;

          xmp {
              width: 100%;
              height: 50px;
              color: #fff;
              font-size: 20px;
              position: absolute;
          }
      }
  }

</style>
