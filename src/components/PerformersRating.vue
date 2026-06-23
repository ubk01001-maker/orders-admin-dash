<template>
  <div class="rating-card">
    <div class="card-header">
      <h3 class="card-title">Ijrochilar reytingi</h3>
      <button class="see-all-btn" @click="showModal = true">
        Barchasini ko'rish <span class="arrow">→</span>
      </button>
    </div>

    <div class="performers-list">
      <div v-for="(performer, index) in data.slice(0, 6)" :key="performer.id" class="performer-row">
        <!-- Rank Number -->
        <span class="rank-number">{{ index + 1 }}</span>

        <!-- Initials Avatar -->
        <div class="avatar" :style="{ backgroundColor: performer.avatarColor }">
          {{ performer.initials }}
        </div>

        <!-- Name & Department -->
        <div class="info-block">
          <div class="name">{{ performer.name }}</div>
          <div class="dept">{{ performer.department }}</div>
        </div>

        <!-- Bar / Rating Score -->
        <div class="score-block">
          <div class="progress-track">
            <div
              class="progress-bar"
              :style="{ width: `${getProgressWidth(performer.ordersCount)}%`, backgroundColor: getBarColor(performer.rating) }"
            ></div>
          </div>
        </div>

        <!-- Order Count & Stars -->
        <div class="meta-block">
          <div class="orders-count">{{ formatNumber(performer.ordersCount) }} ta</div>
          <div class="rating-stars">
            <span class="star-icon">★</span>
            <span class="rating-val">{{ performer.rating.toFixed(1) }}</span>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal for showing all performers -->
    <transition name="fade">
      <div v-if="showModal" class="modal-overlay" @click.self="showModal = false">
        <transition name="zoom">
          <div class="modal-content">
            <div class="modal-header">
              <h3>Barcha Ijrochilar Reytingi</h3>
              <button class="close-btn" @click="showModal = false">&times;</button>
            </div>
            
            <div class="modal-search">
              <input
                v-model="searchQuery"
                type="text"
                placeholder="Ism yoki bo'lim bo'yicha qidirish..."
                class="search-input"
              />
            </div>

            <div class="modal-body">
              <table class="performers-table">
                <thead>
                  <tr>
                    <th>№</th>
                    <th>Ijrochi</th>
                    <th>Bo'lim</th>
                    <th>Buyurtmalar</th>
                    <th>Reyting</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="(perf, i) in filteredPerformers" :key="perf.id">
                    <td>{{ i + 1 }}</td>
                    <td>
                      <div class="table-user">
                        <div class="avatar sm" :style="{ backgroundColor: perf.avatarColor }">
                          {{ perf.initials }}
                        </div>
                        <span class="table-name">{{ perf.name }}</span>
                      </div>
                    </td>
                    <td>{{ perf.department }}</td>
                    <td><strong>{{ formatNumber(perf.ordersCount) }} ta</strong></td>
                    <td>
                      <div class="table-rating">
                        <span class="star-icon">★</span>
                        <span>{{ perf.rating.toFixed(1) }}</span>
                      </div>
                    </td>
                  </tr>
                  <tr v-if="filteredPerformers.length === 0">
                    <td colspan="5" class="empty-row">Ijrochilar topilmadi</td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </transition>
      </div>
    </transition>
  </div>
</template>

