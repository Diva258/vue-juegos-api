<template>
  <article class="card">
    <div class="cover">
      <img :src="deal.thumb" :alt="deal.title" />
      <span class="tag">-{{ Math.round(deal.savingsNum) }}%</span>
    </div>

    <div class="body">
      <h2 class="name" :title="deal.title">{{ deal.title }}</h2>

      <div class="prices">
        <span class="sale">${{ deal.salePrice }}</span>
        <span class="normal">${{ deal.normalPrice }}</span>
      </div>

      <a
        class="link"
        :href="dealLink"
        target="_blank"
        rel="noopener"
      >
        Ver oferta
      </a>
    </div>
  </article>
</template>

<script>
export default {
  props: {
    deal: { type: Object, required: true }
  },
  computed: {
    dealLink() {
      // link público del deal
      return `https://www.cheapshark.com/redirect?dealID=${this.deal.dealID}`
    }
  }
}
</script>

<style scoped>
.card{
  border-radius:18px;
  overflow:hidden;
  background:rgba(255,255,255,.06);
  border:1px solid rgba(255,255,255,.12);
  box-shadow:0 16px 40px rgba(0,0,0,.35);
  transition: transform .18s ease, border-color .18s ease, box-shadow .18s ease;
}
.card:hover{
  transform: translateY(-5px);
  border-color: rgba(124,58,237,.55);
  box-shadow:0 18px 55px rgba(0,0,0,.45);
}

.cover{
  position:relative;
  height:220px;
  background:rgba(0,0,0,.25);
}
.cover img{
  width:100%;
  height:100%;
  object-fit:cover;
  display:block;
  filter:saturate(1.05) contrast(1.03);
}

.tag{
  position:absolute;
  left:12px;
  top:12px;
  padding:6px 10px;
  border-radius:999px;
  font-size:12px;
  color:#fff;
  background:rgba(16,185,129,.25);
  border:1px solid rgba(16,185,129,.45);
  backdrop-filter: blur(8px);
}

.body{ padding:14px 14px 16px; }

.name{
  margin:0 0 10px;
  font-size:16px;
  letter-spacing:.2px;
  white-space:nowrap;
  overflow:hidden;
  text-overflow:ellipsis;
}

.prices{
  display:flex;
  gap:10px;
  align-items:baseline;
  margin-bottom:12px;
}
.sale{
  font-size:18px;
  font-weight:700;
}
.normal{
  color:rgba(234,240,255,.6);
  text-decoration: line-through;
  font-size:13px;
}

.link{
  display:inline-block;
  width:100%;
  text-align:center;
  padding:10px 12px;
  border-radius:12px;
  border:1px solid rgba(255,255,255,.14);
  background:rgba(255,255,255,.08);
  color:#eaf0ff;
  text-decoration:none;
  transition: background .15s ease, transform .15s ease;
}
.link:hover{
  background:rgba(255,255,255,.12);
  transform: translateY(-1px);
}
</style>