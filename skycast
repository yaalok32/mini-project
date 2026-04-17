<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>SkyCast &mdash; Weather Intelligence</title>
<link href="https://fonts.googleapis.com/css2?family=Syne:wght@700;800&family=DM+Sans:ital,opsz,wght@0,9..40,300;0,9..40,400;0,9..40,500;1,9..40,300&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"/>
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<style>
  :root {
    --bg: #0a0e1a;
    --bg2: #0f1525;
    --card: rgba(255,255,255,0.04);
    --card-h: rgba(255,255,255,0.07);
    --border: rgba(255,255,255,0.08);
    --border-b: rgba(255,255,255,0.15);
    --text: #e8eaf2;
    --text-d: #7b82a0;
    --text-m: #4a5070;
    --accent: #4f8ef7;
    --accent2: #7c5cf7;
    --accent3: #f75c8d;
    --warm: #f7a25c;
    --green: #5cf7a2;
    --yellow: #f7e25c;
    --radius: 20px;
    --radius-s: 12px;
  }
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body { font-family: 'DM Sans', sans-serif; background: var(--bg); color: var(--text); min-height: 100vh; overflow-x: hidden; }

  /* Background orbs */
  .orbs { position: fixed; inset: 0; pointer-events: none; z-index: 0; overflow: hidden; }
  .orb { position: absolute; border-radius: 50%; filter: blur(120px); opacity: 0.12; animation: drift 20s ease-in-out infinite alternate; }
  .o1 { width: 600px; height: 600px; background: #4f8ef7; top: -200px; right: -100px; }
  .o2 { width: 500px; height: 500px; background: #7c5cf7; bottom: -100px; left: -100px; animation-delay: -10s; }
  .o3 { width: 300px; height: 300px; background: #f75c8d; top: 50%; left: 40%; animation-delay: -5s; }
  @keyframes drift { to { transform: translate(40px, 30px) scale(1.05); } }

  /* Layout */
  #app { position: relative; z-index: 1; max-width: 1400px; margin: 0 auto; padding: 24px 24px 60px; }

  /* Header */
  header { display: flex; align-items: center; gap: 20px; margin-bottom: 32px; flex-wrap: wrap; }
  .logo { font-family: 'Syne', sans-serif; font-size: 26px; font-weight: 800; white-space: nowrap; letter-spacing: -0.5px; }
  .logo span { color: var(--accent); }
  .search-wrap { flex: 1; min-width: 260px; max-width: 520px; position: relative; }
  .search-wrap input { width: 100%; background: var(--card); border: 1px solid var(--border); border-radius: 50px; padding: 14px 60px 14px 22px; color: var(--text); font-family: 'DM Sans', sans-serif; font-size: 15px; outline: none; transition: all 0.2s; }
  .search-wrap input:focus { border-color: var(--accent); background: var(--card-h); }
  .search-wrap input::placeholder { color: var(--text-m); }
  .search-btn { position: absolute; right: 8px; top: 50%; transform: translateY(-50%); background: var(--accent); border: none; border-radius: 50px; width: 42px; height: 42px; cursor: pointer; font-size: 16px; transition: all 0.2s; display: flex; align-items: center; justify-content: center; }
  .search-btn:hover { background: #6fa4ff; transform: translateY(-50%) scale(1.05); }
  .unit-toggle { display: flex; background: var(--card); border: 1px solid var(--border); border-radius: 50px; overflow: hidden; }
  .unit-btn { padding: 10px 18px; cursor: pointer; font-size: 14px; font-weight: 500; color: var(--text-d); border: none; background: transparent; font-family: inherit; transition: all 0.2s; }
  .unit-btn.active { background: var(--accent); color: #fff; }

  /* Grid */
  .main-grid { display: grid; grid-template-columns: 340px 1fr; gap: 20px; }
  .left-col, .right-col { display: flex; flex-direction: column; gap: 20px; }

  /* Cards */
  .card { background: var(--card); border: 1px solid var(--border); border-radius: var(--radius); padding: 24px; transition: all 0.2s; backdrop-filter: blur(10px); }
  .card:hover { border-color: var(--border-b); background: var(--card-h); }
  .card-title { font-family: 'Syne', sans-serif; font-size: 11px; font-weight: 600; letter-spacing: 1.5px; text-transform: uppercase; color: var(--text-d); margin-bottom: 16px; }

  /* Current weather card */
  .current-card { padding: 28px; }
  .city-name { font-family: 'Syne', sans-serif; font-size: 22px; font-weight: 700; margin-bottom: 4px; }
  .city-date { font-size: 13px; color: var(--text-d); margin-bottom: 24px; }
  .weather-icon { font-size: 64px; margin-bottom: 8px; display: block; }
  .temp-big { font-family: 'Syne', sans-serif; font-size: 80px; font-weight: 800; line-height: 1; letter-spacing: -4px; margin-bottom: 4px; }
  .temp-big sup { font-size: 36px; letter-spacing: 0; vertical-align: super; }
  .weather-desc { font-size: 18px; font-weight: 500; color: var(--text-d); margin-bottom: 20px; text-transform: capitalize; }
  .stats-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; margin-top: 16px; }
  .stat-item { background: rgba(255,255,255,0.03); border: 1px solid var(--border); border-radius: var(--radius-s); padding: 14px; transition: background 0.2s; }
  .stat-item:hover { background: rgba(255,255,255,0.06); }
  .stat-label { font-size: 11px; color: var(--text-m); text-transform: uppercase; letter-spacing: 1px; margin-bottom: 6px; }
  .stat-value { font-family: 'Syne', sans-serif; font-size: 20px; font-weight: 700; }
  .stat-sub { font-size: 12px; color: var(--text-d); margin-top: 2px; }

  /* Sun arc */
  .sun-arc-wrap { position: relative; width: 100%; height: 80px; margin: 12px 0; }
  .sun-arc-wrap svg { width: 100%; height: 100%; }
  .sun-times { display: flex; justify-content: space-between; font-size: 13px; color: var(--text-d); }

  /* Score ring */
  .score-wrap { display: flex; align-items: center; gap: 20px; }
  .score-ring { width: 90px; height: 90px; flex-shrink: 0; position: relative; }
  .score-ring svg { width: 100%; height: 100%; transform: rotate(-90deg); }
  .score-ring .track { fill: none; stroke: rgba(255,255,255,0.08); stroke-width: 7; }
  .score-ring .fill { fill: none; stroke-width: 7; stroke-linecap: round; transition: stroke-dashoffset 1s ease; }
  .score-num { position: absolute; inset: 0; display: flex; flex-direction: column; align-items: center; justify-content: center; font-family: 'Syne', sans-serif; font-size: 22px; font-weight: 800; }
  .score-num small { font-size: 10px; font-weight: 400; color: var(--text-d); }
  .score-info h3 { font-family: 'Syne', sans-serif; font-size: 17px; font-weight: 700; margin-bottom: 6px; }
  .score-info p { font-size: 13px; color: var(--text-d); line-height: 1.5; }

  /* AQI */
  .aqi-value { font-family: 'Syne', sans-serif; font-size: 32px; font-weight: 800; }
  .aqi-category { font-size: 14px; font-weight: 500; margin-top: 2px; }
  .aqi-bar-wrap { margin: 12px 0; }
  .aqi-bar { height: 8px; border-radius: 4px; background: linear-gradient(to right, #5cf7a2, #f7e25c, #f7a25c, #f75c5c, #8b5cf6); position: relative; margin-bottom: 8px; }
  .aqi-dot { position: absolute; top: 50%; transform: translate(-50%, -50%); width: 16px; height: 16px; border-radius: 50%; background: #fff; border: 3px solid var(--bg); box-shadow: 0 0 0 2px currentColor; transition: left 0.8s ease; }
  .aqi-labels { display: flex; justify-content: space-between; font-size: 10px; color: var(--text-m); }
  .aqi-advice { font-size: 13px; color: var(--text-d); margin-top: 10px; line-height: 1.5; }

  /* Hourly scroll */
  .hourly-scroll { display: flex; gap: 12px; overflow-x: auto; padding-bottom: 8px; scroll-snap-type: x mandatory; }
  .hourly-scroll::-webkit-scrollbar { height: 4px; }
  .hourly-scroll::-webkit-scrollbar-thumb { background: var(--border-b); border-radius: 2px; }
  .hour-chip { flex-shrink: 0; scroll-snap-align: start; background: rgba(255,255,255,0.03); border: 1px solid var(--border); border-radius: var(--radius-s); padding: 14px 16px; text-align: center; min-width: 82px; transition: all 0.2s; cursor: default; }
  .hour-chip:hover { background: rgba(255,255,255,0.08); border-color: var(--border-b); transform: translateY(-3px); }
  .hour-chip.now { background: rgba(79,142,247,0.15); border-color: var(--accent); }
  .hour-time { font-size: 12px; color: var(--text-d); margin-bottom: 8px; }
  .hour-icon { font-size: 22px; margin-bottom: 8px; }
  .hour-temp { font-family: 'Syne', sans-serif; font-size: 16px; font-weight: 700; }
  .hour-rain { font-size: 11px; color: var(--accent); margin-top: 4px; }

  /* Forecast */
  .forecast-list { display: flex; flex-direction: column; gap: 8px; }
  .forecast-row { display: grid; grid-template-columns: 120px 40px 1fr 80px; align-items: center; gap: 16px; background: rgba(255,255,255,0.03); border: 1px solid var(--border); border-radius: var(--radius-s); padding: 14px 18px; cursor: pointer; transition: all 0.2s; overflow: hidden; }
  .forecast-row:hover { background: rgba(255,255,255,0.07); border-color: var(--border-b); }
  .forecast-expand { grid-column: 1 / -1; display: none; padding-top: 12px; border-top: 1px solid var(--border); margin-top: 8px; }
  .forecast-expand.open { display: grid; grid-template-columns: repeat(4, 1fr); gap: 12px; }
  .exp-stat { text-align: center; }
  .exp-stat .exp-label { font-size: 11px; color: var(--text-m); text-transform: uppercase; letter-spacing: 1px; }
  .exp-stat .exp-value { font-family: 'Syne', sans-serif; font-size: 18px; font-weight: 700; margin-top: 4px; }
  .forecast-bar-wrap { display: flex; align-items: center; gap: 8px; }
  .forecast-bar { flex: 1; height: 6px; border-radius: 3px; background: linear-gradient(to right, var(--accent), var(--warm)); }
  .forecast-lo { font-size: 13px; color: var(--text-d); }
  .forecast-hi { font-family: 'Syne', sans-serif; font-size: 16px; font-weight: 700; text-align: right; }

  /* Chart */
  .chart-container { position: relative; height: 200px; }

  /* Map */
  #map-card { display: flex; flex-direction: column; }
  #radar-map { flex: 1; min-height: 360px; width: 100%; border-radius: var(--radius-s); overflow: hidden; z-index: 0; }
  .map-controls { display: flex; gap: 10px; margin-bottom: 14px; flex-wrap: wrap; flex-shrink: 0; }
  .map-btn { padding: 8px 16px; border-radius: 50px; border: 1px solid var(--border); background: transparent; color: var(--text-d); font-family: inherit; font-size: 13px; cursor: pointer; transition: all 0.2s; }
  .map-btn.active, .map-btn:hover { background: var(--accent); color: #fff; border-color: var(--accent); }

  /* Best time */
  .time-grid { display: grid; grid-template-columns: repeat(4, 1fr); gap: 10px; }
  .time-slot { background: rgba(255,255,255,0.03); border: 1px solid var(--border); border-radius: var(--radius-s); padding: 14px 10px; text-align: center; transition: all 0.2s; }
  .time-slot:hover { background: rgba(255,255,255,0.07); }
  .time-slot.best { border-color: var(--green); background: rgba(92,247,162,0.06); }
  .time-slot.best .slot-score { color: var(--green); }
  .slot-period { font-size: 12px; color: var(--text-d); margin-bottom: 6px; }
  .slot-score { font-family: 'Syne', sans-serif; font-size: 22px; font-weight: 800; margin-bottom: 4px; }
  .slot-label { font-size: 11px; color: var(--text-m); }

  /* Suggestions */
  .suggest-item { display: flex; align-items: center; gap: 12px; background: rgba(255,255,255,0.03); border: 1px solid var(--border); border-radius: var(--radius-s); padding: 12px 16px; font-size: 14px; margin-bottom: 10px; transition: background 0.2s; }
  .suggest-item:last-child { margin-bottom: 0; }
  .suggest-item:hover { background: rgba(255,255,255,0.06); }
  .suggest-icon { font-size: 20px; flex-shrink: 0; }

  /* Trip recommendation cards */
  .trip-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(250px, 1fr)); gap: 16px; }
  .trip-card { background: rgba(255,255,255,0.03); border: 1px solid var(--border); border-radius: var(--radius); overflow: hidden; cursor: pointer; transition: all 0.2s; position: relative; }
  .trip-card:hover { transform: translateY(-5px); border-color: var(--border-b); box-shadow: 0 16px 40px rgba(0,0,0,0.35); }
  .trip-card-banner { width: 100%; height: 130px; display: flex; align-items: center; justify-content: center; font-size: 52px; position: relative; }
  .trip-card-body { padding: 16px; }
  .trip-card-name { font-family: 'Syne', sans-serif; font-size: 15px; font-weight: 700; margin-bottom: 2px; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
  .trip-card-location { font-size: 11px; color: var(--text-m); text-transform: uppercase; letter-spacing: 0.5px; margin-bottom: 10px; }
  .trip-card-weather { display: flex; align-items: center; gap: 8px; margin-bottom: 10px; font-size: 13px; color: var(--text-d); }
  .trip-card-temp { font-family: 'Syne', sans-serif; font-size: 18px; font-weight: 800; color: var(--text); }
  .trip-tags { display: flex; flex-wrap: wrap; gap: 6px; margin-bottom: 10px; }
  .trip-tag { padding: 3px 10px; border-radius: 50px; font-size: 11px; font-weight: 500; background: rgba(79,142,247,0.12); border: 1px solid rgba(79,142,247,0.25); color: var(--accent); }
  .trip-tag.green { background: rgba(92,247,162,0.1); border-color: rgba(92,247,162,0.25); color: var(--green); }
  .trip-tag.warm { background: rgba(247,162,92,0.1); border-color: rgba(247,162,92,0.25); color: var(--warm); }
  .trip-badge { position: absolute; top: 10px; right: 10px; background: rgba(10,14,26,0.85); backdrop-filter: blur(6px); border: 1px solid var(--border-b); border-radius: 50px; padding: 4px 10px; font-size: 11px; font-weight: 600; }
  .trip-reason { font-size: 12px; color: var(--text-d); line-height: 1.6; border-top: 1px solid var(--border); padding-top: 8px; }
  .trip-dist { font-size: 11px; color: var(--text-m); margin-top: 6px; }
  .trip-header-row { display: flex; align-items: center; justify-content: space-between; margin-bottom: 16px; flex-wrap: wrap; gap: 10px; }
  .trip-filter-tabs { display: flex; gap: 8px; flex-wrap: wrap; }
  .trip-tab { padding: 6px 14px; border-radius: 50px; border: 1px solid var(--border); background: transparent; color: var(--text-d); font-family: inherit; font-size: 12px; cursor: pointer; transition: all 0.2s; }
  .trip-tab.active, .trip-tab:hover { background: var(--accent2); color: #fff; border-color: var(--accent2); }
  .trip-mode-bar { font-size: 12px; color: var(--text-d); margin-bottom: 14px; padding: 9px 14px; background: rgba(255,255,255,0.03); border-radius: var(--radius-s); border: 1px solid var(--border); line-height: 1.5; }
  .trip-loading { display: flex; align-items: center; gap: 12px; color: var(--text-d); font-size: 13px; padding: 20px 0; }
  .trip-spin { width: 18px; height: 18px; border-radius: 50%; border: 2px solid var(--border); border-top-color: var(--accent); animation: spin 0.8s linear infinite; flex-shrink: 0; }
  .no-results { color: var(--text-d); font-size: 14px; padding: 16px 0; line-height: 1.6; }

  /* Loading overlay */
  .loading-overlay { display: none; position: fixed; inset: 0; z-index: 999; background: rgba(10,14,26,0.85); backdrop-filter: blur(8px); align-items: center; justify-content: center; flex-direction: column; gap: 16px; }
  .loading-overlay.show { display: flex; }
  .big-spinner { width: 48px; height: 48px; border-radius: 50%; border: 4px solid var(--border); border-top-color: var(--accent); animation: spin 0.8s linear infinite; }
  @keyframes spin { to { transform: rotate(360deg); } }
  .loading-text { font-family: 'Syne', sans-serif; font-size: 16px; color: var(--text-d); }

  /* Toast */
  .toast { position: fixed; bottom: 24px; left: 50%; transform: translateX(-50%) translateY(80px); background: #1e2235; border: 1px solid var(--border); border-radius: 50px; padding: 14px 24px; font-size: 14px; z-index: 1000; transition: transform 0.3s cubic-bezier(0.34, 1.56, 0.64, 1); pointer-events: none; white-space: nowrap; }
  .toast.show { transform: translateX(-50%) translateY(0); }
  .toast.err { border-color: var(--accent3); color: var(--accent3); }

  /* Empty / welcome */
  .empty-state { min-height: 60vh; display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 16px; text-align: center; }
  .empty-icon { font-size: 80px; opacity: 0.4; }
  .empty-state h2 { font-family: 'Syne', sans-serif; font-size: 28px; font-weight: 700; }
  .empty-state p { color: var(--text-d); font-size: 16px; max-width: 400px; line-height: 1.7; }
  .city-hints { display: flex; gap: 10px; flex-wrap: wrap; justify-content: center; margin-top: 8px; }
  .city-hint { background: var(--card); border: 1px solid var(--border); border-radius: 50px; padding: 8px 18px; font-size: 14px; cursor: pointer; transition: all 0.2s; color: var(--text-d); }
  .city-hint:hover { background: var(--card-h); border-color: var(--accent); color: var(--text); }

  /* Fade-in animation */
  .fade-in { animation: fadeUp 0.5s ease both; }
  @keyframes fadeUp { from { opacity: 0; transform: translateY(16px); } to { opacity: 1; transform: none; } }

  /* Responsive */
  @media (max-width: 900px) {
    .main-grid { grid-template-columns: 1fr; }
    .time-grid { grid-template-columns: repeat(2, 1fr); }
    .temp-big { font-size: 60px; }
  }
  @media (max-width: 600px) {
    #app { padding: 16px 16px 60px; }
    .forecast-row { grid-template-columns: 90px 32px 1fr 60px; gap: 10px; }
    .forecast-expand.open { grid-template-columns: repeat(2, 1fr); }
    .trip-grid { grid-template-columns: 1fr; }
    .stats-grid { grid-template-columns: 1fr 1fr; }
  }
</style>
</head>
<body>
<div class="orbs"><div class="orb o1"></div><div class="orb o2"></div><div class="orb o3"></div></div>
<div class="loading-overlay" id="loadingOverlay">
  <div class="big-spinner"></div>
  <div class="loading-text">Fetching weather data&hellip;</div>
</div>
<div class="toast" id="toastEl"></div>
<div id="app">

  <!-- HEADER -->
  <header>
    <div class="logo">Sky<span>Cast</span></div>
    <div class="search-wrap">
      <input type="text" id="searchInput" placeholder="Search any city &mdash; Delhi, Kasganj, London&hellip;" autocomplete="off">
      <button class="search-btn" onclick="doSearch()">&#128269;</button>
    </div>
    <div class="unit-toggle">
      <button class="unit-btn active" id="btnC" onclick="setUnit('C')">&#176;C</button>
      <button class="unit-btn" id="btnF" onclick="setUnit('F')">&#176;F</button>
    </div>
  </header>

  <!-- EMPTY STATE -->
  <div class="empty-state" id="emptyState">
    <div class="empty-icon">&#127780;&#65039;</div>
    <h2>Weather Intelligence</h2>
    <p>Search any city worldwide for live weather, 7-day forecasts, AQI, radar maps, smart suggestions and curated nearby trip recommendations based on current conditions.</p>
    <div class="city-hints">
      <div class="city-hint" onclick="loadCity('Delhi')">Delhi</div>
      <div class="city-hint" onclick="loadCity('Kasganj')">Kasganj</div>
      <div class="city-hint" onclick="loadCity('Mumbai')">Mumbai</div>
      <div class="city-hint" onclick="loadCity('London')">London</div>
      <div class="city-hint" onclick="loadCity('New York')">New York</div>
      <div class="city-hint" onclick="loadCity('Tokyo')">Tokyo</div>
    </div>
  </div>

  <!-- MAIN DASHBOARD -->
  <div class="main-grid" id="mainDash" style="display:none">

    <!-- LEFT COLUMN -->
    <div class="left-col">

      <!-- Current Weather -->
      <div class="card current-card fade-in">
        <div class="city-name" id="cityNameEl">&#8212;</div>
        <div class="city-date" id="cityDateEl">&#8212;</div>
        <span class="weather-icon" id="weatherIconEl">&#8212;</span>
        <div class="temp-big" id="tempBigEl">&#8212;<sup id="tempUnitEl">&#176;C</sup></div>
        <div class="weather-desc" id="weatherDescEl">&#8212;</div>
        <div class="stats-grid">
          <div class="stat-item"><div class="stat-label">Feels Like</div><div class="stat-value" id="feelsLikeEl">&#8212;</div></div>
          <div class="stat-item"><div class="stat-label">Humidity</div><div class="stat-value" id="humidityEl">&#8212;</div></div>
          <div class="stat-item"><div class="stat-label">Wind</div><div class="stat-value" id="windEl">&#8212;</div><div class="stat-sub" id="windDirEl">&#8212;</div></div>
          <div class="stat-item"><div class="stat-label">Visibility</div><div class="stat-value" id="visibilityEl">&#8212;</div></div>
          <div class="stat-item"><div class="stat-label">Pressure</div><div class="stat-value" id="pressureEl">&#8212;</div></div>
          <div class="stat-item"><div class="stat-label">UV Index</div><div class="stat-value" id="uviEl">&#8212;</div></div>
        </div>
      </div>

      <!-- Sunrise / Sunset -->
      <div class="card fade-in">
        <div class="card-title">&#9728;&#65039; Sunrise &amp; Sunset</div>
        <div class="sun-arc-wrap">
          <svg viewBox="0 0 300 90">
            <defs>
              <linearGradient id="sunGrad"><stop offset="0%" stop-color="#f7a25c"/><stop offset="100%" stop-color="#f7e25c"/></linearGradient>
              <filter id="sunGlow"><feGaussianBlur stdDeviation="3" result="blur"/><feMerge><feMergeNode in="blur"/><feMergeNode in="SourceGraphic"/></feMerge></filter>
            </defs>
            <path d="M 20 80 Q 150 -30 280 80" fill="none" stroke="rgba(255,255,255,0.1)" stroke-width="2"/>
            <path id="sunArc" d="M 20 80 Q 150 -30 280 80" fill="none" stroke="url(#sunGrad)" stroke-width="2.5" stroke-dasharray="320" stroke-dashoffset="320"/>
            <circle id="sunDot" cx="20" cy="80" r="6" fill="#f7e25c" filter="url(#sunGlow)"/>
            <text id="sunriseLabel" x="20" y="90" font-size="9" fill="#7b82a0" text-anchor="middle">&#8212;</text>
            <text id="sunsetLabel" x="280" y="90" font-size="9" fill="#7b82a0" text-anchor="middle">&#8212;</text>
          </svg>
        </div>
        <div class="sun-times">
          <span>&#127749;&#65039; <strong id="sunriseTimeEl">&#8212;</strong></span>
          <span><strong id="daylightEl">&#8212;</strong> daylight</span>
          <span>&#127751;&#65039; <strong id="sunsetTimeEl">&#8212;</strong></span>
        </div>
      </div>

      <!-- Weather Score -->
      <div class="card fade-in">
        <div class="card-title">&#11088; Weather Impact Score</div>
        <div class="score-wrap">
          <div class="score-ring">
            <svg viewBox="0 0 90 90">
              <circle class="track" cx="45" cy="45" r="38"/>
              <circle class="fill" id="scoreArc" cx="45" cy="45" r="38" stroke-dasharray="238.76" stroke-dashoffset="238.76" stroke="#5cf7a2"/>
            </svg>
            <div class="score-num"><span id="scoreNumEl">&#8212;</span><small>/10</small></div>
          </div>
          <div class="score-info">
            <h3 id="scoreLabelEl">&#8212;</h3>
            <p id="scoreDescEl">&#8212;</p>
          </div>
        </div>
      </div>

      <!-- AQI -->
      <div class="card fade-in">
        <div class="card-title">&#127754;&#65039; Air Quality Index</div>
        <div class="aqi-value" id="aqiValEl">&#8212;</div>
        <div class="aqi-category" id="aqiCatEl">&#8212;</div>
        <div class="aqi-bar-wrap">
          <div class="aqi-bar"><div class="aqi-dot" id="aqiDotEl" style="left:0%"></div></div>
          <div class="aqi-labels"><span>Good</span><span>Moderate</span><span>Unhealthy</span><span>Hazardous</span></div>
        </div>
        <div class="aqi-advice" id="aqiAdviceEl">&#8212;</div>
      </div>

    </div> <!-- end left col -->

    <!-- RIGHT COLUMN -->
    <div class="right-col">

      <!-- Hourly Forecast -->
      <div class="card fade-in">
        <div class="card-title">&#128336; Hourly Forecast &mdash; Next 24 Hours</div>
        <div class="hourly-scroll" id="hourlyScroll"></div>
      </div>

      <!-- Temperature Chart -->
      <div class="card fade-in">
        <div class="card-title">&#128200; Temperature Trend</div>
        <div class="chart-container"><canvas id="tempChart"></canvas></div>
      </div>

      <!-- Best Time -->
      <div class="card fade-in">
        <div class="card-title">&#127807; Best Time to Go Outside</div>
        <div class="time-grid" id="timeGrid"></div>
      </div>

      <!-- Clothing + Activities -->
      <div style="display:grid;grid-template-columns:1fr 1fr;gap:20px" class="fade-in">
        <div class="card">
          <div class="card-title">&#128085; Clothing Suggestions</div>
          <div id="clothingList"></div>
        </div>
        <div class="card">
          <div class="card-title">&#127939; Activity Recommendations</div>
          <div id="activityList"></div>
        </div>
      </div>

      <!-- 7-Day Forecast -->
      <div class="card fade-in">
        <div class="card-title">&#128197; 7-Day Forecast</div>
        <div class="forecast-list" id="forecastList"></div>
      </div>

      <!-- Radar Map -->
      <div class="card fade-in" id="map-card">
        <div class="card-title">&#128506;&#65039; Weather Radar</div>
        <div class="map-controls">
          <button class="map-btn active" onclick="setMapLayer('precipitation_new')">&#127783;&#65039; Precipitation</button>
          <button class="map-btn" onclick="setMapLayer('clouds_new')">&#9925;&#65039; Clouds</button>
          <button class="map-btn" onclick="setMapLayer('wind_new')">&#128168; Wind</button>
          <button class="map-btn" onclick="setMapLayer('temp_new')">&#127777;&#65039; Temperature</button>
        </div>
        <div id="radar-map"></div>
      </div>

      <!-- Trip Recommendations -->
      <div class="card fade-in" id="tripCard">
        <div class="trip-header-row">
          <div class="card-title" style="margin-bottom:0">&#9992;&#65039; Nearby Trip Recommendations</div>
          <div class="trip-filter-tabs" id="tripFilterTabs">
            <button class="trip-tab active" data-filter="all" onclick="filterTrips('all')">All</button>
            <button class="trip-tab" data-filter="temple" onclick="filterTrips('temple')">&#128509; Temple</button>
            <button class="trip-tab" data-filter="nature" onclick="filterTrips('nature')">&#127807; Nature</button>
            <button class="trip-tab" data-filter="heritage" onclick="filterTrips('heritage')">&#127963;&#65039; Heritage</button>
            <button class="trip-tab" data-filter="park" onclick="filterTrips('park')">&#127795; Park</button>
            <button class="trip-tab" data-filter="museum" onclick="filterTrips('museum')">&#127956; Museum</button>
          </div>
        </div>
        <div id="tripContent">
          <div class="trip-loading"><div class="trip-spin"></div><span>Finding nearby attractions&hellip;</span></div>
        </div>
      </div>

    </div> <!-- end right col -->
  </div> <!-- end main grid -->
</div> <!-- end app -->

<script>
// ============================================================
// CONFIGURATION
// ============================================================
const OWM_KEY = '677b667737b76919f6a73ddc7085a6aa';
const GEO_KEY = 'de40972bb3dc4177ab316b9894f97275';
const OWM_BASE = 'https://api.openweathermap.org/data/2.5';

let currentUnit = 'C';
let leafMap = null;
let leafWeatherLayer = null;
let leafMarker = null;
let activeMapLayer = 'precipitation_new';
let chartInstance = null;
let appData = null;
let allTripPlaces = [];
let currentTripFilter = 'all';
const tripCache = new Map(); // key: "lat,lon" → array of places

// ============================================================
// UTILITY FUNCTIONS
// ============================================================
const el = id => document.getElementById(id);
const setText = (id, val) => { const e = el(id); if (e) e.textContent = val; };

function showLoader(show) { el('loadingOverlay').classList.toggle('show', show); }

function showToast(msg, isError = false) {
  const t = el('toastEl');
  t.textContent = msg;
  t.className = 'toast' + (isError ? ' err' : '');
  setTimeout(() => t.classList.add('show'), 50);
  setTimeout(() => t.classList.remove('show'), 3500);
}

function tempDisplay(celsius) {
  if (currentUnit === 'F') return Math.round(celsius * 9 / 5 + 32) + '\u00b0F';
  return Math.round(celsius) + '\u00b0C';
}
function tempRaw(celsius) {
  return currentUnit === 'F' ? Math.round(celsius * 9 / 5 + 32) : Math.round(celsius);
}
function windDisplay(mps) {
  return currentUnit === 'F' ? Math.round(mps * 2.237) + ' mph' : Math.round(mps * 3.6) + ' km/h';
}
function visDisplay(metres) {
  return currentUnit === 'F' ? (metres / 1609).toFixed(1) + ' mi' : (metres / 1000).toFixed(1) + ' km';
}
function compassDir(deg) {
  return ['N','NE','E','SE','S','SW','W','NW'][Math.round((deg || 0) / 45) % 8];
}
function formatTime(unixSec, tzOffset = 0) {
  const d = new Date((unixSec + tzOffset) * 1000);
  let h = d.getUTCHours(), m = d.getUTCMinutes();
  const ampm = h >= 12 ? 'PM' : 'AM';
  h = h % 12 || 12;
  return h + ':' + String(m).padStart(2, '0') + ' ' + ampm;
}
function formatDayName(unixSec) {
  return new Date(unixSec * 1000).toLocaleDateString('en-US', { weekday: 'long' });
}
function weatherEmoji(id, pod) {
  if (id >= 200 && id < 300) return '\u26c8\ufe0f';
  if (id >= 300 && id < 400) return '\ud83c\udf26\ufe0f';
  if (id >= 500 && id < 600) return id < 502 ? '\ud83c\udf27\ufe0f' : '\u26c8\ufe0f';
  if (id >= 600 && id < 700) return '\u2744\ufe0f';
  if (id >= 700 && id < 800) return '\ud83c\udf2b\ufe0f';
  if (id === 800) return pod === 'n' ? '\ud83c\udf19' : '\u2600\ufe0f';
  if (id === 801) return '\ud83c\udf24\ufe0f';
  if (id === 802) return '\u26c5';
  return '\u2601\ufe0f';
}
function uviLabel(v) {
  if (v <= 2) return '(Low)';
  if (v <= 5) return '(Moderate)';
  if (v <= 7) return '(High)';
  if (v <= 10) return '(V.High)';
  return '(Extreme)';
}
function haversineKm(lat1, lon1, lat2, lon2) {
  const R = 6371;
  const dLat = (lat2 - lat1) * Math.PI / 180;
  const dLon = (lon2 - lon1) * Math.PI / 180;
  const a = Math.sin(dLat / 2) ** 2 + Math.cos(lat1 * Math.PI / 180) * Math.cos(lat2 * Math.PI / 180) * Math.sin(dLon / 2) ** 2;
  return Math.round(R * 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a)));
}

// ============================================================
// SEARCH & FETCH
// ============================================================
el('searchInput').addEventListener('keydown', e => { if (e.key === 'Enter') doSearch(); });

function doSearch() {
  const q = el('searchInput').value.trim();
  if (!q) { showToast('Please enter a city name', true); return; }
  loadCity(q);
}
function loadCity(city) {
  el('searchInput').value = city;
  fetchAllWeather(city);
}

async function fetchAllWeather(city) {
  showLoader(true);
  try {
    const cwRes = await fetch(OWM_BASE + '/weather?q=' + encodeURIComponent(city) + '&appid=' + OWM_KEY + '&units=metric');
    if (!cwRes.ok) throw new Error(cwRes.status === 404 ? 'City not found — try another name.' : 'Weather API error. Try again.');
    const cw = await cwRes.json();

    const [fcRes, aqRes] = await Promise.all([
      fetch(OWM_BASE + '/forecast?lat=' + cw.coord.lat + '&lon=' + cw.coord.lon + '&appid=' + OWM_KEY + '&units=metric'),
      fetch(OWM_BASE + '/air_pollution?lat=' + cw.coord.lat + '&lon=' + cw.coord.lon + '&appid=' + OWM_KEY)
    ]);
    const fc = await fcRes.json();
    const aq = await aqRes.json();

    appData = { cw, fc, aq };
    renderDashboard(appData);

    el('emptyState').style.display = 'none';
    el('mainDash').style.display = 'grid';
    document.querySelectorAll('.fade-in').forEach((e, i) => {
      e.style.animation = 'none';
      e.offsetHeight;
      e.style.animation = 'fadeUp 0.5s ease ' + (i * 0.07) + 's both';
    });
  } catch (err) {
    showToast(err.message, true);
  } finally {
    showLoader(false);
  }
}

function setUnit(u) {
  currentUnit = u;
  el('btnC').classList.toggle('active', u === 'C');
  el('btnF').classList.toggle('active', u === 'F');
  setText('tempUnitEl', u === 'F' ? '\u00b0F' : '\u00b0C');
  if (appData) renderDashboard(appData);
}

// ============================================================
// RENDER DASHBOARD
// ============================================================
function renderDashboard({ cw, fc, aq }) {
  renderCurrentWeather(cw);
  renderSunArc(cw);
  renderWeatherScore(cw);
  renderAQI(aq);
  renderHourly(fc, cw.timezone);
  renderForecast(fc);
  renderTempChart(fc);
  renderBestTime(fc, cw.timezone);
  renderSuggestions(cw);
  renderMap(cw.coord.lat, cw.coord.lon);
  renderTripRecommendations(cw);
}

// ── Current Weather ──────────────────────────────────────────
function renderCurrentWeather(cw) {
  const localNow = new Date((Math.floor(Date.now() / 1000) + cw.timezone) * 1000);
  setText('cityNameEl', cw.name + ', ' + cw.sys.country);
  setText('cityDateEl', localNow.toLocaleString('en-US', { weekday: 'long', month: 'long', day: 'numeric', hour: '2-digit', minute: '2-digit', timeZone: 'UTC' }));
  el('weatherIconEl').textContent = weatherEmoji(cw.weather[0].id);
  setText('tempBigEl', tempRaw(cw.main.temp));
  setText('tempUnitEl', currentUnit === 'F' ? '\u00b0F' : '\u00b0C');
  setText('weatherDescEl', cw.weather[0].description);
  setText('feelsLikeEl', tempDisplay(cw.main.feels_like));
  setText('humidityEl', cw.main.humidity + '%');
  setText('windEl', windDisplay(cw.wind.speed));
  setText('windDirEl', compassDir(cw.wind.deg) + ' direction');
  setText('visibilityEl', visDisplay(cw.visibility || 10000));
  setText('pressureEl', cw.main.pressure + ' hPa');
  const uvi = cw.uvi != null ? cw.uvi : '—';
  setText('uviEl', uvi === '—' ? '—' : uvi + ' ' + uviLabel(uvi));
}

// ── Sunrise / Sunset Arc ─────────────────────────────────────
function renderSunArc(cw) {
  const tz = cw.timezone, sr = cw.sys.sunrise, ss = cw.sys.sunset;
  const srStr = formatTime(sr, tz), ssStr = formatTime(ss, tz);
  setText('sunriseTimeEl', srStr);
  setText('sunsetTimeEl', ssStr);
  el('sunriseLabel').textContent = srStr;
  el('sunsetLabel').textContent = ssStr;
  const dur = ss - sr;
  setText('daylightEl', Math.floor(dur / 3600) + 'h ' + Math.floor((dur % 3600) / 60) + 'm');
  const now = Math.floor(Date.now() / 1000);
  const pct = now < sr ? 0 : now > ss ? 1 : (now - sr) / (ss - sr);
  el('sunArc').style.strokeDashoffset = 320 * (1 - pct);
  const x = 20 + 260 * pct;
  const y = 80 + Math.sin(Math.PI * pct) * (-110);
  el('sunDot').setAttribute('cx', x);
  el('sunDot').setAttribute('cy', Math.max(10, y));
}

// ── Weather Score ────────────────────────────────────────────
function renderWeatherScore(cw) {
  const t = cw.main.temp, h = cw.main.humidity, w = cw.wind.speed, id = cw.weather[0].id;
  let score = 10;
  if (t < 0 || t > 38) score -= 3; else if (t < 10 || t > 33) score -= 1.5;
  if (h > 80) score -= 1.5; else if (h > 65) score -= 0.5;
  if (w > 10) score -= 1.5; else if (w > 6) score -= 0.5;
  if (id < 700) score -= 2; else if (id < 800) score -= 0.5;
  score = Math.max(1, Math.min(10, Math.round(score)));
  setText('scoreNumEl', score);
  el('scoreArc').style.strokeDashoffset = 238.76 * (1 - score / 10);
  const colors = [null, '#f75c5c', '#f75c5c', '#f75c5c', '#f7a25c', '#f7a25c', '#f7e25c', '#f7e25c', '#5cf7a2', '#5cf7a2', '#5cf7a2'];
  const labels = [null, 'Very Poor', 'Very Poor', 'Poor', 'Fair', 'Fair', 'Good', 'Good', 'Excellent!', 'Excellent!', 'Perfect!'];
  const descs = {1:'Stay indoors.',2:'Very uncomfortable outside.',3:'Challenging conditions.',4:'Moderate discomfort outside.',5:'Fair conditions — brief outings.',6:'Decent weather for light activities.',7:'Good day for outdoor plans.',8:'Great day — enjoy the outdoors!',9:'Near-perfect conditions outside!',10:'Absolutely perfect weather!'};
  el('scoreArc').style.stroke = colors[score];
  setText('scoreLabelEl', labels[score]);
  setText('scoreDescEl', descs[score]);
}

// ── AQI ──────────────────────────────────────────────────────
function renderAQI(aqData) {
  const aqi = aqData.list[0].main.aqi;
  const pm25 = aqData.list[0].components.pm2_5;
  const aqiNum = Math.round(pm25 * 4.79 || aqi * 40);
  const labels = ['', 'Good', 'Fair', 'Moderate', 'Poor', 'Very Poor'];
  const colors = ['', '#5cf7a2', '#f7e25c', '#f7a25c', '#f75c5c', '#8b5cf6'];
  const advice = ['',
    'Air quality is excellent. Enjoy all outdoor activities freely.',
    'Air quality is acceptable for most people. Unusually sensitive may feel mild effects.',
    'Sensitive groups — children, elderly, respiratory conditions — should reduce outdoor time.',
    'Everyone may begin to experience health effects. Sensitive groups should avoid outdoors.',
    'Health alert: serious effects. Everyone should avoid outdoor exertion.'
  ];
  setText('aqiValEl', aqiNum > 0 ? aqiNum : aqi * 40);
  setText('aqiCatEl', labels[aqi] || 'Unknown');
  el('aqiCatEl').style.color = colors[aqi] || '#e8eaf2';
  setText('aqiAdviceEl', advice[aqi] || '');
  el('aqiDotEl').style.left = Math.min(100, (aqi - 1) / 4 * 100) + '%';
  el('aqiDotEl').style.color = colors[aqi] || '#fff';
}

// ── Hourly Forecast ──────────────────────────────────────────
function renderHourly(fc, tz) {
  const now = Math.floor(Date.now() / 1000);
  const items = fc.list.filter(i => i.dt >= now).slice(0, 8);
  el('hourlyScroll').innerHTML = items.map((it, i) => {
    const pop = Math.round((it.pop || 0) * 100);
    return '<div class="hour-chip' + (i === 0 ? ' now' : '') + '">' +
      '<div class="hour-time">' + formatTime(it.dt, tz) + '</div>' +
      '<div class="hour-icon">' + weatherEmoji(it.weather[0].id) + '</div>' +
      '<div class="hour-temp">' + tempDisplay(it.main.temp) + '</div>' +
      (pop > 0 ? '<div class="hour-rain">&#128167;' + pop + '%</div>' : '') + '</div>';
  }).join('');
}

// ── 7-Day Forecast ───────────────────────────────────────────
function renderForecast(fc) {
  const byDay = {};
  fc.list.forEach(it => {
    const key = new Date(it.dt * 1000).toDateString();
    if (!byDay[key]) byDay[key] = [];
    byDay[key].push(it);
  });
  const dayKeys = Object.keys(byDay).slice(0, 7);
  el('forecastList').innerHTML = dayKeys.map((dk, i) => {
    const entries = byDay[dk];
    const temps = entries.map(e => e.main.temp);
    const hi = Math.max(...temps), lo = Math.min(...temps);
    const mid = entries[Math.floor(entries.length / 2)];
    const pop = Math.round(Math.max(...entries.map(e => e.pop || 0)) * 100);
    const avgHum = Math.round(entries.reduce((a, e) => a + e.main.humidity, 0) / entries.length);
    const avgWind = entries.reduce((a, e) => a + e.wind.speed, 0) / entries.length;
    return '<div class="forecast-row" data-frow="1">' +
      '<div style="font-weight:500">' + (i === 0 ? 'Today' : formatDayName(entries[0].dt)) + '</div>' +
      '<div style="font-size:22px;text-align:center">' + weatherEmoji(mid.weather[0].id) + '</div>' +
      '<div class="forecast-bar-wrap"><span class="forecast-lo">' + tempDisplay(lo) + '</span><div class="forecast-bar"></div></div>' +
      '<div class="forecast-hi">' + tempDisplay(hi) + '</div>' +
      '<div class="forecast-expand">' +
        '<div class="exp-stat"><div class="exp-label">Rain</div><div class="exp-value">' + pop + '%</div></div>' +
        '<div class="exp-stat"><div class="exp-label">Humidity</div><div class="exp-value">' + avgHum + '%</div></div>' +
        '<div class="exp-stat"><div class="exp-label">Wind</div><div class="exp-value">' + windDisplay(avgWind) + '</div></div>' +
        '<div class="exp-stat"><div class="exp-label">Condition</div><div class="exp-value" style="font-size:13px">' + mid.weather[0].description + '</div></div>' +
      '</div></div>';
  }).join('');
  // Click handler for forecast expand/collapse
  el('forecastList').querySelectorAll('.forecast-row').forEach(row => {
    row.addEventListener('click', function() {
      this.querySelector('.forecast-expand').classList.toggle('open');
    });
  });
}
function renderTempChart(fc) {
  const now = Math.floor(Date.now() / 1000);
  const items = fc.list.filter(i => i.dt >= now).slice(0, 8);
  const ctx = el('tempChart').getContext('2d');
  if (chartInstance) chartInstance.destroy();
  chartInstance = new Chart(ctx, {
    type: 'line',
    data: {
      labels: items.map(i => formatTime(i.dt)),
      datasets: [
        { label: 'Temperature', data: items.map(i => tempRaw(i.main.temp)), borderColor: '#4f8ef7', backgroundColor: 'rgba(79,142,247,0.08)', tension: 0.4, fill: true, pointBackgroundColor: '#4f8ef7', pointRadius: 4 },
        { label: 'Feels Like', data: items.map(i => tempRaw(i.main.feels_like)), borderColor: '#f7a25c', backgroundColor: 'transparent', tension: 0.4, fill: false, borderDash: [5, 5], pointRadius: 3, pointBackgroundColor: '#f7a25c' }
      ]
    },
    options: {
      responsive: true, maintainAspectRatio: false,
      plugins: {
        legend: { labels: { color: '#7b82a0', font: { family: 'DM Sans', size: 12 } } },
        tooltip: { backgroundColor: '#141929', borderColor: 'rgba(255,255,255,0.1)', borderWidth: 1, titleColor: '#e8eaf2', bodyColor: '#7b82a0', callbacks: { label: ctx => ctx.dataset.label + ': ' + ctx.parsed.y + (currentUnit === 'F' ? '\u00b0F' : '\u00b0C') } }
      },
      scales: {
        x: { ticks: { color: '#7b82a0', font: { size: 11 } }, grid: { color: 'rgba(255,255,255,0.04)' } },
        y: { ticks: { color: '#7b82a0', font: { size: 11 }, callback: v => v + (currentUnit === 'F' ? '\u00b0F' : '\u00b0C') }, grid: { color: 'rgba(255,255,255,0.04)' } }
      }
    }
  });
}

// ── Best Time ─────────────────────────────────────────────────
function renderBestTime(fc, tz) {
  const periods = [
    { label: 'Morning', hours: [6,7,8,9,10,11], icon: '\ud83c\udf05' },
    { label: 'Afternoon', hours: [12,13,14,15,16,17], icon: '\u2600\ufe0f' },
    { label: 'Evening', hours: [18,19,20,21], icon: '\ud83c\udf06' },
    { label: 'Night', hours: [22,23,0,1,2,3], icon: '\ud83c\udf19' }
  ];
  const now = Math.floor(Date.now() / 1000);
  const items = fc.list.filter(i => i.dt >= now).slice(0, 16);
  const scores = periods.map(p => {
    const rel = items.filter(it => p.hours.includes(((it.dt + tz) / 3600 | 0) % 24));
    if (!rel.length) return { ...p, score: 0 };
    const avg = fn => rel.reduce((a, e) => a + fn(e), 0) / rel.length;
    let s = 10;
    const t = avg(e => e.main.temp), hm = avg(e => e.main.humidity), w = avg(e => e.wind.speed), rn = avg(e => e.pop || 0), wid = avg(e => e.weather[0].id);
    if (t < 5 || t > 35) s -= 3; else if (t < 15 || t > 30) s -= 1;
    if (hm > 75) s -= 1.5; if (w > 8) s -= 1.5; if (rn > 0.5) s -= 2; if (wid < 700) s -= 2;
    return { ...p, score: Math.max(1, Math.round(s)) };
  });
  const best = Math.max(...scores.map(s => s.score));
  el('timeGrid').innerHTML = scores.map(s =>
    '<div class="time-slot' + (s.score === best ? ' best' : '') + '">' +
    '<div class="slot-period">' + s.icon + ' ' + s.label + '</div>' +
    '<div class="slot-score">' + s.score + '/10</div>' +
    '<div class="slot-label">' + (s.score === best ? '\u2728 Best time' : s.score >= 7 ? 'Good' : s.score >= 5 ? 'Okay' : 'Avoid') + '</div></div>'
  ).join('');
}

// ── Clothing & Activity Suggestions ──────────────────────────
function renderSuggestions(cw) {
  const t = cw.main.temp, h = cw.main.humidity, id = cw.weather[0].id;
  const rain = id >= 200 && id < 700;
  let clothing = [];
  if (t < 0) clothing = [['\ud83e\udde5','Heavy winter coat & gloves'],['\ud83e\udde3','Scarf and wool hat'],['\ud83e\udd7e','Warm waterproof boots']];
  else if (t < 10) clothing = [['\ud83e\udde5','Thick jacket or coat'],['\ud83e\udd44','Light gloves recommended'],['\ud83d\udc9f','Closed-toe shoes']];
  else if (t < 18) clothing = [['\ud83e\udde3','Light jacket or sweater'],['\ud83d\udc56','Full-length trousers'],['\ud83d\udc9f','Comfortable shoes']];
  else if (t < 26) clothing = [['\ud83d\udc55','T-shirt with light layer'],['\ud83e\ude73','Shorts or light pants'],['\ud83d\ude0e','Sunglasses optional']];
  else clothing = [['\ud83d\udc55','Light breathable clothing'],['\ud83e\ude73','Shorts — stay cool'],['\u2602\ufe0f','UV-protective hat']];
  if (rain) clothing.push(['\u2602\ufe0f','Carry an umbrella!']);
  if (h > 70) clothing.push(['\ud83d\udca7','Stay hydrated — high humidity']);

  let activities = [];
  if (rain) activities = [['\ud83c\udfe0','Indoor activities recommended'],['\ud83d\udcda','Great day for reading/studying'],['\ud83c\udfae','Indoor sports or gym'],['\ud83c\udf72','Cook something warm']];
  else if (t < 5) activities = [['\u26f7\ufe0f','Winter sports if available'],['\ud83d\udeb6','Short walks with layers'],['\ud83d\udd25','Cozy indoor activities'],['\ud83c\udf82','Ice activities']];
  else if (t < 20) activities = [['\ud83d\udeb6','Perfect for a walk or hike'],['\ud83d\udeb4','Cycling — ideal temperature'],['\ud83d\udcf8','Outdoor photography'],['\u26bd','Outdoor team sports']];
  else if (t < 30) activities = [['\ud83c\udfc3','Running and jogging'],['\ud83c\udfca','Swimming / water activities'],['\ud83e\uddd8','Outdoor yoga/fitness'],['\ud83c\udf33','Picnics and nature trips']];
  else activities = [['\ud83c\udfca','Swimming — great relief'],['\ud83c\udf05','Early morning outdoor walks'],['\u2744\ufe0f','Seek air-conditioned spaces'],['\ud83e\udd64','Stay hydrated frequently']];

  const makeItems = arr => arr.slice(0, 4).map(([icon, text]) =>
    '<div class="suggest-item"><span class="suggest-icon">' + icon + '</span><span>' + text + '</span></div>'
  ).join('');
  el('clothingList').innerHTML = makeItems(clothing);
  el('activityList').innerHTML = makeItems(activities);
}

// ── Radar Map ─────────────────────────────────────────────────
function renderMap(lat, lon) {
  const markerHtml = '<div style="background:#4f8ef7;width:12px;height:12px;border-radius:50%;border:2px solid #fff;box-shadow:0 0 10px #4f8ef7"></div>';
  const icon = L.divIcon({ html: markerHtml, iconSize: [12, 12], iconAnchor: [6, 6] });
  if (!leafMap) {
    leafMap = L.map('radar-map', { center: [lat, lon], zoom: 7 });
    setTimeout(() => leafMap.invalidateSize(), 200);
    L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png', { attribution: '&copy; OpenStreetMap &copy; CARTO', maxZoom: 19 }).addTo(leafMap);
    leafMarker = L.marker([lat, lon], { icon }).addTo(leafMap);
  } else {
    leafMap.setView([lat, lon], 7);
    leafMarker ? leafMarker.setLatLng([lat, lon]) : (leafMarker = L.marker([lat, lon], { icon }).addTo(leafMap));
    setTimeout(() => leafMap.invalidateSize(), 200);
  }
  setMapLayer(activeMapLayer);
}
function setMapLayer(name) {
  activeMapLayer = name;
  document.querySelectorAll('.map-btn').forEach(b => b.classList.toggle('active', b.getAttribute('onclick').includes(name)));
  if (!leafMap) return;
  if (leafWeatherLayer) leafMap.removeLayer(leafWeatherLayer);
  leafWeatherLayer = L.tileLayer('https://tile.openweathermap.org/map/' + name + '/{z}/{x}/{y}.png?appid=' + OWM_KEY, { opacity: 0.65, maxZoom: 19 });
  leafWeatherLayer.addTo(leafMap);
}

// ============================================================
// TRIP RECOMMENDATION SYSTEM
// Hybrid: rich curated database + real-time weather per destination
// Works for ANY searched city by finding nearest matching places
// ============================================================

// Comprehensive tourist places database — 120+ real places worldwide
// Each place: name, city, state, country, lat, lon, icon, category (for filter), tags (for weather matching), description
// ============================================================
// DYNAMIC TRIP RECOMMENDATIONS — OpenStreetMap Overpass API
// Fetches real tourist attractions near the searched city
// ============================================================

function getWeatherMode(temp, weatherId) {
  const isRain = weatherId >= 200 && weatherId < 700;
  if (isRain) return 'rain';
  if (temp > 32) return 'hot';
  if (temp > 24) return 'warm';
  if (temp > 15) return 'pleasant';
  if (temp > 5) return 'cool';
  return 'cold';
}

function getModeBanner(mode, cityName, t) {
  if (mode === 'rain') return '🌧️ Rainy in ' + cityName + ' — recommending indoor temples, museums &amp; heritage sites';
  if (mode === 'hot') return '🌡️ Hot ' + Math.round(t) + '°C in ' + cityName + ' — indoor &amp; shaded attractions highlighted';
  if (mode === 'warm') return '🌤️ Warm ' + Math.round(t) + '°C in ' + cityName + ' — great conditions for parks &amp; outdoor spots';
  if (mode === 'pleasant') return '😊 Pleasant ' + Math.round(t) + '°C in ' + cityName + ' — perfect for nature spots, parks &amp; scenic attractions';
  if (mode === 'cold') return '🥶 Cold in ' + cityName + ' — warm indoor museums, temples &amp; heritage sites recommended';
  return '🌸 Good weather in ' + cityName + ' — showing top-rated tourist attractions nearby';
}

function osmToCategory(tags) {
  const tourism = tags.tourism || '';
  const amenity = tags.amenity || '';
  const historic = tags.historic || '';
  const leisure = tags.leisure || '';
  const natural = tags.natural || '';
  if (amenity === 'place_of_worship' || tags.religion) return { cat: 'temple', icon: '⛩️' };
  if (['museum','gallery'].includes(tourism) || amenity === 'museum') return { cat: 'museum', icon: '🏛️' };
  if (['castle','fort','ruins','monument','memorial','archaeological_site','city_gate','palace'].includes(historic) ||
      ['attraction','artwork'].includes(tourism)) return { cat: 'heritage', icon: '🏰' };
  if (['park','garden','nature_reserve'].includes(leisure) || tourism === 'nature_reserve' ||
      natural || tags.boundary === 'national_park') return { cat: 'nature', icon: '🌿' };
  if (['theme_park','zoo','aquarium','viewpoint','yes'].includes(tourism)) return { cat: 'park', icon: '🎡' };
  if (historic) return { cat: 'heritage', icon: '🏛️' };
  return { cat: 'heritage', icon: '📍' };
}

function osmIcon(tags) {
  const t = tags.tourism || '', a = tags.amenity || '', h = tags.historic || '', l = tags.leisure || '';
  if (a === 'place_of_worship') {
    const rel = (tags.religion || '').toLowerCase();
    if (rel === 'hindu') return '🛕';
    if (rel === 'muslim' || rel === 'islam') return '🕌';
    if (rel === 'buddhist' || rel === 'buddhism') return '☸️';
    if (rel === 'christian' || rel === 'christianity') return '⛪';
    if (rel === 'jewish' || rel === 'judaism') return '🕍';
    return '⛩️';
  }
  if (t === 'museum' || a === 'museum') return '🏛️';
  if (t === 'gallery') return '🖼️';
  if (h === 'castle' || h === 'fort') return '🏰';
  if (h === 'ruins') return '🏚️';
  if (h === 'monument' || h === 'memorial') return '🗿';
  if (h === 'archaeological_site') return '⚱️';
  if (l === 'park' || l === 'garden') return '🌳';
  if (l === 'nature_reserve' || t === 'nature_reserve') return '🌿';
  if (t === 'viewpoint') return '🔭';
  if (t === 'zoo') return '🦁';
  if (t === 'aquarium') return '🐠';
  if (t === 'theme_park') return '🎡';
  if (t === 'artwork') return '🗽';
  return '📍';
}

function buildReason(cat, mode, temp) {
  const t = Math.round(temp);
  if (mode === 'rain') {
    if (cat === 'museum') return '🌧️ Rainy day perfect pick — stay dry and explore inside.';
    if (cat === 'temple') return '⛩️ A meaningful indoor spiritual visit, ideal for a rainy day.';
    if (cat === 'heritage') return '🏛️ Many heritage structures are covered — great for wet weather.';
    return '🌂 Pack a raincoat — worth visiting even in the rain.';
  }
  if (mode === 'hot') {
    if (cat === 'museum') return '❄️ Beat the ' + t + '°C heat with air-conditioned indoor exploration.';
    if (cat === 'nature') return '🌲 Seek shade in nature — go early morning to avoid peak heat.';
    return '☀️ Hot day at ' + t + '°C — visit early or late afternoon for best experience.';
  }
  if (mode === 'warm') {
    if (cat === 'nature' || cat === 'park') return '🌿 Warm ' + t + '°C is ideal — perfect outdoor conditions today.';
    return '🌤️ Warm and comfortable for outdoor sightseeing today.';
  }
  if (mode === 'pleasant') return '✨ Perfect weather at ' + t + '°C — one of the best days to visit!';
  if (mode === 'cool') {
    if (cat === 'museum' || cat === 'temple') return '🧥 Cool weather — a warm indoor cultural experience awaits.';
    return '🍂 Cool ' + t + '°C is great for heritage walks without the heat.';
  }
  if (mode === 'cold') {
    if (cat === 'museum') return '🏛️ Cold outside — warm up inside this cultural attraction.';
    return '🧣 Bundle up — this spot is worthwhile on a cold day.';
  }
  return '📍 A great nearby tourist attraction worth visiting.';
}

function weatherScore(cat, mode) {
  if (mode === 'rain') return { museum: 5, temple: 4, heritage: 4, park: 2, nature: 1 }[cat] || 2;
  if (mode === 'hot') return { museum: 5, temple: 3, heritage: 3, park: 4, nature: 3 }[cat] || 3;
  if (mode === 'warm' || mode === 'pleasant') return { nature: 5, park: 5, heritage: 4, temple: 4, museum: 3 }[cat] || 3;
  if (mode === 'cool') return { heritage: 5, museum: 4, temple: 4, nature: 3, park: 3 }[cat] || 3;
  if (mode === 'cold') return { museum: 5, temple: 4, heritage: 4, park: 2, nature: 1 }[cat] || 2;
  return 3;
}

async function fetchOverpassPlaces(lat, lon, radiusKm) {
  const r = radiusKm * 1000;
  // node+way only (no slow relations), anchored regex, 20s timeout
  const q = `[out:json][timeout:20];
(
  node["tourism"~"museum|attraction|viewpoint|zoo|aquarium|theme_park|gallery|nature_reserve"]["name"](around:${r},${lat},${lon});
  node["historic"~"castle|fort|ruins|monument|memorial|archaeological_site|city_gate|palace"]["name"](around:${r},${lat},${lon});
  node["amenity"~"place_of_worship|theatre|arts_centre"]["name"](around:${r},${lat},${lon});
  node["leisure"~"park|nature_reserve|garden"]["name"](around:${r},${lat},${lon});
  way["tourism"~"museum|attraction|viewpoint|zoo|aquarium|theme_park|gallery|nature_reserve"]["name"](around:${r},${lat},${lon});
  way["historic"~"castle|fort|ruins|monument|memorial|archaeological_site|city_gate|palace"]["name"](around:${r},${lat},${lon});
  way["amenity"~"place_of_worship|theatre|arts_centre"]["name"](around:${r},${lat},${lon});
  way["leisure"~"park|nature_reserve|garden"]["name"](around:${r},${lat},${lon});
);
out center tags 80;`;
  const body = 'data=' + encodeURIComponent(q);
  const opts = { method: 'POST', headers: { 'Content-Type': 'application/x-www-form-urlencoded' }, body };
  // Race both mirrors — whichever responds first wins
  return Promise.any([
    fetch('https://overpass-api.de/api/interpreter', opts).then(r => { if (!r.ok) throw 0; return r.json(); }).then(d => d.elements || []),
    fetch('https://overpass.kumi.systems/api/interpreter', opts).then(r => { if (!r.ok) throw 0; return r.json(); }).then(d => d.elements || [])
  ]);
}

async function renderTripRecommendations(cw) {
  // Always reset so previous city's results never bleed into new search
  allTripPlaces = [];
  currentTripFilter = 'all';
  document.querySelectorAll('.trip-tab').forEach(t => t.classList.toggle('active', t.dataset.filter === 'all'));

  const cont = el('tripContent');
  cont.innerHTML = '<div class="trip-loading"><div class="trip-spin"></div><span>Finding nearby tourist attractions for ' + cw.name + '…</span></div>';

  const lat = cw.coord.lat, lon = cw.coord.lon;
  const temp = cw.main.temp, wid = cw.weather[0].id;
  const mode = getWeatherMode(temp, wid);
  const banner = getModeBanner(mode, cw.name, temp);

  // Check cache first
  const cacheKey = lat.toFixed(2) + ',' + lon.toFixed(2);
  if (tripCache.has(cacheKey)) {
    allTripPlaces = tripCache.get(cacheKey);
    drawTripCards(allTripPlaces.slice(0, 6), banner);
    return;
  }

  try {
    // Single fetch at 80 km — covers city + surroundings well
    // Only retry once at 220 km if truly sparse (remote/rural areas)
    let elements = await fetchOverpassPlaces(lat, lon, 80);
    if (elements.length < 5) {
      cont.querySelector('span') && (cont.querySelector('span').textContent = 'Widening search for ' + cw.name + '…');
      elements = await fetchOverpassPlaces(lat, lon, 220);
    }

    const seen = new Set();
    const places = [];
    for (const osm of elements) {
      const tags = osm.tags || {};
      const name = tags['name:en'] || tags.name || '';
      if (!name || name.length < 3 || seen.has(name.toLowerCase())) continue;
      seen.add(name.toLowerCase());
      const elLat = osm.lat || (osm.center && osm.center.lat);
      const elLon = osm.lon || (osm.center && osm.center.lon);
      if (!elLat || !elLon) continue;
      const { cat } = osmToCategory(tags);
      const icon = osmIcon(tags);
      const dist = haversineKm(lat, lon, elLat, elLon);
      const ws = weatherScore(cat, mode);
      const distScore = dist < 10 ? 5 : dist < 30 ? 4 : dist < 80 ? 3 : dist < 150 ? 2 : 1;
      const totalScore = ws * 2 + distScore;
      const city = tags['addr:city'] || tags['addr:town'] || tags['addr:village'] || '';
      const desc = (tags.description || '').substring(0, 120) || 'A notable tourist spot near ' + cw.name + '.';
      places.push({ name, lat: elLat, lon: elLon, cat, icon, dist, totalScore,
        city, desc, reason: buildReason(cat, mode, temp), tags });
    }
    places.sort((a, b) => b.totalScore - a.totalScore || a.dist - b.dist);
    allTripPlaces = places.slice(0, 24);
    if (allTripPlaces.length > 0) tripCache.set(cacheKey, allTripPlaces); // cache for instant re-search

    if (allTripPlaces.length === 0) {
      cont.innerHTML = '<div class="trip-mode-bar">' + banner + '</div>' +
        '<p class="no-results">No tourist attractions found near <strong>' + cw.name + '</strong> in OpenStreetMap data. This may be a small town — try a nearby larger city for more results.</p>';
      return;
    }
  } catch (err) {
    allTripPlaces = [];
    cont.innerHTML = '<div class="trip-mode-bar">' + banner + '</div>' +
      '<p class="no-results">Could not load attractions for <strong>' + cw.name + '</strong>. Please check your connection and try again. (' + (err.message || 'Network error') + ')</p>';
    return;
  }

  drawTripCards(allTripPlaces.slice(0, 6), banner);
}

function drawTripCards(list, bannerHtml) {
  const cont = el('tripContent');
  if (!list || list.length === 0) {
    cont.innerHTML = '<div class="trip-mode-bar">' + (bannerHtml || '') + '</div><p class="no-results">No tourist attractions found matching this filter. Try &ldquo;All&rdquo; to see nearby places.</p>';
    return;
  }
  let html = '<div class="trip-mode-bar">' + (bannerHtml || '') + '</div>';
  html += '<div class="trip-grid">';
  list.forEach((dest, i) => {
    const loc = dest.city || '';
    const distLabel = dest.dist < 100 ? '\ud83d\ude97 Day trip' : dest.dist < 300 ? '\ud83e\uddf3 Weekend trip' : '\u2708\ufe0f Worth the travel';
    const gradients = [
      'linear-gradient(135deg,#1a2040,#0f1525)',
      'linear-gradient(135deg,#1a1530,#0a0e1a)',
      'linear-gradient(135deg,#1a2830,#0a1a20)',
      'linear-gradient(135deg,#201a10,#150f05)',
      'linear-gradient(135deg,#1a1020,#0a0515)'
    ];
    const mapsUrl = 'https://www.google.com/maps/search/?api=1&query=' + encodeURIComponent(dest.name + ' ' + dest.city);
    html += '<div class="trip-card" data-url="' + mapsUrl + '">';
    html +=   '<div class="trip-card-banner" style="background:' + gradients[i % 5] + '">' + dest.icon + '</div>';
    html +=   '<div class="trip-badge">' + dest.dist + ' km</div>';
    html +=   '<div class="trip-card-body">';
    html +=     '<div class="trip-card-name" title="' + dest.name + '">' + dest.name + '</div>';
    html +=     '<div class="trip-card-location">' + loc + '</div>';
    html +=     '<div class="trip-tags">';
    html +=       '<span class="trip-tag">' + dest.icon + ' ' + dest.cat.charAt(0).toUpperCase() + dest.cat.slice(1) + '</span>';
    html +=       '<span class="trip-tag ' + (dest.dist < 100 ? 'green' : 'warm') + '">' + distLabel + '</span>';
    html +=     '</div>';
    html +=     '<div class="trip-reason">' + dest.reason + '</div>';
    html +=     '<div class="trip-dist">\ud83d\udccd About ' + dest.dist + ' km from ' + (appData ? appData.cw.name : 'your city') + '</div>';
    html +=     '<div style="font-size:11px;color:var(--text-m);margin-top:5px;line-height:1.5">' + dest.desc + '</div>';
    html +=   '</div></div>';
  });
  html += '</div>';
  cont.innerHTML = html;

  // Event delegation — handles clicks on cards via data-url (avoids quote-escaping in onclick)
  cont.querySelectorAll('.trip-card').forEach(card => {
    card.addEventListener('click', function() {
      const url = this.getAttribute('data-url');
      if (url) window.open(url, '_blank');
    });
  });

  // Batch-fetch live weather for all destination cards simultaneously
  Promise.allSettled(
    list.map(dest => fetch(OWM_BASE + '/weather?lat=' + dest.lat + '&lon=' + dest.lon + '&appid=' + OWM_KEY + '&units=metric').then(r => r.json()))
  ).then(results => {
    const cards = cont.querySelectorAll('.trip-card');
    results.forEach((res, i) => {
      if (res.status !== 'fulfilled' || !cards[i]) return;
      const dw = res.value;
      const locDiv = cards[i].querySelector('.trip-card-location');
      if (locDiv && dw.weather && dw.main) {
        const weatherLine = document.createElement('div');
        weatherLine.className = 'trip-card-weather';
        weatherLine.innerHTML = '<span style="font-size:18px">' + weatherEmoji(dw.weather[0].id) + '</span>' +
          '<span>' + dw.weather[0].description + '</span>' +
          '<span class="trip-card-temp">' + tempDisplay(dw.main.temp) + '</span>';
        locDiv.insertAdjacentElement('afterend', weatherLine);
      }
    });
  });
}

function filterTrips(filterKey) {
  currentTripFilter = filterKey;
  document.querySelectorAll('.trip-tab').forEach(t => t.classList.toggle('active', t.dataset.filter === filterKey));
  const cw = appData && appData.cw;
  const mode = cw ? getWeatherMode(cw.main.temp, cw.weather[0].id) : 'mild';
  const banner = cw ? getModeBanner(mode, cw.name, cw.main.temp) : '';
  const filtered = filterKey === 'all' ? allTripPlaces : allTripPlaces.filter(d => d.cat === filterKey);
  drawTripCards(filtered.slice(0, 5), banner);
}

// ============================================================
// GEOLOCATION AUTO-DETECT
// ============================================================
if (navigator.geolocation) {
  navigator.geolocation.getCurrentPosition(
    function(pos) {
      fetch(OWM_BASE + '/weather?lat=' + pos.coords.latitude + '&lon=' + pos.coords.longitude + '&appid=' + OWM_KEY + '&units=metric')
        .then(r => r.json())
        .then(data => loadCity(data.name))
        .catch(function() {});
    },
    function() {}
  );
}
</script>
</body>
</html>
