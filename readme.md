<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>ANKIT OS — AI Operating System</title>
<link rel="preconnect" href="https://fonts.googleapis.com"/>
<link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;500;600;700;800;900&family=Syne:wght@400;500;600;700;800&family=JetBrains+Mono:wght@300;400;500&display=swap" rel="stylesheet"/>
<script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js"></script>
<style>
  :root {
    --neon-blue: #00d4ff;
    --neon-purple: #b347ff;
    --neon-pink: #ff2d9b;
    --neon-green: #00ff9d;
    --bg-primary: #020408;
    --bg-secondary: #050b14;
    --glass: rgba(0,212,255,0.04);
    --glass-border: rgba(0,212,255,0.12);
    --glass-hover: rgba(0,212,255,0.08);
    --text-primary: #e8f4ff;
    --text-secondary: rgba(232,244,255,0.55);
    --text-muted: rgba(232,244,255,0.3);
  }
  * { margin:0; padding:0; box-sizing:border-box; }
  html,body { height:100%; overflow:hidden; }
  body {
    background: var(--bg-primary);
    color: var(--text-primary);
    font-family: 'Syne', sans-serif;
    overflow: hidden;
  }

  /* ─── LOADING SCREEN ─── */
  #loader {
    position:fixed; inset:0; z-index:9999;
    background: #020408;
    display:flex; flex-direction:column;
    align-items:center; justify-content:center;
    transition: opacity 0.8s ease, visibility 0.8s ease;
  }
  #loader.hide { opacity:0; visibility:hidden; }
  .loader-ring {
    width:90px; height:90px;
    border:2px solid rgba(0,212,255,0.1);
    border-top:2px solid var(--neon-blue);
    border-radius:50%;
    animation: spin 1s linear infinite;
  }
  .loader-ring2 {
    position:absolute;
    width:110px; height:110px;
    border:1px solid rgba(179,71,255,0.15);
    border-bottom:1px solid var(--neon-purple);
    border-radius:50%;
    animation: spin 1.5s linear infinite reverse;
  }
  .loader-logo {
    position:absolute;
    font-family:'Orbitron',monospace;
    font-size:14px; font-weight:700;
    color:var(--neon-blue);
    letter-spacing:3px;
  }
  .loader-text {
    margin-top:28px;
    font-family:'JetBrains Mono',monospace;
    font-size:11px; color:var(--text-muted);
    letter-spacing:4px;
    animation: pulse 1.5s ease infinite;
  }
  @keyframes spin { to { transform:rotate(360deg); } }
  @keyframes pulse { 0%,100%{opacity:0.4} 50%{opacity:1} }

  /* ─── PARTICLES ─── */
  #particles-canvas {
    position:fixed; inset:0; z-index:0; pointer-events:none;
  }

  /* ─── FLOATING ORBS ─── */
  .orb {
    position:fixed; border-radius:50%; filter:blur(80px);
    pointer-events:none; z-index:0; animation: orbFloat 8s ease-in-out infinite;
  }
  .orb1 { width:500px;height:500px; background:rgba(0,212,255,0.06); top:-100px;left:-100px; animation-delay:0s; }
  .orb2 { width:400px;height:400px; background:rgba(179,71,255,0.07); bottom:-100px;right:-100px; animation-delay:-3s; }
  .orb3 { width:300px;height:300px; background:rgba(255,45,155,0.04); top:50%;left:50%; animation-delay:-6s; }
  @keyframes orbFloat {
    0%,100%{transform:translate(0,0) scale(1);}
    33%{transform:translate(30px,-20px) scale(1.05);}
    66%{transform:translate(-20px,30px) scale(0.95);}
  }

  /* ─── LAYOUT ─── */
  #app { position:relative; z-index:1; display:flex; height:100vh; overflow:hidden; }

  /* ─── SIDEBAR ─── */
  #sidebar {
    width:72px; flex-shrink:0;
    background: rgba(5,11,20,0.9);
    border-right:1px solid var(--glass-border);
    display:flex; flex-direction:column; align-items:center;
    padding:20px 0; gap:6px;
    backdrop-filter:blur(20px);
    transition: width 0.3s cubic-bezier(0.4,0,0.2,1);
    overflow:hidden;
    position:relative; z-index:10;
  }
  #sidebar.expanded { width:220px; }
  .sidebar-logo {
    font-family:'Orbitron',monospace; font-size:13px; font-weight:900;
    color:var(--neon-blue); letter-spacing:2px;
    margin-bottom:16px; white-space:nowrap;
    text-shadow: 0 0 20px rgba(0,212,255,0.5);
    width:100%; text-align:center; padding:0 8px;
  }
  .sidebar-logo .logo-sub {
    display:block; font-size:7px; font-weight:400;
    color:var(--text-muted); letter-spacing:4px; margin-top:2px;
    font-family:'JetBrains Mono',monospace;
  }
  .nav-item {
    width:calc(100% - 16px); height:44px;
    display:flex; align-items:center; gap:12px;
    padding:0 12px; border-radius:10px;
    cursor:pointer; transition:all 0.2s ease;
    white-space:nowrap; color:var(--text-muted);
    border:1px solid transparent;
    position:relative;
  }
  .nav-item svg { flex-shrink:0; width:18px; height:18px; }
  .nav-item span { font-size:13px; font-weight:500; overflow:hidden; }
  .nav-item:hover {
    background: rgba(0,212,255,0.06);
    color: var(--neon-blue);
    border-color: rgba(0,212,255,0.15);
  }
  .nav-item.active {
    background: rgba(0,212,255,0.1);
    color: var(--neon-blue);
    border-color: rgba(0,212,255,0.25);
    box-shadow: 0 0 20px rgba(0,212,255,0.1);
  }
  .nav-item.active::before {
    content:''; position:absolute; left:0; top:20%; bottom:20%;
    width:2px; background:var(--neon-blue);
    border-radius:0 2px 2px 0;
    box-shadow: 0 0 8px var(--neon-blue);
  }
  .sidebar-divider {
    width:40px; height:1px; background:var(--glass-border);
    margin:6px auto;
  }
  .sidebar-toggle {
    margin-top:auto; cursor:pointer;
    width:36px; height:36px; border-radius:8px;
    display:flex; align-items:center; justify-content:center;
    border:1px solid var(--glass-border);
    color:var(--text-muted); transition:all 0.2s;
  }
  .sidebar-toggle:hover { color:var(--neon-blue); border-color:rgba(0,212,255,0.3); }

  /* ─── MAIN CONTENT ─── */
  #main {
    flex:1; overflow-y:auto; overflow-x:hidden;
    scroll-behavior:smooth;
  }
  #main::-webkit-scrollbar { width:4px; }
  #main::-webkit-scrollbar-track { background:transparent; }
  #main::-webkit-scrollbar-thumb { background:rgba(0,212,255,0.2); border-radius:4px; }
  .page { display:none; min-height:100vh; padding:24px; animation: fadeIn 0.4s ease; }
  .page.active { display:block; }
  @keyframes fadeIn { from{opacity:0;transform:translateY(12px)} to{opacity:1;transform:translateY(0)} }

  /* ─── GLASS CARD ─── */
  .glass-card {
    background: rgba(8,16,32,0.7);
    border:1px solid var(--glass-border);
    border-radius:16px;
    backdrop-filter: blur(20px);
    padding:20px;
    transition: all 0.3s ease;
  }
  .glass-card:hover {
    border-color: rgba(0,212,255,0.25);
    box-shadow: 0 0 30px rgba(0,212,255,0.06), 0 8px 32px rgba(0,0,0,0.4);
    transform: translateY(-2px);
  }
  .glass-card.purple { border-color: rgba(179,71,255,0.2); }
  .glass-card.purple:hover { border-color: rgba(179,71,255,0.35); box-shadow:0 0 30px rgba(179,71,255,0.08); }
  .glass-card.green { border-color: rgba(0,255,157,0.2); }
  .glass-card.pink { border-color: rgba(255,45,155,0.2); }

  /* ─── TYPOGRAPHY ─── */
  .page-title {
    font-family:'Orbitron',monospace; font-size:22px; font-weight:700;
    color:var(--text-primary); letter-spacing:1px;
  }
  .section-label {
    font-family:'JetBrains Mono',monospace; font-size:10px;
    color:var(--neon-blue); letter-spacing:4px; text-transform:uppercase;
    margin-bottom:16px;
  }
  .card-title {
    font-size:12px; font-weight:600; color:var(--text-muted);
    letter-spacing:2px; text-transform:uppercase;
    font-family:'JetBrains Mono',monospace;
  }
  .card-value {
    font-family:'Orbitron',monospace; font-size:28px; font-weight:700;
    color:var(--text-primary); line-height:1;
  }

  /* ─── GRID LAYOUTS ─── */
  .grid-2 { display:grid; grid-template-columns:1fr 1fr; gap:16px; }
  .grid-3 { display:grid; grid-template-columns:1fr 1fr 1fr; gap:16px; }
  .grid-4 { display:grid; grid-template-columns:repeat(4,1fr); gap:16px; }

  /* ─── NEON BUTTON ─── */
  .btn-neon {
    display:inline-flex; align-items:center; gap:8px;
    padding:10px 20px; border-radius:10px;
    border:1px solid rgba(0,212,255,0.3);
    background:rgba(0,212,255,0.07);
    color:var(--neon-blue); font-size:13px; font-weight:600;
    cursor:pointer; transition:all 0.2s ease;
    font-family:'Syne',sans-serif;
  }
  .btn-neon:hover {
    background:rgba(0,212,255,0.15);
    border-color:var(--neon-blue);
    box-shadow:0 0 20px rgba(0,212,255,0.2);
  }
  .btn-purple {
    border-color:rgba(179,71,255,0.3); background:rgba(179,71,255,0.07); color:var(--neon-purple);
  }
  .btn-purple:hover { background:rgba(179,71,255,0.15); border-color:var(--neon-purple); box-shadow:0 0 20px rgba(179,71,255,0.2); }
  .btn-green { border-color:rgba(0,255,157,0.3); background:rgba(0,255,157,0.07); color:var(--neon-green); }
  .btn-green:hover { background:rgba(0,255,157,0.15); border-color:var(--neon-green); box-shadow:0 0 20px rgba(0,255,157,0.2); }

  /* ─── INPUTS ─── */
  .neo-input {
    width:100%; padding:11px 14px;
    background:rgba(0,212,255,0.03);
    border:1px solid var(--glass-border);
    border-radius:10px; color:var(--text-primary);
    font-family:'Syne',sans-serif; font-size:14px;
    outline:none; transition:all 0.2s;
  }
  .neo-input:focus { border-color:rgba(0,212,255,0.4); background:rgba(0,212,255,0.06); box-shadow:0 0 0 3px rgba(0,212,255,0.06); }
  .neo-input::placeholder { color:var(--text-muted); }
  textarea.neo-input { resize:vertical; min-height:80px; line-height:1.6; }
  .neo-label { display:block; margin-bottom:7px; font-size:12px; color:var(--text-secondary); font-weight:500; letter-spacing:0.5px; }

  /* ─── PROGRESS RING ─── */
  .ring-wrap { position:relative; display:inline-flex; align-items:center; justify-content:center; }
  .ring-wrap svg { transform:rotate(-90deg); }
  .ring-bg { fill:none; stroke:rgba(0,212,255,0.08); }
  .ring-fill { fill:none; stroke-linecap:round; transition:stroke-dashoffset 1s cubic-bezier(0.4,0,0.2,1); }
  .ring-center { position:absolute; text-align:center; }

  /* ─── PROGRESS BAR ─── */
  .progress-bar { height:6px; background:rgba(255,255,255,0.06); border-radius:99px; overflow:hidden; margin-top:8px; }
  .progress-fill {
    height:100%; border-radius:99px;
    background: linear-gradient(90deg, var(--neon-blue), var(--neon-purple));
    transition: width 1s cubic-bezier(0.4,0,0.2,1);
    box-shadow: 0 0 8px rgba(0,212,255,0.4);
  }

  /* ─── SLIDER ─── */
  input[type=range] {
    width:100%; accent-color:var(--neon-blue); cursor:pointer;
    height:4px; background:rgba(0,212,255,0.15); border-radius:99px;
  }

  /* ─── MOOD DOTS ─── */
  .mood-dot {
    width:42px; height:42px; border-radius:50%;
    border:2px solid transparent; cursor:pointer;
    font-size:22px; display:flex; align-items:center; justify-content:center;
    transition:all 0.2s; background:rgba(255,255,255,0.03);
  }
  .mood-dot.selected { border-color:var(--neon-blue); background:rgba(0,212,255,0.1); transform:scale(1.1); }

  /* ─── AI FEEDBACK CHIP ─── */
  .ai-chip {
    display:inline-flex; align-items:center; gap:8px;
    padding:8px 14px; border-radius:99px;
    background:rgba(0,212,255,0.06); border:1px solid rgba(0,212,255,0.15);
    font-size:12px; color:var(--text-secondary);
    margin:4px; animation: chipIn 0.4s ease both;
  }
  .ai-chip .dot { width:6px; height:6px; border-radius:50%; background:var(--neon-blue); box-shadow:0 0 6px var(--neon-blue); flex-shrink:0; }
  @keyframes chipIn { from{opacity:0;transform:translateY(8px)} to{opacity:1;transform:translateY(0)} }

  /* ─── POMODORO ─── */
  #focus-overlay {
    position:fixed; inset:0; z-index:500;
    background:rgba(2,4,8,0.97);
    display:none; flex-direction:column; align-items:center; justify-content:center;
    animation: fadeIn 0.5s ease;
  }
  #focus-overlay.show { display:flex; }
  .pomodoro-ring { position:relative; }
  .pomodoro-time {
    position:absolute; top:50%; left:50%; transform:translate(-50%,-50%);
    font-family:'Orbitron',monospace; font-size:48px; font-weight:700;
    color:var(--text-primary); text-shadow:0 0 30px rgba(0,212,255,0.4);
    letter-spacing:2px;
  }

  /* ─── STAT CARDS ─── */
  .stat-num { font-family:'Orbitron',monospace; font-size:32px; font-weight:800; line-height:1; }
  .stat-label { font-size:11px; color:var(--text-muted); letter-spacing:2px; text-transform:uppercase; font-family:'JetBrains Mono',monospace; margin-top:4px; }

  /* ─── SCROLLABLE TAGS ─── */
  .tag-wrap { display:flex; flex-wrap:wrap; gap:8px; margin-top:8px; }
  .tag {
    padding:5px 12px; border-radius:99px; font-size:12px; cursor:pointer;
    border:1px solid var(--glass-border); color:var(--text-muted);
    transition:all 0.2s; background:rgba(255,255,255,0.02);
  }
  .tag:hover, .tag.active { background:rgba(0,212,255,0.1); border-color:rgba(0,212,255,0.3); color:var(--neon-blue); }

  /* ─── GOAL BAR ─── */
  .goal-item { margin-bottom:20px; }
  .goal-header { display:flex; justify-content:space-between; align-items:center; margin-bottom:8px; }
  .goal-name { font-size:14px; font-weight:600; color:var(--text-primary); }
  .goal-pct { font-family:'Orbitron',monospace; font-size:13px; color:var(--neon-blue); }

  /* ─── STREAK BADGE ─── */
  .streak-badge {
    display:inline-flex; align-items:center; gap:6px;
    padding:6px 14px; border-radius:99px;
    background:linear-gradient(135deg,rgba(255,165,0,0.1),rgba(255,100,0,0.1));
    border:1px solid rgba(255,165,0,0.25);
    font-family:'Orbitron',monospace; font-size:13px; font-weight:700;
    color:#ffaa00;
  }

  /* ─── NOTIFICATIONS ─── */
  .toast {
    position:fixed; bottom:24px; right:24px; z-index:9000;
    padding:12px 20px; border-radius:12px;
    background:rgba(0,212,255,0.1); border:1px solid rgba(0,212,255,0.3);
    color:var(--text-primary); font-size:13px;
    backdrop-filter:blur(20px);
    animation: toastIn 0.3s ease, toastOut 0.3s ease 2.7s both;
    box-shadow: 0 0 20px rgba(0,212,255,0.15);
  }
  @keyframes toastIn { from{opacity:0;transform:translateY(20px)} to{opacity:1;transform:translateY(0)} }
  @keyframes toastOut { from{opacity:1} to{opacity:0} }

  /* ─── CHART CONTAINER ─── */
  .chart-box { position:relative; height:220px; }

  /* ─── RESPONSIVE ─── */
  @media(max-width:768px) {
    #sidebar { width:56px; }
    #sidebar.expanded { width:200px; }
    .grid-4,.grid-3 { grid-template-columns:1fr 1fr; }
    .grid-2 { grid-template-columns:1fr; }
    .page { padding:16px; }
    .card-value { font-size:22px; }
  }
  @media(max-width:480px) {
    .grid-4,.grid-3,.grid-2 { grid-template-columns:1fr; }
  }

  /* ─── QUOTE TICKER ─── */
  .quote-ticker {
    font-size:15px; font-style:italic; color:var(--text-secondary);
    line-height:1.7; animation: fadeIn 0.6s ease;
    border-left:2px solid var(--neon-blue);
    padding-left:14px;
  }

  /* ─── JOURNAL ENTRY CARD ─── */
  .journal-entry {
    background:rgba(0,212,255,0.03); border:1px solid rgba(0,212,255,0.1);
    border-radius:12px; padding:16px; margin-bottom:12px;
    animation: fadeIn 0.4s ease;
    transition: all 0.2s;
  }
  .journal-entry:hover { border-color:rgba(0,212,255,0.2); }

  /* ─── TOP BAR ─── */
  .topbar {
    display:flex; align-items:center; justify-content:space-between;
    margin-bottom:28px; gap:16px;
    padding-bottom:20px; border-bottom:1px solid var(--glass-border);
  }
  .topbar-right { display:flex; align-items:center; gap:12px; flex-shrink:0; }
  .live-clock {
    font-family:'Orbitron',monospace; font-size:15px; font-weight:600;
    color:var(--neon-blue); letter-spacing:2px;
  }

  /* Scrollbar for main */
  #main { scrollbar-width:thin; scrollbar-color:rgba(0,212,255,0.2) transparent; }
