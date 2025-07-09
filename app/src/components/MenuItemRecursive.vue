<script>
export default {
    name: 'MenuItemRecursive'
}
</script>
<script setup>
import { computed } from 'vue'

const props = defineProps({
    data: {
        type: Object,
        required: true,
        default: () => { }
    },
    expandedItems: {
        type: Object,
        default: () => { }
    },
    depth: {
        type: Number,
        default: 0
    },
    checkSelected: {
        type: Function,
        default: () => { }
    },
    nowSelected: {
        type: Object,
        default: () => ({})
    }
})
const hasChildren = computed(() => props.data.children && props.data.children.length > 0)
const isExpanded = computed(() => props.expandedItems[props.data.stage] === props.data.key)


const emits = defineEmits(['update', 'toggle'])
const toggleExpand = (item) => emits('toggle', item)
const handleIfSelected = (item) => props.checkSelected(item)


</script>
<template>
    <div class="sidebar-content">
        <div :class="['menu-item-content', { 'has-children': hasChildren }]" :style="{ paddingLeft: `${20 * depth}px` }"
            @click="toggleExpand(data)">
            <span class="menu-text" :class="[{ isSelected: handleIfSelected(data) }]">{{ data.text }}</span>
            <svg 
                v-if="data.children && data.children.length > 0"
                :class="['expand-icon', { expanded: expandedItems[data.stage] === data.key }]" width="16" height="16"
                viewBox="0 0 24 24" fill="none" stroke="currentColor">
                <polyline points="9,18 15,12 9,6"></polyline>
            </svg>
        </div>
        <div v-if="hasChildren && isExpanded" class="menu-children">
            <MenuItemRecursive 
                v-for="child in data.children" 
                :key="child.key" 
                :data="child" 
                :depth="depth + 1"
                @toggle="toggleExpand" :check-selected="checkSelected" :expanded-items="expandedItems"
                :now-selected="nowSelected" 
            />
        </div>
    </div>
</template>
<style scoped>
.sidebar-content {
    color: #333;
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

.isSelected {
    color: #42b883;
    font-weight: bold;
}
</style>