<template>
  <div class="dropdown-filter" v-click-outside="close">
    <button class="dropdown-trigger" @click="toggle" type="button">
      <span class="dropdown-label">{{ selectedLabel || label }}</span>
      <svg
        class="dropdown-arrow"
        :class="{ 'is-open': isOpen }"
        width="10"
        height="6"
        viewBox="0 0 10 6"
        fill="none"
        xmlns="http://www.w3.org/2000/svg"
      >
        <path
          d="M1 1L5 5L9 1"
          stroke="#64748B"
          stroke-width="1.5"
          stroke-linecap="round"
          stroke-linejoin="round"
        />
      </svg>
    </button>
    <transition name="slide-up">
      <ul v-if="isOpen" class="dropdown-menu">
        <li
          v-for="option in options"
          :key="option.value"
          class="dropdown-item"
          :class="{ 'is-active': option.value === value }"
          @click="select(option)"
        >
          {{ option.label }}
        </li>
      </ul>
    </transition>
  </div>
</template>

<script>
export default {
  name: 'DropdownFilter',
  props: {
    label: {
      type: String,
      default: 'Tanlash'
    },
    options: {
      type: Array,
      required: true
    },
    value: {
      type: [String, Number],
      default: ''
    }
  },
  data() {
    return {
      isOpen: false
    }
  },
  computed: {
    selectedLabel() {
      const selected = this.options.find(opt => opt.value === this.value);
      return selected ? selected.label : '';
    }
  },
  methods: {
    toggle() {
      this.isOpen = !this.isOpen;
    },
    close() {
      this.isOpen = false;
    },
    select(option) {
      this.$emit('input', option.value);
      this.$emit('change', option.value);
      this.isOpen = false;
    }
  },
  directives: {
    'click-outside': {
      bind(el, binding, vnode) {
        el.clickOutsideEvent = function(event) {
          if (!(el === event.target || el.contains(event.target))) {
            vnode.context[binding.expression](event);
          }
        };
        document.body.addEventListener('click', el.clickOutsideEvent);
      },
      unbind(el) {
        document.body.removeEventListener('click', el.clickOutsideEvent);
      }
    }
  }
}
</script>

<style scoped>
.dropdown-filter {
  position: relative;
  display: inline-block;
  font-family: inherit;
}

.dropdown-trigger {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 12px;
  background: var(--card-bg);
  border: 1px solid var(--border-color);
  border-radius: 8px;
  padding: 8px 16px;
  font-size: 13px;
  color: var(--text-primary);
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s ease;
  min-width: 140px;
  outline: none;
  box-shadow: 0 1px 2px rgba(0, 0, 0, 0.02);
}

.dropdown-trigger:hover {
  background: var(--bg-color);
  border-color: var(--text-secondary);
}

.dropdown-arrow {
  transition: transform 0.2s ease;
}

.dropdown-arrow.is-open {
  transform: rotate(180deg);
}

.dropdown-menu {
  position: absolute;
  top: calc(100% + 6px);
  right: 0;
  z-index: 100;
  background: var(--card-bg);
  border: 1px solid var(--border-color);
  border-radius: 8px;
  box-shadow: 0 10px 15px -3px var(--shadow-color), 0 4px 6px -2px var(--shadow-color);
  padding: 6px;
  margin: 0;
  list-style: none;
  min-width: 100%;
  width: max-content;
  max-height: 250px;
  overflow-y: auto;
}

.dropdown-item {
  padding: 8px 16px;
  font-size: 13px;
  color: var(--text-secondary);
  border-radius: 6px;
  cursor: pointer;
  transition: all 0.15s ease;
  text-align: left;
}

.dropdown-item:hover {
  background: var(--bg-color);
  color: var(--text-primary);
}

.dropdown-item.is-active {
  background: rgba(37, 99, 235, 0.1);
  color: #2563eb;
  font-weight: 600;
}

/* Animations */
.slide-up-enter-active,
.slide-up-leave-active {
  transition: opacity 0.15s ease, transform 0.15s ease;
}
.slide-up-enter,
.slide-up-leave-to {
  opacity: 0;
  transform: translateY(4px);
}
</style>
