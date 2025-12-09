# 🦌 Elk Project Learning Roadmap

> A comprehensive 4-week learning plan to master modern Vue/Nuxt architecture patterns through the Elk codebase

---

## 📊 Project Overview

**Elk** is a nimble Mastodon web client built with cutting-edge frontend technologies. This codebase is an excellent learning resource because:

- **Scale**: ~186 Vue components, ~5,667 lines of composable logic
- **Modern Stack**: Nuxt 4, Vue 3.5, TypeScript, UnoCSS, Pinia
- **Real-world patterns**: i18n, PWA, state management, accessibility, performance optimization
- **Production quality**: Used by thousands, maintained by Vue/Nuxt core team members
- **Best practices**: Shows patterns you can't learn from docs alone

### Tech Stack Breakdown

```
Core Framework:
├── Nuxt 4.x (SSR, auto-imports, file-based routing)
├── Vue 3.5 (Composition API, TypeScript, reactivity)
└── TypeScript (strict typing, interfaces)

State & Data:
├── Pinia (state management)
├── VueUse (composable utilities)
├── Masto.js (Mastodon API client)
└── IndexedDB (client-side storage via idb-keyval)

Styling & UI:
├── UnoCSS (atomic CSS, instant on-demand)
├── Floating Vue (tooltips, popovers)
├── TipTap (rich text editor)
└── CSS Variables (theming, dark mode)

Advanced Features:
├── @nuxtjs/i18n (30+ languages, RTL support)
├── vite-plugin-pwa (offline, notifications)
├── Shiki (syntax highlighting)
└── Virtual Scroller (performance optimization)

Testing & Quality:
├── Vitest (unit testing)
├── ESLint (@antfu/eslint-config)
└── TypeScript (type checking)
```

---

## 🎯 Learning Philosophy

### The Right Approach

1. **Learn by Pattern Recognition**: Don't read linearly. Identify patterns, then see how they're applied throughout the codebase
2. **Build Mental Models**: Understand the "why" behind architectural decisions
3. **Hands-on Practice**: For each concept, create mini-implementations
4. **Progressive Depth**: Start with high-level structure, dive deeper each week
5. **Connect the Dots**: See how different parts integrate (i18n + composables + components)

### What Makes This Codebase Special

- **Composable-First Architecture**: Logic is extracted into reusable composables
- **Type-Safe Everything**: TypeScript used effectively, not just for type-checking
- **Performance-Conscious**: Virtual scrolling, lazy loading, optimization patterns
- **Accessibility Built-In**: ARIA patterns, keyboard navigation, screen reader support
- **Production Patterns**: Error handling, loading states, edge cases handled properly

---

## 📅 4-Week Learning Plan

### Week 1: Foundation & Architecture
**Goal**: Understand project structure, build system, and core patterns

#### Day 1-2: Project Structure & Configuration

**Study These Files:**
```
nuxt.config.ts           # Build configuration, modules, routing rules
package.json             # Dependencies, scripts, ecosystem
app/
├── composables/         # Reusable logic (the heart of the app)
├── components/          # Vue components (186 files)
├── pages/               # File-based routing
├── layouts/             # App layouts
├── middleware/          # Route guards
├── plugins/             # Global plugins
├── utils/               # Pure functions
└── constants/           # Static values
```

**Tasks:**
1. **Read & Annotate**:
   - Open `nuxt.config.ts` and understand each section
   - Note how modules are configured (i18n, PWA, UnoCSS)
   - Understand the alias system and import strategy

2. **Draw a Dependency Graph**:
   ```
   Create a visual map:
   nuxt.config → modules → composables → components → pages
   ```

3. **Explore Build Output**:
   ```bash
   pnpm install
   pnpm build
   # Examine .output/public to see how assets are bundled
   ```

**Key Concepts to Learn:**
- Nuxt auto-imports mechanism
- SSR vs CSR rendering strategies
- Route rules and prerendering
- Module system and composition

#### Day 3-4: Composables Deep Dive

**Essential Composables to Study** (in order):

1. **`app/composables/settings/definition.ts`**
   - Learn: Type definitions for app settings
   - Pattern: How to structure configuration types
   - Notice: Default values, type safety, backward compatibility

2. **`app/composables/settings/storage.ts`**
   - Learn: Client-side persistence patterns
   - Pattern: Reactive storage with computed properties
   - Notice: How settings sync across tabs

3. **`app/composables/i18n.ts`**
   - Learn: i18n utilities beyond the @nuxtjs/i18n module
   - Pattern: Custom formatting for numbers, dates, file sizes
   - Notice: How locale-aware formatters are created

4. **`app/composables/users.ts`**
   - Learn: User state management
   - Pattern: Composable + Pinia store integration
   - Notice: How derived state is computed

5. **`app/composables/masto/`**
   - Learn: API client abstraction
   - Pattern: How to wrap external libraries
   - Notice: Error handling, caching strategies

