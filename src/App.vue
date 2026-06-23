<template>
  <div id="app">
    <!-- Header Section -->
    <header class="dashboard-header">
      <div class="header-left">
        <h1 class="dashboard-title">Буюртмалар бошқаруви</h1>
        <p class="dashboard-subtitle">Seshanba, 23-Iyun, 2026-yil</p>
      </div>
      <div class="header-right">
        <DropdownFilter
          v-model="selectedDepartment"
          :options="departmentsList"
          label="Barcha bo'limlar"
        />
        <DropdownFilter
          v-model="selectedService"
          :options="servicesList"
          label="Barcha xizmatlar"
        />
        <DropdownFilter
          v-model="selectedTimeframe"
          :options="timeframesList"
          label="Bugun"
        />
        <button class="theme-toggle" @click="toggleTheme" aria-label="Mavzuni o'zgartirish">
          <svg v-if="theme === 'light'" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <path d="M21 12.79A9 9 0 1 1 11.21 3 7 7 0 0 0 21 12.79z"></path>
          </svg>
          <svg v-else width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
            <circle cx="12" cy="12" r="5"></circle>
            <line x1="12" y1="1" x2="12" y2="3"></line>
            <line x1="12" y1="21" x2="12" y2="23"></line>
            <line x1="4.22" y1="4.22" x2="5.64" y2="5.64"></line>
            <line x1="18.36" y1="18.36" x2="19.78" y2="19.78"></line>
            <line x1="1" y1="12" x2="3" y2="12"></line>
            <line x1="21" y1="12" x2="23" y2="12"></line>
            <line x1="4.22" y1="19.78" x2="5.64" y2="18.36"></line>
            <line x1="18.36" y1="5.64" x2="19.78" y2="4.22"></line>
          </svg>
        </button>
      </div>
    </header>

    <!-- KPI Cards Section -->
    <section class="kpi-section">
      <KpiCard
        label="ЖАМИ БУЮРТМАЛАР"
        :value="kpiStats.total"
        trend-value="12.5%"
        trend-type="up"
        trend-color="green"
        icon="total"
      />
      <KpiCard
        label="БАЖАРИЛГАН"
        :value="kpiStats.completed"
        :percentage="`${kpiStats.completedPct}%`"
        trend-value="8.3%"
        trend-type="up"
        trend-color="green"
        icon="completed"
      />
      <KpiCard
        label="ЖАРАЁНДА"
        :value="kpiStats.inProgress"
        :percentage="`${kpiStats.inProgressPct}%`"
        trend-value="3.1%"
        trend-type="down"
        trend-color="red"
        icon="pending"
      />
      <KpiCard
        label="БЕКОР ҚИЛИНГАН"
        :value="kpiStats.cancelled"
        :percentage="`${kpiStats.cancelledPct}%`"
        trend-value="0.0%"
        trend-type="neutral"
        trend-color="grey"
        icon="cancelled"
      />
      <KpiCard
        label="КЕЧИКТИРИЛГАН"
        :value="kpiStats.delayed"
        :percentage="`${kpiStats.delayedPct}%`"
        trend-value="5.7%"
        trend-type="down"
        trend-color="red"
        icon="delayed"
      />
    </section>

    <!-- Charts Section (Row 1) -->
    <section class="dashboard-grid grid-row-1">
      <BarChart
        :active-mode="barChartMode"
        @mode-change="onBarChartModeChange"
        :data-kun="barChartData.kun"
        :data-hafta="barChartData.hafta"
        :data-oy="barChartData.oy"
      />
      <DonutChart :data="donutChartData" />
    </section>

    <!-- Details Section (Row 2) -->
    <section class="dashboard-grid grid-row-2">
      <PerformersRating :data="performersData" />
      <ActiveRequesters :data="activeRequestersData" />
    </section>

    <!-- Distribution & Alerts Section (Row 3) -->
    <section class="dashboard-grid grid-row-3">
      <ServiceStats :data="serviceStatsData" />
      <DepartmentStats :data="departmentsData" />
      <DelayedOrders :data="delayedOrdersData" />
    </section>
  </div>
</template>

<script>
import DropdownFilter from './components/DropdownFilter.vue'
import KpiCard from './components/KpiCard.vue'
import BarChart from './components/BarChart.vue'
import DonutChart from './components/DonutChart.vue'
import ServiceStats from './components/ServiceStats.vue'
import PerformersRating from './components/PerformersRating.vue'
import DepartmentStats from './components/DepartmentStats.vue'
import DelayedOrders from './components/DelayedOrders.vue'
import ActiveRequesters from './components/ActiveRequesters.vue'

