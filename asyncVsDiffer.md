
# async vs differ in script tag

## Where we use these in react applications ?
- In a React application, you typically don’t use `<script>` tags with `async` or `defer` manually because **React and modern bundlers (like Webpack, Vite, and Next.js) handle script loading efficiently**. However very rare scenerios we use based on usecase.
- Bundlers are by default using `defer` behaviour.
<hr />

## Without `async` & `defer`

```HTML
<script src="script.js"></script>
```

### Execution Flow 
```
[HTML Parsing] → 🛑 Pause → 📥 Download Script → 🚀 Execute → ▶ Resume HTML Parsing
```
### How It Works:

1. Browser starts **parsing** the HTML.
2. It encounters `<script>` → **pauses HTML parsing**.
3. Downloads the script.
4. Executes the script **immediately**.
5. Resumes parsing HTML after script execution.

### ✅ Best For:

- Small scripts.
- Scripts that must execute **before** rendering (e.g., critical inline JS).

### ❌ Downside:

- Blocks page rendering until the script is loaded.

<hr />

## With `async`
```HTML
<script src="script.js" async></script>
```

### Execution Flow
```
[HTML Parsing] → 📥[Downloading Script in Background] → 🛑 Pause →🚀Execute → ▶ Resume
```

### How It Works:

1. Browser starts parsing HTML.
2. Script starts **downloading in parallel** with HTML parsing.
3. **As soon as** the script is downloaded → **HTML parsing pauses**, script executes.
4. Once script execution is done, HTML parsing resumes.

### ✅ Best For:

- **Independent** scripts (e.g., analytics, ads, trackers).
- Scripts that don’t rely on DOM elements.

### ❌ Downside:

- **Execution order is NOT guaranteed** if multiple scripts have `async`.
- If script execution happens **before** DOM is ready, it may cause errors.

<hr />

## With `defer`
```HTML
<script src="script.js" defer></script>
```

### Execution Flow
```
[HTML Parsing] → 📥 [Downloading Script in Background] → ✅ Complete HTML Parsing → 🚀 Execute Script
```

### How It Works:

1. Browser starts parsing HTML.
2. Script starts **downloading in parallel** (like `async`).
3. **HTML parsing continues** (script does not block).
4. After **HTML parsing is fully complete**, **scripts execute in order**.

### ✅ Best For:

- Scripts that **modify the DOM** (e.g., UI components, dynamic content).
- Ensuring **scripts execute in order**.

### ❌ Downside:

- Cannot be used for scripts that must run **before** rendering.

<hr />

**Which One to Use?**
| Attribute | Loads in Parallel? | Blocks HTML? | Execution Timing | Best Use Case |
| --- | --- | --- | --- | --- |
| None | ❌ No | ✅ Yes | Immediately when downloaded | Small scripts, inline JS |
| `async` | ✅ Yes | ❌ No | As soon as it's downloaded | Analytics, ads, trackers |
| `defer` | ✅ Yes | ❌ No | After full HTML parsing | DOM manipulation, UI scripts |

<hr />

# Scenarios for Each Script Loading Type

### 🔹 **Without Any Attribute (`<script src="script.js"></script>`)**

1️⃣ A script that must **run before rendering** (e.g., setting up global variables).

- Example: A configuration script that initializes an app before rendering.2️⃣ A small inline script that modifies the page **immediately** after being encountered.
- Example: A script that changes the background color before the page loads.

---

### 🔹 **With `async` (`<script src="script.js" async></script>`)**

1️⃣ **Google Analytics** – You want tracking to start as soon as possible without blocking the page.

2️⃣ **Advertisement Scripts (Google Ads, Facebook Pixel, etc.)** – These need to load independently.

---

### 🔹 **With `defer` (`<script src="script.js" defer></script>`)**

1️⃣ **A JavaScript file handling the navbar or dynamic UI** – Ensures the DOM is fully loaded before running.

2️⃣ **A third-party library like a chatbot widget** that interacts with the page but should load after content.