# Visual Design Standard — Prompt Hub Pattern

> **Default visual pattern for all new AI Workspace pages in Sentinel Frontend.**
> When creating new views/pages, follow this standard unless the user specifies otherwise.

---

## Page Layout

```
.ph-page { display: flex; flex-direction: column; gap: 1.25rem; }
```

- Full-height pages: `height: calc(100vh - 138px); overflow: hidden;`
- Content area scrolls internally, page shell does not scroll

---

## Header Pattern

```html
<div class="ph-header">
  <div class="ph-title-block">
    <div class="ph-title">
      <svg width="18" height="18" ...icon... />
      Page Title
      <span class="ph-badge">AI Workspace</span>
    </div>
    <div class="ph-subtitle">Short description of the page</div>
  </div>
  <div class="ph-header-stats">
    <!-- KPI cards -->
  </div>
</div>
```

- Title: `font-size: 1rem; font-weight: 600;`
- Badge: `padding: 0.15rem 0.65rem; border-radius: 12px; font-size: 0.68rem; font-weight: 700; background: rgba(30,64,175,.12); color: var(--ph-accent);`
- Subtitle: `font-size: 0.75rem; color: var(--ph-text-muted);`

---

## KPI Cards

```html
<div class="ph-kpi-card ph-kpi--NAME">
  <div class="ph-kpi-icon"><svg .../></div>
  <div class="ph-kpi-body">
    <div class="ph-kpi-label">Label</div>
    <div class="ph-kpi-value">Value</div>
    <div class="ph-kpi-sub">subtitle</div>
  </div>
</div>
```

- Card: `display: flex; align-items: center; gap: 0.75rem; padding: 0.65rem 1rem; border-radius: 10px; background: var(--bg-card); border: 1px solid var(--border-color);`
- Icon: `width: 36px; height: 36px; border-radius: 8px; display: flex; align-items: center; justify-content: center;`
- Label: `font-size: 0.62rem; font-weight: 700; text-transform: uppercase; letter-spacing: 0.05em;`
- Value: `font-size: 1.4rem; font-weight: 700; color: var(--text-primary);`
- Sub: `font-size: 0.65rem; color: var(--ph-text-muted);`

---

## Filter Bar

### Search Row
```html
<div class="ph-search-row">
  <div class="ph-search-box">
    <svg class="ph-search-icon" ... />
    <input v-model="search" placeholder="Search..." class="ph-search-input" />
    <button v-if="search" class="ph-clear-x" @click="search=''">✕</button>
  </div>
  <button class="ph-filter-toggle" @click="filtersOpen = !filtersOpen">
    Filters
    <span v-if="activeCount > 0" class="ph-filter-badge">{{ activeCount }}</span>
  </button>
  <button class="ph-clear-btn" @click="clearAll">Clear All</button>
</div>
```

### Collapsible Filter Panel
```html
<div v-show="filtersOpen" class="ph-controls">
  <div class="ph-filter-row">
    <span class="ph-filter-label">Categories</span>
    <div class="ph-pills">
      <button :class="['ph-pill', { active }]" :style="getPillStyle(item, active)">
        {{ name }} <span class="ph-pill-count">{{ count }}</span>
      </button>
    </div>
  </div>
</div>
```

### Pill Style (active):
```js
{
  backgroundColor: `${color}18`,
  color: '#ffffff',
  borderColor: color,
  borderLeftColor: color,
  borderLeftWidth: '3px',
  boxShadow: `0 0 0 1px ${color}, 0 0 10px ${color}70, 0 0 18px ${color}35`,
}
```

### Pill Style (inactive):
```js
{
  backgroundColor: 'transparent',
  color: 'var(--text-primary)',
  borderColor: `${color}50`,
  borderLeftColor: color,
  borderLeftWidth: '3px',
}
```

---

## Card Grid

```css
.ph-cards-grid {
  display: grid;
  gap: 1rem;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
}
```

## Card Pattern

```html
<div class="ph-card" :style="{ borderLeftColor: categoryColor }">
  <div class="ph-card-header">
    <span class="ph-card-title">Title</span>
    <span class="ph-card-cat" :style="{ background: categoryColor }">Category</span>
  </div>
  <p class="ph-card-desc">Description</p>
  <div class="ph-card-tags">
    <span class="ph-card-tag" :style="{ background, color }">tag</span>
  </div>
  <div class="ph-card-footer">
    footer items (date, rating, etc.)
  </div>
</div>
```

```css
.ph-card {
  background: var(--bg-card);
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(30,64,175,.06);
  border: 1px solid var(--border-color);
  border-left: 4px solid var(--ph-accent);
  padding: 1rem;
  display: flex;
  flex-direction: column;
  gap: 0.4rem;
  cursor: pointer;
  transition: transform .15s, box-shadow .15s;
}
.ph-card:hover { transform: translateY(-2px); box-shadow: 0 6px 20px rgba(30,64,175,.14); }
```

---

## Color System

### CSS Variables (theme-aware):
- `var(--bg-main)` — page background
- `var(--bg-card)` — card background
- `var(--bg-hover)` — hover background
- `var(--border-color)` — borders
- `var(--text-primary)` — main text
- `var(--text-secondary)` — secondary text
- `var(--text-muted)` — muted/dim text
- `var(--accent)` — accent color

### NEVER hardcode dark-theme colors like `#0f172a`, `#1e293b`, etc.
### ALWAYS use CSS variables for theme compatibility.

### Category Color Pattern:
Each category has a designated color used for:
- Card left border
- Category pill background
- Filter pill accent

---

## NEW Badge (for recent items < 7 business days):
```css
.new-badge {
  background: #10b981;
  color: white;
  padding: 0.1rem 0.45rem;
  border-radius: 6px;
  font-size: 0.58rem;
  font-weight: 800;
  text-transform: uppercase;
  letter-spacing: 0.06em;
  animation: pulse-new 2s infinite;
}
@keyframes pulse-new {
  0%, 100% { opacity: 1; }
  50% { opacity: 0.7; }
}
```

---

## IT User Edit Permissions
```js
const isITUser = computed(() => {
  const dept = (auth.department || '').toLowerCase()
  return dept === 'ti' || dept === 'it' || dept.includes('tecnologia')
})
```
Show add/edit/delete controls only when `isITUser` is true.

---

## Responsive Breakpoints
- Desktop: 3+ columns
- Tablet (< 900px): 2 columns
- Mobile (< 600px): 1 column

---

## Modal Pattern
- Teleport to body
- Overlay: `position: fixed; inset: 0; background: rgba(0,0,0,0.5); z-index: 9999;`
- Content: `max-width: 600px; margin: auto; background: var(--bg-card); border-radius: 12px; padding: 24px;`
- Close button: absolute top-right
- Fade transition
