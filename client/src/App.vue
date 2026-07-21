<template>
  <div class="app" :class="{ 'sidebar-collapsed': isSidebarCollapsed }">
    <button
      type="button"
      class="sidebar-toggle"
      :aria-expanded="isSidebarOpen"
      aria-controls="primary-sidebar"
      aria-label="Toggle navigation menu"
      @click="isSidebarOpen = !isSidebarOpen"
    >
      <svg width="20" height="20" viewBox="0 0 20 20" fill="none">
        <path d="M3 5H17M3 10H17M3 15H17" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" />
      </svg>
    </button>
    <div
      v-if="isSidebarOpen"
      class="sidebar-scrim"
      @click="isSidebarOpen = false"
    ></div>

    <aside
      id="primary-sidebar"
      class="sidebar"
      :class="{ open: isSidebarOpen, collapsed: isSidebarCollapsed }"
    >
      <div class="sidebar-header">
        <h1>{{ t('nav.companyName') }}</h1>
        <span class="sidebar-subtitle">{{ t('nav.subtitle') }}</span>
      </div>
      <nav class="sidebar-nav" aria-label="Primary">
        <router-link
          to="/"
          class="nav-item"
          :class="{ active: $route.path === '/' }"
          :aria-current="$route.path === '/' ? 'page' : undefined"
          :data-tooltip="t('nav.overview')"
        >
          <span class="nav-icon" aria-hidden="true">
            <svg width="20" height="20" viewBox="0 0 20 20" fill="none">
              <rect x="2.5" y="2.5" width="6" height="6" rx="1" stroke="currentColor" stroke-width="1.5" />
              <rect x="11.5" y="2.5" width="6" height="6" rx="1" stroke="currentColor" stroke-width="1.5" />
              <rect x="2.5" y="11.5" width="6" height="6" rx="1" stroke="currentColor" stroke-width="1.5" />
              <rect x="11.5" y="11.5" width="6" height="6" rx="1" stroke="currentColor" stroke-width="1.5" />
            </svg>
          </span>
          <span class="nav-label">{{ t('nav.overview') }}</span>
        </router-link>
        <router-link
          to="/inventory"
          class="nav-item"
          :class="{ active: $route.path === '/inventory' }"
          :aria-current="$route.path === '/inventory' ? 'page' : undefined"
          :data-tooltip="t('nav.inventory')"
        >
          <span class="nav-icon" aria-hidden="true">
            <svg width="20" height="20" viewBox="0 0 20 20" fill="none">
              <path d="M2.5 6.5L10 2.5L17.5 6.5L10 10.5L2.5 6.5Z" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round" />
              <path d="M2.5 6.5V14L10 18L17.5 14V6.5" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round" />
              <path d="M10 10.5V18" stroke="currentColor" stroke-width="1.5" />
            </svg>
          </span>
          <span class="nav-label">{{ t('nav.inventory') }}</span>
        </router-link>
        <router-link
          to="/orders"
          class="nav-item"
          :class="{ active: $route.path === '/orders' }"
          :aria-current="$route.path === '/orders' ? 'page' : undefined"
          :data-tooltip="t('nav.orders')"
        >
          <span class="nav-icon" aria-hidden="true">
            <svg width="20" height="20" viewBox="0 0 20 20" fill="none">
              <rect x="4" y="3.5" width="12" height="14" rx="1.5" stroke="currentColor" stroke-width="1.5" />
              <path d="M7.5 2.5H12.5V4.5H7.5V2.5Z" stroke="currentColor" stroke-width="1.5" stroke-linejoin="round" />
              <path d="M7 8.5H13M7 11.5H13M7 14.5H10.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" />
            </svg>
          </span>
          <span class="nav-label">{{ t('nav.orders') }}</span>
        </router-link>
        <router-link
          to="/spending"
          class="nav-item"
          :class="{ active: $route.path === '/spending' }"
          :aria-current="$route.path === '/spending' ? 'page' : undefined"
          :data-tooltip="t('nav.finance')"
        >
          <span class="nav-icon" aria-hidden="true">
            <svg width="20" height="20" viewBox="0 0 20 20" fill="none">
              <circle cx="10" cy="10" r="7.5" stroke="currentColor" stroke-width="1.5" />
              <path d="M10 5.5V14.5M12.5 7.5C12.5 6.5 11.5 6 10 6C8.5 6 7.5 6.7 7.5 7.7C7.5 8.7 8.5 9 10 9.3C11.5 9.6 12.5 10 12.5 11C12.5 12 11.5 12.5 10 12.5C8.5 12.5 7.5 12 7.5 11" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" />
            </svg>
          </span>
          <span class="nav-label">{{ t('nav.finance') }}</span>
        </router-link>
        <router-link
          to="/demand"
          class="nav-item"
          :class="{ active: $route.path === '/demand' }"
          :aria-current="$route.path === '/demand' ? 'page' : undefined"
          :data-tooltip="t('nav.demandForecast')"
        >
          <span class="nav-icon" aria-hidden="true">
            <svg width="20" height="20" viewBox="0 0 20 20" fill="none">
              <path d="M2.5 14.5L7.5 9.5L11 12L17.5 5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" />
              <path d="M13 5H17.5V9.5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round" />
            </svg>
          </span>
          <span class="nav-label">{{ t('nav.demandForecast') }}</span>
        </router-link>
        <router-link
          to="/reports"
          class="nav-item"
          :class="{ active: $route.path === '/reports' }"
          :aria-current="$route.path === '/reports' ? 'page' : undefined"
          :data-tooltip="t('nav.reports')"
        >
          <span class="nav-icon" aria-hidden="true">
            <svg width="20" height="20" viewBox="0 0 20 20" fill="none">
              <rect x="3" y="2.5" width="14" height="15" rx="1.5" stroke="currentColor" stroke-width="1.5" />
              <path d="M6.5 13.5V10.5M10 13.5V8M13.5 13.5V6" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" />
            </svg>
          </span>
          <span class="nav-label">{{ t('nav.reports') }}</span>
        </router-link>
      </nav>
      <div class="sidebar-footer">
        <button
          type="button"
          class="sidebar-collapse-toggle"
          :aria-expanded="!isSidebarCollapsed"
          :aria-label="isSidebarCollapsed ? 'Expand sidebar' : 'Collapse sidebar'"
          @click="toggleSidebarCollapse"
        >
          <svg
            width="16"
            height="16"
            viewBox="0 0 16 16"
            fill="none"
            class="collapse-chevron"
            :class="{ flipped: isSidebarCollapsed }"
          >
            <path d="M10 3L5 8L10 13" stroke="currentColor" stroke-width="1.75" stroke-linecap="round" stroke-linejoin="round" />
          </svg>
          <span class="collapse-toggle-label">{{ isSidebarCollapsed ? 'Expand sidebar' : 'Collapse sidebar' }}</span>
        </button>
      </div>
    </aside>

    <div class="content-column">
      <header class="topbar">
        <LanguageSwitcher />
        <ProfileMenu
          @show-profile-details="showProfileDetails = true"
          @show-tasks="showTasks = true"
        />
      </header>
      <FilterBar />
      <main class="main-content">
        <router-view />
      </main>
    </div>

    <ProfileDetailsModal
      :is-open="showProfileDetails"
      @close="showProfileDetails = false"
    />

    <TasksModal
      :is-open="showTasks"
      :tasks="tasks"
      @close="showTasks = false"
      @add-task="addTask"
      @delete-task="deleteTask"
      @toggle-task="toggleTask"
    />
  </div>
