# sonyaa.minimarket
<!DOCTYPE html><html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Toko Online Mini</title>
  <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
</head>
<body class="bg-gray-100 font-sans">
  <header class="bg-indigo-600 text-white py-4 shadow-md">
    <div class="max-w-6xl mx-auto flex justify-between items-center px-4">
      <h1 class="text-2xl font-bold">🛍️ Toko Online sonya</h1>
      <button id="cartBtn" class="bg-white text-indigo-600 font-semibold px-3 py-1 rounded-md">Keranjang (0)</button>
    </div>
  </header>  <main class="max-w-6xl mx-auto p-4">
    <div class="flex justify-between mb-4 flex-col sm:flex-row gap-2">
      <input id="search" type="text" placeholder="Cari produk..." class="border px-3 py-2 rounded-md w-full sm:w-1/3">
      <select id="filter" class="border px-3 py-2 rounded-md w-full sm:w-1/4">
        <option value="Semua">Semua Kategori</option>
        <option value="Kesehatan">Kesehatan</option>
        <option value="Pakaian">Pakaian</option>
        <option value="Aksesoris">Aksesoris</option>
      </select>
    </div><div id="products" class="grid sm:grid-cols-2 lg:grid-cols-3 gap-4"></div>

  </main>  <div id="cartPanel" class="fixed inset-y-0 right-0 w-80 bg-white shadow-xl transform translate-x-full transition-transform p-4 overflow-y-auto">
    <div class="flex justify-between items-center border-b pb-2 mb-3">
      <h2 class="font-semibold text-lg">Keranjang</h2>
      <button id="closeCart" class="text-red-500 font-semibold">Tutup ✕</button>
    </div>
    <div id="cartItems" class="space-y-2"></div>
    <div class="border-t mt-3 pt-3 flex justify-between font-semibold">
      <span>Total:</span><span id="total">Rp 0</span>
    </div><!-- Bagian Pembayaran -->
<div id="checkout" class="mt-5 border-t pt-3 hidden">
  <h3 class="font-semibold mb-2 text-indigo-600">💰 Pembayaran</h3>
  <form id="paymentForm" class="space-y-2">
    <input type="text" id="nama" placeholder="Nama Lengkap" class="border rounded-md px-3 py-2 w-full" required>
    <textarea id="alamat" placeholder="Alamat Lengkap" class="border rounded-md px-3 py-2 w-full" required></textarea>
    <select id="metode" class="border rounded-md px-3 py-2 w-full">
      <option value="Transfer Bank">🏦 Transfer Bank</option>
      <option value="E-Wallet">📱 E-Wallet</option>
      <option value="COD">🚚 Bayar di Tempat (COD)</option>
    </select>
    <button type="submit" class="bg-indigo-600 text-white font-semibold w-full py-2 rounded-md hover:bg-indigo-700">Bayar Sekarang</button>
  </form>
</div>

  </div>  <footer class="text-center text-sm text-gray-600 py-4 mt-6">© 2025 Toko Online Mini</footer>  <script>
    const products = [
      {id:'p1',name:'Sandal Terapi Kulit Durian',price:125000,cat:'Kesehatan',desc:'Sandal terapi nyaman dari kulit durian alami'},
      {id:'p2',name:'Kaos Katun Polos',price:90000,cat:'Pakaian',desc:'Kaos adem cocok untuk sehari-hari'},
      {id:'p3',name:'Botol Minum Stainless',price:60000,cat:'Aksesoris',desc:'Botol kuat 500ml untuk outdoor'},
      {id:'p4',name:'Masker Herbal Alami',price:45000,cat:'Kesehatan',desc:'Masker wajah dari bahan herbal alami'}
    ];

    const cart = {};
    const productsDiv = document.getElementById('products');
    const cartBtn = document.getElementById('cartBtn');
    const cartPanel = document.getElementById('cartPanel');
    const cartItems = document.getElementById('cartItems');
    const total = document.getElementById('total');
    const checkout = document.getElementById('checkout');

    function renderProducts(list = products) {
      productsDiv.innerHTML = list.map(p => `
        <div class='bg-white rounded-lg shadow p-4 hover:shadow-lg transition'>
          <h3 class='font-bold text-lg mb-1'>${p.name}</h3>
          <p class='text-sm text-gray-600 mb-2'>${p.desc}</p>
          <div class='flex justify-between items-center'>
            <span class='font-semibold text-indigo-600'>Rp ${p.price.toLocaleString()}</span>
            <button onclick='addToCart("${p.id}")' class='bg-indigo-600 text-white px-3 py-1 rounded-md hover:bg-indigo-700'>Tambah</button>
          </div>
        </div>`).join('');
    }

    function addToCart(id){cart[id]=(cart[id]||0)+1;updateCart();}
    function removeFromCart(id){delete cart[id];updateCart();}

    function updateCart(){
      let totalHarga=0;
      cartItems.innerHTML='';
      for(const id in cart){
        const p=products.find(x=>x.id===id);
        const qty=cart[id];
        totalHarga+=p.price*qty;
        cartItems.innerHTML+=`<div class='flex justify-between items-center border p-2 rounded-md'>
          <div><div class='font-medium'>${p.name}</div><div class='text-sm text-gray-500'>x${qty}</div></div>
          <button onclick='removeFromCart("${id}")' class='text-red-500 text-sm'>Hapus</button></div>`;
      }
      total.textContent='Rp '+totalHarga.toLocaleString();
      cartBtn.textContent=`Keranjang (${Object.keys(cart).length})`;
      checkout.classList.toggle('hidden', Object.keys(cart).length===0);
    }

    document.getElementById('closeCart').onclick=()=>cartPanel.classList.add('translate-x-full');
    cartBtn.onclick=()=>cartPanel.classList.toggle('translate-x-full');

    document.getElementById('search').oninput=e=>filterProducts();
    document.getElementById('filter').onchange=e=>filterProducts();

    function filterProducts(){
      const q=document.getElementById('search').value.toLowerCase();
      const cat=document.getElementById('filter').value;
      const list=products.filter(p=>(cat==='Semua'||p.cat===cat)&&p.name.toLowerCase().includes(q));
      renderProducts(list);
    }

    document.getElementById('paymentForm').onsubmit = (e)=>{
      e.preventDefault();
      const nama=document.getElementById('nama').value.trim();
      const alamat=document.getElementById('alamat').value.trim();
      const metode=document.getElementById('metode').value;
      if(!nama||!alamat)return alert('Isi data lengkap ya!');

      cartItems.innerHTML='<div class="text-center text-green-600 font-semibold">✅ Pembayaran Berhasil!<br>Terima kasih '+nama+'!<br>Pesananmu akan dikirim ke:<br>'+alamat+'</div>';
      Object.keys(cart).forEach(k=>delete cart[k]);
      updateCart();
      setTimeout(()=>alert('Pesanan dikonfirmasi via '+metode),800);
    }

    renderProducts();
  </script></body>
</html>
