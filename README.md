<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>AgriSense — Smart Farm Monitor</title>
<link href="https://fonts.googleapis.com/css2?family=Space+Mono:wght@400;700&family=Syne:wght@400;600;800&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0a0f0a;
    --surface: #111a11;
    --card: #162016;
    --border: #1e3020;
    --green: #4ade80;
    --green-dim: #22c55e;
    --green-glow: rgba(74,222,128,0.15);
    --amber: #fbbf24;
    --amber-glow: rgba(251,191,36,0.15);
    --blue: #60a5fa;
    --blue-glow: rgba(96,165,250,0.15);
    --red: #f87171;
    --red-glow: rgba(248,113,113,0.15);
    --sky: #38bdf8;
    --purple: #c084fc;
    --text: #e2f0e2;
    --text-dim: #7a9a7a;
    --mono: 'Space Mono', monospace;
    --sans: 'Syne', sans-serif;
  }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: var(--sans);
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* Animated background grid */
  body::before {
    content: '';
    position: fixed;
    inset: 0;
    background-image:
      linear-gradient(rgba(74,222,128,0.03) 1px, transparent 1px),
      linear-gradient(90deg, rgba(74,222,128,0.03) 1px, transparent 1px);
    background-size: 40px 40px;
    pointer-events: none;
    z-index: 0;
  }

  /* Glowing orb */
  body::after {
    content: '';
    position: fixed;
    top: -200px;
    left: 50%;
    transform: translateX(-50%);
    width: 800px;
    height: 600px;
    background: radial-gradient(ellipse, rgba(74,222,128,0.06) 0%, transparent 70%);
    pointer-events: none;
    z-index: 0;
  }

  header {
    position: relative;
    z-index: 10;
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 20px 40px;
    border-bottom: 1px solid var(--border);
    background: rgba(10,15,10,0.8);
    backdrop-filter: blur(12px);
    position: sticky;
    top: 0;
  }

  .logo {
    display: flex;
    align-items: center;
    gap: 12px;
  }

  .logo-icon {
    width: 36px;
    height: 36px;
    background: linear-gradient(135deg, var(--green), #166534);
    border-radius: 8px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 18px;
    box-shadow: 0 0 20px var(--green-glow);
  }

  .logo-text {
    font-size: 22px;
    font-weight: 800;
    letter-spacing: -0.5px;
    color: var(--text);
  }

  .logo-text span { color: var(--green); }

  .header-status {
    display: flex;
    align-items: center;
    gap: 8px;
    font-family: var(--mono);
    font-size: 11px;
    color: var(--text-dim);
  }

  .status-dot {
    width: 8px;
    height: 8px;
    border-radius: 50%;
    background: var(--green);
    animation: pulse-dot 2s infinite;
  }

  @keyframes pulse-dot {
    0%, 100% { opacity: 1; box-shadow: 0 0 6px var(--green); }
    50% { opacity: 0.5; box-shadow: none; }
  }

  .header-right {
    display: flex;
    align-items: center;
    gap: 16px;
  }

  .bt-badge {
    display: flex;
    align-items: center;
    gap: 6px;
    padding: 6px 14px;
    border: 1px solid var(--border);
    border-radius: 20px;
    font-family: var(--mono);
    font-size: 11px;
    color: var(--blue);
    background: var(--blue-glow);
  }

  .timestamp {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--text-dim);
  }

  main {
    position: relative;
    z-index: 1;
    max-width: 1400px;
    margin: 0 auto;
    padding: 40px 40px 80px;
  }

  .hero {
    margin-bottom: 48px;
  }

  .hero-label {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--green);
    letter-spacing: 3px;
    text-transform: uppercase;
    margin-bottom: 12px;
  }

  .hero-title {
    font-size: clamp(28px, 4vw, 48px);
    font-weight: 800;
    line-height: 1.1;
    color: var(--text);
    margin-bottom: 16px;
  }

  .hero-title span {
    color: var(--green);
    text-shadow: 0 0 30px rgba(74,222,128,0.4);
  }

  .hero-sub {
    font-family: var(--mono);
    font-size: 13px;
    color: var(--text-dim);
    max-width: 500px;
    line-height: 1.8;
  }

  /* Alert banner */
  .alert-bar {
    display: none;
    align-items: center;
    gap: 12px;
    padding: 12px 20px;
    border-radius: 8px;
    border: 1px solid;
    margin-bottom: 32px;
    font-family: var(--mono);
    font-size: 12px;
    animation: slide-in 0.3s ease;
  }

  .alert-bar.active { display: flex; }
  .alert-bar.warning { border-color: var(--amber); background: var(--amber-glow); color: var(--amber); }
  .alert-bar.danger { border-color: var(--red); background: var(--red-glow); color: var(--red); }
  .alert-bar.info { border-color: var(--green); background: var(--green-glow); color: var(--green); }

  @keyframes slide-in {
    from { transform: translateY(-10px); opacity: 0; }
    to { transform: translateY(0); opacity: 1; }
  }

  /* Sensor grid */
  .sensor-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
    gap: 20px;
    margin-bottom: 32px;
  }

  .card {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 16px;
    padding: 24px;
    position: relative;
    overflow: hidden;
    transition: transform 0.2s, border-color 0.3s;
    cursor: default;
  }

  .card:hover {
    transform: translateY(-2px);
  }

  .card::before {
    content: '';
    position: absolute;
    inset: 0;
    border-radius: 16px;
    opacity: 0;
    transition: opacity 0.3s;
    pointer-events: none;
  }

  .card:hover::before { opacity: 1; }

  .card.green::before { background: radial-gradient(circle at 50% 0%, rgba(74,222,128,0.06), transparent 60%); }
  .card.amber::before { background: radial-gradient(circle at 50% 0%, rgba(251,191,36,0.06), transparent 60%); }
  .card.blue::before { background: radial-gradient(circle at 50% 0%, rgba(96,165,250,0.06), transparent 60%); }
  .card.red::before { background: radial-gradient(circle at 50% 0%, rgba(248,113,113,0.06), transparent 60%); }
  .card.sky::before { background: radial-gradient(circle at 50% 0%, rgba(56,189,248,0.06), transparent 60%); }
  .card.purple::before { background: radial-gradient(circle at 50% 0%, rgba(192,132,252,0.06), transparent 60%); }

  .card-header {
    display: flex;
    align-items: flex-start;
    justify-content: space-between;
    margin-bottom: 20px;
  }

  .card-icon {
    width: 44px;
    height: 44px;
    border-radius: 10px;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 20px;
  }

  .card-icon.green { background: rgba(74,222,128,0.12); }
  .card-icon.amber { background: rgba(251,191,36,0.12); }
  .card-icon.blue { background: rgba(96,165,250,0.12); }
  .card-icon.red { background: rgba(248,113,113,0.12); }
  .card-icon.sky { background: rgba(56,189,248,0.12); }
  .card-icon.purple { background: rgba(192,132,252,0.12); }

  .card-status-badge {
    font-family: var(--mono);
    font-size: 10px;
    padding: 4px 8px;
    border-radius: 20px;
    font-weight: 700;
    letter-spacing: 1px;
  }

  .badge-normal { background: rgba(74,222,128,0.15); color: var(--green); }
  .badge-warning { background: rgba(251,191,36,0.15); color: var(--amber); }
  .badge-alert { background: rgba(248,113,113,0.15); color: var(--red); }
  .badge-active { background: rgba(248,113,113,0.2); color: var(--red); animation: flash 1s infinite; }
  .badge-clear { background: rgba(74,222,128,0.15); color: var(--green); }

  @keyframes flash {
    0%, 100% { opacity: 1; }
    50% { opacity: 0.4; }
  }

  .card-label {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--text-dim);
    letter-spacing: 1px;
    text-transform: uppercase;
    margin-bottom: 4px;
  }

  .card-value {
    font-size: 42px;
    font-weight: 800;
    line-height: 1;
    margin-bottom: 4px;
    font-variant-numeric: tabular-nums;
  }

  .card-value.green { color: var(--green); }
  .card-value.amber { color: var(--amber); }
  .card-value.blue { color: var(--blue); }
  .card-value.red { color: var(--red); }
  .card-value.sky { color: var(--sky); }
  .card-value.purple { color: var(--purple); }

  .card-unit {
    font-size: 16px;
    font-weight: 400;
    color: var(--text-dim);
  }

  .card-desc {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--text-dim);
    margin-top: 4px;
    margin-bottom: 16px;
  }

  /* Progress bar */
  .progress-track {
    background: rgba(255,255,255,0.05);
    border-radius: 4px;
    height: 4px;
    overflow: hidden;
  }

  .progress-fill {
    height: 100%;
    border-radius: 4px;
    transition: width 1s cubic-bezier(0.34,1.56,0.64,1);
  }

  .progress-fill.green { background: linear-gradient(90deg, #166534, var(--green)); }
  .progress-fill.amber { background: linear-gradient(90deg, #92400e, var(--amber)); }
  .progress-fill.blue { background: linear-gradient(90deg, #1e40af, var(--blue)); }
  .progress-fill.sky { background: linear-gradient(90deg, #0369a1, var(--sky)); }

  /* PIR toggle card */
  .pir-state {
    font-size: 18px;
    font-weight: 700;
    margin-bottom: 4px;
  }

  .motion-rings {
    position: absolute;
    right: 20px;
    top: 50%;
    transform: translateY(-50%);
    width: 60px;
    height: 60px;
  }

  .motion-ring {
    position: absolute;
    border-radius: 50%;
    border: 2px solid var(--red);
    opacity: 0;
  }

  .motion-active .motion-ring {
    animation: ring-out 1.5s infinite;
  }

  .motion-ring:nth-child(1) { inset: 20px; animation-delay: 0s !important; }
  .motion-ring:nth-child(2) { inset: 10px; animation-delay: 0.3s !important; }
  .motion-ring:nth-child(3) { inset: 0; animation-delay: 0.6s !important; }

  @keyframes ring-out {
    0% { opacity: 0.8; transform: scale(0.5); }
    100% { opacity: 0; transform: scale(1.5); }
  }

  /* Rain indicator */
  .rain-drops {
    display: flex;
    gap: 6px;
    margin-top: 12px;
  }

  .raindrop {
    width: 6px;
    border-radius: 3px;
    background: var(--sky);
    opacity: 0.3;
    transition: height 0.5s, opacity 0.5s;
  }

  .raindrop.active {
    opacity: 1;
    animation: drip 1.5s infinite;
  }

  @keyframes drip {
    0%, 100% { transform: translateY(0); }
    50% { transform: translateY(4px); }
  }

  /* Bottom sections */
  .bottom-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 20px;
  }

  @media (max-width: 768px) {
    .bottom-grid { grid-template-columns: 1fr; }
    main { padding: 24px 16px; }
    header { padding: 16px 20px; }
    .sensor-grid { grid-template-columns: 1fr 1fr; gap: 12px; }
  }

  @media (max-width: 480px) {
    .sensor-grid { grid-template-columns: 1fr; }
  }

  /* History / mini chart */
  .section-card {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 16px;
    padding: 24px;
  }

  .section-title {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--text-dim);
    letter-spacing: 2px;
    text-transform: uppercase;
    margin-bottom: 20px;
    display: flex;
    align-items: center;
    gap: 8px;
  }

  .section-title::after {
    content: '';
    flex: 1;
    height: 1px;
    background: var(--border);
  }

  .mini-chart {
    display: flex;
    align-items: flex-end;
    gap: 4px;
    height: 60px;
  }

  .bar {
    flex: 1;
    border-radius: 3px 3px 0 0;
    transition: height 0.8s cubic-bezier(0.34,1.56,0.64,1);
    min-height: 4px;
  }

  .bar.green { background: linear-gradient(180deg, var(--green), rgba(74,222,128,0.3)); }
  .bar.blue { background: linear-gradient(180deg, var(--blue), rgba(96,165,250,0.3)); }
  .bar.amber { background: linear-gradient(180deg, var(--amber), rgba(251,191,36,0.3)); }

  .chart-labels {
    display: flex;
    justify-content: space-between;
    margin-top: 8px;
  }

  .chart-label {
    font-family: var(--mono);
    font-size: 9px;
    color: var(--text-dim);
  }

  /* Log */
  .log-list {
    display: flex;
    flex-direction: column;
    gap: 8px;
    max-height: 200px;
    overflow-y: auto;
  }

  .log-list::-webkit-scrollbar { width: 4px; }
  .log-list::-webkit-scrollbar-track { background: transparent; }
  .log-list::-webkit-scrollbar-thumb { background: var(--border); border-radius: 2px; }

  .log-item {
    display: flex;
    align-items: flex-start;
    gap: 12px;
    font-family: var(--mono);
    font-size: 11px;
    line-height: 1.5;
  }

  .log-time { color: var(--text-dim); white-space: nowrap; min-width: 72px; }
  .log-msg { color: var(--text); }
  .log-dot { width: 6px; height: 6px; border-radius: 50%; margin-top: 5px; flex-shrink: 0; }

  /* Input panel */
  .input-panel {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: 16px;
    padding: 28px;
    margin-bottom: 24px;
  }

  .input-panel h3 {
    font-size: 16px;
    font-weight: 700;
    color: var(--text);
    margin-bottom: 6px;
  }

  .input-panel p {
    font-family: var(--mono);
    font-size: 11px;
    color: var(--text-dim);
    margin-bottom: 24px;
    line-height: 1.7;
  }

  .inputs-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    gap: 16px;
    margin-bottom: 20px;
  }

  .input-group label {
    display: block;
    font-family: var(--mono);
    font-size: 10px;
    color: var(--text-dim);
    letter-spacing: 1.5px;
    text-transform: uppercase;
    margin-bottom: 8px;
  }

  .input-group input,
  .input-group select {
    width: 100%;
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: 8px;
    padding: 10px 14px;
    color: var(--text);
    font-family: var(--mono);
    font-size: 13px;
    outline: none;
    transition: border-color 0.2s, box-shadow 0.2s;
  }

  .input-group input:focus,
  .input-group select:focus {
    border-color: var(--green);
    box-shadow: 0 0 0 3px rgba(74,222,128,0.1);
  }

  .input-group select option {
    background: var(--card);
  }

  .update-btn {
    display: inline-flex;
    align-items: center;
    gap: 8px;
    padding: 12px 24px;
    background: var(--green);
    color: #0a0f0a;
    border: none;
    border-radius: 8px;
    font-family: var(--mono);
    font-size: 12px;
    font-weight: 700;
    letter-spacing: 1px;
    cursor: pointer;
    transition: transform 0.15s, box-shadow 0.2s;
  }

  .update-btn:hover {
    transform: translateY(-1px);
    box-shadow: 0 4px 20px rgba(74,222,128,0.3);
  }

  .update-btn:active { transform: translateY(0); }

  .controller-badge {
    display: inline-flex;
    align-items: center;
    gap: 6px;
    padding: 6px 12px;
    background: rgba(192,132,252,0.1);
    border: 1px solid rgba(192,132,252,0.2);
    border-radius: 20px;
    font-family: var(--mono);
    font-size: 10px;
    color: var(--purple);
    margin-bottom: 20px;
  }

  footer {
    position: relative;
    z-index: 1;
    text-align: center;
    padding: 24px;
    border-top: 1px solid var(--border);
    font-family: var(--mono);
    font-size: 11px;
    color: var(--text-dim);
  }

  /* Tooltip */
  .card-tooltip {
    font-family: var(--mono);
    font-size: 10px;
    color: var(--text-dim);
    margin-top: 8px;
    padding-top: 12px;
    border-top: 1px solid var(--border);
    display: flex;
    justify-content: space-between;
  }

  /* Fade in animation for cards */
  .card, .section-card, .input-panel {
    animation: fade-up 0.5s ease backwards;
  }

  .card:nth-child(1) { animation-delay: 0.05s; }
  .card:nth-child(2) { animation-delay: 0.1s; }
  .card:nth-child(3) { animation-delay: 0.15s; }
  .card:nth-child(4) { animation-delay: 0.2s; }
  .card:nth-child(5) { animation-delay: 0.25s; }
  .card:nth-child(6) { animation-delay: 0.3s; }

  @keyframes fade-up {
    from { opacity: 0; transform: translateY(20px); }
    to { opacity: 1; transform: translateY(0); }
  }
</style>
</head>
<body>

<header>
  <div class="logo">
    <div class="logo-icon">🌿</div>
    <div class="logo-text">Agri<span>Sense</span></div>
  </div>
  <div class="header-status">
    <div class="status-dot"></div>
    <span>LIVE MONITORING</span>
  </div>
  <div class="header-right">
    <div class="bt-badge">🔵 BLUETOOTH</div>
    <div class="timestamp" id="clock">--:--:-- --</div>
  </div>
</header>

<main>
  <div class="hero">
    <div class="hero-label">// ARDUINO NANO CONTROLLER</div>
    <h1 class="hero-title">Smart <span>Agricultural</span><br>Monitoring System</h1>
    <p class="hero-sub">Real-time sensor data from your farm. Monitor temperature, humidity, soil moisture, rain, light, and security — all from one dashboard.</p>
  </div>

  <!-- Alert bar -->
  <div class="alert-bar" id="alertBar">
    <span id="alertIcon">⚠️</span>
    <span id="alertText">Alert message here</span>
  </div>

  <!-- Controller badge -->
  <div class="controller-badge">⚡ Arduino Nano · ATmega328P · 16 MHz · Bluetooth Module</div>

  <!-- Sensor Cards -->
  <div class="sensor-grid">

    <!-- Temperature -->
    <div class="card green" id="tempCard">
      <div class="card-header">
        <div class="card-icon green">🌡️</div>
        <span class="card-status-badge badge-normal" id="tempBadge">NORMAL</span>
      </div>
      <div class="card-label">Temperature</div>
      <div class="card-value green" id="tempVal">28.4 <span class="card-unit">°C</span></div>
      <div class="card-desc" id="tempDesc">Optimal for most crops</div>
      <div class="progress-track"><div class="progress-fill green" id="tempBar" style="width:57%"></div></div>
      <div class="card-tooltip">
        <span>MIN 15°C</span><span id="tempRange">28.4°C</span><span>MAX 50°C</span>
      </div>
    </div>

    <!-- Humidity -->
    <div class="card blue" id="humCard">
      <div class="card-header">
        <div class="card-icon blue">💧</div>
        <span class="card-status-badge badge-normal" id="humBadge">NORMAL</span>
      </div>
      <div class="card-label">Humidity</div>
      <div class="card-value blue" id="humVal">62 <span class="card-unit">%</span></div>
      <div class="card-desc" id="humDesc">Good moisture in air</div>
      <div class="progress-track"><div class="progress-fill blue" id="humBar" style="width:62%"></div></div>
      <div class="card-tooltip">
        <span>DRY 0%</span><span id="humRange">62%</span><span>WET 100%</span>
      </div>
    </div>

    <!-- Soil Moisture -->
    <div class="card amber" id="soilCard">
      <div class="card-header">
        <div class="card-icon amber">🌱</div>
        <span class="card-status-badge badge-normal" id="soilBadge">MOIST</span>
      </div>
      <div class="card-label">Soil Moisture</div>
      <div class="card-value amber" id="soilVal">74 <span class="card-unit">%</span></div>
      <div class="card-desc" id="soilDesc">Good water content</div>
      <div class="progress-track"><div class="progress-fill amber" id="soilBar" style="width:74%"></div></div>
      <div class="card-tooltip">
        <span>DRY 0%</span><span id="soilRange">74%</span><span>WET 100%</span>
      </div>
    </div>

    <!-- Light Intensity -->
    <div class="card amber" id="lightCard">
      <div class="card-header">
        <div class="card-icon amber">☀️</div>
        <span class="card-status-badge badge-normal" id="lightBadge">BRIGHT</span>
      </div>
      <div class="card-label">Light Intensity (LDR)</div>
      <div class="card-value amber" id="lightVal">820 <span class="card-unit">lux</span></div>
      <div class="card-desc" id="lightDesc">Bright sunlight</div>
      <div class="progress-track"><div class="progress-fill amber" id="lightBar" style="width:82%"></div></div>
      <div class="card-tooltip">
        <span>DARK 0</span><span id="lightRange">820 lux</span><span>BRIGHT 1000</span>
      </div>
    </div>

    <!-- Rain Sensor -->
    <div class="card sky" id="rainCard">
      <div class="card-header">
        <div class="card-icon sky">🌧️</div>
        <span class="card-status-badge badge-clear" id="rainBadge">NO RAIN</span>
      </div>
      <div class="card-label">Rain Sensor</div>
      <div class="pir-state card-value sky" id="rainVal" style="font-size:28px;">DRY</div>
      <div class="card-desc" id="rainDesc">No rainfall detected</div>
      <div class="rain-drops">
        <div class="raindrop" style="height:20px" id="r1"></div>
        <div class="raindrop" style="height:28px" id="r2"></div>
        <div class="raindrop" style="height:16px" id="r3"></div>
        <div class="raindrop" style="height:24px" id="r4"></div>
        <div class="raindrop" style="height:18px" id="r5"></div>
        <div class="raindrop" style="height:30px" id="r6"></div>
        <div class="raindrop" style="height:22px" id="r7"></div>
      </div>
    </div>

    <!-- PIR Motion -->
    <div class="card red" id="pirCard">
      <div class="card-header">
        <div class="card-icon red">🚨</div>
        <span class="card-status-badge badge-clear" id="pirBadge">CLEAR</span>
      </div>
      <div class="card-label">PIR Motion Sensor</div>
      <div class="pir-state card-value red" id="pirVal" style="font-size:28px;">NO MOTION</div>
      <div class="card-desc" id="pirDesc">Farm perimeter secure</div>
      <div class="motion-rings" id="motionRings">
        <div class="motion-ring"></div>
        <div class="motion-ring"></div>
        <div class="motion-ring"></div>
      </div>
    </div>

  </div><!-- end sensor grid -->

  <!-- Data Input Panel -->
  <div class="input-panel">
    <h3>📡 Update Sensor Readings</h3>
    <p>Enter data received from Arduino Nano via Bluetooth. In a live setup, this would be auto-populated from your Bluetooth module.</p>
    <div class="inputs-grid">
      <div class="input-group">
        <label>Temperature (°C)</label>
        <input type="number" id="inTemp" placeholder="e.g. 28.4" min="-10" max="60" step="0.1">
      </div>
      <div class="input-group">
        <label>Humidity (%)</label>
        <input type="number" id="inHum" placeholder="e.g. 62" min="0" max="100">
      </div>
      <div class="input-group">
        <label>Soil Moisture (%)</label>
        <input type="number" id="inSoil" placeholder="e.g. 74" min="0" max="100">
      </div>
      <div class="input-group">
        <label>Light (0–1000 lux)</label>
        <input type="number" id="inLight" placeholder="e.g. 820" min="0" max="1000">
      </div>
      <div class="input-group">
        <label>Rain Detected</label>
        <select id="inRain">
          <option value="0">No Rain (Dry)</option>
          <option value="1">Rain Detected</option>
        </select>
      </div>
      <div class="input-group">
        <label>Motion Detected</label>
        <select id="inPir">
          <option value="0">No Motion</option>
          <option value="1">Motion Detected!</option>
        </select>
      </div>
    </div>
    <button class="update-btn" onclick="updateDashboard()">⚡ UPDATE DASHBOARD</button>
  </div>

  <!-- Charts + Log -->
  <div class="bottom-grid">
    <div class="section-card">
      <div class="section-title">Temperature History (°C)</div>
      <div class="mini-chart" id="tempChart"></div>
      <div class="chart-labels" id="tempChartLabels"></div>
    </div>

    <div class="section-card">
      <div class="section-title">Event Log</div>
      <div class="log-list" id="logList">
        <div class="log-item">
          <span class="log-dot" style="background:var(--green)"></span>
          <span class="log-time" id="initTime">--:--:--</span>
          <span class="log-msg">System initialized. Monitoring started.</span>
        </div>
      </div>
    </div>
  </div>

</main>

<footer>
  AgriSense &nbsp;·&nbsp; Arduino Nano · DHT11/22 · Soil Sensor · Rain Module · LDR · PIR &nbsp;·&nbsp; Bluetooth Data Feed
</footer>

<script>
// Clock
function updateClock() {
  const now = new Date();
  document.getElementById('clock').textContent = now.toLocaleTimeString('en-IN', { hour12: true });
  document.getElementById('initTime').textContent = now.toLocaleTimeString('en-IN', { hour12: false });
}
updateClock();
setInterval(updateClock, 1000);

// State
let tempHistory = [26, 27, 28, 27.5, 28.4, 29, 28.4];

function getTime() {
  return new Date().toLocaleTimeString('en-IN', { hour12: false });
}

function addLog(msg, color = '#4ade80') {
  const list = document.getElementById('logList');
  const item = document.createElement('div');
  item.className = 'log-item';
  item.innerHTML = `<span class="log-dot" style="background:${color}"></span><span class="log-time">${getTime()}</span><span class="log-msg">${msg}</span>`;
  list.prepend(item);
  // Keep max 12 logs
  while (list.children.length > 12) list.removeChild(list.lastChild);
}

function showAlert(msg, type = 'warning') {
  const bar = document.getElementById('alertBar');
  const icons = { warning: '⚠️', danger: '🚨', info: '✅' };
  document.getElementById('alertIcon').textContent = icons[type] || '⚠️';
  document.getElementById('alertText').textContent = msg;
  bar.className = `alert-bar active ${type}`;
  setTimeout(() => { bar.className = 'alert-bar'; }, 6000);
}

function updateDashboard() {
  const temp = parseFloat(document.getElementById('inTemp').value);
  const hum = parseFloat(document.getElementById('inHum').value);
  const soil = parseFloat(document.getElementById('inSoil').value);
  const light = parseFloat(document.getElementById('inLight').value);
  const rain = parseInt(document.getElementById('inRain').value);
  const pir = parseInt(document.getElementById('inPir').value);

  // Temperature
  if (!isNaN(temp)) {
    document.getElementById('tempVal').innerHTML = `${temp.toFixed(1)} <span class="card-unit">°C</span>`;
    document.getElementById('tempRange').textContent = temp.toFixed(1) + '°C';
    const pct = Math.min(100, Math.max(0, ((temp - 15) / 35) * 100));
    document.getElementById('tempBar').style.width = pct + '%';
    const badge = document.getElementById('tempBadge');
    if (temp > 38) {
      badge.textContent = 'HOT'; badge.className = 'card-status-badge badge-alert';
      document.getElementById('tempDesc').textContent = 'Too hot — risk of crop stress';
      showAlert(`High Temperature: ${temp}°C — Consider irrigation or shade`, 'danger');
      addLog(`🌡️ HIGH TEMP: ${temp}°C`, 'var(--red)');
    } else if (temp < 15) {
      badge.textContent = 'COLD'; badge.className = 'card-status-badge badge-warning';
      document.getElementById('tempDesc').textContent = 'Cold — monitor frost risk';
    } else {
      badge.textContent = 'NORMAL'; badge.className = 'card-status-badge badge-normal';
      document.getElementById('tempDesc').textContent = 'Optimal for most crops';
    }
    tempHistory.push(temp);
    if (tempHistory.length > 7) tempHistory.shift();
    renderTempChart();
    addLog(`🌡️ Temperature updated: ${temp}°C`);
  }

  // Humidity
  if (!isNaN(hum)) {
    document.getElementById('humVal').innerHTML = `${hum} <span class="card-unit">%</span>`;
    document.getElementById('humRange').textContent = hum + '%';
    document.getElementById('humBar').style.width = hum + '%';
    const badge = document.getElementById('humBadge');
    if (hum > 85) {
      badge.textContent = 'HIGH'; badge.className = 'card-status-badge badge-warning';
      document.getElementById('humDesc').textContent = 'High humidity — disease risk';
    } else if (hum < 30) {
      badge.textContent = 'LOW'; badge.className = 'card-status-badge badge-warning';
      document.getElementById('humDesc').textContent = 'Low humidity — dry conditions';
    } else {
      badge.textContent = 'NORMAL'; badge.className = 'card-status-badge badge-normal';
      document.getElementById('humDesc').textContent = 'Good moisture in air';
    }
    addLog(`💧 Humidity updated: ${hum}%`);
  }

  // Soil
  if (!isNaN(soil)) {
    document.getElementById('soilVal').innerHTML = `${soil} <span class="card-unit">%</span>`;
    document.getElementById('soilRange').textContent = soil + '%';
    document.getElementById('soilBar').style.width = soil + '%';
    const badge = document.getElementById('soilBadge');
    if (soil < 20) {
      badge.textContent = 'DRY'; badge.className = 'card-status-badge badge-alert';
      document.getElementById('soilDesc').textContent = 'Soil is dry — irrigate now!';
      showAlert(`Soil Moisture Critical: ${soil}% — Start irrigation`, 'danger');
      addLog(`🌱 DRY SOIL: ${soil}% — Irrigation needed`, 'var(--red)');
    } else if (soil > 90) {
      badge.textContent = 'WATERLOGGED'; badge.className = 'card-status-badge badge-warning';
      document.getElementById('soilDesc').textContent = 'Over-watered — stop irrigation';
    } else {
      badge.textContent = 'MOIST'; badge.className = 'card-status-badge badge-normal';
      document.getElementById('soilDesc').textContent = 'Good water content';
    }
    addLog(`🌱 Soil moisture updated: ${soil}%`);
  }

  // Light
  if (!isNaN(light)) {
    document.getElementById('lightVal').innerHTML = `${light} <span class="card-unit">lux</span>`;
    document.getElementById('lightRange').textContent = light + ' lux';
    document.getElementById('lightBar').style.width = (light / 10) + '%';
    const badge = document.getElementById('lightBadge');
    if (light < 100) {
      badge.textContent = 'DARK'; badge.className = 'card-status-badge badge-warning';
      document.getElementById('lightDesc').textContent = 'Low light — check crop needs';
    } else if (light < 400) {
      badge.textContent = 'DIM'; badge.className = 'card-status-badge badge-warning';
      document.getElementById('lightDesc').textContent = 'Partial light conditions';
    } else if (light > 800) {
      badge.textContent = 'BRIGHT'; badge.className = 'card-status-badge badge-normal';
      document.getElementById('lightDesc').textContent = 'Bright sunlight';
    } else {
      badge.textContent = 'MODERATE'; badge.className = 'card-status-badge badge-normal';
      document.getElementById('lightDesc').textContent = 'Moderate light conditions';
    }
    addLog(`☀️ Light intensity updated: ${light} lux`);
  }

  // Rain
  const rainDrops = document.querySelectorAll('.raindrop');
  const rainBadge = document.getElementById('rainBadge');
  if (rain === 1) {
    document.getElementById('rainVal').textContent = 'RAINING';
    rainBadge.textContent = 'RAIN'; rainBadge.className = 'card-status-badge badge-warning';
    document.getElementById('rainDesc').textContent = 'Rainfall detected';
    rainDrops.forEach(d => d.classList.add('active'));
    showAlert('Rain detected! Adjust irrigation and protect sensitive crops.', 'warning');
    addLog('🌧️ Rain detected!', 'var(--sky)');
  } else {
    document.getElementById('rainVal').textContent = 'DRY';
    rainBadge.textContent = 'NO RAIN'; rainBadge.className = 'card-status-badge badge-clear';
    document.getElementById('rainDesc').textContent = 'No rainfall detected';
    rainDrops.forEach(d => d.classList.remove('active'));
    addLog('🌧️ Rain sensor: No rain');
  }

  // PIR
  const rings = document.getElementById('motionRings');
  const pirBadge = document.getElementById('pirBadge');
  if (pir === 1) {
    document.getElementById('pirVal').textContent = '⚡ MOTION!';
    pirBadge.textContent = 'ALERT'; pirBadge.className = 'card-status-badge badge-active';
    document.getElementById('pirDesc').textContent = 'Intruder or animal detected!';
    rings.classList.add('motion-active');
    showAlert('🚨 Motion Detected on Farm! Check security immediately.', 'danger');
    addLog('🚨 MOTION DETECTED — Security alert!', 'var(--red)');
  } else {
    document.getElementById('pirVal').textContent = 'NO MOTION';
    pirBadge.textContent = 'CLEAR'; pirBadge.className = 'card-status-badge badge-clear';
    document.getElementById('pirDesc').textContent = 'Farm perimeter secure';
    rings.classList.remove('motion-active');
    addLog('🔒 PIR: No motion — Farm secure');
  }
}

function renderTempChart() {
  const container = document.getElementById('tempChart');
  const labels = document.getElementById('tempChartLabels');
  const max = Math.max(...tempHistory, 35);
  container.innerHTML = '';
  labels.innerHTML = '';

  tempHistory.forEach((v, i) => {
    const bar = document.createElement('div');
    bar.className = 'bar green';
    const h = (v / max) * 60;
    bar.style.height = h + 'px';
    bar.title = v + '°C';
    container.appendChild(bar);
    const lbl = document.createElement('span');
    lbl.className = 'chart-label';
    lbl.textContent = 'T' + (i + 1);
    labels.appendChild(lbl);
  });
}

// Initial chart render
renderTempChart();
</script>
</body>
</html>
