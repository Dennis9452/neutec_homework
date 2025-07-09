<template>
  <div class="app-container">
    <!-- 遮罩層 -->
    <div 
      :class="['overlay', { visible: sidebarOpen }]"
      @click="toggleSidebar"
    ></div>

    <!-- 側邊選單 -->
    <div :class="['sidebar', { open: sidebarOpen }]">
      <div class="sidebar-header">
        <div class="sidebar-title">飲品選單</div>
        <button class="close-button" @click="toggleSidebar">×</button>
      </div>
      <div class="sidebar-content">
        <div 
          v-for="item in modifiedData" 
          :key="item.key"
          class="menu-item"
        >
          <div 
            :class="['menu-item-content', { 'has-children': item.children && item.children.length > 0 }]"
            :style="{ paddingLeft: '20px' }"
            @click="() => toggleExpand(item.key, item.stage)"
          >
            <span class="menu-text" :class="[{ isSelected: checkIfSelected(item)}]">{{ item.text }}</span>
            <svg 
              v-if="item.children && item.children.length > 0"
              :class="['expand-icon', { expanded: expandedItems[item.stage] === item.key }]"
              width="16" 
              height="16" 
              viewBox="0 0 24 24" 
              fill="none" 
              stroke="currentColor"
            >
              <polyline points="9,18 15,12 9,6"></polyline>
            </svg>
          </div>
          
          <!-- 第二層 -->
          <div v-if="item.children && expandedItems[item.stage] === item.key" class="menu-children">
            <div 
              v-for="child in item.children" 
              :key="child.key"
              class="menu-item"
            >
              <div 
                :class="['menu-item-content', { 'has-children': child.children && child.children.length > 0 }]"
                :style="{ paddingLeft: '40px' }"
                @click="() => toggleExpand(child.key, child.stage)"
              >
                <span class="menu-text" :class="[{ isSelected: checkIfSelected(child)}]">{{ child.text }}</span>
                <svg 
                  v-if="child.children && child.children.length > 0"
                  :class="['expand-icon', { expanded: expandedItems[child.stage] === child.key }]"
                  width="16" 
                  height="16" 
                  viewBox="0 0 24 24" 
                  fill="none" 
                  stroke="currentColor"
                >
                  <polyline points="9,18 15,12 9,6"></polyline>
                </svg>
              </div>
              
              <!-- 第三層 -->
              <div v-if="child.children && expandedItems[child.stage] === child.key" class="menu-children">
                <div 
                  v-for="grandChild in child.children" 
                  :key="grandChild.key"
                  class="menu-item"
                >
                  <div 
                    :class="['menu-item-content', { 'has-children': grandChild.children && grandChild.children.length > 0 }]"
                    :style="{ paddingLeft: '60px' }"
                    @click="() => toggleExpand(grandChild.key, grandChild.stage)"
                  >
                    <span class="menu-text" :class="[{ isSelected: checkIfSelected(grandChild)}]">{{ grandChild.text }}</span>
                    <svg 
                      v-if="grandChild.children && grandChild.children.length > 0"
                      :class="['expand-icon', { expanded: expandedItems[grandChild.stage] === grandChild.key }]"
                      width="16" 
                      height="16" 
                      viewBox="0 0 24 24" 
                      fill="none" 
                      stroke="currentColor"
                    >
                      <polyline points="9,18 15,12 9,6"></polyline>
                    </svg>
                  </div>
                  
                  <!-- 第四層 -->
                  <div v-if="grandChild.children && expandedItems[grandChild.stage] === grandChild.key " class="menu-children">
                    <div 
                      v-for="greatGrandChild in grandChild.children" 
                      :key="greatGrandChild.key"
                      class="menu-item"
                    >
                      <div 
                        class="menu-item-content"
                        :style="{ paddingLeft: '80px' }"
                        @click="() => toggleExpand(greatGrandChild.key, greatGrandChild.stage)"
                      >
                        <span class="menu-text" :class="[{ isSelected: checkIfSelected(greatGrandChild)}]">{{ greatGrandChild.text }}</span>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- 主頁面 -->
    <div :class="['main-content', { shifted: sidebarOpen }]">
      <div class="header">
        <button class="menuButton" @click="toggleSidebar">☰</button>
        <div class="title"></div>
      </div>
      <div class="content-area">
        <div class="nine-grid-container">
          <div class="nine-grid">
            <!-- 九宮格 --> 
            <template :key="times" v-for="(times) in 9" class="grid-cell">
                <div :class="['grid-cell', {'blink': [3,5,9].includes(times)}]"></div>
            </template>
          </div>
        
          <!-- 四個角落的球 -->
          <div class="ball top-left">1</div>
          <div class="ball top-right">1</div>
          <div class="ball bottom-left">1</div>
          <div class="ball bottom-right">1</div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
  export default {
    name: 'SideBarLayout',
  }
