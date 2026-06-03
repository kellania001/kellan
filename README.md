<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=yes, viewport-fit=cover">
  <title>Arka - Keuangan Jajan</title>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      background: linear-gradient(145deg, #f9e7cf 0%, #f5d7b3 100%);
      font-family: 'Segoe UI', 'Poppins', system-ui, -apple-system, sans-serif;
      min-height: 100vh;
      display: flex;
      justify-content: center;
      align-items: center;
      padding: 16px;
      margin: 0;
    }

    .app-container {
      width: 100%;
      max-width: 440px;
      background: #fff7ed;
      border-radius: 40px;
      box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25), inset 0 2px 6px rgba(255, 255, 255, 0.7);
      padding: 20px 20px 28px;
      display: flex;
      flex-direction: column;
      gap: 18px;
      border: 1px solid rgba(255, 255, 255, 0.6);
      backdrop-filter: blur(2px);
    }

    /* Header dengan nama Arka */
    .header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      background: #ffb56b;
      background-image: linear-gradient(135deg, #ff9f4b 0%, #f2851c 100%);
      padding: 14px 22px;
      border-radius: 80px;
      box-shadow: 0 10px 15px -5px rgba(242, 133, 28, 0.5);
      margin-bottom: 6px;
      color: white;
      position: relative;
      overflow: hidden;
    }

    .app-name {
      font-size: 2.4rem;
      font-weight: 800;
      letter-spacing: 1px;
      text-shadow: 2px 2px 0 rgba(0,0,0,0.15);
      background: rgba(255,255,255,0.15);
      padding: 4px 18px;
      border-radius: 60px;
      cursor: pointer;
      transition: all 0.2s ease;
      user-select: none;
      backdrop-filter: blur(4px);
      line-height: 1.2;
    }

    .app-name:active {
      transform: scale(0.94);
      background: rgba(255,255,255,0.35);
      box-shadow: 0 0 15px rgba(255,255,200,0.8);
    }

    .total-badge {
      background: white;
      color: #c2410c;
      font-weight: 700;
      font-size: 1rem;
      padding: 8px 18px;
      border-radius: 30px;
      display: flex;
      align-items: center;
      gap: 4px;
      box-shadow: 0 4px 8px rgba(0,0,0,0.1);
    }

    /* Menu grid */
    .menu-grid {
      display: grid;
      grid-template-columns: repeat(2, 1fr);
      gap: 14px;
      margin: 6px 0 4px;
    }

    .menu-card {
      background: white;
      border-radius: 28px;
      padding: 18px 10px 14px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      box-shadow: 0 12px 22px -10px rgba(0,0,0,0.15);
      transition: all 0.2s ease;
      cursor: pointer;
      border: 2px solid transparent;
      background: #ffffff;
      position: relative;
      backdrop-filter: blur(2px);
      user-select: none;
    }

    .menu-card:active {
      transform: scale(0.96);
      border-color: #f97316;
      background: #fff7ed;
      box-shadow: 0 8px 18px rgba(249, 115, 22, 0.4);
    }

    .emoji-icon {
      font-size: 2.8rem;
      margin-bottom: 6px;
      filter: drop-shadow(0 6px 6px rgba(0,0,0,0.2));
    }

    .menu-label {
      font-weight: 700;
      font-size: 1rem;
      color: #4a3728;
      background: #fef3e2;
      padding: 5px 14px;
      border-radius: 20px;
      letter-spacing: 0.3px;
      margin-top: 4px;
    }

    .price-tag {
      font-size: 0.9rem;
      font-weight: 700;
      color: #b45309;
      margin-top: 6px;
      background: #ffedd5;
      padding: 2px 12px;
      border-radius: 12px;
    }

    /* Riwayat transaksi */
    .history-section {
      background: #ffffff;
      border-radius: 30px;
      padding: 14px 16px;
      box-shadow: inset 0 2px 8px rgba(0,0,0,0.03), 0 8px 18px rgba(0,0,0,0.08);
      max-height: 170px;
      overflow-y: auto;
      display: flex;
      flex-direction: column;
      gap: 8px;
      border: 1px solid #ffe4cc;
    }

    .history-title {
      font-weight: 700;
      color: #7c4a2e;
      font-size: 0.9rem;
      display: flex;
      align-items: center;
      gap: 6px;
      margin-bottom: 2px;
      padding-left: 4px;
    }

    .history-list {
      list-style: none;
      display: flex;
      flex-direction: column;
      gap: 8px;
    }

    .history-item {
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 0.9rem;
      background: #fff9f2;
      padding: 8px 14px;
      border-radius: 18px;
      border-left: 5px solid #f97316;
    }

    .item-name {
      font-weight: 600;
      color: #3d2b1a;
    }

    .item-price {
      font-weight: 700;
      color: #c2410c;
    }

    .empty-message {
      color: #b08968;
      font-style: italic;
      text-align: center;
      padding: 10px;
      font-size: 0.9rem;
    }

    .reset-area {
      display: flex;
      justify-content: flex-end;
      margin-top: 2px;
    }

    .reset-btn {
      background: none;
      border: 1px solid #fed7aa;
      background: #fff1e0;
      padding: 8px 18px;
      border-radius: 24px;
      font-weight: 600;
      color: #9a4d1c;
      cursor: pointer;
      font-size: 0.8rem;
      transition: 0.2s;
      display: flex;
      align-items: center;
      gap: 4px;
    }

    .reset-btn:active {
      background: #ffd9b5;
      border-color: #f97316;
    }

    .footer-note {
      text-align: center;
      color: #b0845c;
      font-size: 0.7rem;
      margin-top: 2px;
    }

    /* klik efek pada nama Arka */
    .clicked-effect {
      transition: 0.15s;
    }
  </style>
