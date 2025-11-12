http://jsonserver.vm1.test/   --> jsonserver

http://frontend.vm1.test/   --> App.vue


ha valami felrobbant:

***docker compose restart jsonserver***

***docker compose restart frontend***







***bash start.sh***



###### **jsconfig.json**

"@views/\*": \["./frontend/src/views/\*"],

"@stores/\*": \["./frontend/src/stores/\*"],



###### **frontend/viteconfig.js**

'@stores': fileURLToPath(new URL('./src/stores', import.meta.url)),

'@views': fileURLToPath(new URL('./src/views', import.meta.url)),



***docker compose exec frontend fish***

***pnpm install(ha nincs még pnpm)***

***pnpm i vue-router pinia @formkit/vue axios***



###### **jsonserver/data/db.json**

tölsd fel a saját adataiddal, pl { "id": "2", "name": "another title", "views": 200 }



***docker compose restart jsonserver***



csináld meg az utils mappát és a http.js fájlt --> frontend/src/utils/http.js
**frontend/src/utils/http.js**
---

import axios from "axios";



export const http = axios.create({

&nbsp;   baseURL: "http://jsonserver.vm1.test",

})



###### **frontend/src/main.js**

import { http } from "@utils/http"

window.http = http;



import { createPinia } from "pinia"

const pinia = createPinia()

app.use(pinia)





csináld meg a stores mappát és a TémaStore.js-t --> frontend/src/stores/TémaStore.js
**frontend/src/stores/TémaStore.js**
---

import { defineStore } from "pinia"

import { ref } from "vue";



export const useRecipeStore = defineStore('recipes', () => {

&nbsp;   const recipes = ref(\[])

&nbsp;   const isLoading = ref(true)

&nbsp;   const error = ref(undefined)



&nbsp;   async function load(){

&nbsp;	const res = await http.get('recipes')

&nbsp;	recipes.value = res.data.data

&nbsp;       isLoading.value = false

&nbsp;   }



&nbsp;   load().catch((err) => {

&nbsp;       error.value=err.value

&nbsp;   })

&nbsp;

&nbsp;   async function createRecipe(data){

&nbsp;	const res = await http.post('recipes', data)

&nbsp;	recipes.value.push(res.data.data)

&nbsp;   }



&nbsp;   async function deleteRecipe(id){

&nbsp;       await http.delete(`recipes/${id}`)

&nbsp;	recipes.value  = recipes.value.filter(r => r.id != id)



&nbsp;       //const index = recipes.value.findIndex(r => r.id === id)

&nbsp;       //if (index > -1){

&nbsp;       //    recipes.value.splice(index, 1)

&nbsp;       //}

&nbsp;   }



&nbsp;   return{

&nbsp;       recipes,

&nbsp;       isLoading,

&nbsp;	error,

&nbsp;	createRecipe

&nbsp;	deleteRecipe

&nbsp;   }

})



###### **frontend/src/main.js**

import { useRecipeStore } from "@stores/RecipeStore"

window.useRecipeStore = useRecipeStore

window.recipeStore = useRecipeStore()



csináld meg a router.js fájlt --> frontend/src/router.js

###### **frontend/src/router.js**

import { createWebHistory } from "vue-router";

import { createRouter } from "vue-router";



const routes = \[]



export const router = createRouter({

&nbsp;   history: createWebHistory(),

&nbsp;   routes: \[

&nbsp;       {path: '/recipes', name: 'recipes', component: Recipes},

&nbsp;       {path: '/recipes/new', name: 'new-recipe', component: NewRecipe},

&nbsp;       {path: '/', name: 'home', redirect: '/recipes'}

&nbsp;   ]

})





##### **FONTOSABB KONZOL PARANCSOK ELLENŐRZÉSRE**

for (const data of \[{name: "Túrógombóc"},

&nbsp;		    {name: "Káposztasaláta"}]) await http.post('recipes', data)



await http.delete('recipes/WFVVZBM')



const store = useRecipeStore()



store.recipes



store.isLoading



await recipeStore.createRecipe({ name: "Rántott leves", ingredients: "Víz, liszt, olaj, paprika"})



recipeStore.recipes









csináld meg a views/recipes mappában a Recipes.vue-t és a NewRecipe.vue-t --> frontend/src/views/recipes/Recipes.vue | frontend/src/views/recipes/NewRecipe.vue