**Practice Exercise:**
```typescript
// Create your own composable following Elk patterns
// File: composables/useTheme.ts

import { computed, ref, watch } from 'vue'

export function useTheme() {
  const isDark = ref(false)
  const primaryColor = ref('#5B7BA1')
  
  const cssVariables = computed(() => ({
    '--theme-primary': primaryColor.value,
    '--theme-bg': isDark.value ? '#1a1a1a' : '#ffffff',
  }))
  
  watch(cssVariables, (vars) => {
    Object.entries(vars).forEach(([key, value]) => {
      document.documentElement.style.setProperty(key, value)
    })
  }, { immediate: true })
  
  return {
    isDark,
    primaryColor,
    toggleDark: () => isDark.value = !isDark.value,
  }
}
```

#### Day 5-6: Component Architecture

**Study Component Organization:**

```
components/
├── common/          # Shared UI primitives (Button, Input, Modal)
├── status/          # Post/status related components
├── user/            # User profile components
├── publish/         # Compose/publish components
├── settings/        # Settings UI
├── nav/             # Navigation components
└── content/         # Content rendering
```

**Pattern Analysis Tasks:**

1. **Component Naming Convention**:
   ```
   Pick 10 components and identify the pattern:
   - CommonButton.vue
   - StatusCard.vue
   - UserAvatar.vue
   
   Notice: PascalCase, prefixed by domain
   ```

2. **Component Composition**:
   ```
   Open: app/components/status/StatusCard.vue
   
   Analyze:
   - How many child components does it use?
   - What composables are imported?
   - How is state managed?
   - What props are passed down?
   ```

3. **Props vs Composables Decision**:
   ```
   Study when to use:
   - Props: For data passed from parent
   - Composables: For shared logic/state
   - Slots: For content projection
   - Provide/Inject: For deep prop drilling
   ```

#### Day 7: Week 1 Synthesis

**Build a Mini Project:**

Create a simplified version of a core feature:

```
Project: User Profile Card Component

Requirements:
1. Create a composable for user data fetching
2. Build a ProfileCard component
3. Implement loading/error states
4. Add i18n support for labels
5. Make it theme-aware

File Structure:
composables/
  useUserProfile.ts
components/
  profile/
    ProfileCard.vue
    ProfileAvatar.vue
    ProfileStats.vue
```

**Reflection Questions:**
- What patterns did you identify?
- What surprised you?
- What would you do differently?
- What do you still not understand?

---

### Week 2: State Management & Data Flow
**Goal**: Master Pinia stores, composable patterns, and data fetching

#### Day 8-9: Pinia Store Architecture

**Study These Stores:**

1. **Account Store Pattern**:
   ```
   Look for store files (search for defineStore)
   
   Notice:
   - How stores are organized
   - State structure
   - Actions vs Getters
   - Store composition (stores using other stores)
   ```

2. **Composable + Store Integration**:
   ```typescript
   // Common pattern in Elk:
   
   // store.ts
   export const useUserStore = defineStore('user', () => {
     const currentUser = ref(null)
     const isLoggedIn = computed(() => !!currentUser.value)
     
     async function login(credentials) {
       // API call
     }
     
     return { currentUser, isLoggedIn, login }
   })
   
   // composable.ts
   export function useAuth() {
     const userStore = useUserStore()
     
     // Add extra logic on top of store
     const greeting = computed(() => 
       userStore.isLoggedIn 
         ? `Hello, ${userStore.currentUser.name}`
         : 'Welcome'
     )
     
     return {
       ...userStore,
       greeting,
     }
   }
   ```

**Study Pattern:**
```
1. Find a feature (e.g., notifications)
2. Trace the data flow:
   API → Store → Composable → Component
3. Document each step
```

#### Day 10-11: Advanced Composable Patterns

**Pattern 1: Composable Composition**
```typescript
// app/composables/timeline.ts
export function useTimeline() {
  const { formatHumanReadableNumber } = useHumanReadableNumber()
  const { formatDate } = useFormattedDateTime()
  const { t } = useI18n()
  
  // Combine multiple composables
  return {
    // ... combined functionality
  }
}
```

**Pattern 2: Dependency Injection Pattern**
```typescript
// Provider component
const timelineContext = {
  refreshTimeline: () => { /* ... */ },
  loadMore: () => { /* ... */ },
}
provide('timeline', timelineContext)

// Consumer component/composable
const timeline = inject('timeline')
```

**Pattern 3: Lifecycle-Aware Composables**
```typescript
export function useAutoRefresh(callback, interval = 30000) {
  const { pause, resume } = useIntervalFn(callback, interval)
  
  // Pause on visibility change
  useDocumentVisibility((visible) => {
    if (visible) resume()
    else pause()
  })
  
  return { pause, resume }
}
```