</head>
<body>
<div class="app-container">
  <!-- Header dengan nama aplikasi bisa diklik -->
  <div class="header">
    <span class="app-name" id="arkaBrand">✨ Arka</span>
    <div class="total-badge">
      <span>💰</span> <span id="totalDisplay">Rp0</span>
    </div>
  </div>

  <!-- Grid menu jajan -->
  <div class="menu-grid">
    <!-- Es Teh -->
    <div class="menu-card" data-menu="Es Teh" data-price="5000">
      <span class="emoji-icon">🧊🍵</span>
      <span class="menu-label">Es Teh</span>
      <span class="price-tag">Rp5.000</span>
    </div>
    <!-- Es Kopi -->
    <div class="menu-card" data-menu="Es Kopi" data-price="8000">
      <span class="emoji-icon">☕❄️</span>
      <span class="menu-label">Es Kopi</span>
      <span class="price-tag">Rp8.000</span>
    </div>
    <!-- Telor -->
    <div class="menu-card" data-menu="Telor" data-price="4000">
      <span class="emoji-icon">🥚🍳</span>
      <span class="menu-label">Telor</span>
      <span class="price-tag">Rp4.000</span>
    </div>
    <!-- Mie Omelet -->
    <div class="menu-card" data-menu="Mie Omelet" data-price="10000">
      <span class="emoji-icon">🍜🍳</span>
      <span class="menu-label">Mie Omelet</span>
      <span class="price-tag">Rp10.000</span>
    </div>
  </div>

  <!-- Riwayat pembelian -->
  <div class="history-section">
    <div class="history-title">📋 Riwayat Jajan</div>
    <ul class="history-list" id="historyList">
      <li class="empty-message">Belum ada jajan dicatat 🍃</li>
    </ul>
  </div>

  <div class="reset-area">
    <button class="reset-btn" id="resetButton">
      <span>🗑️</span> Hapus Semua
    </button>
  </div>
  <div class="footer-note">ketuk menu untuk tambah • klik <strong>Arka</strong> ✨</div>
</div>

