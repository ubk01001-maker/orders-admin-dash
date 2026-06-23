<template>
  <div class="kpi-card">
    <div class="kpi-icon-container" :class="themeClass">
      <!-- Blue Document/List Icon -->
      <svg v-if="icon === 'total'" width="20" height="20" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path d="M19 3H5C3.9 3 3 3.9 3 5V19C3 20.1 3.9 21 5 21H19C20.1 21 21 20.1 21 19V5C21 3.9 20.1 3 19 3ZM19 19H5V5H19V19Z" fill="currentColor"/>
        <path d="M7 7H17V9H7V7ZM7 11H17V13H7V11ZM7 15H14V17H7V15Z" fill="currentColor"/>
      </svg>

      <!-- Green Check/Completed Icon -->
      <svg v-else-if="icon === 'completed'" width="20" height="20" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path d="M9 16.17L4.83 12L3.41 13.41L9 19L21 7L19.59 5.59L9 16.17Z" fill="currentColor" stroke="currentColor" stroke-width="1"/>
      </svg>

      <!-- Orange Clock/In-Progress Icon -->
      <svg v-else-if="icon === 'pending'" width="20" height="20" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path d="M11.99 2C6.47 2 2 6.48 2 12C2 17.52 6.47 22 11.99 22C17.52 22 22 17.52 22 12C22 6.48 17.52 2 11.99 2ZM12 20C7.58 20 4 16.42 4 12C4 7.58 7.58 4 12 4C16.42 4 20 7.58 20 12C20 16.42 16.42 20 12 20Z" fill="currentColor"/>
        <path d="M12.5 7H11V13L16.25 16.15L17 14.92L12.5 12.25V7Z" fill="currentColor"/>
      </svg>

      <!-- Red Cross/Cancelled Icon -->
      <svg v-else-if="icon === 'cancelled'" width="20" height="20" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path d="M12 2C6.47 2 2 6.48 2 12C2 17.52 6.47 22 12 22C17.52 22 22 17.52 22 12C22 6.48 17.52 2 12 2ZM12 20C7.58 20 4 16.42 4 12C4 7.58 7.58 4 12 4C16.42 4 20 7.58 20 12C20 16.42 16.42 20 12 20Z" fill="currentColor"/>
        <path d="M15.59 7L12 10.59L8.41 7L7 8.41L10.59 12L7 15.59L8.41 17L12 13.41L15.59 17L17 15.59L13.41 12L17 8.41L15.59 7Z" fill="currentColor"/>
      </svg>

      <!-- Purple Exclamation/Delayed Icon -->
      <svg v-else-if="icon === 'delayed'" width="20" height="20" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
        <path d="M12 2C6.48 2 2 6.48 2 12C2 17.52 6.48 22 12 22C17.52 22 22 17.52 22 12C22 6.48 17.52 2 12 2ZM12 20C7.58 20 4 16.42 4 12C4 7.58 7.58 4 12 4C16.42 4 20 7.58 20 12C20 16.42 16.42 20 12 20Z" fill="currentColor"/>
        <path d="M11 7H13V13H11V7ZM11 15H13V17H11V15Z" fill="currentColor"/>
      </svg>
    </div>
    <div class="kpi-info">
      <span class="kpi-label">{{ label }}</span>
      <div class="kpi-value-row">
        <span class="kpi-value">{{ formattedValue }}</span>
        <span v-if="percentage" class="kpi-percentage">{{ percentage }}</span>
      </div>
      <div v-if="showTrend && trendValue" class="kpi-trend" :class="trendClass">
        <span class="trend-arrow">{{ trendArrow }}</span>
        <span class="trend-value">{{ trendValue }}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'KpiCard',
  props: {
    label: {
      type: String,
      required: true
    },
    value: {
      type: [String, Number],
      required: true
    },
    percentage: {
      type: String,
      default: ''
    },
    trendValue: {
      type: String,
      default: ''
    },
    trendType: {
      type: String, // 'up', 'down', 'neutral'
      default: 'neutral'
    },
    trendColor: {
      type: String, // 'green', 'red', 'grey'
      default: 'grey'
    },
    icon: {
      type: String, // 'total', 'completed', 'pending', 'cancelled', 'delayed'
      default: 'total'
    },
    showTrend: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      displayValue: 0
    }
  },
  watch: {
    value: {
      immediate: true,
      handler(newVal) {
        this.animateValue(newVal);
      }
    }
  },
  methods: {
    animateValue(target) {
      const start = this.displayValue || 0;
      const end = Number(target);
      if (isNaN(end)) {
        this.displayValue = target;
        return;
      }
      const duration = 800; // ms
      const startTime = performance.now();
      const step = (now) => {
        const elapsed = now - startTime;
        const progress = Math.min(elapsed / duration, 1);
        const ease = progress * (2 - progress); // easeOutQuad
        this.displayValue = Math.round(start + (end - start) * ease);
        if (progress < 1) {
          requestAnimationFrame(step);
        } else {
          this.displayValue = end;
        }
      };
      requestAnimationFrame(step);
    }
  },
  computed: {
    formattedValue() {
      if (typeof this.displayValue === 'number') {
        return this.displayValue.toLocaleString('ru-RU');
      }
      const parsed = Number(this.displayValue);
      if (!isNaN(parsed)) {
        return parsed.toLocaleString('ru-RU');
      }
      return this.displayValue;
    },
    themeClass() {
      return `theme-${this.icon}`;
    },
    trendClass() {
      return `trend-${this.trendColor}`;
    },
    trendArrow() {
      if (this.trendType === 'up') return '↑';
      if (this.trendType === 'down') return '↓';
      return '→';
    }
  }
}
</script>