</template>

<script>
import { ref, onMounted, onUnmounted, computed, watch } from 'vue'
import { useRoute } from 'vue-router'
import { api } from './api'
import { useAuth } from './composables/useAuth'
import { useI18n } from './composables/useI18n'
import FilterBar from './components/FilterBar.vue'
import ProfileMenu from './components/ProfileMenu.vue'
import ProfileDetailsModal from './components/ProfileDetailsModal.vue'
import TasksModal from './components/TasksModal.vue'
import LanguageSwitcher from './components/LanguageSwitcher.vue'

export default {
  name: 'App',
  components: {
    FilterBar,
    ProfileMenu,
    ProfileDetailsModal,
    TasksModal,
    LanguageSwitcher
  },
  setup() {
    const { currentUser } = useAuth()
    const { t } = useI18n()
    const route = useRoute()
    const showProfileDetails = ref(false)
    const showTasks = ref(false)
    const apiTasks = ref([])

    // Off-canvas sidebar state (below 1024px). Closed by default and on
    // every route change so the drawer never stays open over the page it
    // just navigated to.
    const isSidebarOpen = ref(false)
    watch(() => route.path, () => {
      isSidebarOpen.value = false
    })

    // Collapsible icon-rail sidebar (>=1024px only — the <1024px off-canvas
    // drawer above is untouched by any of this). `null` means "the user
    // hasn't made an explicit choice yet", in which case the tablet-tier
    // auto-default applies; once they click the toggle, that choice is
    // persisted and wins over the tier default from then on. Mirrors the
    // locale persistence pattern at composables/useI18n.js:11.
    const SIDEBAR_COLLAPSE_KEY = 'sidebar-collapsed'
    const storedCollapsePref = localStorage.getItem(SIDEBAR_COLLAPSE_KEY)
    const explicitSidebarCollapsed = ref(
      storedCollapsePref === null ? null : storedCollapsePref === 'true'
    )

    // Tablet tier (1024–1279px) auto-collapses to the icon rail; desktop
    // (>=1280px) defaults to expanded. Tracked via matchMedia rather than
    // read once, since both the auto-default and the toggle's own logic
    // need live awareness of which tier is active as the viewport resizes.
    const tabletTierQuery = window.matchMedia('(min-width: 1024px) and (max-width: 1279px)')
    const isTabletTier = ref(tabletTierQuery.matches)
    const handleTabletTierChange = (event) => {
      isTabletTier.value = event.matches
    }
    tabletTierQuery.addEventListener('change', handleTabletTierChange)
    onUnmounted(() => {
      tabletTierQuery.removeEventListener('change', handleTabletTierChange)
    })

    const isSidebarCollapsed = computed(() => {
      if (explicitSidebarCollapsed.value !== null) return explicitSidebarCollapsed.value
      return isTabletTier.value
    })

    const toggleSidebarCollapse = () => {
      explicitSidebarCollapsed.value = !isSidebarCollapsed.value
      localStorage.setItem(SIDEBAR_COLLAPSE_KEY, String(explicitSidebarCollapsed.value))
    }

    // Merge mock tasks from currentUser with API tasks
    const tasks = computed(() => {
      return [...currentUser.value.tasks, ...apiTasks.value]
    })

    const loadTasks = async () => {
      try {
        apiTasks.value = await api.getTasks()
      } catch (err) {
        console.error('Failed to load tasks:', err)
      }
    }

    const addTask = async (taskData) => {
      try {
        const newTask = await api.createTask(taskData)
        // Add new task to the beginning of the array
        apiTasks.value.unshift(newTask)
      } catch (err) {
        console.error('Failed to add task:', err)
      }
    }

    const deleteTask = async (taskId) => {
      try {
        // Check if it's a mock task (from currentUser)
        const isMockTask = currentUser.value.tasks.some(t => t.id === taskId)

        if (isMockTask) {
          // Remove from mock tasks
          const index = currentUser.value.tasks.findIndex(t => t.id === taskId)
          if (index !== -1) {
            currentUser.value.tasks.splice(index, 1)
          }
        } else {
          // Remove from API tasks
          await api.deleteTask(taskId)
          apiTasks.value = apiTasks.value.filter(t => t.id !== taskId)
        }
      } catch (err) {
        console.error('Failed to delete task:', err)
      }
    }

    const toggleTask = async (taskId) => {
      try {
        // Check if it's a mock task (from currentUser)
        const mockTask = currentUser.value.tasks.find(t => t.id === taskId)

        if (mockTask) {
          // Toggle mock task status
          mockTask.status = mockTask.status === 'pending' ? 'completed' : 'pending'
        } else {
          // Toggle API task
          const updatedTask = await api.toggleTask(taskId)
          const index = apiTasks.value.findIndex(t => t.id === taskId)
          if (index !== -1) {
            apiTasks.value[index] = updatedTask
          }
        }
      } catch (err) {
        console.error('Failed to toggle task:', err)
      }
    }

    onMounted(loadTasks)

    return {
      t,
      isSidebarOpen,
      isSidebarCollapsed,
      toggleSidebarCollapse,
      showProfileDetails,
      showTasks,
      tasks,
      addTask,
      deleteTask,
      toggleTask
    }
  }
}
</script>

