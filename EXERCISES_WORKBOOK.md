# 📝 Elk Learning Exercises Workbook

> Hands-on exercises to practice patterns from the Elk codebase

---

## How to Use This Workbook

1. **Start with basics**: Don't skip exercises, they build on each other
2. **Code first**: Try solving before looking at solutions
3. **Compare**: After solving, compare with Elk's implementation
4. **Iterate**: Refactor your solution to match Elk's patterns
5. **Document**: Write comments explaining why the pattern works

---

## Week 1: Foundation Exercises

### Exercise 1.1: Build a Settings System

**Objective**: Create a type-safe settings system with persistence

**Requirements**:
```typescript
// 1. Define settings interface
interface AppSettings {
  theme: 'light' | 'dark' | 'auto'
  language: string
  notifications: {
    enabled: boolean
    sound: boolean
    desktop: boolean
  }
  display: {
    fontSize: number
    compactMode: boolean
  }
}

// 2. Create composable with:
// - Default values
// - LocalStorage persistence
// - Reactive updates
// - Export/import functionality

// 3. Build settings UI component
```

**Starter Code**:
```typescript
// composables/useSettings.ts
export function useSettings() {
  // Your implementation here
}
```

**Success Criteria**:
- [ ] Settings persist across page reloads
- [ ] Changes are reactive
- [ ] TypeScript types are correct
- [ ] Can export/import settings as JSON

**Study Reference**: `app/composables/settings/`

---

### Exercise 1.2: File-based Routing

**Objective**: Understand Nuxt's file-based routing

**Tasks**:
```
1. Create these pages:
   pages/
   ├── index.vue           (Home page)
   ├── about.vue           (About page)
   ├── posts/
   │   ├── index.vue       (Posts list)
   │   └── [id].vue        (Single post)
   └── users/
       └── [username]/
           ├── index.vue   (User profile)
           └── posts.vue   (User's posts)

2. Add navigation between pages
3. Implement dynamic routes
4. Add loading states
5. Handle 404 errors
```

**Success Criteria**:
- [ ] All routes work correctly
- [ ] Dynamic parameters are extracted
- [ ] Navigation is smooth
- [ ] 404 page shows for invalid routes

**Study Reference**: `app/pages/`

---

### Exercise 1.3: Create 5 Composables

**Objective**: Practice composable patterns

**Composables to Build**:

**1. useToggle**
```typescript
export function useToggle(initialValue = false) {
  // Create a boolean toggle with:
  // - state: Ref<boolean>
  // - toggle(): void
  // - setTrue(): void
  // - setFalse(): void
}

// Usage:
const { state: isOpen, toggle, setTrue, setFalse } = useToggle()
```

**2. useLocalStorage**
```typescript
export function useLocalStorage<T>(key: string, defaultValue: T) {
  // Create a reactive localStorage wrapper:
  // - Reads from localStorage on mount
  // - Writes to localStorage on change
  // - Returns reactive ref
}

// Usage:
const count = useLocalStorage('count', 0)
count.value++ // Automatically persists
```

**3. useDebounce**
```typescript
export function useDebounce<T>(value: Ref<T>, delay = 300) {
  // Create debounced version of a ref
  // Returns new ref that updates after delay
}

// Usage:
const search = ref('')
const debouncedSearch = useDebounce(search, 500)
watch(debouncedSearch, (value) => {
  // API call
})
```

**4. useAsync**
```typescript
export function useAsync<T>(asyncFn: () => Promise<T>) {
  // Execute async function with state tracking:
  // - data: Ref<T | null>
  // - loading: Ref<boolean>
  // - error: Ref<Error | null>
  // - execute(): Promise<void>
}

// Usage:
const { data, loading, error, execute } = useAsync(() => 
  fetch('/api/data').then(r => r.json())
)
```

**5. usePagination**
```typescript
export function usePagination<T>(
  fetchFn: (page: number) => Promise<T[]>,
  options?: { pageSize?: number }
) {
  // Implement pagination:
  // - items: Ref<T[]>
  // - currentPage: Ref<number>
  // - hasMore: Ref<boolean>
  // - nextPage(): Promise<void>
  // - prevPage(): Promise<void>
  // - reset(): void
}
```

