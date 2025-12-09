# 🎯 Elk Patterns Quick Reference

> A quick lookup guide for common patterns found in the Elk codebase

---

## Composable Patterns

### Pattern: Shared State Composable
```typescript
// composables/useCounter.ts
const count = ref(0) // Shared across all usages

export function useCounter() {
  function increment() {
    count.value++
  }
  
  return {
    count: readonly(count),
    increment,
  }
}

// Usage: Same count everywhere
const { count: count1 } = useCounter() // 0
const { count: count2 } = useCounter() // same ref, still 0
```

### Pattern: Instance Composable
```typescript
// composables/useTimer.ts
export function useTimer() {
  const count = ref(0) // New instance each time
  
  const { pause, resume } = useIntervalFn(() => {
    count.value++
  }, 1000)
  
  return { count, pause, resume }
}

// Usage: Independent instances
const timer1 = useTimer() // count: 0
const timer2 = useTimer() // different count: 0
```

### Pattern: Options Composable
```typescript
// composables/useFetch.ts
export function useFetch<T>(
  url: MaybeRef<string>,
  options: {
    immediate?: boolean
    refetch?: Ref<boolean>
    onSuccess?: (data: T) => void
    onError?: (error: Error) => void
  } = {}
) {
  const {
    immediate = true,
    refetch,
    onSuccess,
    onError,
  } = options
  
  const data = ref<T | null>(null)
  const loading = ref(false)
  const error = ref<Error | null>(null)
  
  async function execute() {
    loading.value = true
    try {
      const response = await fetch(unref(url))
      data.value = await response.json()
      onSuccess?.(data.value)
    } catch (e) {
      error.value = e as Error
      onError?.(error.value)
    } finally {
      loading.value = false
    }
  }
  
  if (immediate) execute()
  if (refetch) watch(refetch, execute)
  
  return { data, loading, error, execute }
}
```

### Pattern: Composable Composition
```typescript
// composables/useUserProfile.ts
export function useUserProfile(userId: MaybeRef<string>) {
  // Compose multiple composables
  const { data: user, loading } = useFetch(`/api/users/${unref(userId)}`)
  const { formatDate } = useFormattedDateTime()
  const { t } = useI18n()
  
  const displayName = computed(() => 
    user.value?.name || t('common.anonymous')
  )
  
  const memberSince = computed(() => 
    user.value?.created_at 
      ? formatDate(user.value.created_at)
      : ''
  )
  
  return {
    user,
    loading,
    displayName,
    memberSince,
  }
}
```

---

## Component Patterns

### Pattern: Props with Defaults
```vue
<script setup lang="ts">
interface Props {
  title: string
  subtitle?: string
  size?: 'sm' | 'md' | 'lg'
  variant?: 'primary' | 'secondary'
  disabled?: boolean
}

const props = withDefaults(defineProps<Props>(), {
  subtitle: '',
  size: 'md',
  variant: 'primary',
  disabled: false,
})
</script>
```

### Pattern: v-model Composable
```vue
<script setup lang="ts">
interface Props {
  modelValue: string
}

interface Emits {
  (e: 'update:modelValue', value: string): void
}

const props = defineProps<Props>()
const emit = defineEmits<Emits>()

// Create local model
const model = useVModel(props, 'modelValue', emit)

// Or manually:
const model = computed({
  get: () => props.modelValue,
  set: (value) => emit('update:modelValue', value),
})
</script>

<template>
  <input v-model="model" />
</template>
```

### Pattern: Slot Props
```vue
<!-- RenderlessAutocomplete.vue -->
<script setup lang="ts">
const query = ref('')
const results = ref([])
const isLoading = ref(false)

const search = useDebounceFn(async () => {
  isLoading.value = true
  results.value = await searchAPI(query.value)
  isLoading.value = false
}, 300)

watch(query, search)
</script>

<template>
  <slot
    :query="query"
    :results="results"
    :is-loading="isLoading"
    :on-input="(v) => query = v"
  />
</template>

<!-- Usage -->
<RenderlessAutocomplete v-slot="{ query, results, isLoading, onInput }">
  <input :value="query" @input="onInput($event.target.value)" />
  <div v-if="isLoading">Loading...</div>
  <ul v-else>
    <li v-for="item in results" :key="item.id">
      {{ item.name }}
    </li>
  </ul>
</RenderlessAutocomplete>
```

### Pattern: Provide/Inject Context
```vue
<!-- Parent: TimelineProvider.vue -->
<script setup lang="ts">
const refresh = async () => { /* ... */ }
const loadMore = async () => { /* ... */ }
const isLoading = ref(false)

provide('timeline', {
  refresh,
  loadMore,
  isLoading: readonly(isLoading),
})
</script>

<!-- Child: TimelineItem.vue -->
<script setup lang="ts">
const timeline = inject('timeline')

// Use provided methods
const handleRefresh = () => {
  timeline.refresh()
}
</script>
```

