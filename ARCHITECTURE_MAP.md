# 🗺️ Elk Architecture Map

> A visual guide to understanding how Elk is structured

---

## 📁 Project Structure at a Glance

```
elk/
│
├── 📱 app/                         # Application code
│   ├── 🧩 components/              # Vue components (186 files)
│   │   ├── common/                 # Shared UI (Button, Modal, etc.)
│   │   ├── status/                 # Post/status components
│   │   ├── user/                   # User profile components
│   │   ├── publish/                # Compose/publish UI
│   │   ├── nav/                    # Navigation components
│   │   ├── settings/               # Settings UI
│   │   ├── content/                # Content rendering
│   │   ├── notification/           # Notification UI
│   │   ├── emoji/                  # Emoji picker
│   │   ├── tiptap/                 # Rich text editor
│   │   ├── aria/                   # Accessibility helpers
│   │   └── ...                     # More domains
│   │
│   ├── 🎣 composables/             # Reusable logic (~5,667 lines)
│   │   ├── settings/               # Settings persistence
│   │   ├── masto/                  # Mastodon API client
│   │   ├── tiptap/                 # Editor composables
│   │   ├── push-notifications/     # PWA notifications
│   │   ├── idb/                    # IndexedDB utils
│   │   ├── i18n.ts                 # i18n utilities
│   │   ├── users.ts                # User management
│   │   ├── timeline.ts             # Timeline logic
│   │   ├── notification.ts         # Notification logic
│   │   └── ...                     # 20+ more files
│   │
│   ├── 📄 pages/                   # File-based routing
│   │   ├── index.vue               # Home page
│   │   ├── [[server]]/             # Server-scoped routes
│   │   │   ├── @[account]/         # User profiles
│   │   │   ├── status/[status].vue # Single post
│   │   │   ├── explore/            # Explore pages
│   │   │   ├── list/               # Lists
│   │   │   └── public/             # Public timelines
│   │   ├── settings/               # Settings pages
│   │   ├── notifications/          # Notifications
│   │   ├── bookmarks.vue           # Bookmarks
│   │   └── ...                     # More routes
│   │
│   ├── 🎨 layouts/                 # Page layouts
│   │   └── default.vue             # Main layout
│   │
│   ├── 🚦 middleware/              # Route guards
│   │
│   ├── 🔌 plugins/                 # Nuxt plugins
│   │
│   ├── 💎 utils/                   # Pure functions
│   │   ├── elk-idb.ts              # IndexedDB
│   │   ├── i18n.ts                 # i18n helpers
│   │   └── language.ts             # Language utils
│   │
│   ├── 🎯 constants/               # Static values
│   │
│   └── 💅 styles/                  # Global styles
│       ├── vars.css                # CSS variables
│       ├── global.css              # Global styles
│       └── ...                     # Theme, tiptap, etc.
│
├── ⚙️ config/                      # Configuration
│   ├── i18n.ts                     # Locale config
│   ├── i18n.config.ts              # i18n runtime
│   ├── pwa.ts                      # PWA config
│   └── ...
│
├── 🌍 locales/                     # Translation files
│   ├── en.json                     # English
│   ├── es.json                     # Spanish
│   ├── ar.json                     # Arabic
│   └── ...                         # 30+ languages
│
├── 🎭 mocks/                       # Mock data/services
│
├── 📦 public/                      # Static assets
│
├── 🔧 nuxt.config.ts               # Nuxt configuration
├── 📦 package.json                 # Dependencies
├── 📝 tsconfig.json                # TypeScript config
└── 📖 README.md                    # Documentation
```

---

## 🏗️ Architecture Layers