export default {
  name: 'App',
  components: {
    DropdownFilter,
    KpiCard,
    BarChart,
    DonutChart,
    ServiceStats,
    PerformersRating,
    DepartmentStats,
    DelayedOrders,
    ActiveRequesters
  },
  data() {
    return {
      theme: 'light',
      selectedDepartment: 'all',
      selectedService: 'all',
      selectedTimeframe: 'today',

      departmentsList: [
        { label: 'Barcha bo\'limlar', value: 'all' },
        { label: 'IT bo\'limi', value: 'it' },
        { label: 'Moliya bo\'limi', value: 'finance' },
        { label: 'Xo\'jalik', value: 'xo' },
        { label: 'HR bo\'limi', value: 'hr' },
        { label: 'Avtotransport', value: 'transport' }
      ],
      servicesList: [
        { label: 'Barcha xizmatlar', value: 'all' },
        { label: 'IT xizmatlari', value: 'it_service' },
        { label: 'Xo\'jalik ishlari', value: 'xo_service' },
        { label: 'Avtotransport', value: 'transport_service' },
        { label: 'Chipta / Temir yo\'l', value: 'ticket_service' }
      ],
      timeframesList: [
        { label: 'Bugun', value: 'today' },
        { label: 'Shu hafta', value: 'week' },
        { label: 'Shu oy', value: 'month' },
        { label: 'Shu yil', value: 'year' }
      ]
    }
  },
  mounted() {
    const savedTheme = localStorage.getItem('theme') || 'light';
    this.theme = savedTheme;
    document.documentElement.setAttribute('data-theme', savedTheme);
  },
  methods: {
    toggleTheme() {
      const newTheme = this.theme === 'light' ? 'dark' : 'light';
      this.theme = newTheme;
      document.documentElement.setAttribute('data-theme', newTheme);
      localStorage.setItem('theme', newTheme);
    },
    onBarChartModeChange(mode) {
      const mapping = {
        kun: 'today',
        hafta: 'week',
        oy: 'month',
        yil: 'year'
      };
      this.selectedTimeframe = mapping[mode] || 'today';
    }
  },
  computed: {
    barChartMode() {
      const mapping = {
        today: 'kun',
        week: 'hafta',
        month: 'oy',
        year: 'yil'
      };
      return mapping[this.selectedTimeframe] || 'kun';
    },
    // Dynamic factors based on filter selections
    timeframeFactor() {
      if (this.selectedTimeframe === 'week') return 5.4;
      if (this.selectedTimeframe === 'month') return 22.8;
      if (this.selectedTimeframe === 'year') return 268.0;
      return 1.0;
    },
    departmentFactor() {
      if (this.selectedDepartment === 'it') return 0.32; // 412/1284
      if (this.selectedDepartment === 'finance') return 0.22; // 287/1284
      if (this.selectedDepartment === 'xo') return 0.18; // 234/1284
      if (this.selectedDepartment === 'hr') return 0.15; // 198/1284
      if (this.selectedDepartment === 'transport') return 0.12; // 153/1284
      return 1.0;
    },
    serviceFactor() {
      if (this.selectedService === 'it_service') return 0.38; // 482/1284
      if (this.selectedService === 'xo_service') return 0.28; // 356/1284
      if (this.selectedService === 'transport_service') return 0.21; // 267/1284
      if (this.selectedService === 'ticket_service') return 0.14; // 179/1284
      return 1.0;
    },
    combinedFactor() {
      return this.timeframeFactor * this.departmentFactor * this.serviceFactor;
    },

    // KPI Metrics calculation
    kpiStats() {
      const baseTotal = 1284;
      const baseCompleted = 1038;
      const baseInProgress = 156;
      const baseCancelled = 62;

      const total = Math.max(Math.round(baseTotal * this.combinedFactor), 1);
      
      // Ensure segments add up beautifully by scaling relative values
      const completed = Math.max(Math.round(baseCompleted * this.combinedFactor), 0);
      const inProgress = Math.max(Math.round(baseInProgress * this.combinedFactor), 0);
      const cancelled = Math.max(Math.round(baseCancelled * this.combinedFactor), 0);
      const delayed = Math.max(total - (completed + inProgress + cancelled), 0);

      const completedPct = total > 0 ? Math.round((completed / total) * 100) : 81;
      const inProgressPct = total > 0 ? Math.round((inProgress / total) * 100) : 12;
      const cancelledPct = total > 0 ? Math.round((cancelled / total) * 100) : 5;
      const delayedPct = total > 0 ? Math.max(100 - (completedPct + inProgressPct + cancelledPct), 0) : 2;

      return {
        total,
        completed,
        completedPct,
        inProgress,
        inProgressPct,
        cancelled,
        cancelledPct,
        delayed,
        delayedPct
      }
    },

    // Donut Chart dynamic data
    donutChartData() {
      return [
        { label: 'Bajarilgan', value: this.kpiStats.completed, color: '#10b981' },
        { label: 'Jarayonda', value: this.kpiStats.inProgress, color: '#f59e0b' },
        { label: 'Bekor qilingan', value: this.kpiStats.cancelled, color: '#ef4444' },
        { label: 'Kechiktirilgan', value: this.kpiStats.delayed, color: '#8b5cf6' }
      ]
    },

    // Bar Chart dynamics
    barChartData() {
      const f = this.departmentFactor * this.serviceFactor;
      return {
        kun: [
          { label: '08:00', value: Math.max(Math.round(4 * f), 1) },
          { label: '10:00', value: Math.max(Math.round(10 * f), 1) },
          { label: '12:00', value: Math.max(Math.round(14 * f), 2) },
          { label: '14:00', value: Math.max(Math.round(11 * f), 1) },
          { label: '16:00', value: Math.max(Math.round(8 * f), 1) },
          { label: '18:00', value: Math.max(Math.round(3 * f), 0) }
        ],
        hafta: [
          { label: 'Dush', value: Math.max(Math.round(120 * f), 10) },
          { label: 'Sesh', value: Math.max(Math.round(150 * f), 15) },
          { label: 'Chor', value: Math.max(Math.round(180 * f), 20) },
          { label: 'Pay', value: Math.max(Math.round(140 * f), 12) },
          { label: 'Jum', value: Math.max(Math.round(160 * f), 15) },
          { label: 'Shan', value: Math.max(Math.round(90 * f), 5) },
          { label: 'Yak', value: Math.max(Math.round(45 * f), 2) }
        ],
        oy: [
          { label: 'Yan', value: Math.max(Math.round(850 * f), 100) },
          { label: 'Fev', value: Math.max(Math.round(920 * f), 120) },
          { label: 'Mar', value: Math.max(Math.round(1050 * f), 150) },
          { label: 'Apr', value: Math.max(Math.round(1150 * f), 180) },
          { label: 'May', value: Math.max(Math.round(1250 * f), 200) },
          { label: 'Iyun', value: Math.max(Math.round(1284 * f), 220) }
        ]
      }
    },

    // Services stats data
    serviceStatsData() {
      const tf = this.timeframeFactor;
      const df = this.selectedDepartment;
      
      const itVal = Math.round(482 * tf * (df === 'it' ? 1.0 : df === 'all' ? 1.0 : 0.05));
      const xoVal = Math.round(356 * tf * (df === 'xo' ? 1.0 : df === 'all' ? 1.0 : 0.05));
      const transVal = Math.round(267 * tf * (df === 'transport' ? 1.0 : df === 'all' ? 1.0 : 0.05));
      const ticketVal = Math.round(179 * tf * (df === 'all' ? 1.0 : 0.08));

      const services = [
        { label: 'IT', value: itVal, color: '#3b82f6' },
        { label: 'Xo\'jalik', value: xoVal, color: '#10b981' },
        { label: 'Avtotransport', value: transVal, color: '#f59e0b' },
        { label: 'Chipta / Temir yo\'l', value: ticketVal, color: '#64748b' }
      ];

      // Filter or highlight selected service
      if (this.selectedService !== 'all') {
        const keyMap = {
          it_service: 'IT',
          xo_service: 'Xo\'jalik',
          transport_service: 'Avtotransport',
          ticket_service: 'Chipta / Temir yo\'l'
        };
        const activeLabel = keyMap[this.selectedService];
        return services.map(s => {
          if (s.label !== activeLabel) {
            return { ...s, value: Math.round(s.value * 0.1) };
          }
          return s;
        });
      }

      return services;
    },

    // Departments stats data
    departmentsData() {
      const tf = this.timeframeFactor;
      const sf = this.selectedService;

      const itVal = Math.round(412 * tf * (sf === 'it_service' ? 1.0 : sf === 'all' ? 1.0 : 0.05));
      const finVal = Math.round(287 * tf * (sf === 'all' ? 1.0 : 0.1));
      const xoVal = Math.round(234 * tf * (sf === 'xo_service' ? 1.0 : sf === 'all' ? 1.0 : 0.05));
      const hrVal = Math.round(198 * tf * (sf === 'all' ? 1.0 : 0.1));
      const transVal = Math.round(153 * tf * (sf === 'transport_service' ? 1.0 : sf === 'all' ? 1.0 : 0.05));

      const depts = [
        { label: 'IT bo\'limi', value: itVal, color: '#3b82f6' },
        { label: 'Moliya bo\'limi', value: finVal, color: '#10b981' },
        { label: 'Xo\'jalik', value: xoVal, color: '#f59e0b' },
        { label: 'HR bo\'limi', value: hrVal, color: '#8b5cf6' },
        { label: 'Avtotransport', value: transVal, color: '#eab308' }
      ];

      if (this.selectedDepartment !== 'all') {
        const keyMap = {
          it: 'IT bo\'limi',
          finance: 'Moliya bo\'limi',
          xo: 'Xo\'jalik',
          hr: 'HR bo\'limi',
          transport: 'Avtotransport'
        };
        const activeLabel = keyMap[this.selectedDepartment];
        return depts.map(d => {
          if (d.label !== activeLabel) {
            return { ...d, value: Math.round(d.value * 0.05) };
          }
          return d;
        });
      }

      return depts;
    },

    // Performers Rating list
    performersData() {
      const basePerformers = [
        { id: 1, name: 'Aziz Karimov', department: 'IT bo\'limi', initials: 'AK', ordersCount: 87, rating: 4.9, avatarColor: '#3b82f6', position: 'Bosh mutaxassis (IT)', avgCloseTime: '1.5 soat' },
        { id: 2, name: 'Dilnoza Rahimova', department: 'Moliya bo\'limi', initials: 'DR', ordersCount: 74, rating: 4.8, avatarColor: '#10b981', position: 'Katta hisobchi (Moliya)', avgCloseTime: '2.1 soat' },
        { id: 3, name: 'Bobur Toshmatov', department: 'Xo\'jalik bo\'limi', initials: 'BT', ordersCount: 68, rating: 4.7, avatarColor: '#f59e0b', position: 'Texnik yordamchi (Xo\'jalik)', avgCloseTime: '2.8 soat' },
        { id: 4, name: 'Madina Usmonova', department: 'HR bo\'limi', initials: 'MU', ordersCount: 61, rating: 4.5, avatarColor: '#8b5cf6', position: 'HR menejer', avgCloseTime: '1.9 soat' },
        { id: 5, name: 'Jasur Aliyev', department: 'IT bo\'limi', initials: 'JA', ordersCount: 53, rating: 4.3, avatarColor: '#ef4444', position: 'Tizim administratori (IT)', avgCloseTime: '2.4 soat' },
        { id: 6, name: 'Nodira Saidova', department: 'Avtotransport', initials: 'NS', ordersCount: 47, rating: 4.1, avatarColor: '#eab308', position: 'Logistika mas\'uli (Avtotransport)', avgCloseTime: '3.1 soat' },
        { id: 7, name: 'Farhod Alimov', department: 'Moliya bo\'limi', initials: 'FA', ordersCount: 39, rating: 4.0, avatarColor: '#14b8a6', position: 'Gʻaznachi (Moliya)', avgCloseTime: '2.6 soat' },
        { id: 8, name: 'Elena Petrova', department: 'HR bo\'limi', initials: 'EP', ordersCount: 32, rating: 3.9, avatarColor: '#ec4899', position: 'Kadrlar inspektori (HR)', avgCloseTime: '2.0 soat' }
      ];

      let filtered = basePerformers;

      // Filter by department
      if (this.selectedDepartment !== 'all') {
        const keyMap = {
          it: 'IT bo\'limi',
          finance: 'Moliya bo\'limi',
          xo: 'Xo\'jalik bo\'limi',
          hr: 'HR bo\'limi',
          transport: 'Avtotransport'
        };
        filtered = basePerformers.filter(p => p.department === keyMap[this.selectedDepartment]);
      }

      // Filter by service
      if (this.selectedService !== 'all') {
        const keyMap = {
          it_service: 'IT bo\'limi',
          xo_service: 'Xo\'jalik bo\'limi',
          transport_service: 'Avtotransport'
        };
        const matchDept = keyMap[this.selectedService];
        if (matchDept) {
          filtered = filtered.filter(p => p.department === matchDept);
        }
      }

      // Map values with timeframe factor
      return filtered.map(p => ({
        ...p,
        ordersCount: Math.round(p.ordersCount * this.timeframeFactor)
      })).sort((a, b) => b.ordersCount - a.ordersCount);
    },

    // Delayed orders list
    delayedOrdersData() {
      const baseOrders = [
        { id: 1, number: 'BR-2847', client: 'Sardor Mirzayev', serviceType: 'IT xizmatlari', delayDays: 5, deptKey: 'it' },
        { id: 2, number: 'BR-2831', client: 'Kamola Yusupova', serviceType: 'Hujjatlar aylanmasi', delayDays: 3, deptKey: 'finance' },
        { id: 3, number: 'BR-2819', client: 'Ulug\'bek Nazarov', serviceType: 'Avtotransport', delayDays: 7, deptKey: 'transport' },
        { id: 4, number: 'BR-2805', client: 'Gulnora Karimova', serviceType: 'Xo\'jalik ishlari', delayDays: 2, deptKey: 'xo' },
        { id: 5, number: 'BR-2798', client: 'Farhod Abdullayev', serviceType: 'Kurerlik xizmati', delayDays: 4, deptKey: 'xo' },
        { id: 6, number: 'BR-2780', client: 'Malika Qodirova', serviceType: 'IT xizmatlari', delayDays: 6, deptKey: 'it' },
        { id: 7, number: 'BR-2765', client: 'Otabek Tursunov', serviceType: 'Temir yo\'l chiptasi', delayDays: 1, deptKey: 'transport' }
      ];

      let filtered = baseOrders;

      if (this.selectedDepartment !== 'all') {
        filtered = baseOrders.filter(o => o.deptKey === this.selectedDepartment);
      }

      if (this.selectedService !== 'all') {
        const keyMap = {
          it_service: 'IT xizmatlari',
          xo_service: 'Xo\'jalik ishlari',
          transport_service: 'Avtotransport',
          ticket_service: 'Temir yo\'l chiptasi'
        };
        filtered = filtered.filter(o => o.serviceType === keyMap[this.selectedService]);
      }

      return filtered;
    },
    activeRequestersData() {
      const baseRequesters = [
        { creator_id: 1, creator_name: 'Sardor Mirzayev', dep_name: 'Moliya bo\'limi', order_count: 64, top_service: 'IT', deptKey: 'finance', position: 'Moliya bo\'limi boshlig\'i', avgRatingGiven: 4.8 },
        { creator_id: 2, creator_name: 'Kamola Yusupova', dep_name: 'HR bo\'limi', order_count: 58, top_service: 'Xo\'jalik', deptKey: 'hr', position: 'Katta HR menejer', avgRatingGiven: 4.7 },
        { creator_id: 3, creator_name: 'Ulug\'bek Nazarov', dep_name: 'IT bo\'limi', order_count: 52, top_service: 'IT', deptKey: 'it', position: 'IT tizim muhandisi', avgRatingGiven: 4.9 },
        { creator_id: 4, creator_name: 'Gulnora Karimova', dep_name: 'Moliya bo\'limi', order_count: 47, top_service: 'Chipta', deptKey: 'finance', position: 'Bosh hisobchi (Moliya)', avgRatingGiven: 4.5 },
        { creator_id: 5, creator_name: 'Farhod Abdullayev', dep_name: 'Avtotransport', order_count: 41, top_service: 'Transport', deptKey: 'transport', position: 'Logistika mas\'uli', avgRatingGiven: 4.6 },
        { creator_id: 6, creator_name: 'Nodir Tolipov', dep_name: 'Xo\'jalik bo\'limi', order_count: 36, top_service: 'Xo\'jalik', deptKey: 'xo', position: 'Xo\'jalik yordamchisi', avgRatingGiven: 4.4 },
        { creator_id: 7, creator_name: 'Dilbar Aliyeva', dep_name: 'HR bo\'limi', order_count: 31, top_service: 'Xo\'jalik', deptKey: 'hr', position: 'HR menejeri', avgRatingGiven: 4.8 },
        { creator_id: 8, creator_name: 'Shavkat Tojiyev', dep_name: 'IT bo\'limi', order_count: 25, top_service: 'IT', deptKey: 'it', position: 'Dasturchi (IT)', avgRatingGiven: 4.7 }
      ];

      let filtered = baseRequesters;

      // Filter by department
      if (this.selectedDepartment !== 'all') {
        const keyMap = {
          it: 'IT bo\'limi',
          finance: 'Moliya bo\'limi',
          xo: 'Xo\'jalik bo\'limi',
          hr: 'HR bo\'limi',
          transport: 'Avtotransport'
        };
        filtered = baseRequesters.filter(r => r.dep_name === keyMap[this.selectedDepartment]);
      }

      // Filter by service
      if (this.selectedService !== 'all') {
        const keyMap = {
          it_service: 'IT',
          xo_service: 'Xo\'jalik',
          transport_service: 'Transport',
          ticket_service: 'Chipta'
        };
        const activeService = keyMap[this.selectedService];
        if (activeService) {
          filtered = filtered.filter(r => r.top_service === activeService);
        }
      }

      // Scale by timeframe
      return filtered.map(r => ({
        ...r,
        order_count: Math.round(r.order_count * this.timeframeFactor)
      })).sort((a, b) => b.order_count - a.order_count);
    }
  }
}
</script>