<style>
/*
 * Design tokens — derived from the palette already in use across the app
 * (sampled from the old .top-nav / .nav-tabs / .stat-card / .badge rules),
 * not a new palette. Defined once here; every scoped stylesheet can read
 * these via the CSS custom property cascade without an import.
 */
:root {
  /* spacing — 4px base step */
  --space-1: 0.25rem;   /*  4px */
  --space-2: 0.5rem;    /*  8px */
  --space-3: 0.75rem;   /* 12px */
  --space-4: 1rem;      /* 16px */
  --space-5: 1.25rem;   /* 20px */
  --space-6: 1.5rem;    /* 24px */
  --space-8: 2rem;      /* 32px */
  --space-10: 2.5rem;   /* 40px */
  --space-12: 3rem;     /* 48px */

  /* surfaces */
  --color-bg: #f8fafc;
  --color-bg-hover: #f1f5f9;
  --color-surface: #ffffff;
  --color-border: #e2e8f0;

  /* text — consolidated from #0f172a/#1e293b, the two near-duplicate
     darks the old shell used for headings vs. body copy */
  --color-text: #0f172a;
  --color-text-muted: #64748b;

  /* accent — sampled from the old active-tab color (#2563eb) and its
     background tint (#eff6ff), also reused by LanguageSwitcher's
     .dropdown-item.active */
  --color-primary: #2563eb;
  --color-primary-contrast: #ffffff;
  --color-primary-soft: #eff6ff;

  /* status — unchanged, badge classes depend on these exact hues */
  --color-success: #059669;
  --color-warning: #ea580c;
  --color-danger: #dc2626;
  --color-info: #2563eb;

  /* radius */
  --radius-sm: 4px;
  --radius: 8px;
  --radius-lg: 12px;
  --radius-full: 9999px;

  /* shadow — tinted toward the text color rather than pure black */
  --shadow-sm: 0 1px 2px rgba(15, 23, 42, 0.06);
  --shadow: 0 1px 3px rgba(15, 23, 42, 0.08);
  --shadow-lg: 0 8px 24px rgba(15, 23, 42, 0.10);

  /* layout */
  --sidebar-w: 260px;
  --sidebar-w-collapsed: 72px;
  --topbar-h: 56px;
  --content-max: 1440px;
}

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  background: var(--color-bg);
  color: var(--color-text);
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

