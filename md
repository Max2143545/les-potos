<!doctype html>
<html lang="fr">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Galerie Max & Potes — Albums</title>

<style>
  :root{
    --bg:#0b0b0c; --card:#0f1720; --accent:#06b6d4; --muted:#9aa4ad;
  }
  *{box-sizing:border-box}
  body{
    margin:0; font-family:Inter, system-ui, Arial; background:var(--bg); color:#eef2f7;
  }
  header{
    display:flex; align-items:center; justify-content:space-between;
    padding:14px 20px; background:linear-gradient(180deg,#0e1113, #0b0b0c);
    border-bottom:1px solid rgba(255,255,255,0.03);
    position:sticky; top:0; z-index:50;
  }
  .brand{display:flex; align-items:center; gap:12px; font-weight:700; font-size:18px}
  .brand .dot{width:38px;height:38px;border-radius:10px;background:linear-gradient(135deg,var(--accent),#3b82f6);display:flex;align-items:center;justify-content:center;font-size:18px}
  nav{display:flex; gap:8px; align-items:center}
  button, select, input[type="text"]{background:transparent;border:1px solid rgba(255,255,255,0.06); color:inherit; padding:8px 12px; border-radius:8px}
  button:hover{transform:translateY(-2px); cursor:pointer}
  .main{display:flex; gap:18px; padding:18px}

  /* side */
  .sidebar{width:260px; min-width:220px; background:linear-gradient(180deg,#0d1113,#0b0c0d); padding:14px; border-radius:12px; height:calc(100vh - 120px); overflow:auto}
  .section-title{color:var(--muted); font-size:13px; margin-bottom:8px}
  .albums-list{display:flex; flex-direction:column; gap:6px}
  .album-item{display:flex; justify-content:space-between; align-items:center; padding:8px; border-radius:8px}
  .small{font-size:13px; color:var(--muted)}

  /* content */
  .content{flex:1; min-width:0}
  .controls{display:flex; gap:10px; align-items:center; margin-bottom:12px; flex-wrap:wrap}
  .search{flex:1; min-width:180px}
  .stats{color:var(--muted); font-size:13px}

  /* gallery grid */
  .gallery{
    display:grid;
    grid-template-columns: repeat(auto-fill, minmax(180px, 1fr));
    gap:12px;
  }
  .card{
    background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));
    border-radius:10px; overflow:hidden; position:relative; height:0; padding-bottom:70%;
    display:flex; align-items:center; justify-content:center;
  }
  .card img, .card video{
    position:absolute; top:0; left:0; width:100%; height:100%; object-fit:cover;
  }
  .card .overlay{
    position:absolute; bottom:8px; left:8px; right:8px; display:flex; justify-content:space-between; gap:8px;
    pointer-events:none;
  }
  .tag{background:rgba(0,0,0,0.4); padding:6px 8px; border-radius:8px; font-size:12px; pointer-events:auto;}
  .selected{outline:3px solid rgba(6,182,212,0.12); box-shadow:0 6px 20px rgba(0,0,0,0.6)}

  /* lightbox */
  #lightbox{
    position:fixed; inset:0; background:rgba(2,6,23,0.8); display:none; align-items:center; justify-content:center; z-index:999;
  }
  #lightbox img, #lightbox video{max-width:94%; max-height:90%; border-radius:12px; box-shadow:0 20px 60px rgba(2,6,23,0.8)}
  .lb-actions{position:absolute; top:22px; right:22px; display:flex; gap:8px}

  /* login overlay */
  #loginBox{position:fixed; inset:0; background:linear-gradient(180deg, rgba(1,2,6,0.9), rgba(3,5,11,0.96)); display:flex; align-items:center; justify-content:center; z-index:1000}
  #loginBox .cardL{background:#071018; padding:26px; border-radius:12px; width:320px; text-align:center; box-shadow:0 10px 40px rgba(2,6,23,0.9)}
  input[type=password]{width:100%; margin:10px 0; padding:10px; border-radius:8px; border:1px solid rgba(255,255,255,0.06); background:transparent; color:inherit}

  /* responsive */
  @media (max-width:900px){
    .main{flex-direction:column}
    .sidebar{width:100%; height:auto; order:2}
  }
</style>
</head>
<body>

<header>
  <div class="brand">
    <div class="dot">M</div>
    <div>
      Galerie Max & Potes<br><span class="small">Albums • Photos • Vidéos</span>
    </div>
  </div>

  <nav>
    <button id="btnAll">Tout</button>
    <button id="btnPhotos">Photos</button>
    <button id="btnVideos">Vidéos</button>
    <div style="width:10px"></div>
    <span class="stats" id="stats">0 éléments</span>
    <div style="width:10px"></div>
    <button id="logoutBtn" title="Déconnexion">Déconnexion</button>
  </nav>
</header>

<div class="main">
  <aside class="sidebar">
    <div>
      <div class="section-title">Albums</div>
      <div style="display:flex; gap:8px; margin-bottom:8px">
        <input type="text" id="newAlbumName" placeholder="Nouveau album..." />
        <button id="createAlbumBtn">Créer</button>
      </div>

      <div class="albums-list" id="albumsList">
        <!-- albums insérés par JS -->
      </div>

      <hr style="margin:12px 0; border:none; border-top:1px solid rgba(255,255,255,0.03)">
      <div class="section-title">Gestion rapide</div>
      <div style="display:flex; gap:8px; margin-bottom:8px">
        <select id="slotSelect">
          <option value="">Choisir un emplacement (1-300)</option>
        </select>
        <select id="typeSelect">
          <option value="img">Photo</option>
          <option value="video">Vidéo</option>
        </select>
        <input type="text" id="srcInput" placeholder="nom-fichier ex: photo12.jpg" />
      </div>
      <div style="display:flex; gap:8px">
        <button id="setSlotBtn">Enregistrer</button>
        <button id="clearSlotBtn">Vider</button>
      </div>

      <p class="small" style="margin-top:12px">
        → Pour que les fichiers soient vus par tout le monde, upload tes images dans
        <code>/images/</code> et vidéos dans <code>/videos/</code> sur GitHub.
      </p>
    </div>
  </aside>

  <section class="content">
    <div class="controls">
      <input class="search" id="search" type="text" placeholder="Recherche par nom (ex: photo12) ou album..." />
      <select id="albumSelect"><option value="__all">Tout</option></select>
      <button id="btnExportAlbums">Exporter albums (JSON)</button>
      <button id="btnImportAlbums">Importer albums</button>
      <input type="file" id="importFile" accept="application/json" style="display:none" />
    </div>

    <div class="gallery" id="gallery">
      <!-- cartes générées par JS -->
    </div>

  </section>
</div>

<!-- lightbox -->
<div id="lightbox" onclick="hideLightbox()">
  <div class="lb-actions">
    <button id="lbAddAlbumBtn" class="tag">➕ Ajouter à album</button>
    <button id="lbRemoveAlbumBtn" class="tag">❌ Retirer de l'album</button>
  </div>
  <img id="lbImg" style="display:none" />
  <video id="lbVid" controls style="display:none"></video>
</div>

<!-- login overlay -->
<div id="loginBox">
  <div class="cardL">
    <h2>🔒 Connexion</h2>
    <input id="pw" type="password" placeholder="Mot de passe">
    <button id="loginBtn">Entrer</button>
    <p class="small" style="margin-top:8px">Mot de passe défini dans le code (modifiable)</p>
  </div>
</div>

<script>
/* ======= CONFIG ======= */
const PASSWORD = "maxime"; // change ici
const TOTAL_PHOTOS = 250;  // nombre d'emplacements photo
const TOTAL_VIDEOS = 50;   // nombre d'emplacements vidéo
const MAX_SLOTS = TOTAL_PHOTOS + TOTAL_VIDEOS;

/* ======= STATE ======= */
let items = []; // {id:1..N, type:'img'|'video', src:'images/photo1.jpg' or 'videos/video1.mp4'}
let albums = {}; // { albumName: [id, id, ...] }
let currentFilter = "__all"; // "__all", "img", "video", or albumName
let searchQuery = "";

/* ======= UTIL ======= */
function idToDefaultSrc(id){
  // ids 1..TOTAL_PHOTOS => images/photo{id}.jpg
  // ids TOTAL_PHOTOS+1.. => videos/video{index}.mp4
  if(id <= TOTAL_PHOTOS) return `images/photo${id}.jpg`;
  const vIndex = id - TOTAL_PHOTOS;
  return `videos/video${vIndex}.mp4`;
}

/* ======= INIT ITEMS ======= */
for(let i=1;i<=MAX_SLOTS;i++){
  const type = (i <= TOTAL_PHOTOS) ? 'img' : 'video';
  items.push({id:i, type, src: idToDefaultSrc(i)});
}

/* ======= PERSISTENCE ======= */
function loadAlbums(){
  const raw = localStorage.getItem('gallery_albums_v1');
  if(raw){ try{ albums = JSON.parse(raw) } catch(e){ albums = {} } }
}
function saveAlbums(){ localStorage.setItem('gallery_albums_v1', JSON.stringify(albums)); }

/* ======= LOGIN HANDLING ======= */
function showLogin(show){
  document.getElementById('loginBox').style.display = show ? 'flex' : 'none';
}
document.getElementById('loginBtn').addEventListener('click', ()=>{
  const v = document.getElementById('pw').value;
  if(v === PASSWORD){ sessionStorage.setItem('logged','1'); showLogin(false); renderAll() }
  else alert('Mauvais mot de passe !');
});
document.getElementById('logoutBtn').addEventListener('click', ()=>{
  sessionStorage.removeItem('logged'); showLogin(true);
});

/* auto-login if session present */
if(sessionStorage.getItem('logged') === '1') showLogin(false);

/* ======= UI DOM refs ======= */
const galleryEl = document.getElementById('gallery');
const statsEl = document.getElementById('stats');
const albumSelect = document.getElementById('albumSelect');
const albumsList = document.getElementById('albumsList');

/* ======= ALBUM UI ======= */
function renderAlbumsUI(){
  // select & sidebar
  albumSelect.innerHTML = '<option value="__all">Tout</option>';
  albumsList.innerHTML = '';
  for(const a of Object.keys(albums)){
    const opt = document.createElement('option'); opt.value = a; opt.textContent = a; albumSelect.appendChild(opt);

    const div = document.createElement('div'); div.className = 'album-item';
    const left = document.createElement('div'); left.textContent = a;
    const right = document.createElement('div');
    const btnOpen = document.createElement('button'); btnOpen.textContent = 'Ouvrir'; btnOpen.onclick = ()=>{ currentFilter = a; albumSelect.value = a; renderAll(); }
    const btnDel = document.createElement('button'); btnDel.textContent = 'Supprimer'; btnDel.onclick = ()=>{
      if(confirm(`Supprimer l'album "${a}" ?`)){ delete albums[a]; saveAlbums(); renderAlbumsUI(); renderAll(); }
    };
    right.appendChild(btnOpen); right.appendChild(btnDel);
    div.appendChild(left); div.appendChild(right);
    albumsList.appendChild(div);
  }
  // also update sidebar small counts
  for(const a of Object.keys(albums)){
    // nothing more right now
  }
}

/* create album */
document.getElementById('createAlbumBtn').addEventListener('click', ()=>{
  const name = (document.getElementById('newAlbumName').value || "").trim();
  if(!name) return alert('Donne un nom d\'album.');
  if(albums[name]) return alert('Album existant.');
  albums[name] = []; saveAlbums(); renderAlbumsUI();
  document.getElementById('newAlbumName').value = '';
});

/* ======= SLOT QUICK EDIT UI ======= */
const slotSelect = document.getElementById('slotSelect');
for(let i=1;i<=MAX_SLOTS;i++){
  const opt = document.createElement('option'); opt.value = i; opt.textContent = `${i}`;
  slotSelect.appendChild(opt);
}
document.getElementById('setSlotBtn').addEventListener('click', ()=>{
  const slot = Number(document.getElementById('slotSelect').value);
  const type = document.getElementById('typeSelect').value;
  const src = document.getElementById('srcInput').value.trim();
  if(!slot || !src) return alert('Choisis un emplacement et un nom de fichier.');
  items[slot-1].type = type;
  // normalize src: if no / present, prepend images/ or videos/
  const finalSrc = src.includes('/') ? src : (type==='img' ? `images/${src}` : `videos/${src}`);
  items[slot-1].src = finalSrc;
  renderAll();
});
document.getElementById('clearSlotBtn').addEventListener('click', ()=>{
  const slot = Number(document.getElementById('slotSelect').value);
  if(!slot) return alert('Choisis un emplacement.');
  items[slot-1].src = idToDefaultSrc(slot);
  renderAll();
});

/* ======= RENDER GALLERY ======= */
function filterItems(){
  let list = items.slice();
  // filter by type
  if(currentFilter === 'img') list = list.filter(i=>i.type==='img');
  else if(currentFilter === 'video') list = list.filter(i=>i.type==='video');
  else if(currentFilter !== '__all'){
    // album name
    const albumArr = albums[currentFilter] || [];
    list = list.filter(i=> albumArr.includes(i.id) );
  }
  // search
  if(searchQuery){
    const q = searchQuery.toLowerCase();
    list = list.filter(i=> (i.src || '').toLowerCase().includes(q) || (`${i.id}`).includes(q) );
  }
  return list;
}

function renderAll(){
  galleryEl.innerHTML = '';
  const list = filterItems();
  statsEl.textContent = `${list.length} éléments`;
  for(const it of list){
    const card = document.createElement('div'); card.className = 'card';
    card.dataset.id = it.id;
    if(it.type === 'img'){
      const img = document.createElement('img'); img.src = it.src; img.alt = `photo${it.id}`;
      card.appendChild(img);
    } else {
      const vid = document.createElement('video'); vid.src = it.src; vid.setAttribute('preload','metadata');
      vid.setAttribute('playsinline',''); vid.muted = true; card.appendChild(vid);
    }
    const overlay = document.createElement('div'); overlay.className='overlay';
    const left = document.createElement('div'); left.className='tag'; left.textContent = `#${it.id}`;
    const right = document.createElement('div'); right.style.display='flex'; right.style.gap='6px';
    // add to album button
    const addBtn = document.createElement('button'); addBtn.className='tag'; addBtn.textContent='➕'; addBtn.title='Ajouter à l\'album';
    addBtn.onclick = (e)=>{ e.stopPropagation(); promptAddToAlbum(it.id); };
    // remove from album
    const remBtn = document.createElement('button'); remBtn.className='tag'; remBtn.textContent='✖'; remBtn.title='Retirer de l\'album';
    remBtn.onclick = (e)=>{ e.stopPropagation(); removeFromAllAlbums(it.id); renderAll(); }
    right.appendChild(addBtn); right.appendChild(remBtn);
    overlay.appendChild(left); overlay.appendChild(right);
    card.appendChild(overlay);

    card.addEventListener('click', ()=>{ openLightbox(it); });
    galleryEl.appendChild(card);
  }
}

/* ======= LIGHTBOX ======= */
const lb = document.getElementById('lightbox');
const lbImg = document.getElementById('lbImg');
const lbVid = document.getElementById('lbVid');
let currentLbItem = null;

function openLightbox(item){
  currentLbItem = item;
  if(item.type === 'img'){
    lbVid.style.display='none'; lbVid.pause(); lbImg.style.display='block'; lbImg.src = item.src;
  } else {
    lbImg.style.display='none'; lbVid.style.display='block'; lbVid.src = item.src; lbVid.play();
  }
  lb.style.display='flex';
}
function hideLightbox(){
  lb.style.display='none'; lbVid.pause(); currentLbItem = null;
}

/* add to album prompt */
function promptAddToAlbum(id){
  const name = prompt('Nom de l\'album dans lequel ajouter (nouveau nom crée automatiquement) :');
  if(!name) return;
  if(!albums[name]) albums[name]=[];
  if(!albums[name].includes(id)) albums[name].push(id);
  saveAlbums(); renderAlbumsUI(); renderAll();
}

/* remove from all albums */
function removeFromAllAlbums(id){
  for(const a of Object.keys(albums)){
    albums[a] = albums[a].filter(x=>x!==id);
    if(albums[a].length===0) delete albums[a];
  }
  saveAlbums(); renderAlbumsUI();
}

/* lightbox album buttons */
document.getElementById('lbAddAlbumBtn').addEventListener('click', (e)=>{
  e.stopPropagation();
  if(!currentLbItem) return;
  const name = prompt('Ajouter "'+ (currentLbItem.type==='img' ? 'photo' : 'vidéo')+currentLbItem.id + '" dans quel album ?');
  if(!name) return;
  if(!albums[name]) albums[name]=[];
  if(!albums[name].includes(currentLbItem.id)) albums[name].push(currentLbItem.id);
  saveAlbums(); renderAlbumsUI(); alert('Ajouté !');
});
document.getElementById('lbRemoveAlbumBtn').addEventListener('click',(e)=>{
  e.stopPropagation();
  if(!currentLbItem) return;
  removeFromAllAlbums(currentLbItem.id);
  alert('Retiré de tous les albums');
});

/* ======= FILTERS & SEARCH ======= */
document.getElementById('btnAll').addEventListener('click', ()=>{ currentFilter='__all'; albumSelect.value='__all'; renderAll(); });
document.getElementById('btnPhotos').addEventListener('click', ()=>{ currentFilter='img'; albumSelect.value='__all'; renderAll(); });
document.getElementById('btnVideos').addEventListener('click', ()=>{ currentFilter='video'; albumSelect.value='__all'; renderAll(); });
document.getElementById('search').addEventListener('input', (e)=>{ searchQuery = e.target.value.trim(); renderAll(); });

albumSelect.addEventListener('change', (e)=>{ currentFilter = e.target.value; renderAll(); });

/* ======= IMPORT / EXPORT albums JSON ======= */
document.getElementById('btnExportAlbums').addEventListener('click', ()=>{
  const data = { albums, items }; // export also items so tu peux recharger mapping
  const blob = new Blob([JSON.stringify(data, null, 2)], {type:'application/json'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a'); a.href = url; a.download = 'albums-export.json'; a.click();
  URL.revokeObjectURL(url);
});
document.getElementById('btnImportAlbums').addEventListener('click', ()=> document.getElementById('importFile').click());
document.getElementById('importFile').addEventListener('change', (e)=>{
  const f = e.target.files[0]; if(!f) return;
  const reader = new FileReader(); reader.onload = ()=> {
    try{
      const data = JSON.parse(reader.result);
      if(data.albums) albums = data.albums; if(data.items) { items = data.items; } // optional
      saveAlbums(); renderAlbumsUI(); renderAll(); alert('Import OK');
    } catch(err){ alert('Fichier invalide'); }
  }; reader.readAsText(f);
});

/* ======= STARTUP ======= */
loadAlbums();
renderAlbumsUI();
renderAll();

/* If login required show overlay */
if(sessionStorage.getItem('logged') !== '1') showLogin(true);

/* ======= Helpful note to user dev (console) ======= */
console.log("Site initialisé. Pour rendre visible un fichier sur GitHub Pages : push /images/photoX.jpg ou /videos/videoY.mp4 dans ton repo.");
</script>

</body>
</html>

