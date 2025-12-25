<script setup lang="ts">
import { onMounted, onUnmounted, ref, watch, shallowRef, useSlots, render, h } from 'vue';
import { Timeline, type TimelineOptions, type DataItem, type DataGroup } from 'vis-timeline/standalone';
import 'vis-timeline/styles/vis-timeline-graph2d.css';

interface Props {
  items: DataItem[];
  groups?: DataGroup[];
  options?: TimelineOptions;
  groupHeight?: string | number;
}

const props = withDefaults(defineProps<Props>(), {
  groupHeight: 'auto'
});
const emit = defineEmits(['select', 'click', 'doubleClick', 'contextmenu', 'rangechange', 'rangechanged']);
const slots = useSlots();

const timelineContainer = ref<HTMLElement | null>(null);
const timeline = shallowRef<Timeline | null>(null);

const createTimeline = () => {
  if (!timelineContainer.value) return;

  const options: TimelineOptions = {
    ...props.options,
    template: (item: any, element: HTMLElement) => {
      if (slots.item) {
        render(null, element); // Unmount previous content if any
        element.innerHTML = '';
        const vnode = h('div', { class: 'vt-slot-wrapper' }, slots.item({ item }));
        render(vnode, element);
        return undefined; // Return undefined to prevent vis-timeline from overwriting
      }
      return item.content;
    }
  };

  timeline.value = new Timeline(
    timelineContainer.value,
    props.items,
    props.groups || [],
    options
  );

  // Proxy events
  timeline.value.on('select', (properties) => emit('select', properties));
  timeline.value.on('click', (properties) => emit('click', properties));
  timeline.value.on('doubleClick', (properties) => emit('doubleClick', properties));
  timeline.value.on('contextmenu', (properties) => emit('contextmenu', properties));
  timeline.value.on('rangechange', (properties) => emit('rangechange', properties));
  timeline.value.on('rangechanged', (properties) => emit('rangechanged', properties));
};

onMounted(() => {
  createTimeline();
});

onUnmounted(() => {
  if (timeline.value) {
    timeline.value.destroy();
  }
});

// Watch for items changes
watch(() => props.items, (newItems) => {
  if (timeline.value) {
    timeline.value.setItems(newItems);
  }
}, { deep: true });

// Watch for groups changes
watch(() => props.groups, (newGroups) => {
  if (timeline.value && newGroups) {
    timeline.value.setGroups(newGroups);
  }
}, { deep: true });

// Watch for options changes
watch(() => props.options, (newOptions) => {
  if (timeline.value) {
    timeline.value.destroy();
    createTimeline();
  }
}, { deep: true });

// Expose timeline instance if needed
defineExpose({
  timeline
});
</script>

<template>
  <div 
    ref="timelineContainer" 
    class="vis-timeline-wrapper"
    :style="{ '--vt-group-height': typeof groupHeight === 'number' ? `${groupHeight}px` : groupHeight }"
  >
    <slot name="item" v-if="false" />
  </div>
</template>

<style scoped>
.vis-timeline-wrapper {
  /* Default Theme Variables */
  --vt-bg: rgba(15, 23, 42, 0.6);
  --vt-bg-blur: 8px;
  --vt-border-color: rgba(255, 255, 255, 0.1);
  --vt-grid-color: rgba(255, 255, 255, 0.05);
  --vt-text-color: #94a3b8;
  --vt-label-color: #f1f5f9;
  
  --vt-item-bg: rgba(30, 58, 138, 0.4);
  --vt-item-border: rgba(96, 165, 250, 0.3);
  --vt-item-text: #e2e8f0;
  
  --vt-item-selected-bg: rgba(59, 130, 246, 0.5);
  --vt-item-selected-border: #60a5fa;
  
  --vt-current-time: #f43f5e;

  /* Layout Variables */
  --vt-group-height: auto;

  width: 100%;
  height: 100%;
  border-radius: 12px;
  overflow: hidden;
  background: var(--vt-bg);
  backdrop-filter: blur(var(--vt-bg-blur));
}

:deep(.vis-timeline) {
  border: none;
  font-family: inherit;
}

:deep(.vis-panel.vis-bottom), 
:deep(.vis-panel.vis-center), 
:deep(.vis-panel.vis-left), 
:deep(.vis-panel.vis-right), 
:deep(.vis-panel.vis-top) {
  border-color: var(--vt-border-color);
}

:deep(.vis-text) {
  color: var(--vt-text-color);
}

:deep(.vis-time-axis .vis-grid.vis-vertical) {
  border-color: var(--vt-grid-color);
}

:deep(.vis-time-axis .vis-grid.vis-minor) {
  border-color: rgba(255, 255, 255, 0.03);
}

:deep(.vis-item) {
  border: 1px solid var(--vt-item-border);
  background-color: var(--vt-item-bg);
  color: var(--vt-item-text);
  border-radius: 6px;
  padding: 4px;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
  transition: all 0.2s ease;
}

:deep(.vis-item:hover) {
  border-color: var(--vt-item-selected-border);
  filter: brightness(1.2);
  transform: translateY(-1px);
}

:deep(.vis-item.vis-selected) {
  background-color: var(--vt-item-selected-bg);
  border-color: var(--vt-item-selected-border);
  box-shadow: 0 0 15px rgba(59, 130, 246, 0.3);
}

:deep(.vis-labelset .vis-label) {
  color: var(--vt-label-color);
  border-bottom: 1px solid var(--vt-border-color);
  height: var(--vt-group-height) !important;
  display: flex;
  align-items: center;
}

:deep(.vis-foreground .vis-group) {
  height: var(--vt-group-height) !important;
  border-bottom: 1px solid var(--vt-border-color);
}

:deep(.vis-current-time) {
  background-color: var(--vt-current-time);
  width: 2px;
}
</style>