**Success Criteria**:
- [ ] All composables work independently
- [ ] Properly typed with TypeScript
- [ ] Handle edge cases
- [ ] Can be composed together

---

## Week 2: State & i18n Exercises

### Exercise 2.1: Build a Todo App with Pinia

**Objective**: Master Pinia store patterns

**Requirements**:
```typescript
// stores/todos.ts
export const useTodosStore = defineStore('todos', () => {
  // State
  // - todos: Todo[]
  // - filter: 'all' | 'active' | 'completed'
  
  // Getters
  // - filteredTodos
  // - activeCount
  // - completedCount
  // - allCompleted
  
  // Actions
  // - addTodo(text: string)
  // - toggleTodo(id: number)
  // - removeTodo(id: number)
  // - clearCompleted()
  // - toggleAll()
  
  // Persistence
  // - Save to localStorage
  // - Load on init
})
```

**UI Requirements**:
```vue
<!-- pages/todos.vue -->
- Input to add todos
- List of todos with:
  - Checkbox to toggle
  - Delete button
  - Edit functionality
- Filter tabs (All, Active, Completed)
- Clear completed button
- Item counter
```

**Success Criteria**:
- [ ] Full CRUD operations
- [ ] Filtering works
- [ ] Persists to localStorage
- [ ] Type-safe
- [ ] Reactive updates

**Study Reference**: Search Elk for `defineStore` usage

---

### Exercise 2.2: Implement i18n System

**Objective**: Build multi-language support

**Tasks**:

**1. Setup i18n config**
```typescript
// config/i18n.ts
export const locales = [
  { code: 'en', name: 'English', file: 'en.json' },
  { code: 'es', name: 'Español', file: 'es.json' },
  { code: 'ar', name: 'العربية', file: 'ar.json', dir: 'rtl' },
]
```

**2. Create translation files**
```json
// locales/en.json
{
  "common": {
    "app_name": "My App",
    "welcome": "Welcome",
    "save": "Save",
    "cancel": "Cancel"
  },
  "nav": {
    "home": "Home",
    "about": "About",
    "contact": "Contact"
  },
  "user": {
    "profile": "Profile",
    "settings": "Settings",
    "logout": "Logout"
  },
  "messages": {
    "item_count": "You have {n} item | You have {n} items",
    "greeting": "Hello, {name}!"
  }
}
```

**3. Create custom formatters**
```typescript
// composables/useI18n.ts
export function useI18n() {
  // Custom number formatter
  function formatNumber(num: number, style: 'compact' | 'full') {
    // ...
  }
  
  // Custom date formatter  
  function formatDate(date: Date, format: 'short' | 'long') {
    // ...
  }
  
  // Currency formatter
  function formatCurrency(amount: number, currency = 'USD') {
    // ...
  }
  
  return { formatNumber, formatDate, formatCurrency }
}
```

**4. Build language switcher**
```vue
<!-- components/LanguageSwitcher.vue -->
<template>
  <!-- Dropdown to switch languages -->
  <!-- Support RTL switch -->
  <!-- Show current language -->
</template>
```

**Success Criteria**:
- [ ] Supports 3+ languages
- [ ] RTL works for Arabic
- [ ] Pluralization works
- [ ] Custom formatters work
- [ ] Language persists

**Study Reference**: `config/i18n.ts`, `app/composables/i18n.ts`

---

### Exercise 2.3: API Client with Caching

**Objective**: Build a smart API client

**Requirements**:
```typescript
export function useAPIClient() {
  // Features:
  // 1. Request/Response interceptors
  // 2. In-memory caching
  // 3. Loading state per endpoint
  // 4. Error handling
  // 5. Retry logic
  // 6. Request deduplication
  
  async function get<T>(url: string, options?: RequestOptions) {
    // Check cache first
    // Deduplicate concurrent requests
    // Handle errors with retry
  }
  
  async function post<T>(url: string, data: any) {
    // POST request
    // Invalidate relevant cache
  }
  
  return { get, post, put, delete: del }
}
```

