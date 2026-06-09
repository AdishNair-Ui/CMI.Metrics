<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>CMI Creative Services — Metrics Dashboard</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
<link href="https://fonts.googleapis.com/css2?family=DM+Serif+Display:ital@0;1&family=DM+Sans:wght@300;400;500;600;700&family=JetBrains+Mono:wght@400;600&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0a0c10;
    --surface: #111318;
    --surface2: #181c24;
    --surface3: #1e2330;
    --border: rgba(255,255,255,0.07);
    --border2: rgba(255,255,255,0.12);
    --text: #f0f2f7;
    --text2: #8892a4;
    --text3: #5a647a;
    --accent: #4f7eff;
    --accent2: #7b5ef8;
    --green: #22d3a0;
    --orange: #f97b2c;
    --red: #f94f4f;
    --yellow: #f9c22c;
    --teal: #2cd3e1;
    --pink: #f75fc6;
    --mediamint: #1565e8;
    --mediamint2: #0f43a8;
    --radius: 14px;
    --radius-sm: 8px;
    --shadow: 0 4px 24px rgba(0,0,0,0.45);
    --glow: 0 0 30px rgba(79,126,255,0.12);
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    font-family: 'DM Sans', sans-serif;
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    overflow-x: hidden;
  }

  /* SCROLLBAR */
  ::-webkit-scrollbar { width: 5px; height: 5px; }
  ::-webkit-scrollbar-track { background: var(--bg); }
  ::-webkit-scrollbar-thumb { background: var(--surface3); border-radius: 99px; }

  /* TOP BAR */
  .topbar {
    position: sticky; top: 0; z-index: 100;
    background: rgba(10,12,16,0.92);
    backdrop-filter: blur(20px);
    border-bottom: 1px solid var(--border);
    padding: 0 28px;
    display: flex; align-items: center; gap: 20px;
    height: 60px;
  }
  .logo-badge {
    background: var(--mediamint);
    color: #fff;
    font-family: 'DM Serif Display', serif;
    font-size: 14px;
    padding: 5px 12px;
    border-radius: 6px;
    letter-spacing: 0.02em;
    white-space: nowrap;
  }
  .topbar-title {
    font-size: 15px;
    font-weight: 600;
    color: var(--text);
    letter-spacing: 0.01em;
  }
  .topbar-sub {
    font-size: 12px;
    color: var(--text3);
    font-family: 'JetBrains Mono', monospace;
  }
  .topbar-spacer { flex: 1; }
  .live-badge {
    display: flex; align-items: center; gap: 6px;
    font-size: 11px; font-family: 'JetBrains Mono', monospace;
    color: var(--green); background: rgba(34,211,160,0.08);
    padding: 4px 10px; border-radius: 99px;
    border: 1px solid rgba(34,211,160,0.2);
  }
  .live-dot {
    width: 6px; height: 6px; border-radius: 50%;
    background: var(--green);
    animation: pulse 2s infinite;
  }
  @keyframes pulse { 0%,100%{opacity:1;transform:scale(1)} 50%{opacity:0.5;transform:scale(0.8)} }

  /* NAV TABS */
  .nav-tabs {
    display: flex; align-items: center; gap: 4px;
    padding: 0 28px;
    border-bottom: 1px solid var(--border);
    background: var(--surface);
    overflow-x: auto;
  }
  .nav-tab {
    padding: 13px 18px;
    font-size: 13px; font-weight: 500;
    color: var(--text2);
    cursor: pointer;
    border-bottom: 2px solid transparent;
    transition: all 0.2s;
    white-space: nowrap;
  }
  .nav-tab:hover { color: var(--text); }
  .nav-tab.active { color: var(--accent); border-bottom-color: var(--accent); }

  /* CONTROLS BAR */
  .controls-bar {
    display: flex; align-items: center; gap: 12px; flex-wrap: wrap;
    padding: 16px 28px;
    background: var(--surface);
    border-bottom: 1px solid var(--border);
  }
  .controls-label { font-size: 12px; color: var(--text3); font-weight: 500; letter-spacing: 0.05em; text-transform: uppercase; }
  .period-pills { display: flex; gap: 4px; }
  .pill {
    padding: 6px 14px; border-radius: 99px;
    font-size: 12px; font-weight: 500;
    cursor: pointer; transition: all 0.2s;
    border: 1px solid var(--border2);
    color: var(--text2); background: transparent;
  }
  .pill:hover { color: var(--text); border-color: var(--accent); }
  .pill.active { background: var(--accent); color: #fff; border-color: var(--accent); }

  select, input[type="date"], input[type="number"], input[type="text"] {
    background: var(--surface2);
    border: 1px solid var(--border2);
    color: var(--text);
    border-radius: var(--radius-sm);
    padding: 7px 12px;
    font-size: 13px;
    font-family: 'DM Sans', sans-serif;
    outline: none;
    transition: border-color 0.2s;
  }
  select:focus, input:focus { border-color: var(--accent); }
  select option { background: var(--surface2); }

  .btn {
    padding: 7px 16px;
    border-radius: var(--radius-sm);
    font-size: 13px; font-weight: 500;
    cursor: pointer; transition: all 0.2s;
    border: none; font-family: 'DM Sans', sans-serif;
  }
  .btn-primary { background: var(--accent); color: #fff; }
  .btn-primary:hover { background: #3a6af0; }
  .btn-outline { background: transparent; color: var(--text2); border: 1px solid var(--border2); }
  .btn-outline:hover { color: var(--text); border-color: var(--accent); }
  .btn-success { background: var(--green); color: #0a0c10; }
  .btn-success:hover { filter: brightness(0.9); }

  /* MAIN LAYOUT */
  .main { padding: 24px 28px; }

  /* PAGE */
  .page { display: none; }
  .page.active { display: block; }

  /* KPI CARDS */
  .kpi-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    gap: 14px;
    margin-bottom: 24px;
  }
  .kpi-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 18px 20px;
    position: relative;
    overflow: hidden;
    transition: border-color 0.2s, transform 0.2s;
  }
  .kpi-card:hover { border-color: var(--border2); transform: translateY(-2px); }
  .kpi-card::before {
    content: '';
    position: absolute; top: 0; left: 0; right: 0; height: 2px;
  }
  .kpi-card.accent::before { background: linear-gradient(90deg, var(--accent), var(--accent2)); }
  .kpi-card.green::before { background: var(--green); }
  .kpi-card.orange::before { background: var(--orange); }
  .kpi-card.teal::before { background: var(--teal); }
  .kpi-card.yellow::before { background: var(--yellow); }
  .kpi-card.pink::before { background: var(--pink); }
  .kpi-card.red::before { background: var(--red); }

  .kpi-label { font-size: 11px; color: var(--text3); font-weight: 600; text-transform: uppercase; letter-spacing: 0.07em; margin-bottom: 10px; }
  .kpi-value { font-size: 28px; font-weight: 700; font-family: 'DM Serif Display', serif; color: var(--text); line-height: 1; }
  .kpi-sub { font-size: 12px; color: var(--text2); margin-top: 6px; }
  .kpi-delta {
    display: inline-flex; align-items: center; gap: 3px;
    font-size: 11px; font-family: 'JetBrains Mono', monospace;
    padding: 2px 6px; border-radius: 4px; margin-top: 6px;
  }
  .kpi-delta.up { color: var(--green); background: rgba(34,211,160,0.1); }
  .kpi-delta.down { color: var(--red); background: rgba(249,79,79,0.1); }
  .kpi-delta.neutral { color: var(--yellow); background: rgba(249,194,44,0.1); }

  /* CHARTS ROW */
  .charts-row {
    display: grid;
    gap: 16px;
    margin-bottom: 24px;
  }
  .charts-row.cols-2 { grid-template-columns: 1fr 1fr; }
  .charts-row.cols-3 { grid-template-columns: 1fr 1fr 1fr; }
  .charts-row.cols-12 { grid-template-columns: 1.4fr 1fr; }
  .charts-row.cols-21 { grid-template-columns: 2fr 1fr; }

  .chart-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 20px;
    position: relative;
  }
  .chart-header { display: flex; align-items: flex-start; justify-content: space-between; margin-bottom: 16px; gap: 10px; }
  .chart-title { font-size: 14px; font-weight: 600; color: var(--text); }
  .chart-desc { font-size: 11px; color: var(--text3); margin-top: 3px; }
  .chart-tag {
    font-size: 10px; font-family: 'JetBrains Mono', monospace;
    color: var(--text3); background: var(--surface2);
    padding: 3px 8px; border-radius: 4px;
    border: 1px solid var(--border);
    white-space: nowrap;
  }
  .chart-body { position: relative; height: 220px; }
  .chart-body.tall { height: 300px; }
  .chart-body.short { height: 160px; }

  /* TABLE */
  .table-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    overflow: hidden;
    margin-bottom: 24px;
  }
  .table-header {
    padding: 16px 20px;
    border-bottom: 1px solid var(--border);
    display: flex; align-items: center; justify-content: space-between; gap: 12px;
  }
  .table-title { font-size: 14px; font-weight: 600; }
  .table-scroll { overflow-x: auto; }
  table { width: 100%; border-collapse: collapse; }
  th {
    padding: 11px 14px;
    font-size: 11px; font-weight: 600;
    text-transform: uppercase; letter-spacing: 0.06em;
    color: var(--text3);
    background: var(--surface2);
    text-align: left; white-space: nowrap;
    border-bottom: 1px solid var(--border);
  }
  td {
    padding: 10px 14px;
    font-size: 13px; color: var(--text2);
    border-bottom: 1px solid var(--border);
    white-space: nowrap;
  }
  tr:last-child td { border-bottom: none; }
  tr:hover td { background: rgba(255,255,255,0.02); }
  .td-bold { color: var(--text); font-weight: 600; }
  .td-mono { font-family: 'JetBrains Mono', monospace; font-size: 12px; }

  /* BADGES */
  .badge {
    display: inline-block;
    padding: 2px 8px; border-radius: 4px;
    font-size: 11px; font-weight: 600;
    font-family: 'JetBrains Mono', monospace;
  }
  .badge-green { background: rgba(34,211,160,0.12); color: var(--green); }
  .badge-orange { background: rgba(249,123,44,0.12); color: var(--orange); }
  .badge-red { background: rgba(249,79,79,0.12); color: var(--red); }
  .badge-blue { background: rgba(79,126,255,0.12); color: var(--accent); }
  .badge-yellow { background: rgba(249,194,44,0.12); color: var(--yellow); }

  /* RESOURCE MANAGEMENT */
  .resource-section {
    display: grid; grid-template-columns: 1fr 1fr; gap: 16px;
    margin-bottom: 24px;
  }
  .resource-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 20px;
  }
  .resource-title {
    font-size: 13px; font-weight: 600;
    display: flex; align-items: center; gap: 8px;
    margin-bottom: 16px;
  }
  .team-dot { width: 10px; height: 10px; border-radius: 50%; }
  .team-dot.video { background: var(--accent); }
  .team-dot.static { background: var(--green); }
  .team-dot.floater { background: var(--yellow); }

  .resource-row {
    display: flex; align-items: center; gap: 10px;
    padding: 8px 0;
    border-bottom: 1px solid var(--border);
  }
  .resource-row:last-child { border-bottom: none; }
  .resource-name { flex: 1; font-size: 13px; }
  .resource-count {
    display: flex; align-items: center; gap: 8px;
  }
  .counter-btn {
    width: 26px; height: 26px;
    background: var(--surface2);
    border: 1px solid var(--border2);
    border-radius: 6px;
    color: var(--text);
    font-size: 16px; line-height: 1;
    display: flex; align-items: center; justify-content: center;
    cursor: pointer; transition: all 0.15s;
  }
  .counter-btn:hover { background: var(--accent); border-color: var(--accent); }
  .counter-val {
    font-size: 16px; font-weight: 700;
    font-family: 'JetBrains Mono', monospace;
    color: var(--text); min-width: 28px; text-align: center;
  }

  .total-strength-bar {
    background: var(--surface2);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 16px 20px;
    display: flex; align-items: center; justify-content: space-between;
    margin-bottom: 16px;
  }
  .strength-label { font-size: 12px; color: var(--text2); }
  .strength-value { font-size: 32px; font-weight: 700; font-family: 'DM Serif Display', serif; }

  /* ADDITIONAL HOURS */
  .add-hours-form {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 20px;
    margin-bottom: 24px;
  }
  .form-row { display: flex; align-items: flex-end; gap: 12px; flex-wrap: wrap; margin-bottom: 12px; }
  .form-group { display: flex; flex-direction: column; gap: 5px; }
  .form-label { font-size: 11px; color: var(--text3); font-weight: 600; text-transform: uppercase; letter-spacing: 0.05em; }

  /* EMAIL */
  .email-preview {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    overflow: hidden;
    margin-bottom: 24px;
  }
  .email-header-bar {
    background: var(--surface2);
    padding: 14px 20px;
    display: flex; align-items: center; justify-content: space-between;
    border-bottom: 1px solid var(--border);
  }
  .email-body { padding: 24px 28px; font-size: 14px; line-height: 1.7; color: var(--text2); }
  .email-table-mini { width: 100%; border-collapse: collapse; margin: 16px 0; }
  .email-table-mini th { background: var(--surface2); color: var(--text3); font-size: 11px; padding: 8px 12px; text-align: left; }
  .email-table-mini td { border-bottom: 1px solid var(--border); padding: 8px 12px; font-size: 13px; }

  /* SLIDE PREVIEW */
  .slides-grid { display: grid; grid-template-columns: repeat(auto-fill, minmax(300px, 1fr)); gap: 16px; margin-bottom: 24px; }
  .slide-thumb {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    overflow: hidden;
    cursor: pointer;
    transition: border-color 0.2s, transform 0.2s;
    aspect-ratio: 16/9;
    position: relative;
    display: flex; flex-direction: column; justify-content: flex-end;
    padding: 16px;
  }
  .slide-thumb:hover { border-color: var(--accent); transform: translateY(-3px); }
  .slide-bg {
    position: absolute; inset: 0;
    display: flex; align-items: center; justify-content: center;
    font-size: 11px; color: var(--text3);
  }
  .slide-footer { position: relative; }
  .slide-num { font-family: 'JetBrains Mono', monospace; font-size: 10px; color: var(--text3); }
  .slide-title { font-size: 12px; font-weight: 600; color: var(--text); }

  /* SECTION TITLES */
  .section-title {
    font-size: 12px; font-weight: 700;
    text-transform: uppercase; letter-spacing: 0.1em;
    color: var(--text3);
    margin-bottom: 14px;
    display: flex; align-items: center; gap: 8px;
  }
  .section-title::after {
    content: ''; flex: 1; height: 1px; background: var(--border);
  }

  /* DATE PICKER SECTION */
  .date-range-selector {
    display: flex; align-items: center; gap: 10px;
    padding: 10px 16px;
    background: var(--surface2);
    border-radius: var(--radius-sm);
    border: 1px solid var(--border);
    margin-right: auto;
  }

  /* UTILIZATION GAUGE */
  .util-ring {
    display: flex; flex-direction: column; align-items: center;
    padding: 10px 0;
  }
  .util-ring canvas { max-width: 140px; }
  .util-ring-label { font-size: 11px; color: var(--text3); margin-top: 6px; text-align: center; }

  /* SEPARATOR */
  .sep { height: 1px; background: var(--border); margin: 20px 0; }

  /* TOOLTIP */
  .tooltip {
    position: relative; cursor: help;
  }
  .tooltip::after {
    content: attr(data-tip);
    position: absolute; bottom: 120%; left: 50%; transform: translateX(-50%);
    background: var(--surface3); color: var(--text);
    padding: 5px 10px; border-radius: 6px;
    font-size: 11px; white-space: nowrap;
    pointer-events: none; opacity: 0; transition: opacity 0.2s;
    border: 1px solid var(--border2);
    z-index: 99;
  }
  .tooltip:hover::after { opacity: 1; }

  /* ALERT */
  .alert {
    padding: 12px 16px; border-radius: var(--radius-sm);
    font-size: 13px; margin-bottom: 16px;
    display: flex; align-items: flex-start; gap: 10px;
  }
  .alert.info { background: rgba(79,126,255,0.08); border: 1px solid rgba(79,126,255,0.2); color: #a0b8ff; }
  .alert.success { background: rgba(34,211,160,0.08); border: 1px solid rgba(34,211,160,0.2); color: #7beecd; }

  /* FLOAT RESOURCE NOTE */
  .floater-note {
    font-size: 11px; color: var(--yellow); background: rgba(249,194,44,0.07);
    padding: 6px 12px; border-radius: 6px; border: 1px solid rgba(249,194,44,0.15);
    display: flex; align-items: center; gap: 6px;
    margin-top: 8px;
  }

  /* RESPONSIVE */
  @media (max-width: 900px) {
    .charts-row.cols-2, .charts-row.cols-3, .charts-row.cols-12, .charts-row.cols-21 { grid-template-columns: 1fr; }
    .resource-section { grid-template-columns: 1fr; }
    .main { padding: 16px; }
    .topbar, .controls-bar, .nav-tabs { padding-left: 16px; padding-right: 16px; }
  }

  /* WEEKLY HOURS TABLE */
  .wh-table-input {
    background: var(--surface2);
    border: 1px solid var(--border);
    color: var(--text);
    border-radius: 5px;
    padding: 4px 8px;
    font-size: 12px;
    width: 70px;
    font-family: 'JetBrains Mono', monospace;
  }
  .wh-table-input:focus { border-color: var(--accent); outline: none; }

  .progress-bar {
    height: 6px; border-radius: 99px;
    background: var(--surface3); overflow: hidden;
    margin-top: 6px;
  }
  .progress-fill {
    height: 100%; border-radius: 99px;
    background: linear-gradient(90deg, var(--accent), var(--teal));
    transition: width 0.6s ease;
  }
  .progress-fill.warn { background: linear-gradient(90deg, var(--yellow), var(--orange)); }
  .progress-fill.danger { background: linear-gradient(90deg, var(--orange), var(--red)); }

  /* DESIGNER PERF */
  .designer-bar-row {
    display: flex; align-items: center; gap: 10px;
    padding: 6px 0;
  }
  .designer-name-label { width: 90px; font-size: 12px; color: var(--text2); truncate; overflow: hidden; white-space: nowrap; text-overflow: ellipsis; }
  .designer-bar { flex: 1; height: 8px; background: var(--surface3); border-radius: 99px; overflow: hidden; }
  .designer-fill { height: 100%; border-radius: 99px; }
  .designer-pct { font-size: 11px; font-family: 'JetBrains Mono', monospace; color: var(--text3); min-width: 42px; text-align: right; }
</style>
</head>
<body>

<!-- TOP BAR -->
<div class="topbar">
  <div class="logo-badge">Mediamint</div>
  <div>
    <div class="topbar-title">CMI Creative Services — Metrics Dashboard</div>
    <div class="topbar-sub">Last updated: <span id="lastUpdated"></span></div>
  </div>
  <div class="topbar-spacer"></div>
  <div class="live-badge"><div class="live-dot"></div>Live</div>
</div>

<!-- NAV TABS -->
<div class="nav-tabs">
  <div class="nav-tab active" onclick="showPage('overview')">📊 Overview</div>
  <div class="nav-tab" onclick="showPage('weekly')">📅 Weekly</div>
  <div class="nav-tab" onclick="showPage('monthly')">📆 Monthly</div>
  <div class="nav-tab" onclick="showPage('quarterly')">📈 Quarterly</div>
  <div class="nav-tab" onclick="showPage('annual')">📋 Annual / YTD</div>
  <div class="nav-tab" onclick="showPage('quality')">✅ Quality</div>
  <div class="nav-tab" onclick="showPage('resources')">👥 Resources</div>
  <div class="nav-tab" onclick="showPage('email')">📧 Email Report</div>
  <div class="nav-tab" onclick="showPage('slides')">🖥️ Slides</div>
</div>

<!-- CONTROLS BAR -->
<div class="controls-bar">
  <span class="controls-label">Period</span>
  <div class="period-pills">
    <div class="pill" onclick="setPeriod('daily',this)">Daily</div>
    <div class="pill active" onclick="setPeriod('weekly',this)">Weekly</div>
    <div class="pill" onclick="setPeriod('monthly',this)">Monthly</div>
    <div class="pill" onclick="setPeriod('quarterly',this)">Quarterly</div>
    <div class="pill" onclick="setPeriod('annual',this)">Annual</div>
  </div>
  <div class="date-range-selector">
    <span style="font-size:12px;color:var(--text3)">From</span>
    <input type="date" id="dateFrom" value="2026-01-01">
    <span style="font-size:12px;color:var(--text3)">To</span>
    <input type="date" id="dateTo" value="2026-05-31">
  </div>
  <select id="teamFilter" onchange="applyFilters()">
    <option value="all">All Teams</option>
    <option value="video">Video Team</option>
    <option value="static">Static Team</option>
  </select>
  <button class="btn btn-primary" onclick="applyFilters()">Apply</button>
  <button class="btn btn-outline" onclick="exportPDF()">⬇ Export PDF</button>
</div>

<!-- ═══════════════════ OVERVIEW PAGE ═══════════════════ -->
<div class="main page active" id="page-overview">

  <div class="kpi-grid">
    <div class="kpi-card accent">
      <div class="kpi-label">Total Creatives (Q1-26)</div>
      <div class="kpi-value" id="kpi-total-creatives">759</div>
      <div class="kpi-sub">Across all asset types</div>
      <span class="kpi-delta up">↑ vs Q4-25</span>
    </div>
    <div class="kpi-card green">
      <div class="kpi-label">Utilization Rate</div>
      <div class="kpi-value" id="kpi-utilization">59.0%</div>
      <div class="kpi-sub">Q1-26 average</div>
      <span class="kpi-delta neutral">→ stable</span>
    </div>
    <div class="kpi-card teal">
      <div class="kpi-label">Total Hours Spent</div>
      <div class="kpi-value" id="kpi-hours">2,469</div>
      <div class="kpi-sub">Q1-26 production hrs</div>
      <span class="kpi-delta up">↑ efficient</span>
    </div>
    <div class="kpi-card orange">
      <div class="kpi-label">Overall AHT</div>
      <div class="kpi-value" id="kpi-aht">3.3</div>
      <div class="kpi-sub">Avg hours per creative</div>
      <span class="kpi-delta neutral">→ on target</span>
    </div>
    <div class="kpi-card yellow">
      <div class="kpi-label">Quality Score</div>
      <div class="kpi-value" id="kpi-quality">99.61%</div>
      <div class="kpi-sub">External quality Q1-26</div>
      <span class="kpi-delta up">↑ excellent</span>
    </div>
    <div class="kpi-card pink">
      <div class="kpi-label">New Tasks</div>
      <div class="kpi-value" id="kpi-new-tasks">340</div>
      <div class="kpi-sub">Q1-26 new requests</div>
    </div>
    <div class="kpi-card red">
      <div class="kpi-label">Quality Issues</div>
      <div class="kpi-value" id="kpi-quality-issues">2</div>
      <div class="kpi-sub">Q1-26 errors flagged</div>
      <span class="kpi-delta up">↓ very low</span>
    </div>
    <div class="kpi-card accent">
      <div class="kpi-label">Total Team Strength</div>
      <div class="kpi-value" id="kpi-team">9</div>
      <div class="kpi-sub">Active resources</div>
    </div>
  </div>

  <div class="charts-row cols-12">
    <div class="chart-card">
      <div class="chart-header">
        <div>
          <div class="chart-title">Weekly Volume — Creatives Produced</div>
          <div class="chart-desc">New tasks + Revisions, Q1-2026 week-by-week</div>
        </div>
        <span class="chart-tag">Q1-26</span>
      </div>
      <div class="chart-body tall"><canvas id="chartWeeklyVol"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-header">
        <div>
          <div class="chart-title">Asset Type Mix</div>
          <div class="chart-desc">Volume by format — Q1-26</div>
        </div>
        <span class="chart-tag">Donut</span>
      </div>
      <div class="chart-body tall"><canvas id="chartAssetMix"></canvas></div>
    </div>
  </div>

  <div class="charts-row cols-21">
    <div class="chart-card">
      <div class="chart-header">
        <div>
          <div class="chart-title">Utilization Rate — Weekly Trend</div>
          <div class="chart-desc">Team utilization % vs. staffed hours target</div>
        </div>
        <span class="chart-tag">Line</span>
      </div>
      <div class="chart-body"><canvas id="chartUtilTrend"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-header">
        <div>
          <div class="chart-title">Video vs. Static Utilization</div>
          <div class="chart-desc">Q1-26 by asset category</div>
        </div>
      </div>
      <div class="chart-body" style="display:flex;flex-direction:column;justify-content:center;gap:18px;padding:10px 0">
        <div>
          <div style="display:flex;justify-content:space-between;font-size:12px;margin-bottom:4px;">
            <span style="color:var(--text2)">Video Team</span>
            <span class="td-mono" style="color:var(--accent)">75.93%</span>
          </div>
          <div class="progress-bar"><div class="progress-fill" style="width:75.93%"></div></div>
        </div>
        <div>
          <div style="display:flex;justify-content:space-between;font-size:12px;margin-bottom:4px;">
            <span style="color:var(--text2)">Static / HTML5</span>
            <span class="td-mono" style="color:var(--green)">29.86%</span>
          </div>
          <div class="progress-bar"><div class="progress-fill" style="width:29.86%"></div></div>
        </div>
        <div>
          <div style="display:flex;justify-content:space-between;font-size:12px;margin-bottom:4px;">
            <span style="color:var(--text2)">Overall</span>
            <span class="td-mono" style="color:var(--teal)">52.01%</span>
          </div>
          <div class="progress-bar"><div class="progress-fill" style="width:52.01%"></div></div>
        </div>
        <div style="padding-top:8px;border-top:1px solid var(--border)">
          <div style="font-size:11px;color:var(--text3);margin-bottom:8px">Designer Performance (last period)</div>
          <div class="designer-bar-row">
            <div class="designer-name-label">Pranay</div>
            <div class="designer-bar"><div class="designer-fill" style="width:125%;background:var(--accent);max-width:100%"></div></div>
            <div class="designer-pct">125%</div>
          </div>
          <div class="designer-bar-row">
            <div class="designer-name-label">Lokesh</div>
            <div class="designer-bar"><div class="designer-fill" style="width:100%;background:var(--green)"></div></div>
            <div class="designer-pct">174%</div>
          </div>
          <div class="designer-bar-row">
            <div class="designer-name-label">Prateem</div>
            <div class="designer-bar"><div class="designer-fill" style="width:100%;background:var(--teal)"></div></div>
            <div class="designer-pct">163%</div>
          </div>
          <div class="designer-bar-row">
            <div class="designer-name-label">Suresh</div>
            <div class="designer-bar"><div class="designer-fill" style="width:100%;background:var(--orange)"></div></div>
            <div class="designer-pct">158%</div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div class="charts-row cols-3">
    <div class="chart-card">
      <div class="chart-header">
        <div><div class="chart-title">Monthly Volume Trend</div></div>
        <span class="chart-tag">Bar</span>
      </div>
      <div class="chart-body short"><canvas id="chartMonthVol"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-header">
        <div><div class="chart-title">AHT by Week</div></div>
        <span class="chart-tag">Line</span>
      </div>
      <div class="chart-body short"><canvas id="chartAHT"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-header">
        <div><div class="chart-title">Quarterly Comparison</div></div>
        <span class="chart-tag">Bar</span>
      </div>
      <div class="chart-body short"><canvas id="chartQtrComp"></canvas></div>
    </div>
  </div>

</div>

<!-- ═══════════════════ WEEKLY PAGE ═══════════════════ -->
<div class="main page" id="page-weekly">
  <div class="section-title">Weekly Performance Report</div>

  <div class="kpi-grid">
    <div class="kpi-card accent">
      <div class="kpi-label">Week of</div>
      <div class="kpi-value" style="font-size:18px" id="wk-label">25-May-26</div>
      <div class="kpi-sub">Week 22, 2026</div>
    </div>
    <div class="kpi-card green">
      <div class="kpi-label">Total Tasks</div>
      <div class="kpi-value">38</div>
      <div class="kpi-sub">25 New + 13 Revisions</div>
    </div>
    <div class="kpi-card teal">
      <div class="kpi-label">Creatives</div>
      <div class="kpi-value">106</div>
      <div class="kpi-sub">This week</div>
    </div>
    <div class="kpi-card orange">
      <div class="kpi-label">Hours Spent</div>
      <div class="kpi-value">193.5</div>
      <div class="kpi-sub">+ add'l hours below</div>
    </div>
    <div class="kpi-card yellow">
      <div class="kpi-label">Utilization</div>
      <div class="kpi-value">57.3%</div>
      <div class="kpi-sub">vs 337.5 staffed hrs</div>
    </div>
    <div class="kpi-card pink">
      <div class="kpi-label">AHT/Ad</div>
      <div class="kpi-value">1.8</div>
      <div class="kpi-sub">hrs avg per creative</div>
    </div>
  </div>

  <!-- ADDITIONAL HOURS ENTRY -->
  <div class="add-hours-form">
    <div class="section-title">Additional Hours Entry — Weekly Record</div>
    <div class="alert info">
      ℹ️ The standard 36 additional client meeting hours are pre-loaded. You may override or add extra hours below. All entries are recorded against the selected week and added to Column AL totals.
    </div>
    <div class="form-row">
      <div class="form-group">
        <div class="form-label">Week</div>
        <select id="addHrsWeek">
          <option>Week of 25-May-26</option>
          <option>Week of 18-May-26</option>
          <option>Week of 11-May-26</option>
          <option>Week of 04-May-26</option>
          <option>Week of 27-Apr-26</option>
        </select>
      </div>
      <div class="form-group">
        <div class="form-label">Category</div>
        <select id="addHrsCategory">
          <option>Client Meetings</option>
          <option>Training</option>
          <option>R&D / Innovation</option>
          <option>Administrative</option>
          <option>Other</option>
        </select>
      </div>
      <div class="form-group">
        <div class="form-label">Hours</div>
        <input type="number" id="addHrsAmt" value="36" min="0" max="100" style="width:90px">
      </div>
      <div class="form-group">
        <div class="form-label">Notes</div>
        <input type="text" id="addHrsNotes" placeholder="e.g. QBR call with Doug & Gayle" style="width:260px">
      </div>
      <button class="btn btn-primary" onclick="logAdditionalHours()">+ Log Hours</button>
    </div>
    <div class="table-scroll" id="addHrsTable">
      <table>
        <thead><tr><th>Week</th><th>Category</th><th>Hours</th><th>Notes</th><th>Total w/ Prod</th></tr></thead>
        <tbody id="addHrsTbody">
          <tr>
            <td class="td-bold">Week of 25-May-26</td>
            <td>Client Meetings</td>
            <td class="td-mono">36.0</td>
            <td>Standard client engagement hrs</td>
            <td class="td-mono td-bold">229.5</td>
          </tr>
          <tr>
            <td class="td-bold">Week of 18-May-26</td>
            <td>Client Meetings</td>
            <td class="td-mono">36.0</td>
            <td>Standard client engagement hrs</td>
            <td class="td-mono td-bold">274.2</td>
          </tr>
          <tr>
            <td class="td-bold">Week of 09-Mar-26</td>
            <td>Client Meetings</td>
            <td class="td-mono">20.0</td>
            <td>Additional sprint hrs</td>
            <td class="td-mono td-bold">192.0</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>

  <div class="charts-row cols-2">
    <div class="chart-card">
      <div class="chart-header">
        <div><div class="chart-title">This Week's Hours by Day</div></div>
        <span class="chart-tag">Bar</span>
      </div>
      <div class="chart-body"><canvas id="chartDailyHrs"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-header">
        <div><div class="chart-title">Task Distribution — New vs. Revision</div></div>
        <span class="chart-tag">Doughnut</span>
      </div>
      <div class="chart-body"><canvas id="chartTaskDist"></canvas></div>
    </div>
  </div>

  <!-- WEEKLY KPI TABLE -->
  <div class="table-card">
    <div class="table-header">
      <div class="table-title">Weekly Metrics — Q1 & Q2 2026</div>
      <button class="btn btn-outline" onclick="copyWeeklyReport()">📋 Copy Report</button>
    </div>
    <div class="table-scroll">
      <table id="weeklyTable">
        <thead>
          <tr>
            <th>Week Of</th><th>Wk #</th><th>Tasks</th><th>New</th><th>Rev</th>
            <th>Creatives</th><th>AHT(hrs)</th><th>Utilization</th>
            <th>Hours</th><th>Add'l Hrs</th><th>Total Hrs</th>
            <th>Staffed Hrs</th><th>Quality %</th><th>Issues</th>
          </tr>
        </thead>
        <tbody id="weeklyTbody"></tbody>
      </table>
    </div>
  </div>
</div>

<!-- ═══════════════════ MONTHLY PAGE ═══════════════════ -->
<div class="main page" id="page-monthly">
  <div class="section-title">Monthly Performance Report</div>

  <div class="charts-row cols-2">
    <div class="chart-card">
      <div class="chart-header">
        <div><div class="chart-title">Monthly Creatives Volume</div><div class="chart-desc">Video, Static/HTML5, All types</div></div>
        <span class="chart-tag">Stacked Bar</span>
      </div>
      <div class="chart-body tall"><canvas id="chartMonthlyStack"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-header">
        <div><div class="chart-title">Monthly Utilization</div><div class="chart-desc">Video vs Static vs Overall</div></div>
        <span class="chart-tag">Multi Line</span>
      </div>
      <div class="chart-body tall"><canvas id="chartMonthlyUtil"></canvas></div>
    </div>
  </div>

  <div class="table-card">
    <div class="table-header">
      <div class="table-title">Monthly Summary Table</div>
      <button class="btn btn-outline" onclick="exportMonthly()">⬇ Export</button>
    </div>
    <div class="table-scroll">
      <table>
        <thead>
          <tr><th>Month</th><th>Quarter</th><th>New Tasks</th><th>Revisions</th><th>Total Creatives</th><th>Hours</th><th>Utilization</th><th>AHT (min)</th><th>Quality %</th><th>Team HC</th></tr>
        </thead>
        <tbody id="monthlyTbody"></tbody>
      </table>
    </div>
  </div>
</div>

<!-- ═══════════════════ QUARTERLY PAGE ═══════════════════ -->
<div class="main page" id="page-quarterly">
  <div class="section-title">Quarterly Business Review</div>

  <div class="kpi-grid">
    <div class="kpi-card accent">
      <div class="kpi-label">Q1-26 Creatives</div>
      <div class="kpi-value">759</div>
      <div class="kpi-sub">New: 340 | Rev: 178 | Other: 241</div>
    </div>
    <div class="kpi-card green">
      <div class="kpi-label">Q1-26 Hours</div>
      <div class="kpi-value">2,469</div>
    </div>
    <div class="kpi-card teal">
      <div class="kpi-label">Q1-26 Utilization</div>
      <div class="kpi-value">59.0%</div>
    </div>
    <div class="kpi-card orange">
      <div class="kpi-label">Q1-26 Quality</div>
      <div class="kpi-value">99.61%</div>
    </div>
  </div>

  <div class="charts-row cols-2">
    <div class="chart-card">
      <div class="chart-header">
        <div><div class="chart-title">Quarter-over-Quarter Creatives</div></div>
        <span class="chart-tag">Bar</span>
      </div>
      <div class="chart-body tall"><canvas id="chartQoQVol"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-header">
        <div><div class="chart-title">Quarterly Asset Type Breakdown</div></div>
        <span class="chart-tag">Stacked</span>
      </div>
      <div class="chart-body tall"><canvas id="chartQtrBreakdown"></canvas></div>
    </div>
  </div>

  <div class="charts-row cols-2">
    <div class="chart-card">
      <div class="chart-header">
        <div><div class="chart-title">Quarterly Utilization Trend</div></div>
      </div>
      <div class="chart-body"><canvas id="chartQtrUtil"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-header">
        <div><div class="chart-title">Quarterly AHT per Ad</div></div>
      </div>
      <div class="chart-body"><canvas id="chartQtrAHT"></canvas></div>
    </div>
  </div>

  <div class="table-card">
    <div class="table-header"><div class="table-title">Quarterly Summary</div></div>
    <div class="table-scroll">
      <table>
        <thead><tr><th>Quarter</th><th>Total Tasks</th><th>New</th><th>Revisions</th><th>Total Creatives</th><th>Hours</th><th>Utilization</th><th>AHT</th><th>Quality %</th><th>Errors</th></tr></thead>
        <tbody id="qtrTbody"></tbody>
      </table>
    </div>
  </div>
</div>

<!-- ═══════════════════ ANNUAL PAGE ═══════════════════ -->
<div class="main page" id="page-annual">
  <div class="section-title">Annual / YTD Report</div>

  <div class="kpi-grid">
    <div class="kpi-card accent">
      <div class="kpi-label">YTD 2026 Creatives</div>
      <div class="kpi-value">~1,850</div>
      <div class="kpi-sub">Wk 1–22 estimate</div>
    </div>
    <div class="kpi-card green">
      <div class="kpi-label">2025 Full Year</div>
      <div class="kpi-value">~4,800</div>
      <div class="kpi-sub">All quarters combined</div>
    </div>
    <div class="kpi-card teal">
      <div class="kpi-label">2026 YTD Hours</div>
      <div class="kpi-value">~7,628</div>
    </div>
    <div class="kpi-card orange">
      <div class="kpi-label">Avg Utilization 2026</div>
      <div class="kpi-value">61.2%</div>
    </div>
  </div>

  <div class="charts-row cols-2">
    <div class="chart-card">
      <div class="chart-header">
        <div><div class="chart-title">Annual Volume — Monthly Trend</div></div>
        <span class="chart-tag">Area</span>
      </div>
      <div class="chart-body tall"><canvas id="chartAnnualVol"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-header">
        <div><div class="chart-title">Year-over-Year Comparison</div></div>
        <span class="chart-tag">Grouped Bar</span>
      </div>
      <div class="chart-body tall"><canvas id="chartYoY"></canvas></div>
    </div>
  </div>

  <div class="charts-row cols-3">
    <div class="chart-card">
      <div class="chart-header"><div><div class="chart-title">Video Creatives Trend</div></div></div>
      <div class="chart-body short"><canvas id="chartVideoTrend"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-header"><div><div class="chart-title">Static Creatives Trend</div></div></div>
      <div class="chart-body short"><canvas id="chartStaticTrend"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-header"><div><div class="chart-title">HTML5 Creatives Trend</div></div></div>
      <div class="chart-body short"><canvas id="chartHtml5Trend"></canvas></div>
    </div>
  </div>
</div>

<!-- ═══════════════════ QUALITY PAGE ═══════════════════ -->
<div class="main page" id="page-quality">
  <div class="section-title">Quality Metrics</div>

  <div class="kpi-grid">
    <div class="kpi-card green">
      <div class="kpi-label">External Quality Score</div>
      <div class="kpi-value">99.61%</div>
      <div class="kpi-sub">Q1-26 | Goal: 100%</div>
    </div>
    <div class="kpi-card accent">
      <div class="kpi-label">Q4-25 Quality</div>
      <div class="kpi-value">100%</div>
      <div class="kpi-sub">Oct–Dec 2025</div>
    </div>
    <div class="kpi-card teal">
      <div class="kpi-label">Q3-25 Quality</div>
      <div class="kpi-value">98.96%</div>
    </div>
    <div class="kpi-card orange">
      <div class="kpi-label">Total Errors Q1-26</div>
      <div class="kpi-value">2</div>
      <div class="kpi-sub">Out of 2,469 tickets</div>
    </div>
  </div>

  <div class="charts-row cols-2">
    <div class="chart-card">
      <div class="chart-header">
        <div><div class="chart-title">Monthly Quality Score</div><div class="chart-desc">External quality % vs. 100% goal</div></div>
      </div>
      <div class="chart-body tall"><canvas id="chartQualityLine"></canvas></div>
    </div>
    <div class="chart-card">
      <div class="chart-header">
        <div><div class="chart-title">Error Count by Month</div></div>
      </div>
      <div class="chart-body tall"><canvas id="chartErrors"></canvas></div>
    </div>
  </div>

  <div class="table-card">
    <div class="table-header"><div class="table-title">Quality Log — Monthly Detail</div></div>
    <div class="table-scroll">
      <table>
        <thead><tr><th>Month</th><th>Tickets Reviewed</th><th>Errors</th><th>External Quality %</th><th>Goal</th><th>Status</th></tr></thead>
        <tbody id="qualityTbody"></tbody>
      </table>
    </div>
  </div>
</div>

<!-- ═══════════════════ RESOURCES PAGE ═══════════════════ -->
<div class="main page" id="page-resources">
  <div class="section-title">Resource Management</div>

  <div class="alert info">ℹ️ Adjust headcount per team using the +/– controls. Total Team Strength = Video Team + Static Team. The Floater resource is tracked separately and is <strong>not included</strong> in utilization calculations.</div>

  <!-- DATE SELECTOR FOR RESOURCE SNAPSHOT -->
  <div class="controls-bar" style="margin-bottom:16px;border-radius:var(--radius);border:1px solid var(--border)">
    <span class="controls-label">Snapshot Date</span>
    <input type="date" id="resourceDate" value="2026-06-09">
    <select id="resourceWeek">
      <option>Week of 08-Jun-26</option>
      <option>Week of 25-May-26</option>
      <option>Week of 18-May-26</option>
      <option>Week of 11-May-26</option>
    </select>
    <button class="btn btn-primary" onclick="updateResourceSnapshot()">Update Snapshot</button>
  </div>

  <!-- TOTAL STRENGTH SUMMARY -->
  <div class="total-strength-bar">
    <div>
      <div class="strength-label">Total Team Strength</div>
      <div style="font-size:11px;color:var(--text3);margin-top:2px">Video Team + Static Team (Floater excluded)</div>
    </div>
    <div style="display:flex;align-items:baseline;gap:12px">
      <div id="totalStrength" class="strength-value" style="color:var(--accent)">9</div>
      <div style="font-size:13px;color:var(--text3)">active resources</div>
    </div>
  </div>

  <div class="resource-section">
    <!-- VIDEO TEAM -->
    <div class="resource-card">
      <div class="resource-title">
        <div class="team-dot video"></div>
        Team 1 — Video
        <span class="badge badge-blue" id="videoBadge">4 members</span>
      </div>
      <div id="videoTeam">
        <div class="resource-row">
          <div class="resource-name">Pranay B</div>
          <div class="resource-count">
            <div class="counter-btn" onclick="changeCount('v',0,-1)">−</div>
            <div class="counter-val" id="v-0">1</div>
            <div class="counter-btn" onclick="changeCount('v',0,1)">+</div>
          </div>
          <span class="badge badge-green" style="margin-left:8px">Active</span>
        </div>
        <div class="resource-row">
          <div class="resource-name">David Charlie</div>
          <div class="resource-count">
            <div class="counter-btn" onclick="changeCount('v',1,-1)">−</div>
            <div class="counter-val" id="v-1">1</div>
            <div class="counter-btn" onclick="changeCount('v',1,1)">+</div>
          </div>
          <span class="badge badge-green" style="margin-left:8px">Active</span>
        </div>
        <div class="resource-row">
          <div class="resource-name">Tirumalateja</div>
          <div class="resource-count">
            <div class="counter-btn" onclick="changeCount('v',2,-1)">−</div>
            <div class="counter-val" id="v-2">1</div>
            <div class="counter-btn" onclick="changeCount('v',2,1)">+</div>
          </div>
          <span class="badge badge-green" style="margin-left:8px">Active</span>
        </div>
        <div class="resource-row">
          <div class="resource-name">Rajendar M</div>
          <div class="resource-count">
            <div class="counter-btn" onclick="changeCount('v',3,-1)">−</div>
            <div class="counter-val" id="v-3">1</div>
            <div class="counter-btn" onclick="changeCount('v',3,1)">+</div>
          </div>
          <span class="badge badge-green" style="margin-left:8px">Active</span>
        </div>
      </div>
      <div class="floater-note">
        <span>★</span>
        Video team staffed capacity: <strong id="videoCapacity">120 hrs/week</strong>
      </div>
    </div>

    <!-- STATIC TEAM -->
    <div class="resource-card">
      <div class="resource-title">
        <div class="team-dot static"></div>
        Team 2 — Static / HTML5
        <span class="badge badge-green" id="staticBadge">5 members</span>
      </div>
      <div id="staticTeam">
        <div class="resource-row">
          <div class="resource-name">Lokesh S</div>
          <div class="resource-count">
            <div class="counter-btn" onclick="changeCount('s',0,-1)">−</div>
            <div class="counter-val" id="s-0">1</div>
            <div class="counter-btn" onclick="changeCount('s',0,1)">+</div>
          </div>
          <span class="badge badge-green" style="margin-left:8px">Active</span>
        </div>
        <div class="resource-row">
          <div class="resource-name">Suresh S</div>
          <div class="resource-count">
            <div class="counter-btn" onclick="changeCount('s',1,-1)">−</div>
            <div class="counter-val" id="s-1">1</div>
            <div class="counter-btn" onclick="changeCount('s',1,1)">+</div>
          </div>
          <span class="badge badge-green" style="margin-left:8px">Active</span>
        </div>
        <div class="resource-row">
          <div class="resource-name">Prateem S</div>
          <div class="resource-count">
            <div class="counter-btn" onclick="changeCount('s',2,-1)">−</div>
            <div class="counter-val" id="s-2">1</div>
            <div class="counter-btn" onclick="changeCount('s',2,1)">+</div>
          </div>
          <span class="badge badge-green" style="margin-left:8px">Active</span>
        </div>
        <div class="resource-row">
          <div class="resource-name">Manoj K</div>
          <div class="resource-count">
            <div class="counter-btn" onclick="changeCount('s',3,-1)">−</div>
            <div class="counter-val" id="s-3">1</div>
            <div class="counter-btn" onclick="changeCount('s',3,1)">+</div>
          </div>
          <span class="badge badge-green" style="margin-left:8px">Active</span>
        </div>
        <div class="resource-row">
          <div class="resource-name">Yashwanth B</div>
          <div class="resource-count">
            <div class="counter-btn" onclick="changeCount('s',4,-1)">−</div>
            <div class="counter-val" id="s-4">1</div>
            <div class="counter-btn" onclick="changeCount('s',4,1)">+</div>
          </div>
          <span class="badge badge-green" style="margin-left:8px">Active</span>
        </div>
      </div>
      <div class="floater-note">
        <span>★</span>
        Static team staffed capacity: <strong id="staticCapacity">187.5 hrs/week</strong>
      </div>
    </div>
  </div>

  <!-- FLOATER -->
  <div class="resource-card" style="margin-bottom:16px">
    <div class="resource-title">
      <div class="team-dot floater"></div>
      Floater Resource
      <span class="badge badge-yellow">Not counted in utilization</span>
    </div>
    <div class="resource-row">
      <div class="resource-name">Raju Adabala (Floater)</div>
      <div class="resource-count">
        <div class="counter-btn" onclick="changeCount('f',0,-1)">−</div>
        <div class="counter-val" id="f-0">1</div>
        <div class="counter-btn" onclick="changeCount('f',0,1)">+</div>
      </div>
      <span class="badge badge-yellow" style="margin-left:8px">Floater</span>
    </div>
    <div class="floater-note">⚠️ Floater's hours are tracked but excluded from team utilization rate calculations per operational definition.</div>
  </div>

  <!-- RESOURCE HISTORY -->
  <div class="table-card">
    <div class="table-header">
      <div class="table-title">Resource Strength History</div>
    </div>
    <div class="table-scroll">
      <table>
        <thead><tr><th>Period</th><th>Video Team</th><th>Static Team</th><th>Total (excl. Floater)</th><th>Staffed Hrs/Week</th><th>Notes</th></tr></thead>
        <tbody>
          <tr><td class="td-bold">Q4-24</td><td>—</td><td>—</td><td>8</td><td class="td-mono">320</td><td>Initial period</td></tr>
          <tr><td class="td-bold">Q1-25</td><td>5</td><td>10</td><td>15</td><td class="td-mono">562.5</td><td>Peak headcount</td></tr>
          <tr><td class="td-bold">Q2-25</td><td>5</td><td>10</td><td>15</td><td class="td-mono">562.5</td><td></td></tr>
          <tr><td class="td-bold">Q3-25</td><td>4</td><td>7</td><td>11</td><td class="td-mono">412.5</td><td>Reduction</td></tr>
          <tr><td class="td-bold">Q4-25</td><td>4</td><td>5</td><td>9</td><td class="td-mono">337.5</td><td>Current config</td></tr>
          <tr><td class="td-bold">Q1-26</td><td>4</td><td>5</td><td class="td-bold" style="color:var(--accent)">9</td><td class="td-mono td-bold">337.5</td><td>Active</td></tr>
          <tr><td class="td-bold">Q2-26 (current)</td><td>4</td><td>5</td><td class="td-bold" style="color:var(--accent)">9</td><td class="td-mono td-bold">337.5</td><td>Active</td></tr>
        </tbody>
      </table>
    </div>
  </div>
</div>

<!-- ═══════════════════ EMAIL REPORT PAGE ═══════════════════ -->
<div class="main page" id="page-email">
  <div class="section-title">Email Report Generator</div>

  <div class="controls-bar" style="margin-bottom:16px;border-radius:var(--radius);border:1px solid var(--border)">
    <span class="controls-label">Report Type</span>
    <select id="emailReportType" onchange="generateEmailPreview()">
      <option value="weekly">Weekly Report</option>
      <option value="daily">Daily Report</option>
    </select>
    <span class="controls-label">Period</span>
    <select id="emailPeriod" onchange="generateEmailPreview()">
      <option>Week of 25-May-26</option>
      <option>Week of 18-May-26</option>
      <option>Week of 11-May-26</option>
    </select>
    <span class="controls-label">Recipients</span>
    <input type="text" id="emailRecipients" value="doug@cmi.com, gayle@cmi.com, leadership@mediamint.com" style="width:340px">
    <button class="btn btn-success" onclick="sendEmail()">📧 Send Report</button>
    <button class="btn btn-outline" onclick="copyEmailHTML()">📋 Copy HTML</button>
  </div>

  <div class="email-preview">
    <div class="email-header-bar">
      <div>
        <div style="font-size:14px;font-weight:600">Email Preview</div>
        <div style="font-size:11px;color:var(--text3)" id="emailSubjectLine">Subject: CMI Creative Services — Weekly Report | Week of 25-May-26</div>
      </div>
      <span class="badge badge-blue">Preview Mode</span>
    </div>
    <div class="email-body" id="emailBody">
      <!-- Generated dynamically -->
    </div>
  </div>
</div>

<!-- ═══════════════════ SLIDES PAGE ═══════════════════ -->
<div class="main page" id="page-slides">
  <div class="section-title">Business Review Slides</div>

  <div class="alert success">✅ Slide deck generated from live KPI data. Click any slide to preview. Use the export button to download as presentation-ready HTML.</div>

  <div class="controls-bar" style="margin-bottom:16px;border-radius:var(--radius);border:1px solid var(--border)">
    <select id="slidePeriod">
      <option>Q1-2026 Quarterly Review</option>
      <option>May 2026 Monthly Review</option>
      <option>Annual Review 2025</option>
    </select>
    <button class="btn btn-primary" onclick="generateSlides()">🔄 Regenerate</button>
    <button class="btn btn-success" onclick="exportSlides()">⬇ Export Slides</button>
  </div>

  <div class="slides-grid" id="slidesGrid"></div>
</div>

<script>
// ══════════════════════════════════════════════
//  DATA
// ══════════════════════════════════════════════
const weeklyData = [
  { week:'Week of 29-Dec-25',wk:1,tasks:5,newT:3,rev:2,creatives:23,aht:1.5,util:21.96,hours:74.10,addHrs:36,staffed:337.5,quality:100,issues:0 },
  { week:'Week of 05-Jan-26',wk:2,tasks:41,newT:32,rev:9,creatives:65,aht:1.5,util:50.67,hours:133.80,addHrs:36,staffed:337.5,quality:100,issues:0 },
  { week:'Week of 12-Jan-26',wk:3,tasks:27,newT:20,rev:7,creatives:35,aht:1.7,util:29.66,hours:97.10,addHrs:36,staffed:337.5,quality:100,issues:0 },
  { week:'Week of 19-Jan-26',wk:4,tasks:29,newT:19,rev:10,creatives:70,aht:2.2,util:56.83,hours:191.80,addHrs:36,staffed:337.5,quality:100,issues:0 },
  { week:'Week of 26-Jan-26',wk:5,tasks:37,newT:23,rev:14,creatives:30,aht:4.3,util:50.13,hours:169,addHrs:36,staffed:337.5,quality:97,issues:1 },
  { week:'Week of 02-Feb-26',wk:6,tasks:43,newT:27,rev:16,creatives:78,aht:1.6,util:49.30,hours:166,addHrs:36,staffed:337.5,quality:98,issues:1 },
  { week:'Week of 09-Feb-26',wk:7,tasks:37,newT:20,rev:17,creatives:53,aht:2.7,util:54.31,hours:183,addHrs:36,staffed:337.5,quality:100,issues:0 },
  { week:'Week of 16-Feb-26',wk:8,tasks:50,newT:33,rev:17,creatives:76,aht:2.2,util:61.54,hours:208,addHrs:36,staffed:337.5,quality:100,issues:0 },
  { week:'Week of 23-Feb-26',wk:9,tasks:40,newT:27,rev:13,creatives:62,aht:3.1,util:69.30,hours:234,addHrs:36,staffed:337.5,quality:100,issues:0 },
  { week:'Week of 02-Mar-26',wk:10,tasks:47,newT:28,rev:19,creatives:89,aht:1.6,util:55.56,hours:188,addHrs:36,staffed:337.5,quality:100,issues:0 },
  { week:'Week of 09-Mar-26',wk:11,tasks:47,newT:22,rev:25,creatives:43,aht:2.6,util:50.99,hours:172,addHrs:56,staffed:337.5,quality:100,issues:0 },
  { week:'Week of 16-Mar-26',wk:12,tasks:48,newT:34,rev:14,creatives:50,aht:3.5,util:64.53,hours:218,addHrs:39,staffed:337.5,quality:100,issues:0 },
  { week:'Week of 23-Mar-26',wk:13,tasks:49,newT:38,rev:11,creatives:69,aht:1.9,util:57.07,hours:193,addHrs:52,staffed:337.5,quality:100,issues:0 },
  { week:'Week of 30-Mar-26',wk:14,tasks:18,newT:14,rev:4,creatives:16,aht:12.4,util:71.61,hours:242,addHrs:36,staffed:337.5,quality:100,issues:0 },
  { week:'Week of 04-May-26',wk:19,tasks:42,newT:33,rev:9,creatives:70,aht:3.3,util:68.89,hours:232.5,addHrs:36,staffed:337.5,quality:100,issues:0 },
  { week:'Week of 11-May-26',wk:20,tasks:55,newT:38,rev:17,creatives:54,aht:4.1,util:65.07,hours:219.6,addHrs:36,staffed:337.5,quality:100,issues:0 },
  { week:'Week of 18-May-26',wk:21,tasks:58,newT:38,rev:20,creatives:75,aht:3.2,util:70.58,hours:238.2,addHrs:36,staffed:337.5,quality:100,issues:0 },
  { week:'Week of 25-May-26',wk:22,tasks:38,newT:25,rev:13,creatives:106,aht:1.8,util:57.33,hours:193.5,addHrs:36,staffed:337.5,quality:100,issues:0 },
];

const monthlyData = [
  { month:'Jan-26',quarter:'Q1-26',newT:97,rev:106,creatives:203,hours:467,util:34.56,aht:2.3,quality:100,hc:9 },
  { month:'Feb-26',quarter:'Q1-26',newT:107,rev:162,creatives:269,hours:647,util:47.95,aht:2.4,quality:100,hc:9 },
  { month:'Mar-26',quarter:'Q1-26',newT:136,rev:131,creatives:267,hours:704,util:52.11,aht:2.6,quality:100,hc:9 },
  { month:'Apr-26',quarter:'Q2-26',newT:0,rev:0,creatives:0,hours:0,util:0,aht:0,quality:100,hc:9 },
  { month:'May-26',quarter:'Q2-26',newT:134,rev:59,creatives:305,hours:884,util:65.0,aht:2.9,quality:100,hc:9 },
];

const quarterlyData = [
  { quarter:'Q4-24',tasks:24,newT:16,rev:8,creatives:56,hours:320,util:25,aht:1.8,quality:99.5,errors:0 },
  { quarter:'Q1-25',tasks:180,newT:114,rev:66,creatives:962,hours:6936,util:62,aht:2.4,quality:99.9,errors:1 },
  { quarter:'Q2-25',tasks:175,newT:108,rev:67,creatives:844,hours:6570,util:60,aht:2.5,quality:100,errors:0 },
  { quarter:'Q3-25',tasks:152,newT:88,rev:64,creatives:693,hours:5280,util:45,aht:2.7,quality:98.96,errors:2 },
  { quarter:'Q4-25',tasks:165,newT:94,rev:71,creatives:728,hours:4665,util:55,aht:2.6,quality:100,errors:0 },
  { quarter:'Q1-26',tasks:518,newT:340,rev:178,creatives:759,hours:2469,util:59,aht:3.3,quality:99.61,errors:2 },
];

const qualityData = [
  { month:'Jul-25',tickets:344,errors:0,external:100,goal:100 },
  { month:'Aug-25',tickets:559,errors:1,external:99.62,goal:100 },
  { month:'Sep-25',tickets:475,errors:0,external:100,goal:100 },
  { month:'Oct-25',tickets:389,errors:0,external:100,goal:100 },
  { month:'Nov-25',tickets:351,errors:0,external:100,goal:100 },
  { month:'Dec-25',tickets:333,errors:0,external:100,goal:100 },
  { month:'Jan-26',tickets:351,errors:1,external:99.55,goal:100 },
  { month:'Feb-26',tickets:333,errors:1,external:99.66,goal:100 },
  { month:'Mar-26',tickets:484,errors:0,external:100,goal:100 },
  { month:'Apr-26',tickets:0,errors:0,external:100,goal:100 },
  { month:'May-26',tickets:884,errors:0,external:100,goal:100 },
];

// ══════════════════════════════════════════════
//  CHART CONFIG
// ══════════════════════════════════════════════
Chart.defaults.color = '#8892a4';
Chart.defaults.font.family = "'DM Sans', sans-serif";
Chart.defaults.font.size = 11;
Chart.defaults.plugins.legend.labels.boxWidth = 10;
Chart.defaults.plugins.legend.labels.padding = 14;

const GRID = { color: 'rgba(255,255,255,0.05)', drawBorder: false };
const TOOLTIP_STYLE = {
  backgroundColor: '#1e2330',
  borderColor: 'rgba(255,255,255,0.1)',
  borderWidth: 1,
  titleColor: '#f0f2f7',
  bodyColor: '#8892a4',
  padding: 10,
  cornerRadius: 8,
};

function makeChart(id, type, labels, datasets, options={}) {
  const el = document.getElementById(id);
  if (!el) return;
  if (el._chart) el._chart.destroy();
  const ctx = el.getContext('2d');
  const chart = new Chart(ctx, {
    type,
    data: { labels, datasets },
    options: {
      responsive: true, maintainAspectRatio: false,
      plugins: { legend: { display: datasets.length > 1 }, tooltip: TOOLTIP_STYLE },
      scales: type === 'doughnut' || type === 'pie' ? {} : {
        x: { grid: GRID, ticks: { maxRotation: 45 } },
        y: { grid: GRID, beginAtZero: true, ...options.y }
      },
      ...options
    }
  });
  el._chart = chart;
}

function gradientLine(ctx, color1, color2) {
  const g = ctx.createLinearGradient(0,0,0,300);
  g.addColorStop(0, color1);
  g.addColorStop(1, color2);
  return g;
}

// ══════════════════════════════════════════════
//  INIT CHARTS
// ══════════════════════════════════════════════
function initCharts() {
  const wkLabels = weeklyData.map(d => 'Wk '+d.wk);
  const wkCreatives = weeklyData.map(d => d.creatives);
  const wkUtil = weeklyData.map(d => d.util);
  const wkAHT = weeklyData.map(d => d.aht);
  const wkHours = weeklyData.map(d => d.hours + d.addHrs);

  // OVERVIEW
  makeChart('chartWeeklyVol','bar',wkLabels,[
    { label:'Creatives',data:wkCreatives,backgroundColor:'rgba(79,126,255,0.7)',borderRadius:4,
      borderColor:'rgba(79,126,255,1)',borderWidth:1 }
  ]);

  makeChart('chartAssetMix','doughnut',
    ['Video','Static','HTML5','Taggable','Spotxpress','Waymark','Print','Carousel','BuzzBoard'],
    [{ data:[635,315,158,182,78,4,11,1,27],
       backgroundColor:['#4f7eff','#22d3a0','#7b5ef8','#f97b2c','#2cd3e1','#f75fc6','#f9c22c','#f94f4f','#8892a4'],
       borderWidth:0,hoverBorderWidth:2,hoverBorderColor:'#fff' }],
    { plugins:{ legend:{ display:true, position:'right' } } }
  );

  makeChart('chartUtilTrend','line',wkLabels,[
    { label:'Utilization %',data:wkUtil,borderColor:'#4f7eff',backgroundColor:'rgba(79,126,255,0.08)',
      fill:true,tension:0.4,pointRadius:3 },
    { label:'Target 85%',data:wkLabels.map(()=>85),borderColor:'rgba(249,79,79,0.4)',
      borderDash:[6,4],fill:false,pointRadius:0,tension:0 }
  ]);

  const monthLabels = ['Jan-25','Feb-25','Mar-25','Apr-25','May-25','Jun-25','Jul-25','Aug-25','Sep-25','Oct-25','Nov-25','Dec-25','Jan-26','Feb-26','Mar-26','May-26'];
  const monthVols = [298,461,484,440,400,355,344,559,475,389,351,333,203,269,267,305];
  makeChart('chartMonthVol','bar',monthLabels,[
    { label:'Monthly Volume',data:monthVols,backgroundColor:'rgba(34,211,160,0.65)',borderRadius:4 }
  ]);

  makeChart('chartAHT','line',wkLabels,[
    { label:'AHT (hrs)',data:wkAHT,borderColor:'#f97b2c',backgroundColor:'rgba(249,123,44,0.08)',
      fill:true,tension:0.4,pointRadius:3 }
  ]);

  const qtrLabels = quarterlyData.map(d=>d.quarter);
  const qtrHours = quarterlyData.map(d=>d.hours);
  makeChart('chartQtrComp','bar',qtrLabels,[
    { label:'Hours',data:qtrHours,backgroundColor:'rgba(123,94,248,0.7)',borderRadius:4 }
  ]);

  // WEEKLY PAGE
  makeChart('chartDailyHrs','bar',['Mon','Tue','Wed','Thu','Fri'],[
    { label:'Production Hrs',data:[42,55,48,38,10.5],backgroundColor:'rgba(79,126,255,0.7)',borderRadius:4 },
    { label:'Add\'l Hrs',data:[7.2,7.2,7.2,7.2,7.2],backgroundColor:'rgba(34,211,160,0.5)',borderRadius:4 }
  ]);
  makeChart('chartTaskDist','doughnut',['New Tasks','Revisions'],[
    { data:[25,13],backgroundColor:['#4f7eff','#22d3a0'],borderWidth:0 }
  ],{ plugins:{ legend:{ display:true,position:'bottom' } } });

  // MONTHLY PAGE
  makeChart('chartMonthlyStack','bar',monthLabels,[
    { label:'Video',data:[170,258,342,180,200,130,171,315,258,224,267,144,116,162,213,250],backgroundColor:'rgba(79,126,255,0.8)',borderRadius:2 },
    { label:'Static',data:[63,146,106,120,80,90,78,127,148,94,57,128,40,23,40,30],backgroundColor:'rgba(34,211,160,0.7)',borderRadius:2 },
    { label:'HTML5',data:[65,57,36,90,70,80,95,117,69,71,27,61,47,84,14,25],backgroundColor:'rgba(123,94,248,0.7)',borderRadius:2 },
  ],{ scales:{ x:{ stacked:true, grid:GRID },y:{ stacked:true, grid:GRID } } });

  makeChart('chartMonthlyUtil','line',monthLabels,[
    { label:'Video',data:[67.6,88.5,94.1,70,65,60,62,72,58,55,69,48,67.5,88.3,75.8,80],borderColor:'#4f7eff',tension:0.4,fill:false,pointRadius:2 },
    { label:'Static',data:[51.1,56.4,51.8,45,42,38,44,52,48,40,65,35,30.7,33.8,32.8,35],borderColor:'#22d3a0',tension:0.4,fill:false,pointRadius:2 },
    { label:'Overall',data:[50.1,65.2,67.0,55,52,47,48,55,50,43,61,38,44.6,57.9,50.3,60],borderColor:'#f97b2c',tension:0.4,fill:false,pointRadius:2 },
  ]);

  // QUARTERLY PAGE
  makeChart('chartQoQVol','bar',qtrLabels,[
    { label:'Total Creatives',data:quarterlyData.map(d=>d.creatives),backgroundColor:'rgba(79,126,255,0.7)',borderRadius:4 }
  ]);

  makeChart('chartQtrBreakdown','bar',qtrLabels,[
    { label:'New',data:quarterlyData.map(d=>d.newT),backgroundColor:'rgba(34,211,160,0.7)',borderRadius:2 },
    { label:'Revisions',data:quarterlyData.map(d=>d.rev),backgroundColor:'rgba(79,126,255,0.7)',borderRadius:2 },
  ],{ scales:{ x:{ stacked:true, grid:GRID },y:{ stacked:true, grid:GRID } } });

  makeChart('chartQtrUtil','line',qtrLabels,[
    { label:'Utilization %',data:quarterlyData.map(d=>d.util),borderColor:'#4f7eff',fill:false,tension:0.4 }
  ]);

  makeChart('chartQtrAHT','line',qtrLabels,[
    { label:'AHT hrs',data:quarterlyData.map(d=>d.aht),borderColor:'#f97b2c',fill:false,tension:0.4 }
  ]);

  // ANNUAL PAGE
  makeChart('chartAnnualVol','line',monthLabels,[
    { label:'Total Volume',data:monthVols,borderColor:'#4f7eff',backgroundColor:'rgba(79,126,255,0.08)',
      fill:true,tension:0.4,pointRadius:3 }
  ]);

  makeChart('chartYoY','bar',['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'],[
    { label:'2025',data:[298,461,484,440,400,355,344,559,475,389,351,333],backgroundColor:'rgba(255,255,255,0.15)',borderRadius:2 },
    { label:'2026',data:[203,269,267,0,305,0,0,0,0,0,0,0],backgroundColor:'rgba(79,126,255,0.7)',borderRadius:2 },
  ]);

  const videoMonthly=[170,258,342,180,200,130,171,315,258,224,267,144,116,162,213,0,250];
  const staticMonthly=[63,146,106,120,80,90,78,127,148,94,57,128,40,23,40,0,30];
  const html5Monthly=[65,57,36,90,70,80,95,117,69,71,27,61,47,84,14,0,25];
  makeChart('chartVideoTrend','line',monthLabels,[
    { label:'Video',data:videoMonthly.slice(0,16),borderColor:'#4f7eff',fill:false,tension:0.4,pointRadius:2 }
  ]);
  makeChart('chartStaticTrend','line',monthLabels,[
    { label:'Static',data:staticMonthly.slice(0,16),borderColor:'#22d3a0',fill:false,tension:0.4,pointRadius:2 }
  ]);
  makeChart('chartHtml5Trend','line',monthLabels,[
    { label:'HTML5',data:html5Monthly.slice(0,16),borderColor:'#7b5ef8',fill:false,tension:0.4,pointRadius:2 }
  ]);

  // QUALITY PAGE
  const qMonths = qualityData.map(d=>d.month);
  makeChart('chartQualityLine','line',qMonths,[
    { label:'Quality %',data:qualityData.map(d=>d.external),borderColor:'#22d3a0',backgroundColor:'rgba(34,211,160,0.08)',fill:true,tension:0.4,pointRadius:4 },
    { label:'Goal 100%',data:qMonths.map(()=>100),borderColor:'rgba(249,79,79,0.4)',borderDash:[6,4],fill:false,pointRadius:0 }
  ],{ y:{ min:98,max:101 } });

  makeChart('chartErrors','bar',qMonths,[
    { label:'Errors',data:qualityData.map(d=>d.errors),backgroundColor:qualityData.map(d=>d.errors>0?'rgba(249,79,79,0.7)':'rgba(34,211,160,0.4)'),borderRadius:4 }
  ]);
}

// ══════════════════════════════════════════════
//  TABLE POPULATION
// ══════════════════════════════════════════════
function populateTables() {
  // Weekly
  const wtb = document.getElementById('weeklyTbody');
  wtb.innerHTML = weeklyData.map(d => `
    <tr>
      <td class="td-bold">${d.week}</td>
      <td class="td-mono">${d.wk}</td>
      <td class="td-bold">${d.tasks}</td>
      <td>${d.newT}</td>
      <td>${d.rev}</td>
      <td class="td-bold" style="color:var(--teal)">${d.creatives}</td>
      <td class="td-mono">${d.aht}</td>
      <td class="td-mono">${d.util.toFixed(2)}%</td>
      <td class="td-mono">${d.hours}</td>
      <td class="td-mono" style="color:var(--yellow)">${d.addHrs}</td>
      <td class="td-mono td-bold">${(d.hours+d.addHrs).toFixed(1)}</td>
      <td class="td-mono">${d.staffed}</td>
      <td><span class="badge ${d.quality>=100?'badge-green':d.quality>=98?'badge-yellow':'badge-red'}">${d.quality}%</span></td>
      <td class="td-mono">${d.issues}</td>
    </tr>
  `).join('');

  // Monthly
  const mtb = document.getElementById('monthlyTbody');
  mtb.innerHTML = monthlyData.map(d => `
    <tr>
      <td class="td-bold">${d.month}</td>
      <td><span class="badge badge-blue">${d.quarter}</span></td>
      <td>${d.newT}</td>
      <td>${d.rev}</td>
      <td class="td-bold" style="color:var(--teal)">${d.creatives}</td>
      <td class="td-mono">${d.hours}</td>
      <td class="td-mono">${d.util.toFixed(2)}%</td>
      <td class="td-mono">${(d.aht*60).toFixed(0)}</td>
      <td><span class="badge ${d.quality>=100?'badge-green':'badge-yellow'}">${d.quality}%</span></td>
      <td class="td-mono">${d.hc}</td>
    </tr>
  `).join('');

  // Quarterly
  const qtb = document.getElementById('qtrTbody');
  qtb.innerHTML = quarterlyData.map(d => `
    <tr>
      <td class="td-bold">${d.quarter}</td>
      <td>${d.tasks}</td>
      <td>${d.newT}</td>
      <td>${d.rev}</td>
      <td class="td-bold" style="color:var(--teal)">${d.creatives}</td>
      <td class="td-mono">${d.hours.toLocaleString()}</td>
      <td class="td-mono">${d.util}%</td>
      <td class="td-mono">${d.aht}</td>
      <td><span class="badge ${d.quality>=100?'badge-green':d.quality>=99?'badge-yellow':'badge-red'}">${d.quality}%</span></td>
      <td class="td-mono">${d.errors}</td>
    </tr>
  `).join('');

  // Quality
  const qttb = document.getElementById('qualityTbody');
  qttb.innerHTML = qualityData.map(d => `
    <tr>
      <td class="td-bold">${d.month}</td>
      <td class="td-mono">${d.tickets.toLocaleString()}</td>
      <td class="td-mono ${d.errors>0?'style="color:var(--red)"':''}">${d.errors}</td>
      <td><span class="badge ${d.external>=100?'badge-green':d.external>=99?'badge-yellow':'badge-red'}">${d.external}%</span></td>
      <td class="td-mono">${d.goal}%</td>
      <td>${d.external>=100?'<span class="badge badge-green">✓ On Target</span>':'<span class="badge badge-yellow">⚠ Below Goal</span>'}</td>
    </tr>
  `).join('');
}

// ══════════════════════════════════════════════
//  EMAIL
// ══════════════════════════════════════════════
function generateEmailPreview() {
  const type = document.getElementById('emailReportType').value;
  const period = document.getElementById('emailPeriod').value;
  document.getElementById('emailSubjectLine').textContent =
    `Subject: CMI Creative Services — ${type==='weekly'?'Weekly':'Daily'} Report | ${period}`;
  const latest = weeklyData[weeklyData.length-1];
  document.getElementById('emailBody').innerHTML = `
    <div style="max-width:600px">
      <div style="background:linear-gradient(135deg,#1565e8,#0f43a8);padding:20px 24px;border-radius:10px;margin-bottom:20px">
        <div style="font-size:18px;font-weight:700;color:#fff;font-family:serif">Mediamint — CMI Creative Services</div>
        <div style="color:rgba(255,255,255,0.7);font-size:12px;margin-top:4px">${type==='weekly'?'Weekly':'Daily'} Performance Report | ${period}</div>
      </div>
      <p style="color:var(--text2)">Hi Team,</p>
      <p style="margin:12px 0;color:var(--text2)">Please find below the ${type==='weekly'?'weekly':'daily'} performance summary for CMI Creative Services.</p>
      <table class="email-table-mini">
        <tr><th>KPI</th><th>Value</th><th>Status</th></tr>
        <tr><td>Total Tasks</td><td>${latest.tasks}</td><td>✅</td></tr>
        <tr><td>New Creatives</td><td>${latest.newT}</td><td>✅</td></tr>
        <tr><td>Revisions</td><td>${latest.rev}</td><td>✅</td></tr>
        <tr><td>Total Creatives</td><td>${latest.creatives}</td><td>✅</td></tr>
        <tr><td>Hours (Production)</td><td>${latest.hours}</td><td>✅</td></tr>
        <tr><td>Additional Hours</td><td>${latest.addHrs}</td><td>✅</td></tr>
        <tr><td>Total Hours</td><td>${(latest.hours+latest.addHrs).toFixed(1)}</td><td>✅</td></tr>
        <tr><td>Utilization</td><td>${latest.util.toFixed(2)}%</td><td>${latest.util>=60?'✅':'⚠️'}</td></tr>
        <tr><td>AHT (hrs/ad)</td><td>${latest.aht}</td><td>✅</td></tr>
        <tr><td>Quality Score</td><td>${latest.quality}%</td><td>${latest.quality>=100?'✅':'⚠️'}</td></tr>
        <tr><td>Quality Issues</td><td>${latest.issues}</td><td>${latest.issues===0?'✅':'⚠️'}</td></tr>
      </table>
      <p style="margin:16px 0 4px;color:var(--text2);font-size:13px"><strong>Team Strength:</strong> 9 active resources (Video: 4, Static/HTML5: 5 | Floater: 1 not counted)</p>
      <p style="color:var(--text2);font-size:13px"><strong>Staffed Hours:</strong> 337.5 hrs/week</p>
      <div style="margin-top:20px;padding-top:16px;border-top:1px solid var(--border);font-size:11px;color:var(--text3)">
        This report was generated automatically from CMI Metrics Dashboard · Mediamint · ${new Date().toLocaleDateString()}
      </div>
    </div>
  `;
}

function sendEmail() {
  alert('Email sent to: ' + document.getElementById('emailRecipients').value + '\n\nIn production, this would integrate with your Gmail connector.');
}
function copyEmailHTML() {
  navigator.clipboard.writeText(document.getElementById('emailBody').innerHTML).then(()=>alert('Email HTML copied to clipboard!'));
}

// ══════════════════════════════════════════════
//  SLIDES
// ══════════════════════════════════════════════
const slideData = [
  { title:'CMI Creative Services', subtitle:'Q1-2026 Business Review', bg:'linear-gradient(135deg,#1565e8 0%,#0a0c10 100%)', accent:'#fff' },
  { title:'Executive Summary', subtitle:'Q1-2026 at a Glance', bg:'var(--surface)', accent:'#4f7eff' },
  { title:'Total Creatives: 759', subtitle:'↑ Volume Growth Q1-26', bg:'var(--surface)', accent:'#22d3a0' },
  { title:'Utilization: 59.0%', subtitle:'Team Efficiency Trend', bg:'var(--surface)', accent:'#f97b2c' },
  { title:'Quality: 99.61%', subtitle:'Near-Zero Error Rate', bg:'var(--surface)', accent:'#f9c22c' },
  { title:'Weekly Performance', subtitle:'Week-by-Week Breakdown', bg:'var(--surface)', accent:'#7b5ef8' },
  { title:'Asset Type Mix', subtitle:'Video / Static / HTML5', bg:'var(--surface)', accent:'#2cd3e1' },
  { title:'Team Resources', subtitle:'Headcount & Capacity', bg:'var(--surface)', accent:'#f75fc6' },
  { title:'Quarterly Comparison', subtitle:'Q4-25 → Q1-26', bg:'var(--surface)', accent:'#4f7eff' },
  { title:'Quality Deep Dive', subtitle:'Error Analysis & QC Coverage', bg:'var(--surface)', accent:'#22d3a0' },
  { title:'Outlook & Targets', subtitle:'Q2-2026 Projections', bg:'linear-gradient(135deg,#1565e8 0%,#0a0c10 100%)', accent:'#fff' },
];

function generateSlides() {
  const grid = document.getElementById('slidesGrid');
  grid.innerHTML = slideData.map((s,i) => `
    <div class="slide-thumb" style="background:${s.bg}" onclick="previewSlide(${i})">
      <div class="slide-bg" style="padding:20px;text-align:center;flex-direction:column;gap:6px">
        <div style="font-size:16px;font-weight:700;color:${s.accent};font-family:'DM Serif Display',serif">${s.title}</div>
        <div style="font-size:11px;color:rgba(255,255,255,0.5)">${s.subtitle}</div>
      </div>
      <div class="slide-footer">
        <div class="slide-num">Slide ${i+1} / ${slideData.length}</div>
        <div class="slide-title">${s.subtitle}</div>
      </div>
    </div>
  `).join('');
}

function previewSlide(i) {
  alert(`Slide ${i+1}: "${slideData[i].title}"\n\nIn production, this opens a full-screen slide viewer with chart exports embedded from the live dashboard data.`);
}

function exportSlides() {
  alert('Generating presentation-ready HTML slides with embedded charts...\n\nIn production, this would:\n• Capture each chart as PNG\n• Generate slide HTML with Mediamint branding\n• Offer download as ZIP or direct upload to Google Slides');
}

// ══════════════════════════════════════════════
//  RESOURCE COUNTERS
// ══════════════════════════════════════════════
function changeCount(team, idx, delta) {
  const el = document.getElementById(`${team}-${idx}`);
  if (!el) return;
  let v = parseInt(el.textContent) + delta;
  if (v < 0) v = 0;
  el.textContent = v;
  updateTotals();
}

function updateTotals() {
  let video = 0, stat = 0;
  for (let i=0;i<10;i++){
    const ve=document.getElementById(`v-${i}`); if(ve) video+=parseInt(ve.textContent||0);
    const se=document.getElementById(`s-${i}`); if(se) stat+=parseInt(se.textContent||0);
  }
  document.getElementById('totalStrength').textContent = video+stat;
  document.getElementById('videoBadge').textContent = video+' member'+(video!==1?'s':'');
  document.getElementById('staticBadge').textContent = stat+' member'+(stat!==1?'s':'');
  document.getElementById('videoCapacity').textContent = (video*7.5*4).toFixed(0)+' hrs/week';
  document.getElementById('staticCapacity').textContent = (stat*7.5*5).toFixed(0)+' hrs/week';
  document.getElementById('kpi-team').textContent = video+stat;
}

// ══════════════════════════════════════════════
//  ADDITIONAL HOURS LOGGING
// ══════════════════════════════════════════════
function logAdditionalHours() {
  const week = document.getElementById('addHrsWeek').value;
  const cat = document.getElementById('addHrsCategory').value;
  const hrs = parseFloat(document.getElementById('addHrsAmt').value)||0;
  const notes = document.getElementById('addHrsNotes').value;
  const prodHrs = weeklyData.find(d=>d.week.includes(week.replace('Week of ','')))||{hours:200};
  const total = (prodHrs.hours||200)+hrs;
  const tbody = document.getElementById('addHrsTbody');
  const row = document.createElement('tr');
  row.innerHTML = `<td class="td-bold">${week}</td><td>${cat}</td><td class="td-mono">${hrs.toFixed(1)}</td><td>${notes||'—'}</td><td class="td-mono td-bold">${total.toFixed(1)}</td>`;
  tbody.insertBefore(row, tbody.firstChild);
}

// ══════════════════════════════════════════════
//  NAVIGATION
// ══════════════════════════════════════════════
function showPage(id) {
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.getElementById('page-'+id).classList.add('active');
  document.querySelectorAll('.nav-tab').forEach(t=>t.classList.remove('active'));
  event.target.classList.add('active');
  if (id==='slides') generateSlides();
  if (id==='email') generateEmailPreview();
}

function setPeriod(p,el) {
  document.querySelectorAll('.period-pills .pill').forEach(x=>x.classList.remove('active'));
  el.classList.add('active');
}

function applyFilters() { /* In production: re-fetch and re-render filtered data */ }
function exportPDF() { window.print(); }
function exportMonthly() { alert('Exporting monthly CSV...'); }
function copyWeeklyReport() { alert('Weekly report copied to clipboard as formatted text.'); }
function updateResourceSnapshot() { /* refresh */ }

// ══════════════════════════════════════════════
//  INIT
// ══════════════════════════════════════════════
document.getElementById('lastUpdated').textContent = new Date().toLocaleString();
initCharts();
populateTables();
generateEmailPreview();
</script>
</body>
</html>