.app {
  display: grid;
  grid-template-columns: var(--sidebar-w) 1fr;
  min-height: 100vh;
}

/* Sidebar toggle + scrim: inert on desktop, only shown below 1024px */
.sidebar-toggle {
  display: none;
  position: fixed;
  top: var(--space-3);
  left: var(--space-3);
  align-items: center;
  justify-content: center;
  width: 40px;
  height: 40px;
  background: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: var(--radius);
  color: var(--color-text);
  cursor: pointer;
  box-shadow: var(--shadow);
  z-index: 250;
}

.sidebar-toggle:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

.sidebar-scrim {
  display: none;
}

.sidebar {
  position: sticky;
  top: 0;
  height: 100vh;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  background: var(--color-surface);
  border-right: 1px solid var(--color-border);
  z-index: 100;
}

.sidebar-header {
  padding: var(--space-6) var(--space-5);
  border-bottom: 1px solid var(--color-border);
}

.sidebar-header h1 {
  font-size: 1.25rem;
  font-weight: 700;
  color: var(--color-text);
  letter-spacing: -0.025em;
}

.sidebar-subtitle {
  display: block;
  margin-top: var(--space-1);
  font-size: 0.75rem;
  color: var(--color-text-muted);
  font-weight: 400;
}

.sidebar-nav {
  display: flex;
  flex-direction: column;
  gap: var(--space-1);
  padding: var(--space-4);
}

.nav-item {
  display: flex;
  align-items: center;
  gap: var(--space-3);
  padding: var(--space-3) var(--space-4);
  color: var(--color-text-muted);
  text-decoration: none;
  font-weight: 500;
  font-size: 0.9375rem;
  border-radius: var(--radius);
  border-left: 3px solid transparent;
  transition: background-color 0.2s ease, color 0.2s ease;
}

.nav-item:hover {
  color: var(--color-text);
  background: var(--color-bg-hover);
}

.nav-item:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

.nav-item.active {
  color: var(--color-primary);
  background: var(--color-primary-soft);
  border-left-color: var(--color-primary);
  font-weight: 600;
}

.nav-icon {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 20px;
  height: 20px;
  flex-shrink: 0;
}

.nav-label {
  white-space: nowrap;
}

/* Collapse toggle: pinned to the bottom of the sidebar via margin-top: auto
   on this footer (the nav list above it is a flex column). Hidden below
   1024px — the hamburger/off-canvas drawer already owns collapse-adjacent
   behavior at that tier, so this control would be redundant there. */