<script>
export default {
  name: 'PerformersRating',
  props: {
    data: {
      type: Array,
      default: () => [
        { id: 1, name: 'Aziz Karimov', department: 'IT bo\'limi', initials: 'AK', ordersCount: 87, rating: 4.9, avatarColor: '#3b82f6' },
        { id: 2, name: 'Dilnoza Rahimova', department: 'Moliya bo\'limi', initials: 'DR', ordersCount: 74, rating: 4.8, avatarColor: '#10b981' },
        { id: 3, name: 'Bobur Toshmatov', department: 'Xo\'jalik bo\'limi', initials: 'BT', ordersCount: 68, rating: 4.7, avatarColor: '#f59e0b' },
        { id: 4, name: 'Madina Usmonova', department: 'HR bo\'limi', initials: 'MU', ordersCount: 61, rating: 4.5, avatarColor: '#8b5cf6' },
        { id: 5, name: 'Jasur Aliyev', department: 'IT bo\'limi', initials: 'JA', ordersCount: 53, rating: 4.3, avatarColor: '#ef4444' },
        { id: 6, name: 'Nodira Saidova', department: 'Avtotransport', initials: 'NS', ordersCount: 47, rating: 4.1, avatarColor: '#eab308' },
        { id: 7, name: 'Farhod Alimov', department: 'Moliya bo\'limi', initials: 'FA', ordersCount: 39, rating: 4.0, avatarColor: '#14b8a6' },
        { id: 8, name: 'Elena Petrova', department: 'HR bo\'limi', initials: 'EP', ordersCount: 32, rating: 3.9, avatarColor: '#ec4899' }
      ]
    }
  },
  data() {
    return {
      showModal: false,
      searchQuery: ''
    }
  },
  computed: {
    maxOrders() {
      const counts = this.data.map(p => p.ordersCount);
      return Math.max(...counts, 1);
    },
    filteredPerformers() {
      if (!this.searchQuery.trim()) return this.data;
      const query = this.searchQuery.toLowerCase();
      return this.data.filter(p =>
        p.name.toLowerCase().includes(query) ||
        p.department.toLowerCase().includes(query)
      );
    }
  },
  methods: {
    getProgressWidth(orders) {
      return (orders / this.maxOrders) * 100;
    },
    getBarColor(rating) {
      if (rating >= 4.6) return '#10b981'; // Green
      if (rating >= 4.4) return '#eab308'; // Yellow
      return '#f59e0b'; // Orange
    },
    formatNumber(num) {
      return num.toLocaleString('ru-RU');
    }
  }
}
</script>

<style scoped>
.rating-card {
  background: var(--card-bg);
  border-radius: 12px;
  padding: 24px;
  box-shadow: 0 4px 6px -1px var(--shadow-color), 0 2px 4px -1px var(--shadow-color), 0 0 0 1px var(--border-color);
  height: 100%;
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.card-title {
  font-size: 16px;
  font-weight: 700;
  color: var(--text-primary);
  margin: 0;
}

.see-all-btn {
  background: none;
  border: none;
  font-size: 12px;
  font-weight: 600;
  color: #2563eb;
  cursor: pointer;
  display: flex;
  align-items: center;
  gap: 4px;
  padding: 4px 8px;
  border-radius: 4px;
  transition: all 0.2s ease;
}

.see-all-btn:hover {
  background-color: var(--bg-color);
}

.see-all-btn:hover .arrow {
  transform: translateX(3px);
}

.arrow {
  transition: transform 0.2s ease;
}

.performers-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.performer-row {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 6px 8px;
  transition: background-color 0.2s ease, border-radius 0.2s ease;
}

.performer-row:hover {
  background-color: var(--hover-bg);
  border-radius: 8px;
}

.rank-number {
  font-size: 13px;
  font-weight: 600;
  color: var(--text-secondary);
  width: 16px;
  text-align: center;
}

.avatar {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  color: #ffffff;
  font-weight: 700;
  font-size: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.05);
}

.avatar.sm {
  width: 28px;
  height: 28px;
  font-size: 10px;
}

.info-block {
  width: 120px;
  flex-shrink: 0;
}