**Exercise:**
Study `app/composables/paginator.ts` and recreate a simplified version:
```typescript
// Your implementation
export function useSimplePaginator(fetchFn) {
  const items = ref([])
  const isLoading = ref(false)
  const hasMore = ref(true)
  
  async function loadMore() {
    // Implement pagination logic
  }
  
  return {
    items,
    isLoading,
    hasMore,
    loadMore,
  }
}
```

#### Day 12-13: i18n Deep Dive

**Study Files:**
1. `config/i18n.ts` - Locale configuration
2. `config/i18n.config.ts` - Runtime config
3. `app/composables/i18n.ts` - Custom utilities
4. `locales/en.json` - Translation structure

**Key Patterns to Learn:**

1. **Pluralization Rules**:
   ```typescript
   // From config/i18n.ts
   pluralRule: (choice: number) => {
     const name = new Intl.PluralRules('ru-RU').select(choice)
     return { 
       zero: 2, 
       one: 0, 
       few: 1, 
       many: 2, 
       other: 3 
     }[name]
   }
   ```

2. **Number Formatting**:
   ```typescript
   // From app/composables/i18n.ts
   export function useHumanReadableNumber() {
     const { n, locale } = useI18n()
     
     return (num: number) => {
       return n(
         num,
         num < 10000 ? 'smallCounting' : 
         num < 1000000 ? 'kiloCounting' : 
         'millionCounting',
         locale.value,
       )
     }
   }
   ```

3. **DateTime Formatting**:
   ```typescript
   export function useFormattedDateTime(value, options) {
     const { locale } = useI18n()
     const formatter = computed(() => 
       Intl.DateTimeFormat(locale.value, options)
     )
     return computed(() => formatter.value.format(new Date(value)))
   }
   ```

**Exercise - Build i18n Utils:**
```typescript
// Create: composables/useAdvancedI18n.ts

export function useAdvancedI18n() {
  const { t, locale } = useI18n()
  
  // 1. Implement a smart date formatter
  function smartDate(date: Date) {
    const now = new Date()
    const diff = now - date
    const days = diff / (1000 * 60 * 60 * 24)
    
    if (days < 1) return 'Today'
    if (days < 2) return 'Yesterday'
    if (days < 7) return formatRelative(date)
    return formatAbsolute(date)
  }
  
  // 2. Implement currency formatter
  function formatCurrency(amount: number, currency = 'USD') {
    return new Intl.NumberFormat(locale.value, {
      style: 'currency',
      currency,
    }).format(amount)
  }
  
  // 3. Implement list formatter
  function formatList(items: string[], type: 'and' | 'or' = 'and') {
    return new Intl.ListFormat(locale.value, { 
      type: 'conjunction',
      style: 'long',
    }).format(items)
  }
  
  return {
    smartDate,
    formatCurrency,
    formatList,
  }
}
```

#### Day 14: Week 2 Synthesis

**Build a Feature:**

```
Project: Notification System

Implement:
1. Pinia store for notifications
   - State: notifications[], unreadCount
   - Actions: fetch, markRead, markAllRead
   
2. Composable useNotifications
   - Auto-refresh every 30s
   - Desktop notification API integration
   - i18n for notification text
   
3. Component NotificationCenter.vue
   - List of notifications
   - Mark as read functionality
   - Loading states
   - Empty state
   
4. i18n support
   - Create en.json translations
   - Support pluralization ("1 notification" vs "5 notifications")
```

---

### Week 3: Advanced Patterns & Performance
**Goal**: Learn optimization techniques, accessibility, and advanced Vue patterns

#### Day 15-16: Performance Optimization Patterns

**Study These Techniques:**

1. **Virtual Scrolling** (app/components with vue-virtual-scroller):
   ```vue
   <template>
     <DynamicScroller
       :items="items"
       :min-item-size="100"
       :buffer="200"
     >
       <template #default="{ item, index, active }">
         <DynamicScrollerItem :item="item" :active="active">
           <StatusCard :status="item" />
         </DynamicScrollerItem>
       </template>
     </DynamicScroller>
   </template>
   ```
   
   **Why it matters**: Rendering 1000+ posts without lag

2. **Lazy Loading Images** (search for @unlazy/nuxt usage):
   ```vue
   <template>
     <NuxtImg
       :src="avatarUrl"
       :placeholder="blurhash"
       loading="lazy"
     />
   </template>
   ```

3. **Code Splitting** (observe dynamic imports):
   ```typescript
   // Instead of:
   import HeavyComponent from './HeavyComponent.vue'
   
   // Do:
   const HeavyComponent = defineAsyncComponent(
     () => import('./HeavyComponent.vue')
   )
   ```

4. **Computed vs Ref Optimization**:
   ```typescript
   // ❌ Bad: Runs on every access
   function getTotalCount() {
     return items.value.reduce((sum, item) => sum + item.count, 0)
   }
   
   // ✅ Good: Cached, only recomputes when items change
   const totalCount = computed(() => 
     items.value.reduce((sum, item) => sum + item.count, 0)
   )
   ```

