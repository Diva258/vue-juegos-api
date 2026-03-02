<template>
  <div class="page">
    <header class="topbar">
      <div class="brand">
        <div class="logo">🎮</div>
        <div>
          <h1>Ofertas de Juegos PC</h1>
          <p>Datos desde la API pública CheapShark (sin key)</p>
        </div>
      </div>

      <div class="controls">
        <input
          v-model="search"
          class="input"
          placeholder="Buscar juego..."
        />

        <select v-model="sort" class="select">
          <option value="titleAsc">Nombre A–Z</option>
          <option value="titleDesc">Nombre Z–A</option>
          <option value="saleAsc">Más barato</option>
          <option value="saleDesc">Más caro</option>
          <option value="saveDesc">Más descuento</option>
        </select>

        <button class="btn" @click="loadDeals" :disabled="loading">
          {{ loading ? 'Cargando...' : 'Recargar' }}
        </button>
      </div>
    </header>

    <main class="container">
      <div class="meta">
        <span class="pill">{{ filteredDeals.length }} resultados</span>
        <span v-if="error" class="error">⚠️ {{ error }}</span>
        <span v-else class="sub">Tip: busca “doom”, “witcher”, “red”.</span>
      </div>

      <section v-if="loading" class="skeletonGrid">
        <div v-for="n in 10" :key="n" class="skeletonCard"></div>
      </section>

      <section v-else class="grid">
        <GameCard
          v-for="deal in filteredDeals"
          :key="deal.dealID"
          :deal="deal"
        />
      </section>

      <section v-if="!loading && filteredDeals.length === 0" class="empty">
        <h2>No hay resultados 😅</h2>
        <p>Prueba con otro nombre.</p>
      </section>
    </main>

    <footer class="footer">
      API usada: CheapShark — endpoint deals (storeID=1)
    </footer>
  </div>
</template>

<script>
import GameCard from './components/GameCard.vue'

export default {
  components: { GameCard },
  data() {
    return {
      deals: [],
      search: '',
      sort: 'saveDesc',
      loading: false,
      error: ''
    }
  },
  mounted() {
    this.loadDeals()
  },
  methods: {
    async loadDeals() {
      this.loading = true
      this.error = ''

      try {
        // 20 ofertas (Steam storeID=1). Puedes subir pageSize a 40 si quieres.
        const url = 'https://www.cheapshark.com/api/1.0/deals?storeID=1&pageSize=30'
        const res = await fetch(url)

        if (!res.ok) throw new Error('No se pudo cargar la API')

        const data = await res.json()

        // normalizamos números para ordenar bien
        this.deals = data.map(d => ({
          ...d,
          salePriceNum: Number(d.salePrice),
          normalPriceNum: Number(d.normalPrice),
          savingsNum: Number(d.savings)
        }))
      } catch (e) {
        this.error = 'Error al consumir la API. Revisa tu internet o intenta de nuevo.'
        console.error(e)
      } finally {
        this.loading = false
      }
    }
  },
  computed: {
    filteredDeals() {
      const q = this.search.trim().toLowerCase()

      let list = this.deals.filter(d =>
        !q || d.title.toLowerCase().includes(q)
      )

      switch (this.sort) {
        case 'titleAsc':
          list.sort((a, b) => a.title.localeCompare(b.title))
          break
        case 'titleDesc':
          list.sort((a, b) => b.title.localeCompare(a.title))
          break
        case 'saleAsc':
          list.sort((a, b) => a.salePriceNum - b.salePriceNum)
          break
        case 'saleDesc':
          list.sort((a, b) => b.salePriceNum - a.salePriceNum)
          break
        case 'saveDesc':
        default:
          list.sort((a, b) => b.savingsNum - a.savingsNum)
      }

      return list
    }
  }
}
</script>

<style>
.page{
  min-height:100vh;
  color:#eaf0ff;
  background:
    radial-gradient(900px 500px at 20% -10%, rgba(124,58,237,.45), transparent 60%),
    radial-gradient(900px 500px at 110% 0%, rgba(16,185,129,.25), transparent 55%),
    linear-gradient(180deg, #070a14, #050612);
  font-family: system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
}

.topbar{
  max-width:1200px;
  margin:0 auto;
  padding:26px 20px 10px;
  display:flex;
  align-items:flex-end;
  justify-content:space-between;
  gap:16px;
  flex-wrap:wrap;
}

.brand{ display:flex; gap:12px; align-items:center; }
.logo{
  width:44px; height:44px; border-radius:14px;
  display:grid; place-items:center;
  background:rgba(255,255,255,.08);
  border:1px solid rgba(255,255,255,.12);
}
.brand h1{ margin:0; font-size:28px; letter-spacing:.2px; }
.brand p{ margin:4px 0 0; color:rgba(234,240,255,.72); }

.controls{
  display:grid;
  grid-template-columns: 1fr 180px 130px;
  gap:10px;
  min-width:min(740px, 100%);
}

.input,.select{
  padding:12px 12px;
  border-radius:14px;
  border:1px solid rgba(255,255,255,.14);
  background:rgba(255,255,255,.06);
  color:#eaf0ff;
  outline:none;
}
.input::placeholder{ color:rgba(234,240,255,.55); }
.input:focus,.select:focus{
  border-color:rgba(124,58,237,.7);
  box-shadow:0 0 0 4px rgba(124,58,237,.22);
}

.btn{
  border:1px solid rgba(255,255,255,.16);
  background:rgba(255,255,255,.08);
  color:#eaf0ff;
  border-radius:14px;
  padding:12px 12px;
  cursor:pointer;
  transition: transform .15s ease, background .15s ease;
}
.btn:hover{ transform: translateY(-1px); background:rgba(255,255,255,.12); }
.btn:disabled{ opacity:.6; cursor:not-allowed; }

.container{
  max-width:1200px;
  margin:0 auto;
  padding:10px 20px 26px;
}

.meta{
  display:flex; align-items:center; gap:12px;
  margin:8px 0 16px;
  flex-wrap:wrap;
}
.pill{
  font-size:13px;
  padding:6px 10px;
  border-radius:999px;
  border:1px solid rgba(124,58,237,.45);
  background:rgba(124,58,237,.18);
}
.sub{ color:rgba(234,240,255,.65); font-size:13px; }
.error{ color:#ffd1d1; font-size:13px; }

.grid{
  display:grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap:16px;
}

.skeletonGrid{
  display:grid;
  grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
  gap:16px;
}
.skeletonCard{
  height:360px;
  border-radius:18px;
  border:1px solid rgba(255,255,255,.12);
  background: linear-gradient(90deg, rgba(255,255,255,.06), rgba(255,255,255,.10), rgba(255,255,255,.06));
  background-size: 200% 100%;
  animation: shimmer 1.2s infinite;
}
@keyframes shimmer{
  0%{ background-position: 0% 0; }
  100%{ background-position: 200% 0; }
}

.empty{
  margin-top:18px;
  padding:16px;
  border-radius:16px;
  background:rgba(255,255,255,.06);
  border:1px solid rgba(255,255,255,.12);
}

.footer{
  max-width:1200px;
  margin:0 auto;
  padding:18px 20px 26px;
  color:rgba(234,240,255,.55);
  font-size:13px;
}

@media (max-width: 900px){
  .controls{ grid-template-columns: 1fr; }
}
</style>