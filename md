<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Galerie Max & Potes</title>
<style>
  body { font-family: Arial, sans-serif; margin:0; padding:0; background:#f2f2f2;}
  #app-container { max-width:800px; margin:20px auto; padding:20px; background:white; border-radius:10px; box-shadow:0 0 10px rgba(0,0,0,0.1);}
  button { padding:10px 15px; margin:5px; border:none; border-radius:5px; cursor:pointer; background:#1e90ff; color:white;}
  button:hover { background:#0077ff;}
  #gallery { display:grid; grid-template-columns:repeat(auto-fill,minmax(150px,1fr)); gap:10px; margin-top:20px;}
  #gallery img, #gallery video { width:100%; height:150px; object-fit:cover; border-radius:10px;}
  #top-bar { display:flex; justify-content:center; align-items:center; margin-bottom:10px; }
</style>
</head>
<body>

<div id="app-container">
  <div id="top-bar">
    <h2>Galerie Max & Potes</h2>
  </div>

  <div id="filter-controls">
    <button data-filter="all">Tout</button>
    <button data-filter="photos">Photos</button>
    <button data-filter="videos">Vidéos</button>
  </div>

  <div id="gallery"></div>
</div>

<script>
  // Liste de tes fichiers média
  const mediaFiles = [
    { type: 'image', src: 'https://via.placeholder.com/300x200?text=Photo+1' },
    { type: 'image', src: 'https://via.placeholder.com/300x200?text=Photo+2' },
    { type: 'video', src: 'https://www.w3schools.com/html/mov_bbb.mp4' }
  ];

  const gallery = document.getElementById('gallery');
  const filterButtons = document.querySelectorAll('#filter-controls button');

  function renderGallery(filter='all') {
    gallery.innerHTML = '';
    mediaFiles.forEach(file => {
      if(filter === 'all' || (filter === 'photos' && file.type === 'image') || (filter === 'videos' && file.type === 'video')) {
        if(file.type === 'image') {
          const img = document.createElement('img');
          img.src = file.src;
          gallery.appendChild(img);
        } else {
          const vid = document.createElement('video');
          vid.src = file.src;
          vid.controls = true;
          gallery.appendChild(vid);
        }
      }
    });
  }

  // Initial render
  renderGallery();

  // Gestion des filtres
  filterButtons.forEach(btn => {
    btn.addEventListener('click', () => renderGallery(btn.dataset.filter));
  });
</script>

</body>
</html>