---

## State Management Patterns

### Pattern: Pinia Store (Setup Syntax)
```typescript
// stores/user.ts
export const useUserStore = defineStore('user', () => {
  // State
  const user = ref<User | null>(null)
  const token = ref<string | null>(null)
  
  // Getters
  const isLoggedIn = computed(() => !!user.value)
  const fullName = computed(() => 
    user.value ? `${user.value.firstName} ${user.value.lastName}` : ''
  )
  
  // Actions
  async function login(email: string, password: string) {
    const response = await api.login(email, password)
    user.value = response.user
    token.value = response.token
  }
  
  function logout() {
    user.value = null
    token.value = null
  }
  
  // Persist to localStorage
  watch([user, token], ([user, token]) => {
    if (user && token) {
      localStorage.setItem('user', JSON.stringify(user))
      localStorage.setItem('token', token)
    } else {
      localStorage.removeItem('user')
      localStorage.removeItem('token')
    }
  }, { deep: true })
  
  // Hydrate from localStorage
  function hydrate() {
    const storedUser = localStorage.getItem('user')
    const storedToken = localStorage.getItem('token')
    
    if (storedUser) user.value = JSON.parse(storedUser)
    if (storedToken) token.value = storedToken
  }
  
  return {
    // State
    user: readonly(user),
    token: readonly(token),
    
    // Getters
    isLoggedIn,
    fullName,
    
    // Actions
    login,
    logout,
    hydrate,
  }
})
```

### Pattern: Store Composition
```typescript
// stores/posts.ts
export const usePostsStore = defineStore('posts', () => {
  const userStore = useUserStore() // Use another store
  
  const posts = ref<Post[]>([])
  
  const myPosts = computed(() => 
    posts.value.filter(post => post.userId === userStore.user?.id)
  )
  
  async function fetchPosts() {
    // Use token from user store
    const response = await api.getPosts({
      headers: { Authorization: `Bearer ${userStore.token}` }
    })
    posts.value = response.data
  }
  
  return { posts, myPosts, fetchPosts }
})
```

---

## i18n Patterns

### Pattern: Basic Translation
```vue
<script setup lang="ts">
const { t } = useI18n()
</script>

<template>
  <h1>{{ t('welcome.title') }}</h1>
  <p>{{ t('welcome.description') }}</p>
</template>
```

```json
// locales/en.json
{
  "welcome": {
    "title": "Welcome",
    "description": "Welcome to our app"
  }
}
```

### Pattern: Pluralization
```vue
<script setup lang="ts">
const { t, n } = useI18n()
const count = ref(5)
</script>

<template>
  <p>{{ t('items.count', count) }}</p>
  <!-- "You have 5 items" -->
</template>
```

```json
// locales/en.json
{
  "items": {
    "count": "You have {n} item | You have {n} items"
  }
}
```

### Pattern: Named Interpolation
```vue
<template>
  <p>{{ t('greeting', { name: userName, time: 'morning' }) }}</p>
  <!-- "Good morning, John!" -->
</template>
```

```json
{
  "greeting": "Good {time}, {name}!"
}
```

### Pattern: Linked Messages
```json
{
  "common": {
    "app_name": "MyApp"
  },
  "welcome": {
    "title": "Welcome to @:common.app_name"
  }
}
```

### Pattern: Custom Number Formatter
```typescript
// composables/useI18n.ts
export function useI18n() {
  const { n, locale } = useI18nBase()
  
  function formatCurrency(amount: number) {
    return new Intl.NumberFormat(locale.value, {
      style: 'currency',
      currency: 'USD',
    }).format(amount)
  }
  
  function formatPercentage(value: number) {
    return new Intl.NumberFormat(locale.value, {
      style: 'percent',
      minimumFractionDigits: 1,
      maximumFractionDigits: 1,
    }).format(value)
  }
  
  return {
    n,
    locale,
    formatCurrency,
    formatPercentage,
  }
}
```

---

## TypeScript Patterns

### Pattern: Generic Function
```typescript
function wrapInArray<T>(value: T | T[]): T[] {
  return Array.isArray(value) ? value : [value]
}

// Usage
wrapInArray(1)           // [1]
wrapInArray([1, 2, 3])   // [1, 2, 3]
wrapInArray('hello')     // ['hello']
```

### Pattern: Discriminated Union
```typescript
type AsyncState<T> =
  | { status: 'idle' }
  | { status: 'loading' }
  | { status: 'success'; data: T }
  | { status: 'error'; error: Error }

function handleState<T>(state: AsyncState<T>) {
  switch (state.status) {
    case 'idle':
      return 'Not started'
    case 'loading':
      return 'Loading...'
    case 'success':
      return state.data // TypeScript knows data exists
    case 'error':
      return state.error.message // TypeScript knows error exists
  }
}
```