```
┌─────────────────────────────────────────────────┐
│                   USER                          │
│             (Browser Interface)                 │
└─────────────────────────────────────────────────┘
                      ↕
┌─────────────────────────────────────────────────┐
│              📄 PAGES LAYER                     │
│  (Routing, Layouts, Page Components)            │
│                                                  │
│  pages/index.vue                                 │
│  pages/settings/                                 │
│  pages/[[server]]/@[account]/                   │
└─────────────────────────────────────────────────┘
                      ↕
┌─────────────────────────────────────────────────┐
│           🧩 COMPONENT LAYER                    │
│  (Reusable UI Components)                       │
│                                                  │
│  StatusCard, UserProfile, NavBar                │
│  ComposeBox, NotificationList, etc.             │
└─────────────────────────────────────────────────┘
                      ↕
┌─────────────────────────────────────────────────┐
│           🎣 COMPOSABLES LAYER                  │
│  (Business Logic, State Management)             │
│                                                  │
│  useAuth(), useTimeline(), useI18n()            │
│  useSettings(), useNotifications()              │
└─────────────────────────────────────────────────┘
                      ↕
┌─────────────────────────────────────────────────┐
│             🏪 STORE LAYER                      │
│  (Global State with Pinia)                      │
│                                                  │
│  User Store, Settings Store, etc.               │
└─────────────────────────────────────────────────┘
                      ↕
┌─────────────────────────────────────────────────┐
│             💎 UTILS LAYER                      │
│  (Pure Functions, Helpers)                      │
│                                                  │
│  Date formatting, Validation, etc.              │
└─────────────────────────────────────────────────┘
                      ↕
┌─────────────────────────────────────────────────┐
│             🌐 API LAYER                        │
│  (Mastodon API via masto.js)                    │
│                                                  │
│  HTTP Requests, WebSockets                      │
└─────────────────────────────────────────────────┘
                      ↕
┌─────────────────────────────────────────────────┐
│           💾 STORAGE LAYER                      │
│  (Persistence)                                   │
│                                                  │
│  LocalStorage, IndexedDB, Cookies               │
└─────────────────────────────────────────────────┘
```

---

## 🔄 Data Flow Patterns

### Pattern 1: User Action → API → State → UI

```
┌──────────────┐
│ User clicks  │
│ "Post" btn   │
└──────┬───────┘
       │
       ↓
┌──────────────────────┐
│ Component            │
│ publishPost()        │
└──────┬───────────────┘
       │
       ↓
┌──────────────────────┐
│ Composable           │
│ usePublish()         │
│ - validation         │
│ - formatting         │
└──────┬───────────────┘
       │
       ↓
┌──────────────────────┐
│ API Client           │
│ masto.createStatus() │
└──────┬───────────────┘
       │
       ↓
┌──────────────────────┐
│ Mastodon Server      │
└──────┬───────────────┘
       │
       ↓
┌──────────────────────┐
│ Response             │
└──────┬───────────────┘
       │
       ↓
┌──────────────────────┐
│ Store Update         │
│ Add to timeline      │
└──────┬───────────────┘
       │
       ↓
┌──────────────────────┐
│ UI Update            │
│ Show new post        │
└──────────────────────┘
```

### Pattern 2: Settings Flow

```
┌──────────────┐
│ User changes │
│ setting      │
└──────┬───────┘
       │
       ↓
┌──────────────────────┐
│ Component            │
│ v-model="setting"    │
└──────┬───────────────┘
       │
       ↓
┌──────────────────────┐
│ Composable           │
│ useSettings()        │
│ - reactive ref       │
└──────┬───────────────┘
       │
       ├────────────────────┐
       │                    │
       ↓                    ↓
┌─────────────┐     ┌──────────────┐
│ LocalStorage│     │ CSS Variable │
│ Persist     │     │ Apply theme  │
└─────────────┘     └──────────────┘
```

### Pattern 3: i18n Flow

```
┌──────────────┐
│ User selects │
│ language     │
└──────┬───────┘
       │
       ↓
┌──────────────────────┐
│ Language Switcher    │
└──────┬───────────────┘
       │
       ↓
┌──────────────────────┐
│ i18n Composable      │
│ setLocale()          │
└──────┬───────────────┘
       │
       ├────────────────────────┐
       │                        │
       ↓                        ↓
┌─────────────┐     ┌──────────────────┐
│ Load locale │     │ Update formatters│
│ JSON file   │     │ (date, number)   │
└──────┬──────┘     └──────────────────┘
       │
       ↓
┌──────────────────────┐
│ Re-render all        │
│ translated text      │
└──────────────────────┘
```