</style>
</head>
<body>

<!-- LOADING SCREEN -->
<div id="loader">
  <div style="position:relative;display:flex;align-items:center;justify-content:center;">
    <div class="loader-ring2"></div>
    <div class="loader-ring"></div>
    <div class="loader-logo">ANKIT OS</div>
  </div>
  <div class="loader-text">INITIALIZING SYSTEM...</div>
</div>

<!-- BACKGROUND -->
<canvas id="particles-canvas"></canvas>
<div class="orb orb1"></div>
<div class="orb orb2"></div>
<div class="orb orb3"></div>

<!-- FOCUS OVERLAY -->
<div id="focus-overlay">
  <div style="text-align:center;margin-bottom:40px;">
    <div style="font-family:'Orbitron',monospace;font-size:11px;letter-spacing:6px;color:var(--text-muted);margin-bottom:8px;">FOCUS MODE</div>
    <div style="font-family:'Orbitron',monospace;font-size:28px;font-weight:800;color:var(--neon-blue);text-shadow:0 0 30px rgba(0,212,255,0.5);">DEEP WORK</div>
  </div>
  <div class="pomodoro-ring">
    <svg width="280" height="280" viewBox="0 0 280 280">
      <circle cx="140" cy="140" r="120" fill="none" stroke="rgba(0,212,255,0.06)" stroke-width="8"/>
      <circle id="pomo-ring-fill" cx="140" cy="140" r="120" fill="none"
        stroke="url(#pomoGrad)" stroke-width="8" stroke-linecap="round"
        stroke-dasharray="753.98" stroke-dashoffset="0"
        transform="rotate(-90 140 140)"/>
      <defs>
        <linearGradient id="pomoGrad" x1="0%" y1="0%" x2="100%" y2="0%">
          <stop offset="0%" stop-color="#00d4ff"/>
          <stop offset="100%" stop-color="#b347ff"/>
        </linearGradient>
      </defs>
    </svg>
    <div class="pomodoro-time" id="pomo-display">25:00</div>
  </div>
  <div style="display:flex;gap:16px;margin-top:40px;">
    <button class="btn-neon" id="pomo-start-btn" onclick="togglePomodoro()">▶ Start</button>
    <button class="btn-neon" onclick="resetPomodoro()">↺ Reset</button>
    <button class="btn-neon" style="border-color:rgba(255,45,155,0.3);color:#ff2d9b;" onclick="exitFocus()">✕ Exit</button>
  </div>
  <div style="margin-top:32px;max-width:400px;text-align:center;">
    <div id="pomo-quote" style="font-size:15px;font-style:italic;color:var(--text-muted);line-height:1.8;"></div>
  </div>