**Exercise - Performance Audit:**
```
1. Install vite-plugin-inspect:
   npm i -D vite-plugin-inspect
   
2. Add to nuxt.config.ts:
   vite: {
     plugins: [inspect()],
   }
   
3. Run dev server and visit:
   http://localhost:5314/__inspect/
   
4. Analyze:
   - Bundle sizes
   - Which components are heavy?
   - Which dependencies are large?
   - What can be lazy-loaded?
```

#### Day 17-18: Accessibility Patterns

**Study ARIA Implementation:**

1. **Keyboard Navigation** (app/composables/magickeys.ts):
   ```typescript
   export function useMagicKeys() {
     whenever(keys['?'], () => {
       // Show keyboard shortcuts help
     })
     
     whenever(keys.n, () => {
       // Focus compose box
     })
   }
   ```

2. **Focus Management** (search for useFocusTrap):
   ```typescript
   import { useFocusTrap } from '@vueuse/integrations/useFocusTrap'
   
   export function useModal() {
     const modalEl = ref(null)
     const { activate, deactivate } = useFocusTrap(modalEl)
     
     function open() {
       activate()
     }
     
     function close() {
       deactivate()
     }
     
     return { modalEl, open, close }
   }
   ```

3. **Screen Reader Support** (app/components/aria/):
   ```vue
   <template>
     <div role="region" :aria-label="t('timeline.home')">
       <button
         :aria-label="t('action.post')"
         :aria-describedby="helpId"
       >
         <span aria-hidden="true">📝</span>
         {{ t('action.post') }}
       </button>
       <div :id="helpId" class="sr-only">
         {{ t('help.compose') }}
       </div>
     </div>
   </template>
   ```

**Exercise - Accessible Form:**
```vue
<!-- Create: components/AccessibleForm.vue -->
<template>
  <form @submit.prevent="handleSubmit">
    <!-- 1. Implement proper labels -->
    <label :for="inputId">
      {{ label }}
      <span v-if="required" aria-label="required">*</span>
    </label>
    
    <!-- 2. Error handling with ARIA -->
    <input
      :id="inputId"
      v-model="value"
      :aria-invalid="hasError"
      :aria-describedby="errorId"
    />
    
    <!-- 3. Error message -->
    <div v-if="hasError" :id="errorId" role="alert">
      {{ errorMessage }}
    </div>
    
    <!-- 4. Character count for textarea -->
    <div
      role="status"
      :aria-live="isNearLimit ? 'polite' : 'off'"
    >
      {{ remaining }} characters remaining
    </div>
  </form>
</template>
```

#### Day 19-20: Advanced Vue Patterns

**Pattern 1: Renderless Components**
```vue
<!-- RenderlessToggle.vue -->
<script setup lang="ts">
const isOpen = ref(false)
const toggle = () => isOpen.value = !isOpen.value

defineExpose({ isOpen, toggle })
</script>

<template>
  <slot :is-open="isOpen" :toggle="toggle" />
</template>

<!-- Usage -->
<RenderlessToggle v-slot="{ isOpen, toggle }">
  <button @click="toggle">
    {{ isOpen ? 'Close' : 'Open' }}
  </button>
  <div v-if="isOpen">Content</div>
</RenderlessToggle>
```

**Pattern 2: Composable + Component Hybrid**
```typescript
// useDropdown.ts
export function useDropdown() {
  const isOpen = ref(false)
  const triggerEl = ref(null)
  const menuEl = ref(null)
  
  onClickOutside(menuEl, () => {
    if (isOpen.value) isOpen.value = false
  })
  
  return {
    isOpen,
    triggerEl,
    menuEl,
    toggle: () => isOpen.value = !isOpen.value,
  }
}

// Dropdown.vue
<script setup>
const dropdown = useDropdown()
</script>

<template>
  <div>
    <button ref="dropdown.triggerEl" @click="dropdown.toggle">
      <slot name="trigger" />
    </button>
    <div v-if="dropdown.isOpen" ref="dropdown.menuEl">
      <slot />
    </div>
  </div>
</template>
```

**Pattern 3: Smart Defaults with Props**
```typescript
interface Props {
  modelValue: string
  placeholder?: string
  maxLength?: number
  autofocus?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  placeholder: 'Type something...',
  maxLength: 500,
  autofocus: false,
})
```

#### Day 21: Week 3 Synthesis

**Build an Optimized Feature:**

```
Project: Infinite Scroll Image Gallery

Requirements:
1. Virtual scrolling for performance
2. Lazy loading images with blurhash placeholders
3. Keyboard navigation (arrow keys)
4. Screen reader announcements
5. Loading states and error handling

Implementation Checklist:
□ Create useGallery composable
  - Pagination logic
  - Image preloading
  - Keyboard navigation
  
□ Build GalleryGrid component
  - Virtual scroller integration
  - Responsive grid layout
  - Loading skeleton
  
□ Implement GalleryItem component
  - Lazy loaded image
  - Blurhash placeholder
  - Focus management
  - ARIA labels
  
□ Add accessibility
  - Keyboard shortcuts
  - Screen reader support
  - Focus indicators
```