**Test Cases**:
```typescript
// 1. Caching
const data1 = await client.get('/users/1')
const data2 = await client.get('/users/1') // Should use cache

// 2. Deduplication
Promise.all([
  client.get('/users/1'),
  client.get('/users/1'),
  client.get('/users/1'),
])
// Should only make 1 request

// 3. Error handling
try {
  await client.get('/not-found')
} catch (error) {
  // Should retry 3 times
}
```

**Success Criteria**:
- [ ] Caching works correctly
- [ ] No duplicate requests
- [ ] Automatic retries
- [ ] Type-safe responses
- [ ] Loading states

**Study Reference**: `app/composables/masto/`

---

## Week 3: Performance & Accessibility

### Exercise 3.1: Optimized Image Gallery

**Objective**: Build performant image gallery

**Requirements**:
```vue
<!-- components/ImageGallery.vue -->
<script setup lang="ts">
interface Image {
  id: string
  url: string
  thumbnail: string
  blurhash: string
  width: number
  height: number
}

// Features:
// - Virtual scrolling (only render visible)
// - Lazy loading images
// - Blurhash placeholders
// - Keyboard navigation
// - Infinite scroll
</script>

<template>
  <!-- Implement gallery -->
</template>
```

**Performance Goals**:
- [ ] Smooth 60fps scrolling
- [ ] < 100ms interaction response
- [ ] Lazy load images
- [ ] Virtual scrolling for 1000+ images

**Study Reference**: Components using `vue-virtual-scroller`

---

### Exercise 3.2: Accessible Form

**Objective**: Build fully accessible form

**Requirements**:
```vue
<!-- components/AccessibleForm.vue -->
<template>
  <form @submit.prevent="handleSubmit">
    <!-- Text input with validation -->
    <FormField
      v-model="form.name"
      label="Name"
      required
      :error="errors.name"
    />
    
    <!-- Email with validation -->
    <FormField
      v-model="form.email"
      type="email"
      label="Email"
      required
      :error="errors.email"
    />
    
    <!-- Select with keyboard navigation -->
    <FormSelect
      v-model="form.country"
      label="Country"
      :options="countries"
    />
    
    <!-- Checkbox group -->
    <FormCheckboxGroup
      v-model="form.interests"
      label="Interests"
      :options="interestOptions"
    />
    
    <!-- Submit with loading state -->
    <button
      type="submit"
      :disabled="isSubmitting"
      :aria-busy="isSubmitting"
    >
      {{ isSubmitting ? 'Submitting...' : 'Submit' }}
    </button>
  </form>
</template>
```

**Accessibility Checklist**:
- [ ] All inputs have labels
- [ ] Error messages are announced
- [ ] Keyboard navigation works
- [ ] Focus indicators visible
- [ ] ARIA attributes correct
- [ ] Color contrast 4.5:1+
- [ ] Screen reader friendly

**Study Reference**: `app/components/aria/`

---

### Exercise 3.3: Keyboard Shortcuts System

**Objective**: Implement app-wide keyboard shortcuts

**Requirements**:
```typescript
// composables/useKeyboardShortcuts.ts
export function useKeyboardShortcuts() {
  // Define shortcuts:
  // ? - Show help
  // / - Focus search
  // n - New post
  // g h - Go home
  // g p - Go profile
  // Esc - Close modal
  // j/k - Navigate list
  
  // Features:
  // - Global shortcuts
  // - Context-aware shortcuts
  // - Disable in inputs
  // - Help modal
}
```

**UI Requirements**:
```vue
<!-- components/ShortcutsHelp.vue -->
<template>
  <Modal v-model="isOpen">
    <h2>Keyboard Shortcuts</h2>
    <table>
      <tr>
        <td><kbd>?</kbd></td>
        <td>Show this help</td>
      </tr>
      <!-- List all shortcuts -->
    </table>
  </Modal>
</template>
```

**Success Criteria**:
- [ ] All shortcuts work
- [ ] Don't trigger in inputs
- [ ] Help modal complete
- [ ] Visual indicators
- [ ] Customizable

