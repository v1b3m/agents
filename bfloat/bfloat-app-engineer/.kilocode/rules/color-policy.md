# color-scheme.md

Color policy for bfloat-app-engineer.

## Guidelines

### Color policy (Tailwind + HSL, light/dark, nested)

**Never hardcode colors in components.**
All colors flow: `app/tailwind.css` (HSL vars) → `tailwind.config.ts` (map) → Tailwind classes.

**Source of truth**

- Vars live in `@layer base { :root { … } .dark { … } }` (light = `:root`, dark = `.dark`).
- `tailwind.config.ts` maps vars using **HSL**: `hsl(var(--token) / <alpha-value>)`.
- Nested colors (e.g., `card.DEFAULT`, `card.foreground`, `card.border`) map to distinct CSS vars.

**Workflow (strict)**

1. **Check first**: if a suitable token exists, use it.
2. If missing, **add HSL vars** to **both** `:root` and `.dark`.
3. **Expose** in `tailwind.config.ts` (support nesting).
4. **Use** semantic Tailwind classes only (no raw colors).

**Forbidden in components**

- ❌ `class="text-[#111]"`, `class="text-slate-800"`, `style={{ color: 'hsl(…)' }}`.

---

### Examples (with nesting)

#### `app/tailwind.css`

```css
@layer base {
  :root {
    --background: 240 24% 96%;
    --foreground: 240 10% 3.9%;
    --card: 0 0% 100%;
    --card-foreground: 240 6% 4%;
    --border-card: 240 6% 90%;
  }
  .dark {
    --background: 240 10% 3.9%;
    --foreground: 240 24% 96%;
    --card: 240 6% 10%;
    --card-foreground: 240 6% 96%;
    --border-card: 240 5% 26%;
  }
}
```

#### `tailwind.config.ts` (nested colors)

```ts
extend: {
  colors: {
    background: "hsl(var(--background))",
    foreground: "hsl(var(--foreground))",
    card: {
      DEFAULT: "hsl(var(--card))",
      foreground: "hsl(var(--card-foreground))",
      border: "hsl(var(--border-card) / <alpha-value>)",
    },
  },
}
```

### Component usage

```tsx
// GOOD
<Card className="bg-card text-card-foreground border-card/50" />

// BAD
<Card className="bg-white text-[#0b0b0c] border-slate-200" />
```

---

### Adding a new **nested** token

**Goal:** add `input` with `DEFAULT`, `foreground`, `ring`.

1. `app/tailwind.css` — add to both themes:

```css
:root {
  --input: 0 0% 100%;
  --input-foreground: 240 6% 6%;
  --ring-input: 240 6% 70%;
}
.dark {
  --input: 240 8% 14%;
  --input-foreground: 240 6% 96%;
  --ring-input: 240 6% 40%;
}
```

2. `tailwind.config.ts` — expose nested mapping:

```ts
colors: {
  input: {
    DEFAULT: "hsl(var(--input))",
    foreground: "hsl(var(--input-foreground))",
    ring: "hsl(var(--ring-input) / <alpha-value>)",
  },
}
```

3. Use in components:

```tsx
<input className="bg-input text-input-foreground ring-1 ring-input/60 focus:ring-input" />
```

---

### Naming rules (nesting)

- CSS vars use **kebab-case** with a clear base:
  `--card`, `--card-foreground`, `--border-card`, `--ring-input`.
- Tailwind keys use **semantic** object paths:
  `card.DEFAULT`, `card.foreground`, `card.border`, `input.ring`.
- Prefer **semantic** tokens (surface, content, border, ring, accent, muted, success, warning, danger).

### Review checklist (the model MUST verify)

- Token exists in **both** `:root` and `.dark` (HSL values).
- Token is correctly mapped (with `/ <alpha-value>` where transparency is needed).
- Components use only mapped classes (`bg-…`, `text-…`, `border-…`, `ring-…`).
- No raw colors or un-mapped Tailwind palette classes.
- Changes are limited to: CSS vars → config mapping → component classes.