### Pattern: Utility Types
```typescript
// Make all properties optional
type Partial<T> = {
  [P in keyof T]?: T[P]
}

// Make all properties required
type Required<T> = {
  [P in keyof T]-?: T[P]
}

// Pick specific properties
type Pick<T, K extends keyof T> = {
  [P in K]: T[P]
}

// Omit specific properties
type Omit<T, K extends keyof T> = Pick<T, Exclude<keyof T, K>>

// Usage
interface User {
  id: number
  name: string
  email: string
  password: string
}

type PublicUser = Omit<User, 'password'>
// { id: number; name: string; email: string }

type UserUpdate = Partial<User>
// { id?: number; name?: string; email?: string; password?: string }
```

### Pattern: Type Guards
```typescript
function isString(value: unknown): value is string {
  return typeof value === 'string'
}

function isUser(value: unknown): value is User {
  return (
    typeof value === 'object' &&
    value !== null &&
    'id' in value &&
    'name' in value
  )
}

// Usage
const value: unknown = getData()

if (isString(value)) {
  // TypeScript knows value is string
  console.log(value.toUpperCase())
}

if (isUser(value)) {
  // TypeScript knows value is User
  console.log(value.name)
}
```

---

## Performance Patterns

### Pattern: Computed Caching
```vue
<script setup lang="ts">
const items = ref([/* large array */])

// ❌ Bad: Recalculates on every access
function getTotalPrice() {
  return items.value.reduce((sum, item) => sum + item.price, 0)
}

// ✅ Good: Cached, only recalculates when items change
const totalPrice = computed(() =>
  items.value.reduce((sum, item) => sum + item.price, 0)
)
</script>
```

### Pattern: Lazy Component Loading
```vue
<script setup lang="ts">
// ❌ Bad: Loaded immediately
import HeavyChart from './HeavyChart.vue'

// ✅ Good: Loaded only when needed
const HeavyChart = defineAsyncComponent(
  () => import('./HeavyChart.vue')
)
</script>

<template>
  <Suspense>
    <template #default>
      <HeavyChart />
    </template>
    <template #fallback>
      <div>Loading chart...</div>
    </template>
  </Suspense>
</template>
```

### Pattern: Debounced Input
```vue
<script setup lang="ts">
const searchQuery = ref('')
const searchResults = ref([])

const debouncedSearch = useDebounceFn(async (query: string) => {
  searchResults.value = await api.search(query)
}, 300)

watch(searchQuery, (newQuery) => {
  debouncedSearch(newQuery)
})
</script>
```

### Pattern: Virtual List (Conceptual)
```vue
<template>
  <DynamicScroller
    :items="allItems"
    :min-item-size="80"
    :buffer="200"
  >
    <template #default="{ item, index, active }">
      <DynamicScrollerItem
        :item="item"
        :active="active"
        :data-index="index"
      >
        <ItemCard :item="item" />
      </DynamicScrollerItem>
    </template>
  </DynamicScroller>
</template>
```

---

## Accessibility Patterns

### Pattern: Focus Management
```vue
<script setup lang="ts">
import { useFocusTrap } from '@vueuse/integrations/useFocusTrap'

const modalEl = ref<HTMLElement>()
const isOpen = ref(false)

const { activate, deactivate } = useFocusTrap(modalEl, {
  immediate: false,
  allowOutsideClick: true,
})

watch(isOpen, (open) => {
  if (open) activate()
  else deactivate()
})
</script>

<template>
  <div
    v-if="isOpen"
    ref="modalEl"
    role="dialog"
    aria-modal="true"
    aria-labelledby="modal-title"
  >
    <h2 id="modal-title">Modal Title</h2>
    <button @click="isOpen = false">Close</button>
  </div>
</template>
```

### Pattern: Keyboard Shortcuts
```vue
<script setup lang="ts">
const { Escape, Enter, ArrowUp, ArrowDown } = useMagicKeys()

whenever(Escape, () => {
  closeDialog()
})

whenever(Enter, () => {
  submitForm()
})

const selectedIndex = ref(0)
whenever(ArrowUp, () => {
  selectedIndex.value = Math.max(0, selectedIndex.value - 1)
})

whenever(ArrowDown, () => {
  selectedIndex.value = Math.min(items.length - 1, selectedIndex.value + 1)
})
</script>
```

### Pattern: Screen Reader Announcements
```vue
<script setup lang="ts">
const announcement = ref('')

function announce(message: string) {
  announcement.value = message
  setTimeout(() => {
    announcement.value = ''
  }, 1000)
}

// Usage
async function saveChanges() {
  await api.save()
  announce('Changes saved successfully')
}
</script>

<template>
  <div>
    <!-- Your content -->
    
    <!-- Screen reader announcement -->
    <div
      role="status"
      aria-live="polite"
      aria-atomic="true"
      class="sr-only"
    >
      {{ announcement }}
    </div>
  </div>
</template>

<style>
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border-width: 0;
}
</style>
```