---

## 🎯 Key Pattern: Composable Architecture

### The Composable Flow

```
┌─────────────────────────────────────────────┐
│          Component (UI)                     │
│                                             │
│  <script setup>                             │
│    // Import composable                     │
│    const { data, loading } = useTimeline() │
│  </script>                                  │
│                                             │
│  <template>                                 │
│    <div v-if="loading">Loading...</div>    │
│    <Post v-for="post in data" />           │
│  </template>                                │
└─────────────────┬───────────────────────────┘
                  │ Uses
                  ↓
┌─────────────────────────────────────────────┐
│      Composable (Business Logic)            │
│                                             │
│  export function useTimeline() {            │
│    // State                                 │
│    const data = ref([])                     │
│    const loading = ref(false)               │
│                                             │
│    // Use other composables                 │
│    const client = useMastoClient()          │
│    const { t } = useI18n()                  │
│                                             │
│    // Logic                                 │
│    async function fetch() { ... }           │
│                                             │
│    return { data, loading, fetch }          │
│  }                                          │
└─────────────────┬───────────────────────────┘
                  │ Uses
                  ↓
┌─────────────────────────────────────────────┐
│        Store (Global State)                 │
│                                             │
│  export const useTimelineStore =            │
│    defineStore('timeline', () => {          │
│      const posts = ref([])                  │
│      return { posts }                       │
│    })                                       │
└─────────────────┬───────────────────────────┘
                  │ Uses
                  ↓
┌─────────────────────────────────────────────┐
│         API Client                          │
│                                             │
│  const client = useMastoClient()            │
│  await client.v1.timelines.home.list()      │
└─────────────────────────────────────────────┘
```

---

## 🧩 Component Organization Pattern

```
components/
│
├── domain/                    # Organized by domain
│   ├── ComponentName.vue      # Main component
│   ├── ComponentHeader.vue    # Sub-component
│   ├── ComponentBody.vue      # Sub-component
│   └── ComponentFooter.vue    # Sub-component
│
Example: Status domain
│
├── status/
│   ├── StatusCard.vue         # Main status card
│   ├── StatusHeader.vue       # Author info
│   ├── StatusBody.vue         # Content
│   ├── StatusActions.vue      # Like, share, etc.
│   ├── StatusReply.vue        # Reply UI
│   └── StatusAttachments.vue  # Media

Naming Convention:
- PascalCase
- Prefix with domain
- Descriptive names
- StatusCard not Card
```

---

## 🎨 Styling Architecture

```
┌─────────────────────────────────────────────┐
│           UnoCSS (Atomic CSS)               │
│                                             │
│  <div class="p-4 bg-white dark:bg-dark">   │
│  Utilities → Generated on-demand            │
└─────────────────────────────────────────────┘
                  +
┌─────────────────────────────────────────────┐
│         CSS Variables (Theming)             │
│                                             │
│  :root {                                    │
│    --c-primary: #5B7BA1;                    │
│    --c-text: #000;                          │
│  }                                          │
│                                             │
│  .dark {                                    │
│    --c-text: #fff;                          │
│  }                                          │
└─────────────────────────────────────────────┘
                  +
┌─────────────────────────────────────────────┐
│       Scoped Component Styles               │
│                                             │
│  <style scoped>                             │
│  .custom-class { ... }                      │
│  </style>                                   │
└─────────────────────────────────────────────┘
```

---

## 🌍 i18n Architecture