</script>

<script setup>
import { computed, ref } from 'vue'

// 響應式數據
const sidebarOpen = ref(false)
const expandedItems = ref(JSON.parse(localStorage.getItem('sidebarExpandedItems')) || {})

// 選單數據
const menuData = [
  {
    key: "64f",
    text: "好喝黑糖",
    children: [{
      key: "445",
      text: "黑糖鮮奶",
      children: [{
        key: "37a",
        text: "黑糖珍珠鮮奶"
      }, {
        key: "feb",
        text: "黑糖芋圓鮮奶"
      }, {
        key: "59c",
        text: "黑糖蒟蒻鮮奶"
      }]
    }, {
      key: "29e",
      text: "黑糖冬瓜",
      children: [{
        key: "ac3",
        text: "黑糖冬瓜牛奶"
      }, {
        key: "ca0",
        text: "黑糖冬瓜珍珠"
      }]
    }]
  },
  {
    key: "6c3",
    text: "茶",
    children: [{
      key: "5dc",
      text: "烏龍綠"
    }, {
      key: "b5f",
      text: "綠茶"
    }, {
      key: "b09",
      text: "紅茶"
    }, {
      key: "887",
      text: "青茶"
    }]
  }, {
    key: "c81",
    text: "咖啡",
    children: [{
      key: "e02",
      text: "黑咖啡",
      children: [{
        key: "d20",
        text: "濃縮咖啡"
      }, {
        key: "1f8",
        text: "美式咖啡"
      }]
    }, {
      key: "d7a",
      text: "牛奶咖啡",
      children: [{
        key: "c09",
        text: "拿鐵",
        children: [{
          key: "db2",
          text: "黑糖拿鐵"
        }, {
          key: "9f6",
          text: "榛果拿鐵"
        }, {
          key: "b61",
          text: "香草拿鐵"
        }]
      }, {
        key: "9ac",
        text: "卡布奇諾"
      }, {
        key: "ce8",
        text: "摩卡"
      }]
    }]
  }
]

const modifiedData = computed(() => {
  const stage = 1
  return menuData.map(item => {
    return {
      ...item,
      stage, 
      children: parseChildren(item.children, stage + 1)
    }
  })
})
const nowSelcted = ref(JSON.parse(localStorage.getItem('sidebarExpandedItems')) || {})

// 方法
const parseChildren = (children, stage) => {
  if (!children || children.length === 0) return []
  return children.map(child => {
    return {
      ...child,
      stage,
      children: parseChildren(child.children, stage + 1)
    }
  })
}
const toggleSidebar = () => {
  sidebarOpen.value = !sidebarOpen.value
}

const toggleExpand = (key, stage) => {
  checkStageOnlySelected(key, stage)
  
  expandedItems.value[stage] = key
  for(const sKey in expandedItems.value) {
    if (stage < parseInt(sKey)) {
      delete expandedItems.value[sKey]
    }
  }
  localStorage.setItem('sidebarExpandedItems', JSON.stringify(expandedItems.value))
}

const checkStageOnlySelected = (key, stage) => {
  // 清除如果為不同父層的內容
  for(const sKey in nowSelcted.value) {
    if (sKey.startsWith(stage) || stage < parseInt(sKey)) {
      delete nowSelcted.value[sKey]
    }
  }
  if (nowSelcted.value[stage]) {
    nowSelcted.value[stage] = key

  } else {
    nowSelcted.value[stage] = key
  }
}

const checkIfSelected = (item) => {
  return nowSelcted.value[item.stage] === item.key
}
</script>

<style scoped>
.app-container {
  width: 390px;
  height: 844px;
  position: relative;
  display: flex;
  overflow: hidden;
}

.main-content {
  flex: 1;
  height: 100%;
  position: relative;
  transition: transform 0.3s ease;
}

.header {
  height: 5vh;
  background: rgba(255, 255, 255, 0.1);
  backdrop-filter: blur(10px);
  display: flex;
  flex-direction: row-reverse;
  align-items: center;
  /* padding: 0 20px; */
}

.menuButton {
  background: none;
  border: none;
  color: white;
  font-size: 18px;
  cursor: pointer;
  padding: 30px;
  border-radius: 4px;
  transition: background-color 0.2s;
}

.menuButton:hover {
  background: rgba(255, 255, 255, 0.1);
}

