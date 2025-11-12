# 🧩 Frontend Dolgozat Gyakorló Jegyzet

---

### 🌐 Címek
- **http://jsonserver.vm1.test/** → `jsonserver`  
- **http://frontend.vm1.test/** → `App.vue`

---

### 🧯 Ha valami felrobbant:

```bash
docker compose restart jsonserver
docker compose restart frontend

```

---

### 🧱 Terminálon bash indítása

```bash
bash start.sh
```

---

### ⚙️ `jsconfig.json`

```json
"@views/*": ["./frontend/src/views/*"],
"@stores/*": ["./frontend/src/stores/*"],
```

---

### ⚙️ `frontend/viteconfig.js`

```js
'@stores': fileURLToPath(new URL('./src/stores', import.meta.url)),
'@views': fileURLToPath(new URL('./src/views', import.meta.url)),
```

---

### 🧱 Telepítési parancsok

```bash
docker compose exec frontend fish
pnpm install          # ha nincs még pnpm
pnpm i vue-router pinia @formkit/vue axios
```

---

### 💾 `jsonserver/data/db.json`

Töltsd fel a saját adataiddal, pl.:

```json
{ "id": "2", "name": "another title", "views": 200 }
```

Majd:

```bash
docker compose restart jsonserver
```

---

### 🛠️ `frontend/src/utils/http.js`

```js
import axios from "axios";

export const http = axios.create({
  baseURL: "http://jsonserver.vm1.test",
});
```

---

### 🧩 `frontend/src/main.js`

```js
import { http } from "@utils/http";
window.http = http;

import { createPinia } from "pinia";
const pinia = createPinia();
app.use(pinia);
```

---

### 🏪 `frontend/src/stores/TémaStore.js`

```js
import { defineStore } from "pinia";
import { ref } from "vue";

export const useRecipeStore = defineStore('recipes', () => {
  const recipes = ref([]);
  const isLoading = ref(true);
  const error = ref(undefined);

  async function load() {
    const res = await http.get('recipes');
    recipes.value = res.data.data;
    isLoading.value = false;
  }

  load().catch((err) => {
    error.value = err.value;
  });

  async function createRecipe(data) {
    const res = await http.post('recipes', data);
    recipes.value.push(res.data.data);
  }

  async function deleteRecipe(id) {
    await http.delete(`recipes/${id}`);
    recipes.value = recipes.value.filter(r => r.id != id);
  }

  return {
    recipes,
    isLoading,
    error,
    createRecipe,
    deleteRecipe
  };
});
```

---

### 🧭 `frontend/src/router.js`

```js
import { createWebHistory, createRouter } from "vue-router";

const routes = [];

export const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/recipes', name: 'recipes', component: Recipes },
    { path: '/recipes/new', name: 'new-recipe', component: NewRecipe },
    { path: '/', name: 'home', redirect: '/recipes' }
  ]
});
```

---

## 🧪 Fontosabb konzol parancsok ellenőrzésre

```js
for (const data of [
  { name: "Túrógombóc" },
  { name: "Káposztasaláta" }
]) await http.post('recipes', data);

await http.delete('recipes/WFVVZBM');

const store = useRecipeStore();
store.recipes;
store.isLoading;

await recipeStore.createRecipe({
  name: "Rántott leves",
  ingredients: "Víz, liszt, olaj, paprika"
});

recipeStore.recipes;
```

---

## 👀 Nézetek (`frontend/src/views/recipes/`)

### `Recipes.vue` & `NewRecipe.vue` fájlok létrehozása

---

### 🧭 `frontend/src/main.js`

```js
import { router } from "./router";
app.use(router);
```

---

### 🧱 `App.vue`
Ezentúl mindegyik .vue-ba írj valamit hogy működjön! Elég egy template.
```vue
<script setup></script>

<template>
  <RouterView />
</template>
```

---

### 🧩 `frontend/src/Header.vue`

```vue
<template>
  <div class="flex bg-blue-200 py-2 px-4 gap-3">
    <RouterLink to="/recipes" class="hover:underline" active-class="underline">Receptek</RouterLink>
    <RouterLink to="/recipes/new" class="hover:underline" active-class="underline">Új recept</RouterLink>
  </div>
</template>
```

---

### 🧱 `frontend/src/App.vue`

```vue
<script setup>
import Header from './Header.vue'
</script>

<template>
  <div class="flex flex-col h-screen">
    <Header />
    <div class="bg-green-100 flex-grow-1">
      <RouterView />
    </div>
  </div>
</template>
```

---

### 🧰 `frontend/src/main.js` (FormKit integráció)

```js
import { defaultConfig, plugin } from "@formkit/vue";
app.use(plugin, defaultConfig);
```

---

### 🍽️ `frontend/src/views/recipes/NewRecipes.vue`

```vue
<script setup>
import { useRecipeStore } from '@stores/RecipeStore';
const recipeStore = useRecipeStore();

function save(data) {
  recipeStore.createRecipe(data);
}
</script>

<template>
  <FormKit type="form" :actions="false" @submit="save">
    <FormKit type="text" placeholder="Étel neve" input-class="bg-white"/>
    <FormKit type="submit" label="Mentés"/>
  </FormKit>
</template>
```

---

## ✅ Végleges fájlok

### `frontend/vite.config.js`