.sidebar-footer {
  margin-top: auto;
  padding: var(--space-4);
  border-top: 1px solid var(--color-border);
  display: none;
}

.sidebar-collapse-toggle {
  display: flex;
  align-items: center;
  gap: var(--space-3);
  width: 100%;
  padding: var(--space-2) var(--space-3);
  background: none;
  border: none;
  border-radius: var(--radius);
  color: var(--color-text-muted);
  font-family: inherit;
  font-size: 0.875rem;
  font-weight: 500;
  cursor: pointer;
  transition: background-color 0.2s ease, color 0.2s ease;
}

.sidebar-collapse-toggle:hover {
  color: var(--color-text);
  background: var(--color-bg-hover);
}

.sidebar-collapse-toggle:focus-visible {
  outline: 2px solid var(--color-primary);
  outline-offset: 2px;
}

.collapse-chevron {
  flex-shrink: 0;
  transition: transform 0.2s ease;
}

.collapse-chevron.flipped {
  transform: rotate(180deg);
}

.content-column {
  display: flex;
  flex-direction: column;
  min-width: 0;
}

.topbar {
  /* z-index higher than .filters-bar (z-index: 90): both are sticky
     siblings in the content column, so without this the filter bar —
     later in DOM order — paints over the topbar's own stacking context
     and swallows the language-switcher/profile-menu dropdowns (which
     have z-index: 1000, but only *within* the topbar's context). */
  position: sticky;
  top: 0;
  z-index: 95;
  display: flex;
  align-items: center;
  justify-content: flex-end;
  gap: var(--space-4);
  height: var(--topbar-h);
  padding: 0 var(--space-8);
  background: var(--color-surface);
  border-bottom: 1px solid var(--color-border);
}

.main-content {
  flex: 1;
  width: 100%;
  max-width: var(--content-max);
  padding: var(--space-6) var(--space-8);
}

@media (max-width: 1024px) {
  .app {
    grid-template-columns: 1fr;
  }

  .sidebar-toggle {
    display: flex;
  }

  .topbar {
    padding-left: var(--space-12);
  }

  .sidebar {
    position: fixed;
    inset: 0 auto 0 0;
    width: var(--sidebar-w);
    transform: translateX(-100%);
    transition: transform 0.2s ease;
    z-index: 200;
  }

  .sidebar.open {
    transform: translateX(0);
  }

  .sidebar-scrim {
    display: block;
    position: fixed;
    inset: 0;
    background: rgba(15, 23, 42, 0.4);
    z-index: 150;
  }
}

/* Collapsible icon-rail — desktop (>=1280px, toggled) and tablet
   (1024-1279px, auto-collapsed by default). Everything here is scoped to
   >=1024px so none of it can leak into the off-canvas drawer tier above. */
