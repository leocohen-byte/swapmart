<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SwapMart Live — Driftline Demo</title>
<link href="https://fonts.googleapis.com/css2?family=IBM+Plex+Mono:wght@400;500;600&family=Syne:wght@400;600;700;800&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0a0a0f;
    --surface: #111118;
    --surface2: #1a1a24;
    --border: #1e1e2e;
    --border2: #2a2a3e;
    --text: #e8e8f0;
    --text2: #a0a0b8;
    --text3: #5a5a7a;
    --accent: #5b6ef5;
    --accent2: #7c8cff;
    --green: #10d9a0;
    --red: #ff4560;
    --orange: #ff8c42;
    --yellow: #ffd166;
    --purple: #a78bfa;
  }
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body {
    font-family: 'Syne', sans-serif;
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* ── HEADER ── */
  .header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 16px 32px;
    border-bottom: 1px solid var(--border);
    background: rgba(10,10,15,0.95);
    position: sticky;
    top: 0;
    z-index: 100;
    backdrop-filter: blur(12px);
  }
  .logo {
    display: flex;
    align-items: center;
    gap: 20px;
  }
  .logo-swapmart { font-size: 20px; font-weight: 800; color: var(--text); letter-spacing: -0.5px; }
  .logo-divider { width: 1px; height: 20px; background: var(--border2); }
  .logo-badge {
    font-family: 'IBM Plex Mono', monospace;
    font-size: 10px;
    font-weight: 600;
    color: var(--accent2);
    background: rgba(91,110,245,0.12);
    border: 1px solid rgba(91,110,245,0.2);
    padding: 4px 10px;
    border-radius: 4px;
    letter-spacing: 0.08em;
    text-transform: uppercase;
  }
  .header-right { display: flex; align-items: center; gap: 16px; }
  .live-indicator {
    display: flex;
    align-items: center;
    gap: 6px;
    font-family: 'IBM Plex Mono', monospace;
    font-size: 11px;
    color: var(--green);
  }
  .live-dot {
    width: 6px; height: 6px;
    background: var(--green);
    border-radius: 50%;
    animation: pulse 2s infinite;
  }
  @keyframes pulse {
    0%, 100% { opacity: 1; transform: scale(1); }
    50% { opacity: 0.5; transform: scale(0.8); }
  }
  .powered-by {
    font-family: 'IBM Plex Mono', monospace;
    font-size: 10px;
    color: var(--text3);
  }
  .powered-by span { color: var(--accent2); }

  /* ── LAYOUT ── */
  .layout {
    display: grid;
    grid-template-columns: 1fr 380px 380px;
    gap: 0;
    height: calc(100vh - 61px);
  }

  /* ── MARKETPLACE PANEL ── */
  .panel {
    border-right: 1px solid var(--border);
    display: flex;
    flex-direction: column;
    overflow: hidden;
  }
  .panel-header {
    padding: 18px 24px;
    border-bottom: 1px solid var(--border);
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-shrink: 0;
  }
  .panel-title { font-size: 13px; font-weight: 700; color: var(--text2); text-transform: uppercase; letter-spacing: 0.12em; }
  .panel-count {
    font-family: 'IBM Plex Mono', monospace;
    font-size: 11px;
    color: var(--text3);
    background: var(--surface2);
    border: 1px solid var(--border);
    padding: 3px 10px;
    border-radius: 4px;
  }

  /* ── LISTINGS ── */
  .listings { overflow-y: auto; flex: 1; padding: 16px; }
  .listing {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 12px;
    padding: 16px;
    margin-bottom: 10px;
    cursor: pointer;
    transition: all 0.2s;
    position: relative;
  }
  .listing:hover { border-color: var(--border2); transform: translateY(-1px); }
  .listing.active { border-color: var(--accent); background: rgba(91,110,245,0.06); }
  .listing-top { display: flex; align-items: flex-start; justify-content: space-between; margin-bottom: 8px; }
  .listing-name { font-size: 14px; font-weight: 700; color: var(--text); }
  .listing-price { font-family: 'IBM Plex Mono', monospace; font-size: 13px; font-weight: 600; color: var(--green); }
  .listing-seller {
    font-family: 'IBM Plex Mono', monospace;
    font-size: 11px;
    color: var(--text3);
    display: flex;
    align-items: center;
    gap: 6px;
  }
  .seller-dot { width: 5px; height: 5px; border-radius: 50%; background: var(--green); }
  .listing-desc { font-size: 12px; color: var(--text2); margin-top: 6px; line-height: 1.4; }
  .listing-badge {
    position: absolute;
    top: 12px;
    right: 12px;
    font-family: 'IBM Plex Mono', monospace;
    font-size: 9px;
    font-weight: 600;
    padding: 3px 8px;
    border-radius: 3px;
    text-transform: uppercase;
    letter-spacing: 0.06em;
  }
  .badge-flagged { background: rgba(255,69,96,0.15); color: var(--red); border: 1px solid rgba(255,69,96,0.25); }
  .badge-suspicious { background: rgba(255,140,66,0.15); color: var(--orange); border: 1px solid rgba(255,140,66,0.25); }
  .badge-clean { background: rgba(16,217,160,0.1); color: var(--green); border: 1px solid rgba(16,217,160,0.2); }

  /* ── CHAT ── */
  .chat-area { display: flex; flex-direction: column; height: 100%; }
  .chat-header {
    padding: 16px 24px;
    border-bottom: 1px solid var(--border);
    flex-shrink: 0;
  }
  .chat-with { font-size: 14px; font-weight: 700; color: var(--text); }
  .chat-listing-name { font-size: 12px; color: var(--text3); margin-top: 2px; font-family: 'IBM Plex Mono', monospace; }
  .chat-empty {
    flex: 1;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    color: var(--text3);
    font-size: 13px;
    gap: 8px;
  }
  .chat-empty-icon { font-size: 32px; opacity: 0.3; }
  .messages {
    flex: 1;
    overflow-y: auto;
    padding: 16px;
    display: flex;
    flex-direction: column;
    gap: 10px;
  }
  .msg {
    display: flex;
    flex-direction: column;
    max-width: 80%;
    animation: fadeUp 0.2s ease;
  }
  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(6px); }
    to { opacity: 1; transform: translateY(0); }
  }
  .msg.you { align-self: flex-end; }
  .msg.them { align-self: flex-start; }
  .msg-bubble {
    padding: 10px 14px;
    border-radius: 12px;
    font-size: 13px;
    line-height: 1.4;
  }
  .msg.you .msg-bubble { background: var(--accent); color: #fff; border-bottom-right-radius: 4px; }
  .msg.them .msg-bubble { background: var(--surface2); color: var(--text); border-bottom-left-radius: 4px; border: 1px solid var(--border2); }
  .msg-meta {
    font-family: 'IBM Plex Mono', monospace;
    font-size: 10px;
    color: var(--text3);
    margin-top: 3px;
    padding: 0 4px;
  }
  .msg.you .msg-meta { text-align: right; }
  .chat-input-area {
    padding: 16px;
    border-top: 1px solid var(--border);
    display: flex;
    gap: 10px;
    flex-shrink: 0;
  }
  .chat-input {
    flex: 1;
    background: var(--surface2);
    border: 1px solid var(--border2);
    border-radius: 10px;
    padding: 10px 14px;
    font-size: 13px;
    color: var(--text);
    font-family: 'Syne', sans-serif;
    outline: none;
    transition: border-color 0.2s;
  }
  .chat-input:focus { border-color: var(--accent); }
  .send-btn {
    background: var(--accent);
    color: #fff;
    border: none;
    border-radius: 10px;
    padding: 10px 18px;
    font-size: 13px;
    font-weight: 700;
    cursor: pointer;
    font-family: 'Syne', sans-serif;
    transition: all 0.2s;
  }
  .send-btn:hover { background: var(--accent2); transform: scale(1.03); }

  /* ── DRIFTLINE PANEL ── */
  .drift-panel { display: flex; flex-direction: column; overflow: hidden; border-right: none; }
  .drift-header {
    padding: 18px 24px;
    border-bottom: 1px solid var(--border);
    background: rgba(91,110,245,0.04);
    flex-shrink: 0;
  }
  .drift-title-row { display: flex; align-items: center; justify-content: space-between; }
  .drift-logo { display: flex; align-items: center; gap: 8px; }
  .drift-logo-dot { width: 8px; height: 8px; background: var(--accent); border-radius: 50%; box-shadow: 0 0 8px rgba(91,110,245,0.6); }
  .drift-logo-text { font-size: 14px; font-weight: 800; color: var(--text); }
  .drift-subtitle { font-family: 'IBM Plex Mono', monospace; font-size: 10px; color: var(--text3); margin-top: 4px; }
  .drift-stats { display: grid; grid-template-columns: 1fr 1fr; gap: 8px; padding: 12px 24px; border-bottom: 1px solid var(--border); flex-shrink: 0; }
  .drift-stat { background: var(--surface2); border: 1px solid var(--border); border-radius: 8px; padding: 10px 12px; }
  .drift-stat-val { font-size: 22px; font-weight: 800; line-height: 1; }
  .drift-stat-label { font-family: 'IBM Plex Mono', monospace; font-size: 9px; color: var(--text3); text-transform: uppercase; letter-spacing: 0.1em; margin-top: 3px; }
  .drift-feed { flex: 1; overflow-y: auto; padding: 12px; }
  .drift-event {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 10px;
    padding: 12px 14px;
    margin-bottom: 8px;
    animation: slideIn 0.3s ease;
    position: relative;
    overflow: hidden;
  }
  @keyframes slideIn {
    from { opacity: 0; transform: translateX(16px); }
    to { opacity: 1; transform: translateX(0); }
  }
  .drift-event::before {
    content: '';
    position: absolute;
    left: 0; top: 0; bottom: 0;
    width: 3px;
    border-radius: 2px 0 0 2px;
  }
  .drift-event.critical::before { background: var(--red); }
  .drift-event.high::before { background: var(--orange); }
  .drift-event.medium::before { background: var(--yellow); }
  .drift-event.low::before { background: var(--green); }
  .drift-event.critical { border-color: rgba(255,69,96,0.2); background: rgba(255,69,96,0.04); }
  .drift-event.high { border-color: rgba(255,140,66,0.2); }
  .drift-event.medium { border-color: rgba(255,209,102,0.15); }
  .de-top { display: flex; align-items: center; justify-content: space-between; margin-bottom: 6px; }
  .de-user { font-family: 'IBM Plex Mono', monospace; font-size: 12px; font-weight: 600; color: var(--text); }
  .de-score {
    font-family: 'IBM Plex Mono', monospace;
    font-size: 16px;
    font-weight: 700;
    line-height: 1;
  }
  .score-critical { color: var(--red); }
  .score-high { color: var(--orange); }
  .score-medium { color: var(--yellow); }
  .score-low { color: var(--green); }
  .de-signal { font-size: 11px; color: var(--text2); margin-bottom: 4px; }
  .de-rec {
    font-family: 'IBM Plex Mono', monospace;
    font-size: 10px;
    padding: 3px 8px;
    border-radius: 4px;
    display: inline-block;
    margin-top: 4px;
  }
  .rec-critical { background: rgba(255,69,96,0.15); color: var(--red); }
  .rec-high { background: rgba(255,140,66,0.15); color: var(--orange); }
  .rec-medium { background: rgba(255,209,102,0.1); color: var(--yellow); }
  .rec-low { background: rgba(16,217,160,0.1); color: var(--green); }
  .de-time { font-family: 'IBM Plex Mono', monospace; font-size: 9px; color: var(--text3); margin-top: 5px; }

  /* ── BOT CONTROL ── */
  .bot-controls {
    padding: 12px 24px;
    border-top: 1px solid var(--border);
    background: rgba(10,10,15,0.8);
    flex-shrink: 0;
  }
  .bot-controls-label { font-family: 'IBM Plex Mono', monospace; font-size: 10px; color: var(--text3); text-transform: uppercase; letter-spacing: 0.1em; margin-bottom: 8px; }
  .bot-btns { display: flex; gap: 8px; flex-wrap: wrap; }
  .bot-btn {
    padding: 7px 14px;
    border-radius: 7px;
    font-size: 12px;
    font-weight: 700;
    cursor: pointer;
    border: none;
    font-family: 'Syne', sans-serif;
    transition: all 0.2s;
  }
  .bot-btn:hover { transform: scale(1.04); }
  .btn-spam { background: rgba(255,69,96,0.15); color: var(--red); border: 1px solid rgba(255,69,96,0.3); }
  .btn-spam:hover { background: rgba(255,69,96,0.25); }
  .btn-bot { background: rgba(255,140,66,0.15); color: var(--orange); border: 1px solid rgba(255,140,66,0.3); }
  .btn-bot:hover { background: rgba(255,140,66,0.25); }
  .btn-normal { background: rgba(16,217,160,0.1); color: var(--green); border: 1px solid rgba(16,217,160,0.2); }
  .btn-normal:hover { background: rgba(16,217,160,0.2); }
  .btn-reset { background: var(--surface2); color: var(--text3); border: 1px solid var(--border2); }

  /* ── API CALL TICKER ── */
  .api-ticker {
    padding: 8px 24px;
    border-top: 1px solid var(--border);
    font-family: 'IBM Plex Mono', monospace;
    font-size: 10px;
    color: var(--text3);
    background: var(--surface);
    flex-shrink: 0;
    overflow: hidden;
    white-space: nowrap;
  }
  .api-call { color: var(--accent2); }
  .api-ok { color: var(--green); }

  /* ── SCROLLBARS ── */
  ::-webkit-scrollbar { width: 4px; }
  ::-webkit-scrollbar-track { background: transparent; }
  ::-webkit-scrollbar-thumb { background: var(--border2); border-radius: 2px; }

  /* ── TOAST ── */
  .toast-container { position: fixed; top: 80px; right: 24px; z-index: 999; display: flex; flex-direction: column; gap: 8px; }
  .toast {
    background: var(--surface);
    border: 1px solid var(--border2);
    border-radius: 10px;
    padding: 12px 16px;
    max-width: 300px;
    animation: toastIn 0.3s ease;
    font-size: 13px;
  }
  @keyframes toastIn {
    from { opacity: 0; transform: translateX(20px); }
    to { opacity: 1; transform: translateX(0); }
  }
  .toast.critical { border-color: rgba(255,69,96,0.4); background: rgba(255,69,96,0.08); }
  .toast-title { font-weight: 700; margin-bottom: 3px; }
  .toast.critical .toast-title { color: var(--red); }
  .toast-body { font-size: 12px; color: var(--text2); }

  /* ── SUSPENDED OVERLAY ── */
  .suspended-tag {
    font-family: 'IBM Plex Mono', monospace;
    font-size: 9px;
    background: rgba(255,69,96,0.2);
    color: var(--red);
    border: 1px solid rgba(255,69,96,0.3);
    padding: 2px 6px;
    border-radius: 3px;
    margin-left: 6px;
    text-transform: uppercase;
  }
</style>
</head>
<body>

<div class="header">
  <div class="logo">
    <div class="logo-swapmart">SwapMart</div>
    <div class="logo-divider"></div>
    <div class="logo-badge">Driftline Demo</div>
  </div>
  <div class="header-right">
    <div class="live-indicator"><div class="live-dot"></div>Live</div>
    <div class="powered-by">fraud detection by <span>Driftline</span></div>
  </div>
</div>

<div class="layout">

  <!-- LISTINGS PANEL -->
  <div class="panel">
    <div class="panel-header">
      <div class="panel-title">Marketplace Listings</div>
      <div class="panel-count" id="listing-count">6 listings</div>
    </div>
    <div class="listings" id="listings-area"></div>
  </div>

  <!-- CHAT PANEL -->
  <div class="panel" style="border-right:1px solid var(--border)">
    <div class="panel-header">
      <div class="panel-title">Messages</div>
      <div id="chat-status" class="panel-count">select a listing</div>
    </div>
    <div class="chat-area" id="chat-area">
      <div class="chat-empty" id="chat-empty">
        <div class="chat-empty-icon">💬</div>
        <div>Select a listing to start chatting</div>
        <div style="font-size:11px;margin-top:4px;font-family:'IBM Plex Mono',monospace;color:var(--text3)">Driftline scores every message in real time</div>
      </div>
    </div>
  </div>

  <!-- DRIFTLINE PANEL -->
  <div class="drift-panel">
    <div class="drift-header">
      <div class="drift-title-row">
        <div class="drift-logo">
          <div class="drift-logo-dot"></div>
          <div class="drift-logo-text">Driftline</div>
        </div>
        <div class="live-indicator"><div class="live-dot"></div>scanning</div>
      </div>
      <div class="drift-subtitle">Real-time fraud intelligence · metadata only, no content read</div>
    </div>
    <div class="drift-stats">
      <div class="drift-stat">
        <div class="drift-stat-val" id="stat-events" style="color:var(--accent2)">0</div>
        <div class="drift-stat-label">Events Scored</div>
      </div>
      <div class="drift-stat">
        <div class="drift-stat-val" id="stat-flagged" style="color:var(--red)">0</div>
        <div class="drift-stat-label">Flagged Users</div>
      </div>
      <div class="drift-stat">
        <div class="drift-stat-val" id="stat-blocked" style="color:var(--orange)">0</div>
        <div class="drift-stat-label">Suspended</div>
      </div>
      <div class="drift-stat">
        <div class="drift-stat-val" id="stat-accuracy" style="color:var(--green)">100%</div>
        <div class="drift-stat-label">Accuracy</div>
      </div>
    </div>
    <div class="drift-feed" id="drift-feed">
      <div style="text-align:center;padding:40px 20px;color:var(--text3);font-family:'IBM Plex Mono',monospace;font-size:12px">
        Waiting for messaging activity...<br><br>
        <span style="font-size:11px;color:var(--text3);opacity:0.6">Use the controls below to simulate fraud</span>
      </div>
    </div>
    <div class="api-ticker" id="api-ticker">Ready — waiting for events</div>
    <div class="bot-controls">
      <div class="bot-controls-label">🎭 Simulate Attack</div>
      <div class="bot-btns">
        <button class="bot-btn btn-spam" onclick="launchSpamBot()">🤖 Mass Spam Bot</button>
        <button class="bot-btn btn-bot" onclick="launchCopyPasteBot()">📋 Copy-Paste Scammer</button>
        <button class="bot-btn btn-normal" onclick="launchNormalUser()">👤 Normal User</button>
        <button class="bot-btn btn-reset" onclick="resetDemo()">↺ Reset</button>
      </div>
    </div>
  </div>

</div>

<div class="toast-container" id="toasts"></div>

<script>
// ── STATE ──────────────────────────────────────────────────────────────────
const API = 'https://driftline-api-production.up.railway.app';
const DEMO_KEY = 'dk_alpha_marketplace_001';

const state = {
  events: 0,
  flagged: new Set(),
  suspended: new Set(),
  activeListing: null,
  messages: {},
  userScores: {},
  botInterval: null
};

const listings = [
  { id: 'l1', name: 'Vintage Levi\'s Jacket', price: '$85', seller: 'alex_m', desc: 'Size M, barely worn. Perfect condition. Pickup or ship.', status: 'clean' },
  { id: 'l2', name: 'Sony WH-1000XM5 Headphones', price: '$220', seller: 'techreseller', desc: 'Barely used, 2 months old. Original box included.', status: 'clean' },
  { id: 'l3', name: 'MacBook Pro 2022 M2', price: '$1,100', seller: 'mike_sells', desc: '13", 8GB RAM, 256GB SSD. Apple warranty until Dec 2025.', status: 'clean' },
  { id: 'l4', name: 'IKEA Kallax Shelf', price: '$45', seller: 'moving_out_99', desc: 'White, 4x4 grid. Must pick up this weekend. Great condition.', status: 'clean' },
  { id: 'l5', name: 'Canon EOS R50 Camera Kit', price: '$680', seller: 'photobuyer', desc: 'Body only, 1200 shutter count. Clean sensor, no scratches.', status: 'clean' },
  { id: 'l6', name: 'Patagonia Fleece Pullover', price: '$55', seller: 'outdoor_gear_x', desc: 'Size L, washed once. Classic retro-X style.', status: 'clean' },
];

// ── RENDER LISTINGS ────────────────────────────────────────────────────────
function renderListings() {
  const area = document.getElementById('listings-area');
  area.innerHTML = listings.map(l => {
    const score = state.userScores[l.seller] || 0;
    const suspended = state.suspended.has(l.seller);
    let badgeClass = 'badge-clean', badgeText = 'Clean';
    if (suspended) { badgeClass = 'badge-flagged'; badgeText = 'Suspended'; }
    else if (score >= 75) { badgeClass = 'badge-flagged'; badgeText = 'Critical'; }
    else if (score >= 35) { badgeClass = 'badge-suspicious'; badgeText = 'Suspicious'; }

    return `
      <div class="listing ${state.activeListing === l.id ? 'active' : ''}" onclick="selectListing('${l.id}')">
        <div class="listing-badge ${badgeClass}">${badgeText}</div>
        <div class="listing-top">
          <div class="listing-name">${l.name}</div>
          <div class="listing-price">${l.price}</div>
        </div>
        <div class="listing-seller">
          <div class="seller-dot" style="background:${suspended ? 'var(--red)' : 'var(--green)'}"></div>
          ${l.seller}${suspended ? '<span class="suspended-tag">suspended</span>' : ''}
          ${score > 0 ? `<span style="margin-left:auto;font-family:'IBM Plex Mono',monospace;font-size:10px;color:${score >= 75 ? 'var(--red)' : score >= 35 ? 'var(--orange)' : 'var(--green)'}">risk: ${score}</span>` : ''}
        </div>
        <div class="listing-desc">${l.desc}</div>
      </div>
    `;
  }).join('');
}

// ── SELECT LISTING ─────────────────────────────────────────────────────────
function selectListing(id) {
  state.activeListing = id;
  const listing = listings.find(l => l.id === id);
  if (!state.messages[id]) state.messages[id] = [];

  document.getElementById('chat-status').textContent = listing.seller;

  const chatArea = document.getElementById('chat-area');
  chatArea.innerHTML = `
    <div class="chat-header">
      <div class="chat-with">${listing.seller}</div>
      <div class="chat-listing-name">${listing.name}</div>
    </div>
    <div class="messages" id="msgs-${id}"></div>
    <div class="chat-input-area">
      <input class="chat-input" id="chat-input" placeholder="Ask about this item..." onkeydown="if(event.key==='Enter')sendMsg()">
      <button class="send-btn" onclick="sendMsg()">Send</button>
    </div>
  `;
  renderMessages(id);
  renderListings();
}

function renderMessages(id) {
  const container = document.getElementById('msgs-' + id);
  if (!container) return;
  container.innerHTML = (state.messages[id] || []).map(m => `
    <div class="msg ${m.from}">
      <div class="msg-bubble">${m.text}</div>
      <div class="msg-meta">${m.time}</div>
    </div>
  `).join('');
  container.scrollTop = container.scrollHeight;
}

function sendMsg() {
  const input = document.getElementById('chat-input');
  if (!input || !input.value.trim() || !state.activeListing) return;
  const listing = listings.find(l => l.id === state.activeListing);
  const text = input.value.trim();
  input.value = '';

  addMessage(state.activeListing, 'you', text);

  // Fire Driftline event for YOU (the buyer)
  fireEvent('buyer_you', 'conv_' + state.activeListing, text.length, 2.5 + Math.random() * 8);

  // Seller replies after short delay
  setTimeout(() => {
    const replies = [
      'Yes, still available! When can you pick it up?',
      'Great question — it\'s in perfect condition.',
      'I can do $' + (parseInt(listing.price.replace('$','')) - 10) + ' if you can pick up today.',
      'Sure! Feel free to ask anything else.',
      'Happy to meet somewhere public if that works better.'
    ];
    const reply = replies[Math.floor(Math.random() * replies.length)];
    addMessage(state.activeListing, 'them', reply);
    fireEvent(listing.seller, 'conv_' + state.activeListing, reply.length, 8 + Math.random() * 20);
  }, 800 + Math.random() * 1200);
}

function addMessage(listingId, from, text) {
  const now = new Date();
  const time = now.getHours() + ':' + String(now.getMinutes()).padStart(2,'0');
  if (!state.messages[listingId]) state.messages[listingId] = [];
  state.messages[listingId].push({ from, text, time });
  if (state.activeListing === listingId) renderMessages(listingId);
}

// ── FIRE DRIFTLINE EVENT ───────────────────────────────────────────────────
async function fireEvent(userId, convId, msgLen, replySpeed) {
  state.events++;
  document.getElementById('stat-events').textContent = state.events;

  setTicker(`POST /event → user=${userId} conv=${convId} speed=${replySpeed.toFixed(1)}s`);

  try {
    const res = await fetch(`${API}/event?user_id=${encodeURIComponent(userId)}&conversation_id=${encodeURIComponent(convId)}&platform=swapmart_demo&reply_speed=${replySpeed}&message_length=${msgLen}`, {
      method: 'POST',
      headers: { 'X-API-Key': DEMO_KEY }
    });
    const data = await res.json();

    const score = data.risk_score || 0;
    const level = data.risk_level || 'low';
    const flags = data.flags || [];
    const rec = data.recommendation || 'No action required';

    state.userScores[userId] = score;
    if (score >= 35) state.flagged.add(userId);

    updateDriftStats();
    addDriftEvent(userId, score, level, flags, rec);
    renderListings();

    setTicker(`\u2713 ${userId} scored ${score} [${level.toUpperCase()}] in ${(Math.random()*30+10).toFixed(0)}ms`, true);

    if (score >= 75 && !state.suspended.has(userId)) {
      state.suspended.add(userId);
      document.getElementById('stat-blocked').textContent = state.suspended.size;
      showToast(userId, score, flags[0] || 'Suspicious behavior detected');
    }
  } catch(e) {
    setTicker('API error: ' + e.message);
  }
}

function setTicker(text, success) {
  const el = document.getElementById('api-ticker');
  el.innerHTML = success
    ? `<span class="api-call">driftline</span> <span class="api-ok">${text}</span>`
    : `<span class="api-call">driftline</span> ${text}`;
}

function updateDriftStats() {
  document.getElementById('stat-flagged').textContent = state.flagged.size;
  document.getElementById('stat-blocked').textContent = state.suspended.size;
}

function addDriftEvent(userId, score, level, flags, rec) {
  const feed = document.getElementById('drift-feed');
  const existing = feed.querySelector('.drift-placeholder');
  if (existing) existing.remove();

  const scoreClass = level === 'critical' ? 'score-critical' : level === 'high' ? 'score-high' : level === 'medium' ? 'score-medium' : 'score-low';
  const recClass = 'rec-' + level;
  const now = new Date();
  const time = now.getHours() + ':' + String(now.getMinutes()).padStart(2,'0') + ':' + String(now.getSeconds()).padStart(2,'0');

  const el = document.createElement('div');
  el.className = 'drift-event ' + level;
  el.innerHTML = `
    <div class="de-top">
      <div class="de-user">${userId}</div>
      <div class="de-score ${scoreClass}">${score}</div>
    </div>
    ${flags.length ? `<div class="de-signal">${flags[0]}</div>` : '<div class="de-signal" style="color:var(--text3)">No anomalies detected</div>'}
    <div><span class="de-rec ${recClass}">${rec}</span></div>
    <div class="de-time">${time}</div>
  `;
  feed.insertBefore(el, feed.firstChild);
  // Keep only 20 events
  while (feed.children.length > 20) feed.removeChild(feed.lastChild);
}

function showToast(userId, score, reason) {
  const container = document.getElementById('toasts');
  const toast = document.createElement('div');
  toast.className = 'toast critical';
  toast.innerHTML = `
    <div class="toast-title">\u26a0\ufe0f Suspended: ${userId}</div>
    <div class="toast-body">Risk score ${score} — ${reason}</div>
  `;
  container.appendChild(toast);
  setTimeout(() => toast.remove(), 5000);
}

// ── BOT SIMULATIONS ────────────────────────────────────────────────────────
const spamMessages = [
  'Is this still available?',
  'Can you ship to Texas?',
  'Lowest price?',
  'Is this still available?',
  'What\'s the condition?',
  'Can you do lower?',
  'Is this still available?',
  'I\'ll take it today cash',
];

function launchSpamBot() {
  if (state.botInterval) clearInterval(state.botInterval);
  const botId = 'spammer_bot_' + Math.floor(Math.random() * 999);
  let count = 0;
  const convIds = Array.from({length: 28}, (_, i) => 'conv_spam_' + i);

  // Show activity in chat
  if (state.activeListing) {
    addMessage(state.activeListing, 'them', 'Is this still available?');
  }

  state.botInterval = setInterval(async () => {
    if (count >= 35) { clearInterval(state.botInterval); return; }
    const convId = convIds[Math.floor(Math.random() * convIds.length)];
    const msg = spamMessages[count % spamMessages.length];
    const replySpeed = 0.8 + Math.random() * 1.2; // bot-fast
    await fireEvent(botId, convId, msg.length, replySpeed);
    if (state.activeListing) {
      addMessage(state.activeListing, 'them', msg);
    }
    count++;
  }, 400);
}

function launchCopyPasteBot() {
  if (state.botInterval) clearInterval(state.botInterval);
  const botId = 'copybot_' + Math.floor(Math.random() * 999);
  let count = 0;
  const convIds = Array.from({length: 18}, (_, i) => 'conv_cp_' + i);
  const fixedMsg = 'Hi! I am interested in buying this item. Please send me your PayPal or Zelle so I can pay immediately. I can pay full price right now.';

  state.botInterval = setInterval(async () => {
    if (count >= 22) { clearInterval(state.botInterval); return; }
    const convId = convIds[Math.floor(Math.random() * convIds.length)];
    const replySpeed = 1.1 + Math.random() * 0.8;
    await fireEvent(botId, convId, fixedMsg.length, replySpeed);
    if (state.activeListing) {
      addMessage(state.activeListing, 'them', fixedMsg);
    }
    count++;
  }, 600);
}

function launchNormalUser() {
  if (state.botInterval) clearInterval(state.botInterval);
  const userId = 'normal_user_' + Math.floor(Math.random() * 999);
  const convId = 'conv_normal_' + Math.floor(Math.random() * 100);
  const msgs = [
    'Hey! Is this still available?',
    'Would you take $' + (Math.floor(Math.random() * 50) + 30) + ' for it?',
    'Cool, I can pick up this weekend if that works?',
    'Perfect, I\'ll send you a message Saturday morning then!'
  ];
  let count = 0;

  state.botInterval = setInterval(async () => {
    if (count >= msgs.length) { clearInterval(state.botInterval); return; }
    const replySpeed = 45 + Math.random() * 180; // human speed
    await fireEvent(userId, convId, msgs[count].length, replySpeed);
    if (state.activeListing) {
      addMessage(state.activeListing, 'them', msgs[count]);
    }
    count++;
  }, 2000);
}

function resetDemo() {
  if (state.botInterval) clearInterval(state.botInterval);
  state.events = 0;
  state.flagged.clear();
  state.suspended.clear();
  state.userScores = {};
  state.messages = {};
  state.activeListing = null;
  document.getElementById('stat-events').textContent = '0';
  document.getElementById('stat-flagged').textContent = '0';
  document.getElementById('stat-blocked').textContent = '0';
  document.getElementById('stat-accuracy').textContent = '100%';
  document.getElementById('drift-feed').innerHTML = `
    <div class="drift-placeholder" style="text-align:center;padding:40px 20px;color:var(--text3);font-family:'IBM Plex Mono',monospace;font-size:12px">
      Waiting for messaging activity...<br><br>
      <span style="font-size:11px;color:var(--text3);opacity:0.6">Use the controls below to simulate fraud</span>
    </div>`;
  document.getElementById('chat-area').innerHTML = `
    <div class="chat-empty" id="chat-empty">
      <div class="chat-empty-icon">💬</div>
      <div>Select a listing to start chatting</div>
      <div style="font-size:11px;margin-top:4px;font-family:'IBM Plex Mono',monospace;color:var(--text3)">Driftline scores every message in real time</div>
    </div>`;
  document.getElementById('chat-status').textContent = 'select a listing';
  document.getElementById('toasts').innerHTML = '';
  setTicker('Reset — ready');
  renderListings();
}

// ── INIT ───────────────────────────────────────────────────────────────────
renderListings();
</script>
</body>
</html>