---

### Week 4: Integration & Real-World Patterns
**Goal**: PWA, advanced TypeScript, testing, and production patterns

#### Day 22-23: PWA & Service Worker Patterns

**Study PWA Implementation:**

1. **PWA Configuration** (config/pwa.ts):
   ```typescript
   export const pwa = {
     registerType: 'prompt',
     manifest: {
       name: 'Elk',
       short_name: 'Elk',
       theme_color: '#5B7BA1',
       icons: [/* ... */],
     },
     workbox: {
       // Caching strategies
       runtimeCaching: [
         {
           urlPattern: /^https:\/\/api\./,
           handler: 'NetworkFirst',
           options: {
             cacheName: 'api-cache',
             expiration: {
               maxEntries: 50,
               maxAgeSeconds: 300, // 5 min
             },
           },
         },
       ],
     },
   }
   ```

2. **Push Notifications** (app/composables/push-notifications/):
   ```typescript
   export function usePushNotifications() {
     const permission = ref(Notification.permission)
     
     async function requestPermission() {
       const result = await Notification.requestPermission()
       permission.value = result
       return result === 'granted'
     }
     
     function sendNotification(title, options) {
       if (permission.value === 'granted') {
         return new Notification(title, options)
       }
     }
     
     return {
       permission,
       requestPermission,
       sendNotification,
     }
   }
   ```

**Exercise:**
```
Create a mini PWA with:
1. Offline support
2. Install prompt
3. Push notifications
4. Background sync
```

#### Day 24-25: Advanced TypeScript Patterns

**Study Type Patterns in Elk:**

1. **Discriminated Unions**:
   ```typescript
   type LoadingState<T> =
     | { status: 'idle' }
     | { status: 'loading' }
     | { status: 'success'; data: T }
     | { status: 'error'; error: Error }
   
   function renderState<T>(state: LoadingState<T>) {
     switch (state.status) {
       case 'idle':
         return 'Not started'
       case 'loading':
         return 'Loading...'
       case 'success':
         return `Data: ${state.data}`
       case 'error':
         return `Error: ${state.error.message}`
     }
   }
   ```

2. **Generic Composables**:
   ```typescript
   export function useAsyncData<T>(
     fetcher: () => Promise<T>,
     options?: {
       immediate?: boolean
       onError?: (error: Error) => void
     }
   ) {
     const data = ref<T | null>(null)
     const loading = ref(false)
     const error = ref<Error | null>(null)
     
     async function execute() {
       loading.value = true
       error.value = null
       
       try {
         data.value = await fetcher()
       } catch (e) {
         error.value = e as Error
         options?.onError?.(error.value)
       } finally {
         loading.value = false
       }
     }
     
     if (options?.immediate) {
       execute()
     }
     
     return {
       data: data as Ref<T | null>,
       loading: readonly(loading),
       error: readonly(error),
       execute,
     }
   }
   ```

3. **Utility Types**:
   ```typescript
   // Make all properties optional recursively
   type DeepPartial<T> = {
     [P in keyof T]?: T[P] extends object 
       ? DeepPartial<T[P]> 
       : T[P]
   }
   
   // Extract function return type
   type AsyncReturnType<T extends (...args: any) => Promise<any>> = 
     T extends (...args: any) => Promise<infer R> ? R : never
   
   // Usage
   async function fetchUser() {
     return { id: 1, name: 'John' }
   }
   
   type User = AsyncReturnType<typeof fetchUser>
   // User = { id: number; name: string }
   ```

#### Day 26-27: Testing Patterns

**Study Test Files:**

```bash
# Find test files
find . -name "*.test.ts" -o -name "*.spec.ts"
```

**Testing Patterns:**

1. **Composable Testing**:
   ```typescript
   import { describe, expect, it } from 'vitest'
   import { useCounter } from './useCounter'
   
   describe('useCounter', () => {
     it('increments count', () => {
       const { count, increment } = useCounter()
       
       expect(count.value).toBe(0)
       increment()
       expect(count.value).toBe(1)
     })
     
     it('decrements count', () => {
       const { count, decrement } = useCounter(10)
       
       expect(count.value).toBe(10)
       decrement()
       expect(count.value).toBe(9)
     })
   })
   ```

2. **Component Testing**:
   ```typescript
   import { mount } from '@vue/test-utils'
   import { describe, expect, it } from 'vitest'
   import MyButton from './MyButton.vue'
   
   describe('MyButton', () => {
     it('renders properly', () => {
       const wrapper = mount(MyButton, {
         props: { label: 'Click me' }
       })
       
       expect(wrapper.text()).toContain('Click me')
     })
     
     it('emits click event', async () => {
       const wrapper = mount(MyButton)
       await wrapper.trigger('click')
       
       expect(wrapper.emitted()).toHaveProperty('click')
     })
   })
   ```

