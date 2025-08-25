<!doctype html>
el.appendChild(chip);
})
}


function buildFilters(){
renderChips('#typeChips', unique(PRODUCTS.map(p=>p.type)), 'type');
renderChips('#wheatChips', unique(PRODUCTS.map(p=>p.wheat)), 'wheat');
renderChips('#brandChips', unique(PRODUCTS.map(p=>p.brand)), 'brand');
}


// ===== ФІЛЬТРАЦІЯ ТА СОРТУВАННЯ =====
function applyFilters(){
let list = PRODUCTS.filter(p=>{
const q = state.q.trim().toLowerCase();
const inQ = !q || (p.name.toLowerCase().includes(q) || p.brand.toLowerCase().includes(q) || p.type.toLowerCase().includes(q));
const t = !state.type || p.type===state.type;
const w = !state.wheat || p.wheat===state.wheat;
const b = !state.brand || p.brand===state.brand;
return inQ && t && w && b;
});
switch(state.sort){
case 'price_asc': list.sort((a,b)=>a.price-b.price); break;
case 'price_desc': list.sort((a,b)=>b.price-a.price); break;
case 'name_asc': list.sort((a,b)=>a.name.localeCompare(b.name,'uk')); break;
default: list.sort((a,b)=>b.popular-a.popular);
}
return list;
}


// ===== КАРТКИ ТОВАРІВ =====
function renderCards(list){
const wrap = $('#cards');
wrap.innerHTML = '';
list.forEach(p=>{
const card = document.createElement('article');
card.className='card';
card.innerHTML = `
<div class="thumb">
<img src="${p.img}" alt="${p.name}">
</div>
<div class="body">
<div class="name">${p.name}</div>
<div class="meta">Бренд: ${p.brand} · Тип: ${p.type} · Пшениця: ${p.wheat}</div>
<div class="price">
<span class="now">${p.price} ₴</span>
${p.oldPrice ? `<span class="old">${p.oldPrice} ₴</span>` : ''}
</div>
</div>
<div class="actions">
<button class="btn primary" onclick="alert('Додано до кошика (демо)')">До кошика</button>
<button class="btn" onclick="alert('Деталі товару (демо)')">Детальніше</button>
</div>
`;
wrap.appendChild(card);
})
$('#count').textContent = `Знайдено: ${list.length}`;
}


// ===== ПОДІЇ =====
$('#search').addEventListener('input', e=>{ state.q=e.target.value; update(); })
$('#sort').addEventListener('change', e=>{ state.sort=e.target.value; update(); })
$('#resetBtn').addEventListener('click', ()=>{ state.q=""; $('#search').value=""; state.type=state.wheat=state.brand=null; state.sort='pop'; buildFilters(); update(); })


function update(){
const filtered = applyFilters();
renderCards(filtered);
// підсвічування активних чіпів
$$('.chip').forEach(ch=>ch.classList.remove('active'));
['type','wheat','brand'].forEach(key=>{
if(state[key]){
$$('#'+key+'Chips .chip').forEach(ch=>{ if(ch.textContent.toLowerCase()===state[key]) ch.classList.add('active') })
}
})
}


// INIT
document.getElementById('year').textContent = new Date().getFullYear();
buildFilters();
update();
</script>
</body>
</html>
