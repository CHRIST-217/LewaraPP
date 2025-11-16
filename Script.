// ---------- CONFIG ----------
// Mets ton numéro WhatsApp ici (format international, ex: +33123456789)
const WHATSAPP_NUMBER = "+237676793152"; // <<--- remplace par ton numéro

// Produits d'exemple — modifie selon tes vêtements
const PRODUCTS = [
  { id: 'p1', title: 'T-shirt blanc', price: 15.00, img: 'https://via.placeholder.com/600x800?text=T-shirt+blanc', desc: 'Taille M, bon état.' },
  { id: 'p2', title: 'Robe longue', price: 35.00, img: 'https://via.placeholder.com/600x800?text=Robe+longue', desc: 'Taille S, très peu portée.' },
  { id: 'p3', title: 'Veste en jean', price: 40.00, img: 'https://via.placeholder.com/600x800?text=Veste+jean', desc: 'Taille L, style oversize.' }
];

// ---------- STORAGE KEYS ----------
const LS_LIKES = 'mb_likes';           // likes par produit {id:count}
const LS_TESTIMONIALS = 'mb_testimonials'; // tableau d'avis

// ---------- UTIL ----------
const q = sel => document.querySelector(sel);
const qAll = sel => Array.from(document.querySelectorAll(sel));
function fmtPrice(n){ return n.toFixed(2) + ' €'; }
function encode(text){ return encodeURIComponent(text); }

// ---------- Likes (localStorage) ----------
function loadLikes(){ return JSON.parse(localStorage.getItem(LS_LIKES) || '{}'); }
function saveLikes(obj){ localStorage.setItem(LS_LIKES, JSON.stringify(obj)); }
function toggleLike(productId){
  const likes = loadLikes();
  likes[productId] = (likes[productId] || 0) + 1;
  saveLikes(likes);
  renderProducts();
}

// ---------- Testimonials ----------
function loadTestimonials(){ return JSON.parse(localStorage.getItem(LS_TESTIMONIALS) || '[]'); }
function saveTestimonials(arr){ localStorage.setItem(LS_TESTIMONIALS, JSON.stringify(arr)); }
function addTestimonial(name, msg, rating){
  const arr = loadTestimonials();
  arr.unshift({ name, msg, rating: +rating, at: Date.now() });
  saveTestimonials(arr);
  renderTestimonials();
}

function computeAvgRating(){
  const arr = loadTestimonials();
  if(arr.length === 0) return {avg:0,count:0};
  const sum = arr.reduce((s,a)=>s+a.rating,0);
  return { avg: Math.round((sum/arr.length)*10)/10, count: arr.length };
}

// ---------- Render produits ----------
function productCardHTML(p, likesObj){
  const likes = likesObj[p.id] || 0;
  return `
    <article class="card" id="${p.id}">
      <img src="${p.img}" alt="${p.title}" loading="lazy" />
      <h4>${p.title}</h4>
      <p>${p.desc}</p>
      <div class="row">
        <div class="price">${fmtPrice(p.price)}</div>
      </div>
      <div style="display:flex;gap:8px;align-items:center;margin-top:10px">
        <button class="btn whatsapp" data-id="${p.id}">Acheter sur WhatsApp</button>
        <button class="like-btn" data-id="${p.id}" title="J'aime">❤️ <span class="likes-count">${likes}</span></button>
        <button class="button" data-id="${p.id}" aria-haspopup="dialog">Voir</button>
      </div>
    </article>
  `;
}

function renderProducts(){
  const likes = loadLikes();
  const el = q('#products');
  el.innerHTML = PRODUCTS.map(p => productCardHTML(p, likes)).join('');
  // listeners
  qAll('.whatsapp').forEach(b => b.addEventListener('click', onWhatsAppClick));
  qAll('.like-btn').forEach(b => b.addEventListener('click', e => {
    const id = e.currentTarget.dataset.id; toggleLike(id);
  }));
  qAll('.card .button').forEach(b => b.addEventListener('click', e => {
    const id = e.currentTarget.dataset.id; openProductModal(id);
  }));
}