---

## Error Handling Patterns

### Pattern: Try-Catch with State
```typescript
export function useAsyncAction() {
  const loading = ref(false)
  const error = ref<Error | null>(null)
  
  async function execute<T>(action: () => Promise<T>): Promise<T | null> {
    loading.value = true
    error.value = null
    
    try {
      const result = await action()
      return result
    } catch (e) {
      error.value = e as Error
      console.error('Action failed:', e)
      return null
    } finally {
      loading.value = false
    }
  }
  
  return {
    loading: readonly(loading),
    error: readonly(error),
    execute,
  }
}

// Usage
const { loading, error, execute } = useAsyncAction()

async function saveData() {
  const result = await execute(() => api.save(data))
  if (result) {
    // Success
  }
}
```

### Pattern: Error Boundary
```vue
<!-- ErrorBoundary.vue -->
<script setup lang="ts">
const error = ref<Error | null>(null)

onErrorCaptured((err) => {
  error.value = err
  return false // Prevent propagation
})

function retry() {
  error.value = null
}
</script>

<template>
  <div v-if="error">
    <h2>Something went wrong</h2>
    <p>{{ error.message }}</p>
    <button @click="retry">Try Again</button>
  </div>
  <slot v-else />
</template>

<!-- Usage -->
<ErrorBoundary>
  <MyComponent />
</ErrorBoundary>
```

---

## Testing Patterns

### Pattern: Composable Test
```typescript
import { describe, expect, it } from 'vitest'
import { useCounter } from './useCounter'

describe('useCounter', () => {
  it('initializes with default value', () => {
    const { count } = useCounter()
    expect(count.value).toBe(0)
  })
  
  it('initializes with custom value', () => {
    const { count } = useCounter(10)
    expect(count.value).toBe(10)
  })
  
  it('increments count', () => {
    const { count, increment } = useCounter()
    increment()
    expect(count.value).toBe(1)
  })
  
  it('decrements count', () => {
    const { count, decrement } = useCounter(5)
    decrement()
    expect(count.value).toBe(4)
  })
})
```

### Pattern: Component Test
```typescript
import { mount } from '@vue/test-utils'
import { describe, expect, it } from 'vitest'
import MyButton from './MyButton.vue'

describe('MyButton', () => {
  it('renders with label', () => {
    const wrapper = mount(MyButton, {
      props: { label: 'Click me' }
    })
    
    expect(wrapper.text()).toContain('Click me')
  })
  
  it('emits click event', async () => {
    const wrapper = mount(MyButton)
    await wrapper.trigger('click')
    
    expect(wrapper.emitted('click')).toHaveLength(1)
  })
  
  it('is disabled when prop is true', () => {
    const wrapper = mount(MyButton, {
      props: { disabled: true }
    })
    
    expect(wrapper.attributes('disabled')).toBeDefined()
  })
})
```

---

## Cheat Sheet

### Common VueUse Composables

```typescript
// Mouse & Touch
const { x, y } = useMouse()
const { pressed } = useMousePressed()
const { isOutside } = useMouseInElement(target)
onClickOutside(target, callback)

// Element
const { width, height } = useElementSize(target)
const { isVisible } = useElementVisibility(target)
const { stop } = useIntersectionObserver(target, callback)

// Browser
const online = useOnline()
const { copy } = useClipboard()
const { share } = useShare()
const { isFullscreen, toggle } = useFullscreen()

// Sensors
const { isSupported, coords } = useGeolocation()
const { state } = useNetwork()
const { isSupported, orientation } = useScreenOrientation()

// State
const counter = useCounter(0)
const toggle = useToggle(false)
const storage = useStorage('key', defaultValue)
const { state, undo, redo } = useRefHistory(target)

// Utilities
const debouncedValue = useDebounce(value, 300)
const throttledValue = useThrottle(value, 1000)
const { pause, resume } = useIntervalFn(callback, 1000)
const { start, stop } = useTimeoutFn(callback, 3000)
```

### Common Patterns Summary

| Pattern | When to Use | Example |
|---------|-------------|---------|
| Composable | Reusable logic | `useAuth()`, `useI18n()` |
| Store | Global state | User data, settings |
| Props/Emit | Parent-child | Component communication |
| Provide/Inject | Deep passing | Theme, i18n context |
| Computed | Derived state | Filtered lists, totals |
| Watch | Side effects | API calls, localStorage |
| Ref | Reactive primitive | `count`, `isOpen` |
| Reactive | Reactive object | `user`, `settings` |

---

*Use this as a quick reference while coding. Bookmark key patterns you use often!*