@media (min-width: 1024px) {
  .sidebar-footer {
    display: block;
  }

  .sidebar {
    width: var(--sidebar-w);
    transition: width 0.2s ease;
  }

  .app {
    transition: grid-template-columns 0.2s ease;
  }

  .app.sidebar-collapsed {
    grid-template-columns: var(--sidebar-w-collapsed) 1fr;
  }

  .app.sidebar-collapsed .sidebar {
    width: var(--sidebar-w-collapsed);
  }

  .sidebar.collapsed {
    /* Let the icon-rail tooltips (positioned to escape the rail on the
       right) paint outside the sidebar's box. The nav list is short
       enough (6 items) that losing scroll-if-needed here isn't a
       practical concern, unlike the expanded state which keeps its
       overflow-y: auto for taller content. */
    overflow: visible;
  }

  .sidebar.collapsed .sidebar-header {
    padding: var(--space-4) var(--space-2);
    min-height: var(--topbar-h);
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .sidebar.collapsed .sidebar-header h1,
  .sidebar.collapsed .sidebar-subtitle {
    display: none;
  }

  .sidebar.collapsed .sidebar-nav {
    padding: var(--space-4) var(--space-2);
  }

  .sidebar.collapsed .nav-item {
    position: relative;
    justify-content: center;
    padding: var(--space-3) var(--space-2);
  }

  .sidebar.collapsed .nav-label {
    /* Visually hidden, not aria-hidden — screen readers still get the
       label as the link's accessible name. */
    position: absolute;
    width: 1px;
    height: 1px;
    padding: 0;
    margin: -1px;
    overflow: hidden;
    clip: rect(0, 0, 0, 0);
    white-space: nowrap;
    border: 0;
  }

  .sidebar.collapsed .nav-item::after {
    content: attr(data-tooltip);
    position: absolute;
    left: calc(100% + var(--space-2));
    top: 50%;
    transform: translateY(-50%);
    background: var(--color-text);
    color: var(--color-surface);
    padding: var(--space-1) var(--space-3);
    border-radius: var(--radius-sm);
    font-size: 0.75rem;
    font-weight: 500;
    white-space: nowrap;
    box-shadow: var(--shadow-lg);
    opacity: 0;
    visibility: hidden;
    pointer-events: none;
    transition: opacity 0.15s ease, visibility 0.15s ease;
    z-index: 300;
  }

  .sidebar.collapsed .nav-item:hover::after,
  .sidebar.collapsed .nav-item:focus-visible::after {
    opacity: 1;
    visibility: visible;
  }

  .sidebar.collapsed .sidebar-collapse-toggle {
    justify-content: center;
  }

  .sidebar.collapsed .collapse-toggle-label {
    display: none;
  }
}

.page-header {
  margin-bottom: 1.5rem;
}

.page-header h2 {
  font-size: 1.875rem;
  font-weight: 700;
  color: #0f172a;
  margin-bottom: 0.375rem;
  letter-spacing: -0.025em;
}

.page-header p {
  color: #64748b;
  font-size: 0.938rem;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.25rem;
  margin-bottom: 1.5rem;
}

.stat-card {
  background: white;
  padding: 1.25rem;
  border-radius: 10px;
  border: 1px solid #e2e8f0;
  transition: all 0.2s ease;
}

.stat-card:hover {
  border-color: #cbd5e1;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.06);
}

.stat-label {
  color: #64748b;
  font-size: 0.875rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 0.625rem;
}

.stat-value {
  font-size: 2.25rem;
  font-weight: 700;
  color: #0f172a;
  letter-spacing: -0.025em;
}

.stat-card.warning .stat-value {
  color: #ea580c;
}

.stat-card.success .stat-value {
  color: #059669;
}

.stat-card.danger .stat-value {
  color: #dc2626;
}

.stat-card.info .stat-value {
  color: #2563eb;
}

.card {
  background: white;
  border-radius: 10px;
  padding: 1.25rem;
  border: 1px solid #e2e8f0;
  margin-bottom: 1.25rem;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  padding-bottom: 0.875rem;
  border-bottom: 1px solid #e2e8f0;
}

.card-title {
  font-size: 1.125rem;
  font-weight: 700;
  color: #0f172a;
  letter-spacing: -0.025em;
}

.table-container {
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background: #f8fafc;
  border-top: 1px solid #e2e8f0;
  border-bottom: 1px solid #e2e8f0;
}

th {
  text-align: left;
  padding: 0.5rem 0.75rem;
  font-weight: 600;
  color: #475569;
  font-size: 0.75rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

td {
  padding: 0.5rem 0.75rem;
  border-top: 1px solid #f1f5f9;
  color: #334155;
  font-size: 0.875rem;
}

tbody tr {
  transition: background-color 0.15s ease;
}

tbody tr:hover {
  background: #f8fafc;
}

.badge {
  display: inline-block;
  padding: 0.313rem 0.75rem;
  border-radius: 6px;
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.025em;
}

.badge.success {
  background: #d1fae5;
  color: #065f46;
}

.badge.warning {
  background: #fed7aa;
  color: #92400e;
}

.badge.danger {
  background: #fecaca;
  color: #991b1b;
}

.badge.info {
  background: #dbeafe;
  color: #1e40af;
}

.badge.increasing {
  background: #d1fae5;
  color: #065f46;
}

.badge.decreasing {
  background: #fecaca;
  color: #991b1b;
}

.badge.stable {
  background: #e0e7ff;
  color: #3730a3;
}

.badge.high {
  background: #fecaca;
  color: #991b1b;
}

.badge.medium {
  background: #fed7aa;
  color: #92400e;
}

.badge.low {
  background: #dbeafe;
  color: #1e40af;
}

.loading {
  text-align: center;
  padding: 3rem;
  color: #64748b;
  font-size: 0.938rem;
}

.error {
  background: #fef2f2;
  border: 1px solid #fecaca;
  color: #991b1b;
  padding: 1rem;
  border-radius: 8px;
  margin: 1rem 0;
  font-size: 0.938rem;
}
</style>