**Exercise - Write Tests:**
```
Test your notification system from Week 2:
1. Test the composable
2. Test the store
3. Test the component
```

#### Day 28: Final Project - Build a Mini Elk

**Bring It All Together:**

```
Project: Mini Social App

Features to Implement:
□ Authentication (login/logout)
□ Timeline (infinite scroll)
□ Compose post
□ User profiles
□ Notifications
□ i18n (2+ languages)
□ Dark mode
□ PWA support
□ Accessibility
□ TypeScript throughout

Architecture Requirements:
□ Use composables for all logic
□ Pinia for global state
□ File-based routing
□ Component library (at least 10 components)
□ Utility functions
□ Custom i18n utilities
□ Performance optimizations
□ ARIA support
□ Unit tests (>70% coverage)

Directory Structure:
my-social-app/
├── app/
│   ├── composables/
│   │   ├── useAuth.ts
│   │   ├── useTimeline.ts
│   │   ├── useNotifications.ts
│   │   └── useI18n.ts
│   ├── stores/
│   │   ├── user.ts
│   │   └── posts.ts
│   ├── components/
│   │   ├── common/
│   │   ├── post/
│   │   └── user/
│   ├── pages/
│   │   ├── index.vue
│   │   ├── profile.vue
│   │   └── notifications.vue
│   └── utils/
│       ├── date.ts
│       └── validation.ts
├── config/
│   ├── i18n.ts
│   └── pwa.ts
├── locales/
│   ├── en.json
│   └── es.json
└── tests/
    └── ...
```

---

## 🔍 Specific Learning Focus Areas

### 1. Composable Architecture

**Why Elk Excels:**
- Logic is extracted from components
- Reusable across the app
- Testable in isolation
- Type-safe

**Study These Composables in Detail:**

```
Priority 1 (Must Study):
├── composables/i18n.ts          # i18n utilities
├── composables/users.ts          # User management
├── composables/timeline.ts       # Timeline logic
└── composables/settings/         # Settings persistence

Priority 2 (Important):
├── composables/paginator.ts      # Pagination pattern
├── composables/dialog.ts         # Modal management
├── composables/emojis.ts         # Emoji picker
└── composables/notification.ts   # Notifications

Priority 3 (Advanced):
├── composables/tiptap/           # Rich text editor integration
├── composables/push-notifications/ # PWA notifications
├── composables/masto/            # API client
└── composables/idb/              # IndexedDB storage
```

### 2. i18n Implementation

**What Makes Elk's i18n Special:**

1. **30+ Languages** with proper pluralization
2. **RTL Support** (Arabic, Persian, etc.)
3. **Custom Formatters** (numbers, dates, file sizes)
4. **Performance** (lazy-loaded locale files)
5. **Type Safety** (TypeScript integration)

**Key Files to Study:**
```
config/i18n.ts              # Locale definitions, plural rules
config/i18n.config.ts       # Runtime configuration
app/composables/i18n.ts     # Custom utilities
locales/en.json             # Translation structure
```

**Patterns to Learn:**

```typescript
// 1. Plural Rules for Complex Languages
pluralRule: (choice: number) => {
  const name = new Intl.PluralRules('ar-EG').select(choice)
  return { zero: 0, one: 1, two: 2, few: 3, many: 4, other: 5 }[name]
}

// 2. Smart Number Formatting
export function useHumanReadableNumber() {
  return (num: number) => {
    return num < 10000 
      ? formatSmall(num)
      : num < 1000000 
        ? formatKilo(num) 
        : formatMillion(num)
  }
}

// 3. Context-Aware Date Formatting
export function useTimeAgo(short = false) {
  const { t, locale } = useI18n()
  return {
    showSecond: !short,
    updateInterval: short ? 60000 : 1000,
    messages: {
      justNow: t('time_ago.just_now'),
      minute: (n) => t('time_ago.minute', n),
      // ...
    },
  }
}
```

### 3. Component Design Patterns

**Pattern 1: Compound Components**
```
StatusCard/
├── StatusCard.vue
├── StatusHeader.vue
├── StatusBody.vue
├── StatusActions.vue
└── StatusFooter.vue
```

**Pattern 2: Headless/Renderless**
```vue
<template>
  <slot v-bind="{ data, loading, error, retry }" />
</template>
```

**Pattern 3: Container/Presenter**
```
TimelineContainer.vue (logic) → TimelineView.vue (UI)
```

### 4. State Management Philosophy

**When to Use What:**

```
Ref/Reactive:
└── Component-local state

Composables:
└── Shared logic (can be stateful or stateless)

Pinia Stores:
└── Global state (user, settings, cache)

Props/Emit:
└── Parent-child communication

Provide/Inject:
└── Deep prop passing (theme, i18n context)
```