.name {
  font-size: 13px;
  font-weight: 600;
  color: var(--text-primary);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.dept {
  font-size: 11px;
  color: var(--text-secondary);
}

.score-block {
  flex: 1;
  min-width: 60px;
  display: flex;
  align-items: center;
}

.progress-track {
  height: 6px;
  width: 100%;
  background-color: var(--bg-color);
  border-radius: 9999px;
  overflow: hidden;
  transition: background-color 0.3s ease;
}

.progress-bar {
  height: 100%;
  border-radius: 9999px;
  transition: width 0.8s cubic-bezier(0.16, 1, 0.3, 1);
}

.meta-block {
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: 12px;
  min-width: 80px;
}

.orders-count {
  font-size: 12px;
  color: var(--text-secondary);
  font-weight: 500;
  text-align: right;
  white-space: nowrap;
}

.rating-stars {
  display: flex;
  align-items: center;
  gap: 2px;
  font-size: 12px;
  font-weight: 700;
  color: #f59e0b;
  min-width: 38px;
  justify-content: flex-end;
}

.star-icon {
  font-size: 12px;
}

.rating-val {
  color: var(--text-secondary);
}

/* Modal Styling */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(15, 23, 42, 0.4);
  backdrop-filter: blur(4px);
  z-index: 1000;
  display: flex;
  align-items: center;
  justify-content: center;
}

.modal-content {
  background: var(--card-bg);
  border-radius: 16px;
  width: 90%;
  max-width: 600px;
  max-height: 80vh;
  box-shadow: 0 25px 50px -12px var(--shadow-color);
  display: flex;
  flex-direction: column;
  overflow: hidden;
  transition: background-color 0.3s ease;
}

.modal-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 24px;
  border-bottom: 1px solid var(--border-color);
}

.modal-header h3 {
  font-size: 18px;
  font-weight: 700;
  color: var(--text-primary);
  margin: 0;
}

.close-btn {
  background: none;
  border: none;
  font-size: 24px;
  color: var(--text-secondary);
  cursor: pointer;
  padding: 4px;
  line-height: 1;
  transition: color 0.2s;
}

.close-btn:hover {
  color: var(--text-primary);
}

.modal-search {
  padding: 16px 24px 8px 24px;
}

.search-input {
  width: 100%;
  padding: 10px 14px;
  border: 1px solid var(--border-color);
  background: var(--bg-color);
  color: var(--text-primary);
  border-radius: 8px;
  font-size: 14px;
  outline: none;
  transition: border-color 0.2s, box-shadow 0.2s, background-color 0.3s ease, color 0.3s ease;
  box-sizing: border-box;
}

.search-input:focus {
  border-color: #3b82f6;
  box-shadow: 0 0 0 3px rgba(59, 130, 246, 0.1);
}

.modal-body {
  padding: 16px 24px 24px 24px;
  overflow-y: auto;
  flex: 1;
}

.performers-table {
  width: 100%;
  border-collapse: collapse;
  text-align: left;
}

.performers-table th {
  padding: 12px;
  border-bottom: 2px solid var(--border-color);
  font-size: 12px;
  font-weight: 600;
  color: var(--text-secondary);
  text-transform: uppercase;
}

.performers-table td {
  padding: 14px 12px;
  border-bottom: 1px solid var(--border-color);
  font-size: 13.5px;
  color: var(--text-primary);
  vertical-align: middle;
}

.table-user {
  display: flex;
  align-items: center;
  gap: 10px;
}

.table-name {
  font-weight: 600;
  color: var(--text-primary);
}

.table-rating {
  display: flex;
  align-items: center;
  gap: 4px;
  color: #f59e0b;
  font-weight: 700;
}

.empty-row {
  text-align: center;
  color: var(--text-secondary);
  padding: 30px !important;
}

/* Modal Animations */
.fade-enter-active, .fade-leave-active {
  transition: opacity 0.25s ease;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}

.zoom-enter-active, .zoom-leave-active {
  transition: transform 0.25s cubic-bezier(0.34, 1.56, 0.64, 1), opacity 0.25s ease;
}
.zoom-enter, .zoom-leave-to {
  transform: scale(0.95);
  opacity: 0;
}
</style>