<style>
/* Global Styles & Reset */
@import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700;800&display=swap');

:root {
  --bg-color: #f8fafc;
  --card-bg: #ffffff;
  --text-primary: #1e293b;
  --text-secondary: #64748b;
  --border-color: #e2e8f0;
  --shadow-color: rgba(0, 0, 0, 0.01);
  --shadow-hover: rgba(0, 0, 0, 0.04);
  --hover-bg: #f1f5f9;
}

[data-theme="dark"] {
  --bg-color: #0f172a;
  --card-bg: #1e293b;
  --text-primary: #f8fafc;
  --text-secondary: #94a3b8;
  --border-color: #334155;
  --shadow-color: rgba(0, 0, 0, 0.15);
  --shadow-hover: rgba(0, 0, 0, 0.25);
  --hover-bg: #334155;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Plus Jakarta Sans', -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif;
  background-color: var(--bg-color);
  color: var(--text-primary);
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  padding: 30px;
  transition: background-color 0.3s ease, color 0.3s ease;
}

#app {
  max-width: 1600px;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  gap: 24px;
}

/* Header styling */
.dashboard-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  flex-wrap: wrap;
  gap: 16px;
}

.dashboard-title {
  font-size: 26px;
  font-weight: 700;
  color: var(--text-primary);
  letter-spacing: -0.01em;
}