###### **frontend/src/main.js**

import { router } from "./router"

app.use(router)



###### **App.vue, ezentúl mindegyik .vue-ba írj valamit hogy működjön!**

<script setup>



</script>



<template>

&nbsp; <RouterView />

</template>





Csináld meg a Header.vue-t --> frontend/src/Header.vue

###### **frontend/src/Header.vue**

<template>

&nbsp;   <div class="flex bg-blue-200 py-2 px-4 gap-3">

&nbsp;       <RouterLink to="/recipes" class="hover:underline" active-class="underline">Receptek</RouterLink>

&nbsp;       <RouterLink to="/recipes/new" class="hover:underline" active-class="underline">Új recept</RouterLink>

&nbsp;   </div>

</template>



###### **frontend/src/App.vue**

<script setup>

import Header from './Header.vue'



</script>



<template>

&nbsp; <div class="flex flex-col h-screen">

&nbsp;   <Header />

&nbsp;   <div class="bg-green-100 flex-grow-1">

&nbsp;      <RouterView />

&nbsp;   </div>

&nbsp; </div>

</template>



###### **frontend/src/main.js**

import { defaultConfig, plugin } from "@formkit/vue"

app.use(plugin, defaultConfig)



###### **frontend/src/views/recipes/NewRecipes.vue**

<script setup>

import { useRecipeStore } from '@stores/RecipeStore';



const recipeStore = useRecipeStore()



function save(data) {

&nbsp;   recipeStore.createRecipe(data)

}

</script>



<template>

&nbsp;   <FormKit type="form" :actions="false" @submit="save">

&nbsp;       <FormKit type="text" placeholder="Étel neve" input-class="bg-white"/>

&nbsp;       <FormKit type="submit" label="Mentés"/>

&nbsp;   </FormKit>

</template>







##### **VÉGLEGES FÁJLOK:**

###### **frontend/vite.config.js**

import tailwind from '@tailwindcss/vite';

import vue from '@vitejs/plugin-vue';

import { fileURLToPath, URL } from 'node:url';

import { defineConfig } from 'vite';



export default defineConfig({

&nbsp;   plugins: \[vue(), tailwind()],

&nbsp;   server: {

&nbsp;       host: true,

&nbsp;       allowedHosts: \['frontend.vm1.test']

&nbsp;   },

&nbsp;   resolve: {

&nbsp;       alias: {

&nbsp;           '@': fileURLToPath(new URL('./src', import.meta.url)),

&nbsp;           '@assets': fileURLToPath(new URL('./src/assets', import.meta.url)),

&nbsp;           '@stores': fileURLToPath(new URL('./src/stores', import.meta.url)),

&nbsp;           '@views': fileURLToPath(new URL('./src/views', import.meta.url)),

&nbsp;           '@utils': fileURLToPath(new URL('./src/utils', import.meta.url))

&nbsp;       }

&nbsp;   }

})





###### **main/jsconfig.json**

{

&nbsp; "compilerOptions": {

&nbsp;   "paths": {

&nbsp;     "@assets/\*": \["./frontend/src/assets/\*"],

&nbsp;     "@utils/\*": \["./frontend/src/utils/\*"],

&nbsp;     "@views/\*": \["./frontend/src/views/\*"],

&nbsp;     "@stores/\*": \["./frontend/src/stores/\*"],

&nbsp;   }

&nbsp; }

}





###### **frontend/src/utils/http.js**

import axios from "axios";



export const http = axios.create({

&nbsp;   baseURL: "http://jsonserver.vm1.test",

})





frontend/src/router.js

import NewRecipe from "@views/recipes/NewRecipes.vue";

import Recipes from "@views/recipes/Recipes.vue";

import { createWebHistory } from "vue-router";

import { createRouter } from "vue-router";



const routes = \[]



export const router = createRouter({

&nbsp;   history: createWebHistory(),

&nbsp;   routes: \[

&nbsp;       {path: '/recipes', name: 'recipes', component: Recipes},

&nbsp;       {path: '/recipes/new', name: 'new-recipe', component: NewRecipe},

&nbsp;       {path: '/', name: 'home', redirect: '/recipes'}

&nbsp;   ]

})





frontend/src/store/RecipeStore.js

