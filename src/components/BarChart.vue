<template>
  <div class="chart-card">
    <div class="chart-header">
      <h3 class="chart-title">Buyurtmalar dinamikasi</h3>
      <div class="toggle-group">
        <button
          v-for="mode in modes"
          :key="mode.value"
          class="toggle-btn"
          :class="{ 'is-active': activeMode === mode.value }"
          @click="changeMode(mode.value)"
        >
          {{ mode.label }}
        </button>
      </div>
    </div>
    
    <div class="chart-content">
      <!-- Y-Axis Labels -->
      <div class="y-axis">
        <div v-for="val in yAxisValues" :key="val" class="y-label">
          {{ val }}
        </div>
      </div>

      <!-- Chart Bars Area -->
      <div class="bars-container">
        <!-- Horizontal Grid Lines -->
        <div class="grid-lines">
          <div v-for="n in 5" :key="n" class="grid-line"></div>
        </div>

        <!-- Bars -->
        <div class="bars-wrapper">
          <div
            v-for="(item, index) in chartData"
            :key="index"
            class="bar-column"
          >
            <div class="bar-track">
              <!-- Actual Bar -->
              <div
                class="bar-fill"
                :style="{ height: `${getBarHeightPercent(item.value)}%` }"
              >
                <!-- Tooltip -->
                <div class="bar-tooltip">
                  <div class="tooltip-val">{{ item.value }} ta</div>
                  <div class="tooltip-lbl">{{ item.label }}</div>
                </div>
              </div>
            </div>
            <div class="x-label">{{ item.label }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'BarChart',
  props: {
    dataKun: {
      type: Array,
      default: () => [
        { label: '08:00', value: 4 },
        { label: '10:00', value: 10 },
        { label: '12:00', value: 14 },
        { label: '14:00', value: 11 },
        { label: '16:00', value: 8 },
        { label: '18:00', value: 3 }
      ]
    },
    dataHafta: {
      type: Array,
      default: () => [
        { label: 'Dush', value: 120 },
        { label: 'Sesh', value: 150 },
        { label: 'Chor', value: 180 },
        { label: 'Pay', value: 140 },
        { label: 'Jum', value: 160 },
        { label: 'Shan', value: 90 },
        { label: 'Yak', value: 45 }
      ]
    },
    dataOy: {
      type: Array,
      default: () => [
        { label: 'Yan', value: 850 },
        { label: 'Fev', value: 920 },
        { label: 'Mar', value: 1050 },
        { label: 'Apr', value: 1150 },
        { label: 'May', value: 1250 },
        { label: 'Iyun', value: 1284 }
      ]
    },
    dataYil: {
      type: Array,
      default: () => [
        { label: '2022-y', value: 5400 },
        { label: '2023-y', value: 6800 },
        { label: '2024-y', value: 8500 },
        { label: '2025-y', value: 10200 },
        { label: '2026-y', value: 12840 }
      ]
    }
  },
  data() {
    return {
      activeMode: 'kun', // 'kun', 'hafta', 'oy', 'yil'
      modes: [
        { label: 'Yil', value: 'yil' },
        { label: 'Oy', value: 'oy' },
        { label: 'Hafta', value: 'hafta' },
        { label: 'Kun', value: 'kun' }
      ]
    }
  },
  computed: {
    chartData() {
      if (this.activeMode === 'yil') return this.dataYil;
      if (this.activeMode === 'oy') return this.dataOy;
      if (this.activeMode === 'hafta') return this.dataHafta;
      return this.dataKun;
    },
    maxValue() {
      const vals = this.chartData.map(d => d.value);
      return Math.max(...vals, 10);
    },
    // Rounded max scale
    maxScale() {
      const max = this.maxValue;
      if (max <= 20) return 20;
      if (max <= 200) return 200;
      if (max <= 2000) return 2000;
      return Math.ceil(max / 5000) * 5000;
    },
    yAxisValues() {
      const scale = this.maxScale;
      return [scale, scale * 0.75, scale * 0.5, scale * 0.25, 0];
    }
  },
  methods: {
    changeMode(mode) {
      this.activeMode = mode;
      this.$emit('mode-change', mode);
    },
    getBarHeightPercent(val) {
      return (val / this.maxScale) * 100;
    }
  }
}
</script>