.dashboard-subtitle {
  font-size: 13.5px;
  color: var(--text-secondary);
  font-weight: 500;
  margin-top: 2px;
}

.header-right {
  display: flex;
  align-items: center;
  gap: 12px;
}

/* Theme toggle button styling */
.theme-toggle {
  background: var(--card-bg);
  border: 1px solid var(--border-color);
  border-radius: 8px;
  width: 38px;
  height: 38px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  color: var(--text-secondary);
  transition: all 0.25s ease;
  outline: none;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.02);
}

.theme-toggle:hover {
  background: var(--bg-color);
  border-color: var(--text-secondary);
  color: var(--text-primary);
  transform: translateY(-1px);
}

/* KPI Cards Layout */
.kpi-section {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

/* Grid Layouts */
.dashboard-grid {
  display: grid;
  gap: 20px;
}

/* Row 1 layout: Wide chart + status breakdown */
.grid-row-1 {
  grid-template-columns: 3fr 1.2fr;
}

/* Row 2 layout: Performers rating + Active requesters rating side-by-side */
.grid-row-2 {
  grid-template-columns: 1fr 1fr;
}

/* Row 3 layout: Distribution stats and alerts */
.grid-row-3 {
  grid-template-columns: 1fr 1fr 1fr;
}

/* Responsive queries */
@media (max-width: 1200px) {
  .grid-row-1 {
    grid-template-columns: 1fr;
  }
  .grid-row-2 {
    grid-template-columns: 1fr;
  }
  .grid-row-3 {
    grid-template-columns: 1fr 1fr;
  }
}

@media (max-width: 768px) {
  body {
    padding: 16px;
  }
  .dashboard-header {
    flex-direction: column;
    align-items: flex-start;
  }
  .header-right {
    width: 100%;
    overflow-x: auto;
    padding-bottom: 8px;
  }
  .grid-row-1, .grid-row-2, .grid-row-3 {
    grid-template-columns: 1fr;
  }
}
</style>