**Study Reference**: `app/composables/magickeys.ts`

---

## Week 4: Advanced Integration

### Exercise 4.1: PWA Implementation

**Objective**: Add PWA features to an app

**Tasks**:

**1. Basic PWA**
```typescript
// nuxt.config.ts
export default defineNuxtConfig({
  modules: ['@vite-pwa/nuxt'],
  pwa: {
    manifest: {
      name: 'My App',
      short_name: 'App',
      theme_color: '#5B7BA1',
      icons: [/* ... */],
    },
    workbox: {
      // Cache strategies
    },
  },
})
```

**2. Install Prompt**
```vue
<!-- components/InstallPrompt.vue -->
<script setup lang="ts">
const { showInstallPrompt, install } = usePWA()

// Show banner when installable
// Handle install click
// Hide after install
</script>
```

**3. Update Notification**
```vue
<!-- components/UpdateNotification.vue -->
<script setup lang="ts">
const { needRefresh, updateServiceWorker } = usePWA()

// Show when update available
// Reload to update
</script>
```

**4. Offline Page**
```vue
<!-- pages/offline.vue -->
<template>
  <div>
    <h1>You're offline</h1>
    <p>Check your connection</p>
  </div>
</template>
```

**5. Push Notifications**
```typescript
// composables/usePushNotifications.ts
export function usePushNotifications() {
  // Request permission
  // Subscribe to push
  // Handle notifications
  // Unsubscribe
}
```

**Success Criteria**:
- [ ] Installable on mobile/desktop
- [ ] Works offline
- [ ] Shows update prompts
- [ ] Push notifications work
- [ ] Caching strategies work

**Study Reference**: `config/pwa.ts`, `app/composables/push-notifications/`

---

### Exercise 4.2: Testing Suite

**Objective**: Write comprehensive tests

**Tests to Write**:

**1. Composable Tests**
```typescript
// tests/composables/useCounter.test.ts
describe('useCounter', () => {
  it('increments', () => {
    // Test increment
  })
  
  it('decrements', () => {
    // Test decrement
  })
  
  it('resets', () => {
    // Test reset
  })
})
```

**2. Component Tests**
```typescript
// tests/components/Button.test.ts
describe('Button', () => {
  it('renders label', () => {
    // Test rendering
  })
  
  it('emits click', () => {
    // Test event
  })
  
  it('is disabled', () => {
    // Test disabled state
  })
})
```

**3. Store Tests**
```typescript
// tests/stores/user.test.ts
describe('User Store', () => {
  it('logs in', async () => {
    // Test login
  })
  
  it('logs out', () => {
    // Test logout
  })
  
  it('persists', () => {
    // Test persistence
  })
})
```

**4. Integration Test**
```typescript
// tests/integration/auth-flow.test.ts
describe('Auth Flow', () => {
  it('full login flow', async () => {
    // 1. Visit login page
    // 2. Enter credentials
    // 3. Submit form
    // 4. Verify redirect
    // 5. Check user state
  })
})
```

**Coverage Goal**: >70%

**Study Reference**: Find `*.test.ts` files in Elk

---

### Exercise 4.3: Final Project - Mini Social App

**Objective**: Build complete application using all patterns

**Features**:

**1. Authentication**
- [ ] Login/Register pages
- [ ] JWT token handling
- [ ] Protected routes
- [ ] User profile

**2. Posts**
- [ ] Create post
- [ ] Edit post
- [ ] Delete post
- [ ] Like post
- [ ] Comment on post

**3. Timeline**
- [ ] Infinite scroll
- [ ] Pull to refresh
- [ ] Real-time updates
- [ ] Virtual scrolling

**4. User Profiles**
- [ ] View profile
- [ ] Edit profile
- [ ] Upload avatar
- [ ] Follow/Unfollow

**5. Notifications**
- [ ] In-app notifications
- [ ] Push notifications
- [ ] Mark as read
- [ ] Notification settings

**6. Search**
- [ ] Search posts
- [ ] Search users
- [ ] Search hashtags
- [ ] Filters