### 5. Performance Techniques

**Elk's Performance Arsenal:**

1. **Virtual Scrolling**: Only render visible items
2. **Code Splitting**: Lazy load routes and heavy components
3. **Image Optimization**: Blurhash placeholders, lazy loading
4. **Caching Strategies**: Service worker, HTTP cache headers
5. **Bundle Size**: Tree-shaking, dynamic imports
6. **Reactivity Optimization**: Computed over methods, shallowRef
7. **SSR**: Server-side rendering for initial load

**Study Pattern:**
```typescript
// Find and document all instances of:
- defineAsyncComponent()
- <Suspense>
- shallowRef()
- markRaw()
- v-once
- v-memo
```

---

## 🛠️ Practical Exercises & Challenges

### Week 1 Challenges

**Challenge 1: Recreate the Config**
```
Task: Create nuxt.config.ts from scratch with:
- TypeScript support
- 3 modules of your choice
- Custom aliases
- Runtime config
- Route rules
```

**Challenge 2: Build a Settings System**
```
Requirements:
1. Type-safe settings definition
2. Reactive storage (localStorage)
3. Settings page UI
4. Export/import functionality
```

### Week 2 Challenges

**Challenge 3: Paginated API Client**
```
Create a composable that:
1. Fetches paginated data
2. Handles loading states
3. Implements infinite scroll
4. Supports refresh
5. Caches results
```

**Challenge 4: Multi-language App**
```
Add i18n to your existing app:
1. Support 3 languages
2. Implement language switcher
3. Add RTL support for one language
4. Create custom date formatter
5. Handle pluralization
```

### Week 3 Challenges

**Challenge 5: Accessible Modal**
```
Build a modal component with:
1. Focus trap
2. Keyboard navigation (ESC to close)
3. ARIA attributes
4. Screen reader announcements
5. Animation
```

**Challenge 6: Performance Optimization**
```
Take a slow component and optimize it:
1. Profile with Vue DevTools
2. Identify bottlenecks
3. Apply 3 optimization techniques
4. Measure improvements
```

### Week 4 Challenges

**Challenge 7: PWA Features**
```
Add to your app:
1. Install prompt
2. Offline page
3. Background sync
4. Push notifications
5. App shortcuts
```

**Challenge 8: Full Test Suite**
```
Write tests for:
1. 3 composables (unit tests)
2. 5 components (component tests)
3. 1 integration test (full flow)
4. Achieve >70% coverage
```

---

## 📚 Deep Dive Topics

### Topic 1: Auto-Imports System

**How Nuxt Auto-Imports Work:**

```typescript
// nuxt.config.ts
imports: {
  dirs: [
    './composables/masto',
    './composables/push-notifications',
  ],
}

// Now you can use anywhere without import:
const user = useUserStore() // Auto-imported!
const { t } = useI18n()     // Auto-imported!
```

**Learn:**
1. How Nuxt scans directories
2. How to configure custom imports
3. Type generation for auto-imports
4. Debugging import issues

### Topic 2: TipTap Integration

**Rich Text Editor Architecture:**

Study: `app/composables/tiptap/`

```typescript
// Pattern: Extensible editor configuration
export function useTiptap(options) {
  const editor = useEditor({
    extensions: [
      Document,
      Paragraph,
      Text,
      Bold,
      Italic,
      Mention.configure({
        // Custom mention handling
      }),
    ],
    // ...
  })
  
  return { editor }
}
```

### Topic 3: UnoCSS Patterns

**Atomic CSS Philosophy:**

```vue
<template>
  <!-- Traditional CSS -->
  <div class="card"></div>
  
  <!-- UnoCSS -->
  <div class="p-4 rounded-lg bg-white shadow-md hover:shadow-lg">
    <!-- Utility classes, instant -->
  </div>
</template>

<!-- Shortcuts -->
<style>
.btn {
  @apply px-4 py-2 rounded bg-primary text-white;
}
</style>
```

### Topic 4: Deployment Patterns

**Study Docker Setup:**

```dockerfile
# Dockerfile
FROM node:18-alpine

WORKDIR /app
COPY package*.json ./
RUN pnpm install

COPY . .
RUN pnpm build

EXPOSE 3000
CMD ["pnpm", "start"]
```

**Study Deployment Configs:**
- Netlify
- Vercel
- Cloudflare Pages
- Docker Compose

---

## 🎓 Learning Resources

### Must-Read Files (in order):

1. `nuxt.config.ts` - Understand build setup
2. `package.json` - Know the dependencies
3. `app/composables/settings/definition.ts` - Settings architecture
4. `config/i18n.ts` - i18n setup
5. `app/composables/i18n.ts` - Custom i18n utilities
6. `app/composables/users.ts` - State management
7. Choose 5 components from different domains

### External Resources:

