# 🧠 Gyakorló dolgozat – Mini CRUD SPA Vue 3 + Pinia + Vue Router + FormKit + json-server

### Indulj ki a csatolt `vue-app-2.zip`-ből

---

## 🎯 Cél
Készíts egy kis **egyoldalas alkalmazást**, ami egy egyszerű erőforráson végez CRUD műveletek közül a **listázást, létrehozást és törlést** REST API-n keresztül.  
Az adatmodellben legyen **4 mező**, ne mindegyik szöveges.

A téma választható az alábbiak közül, vagy sajátot is választhatsz.

---

### ⚙️ 0) Kapcsold ki a Copilot kiegészítést

VSCode-ban jobb alsó sarokban **Copilot ikon → All files**-ból elég kivenni a pipát, akkor kiveszi a többiből is.

---

## 🧩 1) Választható témák (vagy saját)

- **Könyvek:** cím, szerző, év, műfaj  
- **Film- vagy sorozatlista:** cím, műfaj, értékelés 1–5  
- **Receptek:** név, hozzávalók, elkészítés, elkészítési idő  
- **Felhasználók:** név, email, szerepkör, születési dátum  
- **Termékek:** név, leírás, ár, készlet darabszám  
- **Saját téma:** bármilyen 4 mezős egyszerű domain modell

Választás után adj egy **erőforrás-nevet** (pl. `books`), és határozd meg a mezőket.  
Legalább egy szöveg és egy nem-szöveg típusú mező legyen.

---

## 📁 2) Projekt-struktúra és tooling

**Mappastruktúra:**
```
src/views/[resource]/… (List, Edit, New)
src/stores/[Resource]Store.js
src/Header.vue
src/router.js
src/utils/http.js
```

**Alias:** `@views` mutasson az `src/views` mappára (vite config és jsonconfig fájlban is)  
**Csomagok:** `vue-router`, `pinia`, `@formkit/vue`, `axios`

---

## 🗄️ 3) json-server beállítás és seed adatok

- Hozz létre egy `db.json`-t a választott erőforrással, pl. `books`.  
- Töltsd fel **3 minta rekorddal.**  
- Indítsd a json-server-t és ellenőrizd a böngészőben, hogy az erőforrás elérhető.

---

## 🌐 4) HTTP kliens (axios)

Telepítés után hozz létre egy **központi axios példányt** az `src/utils/http.js` fájlban.  
Az exportált konstans neve legyen `http`.

---

## 🏪 5) Pinia store – REST műveletek

A kommunikáció a szerverrel a **store-ban** történjen (`load`, `create`, `update`, `delete`).  
Legyen állapot (`ref`): `books` (vagy a témádnak megfelelő), `isLoading`, `error`.

---

## 🧭 6) Vue Router

Kötelező oldalak: **Lista, Új.**  
A gyökér irányítson a listára.

Az `App.vue` csak a `Header` komponenst és a `RouterView`-t jelenítse meg.

---

## 🧱 7) Header komponens navigációval

Készíts egy **fejlécet** egy `Header.vue` komponensben, amiben a navigációs linkek vannak a két oldalra.

---

## 📝 8) FormKit – Új elem űrlap

Az űrlapot **FormKit** segítségével készítsd.  
Az űrlap elküldésekor a **Pinia store** megfelelő metódusát hívja meg.  
Elküldés után vigyen át a listára.

---

## 📋 9) Lista nézet – táblázat és törlés funkció

A listanézetben kártyákon vagy táblázatban jelenjenek meg az elemek, az összes adatukkal.  
Legyenek **törlés gombok** az elemek mellett.

---

## ✅ 10) Elfogadási kritériumok

- Header komponens megjelenik minden oldalon, navigációs linkekkel  
- `/books` listázza az elemeket (vagy a megfelelő path segment)  
- `/books/new` új elemet vesz fel FormKit űrlappal, mentés után visszairányít  
- Törlés működik, lista frissül  
- Minden adat a `json-server db.json`-ból töltődik és oda mentődik vissza
