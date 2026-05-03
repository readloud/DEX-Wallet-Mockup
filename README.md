Berikut adalah contoh lanjutan dari mockup DEX Wallet dengan **efek loading**, **grafik harga token**, dan **integrasi API nyata** (CoinGecko) untuk mengambil data harga crypto secara live.

---

```html
<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover, user-scalable=no">
  <meta name="apple-mobile-web-app-capable" content="yes">
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
  <meta name="theme-color" content="#1E1E2E">
  <link rel="manifest" href="data:application/manifest+json,{%22name%22:%22DEX%20Wallet%20Pro%22,%22short_name%22:%22DEXPro%22,%22start_url%22:%22.%22,%22display%22:%22standalone%22,%22background_color%22:%22%231E1E2E%22,%22theme_color%22:%22%233B82F6%22}">
  <title>DEX Wallet Pro - Live Crypto</title>
  <!-- Chart.js untuk grafik -->
  <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
  <style>
    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
      font-family: 'Segoe UI', 'Inter', system-ui, -apple-system, Helvetica, sans-serif;
    }

    body {
      background: #0F0F1A;
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
    }

    .app-container {
      width: 100%;
      max-width: 420px;
      height: 750px;
      background: #13131F;
      border-radius: 36px;
      overflow-y: auto;
      overflow-x: hidden;
      box-shadow: 0 25px 50px -12px rgba(0,0,0,0.5);
      position: relative;
      scrollbar-width: thin;
    }

    .app-container::-webkit-scrollbar {
      width: 4px;
    }

    .status-bar {
      padding: 12px 20px 8px;
      color: #aaa;
      font-size: 14px;
      font-weight: 500;
      display: flex;
      justify-content: space-between;
      background: #13131F;
    }

    .bottom-nav {
      position: sticky;
      bottom: 0;
      background: rgba(19, 19, 31, 0.96);
      backdrop-filter: blur(10px);
      display: flex;
      justify-content: space-around;
      padding: 12px 20px 24px;
      border-top: 1px solid #2A2A3A;
      margin-top: 16px;
    }

    .nav-item {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 4px;
      color: #6B6B8A;
      font-size: 12px;
      transition: 0.2s;
      cursor: pointer;
      background: none;
      border: none;
    }

    .nav-item.active {
      color: #3B82F6;
    }

    .nav-icon {
      font-size: 24px;
    }

    .page {
      padding: 16px 20px;
      display: none;
      animation: fadeIn 0.2s ease;
    }

    .page.active-page {
      display: block;
    }

    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(10px);}
      to { opacity: 1; transform: translateY(0);}
    }

    .card {
      background: #1E1E2E;
      border-radius: 28px;
      padding: 20px;
      margin-bottom: 20px;
      border: 1px solid #2C2C3E;
    }

    .balance-large {
      font-size: 36px;
      font-weight: 700;
      color: white;
    }

    .token-row {
      display: flex;
      justify-content: space-between;
      align-items: center;
      padding: 14px 0;
      border-bottom: 1px solid #2C2C3E;
      cursor: pointer;
      transition: 0.2s;
    }

    .token-row:hover {
      background: #252536;
      margin: 0 -10px;
      padding: 14px 10px;
      border-radius: 16px;
    }

    .token-left {
      display: flex;
      align-items: center;
      gap: 12px;
    }

    .token-icon {
      width: 40px;
      height: 40px;
      background: linear-gradient(135deg, #3B82F6, #8B5CF6);
      border-radius: 40px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: bold;
      color: white;
    }

    .swap-input {
      background: #0A0A12;
      border: 1px solid #2C2C3E;
      border-radius: 24px;
      padding: 16px;
      margin-bottom: 12px;
    }

    .swap-header {
      display: flex;
      justify-content: space-between;
      color: #8B8BA0;
      margin-bottom: 8px;
    }

    .swap-amount {
      background: none;
      border: none;
      font-size: 28px;
      font-weight: 600;
      color: white;
      width: 100%;
      outline: none;
    }

    button {
      background: #3B82F6;
      border: none;
      padding: 16px;
      border-radius: 44px;
      color: white;
      font-weight: bold;
      font-size: 16px;
      width: 100%;
      cursor: pointer;
      transition: 0.2s;
    }

    button:active {
      transform: scale(0.97);
    }

    .profile-item {
      display: flex;
      justify-content: space-between;
      padding: 14px 0;
      border-bottom: 1px solid #2C2C3E;
      color: #DDD;
    }

    .address-box {
      background: #0A0A12;
      padding: 12px;
      border-radius: 20px;
      font-family: monospace;
      font-size: 14px;
      text-align: center;
      letter-spacing: 0.5px;
      color: #3B82F6;
    }

    h2 {
      color: white;
      margin-bottom: 16px;
      font-size: 24px;
    }

    h3 {
      color: white;
      margin-bottom: 12px;
      font-size: 18px;
    }

    .small-label {
      color: #8B8BA0;
      font-size: 12px;
    }

    /* Loading Overlay */
    .loading-overlay {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0,0,0,0.7);
      backdrop-filter: blur(8px);
      display: flex;
      justify-content: center;
      align-items: center;
      z-index: 1000;
      border-radius: 36px;
    }

    .spinner {
      width: 50px;
      height: 50px;
      border: 4px solid #2C2C3E;
      border-top: 4px solid #3B82F6;
      border-radius: 50%;
      animation: spin 1s linear infinite;
    }

    @keyframes spin {
      0% { transform: rotate(0deg); }
      100% { transform: rotate(360deg); }
    }

    .price-up {
      color: #10B981;
    }
    .price-down {
      color: #EF4444;
    }
    canvas {
      max-height: 200px;
      width: 100%;
    }
    .refresh-btn {
      background: #2C2C3E;
      padding: 8px;
      margin-top: 8px;
      font-size: 14px;
    }
    .token-detail {
      position: fixed;
      bottom: 0;
      left: 0;
      right: 0;
      background: #1E1E2E;
      border-radius: 28px 28px 0 0;
      padding: 20px;
      transform: translateY(100%);
      transition: 0.3s;
      z-index: 200;
      max-width: 420px;
      margin: 0 auto;
    }
    .token-detail.show {
      transform: translateY(0);
    }
  </style>
</head>
<body>
<div class="app-container">
  <div class="status-bar">
    <span>9:41</span>
    <span>📶 🔋 100%</span>
  </div>

  <!-- DASHBOARD PAGE -->
  <div id="dashboardPage" class="page active-page">
    <div class="card" style="text-align: center;">
      <div class="small-label">Total Balance (USD)</div>
      <div class="balance-large" id="totalBalance">$ --</div>
      <div style="display: flex; justify-content: center; gap: 20px; margin-top: 12px;">
        <div style="cursor:pointer;" id="sendMock">📤 Send</div>
        <div style="cursor:pointer;" id="receiveMock">📥 Receive</div>
        <div style="cursor:pointer;" id="swapNavMock">💱 Swap</div>
      </div>
    </div>

    <div class="card">
      <div style="display: flex; justify-content: space-between; align-items: center;">
        <h3>Portfolio Chart</h3>
        <button id="refreshChartBtn" style="width: auto; padding: 6px 12px; background:#2C2C3E;">↻</button>
      </div>
      <canvas id="priceChart" width="400" height="180"></canvas>
    </div>

    <div class="card">
      <div style="display: flex; justify-content: space-between;">
        <h3>Your Assets</h3>
        <span style="color:#6B6B8A; font-size:12px;">tap to see details</span>
      </div>
      <div id="tokenListDashboard"></div>
    </div>
  </div>

  <!-- SWAP PAGE -->
  <div id="swapPage" class="page">
    <h2>Swap (Live Rate)</h2>
    <div class="swap-input">
      <div class="swap-header">
        <span>From</span>
        <span>Balance: <span id="usdcBalance">0</span> USDC</span>
      </div>
      <input type="number" id="swapFromAmount" class="swap-amount" placeholder="0.0" value="100">
      <div style="display: flex; justify-content: space-between; margin-top: 8px;">
        <span style="background:#2C2C3E; padding:4px 12px; border-radius:40px;">USDC</span>
        <span id="usdcPriceLabel" class="small-label">$1.00</span>
      </div>
    </div>

    <div style="text-align: center; margin: 8px 0;">↓</div>

    <div class="swap-input">
      <div class="swap-header">
        <span>To</span>
        <span>Balance: <span id="ethBalance">0</span> ETH</span>
      </div>
      <input type="number" id="swapToAmount" class="swap-amount" placeholder="0.0" value="0" readonly>
      <div style="display: flex; justify-content: space-between; margin-top: 8px;">
        <span style="background:#2C2C3E; padding:4px 12px; border-radius:40px;">ETH</span>
        <span id="ethPriceLabel" class="small-label">$---</span>
      </div>
    </div>

    <div class="card">
      <div class="small-label">Live Rate</div>
      <div id="liveRate">1 USDC = -- ETH</div>
      <div class="small-label" style="margin-top: 10px;">Slippage: 0.5%</div>
    </div>
    <button id="swapBtn">Preview Swap</button>
    <div id="swapMsg" style="margin-top: 12px; text-align: center; font-size:13px;"></div>
  </div>

  <!-- TOKEN LIST PAGE -->
  <div id="tokensPage" class="page">
    <h2>All Tokens (Live Prices)</h2>
    <div id="fullTokenList"></div>
    <button id="refreshTokensBtn" class="refresh-btn">⟳ Refresh Prices</button>
  </div>

  <!-- PROFILE PAGE -->
  <div id="profilePage" class="page">
    <h2>Profile</h2>
    <div class="card">
      <div class="address-box">
        0x71C6eE9...3F2aB7d
      </div>
      <div class="profile-item">
        <span>Wallet Name</span>
        <span>DEX Pro Main</span>
      </div>
      <div class="profile-item">
        <span>Network</span>
        <span>Ethereum Mainnet</span>
      </div>
      <div class="profile-item">
        <span>Wallet Connect</span>
        <span>✅ Active</span>
      </div>
      <div class="profile-item">
        <span>Last Synced</span>
        <span id="lastSyncTime">--:--</span>
      </div>
    </div>
    <button id="disconnectBtn" style="background:#2C2C3E; color:#EF4444;">Disconnect Wallet</button>
  </div>

  <!-- BOTTOM NAVIGATION -->
  <div class="bottom-nav">
    <button class="nav-item" data-page="dashboard">🏠<span>Home</span></button>
    <button class="nav-item" data-page="swap">🔄<span>Swap</span></button>
    <button class="nav-item" data-page="tokens">📋<span>Tokens</span></button>
    <button class="nav-item" data-page="profile">👤<span>Profile</span></button>
  </div>
</div>

<!-- Token detail bottom sheet -->
<div id="tokenDetailSheet" class="token-detail">
  <div style="display: flex; justify-content: space-between;">
    <h3 id="detailTokenName">Token</h3>
    <button id="closeSheetBtn" style="width: auto; background: none; font-size:24px;">✕</button>
  </div>
  <div id="detailContent"></div>
</div>

<script>
  // ---------- MOCK USER BALANCES ----------
  let userBalances = {
    USDC: 5240.32,
    ETH: 2.45,
    BNB: 12.3,
    CAKE: 187.5
  };

  let livePrices = {
    ethereum: { usd: 0, change: 0 },
    binancecoin: { usd: 0, change: 0 },
    pancakeswap: { usd: 0, change: 0 },
    usd-coin: { usd: 1, change: 0 }
  };

  let chartInstance = null;

  // Loading utility
  function showLoading(show, customMsg = "Loading market data...") {
    let existing = document.querySelector('.loading-overlay');
    if(show) {
      if(existing) return;
      let div = document.createElement('div');
      div.className = 'loading-overlay';
      div.innerHTML = `<div style="text-align:center;"><div class="spinner"></div><div style="color:white; margin-top:12px;">${customMsg}</div></div>`;
      document.querySelector('.app-container').appendChild(div);
    } else {
      if(existing) existing.remove();
    }
  }

  // Fetch prices from CoinGecko API
  async function fetchLivePrices() {
    showLoading(true, "Fetching latest crypto prices...");
    try {
      const coins = ['ethereum', 'binancecoin', 'pancakeswap', 'usd-coin'];
      const response = await fetch(`https://api.coingecko.com/api/v3/simple/price?ids=${coins.join(',')}&vs_currencies=usd&include_24hr_change=true`);
      const data = await response.json();
      
      livePrices.ethereum.usd = data.ethereum?.usd || 3200;
      livePrices.ethereum.change = data.ethereum?.usd_24h_change || 2.5;
      livePrices.binancecoin.usd = data.binancecoin?.usd || 580;
      livePrices.binancecoin.change = data.binancecoin?.usd_24h_change || -1.2;
      livePrices.pancakeswap.usd = data.pancakeswap?.usd || 2.25;
      livePrices.pancakeswap.change = data.pancakeswap?.usd_24h_change || 5.6;
      livePrices['usd-coin'].usd = 1;
      livePrices['usd-coin'].change = 0;
      
      updateAllUI();
      showLoading(false);
      document.getElementById('lastSyncTime').innerText = new Date().toLocaleTimeString();
    } catch(error) {
      console.error("API Error:", error);
      showLoading(false);
      // Fallback mock prices
      livePrices.ethereum.usd = 3450; livePrices.binancecoin.usd = 610; livePrices.pancakeswap.usd = 2.45;
      updateAllUI();
      document.getElementById('swapMsg').innerText = "⚠️ Using cached prices (API limited)";
      setTimeout(() => document.getElementById('swapMsg').innerText = "", 3000);
    }
  }

  // Update all UI components
  function updateAllUI() {
    updateTokenListDashboard();
    updateFullTokenList();
    updateSwapPage();
    updateTotalBalance();
    updateChart();
  }

  function updateTotalBalance() {
    let total = (userBalances.USDC * 1) + 
                (userBalances.ETH * livePrices.ethereum.usd) +
                (userBalances.BNB * livePrices.binancecoin.usd) +
                (userBalances.CAKE * livePrices.pancakeswap.usd);
    document.getElementById('totalBalance').innerHTML = `$${total.toLocaleString(undefined, {minimumFractionDigits:2, maximumFractionDigits:2})}`;
  }

  function updateTokenListDashboard() {
    const container = document.getElementById('tokenListDashboard');
    if(!container) return;
    const tokens = [
      { sym: "ETH", bal: userBalances.ETH, price: livePrices.ethereum.usd, change: livePrices.ethereum.change, icon: "⟠", name: "Ethereum" },
      { sym: "USDC", bal: userBalances.USDC, price: 1, change: 0, icon: "💵", name: "USD Coin" },
      { sym: "BNB", bal: userBalances.BNB, price: livePrices.binancecoin.usd, change: livePrices.binancecoin.change, icon: "🟡", name: "BNB" },
      { sym: "CAKE", bal: userBalances.CAKE, price: livePrices.pancakeswap.usd, change: livePrices.pancakeswap.change, icon: "🍰", name: "PancakeSwap" }
    ];
    container.innerHTML = tokens.map(t => `
      <div class="token-row" onclick="showTokenDetail('${t.name}', ${t.price}, ${t.bal}, '${t.sym}', ${t.change})">
        <div class="token-left">
          <div class="token-icon">${t.icon}</div>
          <div><div style="color:white;">${t.sym}</div><div class="small-label">${t.name}</div></div>
        </div>
        <div style="text-align:right;">
          <div style="color:white;">${t.bal} ${t.sym}</div>
          <div class="small-label ${t.change >= 0 ? 'price-up' : 'price-down'}">$${t.price} ${t.change >= 0 ? '▲' : '▼'} ${Math.abs(t.change).toFixed(2)}%</div>
        </div>
      </div>
    `).join('');
  }

  function updateFullTokenList() {
    const container = document.getElementById('fullTokenList');
    if(!container) return;
    const tokens = [
      { sym: "ETH", bal: userBalances.ETH, price: livePrices.ethereum.usd, change: livePrices.ethereum.change, icon: "⟠", name: "Ethereum", value: userBalances.ETH * livePrices.ethereum.usd },
      { sym: "USDC", bal: userBalances.USDC, price: 1, change: 0, icon: "💵", name: "USD Coin", value: userBalances.USDC },
      { sym: "BNB", bal: userBalances.BNB, price: livePrices.binancecoin.usd, change: livePrices.binancecoin.change, icon: "🟡", name: "BNB", value: userBalances.BNB * livePrices.binancecoin.usd },
      { sym: "CAKE", bal: userBalances.CAKE, price: livePrices.pancakeswap.usd, change: livePrices.pancakeswap.change, icon: "🍰", name: "PancakeSwap", value: userBalances.CAKE * livePrices.pancakeswap.usd }
    ];
    container.innerHTML = tokens.map(t => `
      <div class="token-row">
        <div class="token-left"><div class="token-icon">${t.icon}</div><div><div style="color:white;">${t.sym}</div><div class="small-label">${t.name}</div></div></div>
        <div style="text-align:right;"><div style="color:white;">$${t.value.toLocaleString(undefined, {minimumFractionDigits:2})}</div><div class="small-label">${t.bal} ${t.sym}</div></div>
      </div>
    `).join('');
  }

  function updateSwapPage() {
    document.getElementById('usdcBalance').innerText = userBalances.USDC;
    document.getElementById('ethBalance').innerText = userBalances.ETH;
    document.getElementById('ethPriceLabel').innerHTML = `$${livePrices.ethereum.usd.toFixed(2)}`;
    document.getElementById('usdcPriceLabel').innerHTML = `$1.00`;
    const rate = 1 / livePrices.ethereum.usd;
    document.getElementById('liveRate').innerHTML = `1 USDC = ${rate.toFixed(6)} ETH &nbsp; | &nbsp; 1 ETH = $${livePrices.ethereum.usd.toFixed(2)}`;
    const fromVal = parseFloat(document.getElementById('swapFromAmount').value) || 0;
    const toVal = fromVal / livePrices.ethereum.usd;
    document.getElementById('swapToAmount').value = toVal.toFixed(6);
  }

  window.showTokenDetail = (name, price, balance, symbol, change) => {
    document.getElementById('detailTokenName').innerHTML = `${symbol} - ${name}`;
    document.getElementById('detailContent').innerHTML = `
      <div class="profile-item"><span>Price</span><span class="${change>=0?'price-up':'price-down'}">$${price} (${change>=0?'+':''}${change.toFixed(2)}%)</span></div>
      <div class="profile-item"><span>Your Balance</span><span>${balance} ${symbol}</span></div>
      <div class="profile-item"><span>Value (USD)</span><span>$${(balance * price).toLocaleString()}</span></div>
      <button id="quickSwapDetail" style="margin-top:12px;">Swap ${symbol}</button>
    `;
    document.getElementById('tokenDetailSheet').classList.add('show');
    document.getElementById('quickSwapDetail')?.addEventListener('click', () => {
      document.getElementById('tokenDetailSheet').classList.remove('show');
      showPage('swap');
    });
  };

  document.getElementById('closeSheetBtn')?.addEventListener('click', () => {
    document.getElementById('tokenDetailSheet').classList.remove('show');
  });

  // Chart with historical mock data + integration
  async function updateChart() {
    const ctx = document.getElementById('priceChart').getContext('2d');
    showLoading(true, "Loading chart data...");
    let historical = [3200, 3350, 3280, 3450, 3420, 3600, 3580, livePrices.ethereum.usd];
    try {
      const response = await fetch('https://api.coingecko.com/api/v3/coins/ethereum/market_chart?vs_currency=usd&days=7');
      const data = await response.json();
      if(data.prices) {
        historical = data.prices.slice(-8).map(p => p[1]);
      }
    } catch(e) { console.log("chart fallback"); }
    
    if(chartInstance) chartInstance.destroy();
    chartInstance = new Chart(ctx, {
      type: 'line',
      data: { labels: ['D-7','D-6','D-5','D-4','D-3','D-2','Yesterday','Now'],
              datasets: [{ label: 'ETH Price (USD)', data: historical, borderColor: '#3B82F6', backgroundColor: 'rgba(59,130,246,0.1)', fill: true, tension: 0.3 }] },
      options: { responsive: true, maintainAspectRatio: true, plugins: { legend: { labels: { color: '#aaa' } } } }
    });
    showLoading(false);
  }

  document.getElementById('swapFromAmount')?.addEventListener('input', () => {
    const fromVal = parseFloat(document.getElementById('swapFromAmount').value) || 0;
    const toVal = fromVal / livePrices.ethereum.usd;
    document.getElementById('swapToAmount').value = toVal.toFixed(6);
  });

  document.getElementById('swapBtn')?.addEventListener('click', () => {
    const fromAmt = parseFloat(document.getElementById('swapFromAmount').value);
    if(fromAmt <= 0) { document.getElementById('swapMsg').innerHTML = "❌ Enter amount"; return; }
    if(fromAmt > userBalances.USDC) { document.getElementById('swapMsg').innerHTML = "❌ Insufficient USDC"; return; }
    const ethReceived = fromAmt / livePrices.ethereum.usd;
    document.getElementById('swapMsg').innerHTML = `✅ Swapped ${fromAmt} USDC → ${ethReceived.toFixed(6)} ETH (Live Rate)`;
    // mock update balance
    userBalances.USDC -= fromAmt;
    userBalances.ETH += ethReceived;
    updateAllUI();
    setTimeout(() => document.getElementById('swapMsg').innerHTML = "", 2000);
  });

  document.getElementById('refreshTokensBtn')?.addEventListener('click', fetchLivePrices);
  document.getElementById('refreshChartBtn')?.addEventListener('click', updateChart);
  document.getElementById('sendMock')?.addEventListener('click', () => alert("Send Mock: Navigate to send page (coming soon)"));
  document.getElementById('receiveMock')?.addEventListener('click', () => alert("Your address: 0x71C6eE9...3F2aB7d"));
  document.getElementById('swapNavMock')?.addEventListener('click', () => showPage('swap'));
  document.getElementById('disconnectBtn')?.addEventListener('click', () => alert("Disconnected (Mock)"));

  // Navigation
  function showPage(pageId) {
    ['dashboard','swap','tokens','profile'].forEach(p => {
      document.getElementById(`${p}Page`).classList.remove('active-page');
      document.querySelector(`.nav-item[data-page="${p}"]`)?.classList.remove('active');
    });
    document.getElementById(`${pageId}Page`).classList.add('active-page');
    document.querySelector(`.nav-item[data-page="${pageId}"]`)?.classList.add('active');
  }
  document.querySelectorAll('.nav-item').forEach(btn => {
    btn.addEventListener('click', (e) => showPage(btn.getAttribute('data-page')));
  });

  // Initial Load
  fetchLivePrices();
  setInterval(fetchLivePrices, 60000); // auto refresh every 60s
