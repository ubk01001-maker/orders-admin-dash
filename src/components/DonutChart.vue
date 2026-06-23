<template>
  <div class="donut-card">
    <h3 class="donut-title">Status bo'yicha</h3>
    
    <div class="donut-body">
      <!-- SVG Donut Chart -->
      <div class="chart-container">
        <svg width="200" height="200" viewBox="0 0 120 120" class="donut-svg">
          <!-- Background track (optional but nice) -->
          <circle
            cx="60"
            cy="60"
            r="40"
            fill="transparent"
            stroke="var(--border-color)"
            stroke-width="12"
          />
          <!-- Slices -->
          <circle
            v-for="(slice, index) in computedSlices"
            :key="index"
            cx="60"
            cy="60"
            r="40"
            fill="transparent"
            :stroke="slice.color"
            stroke-width="12"
            :stroke-dasharray="`${slice.strokeLength} ${circumference}`"
            :transform="`rotate(${slice.angle} 60 60)`"
            class="donut-segment"
            :class="{ 'is-hovered': activeIndex === index }"
            @mouseenter="setActive(index)"
            @mouseleave="clearActive()"
          />
        </svg>

        <!-- Center Label -->
        <div class="center-label">
          <span class="center-val">{{ formatNumber(currentTotal) }}</span>
          <span class="center-lbl">{{ currentLabel }}</span>
        </div>
      </div>

      <!-- Legend -->
      <div class="legend-container">
        <div
          v-for="(item, index) in legendItems"
          :key="index"
          class="legend-item"
          :class="{ 'is-hovered': activeIndex === index }"
          @mouseenter="setActive(index)"
          @mouseleave="clearActive()"
        >
          <div class="legend-info">
            <span class="legend-dot" :style="{ backgroundColor: item.color }"></span>
            <span class="legend-name">{{ item.label }}</span>
          </div>
          <div class="legend-values">
            <span class="legend-count">{{ formatNumber(item.value) }}</span>
            <span class="legend-pct">({{ item.pct }}%)</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'DonutChart',
  props: {
    data: {
      type: Array,
      default: () => [
        { label: 'Bajarilgan', value: 1038, color: '#10b981' },
        { label: 'Jarayonda', value: 156, color: '#f59e0b' },
        { label: 'Bekor qilingan', value: 62, color: '#ef4444' },
        { label: 'Kechiktirilgan', value: 28, color: '#8b5cf6' }
      ]
    }
  },
  data() {
    return {
      activeIndex: null,
      radius: 40
    }
  },
  computed: {
    totalValue() {
      return this.data.reduce((acc, curr) => acc + curr.value, 0);
    },
    circumference() {
      return 2 * Math.PI * this.radius;
    },
    computedSlices() {
      let accumulatedValue = 0;
      return this.data.map(item => {
        const percent = this.totalValue > 0 ? item.value / this.totalValue : 0;
        const strokeLength = percent * this.circumference;
        const angle = (accumulatedValue / this.totalValue) * 360 - 90;
        accumulatedValue += item.value;
        return {
          ...item,
          percent,
          strokeLength,
          angle
        }
      });
    },
    legendItems() {
      return this.data.map(item => {
        const pct = this.totalValue > 0 ? Math.round((item.value / this.totalValue) * 100) : 0;
        return {
          ...item,
          pct
        }
      });
    },
    currentTotal() {
      if (this.activeIndex !== null) {
        return this.data[this.activeIndex].value;
      }
      return this.totalValue;
    },
    currentLabel() {
      if (this.activeIndex !== null) {
        return this.data[this.activeIndex].label;
      }
      return 'jami';
    }
  },
  methods: {
    setActive(index) {
      this.activeIndex = index;
    },
    clearActive() {
      this.activeIndex = null;
    },
    formatNumber(num) {
      if (typeof num === 'number') {
        return num.toLocaleString('ru-RU');
      }
      return num;
    }
  }
}
</script>

<style scoped>
.donut-card {
  background: var(--card-bg);
  border-radius: 12px;
  padding: 24px;
  box-shadow: 0 4px 6px -1px var(--shadow-color), 0 2px 4px -1px var(--shadow-color), 0 0 0 1px var(--border-color);
  height: 100%;
  display: flex;
  flex-direction: column;
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
}

.donut-title {
  font-size: 16px;
  font-weight: 700;
  color: var(--text-primary);
  margin: 0 0 20px 0;
}

.donut-body {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
  flex: 1;
}

.chart-container {
  position: relative;
  width: 200px;
  height: 200px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.donut-svg {
  transform: rotate(0deg);
  transition: transform 0.3s ease;
}

.donut-segment {
  transition: stroke-width 0.2s ease, opacity 0.2s ease;
  cursor: pointer;
}

.donut-segment.is-hovered {
  stroke-width: 16px;
}

.center-label {
  position: absolute;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  pointer-events: none;
}

.center-val {
  font-size: 28px;
  font-weight: 700;
  color: var(--text-primary);
  line-height: 1.1;
  transition: all 0.2s ease;
}

.center-lbl {
  font-size: 12px;
  color: var(--text-secondary);
  font-weight: 600;
  text-transform: lowercase;
  margin-top: 2px;
}

.legend-container {
  width: 100%;
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.legend-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 6px 10px;
  border-radius: 8px;
  transition: background-color 0.2s ease;
  cursor: pointer;
}

.legend-item:hover,
.legend-item.is-hovered {
  background-color: var(--bg-color);
}

.legend-info {
  display: flex;
  align-items: center;
  gap: 8px;
}

.legend-dot {
  width: 8px;
  height: 8px;
  border-radius: 50%;
  flex-shrink: 0;
}

.legend-name {
  font-size: 12px;
  color: var(--text-secondary);
  font-weight: 500;
}

.legend-values {
  display: flex;
  align-items: center;
  gap: 4px;
  font-size: 12px;
  font-weight: 600;
}

.legend-count {
  color: var(--text-primary);
}

.legend-pct {
  color: var(--text-secondary);
}
</style>
