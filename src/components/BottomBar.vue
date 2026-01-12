<template>
  <nav class="bottom-bar">
    <router-link to="/documents" class="bar-item" :class="{ active: isActive('/documents') }">
      <svg class="icon" viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor"><path d="M3 7h18M3 12h18M3 17h18" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></svg>
      <span>Documents</span>
    </router-link>

    <router-link to="/feedbacks" class="bar-item" :class="{ active: isActive('/feedbacks') }">
      <svg class="icon" viewBox="0 0 24 24" width="20" height="20" fill="none" stroke="currentColor"><path d="M21 15a2 2 0 0 1-2 2H7l-4 4V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2z" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></svg>
      <span>Feedbacks</span>
    </router-link>
  </nav>
</template>

<script setup>
import { useRoute } from 'vue-router';
const route = useRoute();
const isActive = (path) => route.path === path;
</script>

<style scoped>
.bottom-bar {
  position: fixed;
  left: 12px;
  right: 12px;
  bottom: 12px;
  height: 56px;
  background: linear-gradient(90deg, #ffffff, #f8fafc);
  border-radius: 14px;
  box-shadow: 0 8px 24px rgba(16,24,40,0.08);
  display: flex;
  gap: 8px;
  padding: 6px;
  align-items: center;
  justify-content: center;
  z-index: 60;
}
.bar-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  border-radius: 10px;
  color: #374151;
  text-decoration: none;
  font-weight: 600;
  background: transparent;
  transition: all .16s ease;
}
.bar-item .icon { color: #6b7280; }
.bar-item.active {
  background: linear-gradient(90deg,#667eea,#764ba2);
  color: #fff;
}
.bar-item.active .icon { color: #fff; }

@media (max-width: 640px) {
  .bottom-bar { left: 8px; right: 8px; bottom: 10px; height: 60px; }
  .bar-item { padding: 10px 12px; font-size: 14px; }
}
</style>
