<script setup lang="ts">
defineProps<{
  status: 'todo' | 'doing' | 'done';
}>();
</script>

<template>
  <span :class="['status-badge', status]">
    {{ status.toUpperCase() }}
  </span>
</template>

<style scoped>
.status-badge {
  font-size: 0.65rem;
  padding: 2px 6px;
  border-radius: 4px;
  font-weight: 800;
  text-transform: uppercase;
  display: inline-block;
}

.todo {
  background: #94a3b8;
  color: #0f172a;
}

.doing {
  background: #f59e0b;
  color: #0f172a;
}

.done {
  background: #10b981;
  color: #0f172a;
}
</style>