```js
import tailwind from '@tailwindcss/vite';
import vue from '@vitejs/plugin-vue';
import { fileURLToPath, URL } from 'node:url';
import { defineConfig } from 'vite';

export default defineConfig({
  plugins: [vue(), tailwind()],
  server: {
    host: true,
    allowedHosts: ['frontend.vm1.test']
  },
  resolve: {
    alias: {
      '@': fileURLToPath(new URL('./src', import.meta.url)),
      '@assets': fileURLToPath(new URL('./src/assets', import.meta.url)),
      '@stores': fileURLToPath(new URL('./src/stores', import.meta.url)),
      '@views': fileURLToPath(new URL('./src/views', import.meta.url)),
      '@utils': fileURLToPath(new URL('./src/utils', import.meta.url))
    }
  }
});
```

---

### `main/jsconfig.json`

```json
{
  "compilerOptions": {
    "paths": {
      "@assets/*": ["./frontend/src/assets/*"],
      "@utils/*": ["./frontend/src/utils/*"],
      "@views/*": ["./frontend/src/views/*"],
      "@stores/*": ["./frontend/src/stores/*"]
    }
  }
}
```

---

### `frontend/src/utils/http.js`

```js
import axios from "axios";

export const http = axios.create({
  baseURL: "http://jsonserver.vm1.test",
});
```

---

### `frontend/src/router.js`

```js
import NewRecipe from "@views/recipes/NewRecipes.vue";
import Recipes from "@views/recipes/Recipes.vue";
import { createWebHistory, createRouter } from "vue-router";

const routes = [];

export const router = createRouter({
  history: createWebHistory(),
  routes: [
    { path: '/recipes', name: 'recipes', component: Recipes },
    { path: '/recipes/new', name: 'new-recipe', component: NewRecipe },
    { path: '/', name: 'home', redirect: '/recipes' }
  ]
});
```

---

### `frontend/src/store/RecipeStore.js`

```js
import { http } from "@utils/http";
import { defineStore } from "pinia"
import { ref } from "vue";

export const useRecipeStore = defineStore('recipes', () => {
    const recipes = ref([])
    const isLoading = ref(true)
    const error = ref(undefined)

    async function load(){
        const res = await http.get('recipes')
        recipes.value = res.data.data
        isLoading.value = false
    }

    load().catch((err) => {
        error.value=err.value
    })

    async function createRecipe(data){
        const res = await http.post('recipes', data)
    	recipes.value.push(res.data.data)
    }

    async function deleteRecipe(id){
        await http.delete(`recipes/${id}`)
        //const index = recipes.value.findIndex(r => r.id === id)
        //if (index > -1){
        //    recipes.value.splice(index, 1)
        //}
        recipes.value  = recipes.value.filter(r => r.id != id)
    }

    return{
        recipes,
        isLoading,
        error,
        createRecipe,
        deleteRecipe
    }
})

```

---

### `frontend/src/main.js`

```js
import "@assets/style.css";
import { useRecipeStore } from "@stores/RecipeStore";
import { http } from "@utils/http";
import { createPinia } from "pinia";
import { createApp } from "vue";
import App from "./App.vue";
import { router } from "./router";
import { defaultConfig, plugin } from "@formkit/vue";

const app = createApp(App);
const pinia = createPinia();
app.use(pinia);
app.use(router);
app.use(plugin, defaultConfig);
app.mount('#app-root');

window.http = http;
window.useRecipeStore = useRecipeStore;
window.recipeStore = useRecipeStore();
```

---

### `frontend/src/App.vue`
```vue
<script setup>
import Header from './Header.vue'
</script>

<template>
  <div class="flex flex-col h-screen">
    <Header />
    <div class="bg-green-100 flex-grow-1">
      <RouterView />
    </div>
  </div>
</template>
```

---

### `frontend/src/Header.vue`
```vue
<template>
    <div class="flex bg-blue-200 py-2 px-4 gap-3">
        <RouterLink to="/recipes" class="hover:underline" active-class="underline">Receptek</RouterLink>
        <RouterLink to="/recipes/new" class="hover:underline" active-class="underline">Új recept</RouterLink>
    </div>
</template>
```

---

### `frontend/src/views/recipes/NewRecipes.vue`
```vue
<template>
    <div class="flex bg-blue-200 py-2 px-4 gap-3">
        <RouterLink to="/recipes" class="hover:underline" active-class="underline">Receptek</RouterLink>
        <RouterLink to="/recipes/new" class="hover:underline" active-class="underline">Új recept</RouterLink>
    </div>
</template>
```

---
### `frontend/src/views/recipes/Recipe.vue`
```vue
<script setup>

</script>

<template>
Recipes
</template>
```

---

### 🧁 `jsonserver/data/db.json`

```json
{
  "recipes": [
    {
      "id": "1",
      "name": "Palacsinta",
      "ingredients": "szódavíz, liszt, tojás",
      "preparation": "vegyünk",
      "preparationTime": 15
    },
    {
      "id": "2",
      "name": "another title",
      "views": 200
    },
    {
      "name": "Rántott leves",
      "ingredients": "Víz, liszt, olaj",
      "id": "yBTLBAO"
    },
    {
      "text_5": "kuglós",
      "id": "5kzuwry"
    }
  ]
}
```