.main-title {
  color: white;
  font-size: 18px;
  font-weight: 600;
  margin-left: 15px;
}

.content-area {
    flex: 1;
    display: flex;
    align-items: center;
    justify-content: center;
    padding: 20px;
    position: relative;
    margin: 50% auto;
}

.nine-grid-container {
    position: relative;
    width: 100%;
    max-width: 350px;
    z-index: 1;
}

.nine-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 10px;
    max-width: 350px;
    width: 100%;
    aspect-ratio: 1;
    position: relative;
}

.grid-cell {
    width: 100%;
    height: 100px;
    border: black solid 2px;
    background: radial-gradient(circle, rgba(113,81,95,1) 81%, rgba(0,0,0,1) 100%);
    position: relative;
}
.grid-cell.blink {
    animation: blink 0.5s infinite;
}

@keyframes blink {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.6; }
}

.ball {
    width: 30px;
    height: 30px;
    background-color: #A5F12B;
    border-radius: 50%;
    position: absolute;
    top: 50%;
    left: 20px;
    box-shadow: 0 4px 15px rgba(255, 107, 107, 0.4);
    color: black
}
@keyframes moveRight {
    100% { margin-left: 18rem; }
}
@keyframes moveRightLate {
    0% { transform: translateX(-20rem); }
    100% { transform: translateX(26rem); }
}

@keyframes pulse-dot {
  0% {
    transform: scale(1);
  }
  50% {
    transform: scale(1.5);
  }
  100% {
    transform: scale(.8);
  }
}

.ball.top-left {
    top: 30px;
    left: 15px;
}

.ball.top-right {
    top: 30px;
    left: 305px;
    /* animation-delay: 4s; */
}

.ball.bottom-left {
    top: 250px;
    left: 15px;
}

.ball.bottom-right {
    top: 250px;
    left: 305px;
    /* animation-delay: 4s; */
}

.top-left, .top-right, .bottom-left, .bottom-right {
    animation-name: moveRight, pulse-dot;
    animation-duration: 1.5s, 1.5s;
    animation-iteration-count: infinite, infinite;
    animation-timing-function: ease-in-out, ease-in-out;
    animation-delay: 0s, 0s;
}


.sidebar {
  position: fixed;
  top: 0;
  right: -390px;
  width: 50%;
  height: 100%;
  background: white;
  box-shadow: 2px 0 10px rgba(0, 0, 0, 0.1);
  transform: translateX(-100%);
  transition: transform 0.3s ease;
  z-index: 1000;
  overflow-y: auto;
}

.sidebar.open {
  transform: translateX(-24.7rem);
}

.sidebar-header {
  height: 60px;
  background: #f8f9fa;
  display: flex;
  flex-direction: row-reverse;
  align-items: center;
  justify-content: space-between;
  padding: 0 20px;
  border-bottom: 1px solid #e9ecef;
}

.sidebar-title {
  font-size: 18px;
  font-weight: 600;
  color: #333;
}

.close-button {
  background: none;
  border: none;
  font-size: 20px;
  cursor: pointer;
  color: #666;
  padding: 8px;
  border-radius: 4px;
  transition: background-color 0.2s;
}

.close-button:hover {
  background: rgba(0, 0, 0, 0.1);
}

.sidebar-content {
  padding: 20px 0;
}

.menu-item {
  margin-bottom: 2px;
}

.menu-item-content {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 12px 20px;
  cursor: pointer;
  transition: background-color 0.2s;
  border-left: 3px solid transparent;
}

.menu-item-content:hover {
  background-color: #f8f9fa;
}

.menu-item-content.has-children:hover {
  background-color: #e3f2fd;
}

.menu-text {
  font-size: 15px;
  color: #333;
  font-weight: 500;
}

.expand-icon {
  transition: transform 0.2s ease;
  color: #666;
}

.expand-icon.expanded {
  transform: rotate(90deg);
}

.menu-children {
  background-color: #f8f9fa;
  border-left: 2px solid #e3f2fd;
}

.overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background: rgba(0, 0, 0, 0.5);
  z-index: 999;
  opacity: 0;
  visibility: hidden;
  transition: opacity 0.3s ease, visibility 0.3s ease;
}

.overlay.visible {
  opacity: 1;
  visibility: visible;
}

.isSelected {
  color: #42b883; /* Vue.js 綠色 */
  font-weight: bold;
}

/* 手機版優化 */
@media (max-width: 390px) {
  .sidebar {
    width: 50%;
  }
  
  .main-content.shifted {
    /* transform: translateX(50%); */
  }
}
</style>