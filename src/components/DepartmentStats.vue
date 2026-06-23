<template>
  <div class="dept-card">
    <h3 class="dept-title">Departamentlar</h3>
    <div class="dept-list">
      <div v-for="(item, index) in data" :key="index" class="dept-item">
        <div class="dept-meta">
          <span class="dept-label">{{ item.label }}</span>
          <span class="dept-value">{{ formatNumber(item.value) }}</span>
        </div>
        <div class="progress-track">
          <div
            class="progress-bar"
            :style="{ width: `${getProgressWidth(item.value)}%`, backgroundColor: item.color }"
          ></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DepartmentStats',
  props: {
    data: {
      type: Array,
      default: () => [
        { label: 'IT bo\'limi', value: 412, color: '#3b82f6' },
        { label: 'Moliya bo\'limi', value: 287, color: '#10b981' },
        { label: 'Xo\'jalik', value: 234, color: '#f59e0b' },
        { label: 'HR bo\'limi', value: 198, color: '#8b5cf6' },
        { label: 'Avtotransport', value: 153, color: '#eab308' }
      ]
    }
  },
  computed: {
    maxValue() {
      const vals = this.data.map(d => d.value);
      return Math.max(...vals, 1);
    }
  },
  methods: {
    getProgressWidth(val) {
      return (val / this.maxValue) * 100;
    },
    formatNumber(num) {
      return num.toLocaleString('ru-RU');
    }
  }
}
</script>

<style scoped>
.dept-card {
  background: var(--card-bg);
  border-radius: 12px;
  padding: 20px;
  box-shadow: 0 4px 6px -1px var(--shadow-color), 0 2px 4px -1px var(--shadow-color), 0 0 0 1px var(--border-color);
  height: 100%;
  display: flex;
  flex-direction: column;
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
}

.dept-title {
  font-size: 16px;
  font-weight: 700;
  color: var(--text-primary);
  margin: 0 0 16px 0;
}

.dept-list {
  display: flex;
  flex-direction: column;
  gap: 10px;
  flex: 1;
  justify-content: space-around;
}

.dept-item {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.dept-meta {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.dept-label {
  font-size: 13px;
  color: var(--text-secondary);
  font-weight: 600;
}

.dept-value {
  font-size: 13px;
  color: var(--text-primary);
  font-weight: 700;
}

.progress-track {
  height: 6px;
  background-color: var(--bg-color);
  border-radius: 9999px;
  overflow: hidden;
  position: relative;
  transition: background-color 0.3s ease;
}

.progress-bar {
  height: 100%;
  border-radius: 9999px;
  transition: width 0.8s cubic-bezier(0.16, 1, 0.3, 1);
}
</style>