<style scoped>
.kpi-card {
  background: var(--card-bg);
  border-radius: 12px;
  padding: 20px;
  display: flex;
  align-items: flex-start;
  gap: 16px;
  box-shadow: 0 4px 6px -1px var(--shadow-color), 0 2px 4px -1px var(--shadow-color), 0 0 0 1px var(--border-color);
  transition: transform 0.2s ease, box-shadow 0.2s ease, background-color 0.3s ease;
  flex: 1;
  min-width: 200px;
}

.kpi-card:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 15px -3px var(--shadow-hover), 0 4px 6px -2px var(--shadow-hover), 0 0 0 1px var(--border-color);
}

.kpi-icon-container {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 44px;
  height: 44px;
  border-radius: 50%;
  flex-shrink: 0;
}

/* Theme Colors for Icons */
.theme-total {
  background-color: #eff6ff;
  color: #3b82f6;
}

[data-theme="dark"] .theme-total {
  background-color: rgba(59, 130, 246, 0.15);
}

.theme-completed {
  background-color: #ecfdf5;
  color: #10b981;
}

[data-theme="dark"] .theme-completed {
  background-color: rgba(16, 185, 129, 0.15);
}

.theme-pending {
  background-color: #fffbeb;
  color: #f59e0b;
}

[data-theme="dark"] .theme-pending {
  background-color: rgba(245, 158, 11, 0.15);
}

.theme-cancelled {
  background-color: #fef2f2;
  color: #ef4444;
}

[data-theme="dark"] .theme-cancelled {
  background-color: rgba(239, 68, 68, 0.15);
}

.theme-delayed {
  background-color: #f5f3ff;
  color: #8b5cf6;
}

[data-theme="dark"] .theme-delayed {
  background-color: rgba(139, 92, 246, 0.15);
}

.kpi-info {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.kpi-label {
  font-size: 11px;
  text-transform: uppercase;
  color: var(--text-secondary);
  font-weight: 700;
  letter-spacing: 0.05em;
}

.kpi-value-row {
  display: flex;
  align-items: baseline;
  gap: 8px;
}

.kpi-value {
  font-size: 28px;
  font-weight: 700;
  color: var(--text-primary);
  line-height: 1.2;
}

.kpi-percentage {
  font-size: 13px;
  font-weight: 500;
  color: var(--text-secondary);
}

.kpi-trend {
  display: flex;
  align-items: center;
  gap: 4px;
  font-size: 12px;
  font-weight: 600;
  margin-top: 2px;
}

.trend-green {
  color: #10b981;
}

.trend-red {
  color: #ef4444;
}

.trend-grey {
  color: #64748b;
}

.trend-arrow {
  font-size: 14px;
  line-height: 1;
}
</style>
