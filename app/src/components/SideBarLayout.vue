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
        <div class="sidebar-selection">
          <div> 快速選項 </div>  
          <div> 
            <select style="height:30px; width: 160px; margin:5px"v-model="selectionValue" @change="onSelected">
              <option v-for="stage in selectionData" :key="stage[stage.length - 1]._label" :value="JSON.stringify(stage)">
                {{ stage[stage.length - 1]._label }} 
              </option>
            </select>
          </div>
        </div>
        <MenuItemRecursive
          v-for="child in modifiedData"
          :key="child.key"
          :data="child"
          :depth="1"
          @toggle="toggleExpand" 
          :check-selected="checkIfSelected" 
          :expanded-items="expandedItems"
          :now-selected="nowSelected"
        />
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
import MenuItemRecursive from './MenuItemRecursive.vue'
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
function getAllPaths(data, currentPath = [], allPaths = [], stage = 1) {
  
  data.forEach(item => {
    const newPath = [...currentPath, {
      text: item.text,
      stage,
      key: item.key,
      _label: item.text,
      _value: `${stage}-${item.key}`
    }];
    
    // 葉子節點，保存完整路徑
    allPaths.push(newPath)

    if (item.children && item.children.length > 0) {
      getAllPaths(item.children, newPath, allPaths, stage + 1)
    } 
    
  })
  return allPaths
}

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
const selectionData = computed(() => getAllPaths(menuData))
const nowSelected = ref(JSON.parse(localStorage.getItem('sidebarExpandedItems')) || {})

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
const onSelected = (event) => {
  const selectedValue = JSON.parse(event.target.value)
  if (selectedValue) {
    selectedValue.forEach(item => {
      toggleExpand(item, true)
    })
  }
}

const toggleSidebar = () => {
  sidebarOpen.value = !sidebarOpen.value
}

const toggleExpand = (item, fromSelect) => {
    checkStageOnlySelected(item.key, item.stage)
    if( !fromSelect && expandedItems.value[item.stage] === item.key) {
        delete expandedItems.value[item.stage]
        if(!(item.children && item.children.length > 0)) {
          // 如果沒有子項目，則維持當前階段的選擇
          nowSelected.value[item.stage] = item.key
        } else {
          // 如果有子項目，則清除當前階段的選擇
          delete nowSelected.value[item.stage]
        }
    } else {
      expandedItems.value[item.stage] = item.key
      for(const sKey in expandedItems.value) {
          if (item.stage < parseInt(sKey)) {
              delete expandedItems.value[sKey]
          }
      }
    }
    localStorage.setItem('sidebarExpandedItems', JSON.stringify(expandedItems.value))
}

const checkStageOnlySelected = (key, stage) => {
    // 清除如果為不同父層的內容
    for(const sKey in nowSelected.value) {
        if (sKey.startsWith(stage) || stage < parseInt(sKey)) {
            delete nowSelected.value[sKey]
        }
    }
    if (nowSelected.value[stage]) {
        nowSelected.value[stage] = key

    } else {
        nowSelected.value[stage] = key
    }
}

const checkIfSelected = (item) => nowSelected.value[item.stage] === item.key
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
    animation: blink 0.5s infinite ease-in-out;
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

.sidebar-selection {
  padding: 10px;
  font-size: 15px;
  color: #011325;
  border-bottom: 1px solid #e9ecef;
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

/* 手機版優化 */
@media (max-width: 390px) {
  .sidebar {
    width: 50%;
  }
}
</style>