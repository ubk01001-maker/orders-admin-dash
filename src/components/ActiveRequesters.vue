<template>
  <div class="requesters-card">
    <div class="card-header">
      <h3 class="card-title">Eng ko'p murojaat qilgan xodimlar</h3>
      <button class="see-all-btn" @click="showModal = true">
        Barchasini ko'rish <span class="arrow">→</span>
      </button>
    </div>

    <div class="requesters-list">
      <div v-for="(req, index) in data.slice(0, 8)" :key="req.creator_id" class="requester-row">
        <!-- Rank Number -->
        <span class="rank-number">{{ index + 1 }}</span>

        <!-- Initials Avatar -->
        <div class="avatar" :style="{ backgroundColor: getAvatarColor(index) }">
          {{ getInitials(req.creator_name) }}
        </div>

        <!-- Name & Department -->
        <div class="info-block">
          <div class="name-row">
            <span class="name">{{ req.creator_name }}</span>
            <span v-if="req.top_service" class="service-badge">{{ req.top_service }}</span>
          </div>
          <div class="dept">{{ req.dep_name }}</div>
        </div>

        <!-- Progress Bar -->
        <div class="progress-block">
          <div class="progress-track">
            <div
              class="progress-bar"
              :style="{ width: `${getProgressWidth(req.order_count)}%` }"
            ></div>
          </div>
        </div>

        <!-- Order Count -->
        <div class="count-block">
          <span class="count-val">{{ formatNumber(req.order_count) }}</span>
        </div>
      </div>
    </div>

    <!-- Modal for showing all requesters -->
    <transition name="fade">
      <div v-if="showModal" class="modal-overlay" @click.self="showModal = false">
        <transition name="zoom">
          <div class="modal-content">
            <div class="modal-header">
              <h3>Barcha Murojaat Qilgan Xodimlar</h3>
              <button class="close-btn" @click="showModal = false">&times;</button>
            </div>
            
            <div class="modal-search">
              <input
                v-model="searchQuery"
                type="text"
                placeholder="Ism, bo'lim yoki xizmat bo'yicha qidirish..."
                class="search-input"
              />
            </div>

            <div class="modal-body">
              <table class="requesters-table">
                <thead>
                  <tr>
                    <th>№</th>
                    <th>Xodim</th>
                    <th>Bo'lim</th>
                    <th>Eng ko'p foydalanilgan xizmat</th>
                    <th>Buyurtmalar</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="(req, i) in filteredRequesters" :key="req.creator_id">
                    <td>{{ i + 1 }}</td>
                    <td>
                      <div class="table-user">
                        <div class="avatar sm" :style="{ backgroundColor: getAvatarColor(i) }">
                          {{ getInitials(req.creator_name) }}
                        </div>
                        <span class="table-name">{{ req.creator_name }}</span>
                      </div>
                    </td>
                    <td>{{ req.dep_name }}</td>
                    <td>
                      <span class="service-badge table-badge">{{ req.top_service }}</span>
                    </td>
                    <td><strong class="table-count">{{ formatNumber(req.order_count) }} ta</strong></td>
                  </tr>
                  <tr v-if="filteredRequesters.length === 0">
                    <td colspan="5" class="empty-row">Xodimlar topilmadi</td>
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
  name: 'ActiveRequesters',
  props: {
    data: {
      type: Array,
      required: true
    }
  },
  data() {
    return {
      showModal: false,
      searchQuery: '',
      avatarColors: ['#2E6DB4', '#27AE60', '#E4A216', '#7B61FF', '#E85D75', '#50C9C3']
    }
  },
  computed: {
    maxOrders() {
      const counts = this.data.map(r => r.order_count);
      return Math.max(...counts, 1);
    },
    filteredRequesters() {
      if (!this.searchQuery.trim()) return this.data;
      const query = this.searchQuery.toLowerCase();
      return this.data.filter(r =>
        r.creator_name.toLowerCase().includes(query) ||
        r.dep_name.toLowerCase().includes(query) ||
        (r.top_service && r.top_service.toLowerCase().includes(query))
      );
    }
  },
  methods: {
    getProgressWidth(count) {
      return (count / this.maxOrders) * 100;
    },
    getAvatarColor(index) {
      return this.avatarColors[index % this.avatarColors.length];
    },
    getInitials(name) {
      if (!name) return '';
      const parts = name.split(' ');
      if (parts.length >= 2) {
        return (parts[0][0] + parts[1][0]).toUpperCase();
      }
      return name.slice(0, 2).toUpperCase();
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
.requesters-card {
  background: var(--card-bg);
  border-radius: 14px;
  padding: 22px;
  border: 1px solid var(--border-color);
  height: 100%;
  display: flex;
  flex-direction: column;
  transition: background-color 0.3s ease, border-color 0.3s ease;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
}

.card-title {
  font-size: 14px;
  font-weight: 600;
  color: var(--text-primary);
  margin: 0;
}

.see-all-btn {
  background: none;
  border: none;
  font-size: 12px;
  font-weight: 600;
  color: #2E6DB4;
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

.requesters-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.requester-row {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 6px 8px;
  transition: background-color 0.2s ease, border-radius 0.2s ease;
}

.requester-row:hover {
  background-color: var(--bg-color);
  border-radius: 8px;
}

/* Rank Number styling */
.rank-number {
  font-size: 12px;
  color: var(--text-secondary);
  opacity: 0.8;
  min-width: 18px;
  font-weight: 500;
}

/* Avatar styling */
.avatar {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  color: #ffffff;
  font-weight: 700;
  font-size: 11px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.05);
}

.avatar.sm {
  width: 28px;
  height: 28px;
  font-size: 10px;
}

/* Info block styling */
.info-block {
  width: 140px;
  flex-shrink: 0;
  display: flex;
  flex-direction: column;
  gap: 2px;
}

.name-row {
  display: flex;
  align-items: center;
  gap: 6px;
  flex-wrap: wrap;
}

.name {
  font-size: 12px;
  font-weight: 500;
  color: var(--text-primary);
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.dept {
  font-size: 11px;
  color: var(--text-secondary);
  opacity: 0.9;
}

.service-badge {
  background-color: var(--bg-color);
  border: 1px solid var(--border-color);
  color: var(--text-secondary);
  font-size: 9px;
  font-weight: 600;
  padding: 1px 4px;
  border-radius: 4px;
  text-transform: uppercase;
}

.service-badge.table-badge {
  font-size: 10px;
  padding: 2px 6px;
}

/* Progress bar styling */
.progress-block {
  flex: 1;
  min-width: 60px;
  display: flex;
  align-items: center;
}

.progress-track {
  height: 6px;
  width: 100%;
  background-color: var(--bg-color);
  border-radius: 20px;
  overflow: hidden;
  transition: background-color 0.3s ease;
}

.progress-bar {
  height: 100%;
  border-radius: 20px;
  background-color: #2E6DB4;
  transition: width 0.8s cubic-bezier(0.16, 1, 0.3, 1);
}

/* Count block styling */
.count-block {
  min-width: 50px;
  text-align: right;
}

.count-val {
  font-size: 12px;
  font-weight: 700;
  color: #2E6DB4;
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
  max-width: 650px;
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
  border-color: #2E6DB4;
  box-shadow: 0 0 0 3px rgba(46, 109, 180, 0.15);
}

.modal-body {
  padding: 16px 24px 24px 24px;
  overflow-y: auto;
  flex: 1;
}

.requesters-table {
  width: 100%;
  border-collapse: collapse;
  text-align: left;
}

.requesters-table th {
  padding: 12px;
  border-bottom: 2px solid var(--border-color);
  font-size: 12px;
  font-weight: 600;
  color: var(--text-secondary);
  text-transform: uppercase;
}

.requesters-table td {
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

.table-count {
  color: #2E6DB4;
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