<style scoped>
.chart-card {
  background: var(--card-bg);
  border-radius: 12px;
  padding: 24px;
  box-shadow: 0 4px 6px -1px var(--shadow-color), 0 2px 4px -1px var(--shadow-color), 0 0 0 1px var(--border-color);
  display: flex;
  flex-direction: column;
  height: 100%;
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
}

.chart-header {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 24px;
}

.chart-title {
  font-size: 16px;
  font-weight: 700;
  color: var(--text-primary);
  margin: 0;
}

.toggle-group {
  display: flex;
  background: var(--bg-color);
  border-radius: 8px;
  padding: 3px;
  transition: background-color 0.3s ease;
}

.toggle-btn {
  background: transparent;
  border: none;
  font-size: 12px;
  font-weight: 600;
  color: var(--text-secondary);
  padding: 6px 12px;
  border-radius: 6px;
  cursor: pointer;
  transition: all 0.2s ease;
  outline: none;
}

.toggle-btn.is-active {
  background: var(--card-bg);
  color: var(--text-primary);
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.1);
}

.chart-content {
  display: flex;
  position: relative;
  height: 200px;
  gap: 16px;
  margin-top: 10px;
}

.y-axis {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  height: calc(100% - 24px); /* aligns with bars */
  font-size: 11px;
  color: var(--text-secondary);
  width: 28px;
  text-align: right;
  font-weight: 500;
}

.bars-container {
  position: relative;
  flex: 1;
  height: 100%;
}

.grid-lines {
  position: absolute;
  top: 6px;
  left: 0;
  right: 0;
  height: calc(100% - 30px);
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  pointer-events: none;
}

.grid-line {
  border-top: 1px dashed var(--border-color);
  width: 100%;
}

.bars-wrapper {
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  justify-content: space-around;
  align-items: flex-end;
  height: 100%;
  z-index: 10;
}

.bar-column {
  display: flex;
  flex-direction: column;
  align-items: center;
  height: 100%;
  width: 50px;
  justify-content: flex-end;
}

.bar-track {
  height: calc(100% - 30px);
  width: 100%;
  display: flex;
  align-items: flex-end;
  justify-content: center;
  position: relative;
}

.bar-fill {
  width: 24px;
  background: #3b82f6;
  border-radius: 6px 6px 0 0;
  position: relative;
  cursor: pointer;
  transition: height 0.4s cubic-bezier(0.16, 1, 0.3, 1), background-color 0.2s ease;
}

.bar-fill:hover {
  background: #2563eb;
}

.x-label {
  font-size: 11px;
  color: var(--text-secondary);
  font-weight: 500;
  margin-top: 8px;
  text-align: center;
  white-space: nowrap;
}

/* Tooltip on hover */
.bar-tooltip {
  position: absolute;
  bottom: 100%;
  left: 50%;
  transform: translateX(-50%) translateY(-6px);
  background: #1e293b;
  color: #ffffff;
  padding: 6px 10px;
  border-radius: 6px;
  font-size: 11px;
  pointer-events: none;
  opacity: 0;
  visibility: hidden;
  transition: opacity 0.2s ease, transform 0.2s ease, visibility 0.2s;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
  z-index: 50;
  white-space: nowrap;
}

.bar-tooltip::after {
  content: '';
  position: absolute;
  top: 100%;
  left: 50%;
  transform: translateX(-50%);
  border-width: 4px;
  border-style: solid;
  border-color: #1e293b transparent transparent transparent;
}

.bar-fill:hover .bar-tooltip {
  opacity: 1;
  visibility: visible;
  transform: translateX(-50%) translateY(-8px);
}

.tooltip-val {
  font-weight: 700;
}
.tooltip-lbl {
  font-size: 9px;
  color: #94a3b8;
  margin-top: 1px;
}
</style>