```
┌─────────────────────────────────────────────┐
│         Locale Configuration                │
│         (config/i18n.ts)                    │
│                                             │
│  - Define locales                           │
│  - Plural rules                             │
│  - Date/number formats                      │
└─────────────────┬───────────────────────────┘
                  │
                  ↓
┌─────────────────────────────────────────────┐
│      Translation Files                      │
│      (locales/*.json)                       │
│                                             │
│  {                                          │
│    "common": { "save": "Save" },            │
│    "user": { "profile": "Profile" }         │
│  }                                          │
└─────────────────┬───────────────────────────┘
                  │
                  ↓
┌─────────────────────────────────────────────┐
│      Custom Utilities                       │
│      (composables/i18n.ts)                  │
│                                             │
│  - useHumanReadableNumber()                 │
│  - useFormattedDateTime()                   │
│  - useFileSizeFormatter()                   │
└─────────────────┬───────────────────────────┘
                  │
                  ↓
┌─────────────────────────────────────────────┐
│         Components Use                      │
│                                             │
│  const { t, locale } = useI18n()            │
│  {{ t('common.save') }}                     │
└─────────────────────────────────────────────┘
```

---

## 🔐 State Management Flow

```
┌─────────────────────────────────────────────┐
│            Component                        │
└─────────────────┬───────────────────────────┘
                  │
                  ↓
┌─────────────────────────────────────────────┐
│        Composable (Facade)                  │
│                                             │
│  export function useAuth() {                │
│    const store = useUserStore()             │
│    // Add extra logic on top of store       │
│    return { ...store, extraMethod }         │
│  }                                          │
└─────────────────┬───────────────────────────┘
                  │
                  ↓
┌─────────────────────────────────────────────┐
│         Pinia Store                         │
│                                             │
│  export const useUserStore =                │
│    defineStore('user', () => {              │
│      // State                               │
│      const user = ref(null)                 │
│                                             │
│      // Getters                             │
│      const isLoggedIn = computed(...)       │
│                                             │
│      // Actions                             │
│      async function login() { ... }         │
│                                             │
│      return { user, isLoggedIn, login }     │
│    })                                       │
└─────────────────┬───────────────────────────┘
                  │
                  ↓
┌─────────────────────────────────────────────┐
│         Persistence                         │
│                                             │
│  watch(user, () => {                        │
│    localStorage.setItem(...)                │
│  })                                         │
└─────────────────────────────────────────────┘
```

---

## 🚀 Performance Optimization Points

```
┌─────────────────────────────────────────────┐
│           Route Level                       │
│                                             │
│  - Code splitting per route                 │
│  - Lazy load pages                          │
│  - Preload critical routes                  │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│         Component Level                     │
│                                             │
│  - defineAsyncComponent()                   │
│  - <Suspense> boundaries                    │
│  - v-once for static content                │
│  - v-memo for expensive lists               │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│          Data Level                         │
│                                             │
│  - Virtual scrolling (1000+ items)          │
│  - Computed caching                         │
│  - Debounced input                          │
│  - Request deduplication                    │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│          Asset Level                        │
│                                             │
│  - Lazy load images                         │
│  - Blurhash placeholders                    │
│  - WebP format                              │
│  - CDN for static assets                    │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│         Network Level                       │
│                                             │
│  - Service worker caching                   │
│  - HTTP/2 multiplexing                      │
│  - API response caching                     │
│  - Preconnect to API                        │
└─────────────────────────────────────────────┘
```

---

## ♿ Accessibility Architecture

```
┌─────────────────────────────────────────────┐
│         Keyboard Navigation                 │
│                                             │
│  - Tab order logical                        │
│  - Shortcuts (/, ?, n, etc.)               │
│  - Focus trap in modals                     │
│  - Escape to close                          │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│          ARIA Attributes                    │
│                                             │
│  - role="dialog"                            │
│  - aria-label                               │
│  - aria-describedby                         │
│  - aria-live regions                        │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│        Screen Reader Support                │
│                                             │
│  - Semantic HTML                            │
│  - Skip links                               │
│  - Announcement regions                     │
│  - Alt text for images                      │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│          Visual Accessibility               │
│                                             │
│  - 4.5:1 color contrast                     │
│  - Focus indicators                         │
│  - Reduced motion support                   │
│  - Zoom support                             │
└─────────────────────────────────────────────┘
```

---

## 📱 PWA Architecture

