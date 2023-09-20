<template>
  <div class="wrapper">
    <TransitionGroup :css="false" @before-enter="onBeforeEnter" @enter="onEnter" @leave="onLeave">
      <li class="li" v-for="(item, index) in activeArr" :key="item.actionId" :data-index="item.actionId">
        <p>{{ item.name }}</p>
      </li>
    </TransitionGroup>
  </div>
  <button @click="add"> add</button>
</template>

<script setup>
import gsap from 'gsap'
import { ref, nextTick, onMounted } from 'vue'

const index = ref(0);
const activeArr = ref([]);

const mockData = ref([{
  actionId: '1',
  inouttypeName: '左移',
  name: '左左左',
  to: '左移',
  stay: 1
},
{
  actionId: '2',
  inouttypeName: '左移',
  name: '左2222',
  to: '左移',
  stay: 1
},
{
  actionId: '3',
  inouttypeName: '左移',
  name: '左3333',
  to: '右移',
  stay: 1
},
{
  actionId: '4',
  inouttypeName: '右移',
  name: '右右右',
  to: '上移',
  stay: 1
}, {
  actionId: '5',
  inouttypeName: '上移',
  name: '上上上',
  to: '下移',
  stay: 1
}, {
  actionId: '6',
  inouttypeName: '下移',
  name: '下下下',
  to: '左移',
  stay: 1
}]);

onMounted(() => {
  init();
})

function add() {
  insertFunActive();
}

function init() {
  insertFunActive()
}

//添加运动元素
function insertFunActive() {
  activeArr.value.push(mockData.value[index.value])
}
//删除运动元素
function delateFunActive() {
  activeArr.value.splice(0, 1)
}

// 在元素被插入到 DOM 之前被调用,用这个来设置元素的 "enter-from" 状态
function onBeforeEnter(el) {
  el.setAttribute('to', mockData.value[index.value].to);
  domOnBeforeEnter(el, mockData.value[index.value].inouttypeName);
}

// 在元素被插入到 DOM 之后的下一帧被调用,用这个来开始进入动画
function onEnter(el, done) {
  domOnEnter(el, mockData.value[index.value].inouttypeName, done)
  let timer;
  clearTimeout(timer);
  timer = setTimeout(() => {
    index.value++;
    if (activeArr.value.length === 1) delateFunActive();
    if (index.value == mockData.value.length) index.value = 0;
    insertFunActive();
  }, 5000 + mockData.value[index.value].stay * 1000)
}

// 在离开过渡开始时调用，用这个来开始离开动画
function onLeave(el, done) {
  const to = el.getAttribute('to')
  domOnLeave(el, to, done)
}

function domOnBeforeEnter(el, val) {
  switch (val) {
    case '上移': {
      // el.style.y = 100;
      el.style.transform = 'translate(0, 50px)';
      break
    }
    case '下移': {
      // el.style.y = -50;
      el.style.transform = 'translate(0, -50px)';
      break
    }
    case '左移': {
      // el.style.x = 900;
      el.style.transform = 'translate(900px, 0)';
      break
    }
    case '右移': {
      // el.style.x = -900;
      el.style.transform = 'translate(-900px, 0)';
      break
    }
    case '立即显示': {
      // el.style.x = 0;
      el.style.transform = `translate(0, 0)`;
      break
    }
  }
}

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
    case '立即显示': {
      gsap.to(el, {
        duration: 0,
        ease: "none",
        x: 0,
        onComplete: done
      })
      break
    }
  }
}

function domOnLeave(el, val, done) {
  switch (val) {
    case '上移': {
      gsap.to(el, {
        duration: 5,
        ease: "none",
        y: -50,
        onComplete: done
      })
      break
    }
    case '下移': {
      gsap.to(el, {
        duration: 5,
        ease: "none",
        y: 50,
        onComplete: done
      })
      break
    }
    case '左移': {
      gsap.to(el, {
        duration: 5,
        ease: "none",
        x: -900,
        onComplete: done
      })
      break
    }
    case '右移': {
      gsap.to(el, {
        duration: 5,
        ease: "none",
        x: 900,
        onComplete: done
      })
      break
    }
    case '立即显示': {
      gsap.to(el, {
        duration: 0,
        ease: "none",
        opacity: 0,
        onComplete: done
      })
      break
    }
  }
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
    height: 50px;
    position: absolute;
    list-style: none;

    &:nth-child(1) {
      background-color: rgba(47, 35, 183, 0.5);
    }

    &:nth-child(2) {
      background-color: rgba(125, 194, 40, 0.5);
    }

    &:nth-child(3) {
      background-color: rgba(230, 13, 13, 0.41);
    }
  }

  p {
    color: #fff;
    font-size: 18px;
  }
}
</style>
