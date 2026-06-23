<template>
  <div class="delayed-card">
    <h3 class="card-title">Kechiktirilgan buyurtmalar</h3>

    <div class="orders-list">
      <div v-for="order in data.slice(0, 5)" :key="order.id" class="order-row">
        <!-- Order ID/Number -->
        <span class="order-number">#{{ order.number }}</span>
        <!-- Client/Employee Name -->
        <span class="order-client">{{ order.client }}</span>
        <!-- Delay Badge -->
        <span class="delay-badge">+{{ order.delayDays }} kun</span>
      </div>
    </div>

    <div class="card-footer">
      <button class="see-all-btn" @click="showModal = true">
        Barchasini ko'rish <span class="arrow">→</span>
      </button>
    </div>

    <!-- Modal for showing all delayed orders -->
    <transition name="fade">
      <div v-if="showModal" class="modal-overlay" @click.self="showModal = false">
        <transition name="zoom">
          <div class="modal-content">
            <div class="modal-header">
              <h3>Barcha Kechiktirilgan Buyurtmalar</h3>
              <button class="close-btn" @click="showModal = false">&times;</button>
            </div>
            
            <div class="modal-search">
              <input
                v-model="searchQuery"
                type="text"
                placeholder="Buyurtma ID yoki ism bo'yicha qidirish..."
                class="search-input"
              />
            </div>

            <div class="modal-body">
              <table class="orders-table">
                <thead>
                  <tr>
                    <th>Buyurtma №</th>
                    <th>Xaridor / Mas'ul</th>
                    <th>Xizmat turi</th>
                    <th>Kechikish muddati</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="order in filteredOrders" :key="order.id">
                    <td><strong class="order-num-text">#{{ order.number }}</strong></td>
                    <td>{{ order.client }}</td>
                    <td>{{ order.serviceType }}</td>
                    <td>
                      <span class="delay-badge danger">+{{ order.delayDays }} kun</span>
                    </td>
                  </tr>
                  <tr v-if="filteredOrders.length === 0">
                    <td colspan="4" class="empty-row">Kechiktirilgan buyurtmalar topilmadi</td>
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
  name: 'DelayedOrders',
  props: {
    data: {
      type: Array,
      default: () => [
        { id: 1, number: 'BR-2847', client: 'Sardor Mirzayev', serviceType: 'IT xizmatlari', delayDays: 5 },
        { id: 2, number: 'BR-2831', client: 'Kamola Yusupova', serviceType: 'Hujjatlar aylanmasi', delayDays: 3 },
        { id: 3, number: 'BR-2819', client: 'Ulug\'bek Nazarov', serviceType: 'Avtotransport', delayDays: 7 },
        { id: 4, number: 'BR-2805', client: 'Gulnora Karimova', serviceType: 'Xo\'jalik ishlari', delayDays: 2 },
        { id: 5, number: 'BR-2798', client: 'Farhod Abdullayev', serviceType: 'Kurerlik xizmati', delayDays: 4 },
        { id: 6, number: 'BR-2780', client: 'Malika Qodirova', serviceType: 'IT xizmatlari', delayDays: 6 },
        { id: 7, number: 'BR-2765', client: 'Otabek Tursunov', serviceType: 'Temir yo\'l chiptasi', delayDays: 1 }
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
    filteredOrders() {
      if (!this.searchQuery.trim()) return this.data;
      const query = this.searchQuery.toLowerCase();
      return this.data.filter(order =>
        order.number.toLowerCase().includes(query) ||
        order.client.toLowerCase().includes(query) ||
        order.serviceType.toLowerCase().includes(query)
      );
    }
  }
}
</script>

<style scoped>
.delayed-card {
  background: var(--card-bg);
  border-radius: 12px;
  padding: 24px;
  box-shadow: 0 4px 6px -1px var(--shadow-color), 0 2px 4px -1px var(--shadow-color), 0 0 0 1px var(--border-color);
  height: 100%;
  display: flex;
  flex-direction: column;
  transition: background-color 0.3s ease, box-shadow 0.3s ease;
}

.card-title {
  font-size: 16px;
  font-weight: 700;
  color: var(--text-primary);
  margin: 0 0 20px 0;
}

.orders-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
  flex: 1;
}

.order-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px 14px;
  background-color: rgba(239, 68, 68, 0.06);
  border-radius: 8px;
  border-left: 3px solid #ef4444;
  transition: transform 0.2s ease;
}

.order-row:hover {
  transform: translateX(2px);
}

.order-number {
  font-size: 12px;
  font-weight: 700;
  color: #ef4444;
}

.order-client {
  font-size: 13px;
  color: var(--text-primary);
  font-weight: 500;
  flex: 1;
  margin-left: 12px;
}

.delay-badge {
  background-color: #fee2e2;
  color: #ef4444;
  font-size: 11px;
  font-weight: 700;
  padding: 3px 8px;
  border-radius: 6px;
  white-space: nowrap;
}

.card-footer {
  margin-top: 16px;
  display: flex;
  justify-content: flex-end;
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

.orders-table {
  width: 100%;
  border-collapse: collapse;
  text-align: left;
}

.orders-table th {
  padding: 12px;
  border-bottom: 2px solid var(--border-color);
  font-size: 12px;
  font-weight: 600;
  color: var(--text-secondary);
  text-transform: uppercase;
}

.orders-table td {
  padding: 14px 12px;
  border-bottom: 1px solid var(--border-color);
  font-size: 13.5px;
  color: var(--text-primary);
  vertical-align: middle;
}

.order-num-text {
  color: #ef4444;
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