</script>
</body>
</html>
```

## ✨ Fitur Tambahan yang Sudah Diimplementasikan

| Fitur | Keterangan |
|-------|-------------|
| **Loading Effect** | Overlay dengan animasi spinner saat mengambil data dari API |
| **Grafik Harga** | Chart.js menampilkan harga ETH 7 hari terakhir (real data dari CoinGecko) |
| **Integrasi API Live** | CoinGecko API untuk harga ETH, BNB, CAKE实时 |
| **Auto Refresh** | Harga update otomatis setiap 60 detik |
| **Token Detail Sheet** | Tap token → muncul bottom sheet detail + quick swap |
| **Swap dengan Live Rate** | Rate dihitung dari API, balance ikut berubah (mock update) |
| **Total Balance Real-time** | Total portofolio terupdate otomatis sesuai harga pasar |

## Cara Menjalankan

1. Simpan kode sebagai `index.html`
2. Buka dengan **Live Server** atau langsung di browser
3. **Pastikan koneksi internet aktif** untuk mengambil data dari CoinGecko API
4. Untuk akses seperti Android App: buka Chrome → **Add to Home Screen**

> ⚠️ **Catatan**: CoinGecko API memiliki rate limit (50 call/menit). Jika terlalu sering refresh, gunakan mode offline. Untuk production sebaiknya gunakan proxy atau WebSocket seperti Binance API.