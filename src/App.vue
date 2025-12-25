<script setup lang="ts">
import { ref } from 'vue';
import VisTimeline from './components/VisTimeline.vue';
import StatusBadge from './components/StatusBadge.vue';
import type { DataItem, DataGroup, TimelineOptions } from 'vis-timeline/standalone';

const today = new Date().toISOString().split('T')[0];

const items = ref<DataItem[]>([
  { id: 'break', content: 'LUNCH BREAK', start: `${today} 11:00:00`, end: `${today} 12:00:00`, type: 'background', className: 'break-time' },
  { id: 1, content: 'Morning Meeting', start: `${today} 09:00:00`, end: `${today} 10:00:00`, group: 2, status: 'done' },
  { id: 2, content: 'Deep Work Session', start: `${today} 10:00:00`, end: `${today} 13:00:00`, group: 2, status: 'doing' },
  { id: 3, content: 'Client Call', start: `${today} 14:00:00`, end: `${today} 15:00:00`, group: 1, status: 'todo' },
  { id: 4, content: 'Code Review', start: `${today} 15:30:00`, end: `${today} 17:00:00`, group: 2, status: 'todo' },
  { id: 5, content: 'Market Research', start: `${today} 09:30:00`, end: `${today} 14:30:00`, group: 4, status: 'doing' },
]);

const groups = ref<DataGroup[]>([
  { id: 1, content: 'Strategic Planning' },
  { id: 2, content: 'Engineering' },
  { id: 3, content: 'Quality Assurance' },
  { id: 4, content: 'Marketing' },
]);

const options = ref<TimelineOptions>({
  height: '450px',
  editable: true,
  showCurrentTime: true,
  stack: false, // Changed to false to see overlap clearly
  margin: {
    item: 10,
    axis: 15
  },
  orientation: 'top',
  start: `${today} 07:00:00`,
  end: `${today} 23:00:00`,
  min: `${today} 07:00:00`,
  max: `${today} 23:00:00`,
  zoomable: true,
  zoomMin: 1000 * 60 * 60, // 1 hour
  zoomMax: 1000 * 60 * 60 * 24, // 1 day
  locale: 'ja',
  timeAxis: { scale: 'hour', step: 1},
  format: {  
    majorLabels: {  
      day: 'MM月DD日',
      hour: 'MM月DD日',
      minute: 'MM月DD日',
    },
    minorLabels: function(date, scale, step) {  
      // 毎正時（分が00分）の場合のみラベルを表示  
      if (date.minutes() === 0) {  
        return date.format('HH');  
      }  
      return ''; // それ以外は空文字を返して非表示  
    } 
  }
});

const handleSelect = (properties: any) => {
  console.log('Selected:', properties);
};
</script>

<template>
  <main>
    <h1>Vis Timeline + Vue 3</h1>
    <p>A premium timeline integration using TypeScript and Vite.</p>
    
    <div class="glass-panel">
      <h2>Main Project Timeline</h2>
      <p style="font-size: 0.9rem; opacity: 0.8; margin-bottom: 1rem;">
        Custom item rendering using <code>#item</code> slot.
      </p>
      <VisTimeline 
        :items="items" 
        :groups="groups" 
        :options="options"
        :group-height="70"
        @select="handleSelect"
      >
        <template #item="{ item }">
          <div class="custom-item-content">
            <span class="icon">{{ item.type === 'box' ? '📌' : item.type === 'point' ? '💎' : '📊' }}</span>
            <span class="text">{{ item.content }}</span>
            <StatusBadge v-if="item.status" :status="item.status" />
          </div>
        </template>
      </VisTimeline>
    </div>

    <div class="glass-panel">
      <h2>Theme Customization Demo</h2>
      <p style="font-size: 0.9rem; opacity: 0.8; margin-bottom: 1rem;">
        Using CSS Variables to create a "Purple Rain" theme.
      </p>
      <VisTimeline 
        :items="items.slice(0, 5)" 
        :options="{ ...options, height: '250px' }"
        class="custom-timeline"
      />
    </div>

    <div class="controls glass-panel">
      <h3>Controls</h3>
      <div class="button-group">
        <button @click="items.push({ id: items.length + 1, content: 'New Event', start: new Date(), group: 2 })" class="btn">
          Add Realtime Event
        </button>
      </div>
    </div>
  </main>
</template>

<style scoped>
.custom-item-content {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 0.85rem;
}

.icon {
  font-size: 1rem;
}

.badge {
  background: #f59e0b;
  color: #000;
  font-size: 0.65rem;
  padding: 2px 6px;
  border-radius: 4px;
  font-weight: 800;
  text-transform: uppercase;
}

:deep(.break-time) {
  background-color: rgba(244, 63, 94, 0.2) !important;
  z-index: 10 !important;
  pointer-events: none;
  display: flex;
  align-items: center;
  justify-content: center;
}

:deep(.break-time .vis-item-content) {
  color: #f43f5e;
  font-weight: bold;
  font-size: 1.2rem;
  letter-spacing: 0.2rem;
  opacity: 0.6;
  transform: rotate(-15deg);
}

.custom-timeline {
  --vt-bg: rgba(88, 28, 135, 0.3);
  --vt-item-bg: rgba(147, 51, 234, 0.4);
  --vt-item-border: rgba(192, 132, 252, 0.5);
  --vt-item-selected-bg: rgba(168, 85, 247, 0.6);
  --vt-item-selected-border: #d8b4fe;
  --vt-current-time: #f472b6;
}

.controls {
  margin-top: 2rem;
  padding: 1.5rem;
}

h3 {
  margin-top: 0;
  color: #f8fafc;
}

.button-group {
  display: flex;
  gap: 1rem;
  justify-content: center;
}

.btn {
  background: linear-gradient(135deg, #3b82f6 0%, #2563eb 100%);
  color: white;
  border: none;
  padding: 0.8rem 1.5rem;
  border-radius: 8px;
  font-weight: 600;
  cursor: pointer;
  transition: transform 0.2s, box-shadow 0.2s;
}

.btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(37, 99, 235, 0.4);
}

.btn:active {
  transform: translateY(0);
}
</style>