</div>

<!-- APP -->
<div id="app">
  <!-- SIDEBAR -->
  <nav id="sidebar">
    <div class="sidebar-logo">
      <span>ANKIT</span>
      <span class="logo-sub">OPERATING SYSTEM</span>
    </div>

    <div class="nav-item active" data-page="home" onclick="navigate('home',this)">
      <svg fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24"><path d="M3 9.5L12 3l9 6.5V20a1 1 0 01-1 1H4a1 1 0 01-1-1V9.5z"/><path d="M9 21V12h6v9"/></svg>
      <span>Home</span>
    </div>
    <div class="nav-item" data-page="journal" onclick="navigate('journal',this)">
      <svg fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24"><path d="M14 2H6a2 2 0 00-2 2v16a2 2 0 002 2h12a2 2 0 002-2V8z"/><polyline points="14 2 14 8 20 8"/><line x1="16" y1="13" x2="8" y2="13"/><line x1="16" y1="17" x2="8" y2="17"/><polyline points="10 9 9 9 8 9"/></svg>
      <span>Journal</span>
    </div>
    <div class="nav-item" data-page="analytics" onclick="navigate('analytics',this)">
      <svg fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24"><polyline points="22 12 18 12 15 21 9 3 6 12 2 12"/></svg>
      <span>Analytics</span>
    </div>
    <div class="nav-item" data-page="charts" onclick="navigate('charts',this)">
      <svg fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24"><rect x="3" y="3" width="18" height="18" rx="2"/><path d="M7 16V8M12 16v-5M17 16v-8"/></svg>
      <span>Charts</span>
    </div>
    <div class="sidebar-divider"></div>
    <div class="nav-item" data-page="focus" onclick="navigate('focus',this)">
      <svg fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24"><circle cx="12" cy="12" r="10"/><circle cx="12" cy="12" r="6"/><circle cx="12" cy="12" r="2"/></svg>
      <span>Focus</span>
    </div>
    <div class="nav-item" data-page="creator" onclick="navigate('creator',this)">
      <svg fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24"><polygon points="23 7 16 12 23 17 23 7"/><rect x="1" y="5" width="15" height="14" rx="2"/></svg>
      <span>Creator</span>
    </div>
    <div class="nav-item" data-page="goals" onclick="navigate('goals',this)">
      <svg fill="none" stroke="currentColor"