// ---------- WhatsApp ----------
function onWhatsAppClick(e){
  const id = e.currentTarget.dataset.id;
  const p = PRODUCTS.find(x => x.id === id);
  const text = `Bonjour ! Je suis intéressé(e) par "${p.title}" (prix ${p.price}€). Est-il toujours disponible ?`;
  // Format wa.me link
  const cleanNum = WHATSAPP_NUMBER.replace(/[^\d+]/g, '');
  const waLink = `https://wa.me/${cleanNum.replace(/^\+/, '')}?text=${encode(text)}`;
  window.open(waLink, '_blank');
}
q('#contact-whatsapp')?.addEventListener('click', ()=> {
  const text = `Bonjour, je souhaite avoir des infos sur vos vêtements à vendre.`;
  const cleanNum = WHATSAPP_NUMBER.replace(/[^\d+]/g, '');
  const waLink = `https://wa.me/${cleanNum.replace(/^\+/, '')}?text=${encode(text)}`;
  window.open(waLink, '_blank');
});

// ---------- Modal produit ----------
function openProductModal(id){
  const p = PRODUCTS.find(x => x.id === id);
  const likes = loadLikes()[p.id] || 0;
  const body = q('#modal-body');
  body.innerHTML = `
    <img src="${p.img}" alt="${p.title}" style="width:100%;height:320px;object-fit:cover;border-radius:6px" />
    <h3>${p.title}</h3>
    <p>${p.desc}</p>
    <p style="font-weight:700">${fmtPrice(p.price)}</p>
    <div style="display:flex;gap:8px;margin-top:8px">
      <button class="whatsapp btn" data-id="${p.id}">Acheter sur WhatsApp</button>
      <button class="like-btn" data-id="${p.id}">❤️ <span class="likes-count">${likes}</span></button>
    </div>
  `;
  q('#product-modal').classList.remove('hidden');
  // attach listeners inside modal
  q('#product-modal .whatsapp')?.addEventListener('click', onWhatsAppClick);
  q('#product-modal .like-btn')?.addEventListener('click', e => {
    const id = e.currentTarget.dataset.id; toggleLike(id);
    openProductModal(id); // refresh modal counts
  });
}
q('#close-modal')?.addEventListener('click', ()=> q('#product-modal').classList.add('hidden'));

// close modal on outside click
q('#product-modal')?.addEventListener('click', e => {
  if(e.target.id === 'product-modal') q('#product-modal').classList.add('hidden');
});

// ---------- Testimonials rendering & form ----------
function renderTestimonials(){
  const arr = loadTestimonials();
  const list = q('#testimonials-list');
  if(arr.length === 0){ list.innerHTML = '<p>Aucun avis pour le moment.</p>'; }
  else {
    list.innerHTML = arr.map(a => `
      <div class="testimonial">
        <div style="display:flex;justify-content:space-between;align-items:center">
          <strong>${escapeHtml(a.name)}</strong>
          <span>${'★'.repeat(a.rating)}${'☆'.repeat(5-a.rating)}</span>
        </div>
        <div style="margin-top:6px">${escapeHtml(a.msg)}</div>
        <div style="margin-top:6px;font-size:.8rem;color:#666">${new Date(a.at).toLocaleString()}</div>
      </div>
    `).join('');
  }
  const s = computeAvgRating();
  q('#avg-rating').textContent = s.avg.toFixed(1);
  q('#count-rating').textContent = s.count;
}

function escapeHtml(s){ return (s+'').replace(/[&<>"']/g, c => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#39;'}[c])); }

// rating input behaviour
function initRatingInput(){
  const starWrap = q('#t-rating');
  if(!starWrap) return;
  let current = 5; // default
  starWrap.querySelectorAll('button').forEach(b => {
    const v = +b.dataset.value;
    b.addEventListener('click', ()=> {
      current = v;
      starWrap.setAttribute('data-rating', v);
      updateStars(starWrap, v);
    });
  });
  updateStars(starWrap, current);
  // on submit, read starWrap.dataset.rating
  q('#testimonial-form').addEventListener('submit', e => {
    e.preventDefault();
    const name = q('#t-name').value.trim();
    const msg = q('#t-message').value.trim();
    const rating = +starWrap.getAttribute('data-rating') || 5;
    if(!name || !msg) return alert('Merci de remplir le nom et le commentaire.');
    addTestimonial(name, msg, rating);
    q('#testimonial-form').reset();
    updateStars(starWrap, 5);
  });
}
function updateStars(el, r){
  el.querySelectorAll('button').forEach(b => {
    const v = +b.dataset.value;
    b.classList.toggle('star-on', v <= r);
  });
}

// ---------- Init ----------
function init(){
  renderProducts();
  renderTestimonials();
  initRatingInput();
}
init();