```
┌─────────────────────────────────────────────┐
│          Manifest                           │
│          (manifest.json)                    │
│                                             │
│  - App name, icons                          │
│  - Theme colors                             │
│  - Display mode                             │
└─────────────────┬───────────────────────────┘
                  │
                  ↓
┌─────────────────────────────────────────────┐
│       Service Worker                        │
│       (workbox)                             │
│                                             │
│  - Cache strategies                         │
│  - Offline support                          │
│  - Background sync                          │
│  - Push notifications                       │
└─────────────────┬───────────────────────────┘
                  │
                  ↓
┌─────────────────────────────────────────────┐
│      PWA Features                           │
│                                             │
│  - Install prompt                           │
│  - Update notifications                     │
│  - Offline page                             │
│  - App shortcuts                            │
└─────────────────────────────────────────────┘
```

---

## 🧪 Testing Strategy

```
┌─────────────────────────────────────────────┐
│         Unit Tests                          │
│         (Vitest)                            │
│                                             │
│  - Test composables                         │
│  - Test utils                               │
│  - Test stores                              │
│  - Fast, isolated                           │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│       Component Tests                       │
│       (@vue/test-utils)                     │
│                                             │
│  - Test components                          │
│  - Test props/events                        │
│  - Test user interaction                    │
│  - Mount in isolation                       │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│      Integration Tests                      │
│                                             │
│  - Test full flows                          │
│  - Test multiple components                 │
│  - Test real interactions                   │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│         Type Checking                       │
│         (TypeScript)                        │
│                                             │
│  - Compile-time safety                      │
│  - Interface validation                     │
│  - IDE autocomplete                         │
└─────────────────────────────────────────────┘
```

---

## 🎯 Learning Path Through Architecture

### Week 1: Understand the Structure
```
Focus: How files are organized
Study: Directory structure, naming conventions
Build: Navigate confidently
```

### Week 2: Follow the Data
```
Focus: How data flows
Study: Component → Composable → Store → API
Build: Data-driven features
```

### Week 3: Master the Patterns
```
Focus: Why patterns exist
Study: Performance, accessibility, DX
Build: Production-quality code
```

### Week 4: Integration
```
Focus: How everything connects
Study: PWA, testing, deployment
Build: Complete applications
```

---

## 📊 Key Metrics

```
Codebase Stats:
- Components: 186
- Composables: ~30
- Pages: 54
- Languages: 30+
- Lines of Code: ~50,000+

Performance Goals:
- FCP: < 1.8s
- LCP: < 2.5s
- TTI: < 3.8s
- FID: < 100ms
- CLS: < 0.1

Accessibility:
- WCAG AA compliance
- Keyboard navigable
- Screen reader friendly
- Color contrast 4.5:1+
```

---

## 🗺️ Navigation Tips

### Finding Your Way
```
Looking for UI?          → components/
Looking for logic?       → composables/
Looking for routes?      → pages/
Looking for config?      → config/
Looking for translations?→ locales/
Looking for styles?      → app/styles/
Looking for types?       → Look for .ts files
```

### Common File Patterns
```
Settings:
  - composables/settings/definition.ts  (types)
  - composables/settings/storage.ts     (logic)
  - pages/settings/                     (UI)

i18n:
  - config/i18n.ts                      (config)
  - composables/i18n.ts                 (utils)
  - locales/*.json                      (translations)

Features:
  - composables/[feature].ts            (logic)
  - components/[feature]/               (UI)
  - pages/[feature].vue                 (route)
```

---

## 🎓 Use This Map To:

1. **Understand Structure**: See how Elk is organized
2. **Find Patterns**: Know where to look for examples
3. **Navigate Codebase**: Quickly locate files
4. **Plan Learning**: Know what to study when
5. **Build Features**: Follow established patterns
6. **Debug Issues**: Trace data flow
7. **Contribute**: Understand conventions

---

**Next Step**: Open `START_HERE.md` to begin your learning journey!

---

*Keep this map handy as a quick reference while exploring Elk!* 🗺️✨