**7. Settings**
- [ ] Account settings
- [ ] Privacy settings
- [ ] Notification settings
- [ ] Theme customization
- [ ] Language selection

**8. i18n**
- [ ] 3+ languages
- [ ] RTL support
- [ ] Number formatting
- [ ] Date formatting

**9. PWA**
- [ ] Installable
- [ ] Offline support
- [ ] Push notifications
- [ ] Background sync

**10. Accessibility**
- [ ] Keyboard navigation
- [ ] Screen reader support
- [ ] ARIA attributes
- [ ] Focus management

**Technical Requirements**:
- [ ] TypeScript throughout
- [ ] Pinia for state
- [ ] Composables for logic
- [ ] Component library (20+)
- [ ] Unit tests (70%+)
- [ ] E2E tests (5+ flows)
- [ ] Performance optimized
- [ ] SEO friendly

**File Structure**:
```
my-social-app/
├── app/
│   ├── components/
│   │   ├── common/         (Button, Input, Modal, etc.)
│   │   ├── post/           (PostCard, PostForm, etc.)
│   │   ├── user/           (UserCard, UserProfile, etc.)
│   │   └── nav/            (Navbar, Sidebar, etc.)
│   ├── composables/
│   │   ├── useAuth.ts
│   │   ├── usePosts.ts
│   │   ├── useUsers.ts
│   │   ├── useNotifications.ts
│   │   └── useI18n.ts
│   ├── stores/
│   │   ├── auth.ts
│   │   ├── posts.ts
│   │   ├── users.ts
│   │   └── notifications.ts
│   ├── pages/
│   │   ├── index.vue
│   │   ├── login.vue
│   │   ├── register.vue
│   │   ├── profile/[id].vue
│   │   ├── post/[id].vue
│   │   └── settings/
│   ├── layouts/
│   │   ├── default.vue
│   │   └── auth.vue
│   ├── middleware/
│   │   └── auth.ts
│   └── utils/
│       ├── api.ts
│       ├── validation.ts
│       └── date.ts
├── config/
│   ├── i18n.ts
│   └── pwa.ts
├── locales/
│   ├── en.json
│   ├── es.json
│   └── ar.json
├── tests/
│   ├── unit/
│   ├── component/
│   └── e2e/
└── nuxt.config.ts
```

**Timeline**: 5-7 days

**Evaluation Criteria**:
- [ ] All features work
- [ ] Follows Elk patterns
- [ ] Clean code
- [ ] Well tested
- [ ] Accessible
- [ ] Performant
- [ ] i18n support
- [ ] PWA ready

---

## Bonus Challenges

### Challenge 1: Real-time Chat
Build a real-time chat using WebSockets with:
- Typing indicators
- Read receipts
- Emoji reactions
- File uploads
- Message search

### Challenge 2: Admin Dashboard
Create an admin dashboard with:
- Analytics charts
- User management
- Content moderation
- System settings
- Activity logs

### Challenge 3: Mobile App
Convert your app to mobile using Capacitor:
- Native functionality
- Camera access
- Biometric auth
- App store ready
- Deep linking

---

## Solutions & Code Reviews

After completing exercises:

1. **Self-Review Checklist**:
   - [ ] Does it follow Elk patterns?
   - [ ] Is it type-safe?
   - [ ] Is it performant?
   - [ ] Is it accessible?
   - [ ] Is it tested?
   - [ ] Is it documented?

2. **Compare with Elk**:
   - Find similar features in Elk
   - Compare implementations
   - Note differences
   - Learn from improvements

3. **Refactor**:
   - Apply learned patterns
   - Improve code quality
   - Add missing features

---

## Tips for Success

1. **Start Small**: Don't try to build everything at once
2. **Read First**: Study Elk's implementation before coding
3. **Type Everything**: Use TypeScript from the start
4. **Test Often**: Write tests as you code
5. **Refactor**: Iterate on your solutions
6. **Ask Questions**: Use Discord/GitHub discussions
7. **Stay Curious**: Dig deeper when something interests you
8. **Build Real Things**: Apply to your own projects

---

*Happy coding! 🚀*