<script>
  (function() {
    // Data harga sesuai menu
    const menuPrices = {
      "Es Teh": 5000,
      "Es Kopi": 8000,
      "Telor": 4000,
      "Mie Omelet": 10000
    };

    // State: array transaksi { name, price }
    let transactions = [];

    // Elemen DOM
    const historyListEl = document.getElementById('historyList');
    const totalDisplayEl = document.getElementById('totalDisplay');
    const menuCards = document.querySelectorAll('.menu-card');
    const resetBtn = document.getElementById('resetButton');
    const arkaBrand = document.getElementById('arkaBrand');

    // Fungsi format rupiah
    function formatRupiah(amount) {
      return 'Rp' + amount.toLocaleString('id-ID');
    }

    // Hitung total dari transaksi
    function calculateTotal() {
      return transactions.reduce((sum, item) => sum + item.price, 0);
    }

    // Update tampilan riwayat dan total
    function renderUI() {
      // Render list history
      if (transactions.length === 0) {
        historyListEl.innerHTML = '<li class="empty-message">Belum ada jajan dicatat 🍃</li>';
      } else {
        let html = '';
        transactions.forEach((item, index) => {
          html += `
            <li class="history-item">
              <span class="item-name">🍽️ ${item.name}</span>
              <span class="item-price">${formatRupiah(item.price)}</span>
            </li>
          `;
        });
        historyListEl.innerHTML = html;
      }

      // Update total
      const total = calculateTotal();
      totalDisplayEl.textContent = formatRupiah(total);
    }

    // Tambah transaksi baru
    function addTransaction(menuName, price) {
      transactions.push({
        name: menuName,
        price: price
      });
      renderUI();
    }

    // Reset semua transaksi
    function resetTransactions() {
      if (transactions.length === 0) return;
      transactions = [];
      renderUI();
      // Sedikit animasi pada tombol reset (opsional)
    }

    // Event listener untuk setiap menu card
    menuCards.forEach(card => {
      card.addEventListener('click', function(e) {
        const menu = this.getAttribute('data-menu');
        const priceAttr = this.getAttribute('data-price');
        const price = parseInt(priceAttr, 10);
        
        if (menu && !isNaN(price)) {
          addTransaction(menu, price);
          
          // Efek kecil visual: getar halus? biar smooth
          this.style.transition = '0.1s';
          this.style.transform = 'scale(0.92)';
          setTimeout(() => {
            this.style.transform = '';
          }, 120);
        }
      });
    });

    // Tombol reset
    resetBtn.addEventListener('click', function() {
      resetTransactions();
    });

    // *** FITUR NAMA ARKA BISA DI KLIK ***
    // Ketika diklik, munculkan pesan atau animasi khusus, juga bisa menampilkan info.
    arkaBrand.addEventListener('click', function(e) {
      e.stopPropagation(); // biar tidak mengganggu event lain
      
      // Efek visual klik
      arkaBrand.style.transform = 'scale(0.9)';
      setTimeout(() => {
        arkaBrand.style.transform = '';
      }, 150);

      // Tampilkan alert atau toast simpel: menunjukkan total jajan saat ini
      const totalNow = calculateTotal();
      const message = totalNow === 0 
        ? '🛒 Arka: Kamu belum jajan apa pun. Yuk pilih menu!' 
        : `🧾 Arka mencatat total jajanmu: ${formatRupiah(totalNow)}. Semangat ngemil!`;
      
      alert(message);
      
      // Bisa juga tambahkan animasi kecil atau getar di header (tambahan)
      const header = document.querySelector('.header');
      header.style.transition = '0.15s';
      header.style.transform = 'scale(1.02)';
      setTimeout(() => {
        header.style.transform = '';
      }, 150);
    });

    // Inisialisasi tampilan
    renderUI();

    // Optional: simpan ke localStorage agar data tidak hilang? 
    // Untuk kesederhanaan tidak pakai, tapi bisa ditambahkan.
    // Bisa load dari localStorage jika mau, tapi sesuai permintaan fokus pada interaksi.
  })();
</script>
</body>
</html>