**Official Docs:**
- Vue 3: https://vuejs.org/
- Nuxt: https://nuxt.com/
- Pinia: https://pinia.vuejs.org/
- VueUse: https://vueuse.org/
- UnoCSS: https://uno.antfu.me/

**Advanced Topics:**
- vue-i18n: https://vue-i18n.intlify.dev/
- TipTap: https://tiptap.dev/
- Workbox (PWA): https://developer.chrome.com/docs/workbox

**Best Practices:**
- Vue Style Guide: https://vuejs.org/style-guide/
- TypeScript Handbook: https://www.typescriptlang.org/docs/

---

## ✅ Daily Learning Routine

### Effective Study Schedule:

**Morning (1-2 hours):**
```
1. Pick a file to study (15 min)
2. Read and annotate code (30 min)
3. Identify patterns (15 min)
4. Write notes/diagrams (20 min)
```

**Afternoon (1-2 hours):**
```
1. Recreate a pattern from scratch (30 min)
2. Build a mini version (45 min)
3. Compare with original (15 min)
```

**Evening (30 min):**
```
1. Review notes from the day
2. Update your learning journal
3. Plan tomorrow's focus
```

### Learning Journal Template:

```markdown
# Day X - [Date]

## What I Studied
- File: app/composables/...
- Pattern: ...
- Key concepts: ...

## What I Learned
1. ...
2. ...
3. ...

## Questions/Confusion
- Why does X work this way?
- How does Y integrate with Z?

## What I Built
- Project: ...
- Patterns applied: ...
- Challenges faced: ...

## Tomorrow's Focus
- [ ] Study: ...
- [ ] Build: ...
- [ ] Review: ...
```

---

## 🎯 Progress Checklist

### Week 1: Foundation ✓
- [ ] Understand project structure
- [ ] Grasp Nuxt configuration
- [ ] Study 10+ composables
- [ ] Analyze 20+ components
- [ ] Build mini project

### Week 2: State & Data ✓
- [ ] Master Pinia patterns
- [ ] Understand composable composition
- [ ] Deep dive i18n
- [ ] Build notification system

### Week 3: Advanced Patterns ✓
- [ ] Learn performance optimization
- [ ] Implement accessibility
- [ ] Study advanced Vue patterns
- [ ] Build optimized gallery

### Week 4: Integration ✓
- [ ] Understand PWA
- [ ] Master TypeScript patterns
- [ ] Write tests
- [ ] Build complete mini app

---

## 🚀 Beyond One Month

### After Mastering Elk:

**Next Steps:**
1. **Contribute to Elk**: Fix bugs, add features
2. **Build Your Own Project**: Apply patterns to real app
3. **Study Other Codebases**:
   - Nuxt 4 source
   - VueUse source
   - Anthony Fu's projects
4. **Deep Dive Specific Areas**:
   - SSR internals
   - Vite plugins
   - Browser APIs
   - Web performance

### Portfolio Projects:

Use Elk patterns to build:
1. Blog platform
2. Chat application
3. Project management tool
4. E-commerce site
5. Content management system

---

## 💡 Pro Tips

### Dos:
- ✅ Take notes as you read code
- ✅ Recreate patterns from scratch
- ✅ Ask "why" not just "what"
- ✅ Build real projects
- ✅ Compare your code with Elk
- ✅ Use TypeScript from day 1
- ✅ Focus on patterns over syntax
- ✅ Study git history (see evolution)

### Don'ts:
- ❌ Don't copy-paste without understanding
- ❌ Don't skip the basics
- ❌ Don't study passively
- ❌ Don't try to learn everything at once
- ❌ Don't ignore error messages
- ❌ Don't skip writing tests
- ❌ Don't forget to take breaks

---

## 🎓 Final Thoughts

Elk is a masterclass in modern Vue/Nuxt development. The patterns you learn here—composable architecture, type-safe i18n, performance optimization, accessibility—are directly applicable to professional work.

**Remember:**
- **Learning is non-linear**: You'll have "aha!" moments weeks after first seeing something
- **Build, don't just read**: The act of coding solidifies learning
- **Patterns over code**: Understand the "why" and you can apply it anywhere
- **Quality over speed**: Deep learning takes time
- **Enjoy the process**: This is genuinely good code to learn from

**You're learning from the best:**
- Anthony Fu (Vue/Vite core team)
- Daniel Roe (Nuxt core team)
- 三咲智子 Kevin Deng (Vue community)
- Patak (Vite core team)

Their patterns and practices represent years of experience and deep understanding of the Vue ecosystem.

Good luck on your learning journey! 🦌✨

---

## 📞 Get Help

- **Elk Discord**: https://chat.elk.zone
- **GitHub Discussions**: https://github.com/elk-zone/elk/discussions
- **Vue Discord**: https://chat.vuejs.org/
- **Nuxt Discord**: https://discord.nuxt.com/

---

*This roadmap is a living document. Update it as you learn and discover new patterns!*