import { http } from "@utils/http";

import { defineStore } from "pinia"

import { ref } from "vue";



export const useRecipeStore = defineStore('recipes', () => {

&nbsp;   const recipes = ref(\[])

&nbsp;   const isLoading = ref(true)

&nbsp;   const error = ref(undefined)



&nbsp;   async function load(){

&nbsp;       const res = await http.get('recipes')

&nbsp;       recipes.value = res.data.data

&nbsp;       isLoading.value = false

&nbsp;   }



&nbsp;   load().catch((err) => {

&nbsp;       error.value=err.value

&nbsp;   })



&nbsp;   async function createRecipe(data){

&nbsp;       const res = await http.post('recipes', data)

&nbsp;   	recipes.value.push(res.data.data)

&nbsp;   }



&nbsp;   async function deleteRecipe(id){

&nbsp;       await http.delete(`recipes/${id}`)

&nbsp;       //const index = recipes.value.findIndex(r => r.id === id)

&nbsp;       //if (index > -1){

&nbsp;       //    recipes.value.splice(index, 1)

&nbsp;       //}

&nbsp;       recipes.value  = recipes.value.filter(r => r.id != id)

&nbsp;   }



&nbsp;   return{

&nbsp;       recipes,

&nbsp;       isLoading,

&nbsp;       error,

&nbsp;       createRecipe,

&nbsp;       deleteRecipe

&nbsp;   }

})









###### **frontend/src/main.js**

import "@assets/style.css"

import { useRecipeStore } from "@stores/RecipeStore"

import { http } from "@utils/http"

import { createPinia } from "pinia"

import {createApp}from 'vue'

import App from "./App.vue"

import { router } from "./router"

import { defaultConfig, plugin } from "@formkit/vue"



const app = createApp(App)

const pinia = createPinia()

app.use(pinia)

app.use(router)

app.use(plugin, defaultConfig)

app.mount('#app-root')



window.http = http;

window.useRecipeStore = useRecipeStore

window.recipeStore = useRecipeStore()









###### **frontend/src/App.vue**

<script setup>

import Header from './Header.vue'



</script>



<template>

&nbsp; <div class="flex flex-col h-screen">

&nbsp;   <Header />

&nbsp;   <div class="bg-green-100 flex-grow-1">

&nbsp;      <RouterView />

&nbsp;   </div>

&nbsp; </div>

</template>





###### **frontend/src/Header.vue**

<template>

&nbsp;   <div class="flex bg-blue-200 py-2 px-4 gap-3">

&nbsp;       <RouterLink to="/recipes" class="hover:underline" active-class="underline">Receptek</RouterLink>

&nbsp;       <RouterLink to="/recipes/new" class="hover:underline" active-class="underline">Új recept</RouterLink>

&nbsp;   </div>

</template>







###### **frontend/src/views/recipes/NewRecipes.vue**

<template>

&nbsp;   <div class="flex bg-blue-200 py-2 px-4 gap-3">

&nbsp;       <RouterLink to="/recipes" class="hover:underline" active-class="underline">Receptek</RouterLink>

&nbsp;       <RouterLink to="/recipes/new" class="hover:underline" active-class="underline">Új recept</RouterLink>

&nbsp;   </div>

</template>







###### **frontend/src/views/recipes/Recipe.vue**

<script setup>



</script>



<template>

Recipes

</template>





###### **jsonserver/data/db.json**

{

&nbsp; "recipes": \[

&nbsp;   {

&nbsp;     "id": "1",

&nbsp;     "name": "Palacsinta",

&nbsp;     "ingredients": "szódavíz, liszt, tojás",

&nbsp;     "preparation": "vegyünk",

&nbsp;     "preparationTime": 15

&nbsp;   },

&nbsp;   {

&nbsp;     "id": "2",

&nbsp;     "name": "another title",

&nbsp;     "views": 200

&nbsp;   },

&nbsp;   {

&nbsp;     "name": "Rántott leves",

&nbsp;     "ingredients": "Víz, liszt, olaj",

&nbsp;     "id": "yBTLBAO"

&nbsp;   },

&nbsp;   {

&nbsp;     "text\_5": "kuglós",

&nbsp;     "id": "5kzuwry"

&nbsp;   }

&nbsp; ]

}









