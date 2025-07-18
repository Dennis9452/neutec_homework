﻿# neutec_homework
※ deploy.sh 可以自動build版到 github pages上
github pages: https://dennis9452.github.io/

如果不使用github pages，則依序執行 npm install -> npm run dev


主頁面九宮格動畫
    
1.請使用兩種以上的動畫執行方式，來繪製動畫 -> 已結合在程式碼內(animation名稱: moveRight & pulse-dot)
    
2.四顆球同時朝同一個座標點移動 -> 

    - 以目前程式碼來執行的話，在"ball"的class加上'animate-to-center'的class名稱，觸發往中間移動的動畫 => 

    @keyframes toCenter {
        to { 
            top: 50%;
            left: 50%; 
        }
    }
    .animate-to-center {
        animation: toCenter 1.5s ease-in-out infinite;
    }

3.請在效能考量下，設計可同時存在一百顆球、且指定飛行起終點的結構 ->

    - 會把css動畫先改成用requestAnimationFrame來實作動畫效果，100顆球指定飛行的起終點的話就設定為一個固定的config，
    再規劃所需的動畫(easeProgress)的方式將球的動畫實現，同時球的產生也改成用svg做呈現，
    要傳入requestAnimationFrame的callback則作為動畫規劃的內容
    
    <!-- 渲染所有球 -->
    <circle v-for="ball in activeBalls" :key="ball.id" :cx="ball.currentX" :cy="ball.currentY"
        :r="8 + ball.progress * 4" :fill="ball.color" :opacity="0.8 - ball.progress * 0.3" :style="{
        filter: 'drop-shadow(0 2px 4px rgba(0,0,0,0.3))',
        transition: 'r 0.1s ease'
        }" />

    for (let i = 0; i < 100; i++) {
        balls.value.push({
            startX: 0,
            startY: 0,
            endX: 350,
            endY: 350,
            duration: 1000,
            startTime: Date.now()
            color: 'orange'
        });
    }

    requestAnimationFrame(anime)
    const animate = () => {
        const now = Date.now();
        let hasActiveBalls = false;

        balls.value = balls.value.map(ball => {

            const elapsed = now - ball.startTime;
            const progress = Math.min(elapsed / ball.duration, 1);

            if (progress >= 1) {
            return { ...ball, isActive: false, progress: 1 };
            }

            hasActiveBalls = true;
            const easeProgress = easeInOutCubic(progress);

            const currentX = ball.startX + (ball.endX - ball.startX) * easeProgress;
            const currentY = ball.startY + (ball.endY - ball.startY) * easeProgress;

            return {
            ...ball,
            currentX,
            currentY,
            progress: easeProgress
            };
        });
        if (hasActiveBalls) {
            animationId = requestAnimationFrame(animate);
        } 
    }

側邊選單

1.另提供一個下拉選單，需包含所有種類。從下拉選單中選取任一項目時，等同點擊該項目 -> 

    已結合在程式碼內

2.請實作記憶功能，關閉分頁後再開啟，可以顯示上次選取的項目 -> 
    
    已結合在程式碼內，利用localStorage紀錄

3.請在效能考量下，設計可收合、展開最多一百層的選單 

    不認為程式碼內的遞迴生成方式可以解決，100次的遞迴應該會造成stack overflow，有想到可以用web worker來另外處理，
    不影響當前JS執行效能，或是搭配遞迴搭配map，將已遞迴過的路線記錄下來，若後續有重複的就可以不用再進行遞迴
