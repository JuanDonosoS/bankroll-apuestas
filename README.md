[bankroll-dashboard (6).html](https://github.com/user-attachments/files/32190599/bankroll-dashboard.6.html)
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Bankroll — Registro de Apuestas</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Poppins:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/react/18.2.0/umd/react.production.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/react-dom/18.2.0/umd/react-dom.production.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/babel-standalone/7.23.5/babel.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
<style>
  :root{
    --bg: #F2F4FB;
    --card: #FFFFFF;
    --border: #E3E7F5;
    --ink: #1C2140;
    --ink-soft: #6B7091;
    --ink-faint: #9DA2C3;
    --sidebar: #171B33;
    --sidebar-soft: #A7ACD6;
    --sidebar-line: #2A2F52;
    --accent: #4C5FD9;
    --accent-dark: #3F50C4;
    --accent-soft: #EDEFFC;
    --win: #189B57;
    --win-soft: #E4F7EC;
    --lose: #DD4B3E;
    --lose-soft: #FCEAE8;
    --teal: #0C8E88;
    --teal-soft: #E1F5F3;
    --violet: #7C4FD9;
    --violet-soft: #F0EAFB;
    --amber: #B4650F;
    --amber-soft: #FBEEDD;
    --radius-card: 16px;
    --radius-pill: 999px;
    --shadow: 0 1px 2px rgba(28,33,64,0.04), 0 6px 20px rgba(28,33,64,0.05);
    --shadow-pop: 0 12px 32px rgba(23,27,51,0.16);
  }
  *{box-sizing:border-box;}
  html,body{margin:0;padding:0;}
  body{
    font-family: 'Poppins', sans-serif;
    background: var(--bg);
    color: var(--ink);
    -webkit-font-smoothing: antialiased;
  }
  #root{min-height:100vh;}
  ::-webkit-scrollbar{width:10px; height:10px;}
  ::-webkit-scrollbar-thumb{background:#D3D8ED; border-radius: 8px;}
  ::-webkit-scrollbar-track{background:transparent;}

  .app{ display:flex; min-height:100vh; }

  /* ---------- SIDEBAR ---------- */
  .sidebar{
    width: 252px;
    flex-shrink:0;
    background: var(--sidebar);
    color: var(--sidebar-soft);
    padding: 28px 18px;
    display:flex;
    flex-direction:column;
    position: sticky;
    top:0;
    height:100vh;
  }
  .brand{ display:flex; align-items:center; gap:11px; padding: 4px 8px 26px 8px; }
  .brand-mark{
    width:36px; height:36px; border-radius:11px;
    background: linear-gradient(145deg, #6472F0, #4C5FD9 60%, #3C4CC0);
    display:flex; align-items:center; justify-content:center; flex-shrink:0;
  }
  .brand-name{ font-weight:700; font-size:16.5px; color:#fff; letter-spacing:0.2px; line-height:1.15; }
  .brand-sub{ font-size:11px; color:#8489B8; font-weight:500; margin-top:1px; }
  .nav-section-label{
    font-size:10.5px; font-weight:600; color:#585D8C;
    text-transform: uppercase; letter-spacing:0.09em; padding: 14px 12px 8px 12px;
  }
  .nav-item{
    display:flex; align-items:center; gap:11px; padding: 10px 12px; border-radius: 10px;
    cursor:pointer; font-size:13.6px; font-weight:500; color: var(--sidebar-soft);
    margin-bottom:2px; transition: background .15s ease, color .15s ease; position:relative;
  }
  .nav-item svg{ flex-shrink:0; opacity:0.85; }
  .nav-item:hover{ background: #1F2447; color:#fff; }
  .nav-item.active{ background: #262C55; color:#fff; }
  .nav-item.active svg{ opacity:1; }
  .nav-item.active::before{
    content:""; position:absolute; left:-18px; top:8px; bottom:8px; width:3px;
    background: var(--accent); border-radius: 0 4px 4px 0;
  }
  .nav-item.disabled{ cursor:default; opacity:0.55; }
  .nav-item.disabled:hover{ background:transparent; color:var(--sidebar-soft); }
  .soon-badge{
    margin-left:auto; font-size:9px; font-weight:600; background:#2A2F52; color:#9AA0D6;
    padding: 2.5px 7px; border-radius: 999px; letter-spacing:0.03em;
  }
  .sidebar-foot{
    margin-top:auto; padding: 14px 12px; border-top: 1px solid var(--sidebar-line);
    font-size:11.5px; color:#6E739F; line-height:1.5;
  }
  .sidebar-foot b{ color:#B7BBE4; }
  .logout-btn{
    display:flex; align-items:center; gap:9px; padding:10px 10px; border-radius:10px;
    color:#B7BBE4; font-size:13px; font-weight:500; cursor:pointer;
  }
  .logout-btn:hover{ background:#1F2447; color:#fff; }

  /* ---------- MAIN ---------- */
  .main{ flex:1; min-width:0; padding: 30px 38px 60px 38px; }
  .topbar{ display:flex; align-items:flex-start; justify-content:space-between; gap:20px; margin-bottom:22px; flex-wrap:wrap; }
  .page-title{ font-size:23px; font-weight:700; margin:0 0 4px 0; letter-spacing:-0.2px; }
  .page-subtitle{ font-size:13.5px; color:var(--ink-soft); margin:0; font-weight:400; }

  .topbar-right{ display:flex; flex-direction:column; align-items:flex-end; gap:10px; }

  .month-filter{
    display:flex; gap:6px; align-items:center; background:#fff; padding:5px;
    border-radius: var(--radius-pill); border:1px solid var(--border);
    box-shadow: var(--shadow); flex-wrap:wrap;
  }
  .month-pill{
    padding: 7px 16px; border-radius: var(--radius-pill); font-size:12.8px; font-weight:500;
    color: var(--ink-soft); cursor:pointer; white-space:nowrap; transition: background .15s ease, color .15s ease;
  }
  .month-pill:hover{ background:#F3F4FC; }
  .month-pill.active{ background: var(--ink); color:#fff; }
  .month-dropdown-btn{
    display:flex; align-items:center; gap:7px; padding: 7px 15px; border-radius: var(--radius-pill);
    font-size:12.8px; font-weight:500; color: var(--ink-soft); cursor:pointer; white-space:nowrap;
    transition: background .15s ease, color .15s ease;
  }
  .month-dropdown-btn:hover{ background:#F3F4FC; }
  .month-dropdown-btn.active{ background: var(--ink); color:#fff; }
  .month-dropdown-popover{ padding:8px; }
  .month-option{
    padding:9px 12px; border-radius:9px; font-size:13px; font-weight:500; color:var(--ink);
    cursor:pointer; display:flex; align-items:center; gap:8px;
  }
  .month-option:hover{ background:#F3F4FC; }
  .month-option.selected{ background: var(--accent-soft); color: var(--accent); font-weight:600; }
  .month-option.add{ color: var(--accent); font-weight:600; border-top:1px solid var(--border); margin-top:4px; padding-top:12px; border-radius:0 0 9px 9px; }
  .month-option.add:hover{ background:#F7F8FD; }

  .btn-primary{
    background: var(--accent); color:#fff; padding:10px 17px; border-radius:11px;
    font-weight:600; font-size:13px; display:flex; align-items:center; gap:7px;
    cursor:pointer; border:none; box-shadow: var(--shadow); font-family:'Poppins',sans-serif;
    white-space:nowrap;
  }
  .btn-primary:hover{ background: var(--accent-dark); }
  .btn-secondary{
    background:#fff; border:1px solid var(--border); color:var(--ink); padding:9px 14px;
    border-radius:11px; font-weight:500; font-size:12.8px; display:flex; align-items:center;
    gap:7px; cursor:pointer; box-shadow:var(--shadow); font-family:'Poppins',sans-serif;
    white-space:nowrap;
  }
  .btn-secondary:hover{ background:#F7F8FD; }
  .btn-secondary[disabled]{ opacity:0.5; cursor:not-allowed; }
  .btn-secondary[disabled]:hover{ background:#fff; }

  /* ---------- POPOVERS ---------- */
  .popover-anchor{ position:relative; }
  .popover{
    position:absolute; top:calc(100% + 10px); right:0; width:308px; background:#fff;
    border:1px solid var(--border); border-radius: 15px; box-shadow: var(--shadow-pop);
    padding: 18px 18px 16px 18px; z-index: 60; text-align:left;
  }
  .popover-title{ font-size:13.5px; font-weight:600; margin:0 0 3px 0; }
  .popover-sub{ font-size:11.5px; color:var(--ink-soft); margin:0 0 14px 0; }
  .field-row{ margin-bottom:12px; }
  .field-label{ font-size:11.3px; font-weight:600; color:var(--ink-soft); margin-bottom:5px; display:block; }
  .field-input, .field-select, .field-textarea{
    width:100%; padding:9px 11px; border:1px solid var(--border); border-radius:9px;
    font-family:'Poppins',sans-serif; font-size:12.8px; outline:none; color:var(--ink); background:#fff;
  }
  .field-input:focus, .field-select:focus, .field-textarea:focus{ border-color: var(--accent); }
  .vs-row{ display:flex; align-items:center; gap:8px; }
  .vs-row .field-input{ flex:1; min-width:0; }
  .vs-tag{ font-size:11.5px; font-weight:600; color:var(--ink-faint); flex-shrink:0; }
  .currency-input{
    display:flex; align-items:stretch; border:1px solid var(--border); border-radius:9px;
    background:#fff; overflow:hidden; transition: border-color .15s ease;
  }
  .currency-input:focus-within{ border-color: var(--accent); }
  .currency-prefix{
    display:flex; align-items:center; padding: 0 0 0 11px; font-size:12.8px; font-weight:600; color:var(--ink-soft);
  }
  .currency-field{
    border:none; outline:none; flex:1; padding:9px 11px 9px 3px; font-family:'Poppins',sans-serif;
    font-size:12.8px; color:var(--ink); background:transparent;
  }
  .field-hint{ font-size:10.8px; color:var(--ink-faint); margin-top:5px; }
  .popover-actions{ display:flex; gap:8px; margin-top:14px; }
  .popover-actions .btn-primary, .popover-actions .btn-ghost{ flex:1; justify-content:center; }
  .btn-ghost{
    background:#F4F5FB; color:var(--ink-soft); padding:10px 14px; border-radius:11px;
    font-weight:500; font-size:12.8px; border:none; cursor:pointer; font-family:'Poppins',sans-serif;
  }
  .btn-ghost:hover{ background:#ECEEF8; }
  .field-error{ font-size:11px; color:var(--lose); margin-top:6px; }
  .overlay-catch{ position:fixed; inset:0; z-index:55; }

  /* ---------- KPI CARDS ---------- */
  .kpi-grid{ display:grid; grid-template-columns: repeat(5, 1fr); gap:16px; margin-bottom: 22px; }
  .kpi-card{
    background: var(--card); border: 1px solid var(--border); border-radius: var(--radius-card);
    padding: 18px 18px 16px 18px; box-shadow: var(--shadow); position:relative; overflow:hidden;
  }
  .kpi-top{ display:flex; align-items:center; justify-content:space-between; margin-bottom:14px; }
  .kpi-icon{ width:34px; height:34px; border-radius:10px; display:flex; align-items:center; justify-content:center; }
  .kpi-label{ font-size:12px; color: var(--ink-soft); font-weight:500; margin-bottom:6px; }
  .kpi-value{ font-size:21px; font-weight:700; letter-spacing:-0.3px; line-height:1.15; }
  .kpi-delta{ font-size:11.5px; font-weight:600; margin-top:7px; display:flex; align-items:center; gap:4px; }

  /* ---------- PANELS / CHARTS ---------- */
  .panel-row{ display:grid; grid-template-columns: 1.6fr 1fr; gap:16px; margin-bottom:16px; align-items:stretch; }
  .panel-row.thirds{ grid-template-columns: 1fr 1fr; }
  .panel{
    background: var(--card); border:1px solid var(--border); border-radius: var(--radius-card);
    box-shadow: var(--shadow); padding: 20px 22px 18px 22px; display:flex; flex-direction:column;
  }
  .panel-head{ display:flex; align-items:flex-start; justify-content:space-between; margin-bottom:14px; gap:10px;}
  .panel-title{ font-size:14.5px; font-weight:600; margin:0 0 2px 0; }
  .panel-sub{ font-size:11.8px; color:var(--ink-soft); margin:0; }
  .legend-dot{ width:8px; height:8px; border-radius:50%; display:inline-block; margin-right:6px; }
  .mini-legend{ display:flex; gap:16px; font-size:11.8px; color:var(--ink-soft); margin-top:2px; flex-wrap:wrap; }
  .chart-wrap{ position:relative; flex:1; min-height: 230px; }
  .chart-wrap.small{ min-height:200px; }
  .donut-center{ position:absolute; top:50%; left:50%; transform:translate(-50%,-50%); text-align:center; pointer-events:none; }
  .donut-center .num{ font-size: 24px; font-weight:700; color:var(--ink); }
  .donut-center .lbl{ font-size:10.5px; color:var(--ink-soft); font-weight:500; margin-top:1px; }

  /* ---------- TABLE (Historial) ---------- */
  .toolbar{ display:flex; align-items:center; justify-content:space-between; gap:14px; margin-bottom:16px; flex-wrap:wrap; }
  .toolbar-left{ display:flex; align-items:center; gap:10px; flex-wrap:wrap; }
  .search-box{
    display:flex; align-items:center; gap:8px; background:#fff; border:1px solid var(--border);
    border-radius: 11px; padding: 9px 14px; min-width: 260px; box-shadow: var(--shadow);
  }
  .search-box input{
    border:none; outline:none; font-family:'Poppins',sans-serif; font-size:13px; color:var(--ink);
    width:100%; background:transparent;
  }
  .search-box input::placeholder{ color: var(--ink-faint); }
  .result-filters{ display:flex; gap:6px; }
  .rf-chip{
    padding:8px 14px; border-radius:10px; font-size:12.5px; font-weight:500; background:#fff;
    border:1px solid var(--border); color:var(--ink-soft); cursor:pointer; box-shadow: var(--shadow);
  }
  .rf-chip.active-all{ background:var(--ink); color:#fff; border-color:var(--ink); }
  .rf-chip.active-win{ background:var(--win-soft); color:var(--win); border-color:#BEE9CF; }
  .rf-chip.active-lose{ background:var(--lose-soft); color:var(--lose); border-color:#F7CEC9; }

  .table-panel{ background:var(--card); border:1px solid var(--border); border-radius: var(--radius-card); box-shadow: var(--shadow); overflow:hidden; }
  table{ width:100%; border-collapse:collapse; font-size:13px; }
  thead th{
    text-align:left; font-size:11px; font-weight:600; color: var(--ink-soft); text-transform:uppercase;
    letter-spacing:0.04em; padding: 13px 16px; border-bottom:1px solid var(--border); background:#FAFBFE; position:sticky; top:0;
  }
  tbody td{ padding: 12px 16px; border-bottom:1px solid #F0F1F9; color: var(--ink); vertical-align: middle; }
  tbody tr:last-child td{ border-bottom:none; }
  tbody tr:hover{ background:#FAFBFF; }
  .pron-cell{ max-width: 380px; }
  .pron-text{ font-weight:500; font-size:13px; line-height:1.4; }
  .pron-text-sub{ font-weight:400; font-size:12.5px; line-height:1.4; color: var(--ink-soft); }
  .tipo-badge{
    display:inline-flex; align-items:center; padding:4px 10px; border-radius:999px;
    background: var(--violet-soft); color: var(--violet); font-size:11px; font-weight:600; white-space:nowrap;
  }
  .deporte-badge{
    display:inline-flex; align-items:center; padding:4px 10px; border-radius:999px;
    background: var(--teal-soft); color: var(--teal); font-size:11px; font-weight:600; white-space:nowrap;
  }
  .date-badge{
    display:inline-flex; align-items:center; padding:4px 9px; border-radius:8px;
    background:#F1F2FA; color:var(--ink-soft); font-size:11.5px; font-weight:600; white-space:nowrap;
  }
  .badge{ display:inline-flex; align-items:center; gap:5px; padding: 4px 10px; border-radius: 999px; font-size:11.5px; font-weight:600; }
  .badge.win{ background:var(--win-soft); color:var(--win); }
  .badge.lose{ background:var(--lose-soft); color:var(--lose); }
  .badge.pending{ background:var(--amber-soft); color:var(--amber); }
  .num-cell{ font-variant-numeric: tabular-nums; font-weight:500; }
  .bal-pos{ color: var(--win); font-weight:600; font-variant-numeric: tabular-nums; }
  .bal-neg{ color: var(--lose); font-weight:600; font-variant-numeric: tabular-nums; }
  .bal-pending{ color: var(--ink-faint); font-weight:500; }
  .table-scroll{ max-height: 560px; overflow-y:auto; }
  .table-foot{
    display:flex; align-items:center; justify-content:space-between; padding: 12px 18px; font-size:12px;
    color: var(--ink-soft); border-top:1px solid var(--border); background:#FAFBFE;
  }
  .empty-state{ padding: 60px 20px; text-align:center; color:var(--ink-soft); font-size:13.5px; }

  .row-edit-btn{
    width:30px; height:30px; border-radius:9px; border:1px solid var(--border); background:#fff;
    display:flex; align-items:center; justify-content:center; cursor:pointer;
  }
  .row-edit-btn:hover{ background:#F3F4FC; border-color:#C9CEE8; }
  .row-delete-btn:hover{ background: var(--lose-soft); border-color:#F7CEC9; }
  .result-edit{ display:flex; gap:5px; flex-wrap:wrap; }
  .result-edit button{
    border:none; cursor:pointer; font-family:'Poppins',sans-serif; font-size:11px; font-weight:600;
    padding: 5px 9px; border-radius: 999px;
  }
  .result-edit .opt-win{ background:var(--win-soft); color:var(--win); }
  .result-edit .opt-win.chosen{ background:var(--win); color:#fff; }
  .result-edit .opt-lose{ background:var(--lose-soft); color:var(--lose); }
  .result-edit .opt-lose.chosen{ background:var(--lose); color:#fff; }
  .result-edit .opt-pending{ background:#F1F2FA; color:var(--ink-soft); }
  .result-edit .opt-pending.chosen{ background:var(--ink-faint); color:#fff; }

  /* ---------- placeholder pages ---------- */
  .placeholder{ background:var(--card); border:1px dashed #C9CEE8; border-radius: var(--radius-card); padding: 70px 40px; text-align:center; color:var(--ink-soft); }
  .placeholder h3{ color:var(--ink); font-size:17px; margin:14px 0 6px 0; }
  .placeholder p{ font-size:13.5px; max-width:420px; margin:0 auto; line-height:1.6; }

  @media (max-width: 1180px){ .kpi-grid{ grid-template-columns: repeat(3, 1fr); } .panel-row{ grid-template-columns: 1fr; } }
  /* ---------- AUTENTICACIÓN ---------- */
  .auth-shell{
    min-height:100vh; display:flex; align-items:center; justify-content:center;
    background: var(--bg); padding: 24px;
  }
  .auth-loading{ color: var(--ink-soft); font-size:14px; }
  .auth-card{
    width:100%; max-width:380px; background:var(--card); border:1px solid var(--border);
    border-radius: 20px; box-shadow: var(--shadow-pop); padding: 30px 30px 26px 30px;
  }
  .auth-brand{ display:flex; align-items:center; gap:11px; margin-bottom:22px; }
  .auth-brand-name{ font-weight:700; font-size:17px; color: var(--ink); letter-spacing:0.2px; line-height:1.15; }
  .auth-brand-sub{ font-size:11px; color:var(--ink-soft); font-weight:500; margin-top:1px; }
  .auth-title{ font-size:16px; font-weight:700; margin:0 0 4px 0; color:var(--ink); }
  .auth-sub{ font-size:12.5px; color:var(--ink-soft); margin:0 0 16px 0; line-height:1.5; }
  .auth-links{ display:flex; justify-content:space-between; margin-top:14px; flex-wrap:wrap; gap:8px; }
  .auth-link{ font-size:12px; color: var(--accent); font-weight:600; cursor:pointer; }
  .auth-link:hover{ text-decoration:underline; }

  @media (max-width: 760px){ .sidebar{ display:none; } .kpi-grid{ grid-template-columns: repeat(2, 1fr); } .main{ padding: 20px; } }
</style>
</head>
<body>
<div id="root"></div>
<script type="text/babel" data-presets="react">

const { useState, useMemo, useRef, useEffect } = React;

/* ============ SUPABASE ============ */
const SUPABASE_URL = "https://vgtryiirgjvsbgljxhky.supabase.co";
const SUPABASE_PUBLISHABLE_KEY = "sb_publishable_Yn1kxiC6yMXYq-pUAl9GZg_BB3kIk4R";
const supabaseClient = supabase.createClient(SUPABASE_URL, SUPABASE_PUBLISHABLE_KEY);

/* ============ ICONOS SVG (dibujados a mano, sin librerías) ============ */
const IconGrid = ({color="currentColor"}) => (
  <svg width="17" height="17" viewBox="0 0 24 24" fill="none">
    <rect x="3" y="3" width="8" height="8" rx="2" stroke={color} strokeWidth="1.8"/>
    <rect x="13" y="3" width="8" height="8" rx="2" stroke={color} strokeWidth="1.8"/>
    <rect x="3" y="13" width="8" height="8" rx="2" stroke={color} strokeWidth="1.8"/>
    <rect x="13" y="13" width="8" height="8" rx="2" stroke={color} strokeWidth="1.8"/>
  </svg>
);
const IconList = ({color="currentColor"}) => (
  <svg width="17" height="17" viewBox="0 0 24 24" fill="none">
    <path d="M8 6h13" stroke={color} strokeWidth="1.8" strokeLinecap="round"/>
    <path d="M8 12h13" stroke={color} strokeWidth="1.8" strokeLinecap="round"/>
    <path d="M8 18h13" stroke={color} strokeWidth="1.8" strokeLinecap="round"/>
    <circle cx="3.2" cy="6" r="1.4" fill={color}/>
    <circle cx="3.2" cy="12" r="1.4" fill={color}/>
    <circle cx="3.2" cy="18" r="1.4" fill={color}/>
  </svg>
);
const IconPlus = ({color="currentColor"}) => (
  <svg width="16" height="16" viewBox="0 0 24 24" fill="none">
    <path d="M12 5v14M5 12h14" stroke={color} strokeWidth="2.2" strokeLinecap="round"/>
  </svg>
);
const IconChevronDown = ({color="currentColor"}) => (
  <svg width="13" height="13" viewBox="0 0 24 24" fill="none">
    <path d="M6 9l6 6 6-6" stroke={color} strokeWidth="2.1" strokeLinecap="round" strokeLinejoin="round"/>
  </svg>
);
const IconPlusCircle = ({color="currentColor"}) => (
  <svg width="17" height="17" viewBox="0 0 24 24" fill="none">
    <circle cx="12" cy="12" r="9" stroke={color} strokeWidth="1.8"/>
    <path d="M12 8v8M8 12h8" stroke={color} strokeWidth="1.8" strokeLinecap="round"/>
  </svg>
);
const IconGear = ({color="currentColor"}) => (
  <svg width="17" height="17" viewBox="0 0 24 24" fill="none">
    <circle cx="12" cy="12" r="3.2" stroke={color} strokeWidth="1.8"/>
    <path d="M12 3.5v2.2M12 18.3v2.2M20.5 12h-2.2M5.7 12H3.5M17.7 6.3l-1.55 1.55M7.85 16.15L6.3 17.7M17.7 17.7l-1.55-1.55M7.85 7.85L6.3 6.3" stroke={color} strokeWidth="1.8" strokeLinecap="round"/>
  </svg>
);
const IconWallet = ({color="currentColor"}) => (
  <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
    <rect x="3" y="6" width="18" height="13" rx="2.5" stroke={color} strokeWidth="1.8"/>
    <path d="M3 10h18" stroke={color} strokeWidth="1.8"/>
    <circle cx="16.5" cy="14" r="1.3" fill={color}/>
    <path d="M7 6V5.2C7 4 8 3 9.3 3h5.4C16 3 17 4 17 5.2V6" stroke={color} strokeWidth="1.8"/>
  </svg>
);
const IconTrend = ({color="currentColor"}) => (
  <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
    <path d="M3 17l6-6.5 4 4L21 6" stroke={color} strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"/>
    <path d="M15 6h6v6" stroke={color} strokeWidth="2" strokeLinecap="round" strokeLinejoin="round"/>
  </svg>
);
const IconTarget = ({color="currentColor"}) => (
  <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
    <circle cx="12" cy="12" r="8.3" stroke={color} strokeWidth="1.8"/>
    <circle cx="12" cy="12" r="4.6" stroke={color} strokeWidth="1.8"/>
    <circle cx="12" cy="12" r="1.1" fill={color}/>
  </svg>
);
const IconTicket = ({color="currentColor"}) => (
  <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
    <path d="M3 9.5a2 2 0 0 1 0-4h18a2 2 0 0 1 0 4 1.7 1.7 0 0 0 0 3.4A2 2 0 0 1 21 16.5H3a2 2 0 0 1 0-3.6 1.7 1.7 0 0 0 0-3.4Z" stroke={color} strokeWidth="1.7" strokeLinejoin="round"/>
    <path d="M9 6v12" stroke={color} strokeWidth="1.7" strokeDasharray="2.4 2.4"/>
  </svg>
);
const IconFlame = ({color="currentColor"}) => (
  <svg width="18" height="18" viewBox="0 0 24 24" fill="none">
    <path d="M12 3c1 3-2.5 3.6-2.5 6.7A2.5 2.5 0 0 0 12 12.2a2.5 2.5 0 0 0 2.5-2.5c1.5 1 2.5 2.9 2.5 4.8a5 5 0 1 1-10 0c0-3.4 2-4.7 3-6C10.6 7.4 11.6 5.4 12 3Z" stroke={color} strokeWidth="1.7" strokeLinejoin="round"/>
  </svg>
);
const IconSearch = ({color="currentColor"}) => (
  <svg width="15" height="15" viewBox="0 0 24 24" fill="none">
    <circle cx="10.5" cy="10.5" r="6.5" stroke={color} strokeWidth="1.9"/>
    <path d="M20 20l-4.3-4.3" stroke={color} strokeWidth="1.9" strokeLinecap="round"/>
  </svg>
);
const IconCoin = ({color="currentColor"}) => (
  <svg width="15" height="15" viewBox="0 0 24 24" fill="none">
    <circle cx="12" cy="12" r="8.5" stroke={color} strokeWidth="1.8"/>
    <path d="M12 7.5v9M9.7 9.3c0-1 1-1.8 2.3-1.8s2.3.7 2.3 1.6c0 2.3-4.6 1-4.6 3.3 0 .9 1 1.6 2.3 1.6s2.3-.8 2.3-1.8" stroke={color} strokeWidth="1.6" strokeLinecap="round"/>
  </svg>
);
const IconPencil = ({color="currentColor"}) => (
  <svg width="14" height="14" viewBox="0 0 24 24" fill="none">
    <path d="M4 20l.9-3.9L15.6 5.4a1.6 1.6 0 0 1 2.3 0l.7.7a1.6 1.6 0 0 1 0 2.3L8 19.1 4 20Z" stroke={color} strokeWidth="1.7" strokeLinejoin="round"/>
    <path d="M14 7l3 3" stroke={color} strokeWidth="1.7"/>
  </svg>
);
const IconClose = ({color="currentColor"}) => (
  <svg width="14" height="14" viewBox="0 0 24 24" fill="none">
    <path d="M6 6l12 12M18 6L6 18" stroke={color} strokeWidth="1.9" strokeLinecap="round"/>
  </svg>
);
const IconTrash = ({color="currentColor"}) => (
  <svg width="14" height="14" viewBox="0 0 24 24" fill="none">
    <path d="M4 7h16" stroke={color} strokeWidth="1.7" strokeLinecap="round"/>
    <path d="M9 7V5.2C9 4.5 9.6 4 10.3 4h3.4c.7 0 1.3.5 1.3 1.2V7" stroke={color} strokeWidth="1.7" strokeLinecap="round" strokeLinejoin="round"/>
    <path d="M6 7l.8 12.1c.05.8.7 1.4 1.5 1.4h7.4c.8 0 1.45-.6 1.5-1.4L18 7" stroke={color} strokeWidth="1.7" strokeLinecap="round" strokeLinejoin="round"/>
    <path d="M10 11v6M14 11v6" stroke={color} strokeWidth="1.7" strokeLinecap="round"/>
  </svg>
);
const IconLogout = ({color="currentColor"}) => (
  <svg width="15" height="15" viewBox="0 0 24 24" fill="none">
    <path d="M9 4H6.5A2.5 2.5 0 0 0 4 6.5v11A2.5 2.5 0 0 0 6.5 20H9" stroke={color} strokeWidth="1.7" strokeLinecap="round" strokeLinejoin="round"/>
    <path d="M15 16l4-4-4-4" stroke={color} strokeWidth="1.7" strokeLinecap="round" strokeLinejoin="round"/>
    <path d="M19 12H9" stroke={color} strokeWidth="1.7" strokeLinecap="round"/>
  </svg>
);

/* ============ INPUT DE MONTO CON FORMATO DE MILES ============ */
function CurrencyInput({value, onChange, placeholder}){
  const display = (value === "" || value === null || value === undefined || isNaN(value))
    ? "" : Number(value).toLocaleString("es-CL");
  function handleChange(e){
    const raw = e.target.value.replace(/[^\d]/g, "");
    onChange(raw === "" ? "" : parseInt(raw, 10));
  }
  return (
    <div className="currency-input">
      <span className="currency-prefix">$</span>
      <input className="currency-field" inputMode="numeric" value={display} onChange={handleChange} placeholder={placeholder} />
    </div>
  );
}

/* ============ DEPORTES, TIPOS DE APUESTA Y DETALLE ============ */
const TIPOS_FUTBOL = [
  "Resultado (1X2)",
  "Doble oportunidad",
  "Hándicap",
  "Más/Menos goles",
  "Ambos equipos anotan",
  "Resultado exacto",
  "Primer tiempo / Final",
  "Córners",
  "Tarjetas",
  "Goleador",
  "Combinada (varios mercados)",
  "Otro",
];
const TIPOS_TENIS = [
  "Ganador del partido",
  "Hándicap de sets",
  "Más/Menos sets",
  "Más/Menos games",
  "Ganador del primer set",
  "Combinada (varios mercados)",
  "Otro",
];
const TIPOS_NBA = [
  "Ganador (Moneyline)",
  "Hándicap (Spread)",
  "Más/Menos puntos (Total)",
  "Anotador principal",
  "Combinada (varios mercados)",
  "Otro",
];
const TIPOS_NFL = [
  "Ganador (Moneyline)",
  "Hándicap (Spread)",
  "Más/Menos puntos (Total)",
  "Touchdown anotador",
  "Combinada (varios mercados)",
  "Otro",
];
const TIPOS_MLB = [
  "Ganador (Moneyline)",
  "Línea de carreras (Run line)",
  "Más/Menos carreras (Total)",
  "Combinada (varios mercados)",
  "Otro",
];

const DEPORTES = [
  { key: "futbol", label: "Fútbol", tipos: TIPOS_FUTBOL },
  { key: "tenis", label: "Tenis", tipos: TIPOS_TENIS },
  { key: "nba", label: "NBA", tipos: TIPOS_NBA },
  { key: "nfl", label: "NFL", tipos: TIPOS_NFL },
  { key: "mlb", label: "MLB", tipos: TIPOS_MLB },
  { key: "otro", label: "Otro deporte", tipos: null },
];
function deporteLabel(key){ const d = DEPORTES.find(d=>d.key===key); return d ? d.label : key; }

const OTRO_DETALLE = "Otro (especificar)";

function getOpcionesDetalle(deporte, tipo, equipoLocal, equipoVisitante){
  const local = (equipoLocal || "").trim() || "el equipo/jugador local";
  const visitante = (equipoVisitante || "").trim() || "el equipo/jugador visitante";

  if(deporte === "futbol"){
    switch(tipo){
      case "Resultado (1X2)":
        return ["Gana " + local, "Empate", "Gana " + visitante];
      case "Doble oportunidad":
        return ["Gana " + local + " o empate", "Gana " + visitante + " o empate", "Gana " + local + " o " + visitante];
      case "Hándicap":
        return ["Hándicap " + local + " -2", "Hándicap " + local + " -1", "Hándicap " + local + " +1", "Hándicap " + local + " +2",
          "Hándicap " + visitante + " -2", "Hándicap " + visitante + " -1", "Hándicap " + visitante + " +1", "Hándicap " + visitante + " +2"];
      case "Más/Menos goles":
        return ["Más de 0.5 goles", "Más de 1.5 goles", "Más de 2.5 goles", "Más de 3.5 goles", "Más de 4.5 goles",
          "Menos de 0.5 goles", "Menos de 1.5 goles", "Menos de 2.5 goles", "Menos de 3.5 goles", "Menos de 4.5 goles"];
      case "Ambos equipos anotan":
        return ["Sí, anotan " + local + " y " + visitante, "No, al menos uno de los dos no anota"];
      case "Resultado exacto":
        return ["2-0", "2-1", "3-0", "3-1", "1-1", "0-0", "0-1", "0-2", "1-2", "1-0", "2-3"].map(marcador => {
          const [gl, gv] = marcador.split("-");
          return local + " " + gl + " - " + gv + " " + visitante;
        });
      case "Primer tiempo / Final":
        return [local + " / " + local, local + " / Empate", local + " / " + visitante,
          "Empate / " + local, "Empate / Empate", "Empate / " + visitante,
          visitante + " / " + local, visitante + " / Empate", visitante + " / " + visitante];
      case "Córners":
        return ["Más de 7.5 córners", "Más de 8.5 córners", "Más de 9.5 córners", "Más de 10.5 córners",
          "Menos de 7.5 córners", "Menos de 8.5 córners", "Menos de 9.5 córners", "Menos de 10.5 córners"];
      case "Tarjetas":
        return ["Más de 2.5 tarjetas", "Más de 3.5 tarjetas", "Más de 4.5 tarjetas", "Más de 5.5 tarjetas",
          "Menos de 2.5 tarjetas", "Menos de 3.5 tarjetas", "Menos de 4.5 tarjetas", "Menos de 5.5 tarjetas"];
      default:
        return null;
    }
  }

  if(deporte === "tenis"){
    switch(tipo){
      case "Ganador del partido":
        return ["Gana " + local, "Gana " + visitante];
      case "Hándicap de sets":
        return ["Hándicap " + local + " -1.5", "Hándicap " + local + " +1.5", "Hándicap " + visitante + " -1.5", "Hándicap " + visitante + " +1.5"];
      case "Más/Menos sets":
        return ["Más de 2.5 sets", "Menos de 2.5 sets"];
      case "Más/Menos games":
        return ["Más de 20.5 games", "Más de 21.5 games", "Más de 22.5 games",
          "Menos de 20.5 games", "Menos de 21.5 games", "Menos de 22.5 games"];
      case "Ganador del primer set":
        return ["Gana " + local + " el primer set", "Gana " + visitante + " el primer set"];
      default:
        return null;
    }
  }

  if(deporte === "nba"){
    switch(tipo){
      case "Ganador (Moneyline)":
        return ["Gana " + local, "Gana " + visitante];
      case "Hándicap (Spread)":
        return ["Hándicap " + local + " -3.5", "Hándicap " + local + " +3.5", "Hándicap " + local + " -5.5", "Hándicap " + local + " +5.5",
          "Hándicap " + visitante + " -3.5", "Hándicap " + visitante + " +3.5", "Hándicap " + visitante + " -5.5", "Hándicap " + visitante + " +5.5"];
      case "Más/Menos puntos (Total)":
        return ["Más de 215.5 puntos", "Más de 220.5 puntos", "Más de 225.5 puntos",
          "Menos de 215.5 puntos", "Menos de 220.5 puntos", "Menos de 225.5 puntos"];
      default:
        return null;
    }
  }

  if(deporte === "nfl"){
    switch(tipo){
      case "Ganador (Moneyline)":
        return ["Gana " + local, "Gana " + visitante];
      case "Hándicap (Spread)":
        return ["Hándicap " + local + " -3.5", "Hándicap " + local + " +3.5", "Hándicap " + local + " -6.5", "Hándicap " + local + " +6.5",
          "Hándicap " + visitante + " -3.5", "Hándicap " + visitante + " +3.5", "Hándicap " + visitante + " -6.5", "Hándicap " + visitante + " +6.5"];
      case "Más/Menos puntos (Total)":
        return ["Más de 41.5 puntos", "Más de 44.5 puntos", "Más de 47.5 puntos",
          "Menos de 41.5 puntos", "Menos de 44.5 puntos", "Menos de 47.5 puntos"];
      default:
        return null;
    }
  }

  if(deporte === "mlb"){
    switch(tipo){
      case "Ganador (Moneyline)":
        return ["Gana " + local, "Gana " + visitante];
      case "Línea de carreras (Run line)":
        return [local + " -1.5", local + " +1.5", visitante + " -1.5", visitante + " +1.5"];
      case "Más/Menos carreras (Total)":
        return ["Más de 7.5 carreras", "Más de 8.5 carreras", "Más de 9.5 carreras",
          "Menos de 7.5 carreras", "Menos de 8.5 carreras", "Menos de 9.5 carreras"];
      default:
        return null;
    }
  }

  return null; // "otro" deporte: todo texto libre
}

const EQUIPOS_BASE = [
  "Colo-Colo", "Universidad de Chile", "Universidad Católica", "Everton", "Palestino", "O'Higgins", 
  "Cobresal", "Deportes La Serena", "Unión La Calera", "Audax Italiano", "Coquimbo Unido", 
  "Huachipato", "Ñublense", "Deportes Concepción", "Universidad de Concepción", "Deportes Limache", 
  "Unión Española", "Deportes Iquique", "Deportes Puerto Montt", "Unión San Felipe", 
  "San Luis de Quillota", "Deportes Copiapó", "Deportes Temuco", "Santiago Wanderers", "Rangers", 
  "Cobreloa", "Barnechea", "Curicó Unido", "Deportes Recoleta", "Deportes Magallanes", 
  "San Marcos de Arica", "Deportes Valdivia", "River Plate", "Boca Juniors", "Racing Club", 
  "Independiente", "San Lorenzo", "Vélez Sarsfield", "Newell's Old Boys", "Rosario Central", 
  "Estudiantes de La Plata", "Gimnasia La Plata", "Talleres", "Belgrano", "Instituto", 
  "Argentinos Juniors", "Huracán", "Banfield", "Lanús", "Defensa y Justicia", "Tigre", "Platense", 
  "Central Córdoba", "Barracas Central", "Sarmiento", "Unión de Santa Fe", "Godoy Cruz", 
  "Independiente Rivadavia", "Deportivo Riestra", "Atlético Tucumán", "San Martín de San Juan", 
  "Aldosivi", "Chacarita Juniors", "Almagro", "Nueva Chicago", "San Telmo", "Deportivo Morón", 
  "Almirante Brown", "Ferro Carril Oeste", "All Boys", "Temperley", "Guillermo Brown", 
  "Gimnasia de Mendoza", "Estudiantes de Río Cuarto", "Chaco For Ever", "Quilmes", 
  "Brown de Adrogué", "Peñarol", "Nacional", "Danubio", "Defensor Sporting", 
  "Liverpool FC Uruguay", "Cerro Largo", "Wanderers", "Racing de Montevideo", 
  "River Plate Uruguay", "Progreso", "Universitario", "Alianza Lima", "Sporting Cristal", 
  "Cienciano", "Melgar", "Cusco FC", "Cesar Vallejo", "ADT", "Los Chankas", "Deportivo Garcilaso", 
  "Comerciantes Unidos", "Atlético Grau", "UTC", "Binacional", "Millonarios", "Atlético Nacional", 
  "América de Cali", "Independiente Santa Fe", "Deportivo Cali", "Junior de Barranquilla", 
  "Once Caldas", "Deportes Tolima", "Envigado", "Bucaramanga", "LDU Quito", "Barcelona SC", 
  "Emelec", "Independiente del Valle", "Aucas", "Universidad Católica Ecuador", "El Nacional", 
  "Deportivo Cuenca", "Olimpia", "Cerro Porteño", "Libertad", "Guaraní", "Sportivo Luqueño", 
  "Nacional Paraguay", "Bolívar", "The Strongest", "Always Ready", "Wilstermann", 
  "Nacional Potosí", "Blooming", "Deportivo Táchira", "Caracas FC", "Metropolitanos", "Monagas", 
  "Estudiantes de Mérida", "Flamengo", "Palmeiras", "Corinthians", "São Paulo", "Santos", "Grêmio", 
  "Internacional", "Cruzeiro", "Atlético Mineiro", "Fluminense", "Botafogo", "Vasco da Gama", 
  "Bahia", "Fortaleza EC", "Athletico Paranaense", "Ceará", "Vitória", "Sport Recife", "Coritiba", 
  "Goiás", "Guarani", "Chapecoense", "Náutico", "Avaí", "Cuiabá", "América Mineiro", "Cruz Azul", 
  "América", "Chivas Guadalajara", "Monterrey", "Tigres UANL", "Toluca", "Pumas UNAM", 
  "Santos Laguna", "León", "Pachuca", "Necaxa", "Puebla", "Querétaro", "Atlas", "Juárez", 
  "Mazatlán", "San Luis", "Tijuana", "Inter Miami", "LA Galaxy", "LAFC", "Columbus Crew", 
  "FC Cincinnati", "San Jose Earthquakes", "New York Red Bulls", "Orlando City", 
  "Portland Timbers", "Vancouver Whitecaps", "Austin FC", "Charlotte FC", "Real Salt Lake", 
  "Nashville SC", "FC Dallas", "Seattle Sounders", "Atlanta United", "Toronto FC", 
  "Philadelphia Union", "NYCFC", "Real Madrid", "Barcelona", "Atlético Madrid", "Real Sociedad", 
  "Athletic Bilbao", "Real Betis", "Villarreal", "Sevilla", "Valencia", "Girona", "Celta de Vigo", 
  "Osasuna", "Rayo Vallecano", "Mallorca", "Getafe", "Las Palmas", "Alavés", "Espanyol", "Leganés", 
  "Real Zaragoza", "Sporting Gijón", "Levante", "Racing Santander", "Elche", "Almería", 
  "Manchester City", "Manchester United", "Liverpool", "Chelsea", "Arsenal", "Tottenham", 
  "Newcastle United", "Aston Villa", "West Ham", "Brighton", "Wolves", "Crystal Palace", "Fulham", 
  "Brentford", "Bournemouth", "Nottingham Forest", "Burnley", "Leeds United", "Sunderland", 
  "Leicester City", "Southampton", "Norwich City", "West Bromwich Albion", "Middlesbrough", 
  "Coventry City", "Sheffield United", "Juventus", "Inter de Milán", "AC Milan", "Napoli", 
  "AS Roma", "Lazio", "Atalanta", "Fiorentina", "Bologna", "Torino", "Genoa", "Udinese", 
  "Cagliari", "Empoli", "Sassuolo", "Parma", "Como", "Palermo", "Bari", "Cremonese", "Cesena", 
  "Sampdoria", "Venezia", "Bayern Múnich", "Borussia Dortmund", "RB Leipzig", "Bayer Leverkusen", 
  "Eintracht Frankfurt", "VfB Stuttgart", "Borussia Mönchengladbach", "Wolfsburgo", "Union Berlin", 
  "Werder Bremen", "Hoffenheim", "Freiburgo", "Mainz", "Augsburgo", "Hamburgo SV", "Schalke 04", 
  "Fortuna Düsseldorf", "Hertha Berlín", "Colonia", "Karlsruher SC", "PSG", 
  "Olympique de Marsella", "Olympique de Lyon", "Monaco", "Lille", "Niza", "Rennes", "Lens", 
  "Estrasburgo", "Nantes", "Toulouse", "Brest", "Reims", "Le Havre", "Auxerre", "Angers", 
  "Saint-Étienne", "Ajaccio", "Benfica", "Porto", "Sporting CP", "Braga", "Vitória de Guimarães", 
  "Boavista", "Rio Ave", "Famalicão", "Estoril", "Gil Vicente", "Moreirense", "Casa Pia", "Ajax", 
  "PSV Eindhoven", "Feyenoord", "AZ Alkmaar", "Twente", "Anderlecht", "Club Brugge", "Genk", 
  "Standard Lieja", "Unión Saint-Gilloise", "Galatasaray", "Fenerbahçe", "Besiktas", "Trabzonspor", 
  "Sirius", "Malmö", "Celtic", 
];

const MESES_NOMBRE = {1:"enero",2:"febrero",3:"marzo",4:"abril",5:"mayo",6:"junio",7:"julio",8:"agosto",9:"septiembre",10:"octubre",11:"noviembre",12:"diciembre"};
const MESES_CORTO = {1:"ene",2:"feb",3:"mar",4:"abr",5:"may",6:"jun",7:"jul",8:"ago",9:"sep",10:"oct",11:"nov",12:"dic"};

const MONEDA = "$";

function fmtMoney(n){
  const sign = n < 0 ? "-" : "";
  const v = Math.abs(Math.round(n));
  return sign + MONEDA + v.toLocaleString("es-CL");
}
function fmtPct(n, decimals=1){ return (n*100).toFixed(decimals) + "%"; }
function fmtDate(fecha){
  if(!fecha) return "";
  const [y,m,d] = fecha.split("-").map(s=>parseInt(s,10));
  return d + " " + (MESES_CORTO[m] || "") + " " + y;
}
function todayISO(){
  const d = new Date();
  const y = d.getFullYear();
  const m = String(d.getMonth()+1).padStart(2,"0");
  const day = String(d.getDate()).padStart(2,"0");
  return y + "-" + m + "-" + day;
}
function mesKeyFromFecha(fecha){ return fecha.slice(0,7); }
function mesLabelFromKey(key){
  const [y,m] = key.split("-").map(s=>parseInt(s,10));
  const nombre = MESES_NOMBRE[m] || "";
  return nombre.charAt(0).toUpperCase() + nombre.slice(1) + " " + y;
}

/* ============ MAPEO SUPABASE (snake_case DB <-> camelCase app) ============ */
function dbMonthToClient(m){
  return { id: m.id, key: m.key, label: m.label, capital: Number(m.capital) };
}
function dbBetToClient(b, mesKey){
  return {
    id: b.id, monthId: b.month_id, fecha: b.fecha, mes: mesKey,
    equipoLocal: b.equipo_local, equipoVisitante: b.equipo_visitante,
    tipoApuesta: b.tipo_apuesta, detalle: b.detalle, deporte: b.deporte || "futbol",
    cuota: Number(b.cuota), apostado: Number(b.apostado), resultado: b.resultado,
  };
}
function mensajeErrorDb(error){
  if(!error) return "";
  if(error.code === "23505") return "Ese mes ya existe.";
  return "No pudimos guardar el cambio: " + error.message;
}

/* ============ MODELO: enriquecer apuestas con balance / acumulado ============ */
function buildEnriched(bets, months){
  const monthMap = {};
  months.forEach(m => monthMap[m.key] = m);
  const sorted = [...bets].sort((a,b)=>{
    if(a.fecha !== b.fecha) return a.fecha < b.fecha ? -1 : 1;
    return a.id - b.id;
  });
  const acumMes = {};
  months.forEach(m => acumMes[m.key] = m.capital);
  let acumGeneral = 0;
  return sorted.map(b => {
    const capital = monthMap[b.mes] ? monthMap[b.mes].capital : 0;
    let balance = null;
    if(b.resultado === "GANADA") balance = b.apostado * (b.cuota - 1);
    else if(b.resultado === "PERDIDO") balance = -b.apostado;
    if(balance !== null){
      acumMes[b.mes] = (acumMes[b.mes] || 0) + balance;
      acumGeneral += balance;
    }
    return { ...b, capital, balance, acumulado: acumMes[b.mes], acumuladoGeneral: acumGeneral,
      partido: b.equipoLocal + " vs " + b.equipoVisitante };
  });
}

function computeStats(betsScope, months){
  const settled = betsScope.filter(b => b.resultado !== "PENDIENTE");
  const pendientes = betsScope.length - settled.length;
  const ganadas = settled.filter(b => b.resultado === "GANADA").length;
  const perdidas = settled.length - ganadas;
  const efectividad = settled.length ? ganadas/settled.length : 0;
  const cuotaProm = settled.length ? settled.reduce((s,b)=>s+b.cuota,0)/settled.length : 0;
  const utilidad = settled.reduce((s,b)=>s+b.balance,0);
  const saldoInicialTotal = months.reduce((s,m)=> s + (m.capital||0), 0);
  const saldoActual = saldoInicialTotal + utilidad;
  const utilidadPct = saldoInicialTotal ? utilidad/saldoInicialTotal : 0;

  let racha = 0, rachaTipo = null;
  for(let i = settled.length-1; i>=0; i--){
    const r = settled[i].resultado;
    if(rachaTipo === null){ rachaTipo = r; racha = 1; }
    else if(r === rachaTipo){ racha++; }
    else break;
  }

  return { total: betsScope.length, settledCount: settled.length, pendientes, ganadas, perdidas,
    efectividad, cuotaProm, utilidad, saldoInicialTotal, saldoActual, utilidadPct, racha, rachaTipo };
}

/* ============ APP ============ */
function App({session}){
  const userId = session.user.id;
  const [page, setPage] = useState("dashboard");
  const [months, setMonths] = useState([]);
  const [bets, setBets] = useState([]);
  const [loadingData, setLoadingData] = useState(true);
  const [dataError, setDataError] = useState("");
  const [mesSel, setMesSel] = useState("general");
  const [busqueda, setBusqueda] = useState("");
  const [filtroResultado, setFiltroResultado] = useState("todas");
  const [equiposConocidos, setEquiposConocidos] = useState(EQUIPOS_BASE);

  useEffect(()=>{
    let cancelado = false;
    async function cargar(){
      setLoadingData(true); setDataError("");
      const [{ data: monthsData, error: e1 }, { data: betsData, error: e2 }] = await Promise.all([
        supabaseClient.from("months").select("*").eq("user_id", userId).order("key"),
        supabaseClient.from("bets").select("*").eq("user_id", userId),
      ]);
      if(cancelado) return;
      if(e1 || e2){ setDataError(mensajeErrorDb(e1 || e2)); setLoadingData(false); return; }
      const monthsClient = (monthsData || []).map(dbMonthToClient);
      const monthById = {}; monthsClient.forEach(m => monthById[m.id] = m.key);
      const betsClient = (betsData || []).map(b => dbBetToClient(b, monthById[b.month_id]));
      setMonths(monthsClient);
      setBets(betsClient);
      const equipos = new Set(EQUIPOS_BASE);
      betsClient.forEach(b => { equipos.add(b.equipoLocal); equipos.add(b.equipoVisitante); });
      setEquiposConocidos(Array.from(equipos));
      setLoadingData(false);
    }
    cargar();
    return ()=>{ cancelado = true; };
  }, [userId]);

  const enriched = useMemo(()=> buildEnriched(bets, months), [bets, months]);
  const betsFiltrados = useMemo(()=>{
    return mesSel === "general" ? enriched : enriched.filter(b=>b.mes===mesSel);
  }, [enriched, mesSel]);
  const mesesEnScope = mesSel === "general" ? months : months.filter(m=>m.key===mesSel);
  const stats = useMemo(()=> computeStats(betsFiltrados, mesesEnScope), [betsFiltrados, mesesEnScope]);

  async function crearMesEnDb(key, capital){
    const { data, error } = await supabaseClient.from("months")
      .insert({ user_id: userId, key, label: mesLabelFromKey(key), capital })
      .select().single();
    if(error) return { error };
    const mes = dbMonthToClient(data);
    setMonths(prev => [...prev, mes]);
    return { mes };
  }

  async function addBet({deporte, equipoLocal, equipoVisitante, tipoApuesta, detalle, cuota, apostado}){
    const fecha = todayISO();
    const key = mesKeyFromFecha(fecha);
    let mes = months.find(m=>m.key===key);
    if(!mes){
      const r = await crearMesEnDb(key, 100000);
      if(r.error) return mensajeErrorDb(r.error);
      mes = r.mes;
    }
    const { data, error } = await supabaseClient.from("bets").insert({
      user_id: userId, month_id: mes.id, fecha, deporte,
      equipo_local: equipoLocal, equipo_visitante: equipoVisitante,
      tipo_apuesta: tipoApuesta, detalle, cuota, apostado, resultado: "PENDIENTE",
    }).select().single();
    if(error) return mensajeErrorDb(error);
    setBets(prev => [...prev, dbBetToClient(data, mes.key)]);
    setEquiposConocidos(prev => {
      const lower = prev.map(e=>e.toLowerCase());
      const nuevos = [equipoLocal, equipoVisitante].filter(eq => !lower.includes(eq.toLowerCase()));
      return nuevos.length ? [...prev, ...nuevos] : prev;
    });
    setMesSel(key);
    return null;
  }

  async function deleteBet(id){
    const { error } = await supabaseClient.from("bets").delete().eq("id", id);
    if(error){ setDataError(mensajeErrorDb(error)); return; }
    setBets(prev => prev.filter(b => b.id !== id));
  }

  async function updateResultado(id, resultado){
    const { error } = await supabaseClient.from("bets").update({ resultado }).eq("id", id);
    if(error){ setDataError(mensajeErrorDb(error)); return; }
    setBets(prev => prev.map(b => b.id===id ? {...b, resultado} : b));
  }

  async function setCapital(mesKey, capital){
    const mes = months.find(m=>m.key===mesKey);
    if(!mes) return "No encontramos ese mes.";
    const { error } = await supabaseClient.from("months").update({ capital }).eq("id", mes.id);
    if(error) return mensajeErrorDb(error);
    setMonths(prev => prev.map(m => m.key===mesKey ? {...m, capital} : m));
    return null;
  }

  async function addMonth({key, capital}){
    if(months.some(m=>m.key===key)) return "Ese mes ya existe en tus pestañas.";
    const r = await crearMesEnDb(key, capital);
    if(r.error) return mensajeErrorDb(r.error);
    setMesSel(key);
    return null;
  }

  function limpiarDatosLocales(){
    setBets([]); setMonths([]); setMesSel("general");
  }

  async function cerrarSesion(){
    await supabaseClient.auth.signOut();
  }

  if(loadingData){
    return (
      <div className="app">
        <Sidebar page={page} setPage={setPage} onLogout={cerrarSesion} />
        <div className="main"><div className="auth-loading">Cargando tus datos…</div></div>
      </div>
    );
  }

  return (
    <div className="app">
      <Sidebar page={page} setPage={setPage} onLogout={cerrarSesion} />
      <div className="main">
        {dataError && <div className="field-error" style={{marginBottom:14}}>{dataError}</div>}
        <TopBar
          page={page} mesSel={mesSel} setMesSel={setMesSel} months={months}
          onAddMonth={addMonth}
          onAddBet={addBet}
          onSetCapital={setCapital}
          equiposConocidos={equiposConocidos}
        />
        {page === "dashboard" && <Dashboard stats={stats} mesSel={mesSel} betsFiltrados={betsFiltrados} />}
        {page === "historial" && (
          <Historial
            betsFiltrados={betsFiltrados}
            busqueda={busqueda} setBusqueda={setBusqueda}
            filtroResultado={filtroResultado} setFiltroResultado={setFiltroResultado}
            onUpdateResultado={updateResultado}
            onDeleteBet={deleteBet}
            months={months} mesSel={mesSel} onSetCapital={setCapital}
          />
        )}
        {page === "ingreso" && <Placeholder
          title="Ingreso de apuestas"
          text="Ya puedes cargar apuestas nuevas desde el botón 'Nueva apuesta' en Historial. Esta sección quedará para opciones de carga masiva más adelante."
          icon={<IconPlusCircle color="#8489B8" />}
        />}
        {page === "config" && <Configuracion session={session} onDataWiped={limpiarDatosLocales} />}
      </div>
    </div>
  );
}

/* ============ SIDEBAR ============ */
function Sidebar({page, setPage, onLogout}){
  const NavItem = ({id, icon, label, disabled}) => (
    <div className={"nav-item" + (page===id ? " active" : "") + (disabled ? " disabled" : "")}
      onClick={()=> !disabled && setPage(id)}>
      {icon}
      <span>{label}</span>
      {disabled && <span className="soon-badge">PRONTO</span>}
    </div>
  );
  return (
    <div className="sidebar">
      <div className="brand">
        <div className="brand-mark"><IconTicket color="#fff" /></div>
        <div>
          <div className="brand-name">Bankroll</div>
          <div className="brand-sub">Registro NEO VIP</div>
        </div>
      </div>
      <div className="nav-section-label">Análisis</div>
      <NavItem id="dashboard" icon={<IconGrid color={page==="dashboard"?"#fff":"#8489B8"}/>} label="Dashboard" />
      <NavItem id="historial" icon={<IconList color={page==="historial"?"#fff":"#8489B8"}/>} label="Historial de apuestas" />
      <div className="nav-section-label">Gestión</div>
      <NavItem id="ingreso" icon={<IconPlusCircle color="#8489B8"/>} label="Ingresar apuesta" />
      <NavItem id="config" icon={<IconGear color="#8489B8"/>} label="Configuración" />
      <div className="sidebar-foot">
        <div className="logout-btn" onClick={onLogout}>
          <IconLogout color="#B7BBE4" /> Cerrar sesión
        </div>
      </div>
    </div>
  );
}

/* ============ TOPBAR ============ */
function TopBar({page, mesSel, setMesSel, months, onAddMonth, onAddBet, onSetCapital, equiposConocidos}){
  const titles = {
    dashboard: ["Dashboard", "Resumen de rendimiento del grupo de apuestas"],
    historial: ["Historial de apuestas", "Todas las apuestas registradas, en orden cronológico"],
    ingreso: ["Ingresar apuesta", "Carga manual de nuevas apuestas"],
    config: ["Configuración", "Preferencias de la cuenta"],
  };
  const [t, sub] = titles[page];
  const showFilter = page === "dashboard" || page === "historial";

  return (
    <div className="topbar">
      <div>
        <h1 className="page-title">{t}</h1>
        <p className="page-subtitle">{sub}</p>
      </div>
      {showFilter && (
        <div className="topbar-right">
          <div style={{display:"flex", gap:10, alignItems:"center"}}>
            {page === "historial" && <NuevaApuestaButton onAddBet={onAddBet} equiposConocidos={equiposConocidos} />}
            <MonthFilter mesSel={mesSel} setMesSel={setMesSel} months={months} onAddMonth={onAddMonth} />
          </div>
        </div>
      )}
    </div>
  );
}

function MonthFilter({mesSel, setMesSel, months, onAddMonth}){
  const [dropOpen, setDropOpen] = useState(false);
  const [addOpen, setAddOpen] = useState(false);
  const ordered = useMemo(()=> [...months].sort((a,b)=> b.key.localeCompare(a.key)), [months]);
  const mesActual = months.find(m=>m.key===mesSel);

  return (
    <div className="popover-anchor">
      <div className="month-filter">
        <div className={"month-pill" + (mesSel==="general" ? " active" : "")}
          onClick={()=>{ setMesSel("general"); setDropOpen(false); }}>General</div>
        <div className={"month-dropdown-btn" + (mesSel!=="general" ? " active" : "")}
          onClick={()=>setDropOpen(o=>!o)}>
          <span>{mesActual ? mesActual.label : "Elige un mes"}</span>
          <IconChevronDown color={mesSel!=="general" ? "#fff" : "currentColor"} />
        </div>
      </div>
      {dropOpen && (
        <React.Fragment>
          <div className="overlay-catch" onClick={()=>setDropOpen(false)}></div>
          <div className="popover month-dropdown-popover" style={{width:220}}>
            {ordered.length === 0 && <div className="month-option" style={{color:"var(--ink-faint)", cursor:"default"}}>Aún no hay meses</div>}
            {ordered.map(m => (
              <div key={m.key} className={"month-option" + (mesSel===m.key ? " selected" : "")}
                onClick={()=>{ setMesSel(m.key); setDropOpen(false); }}>{m.label}</div>
            ))}
            <div className="month-option add" onClick={()=>{ setDropOpen(false); setAddOpen(true); }}>
              <IconPlus color="currentColor" /> Agregar mes
            </div>
          </div>
        </React.Fragment>
      )}
      {addOpen && (
        <React.Fragment>
          <div className="overlay-catch" onClick={()=>setAddOpen(false)}></div>
          <AddMonthPopover onClose={()=>setAddOpen(false)} onAddMonth={onAddMonth} existingKeys={months.map(m=>m.key)} />
        </React.Fragment>
      )}
    </div>
  );
}

function AddMonthPopover({onClose, onAddMonth, existingKeys}){
  const [mesInput, setMesInput] = useState(todayISO().slice(0,7));
  const [capital, setCapitalVal] = useState(100000);
  const [error, setError] = useState("");
  const [loading, setLoading] = useState(false);

  async function submit(){
    if(!mesInput){ setError("Elige un mes"); return; }
    if(existingKeys.includes(mesInput)){ setError("Ese mes ya existe en tus pestañas"); return; }
    if(!capital || capital <= 0){ setError("El capital inicial debe ser mayor a 0"); return; }
    setLoading(true);
    const err = await onAddMonth({key: mesInput, capital: Number(capital)});
    setLoading(false);
    if(err){ setError(err); return; }
    onClose();
  }

  return (
    <div className="popover" style={{width:280}}>
      <p className="popover-title">Nuevo mes</p>
      <p className="popover-sub">Crea una nueva pestaña de seguimiento mensual.</p>
      <div className="field-row">
        <label className="field-label">Mes</label>
        <input className="field-input" type="month" value={mesInput} onChange={e=>setMesInput(e.target.value)} />
      </div>
      <div className="field-row">
        <label className="field-label">Capital inicial</label>
        <CurrencyInput value={capital} onChange={setCapitalVal} placeholder="100.000" />
      </div>
      {error && <div className="field-error">{error}</div>}
      <div className="popover-actions">
        <button className="btn-ghost" onClick={onClose}>Cancelar</button>
        <button className="btn-primary" style={{justifyContent:"center"}} disabled={loading} onClick={submit}>{loading ? "Creando…" : "Crear mes"}</button>
      </div>
    </div>
  );
}

function NuevaApuestaButton({onAddBet, equiposConocidos}){
  const [open, setOpen] = useState(false);
  return (
    <div className="popover-anchor">
      <button className="btn-primary" onClick={()=>setOpen(o=>!o)}>
        <IconPlus color="#fff" /> Nueva apuesta
      </button>
      {open && (
        <React.Fragment>
          <div className="overlay-catch" onClick={()=>setOpen(false)}></div>
          <NuevaApuestaPopover onClose={()=>setOpen(false)} onAddBet={onAddBet} equiposConocidos={equiposConocidos} />
        </React.Fragment>
      )}
    </div>
  );
}

function NuevaApuestaPopover({onClose, onAddBet, equiposConocidos}){
  const [deporte, setDeporte] = useState("futbol");
  const [equipoLocal, setEquipoLocal] = useState("");
  const [equipoVisitante, setEquipoVisitante] = useState("");
  const deporteObj = DEPORTES.find(d=>d.key===deporte);
  const tiposDisponibles = deporteObj ? deporteObj.tipos : null;
  const [tipoApuesta, setTipoApuesta] = useState(tiposDisponibles ? tiposDisponibles[0] : "");
  const [tipoLibre, setTipoLibre] = useState("");
  const [detalleIndex, setDetalleIndex] = useState(0);
  const [detalleLibre, setDetalleLibre] = useState("");
  const [cuota, setCuota] = useState("");
  const [apostado, setApostado] = useState("");
  const [error, setError] = useState("");
  const [loading, setLoading] = useState(false);

  const opcionesDetalle = tiposDisponibles ? getOpcionesDetalle(deporte, tipoApuesta, equipoLocal, equipoVisitante) : null;

  function cambiarDeporte(nuevoDeporte){
    setDeporte(nuevoDeporte);
    const d = DEPORTES.find(x=>x.key===nuevoDeporte);
    setTipoApuesta(d && d.tipos ? d.tipos[0] : "");
    setTipoLibre("");
    setDetalleIndex(0);
    setDetalleLibre("");
  }
  function cambiarTipo(nuevoTipo){
    setTipoApuesta(nuevoTipo);
    setDetalleIndex(0);
    setDetalleLibre("");
  }

  const esOtroDetalle = !opcionesDetalle || detalleIndex >= opcionesDetalle.length;
  const detalleFinal = (opcionesDetalle && !esOtroDetalle) ? opcionesDetalle[detalleIndex] : detalleLibre.trim();
  const tipoFinal = tiposDisponibles ? tipoApuesta : tipoLibre.trim();

  async function submit(){
    if(!equipoLocal.trim() || !equipoVisitante.trim()){ setError("Completa los dos equipos / jugadores del evento"); return; }
    if(!tipoFinal){ setError("Escribe el tipo de apuesta"); return; }
    if(!detalleFinal){ setError("Escribe o elige el detalle del pronóstico"); return; }
    const cuotaNum = parseFloat(cuota);
    const apostadoNum = Number(apostado);
    if(!cuotaNum || cuotaNum <= 1){ setError("La cuota debe ser mayor a 1"); return; }
    if(!apostadoNum || apostadoNum <= 0){ setError("El monto apostado debe ser mayor a 0"); return; }
    setLoading(true);
    const err = await onAddBet({deporte, equipoLocal: equipoLocal.trim(), equipoVisitante: equipoVisitante.trim(),
      tipoApuesta: tipoFinal, detalle: detalleFinal, cuota: cuotaNum, apostado: apostadoNum});
    setLoading(false);
    if(err){ setError(err); return; }
    onClose();
  }

  return (
    <div className="popover">
      <p className="popover-title">Nueva apuesta</p>
      <p className="popover-sub">Se registra con la fecha de hoy y se agrega como <b>pendiente</b>; edita el resultado cuando termine el evento.</p>
      <div className="field-row">
        <label className="field-label">Deporte</label>
        <select className="field-select" value={deporte} onChange={e=>cambiarDeporte(e.target.value)}>
          {DEPORTES.map(d => <option key={d.key} value={d.key}>{d.label}</option>)}
        </select>
      </div>
      <div className="field-row">
        <label className="field-label">Partido / Enfrentamiento</label>
        <div className="vs-row">
          <input className="field-input" list="lista-equipos" placeholder="Local / Jugador 1"
            value={equipoLocal} onChange={e=>setEquipoLocal(e.target.value)} />
          <span className="vs-tag">vs</span>
          <input className="field-input" list="lista-equipos" placeholder="Visitante / Jugador 2"
            value={equipoVisitante} onChange={e=>setEquipoVisitante(e.target.value)} />
        </div>
        <datalist id="lista-equipos">
          {equiposConocidos.map(eq => <option key={eq} value={eq} />)}
        </datalist>
      </div>
      <div className="field-row">
        <label className="field-label">Tipo de apuesta</label>
        {tiposDisponibles ? (
          <React.Fragment>
            <select className="field-select" value={tipoApuesta} onChange={e=>cambiarTipo(e.target.value)}>
              {tiposDisponibles.map(t => <option key={t} value={t}>{t}</option>)}
            </select>
            <div className="field-hint">Categorías basadas en los mercados típicos de casas como Betano.</div>
          </React.Fragment>
        ) : (
          <input className="field-input" placeholder="Ej: Ganador de la carrera" value={tipoLibre} onChange={e=>setTipoLibre(e.target.value)} />
        )}
      </div>
      <div className="field-row">
        <label className="field-label">Detalle del pronóstico</label>
        {opcionesDetalle && (
          <select className="field-select" value={detalleIndex} onChange={e=>setDetalleIndex(Number(e.target.value))}>
            {opcionesDetalle.map((op, i) => <option key={i} value={i}>{op}</option>)}
            <option value={opcionesDetalle.length}>{OTRO_DETALLE}</option>
          </select>
        )}
        {esOtroDetalle && (
          <textarea className="field-textarea" rows="2" style={opcionesDetalle ? {marginTop:8} : {}}
            placeholder={opcionesDetalle ? "Especifica el detalle exacto" : "Ej: Gana River y +2.5 goles"}
            value={detalleLibre} onChange={e=>setDetalleLibre(e.target.value)} />
        )}
      </div>
      <div style={{display:"flex", gap:10}}>
        <div className="field-row" style={{flex:1}}>
          <label className="field-label">Cuota</label>
          <input className="field-input" type="number" step="0.01" min="1.01" placeholder="1.75" value={cuota} onChange={e=>setCuota(e.target.value)} />
        </div>
        <div className="field-row" style={{flex:1}}>
          <label className="field-label">Monto apostado</label>
          <CurrencyInput value={apostado} onChange={setApostado} placeholder="8.000" />
        </div>
      </div>
      {error && <div className="field-error">{error}</div>}
      <div className="popover-actions">
        <button className="btn-ghost" onClick={onClose}>Cancelar</button>
        <button className="btn-primary" style={{justifyContent:"center"}} disabled={loading} onClick={submit}>{loading ? "Agregando…" : "Agregar"}</button>
      </div>
    </div>
  );
}

function CapitalButton({months, mesSel, onSetCapital}){
  const [open, setOpen] = useState(false);
  const disabled = mesSel === "general";
  return (
    <div className="popover-anchor">
      <button className="btn-secondary" disabled={disabled}
        title={disabled ? "Selecciona un mes específico para definir su capital" : "Definir capital de este mes"}
        onClick={()=> !disabled && setOpen(o=>!o)}>
        <IconCoin color={disabled ? "#B7BBD8" : "var(--ink-soft)"} /> Capital
      </button>
      {open && !disabled && (
        <React.Fragment>
          <div className="overlay-catch" onClick={()=>setOpen(false)}></div>
          <CapitalPopover months={months} mesSel={mesSel} onClose={()=>setOpen(false)} onSetCapital={onSetCapital} />
        </React.Fragment>
      )}
    </div>
  );
}

function CapitalPopover({months, mesSel, onClose, onSetCapital}){
  const mesActual = months.find(m=>m.key===mesSel);
  const [capital, setCapitalVal] = useState(mesActual ? mesActual.capital : 0);
  const [error, setError] = useState("");
  const [loading, setLoading] = useState(false);

  async function submit(){
    const num = Number(capital);
    if(!num || num <= 0){ setError("El monto debe ser mayor a 0"); return; }
    setLoading(true);
    const err = await onSetCapital(mesSel, num);
    setLoading(false);
    if(err){ setError(err); return; }
    onClose();
  }

  return (
    <div className="popover" style={{left:0, right:"auto"}}>
      <p className="popover-title">Capital de {mesActual ? mesActual.label : ""}</p>
      <p className="popover-sub">Es el capital con el que partes este mes: el saldo acumulado se calcula a partir de este monto.</p>
      <div className="field-row">
        <label className="field-label">Capital inicial</label>
        <CurrencyInput value={capital} onChange={setCapitalVal} placeholder="100.000" />
      </div>
      {error && <div className="field-error">{error}</div>}
      <div className="popover-actions">
        <button className="btn-ghost" onClick={onClose}>Cancelar</button>
        <button className="btn-primary" style={{justifyContent:"center"}} disabled={loading} onClick={submit}>{loading ? "Guardando…" : "Guardar"}</button>
      </div>
    </div>
  );
}

function Placeholder({title, text, icon}){
  return (
    <div className="placeholder">
      <div style={{width:52,height:52,borderRadius:14,background:"#F1F2FA",display:"flex",alignItems:"center",justifyContent:"center",margin:"0 auto"}}>
        {icon}
      </div>
      <h3>{title}</h3>
      <p>{text}</p>
    </div>
  );
}

/* ============ CONFIGURACIÓN (cuenta) ============ */
function Configuracion({session, onDataWiped}){
  const [pass1, setPass1] = useState("");
  const [pass2, setPass2] = useState("");
  const [passMsg, setPassMsg] = useState("");
  const [passErr, setPassErr] = useState("");
  const [passLoading, setPassLoading] = useState(false);
  const [confirmDelete, setConfirmDelete] = useState(false);
  const [deleteLoading, setDeleteLoading] = useState(false);
  const [deleteMsg, setDeleteMsg] = useState("");

  async function cambiarPassword(e){
    e.preventDefault();
    setPassErr(""); setPassMsg("");
    if(pass1.length < 6){ setPassErr("La contraseña debe tener al menos 6 caracteres"); return; }
    if(pass1 !== pass2){ setPassErr("Las contraseñas no coinciden"); return; }
    setPassLoading(true);
    const { error } = await supabaseClient.auth.updateUser({ password: pass1 });
    setPassLoading(false);
    if(error){ setPassErr(traducirErrorAuth(error.message)); return; }
    setPass1(""); setPass2("");
    setPassMsg("Contraseña actualizada.");
  }

  async function cerrarSesion(){
    await supabaseClient.auth.signOut();
  }

  async function borrarMisDatos(){
    setDeleteLoading(true); setDeleteMsg("");
    const userId = session.user.id;
    const { error: e1 } = await supabaseClient.from("bets").delete().eq("user_id", userId);
    const { error: e2 } = await supabaseClient.from("months").delete().eq("user_id", userId);
    setDeleteLoading(false);
    if(e1 || e2){ setDeleteMsg("Hubo un error borrando los datos: " + ((e1||e2).message)); return; }
    setDeleteMsg("Tus meses y apuestas fueron eliminados.");
    setConfirmDelete(false);
    onDataWiped();
  }

  return (
    <div style={{maxWidth:460}}>
      <div className="panel" style={{marginBottom:16}}>
        <p className="panel-title">Cuenta</p>
        <p className="panel-sub" style={{marginBottom:16}}>Sesión iniciada como <b>{session.user.email}</b></p>
        <button className="btn-secondary" onClick={cerrarSesion}>Cerrar sesión</button>
      </div>

      <div className="panel" style={{marginBottom:16}}>
        <p className="panel-title">Cambiar contraseña</p>
        <form onSubmit={cambiarPassword} style={{marginTop:14}}>
          <div className="field-row">
            <label className="field-label">Contraseña nueva</label>
            <input className="field-input" type="password" value={pass1} onChange={e=>setPass1(e.target.value)} placeholder="Mínimo 6 caracteres" />
          </div>
          <div className="field-row">
            <label className="field-label">Repite la contraseña</label>
            <input className="field-input" type="password" value={pass2} onChange={e=>setPass2(e.target.value)} placeholder="••••••••" />
          </div>
          {passErr && <div className="field-error">{passErr}</div>}
          {passMsg && <div className="field-hint" style={{color:"var(--win)"}}>{passMsg}</div>}
          <button className="btn-primary" style={{marginTop:8}} disabled={passLoading}>{passLoading ? "Guardando…" : "Actualizar contraseña"}</button>
        </form>
      </div>

      <div className="panel" style={{borderColor:"#F7CEC9"}}>
        <p className="panel-title" style={{color:"var(--lose)"}}>Eliminar mis datos</p>
        <p className="panel-sub" style={{marginBottom:14}}>
          Esto borra todos tus meses y apuestas guardados de forma permanente. Tu cuenta de acceso (correo/contraseña) queda activa —
          borrarla por completo todavía requiere un paso adicional en el servidor que no hemos construido; te aviso apenas lo tengamos listo.
        </p>
        {!confirmDelete ? (
          <button className="btn-secondary" style={{color:"var(--lose)"}} onClick={()=>setConfirmDelete(true)}>Eliminar mis datos</button>
        ) : (
          <div>
            <div className="field-error" style={{marginBottom:10}}>¿Seguro? Esta acción no se puede deshacer.</div>
            <div style={{display:"flex", gap:8}}>
              <button className="btn-ghost" onClick={()=>setConfirmDelete(false)}>Cancelar</button>
              <button className="btn-primary" style={{background:"var(--lose)"}} disabled={deleteLoading} onClick={borrarMisDatos}>
                {deleteLoading ? "Borrando…" : "Sí, eliminar todo"}
              </button>
            </div>
          </div>
        )}
        {deleteMsg && <div className="field-hint" style={{marginTop:10}}>{deleteMsg}</div>}
      </div>
    </div>
  );
}
</script>
<script type="text/babel" data-presets="react">
/* ============ DASHBOARD ============ */
function Dashboard({stats, mesSel, betsFiltrados}){
  const utilPositiva = stats.utilidad >= 0;
  const cuotaNota = stats.pendientes > 0
    ? stats.settledCount + " resueltas · " + stats.pendientes + " pendientes"
    : stats.total + " apuestas registradas";
  return (
    <React.Fragment>
      <div className="kpi-grid">
        <KpiCard icon={<IconWallet color="#4C5FD9"/>} iconBg="#EDEFFC"
          label="Saldo actual" value={fmtMoney(stats.saldoActual)}
          deltaLabel={(utilPositiva?"+":"") + fmtMoney(stats.utilidad) + " vs. saldo inicial"}
          deltaColor={utilPositiva ? "var(--win)" : "var(--lose)"} />
        <KpiCard icon={<IconTrend color={utilPositiva?"#189B57":"#DD4B3E"}/>} iconBg={utilPositiva?"#E4F7EC":"#FCEAE8"}
          label="Utilidad" value={(utilPositiva?"+":"") + fmtMoney(stats.utilidad)}
          deltaLabel={(utilPositiva?"+":"") + fmtPct(stats.utilidadPct)}
          deltaColor={utilPositiva ? "var(--win)" : "var(--lose)"} />
        <KpiCard icon={<IconTarget color="#0C8E88"/>} iconBg="#E1F5F3"
          label="Efectividad" value={fmtPct(stats.efectividad)}
          deltaLabel={stats.ganadas + " ganadas / " + stats.perdidas + " perdidas"}
          deltaColor="var(--ink-soft)" deltaWeight={500} />
        <KpiCard icon={<IconTicket color="#7C4FD9"/>} iconBg="#F0EAFB"
          label="Cuota promedio" value={stats.cuotaProm ? stats.cuotaProm.toFixed(2) : "—"}
          deltaLabel={cuotaNota} deltaColor="var(--ink-soft)" deltaWeight={500} />
        <KpiCard icon={<IconFlame color="#B4650F"/>} iconBg="#FBEEDD"
          label="Racha actual" value={stats.rachaTipo ? (stats.racha + (stats.rachaTipo==="GANADA" ? " ganadas" : " perdidas")) : "—"}
          deltaLabel={stats.rachaTipo==="GANADA" ? "En racha positiva" : (stats.rachaTipo ? "Buscando cortar la racha" : "Sin apuestas resueltas")}
          deltaColor={stats.rachaTipo==="GANADA" ? "var(--win)" : "var(--lose)"} />
      </div>
      <div className="panel-row">
        <EvolucionChart betsFiltrados={betsFiltrados} mesSel={mesSel} />
        <EfectividadDonut stats={stats} />
      </div>
      <div className="panel-row thirds">
        <RendimientoDiarioChart betsFiltrados={betsFiltrados} />
        <TopApuestas betsFiltrados={betsFiltrados} />
      </div>
      <div className="panel-row" style={{gridTemplateColumns:"1fr"}}>
        <RendimientoPorTipoChart betsFiltrados={betsFiltrados} />
      </div>
    </React.Fragment>
  );
}

function KpiCard({icon, iconBg, label, value, deltaLabel, deltaColor, deltaWeight}){
  return (
    <div className="kpi-card">
      <div className="kpi-top"><div className="kpi-icon" style={{background:iconBg}}>{icon}</div></div>
      <div className="kpi-label">{label}</div>
      <div className="kpi-value">{value}</div>
      <div className="kpi-delta" style={{color:deltaColor, fontWeight:deltaWeight||600}}>{deltaLabel}</div>
    </div>
  );
}

function EvolucionChart({betsFiltrados, mesSel}){
  const canvasRef = useRef(null);
  const chartRef = useRef(null);
  const esGeneral = mesSel === "general";

  const {labels, data} = useMemo(()=>{
    const settled = betsFiltrados.filter(b=>b.resultado!=="PENDIENTE");
    const labels = ["Inicio", ...settled.map(b=>fmtDate(b.fecha))];
    const data = [settled.length ? (esGeneral ? 0 : (settled[0].acumulado - settled[0].balance)) : 0,
      ...settled.map(b => esGeneral ? b.acumuladoGeneral : b.acumulado)];
    return {labels, data};
  }, [betsFiltrados, esGeneral]);

  useEffect(()=>{
    if(!canvasRef.current) return;
    if(chartRef.current) chartRef.current.destroy();
    const ctx = canvasRef.current.getContext("2d");
    const gradient = ctx.createLinearGradient(0,0,0,240);
    gradient.addColorStop(0, "rgba(76,95,217,0.22)");
    gradient.addColorStop(1, "rgba(76,95,217,0.0)");
    chartRef.current = new Chart(ctx, {
      type: "line",
      data: { labels, datasets: [{ data, borderColor: "#4C5FD9", backgroundColor: gradient, borderWidth: 2.4,
        pointRadius: 0, pointHoverRadius: 4, pointHoverBackgroundColor: "#4C5FD9", tension: 0.32, fill: true }] },
      options: {
        responsive:true, maintainAspectRatio:false,
        plugins:{ legend:{display:false},
          tooltip:{ backgroundColor:"#1C2140", padding:10, cornerRadius:8, titleFont:{family:"Poppins", size:11},
            bodyFont:{family:"Poppins", size:12, weight:"600"},
            callbacks:{ label: (ctx)=> (esGeneral ? "Utilidad acum.: " : "Saldo: ") + fmtMoney(ctx.parsed.y) } } },
        scales:{
          x:{ grid:{display:false}, ticks:{ font:{family:"Poppins", size:10.5}, color:"#9DA2C3", maxTicksLimit:8 } },
          y:{ grid:{color:"#EEF0FA"}, ticks:{ font:{family:"Poppins", size:10.5}, color:"#9DA2C3",
              callback:(v)=> MONEDA+(v/1000).toFixed(0)+"k" } } },
        interaction:{ mode:"index", intersect:false }
      }
    });
    return ()=>{ if(chartRef.current) chartRef.current.destroy(); };
  }, [labels, data, esGeneral]);

  return (
    <div className="panel">
      <div className="panel-head">
        <div>
          <p className="panel-title">{esGeneral ? "Utilidad acumulada" : "Evolución del saldo"}</p>
          <p className="panel-sub">{esGeneral ? "Ganancia neta a lo largo de todos los meses registrados" : "Saldo del bankroll apuesta a apuesta durante el mes"}</p>
        </div>
      </div>
      <div className="chart-wrap"><canvas ref={canvasRef}></canvas></div>
    </div>
  );
}

function EfectividadDonut({stats}){
  const canvasRef = useRef(null);
  const chartRef = useRef(null);
  useEffect(()=>{
    if(!canvasRef.current) return;
    if(chartRef.current) chartRef.current.destroy();
    const ctx = canvasRef.current.getContext("2d");
    chartRef.current = new Chart(ctx, {
      type: "doughnut",
      data: { labels: ["Ganadas", "Perdidas"], datasets: [{ data: [stats.ganadas, stats.perdidas],
        backgroundColor: ["#189B57", "#DD4B3E"], borderWidth: 0, hoverOffset: 4 }] },
      options: { responsive:true, maintainAspectRatio:false, cutout: "72%",
        plugins:{ legend:{ display:false },
          tooltip:{ backgroundColor:"#1C2140", padding:10, cornerRadius:8, bodyFont:{family:"Poppins", size:12, weight:"600"},
            callbacks:{ label:(ctx)=> ctx.label + ": " + ctx.parsed } } } }
    });
    return ()=>{ if(chartRef.current) chartRef.current.destroy(); };
  }, [stats.ganadas, stats.perdidas]);

  return (
    <div className="panel">
      <div className="panel-head">
        <div><p className="panel-title">Efectividad</p><p className="panel-sub">Ganadas vs. perdidas</p></div>
      </div>
      <div className="chart-wrap small">
        <canvas ref={canvasRef}></canvas>
        <div className="donut-center">
          <div className="num">{fmtPct(stats.efectividad, 0)}</div>
          <div className="lbl">acierto</div>
        </div>
      </div>
      <div className="mini-legend">
        <span><span className="legend-dot" style={{background:"#189B57"}}></span>Ganadas ({stats.ganadas})</span>
        <span><span className="legend-dot" style={{background:"#DD4B3E"}}></span>Perdidas ({stats.perdidas})</span>
      </div>
    </div>
  );
}

function RendimientoDiarioChart({betsFiltrados}){
  const canvasRef = useRef(null);
  const chartRef = useRef(null);
  const {labels, data} = useMemo(()=>{
    const porDia = {};
    betsFiltrados.forEach(b=>{
      if(b.balance===null) return;
      porDia[b.fecha] = (porDia[b.fecha]||0) + b.balance;
    });
    const keys = Object.keys(porDia).sort();
    return { labels: keys.map(fmtDate), data: keys.map(k=>porDia[k]) };
  }, [betsFiltrados]);

  useEffect(()=>{
    if(!canvasRef.current) return;
    if(chartRef.current) chartRef.current.destroy();
    const ctx = canvasRef.current.getContext("2d");
    chartRef.current = new Chart(ctx, {
      type:"bar",
      data:{ labels, datasets:[{ data, backgroundColor: data.map(v=> v>=0 ? "#189B57" : "#DD4B3E"), borderRadius: 4, maxBarThickness: 16 }] },
      options:{ responsive:true, maintainAspectRatio:false,
        plugins:{ legend:{display:false},
          tooltip:{ backgroundColor:"#1C2140", padding:10, cornerRadius:8, bodyFont:{family:"Poppins", size:12, weight:"600"},
            callbacks:{ label:(ctx)=> fmtMoney(ctx.parsed.y) } } },
        scales:{ x:{ grid:{display:false}, ticks:{ font:{family:"Poppins", size:10}, color:"#9DA2C3", maxTicksLimit:10 } },
          y:{ grid:{color:"#EEF0FA"}, ticks:{ font:{family:"Poppins", size:10}, color:"#9DA2C3",
              callback:(v)=> MONEDA+(v/1000).toFixed(0)+"k" } } } }
    });
    return ()=>{ if(chartRef.current) chartRef.current.destroy(); };
  }, [labels, data]);

  return (
    <div className="panel">
      <div className="panel-head">
        <div><p className="panel-title">Rendimiento por día</p><p className="panel-sub">Balance neto de cada jornada de apuestas</p></div>
      </div>
      <div className="chart-wrap small"><canvas ref={canvasRef}></canvas></div>
    </div>
  );
}

function RendimientoPorTipoChart({betsFiltrados}){
  const canvasRef = useRef(null);
  const chartRef = useRef(null);
  const {labels, data, counts} = useMemo(()=>{
    const porTipo = {};
    const conteo = {};
    betsFiltrados.forEach(b=>{
      if(b.balance===null) return;
      porTipo[b.tipoApuesta] = (porTipo[b.tipoApuesta]||0) + b.balance;
      conteo[b.tipoApuesta] = (conteo[b.tipoApuesta]||0) + 1;
    });
    const keys = Object.keys(porTipo).sort((a,b)=> porTipo[b]-porTipo[a]);
    return { labels: keys, data: keys.map(k=>porTipo[k]), counts: keys.map(k=>conteo[k]) };
  }, [betsFiltrados]);

  useEffect(()=>{
    if(!canvasRef.current) return;
    if(chartRef.current) chartRef.current.destroy();
    const ctx = canvasRef.current.getContext("2d");
    chartRef.current = new Chart(ctx, {
      type:"bar",
      data:{ labels, datasets:[{ data, backgroundColor: data.map(v=> v>=0 ? "#189B57" : "#DD4B3E"), borderRadius: 6, maxBarThickness: 34 }] },
      options:{ responsive:true, maintainAspectRatio:false, indexAxis:"y",
        plugins:{ legend:{display:false},
          tooltip:{ backgroundColor:"#1C2140", padding:10, cornerRadius:8, bodyFont:{family:"Poppins", size:12, weight:"600"},
            callbacks:{ label:(ctx)=> fmtMoney(ctx.parsed.x) + " · " + counts[ctx.dataIndex] + " apuestas" } } },
        scales:{ y:{ grid:{display:false}, ticks:{ font:{family:"Poppins", size:11.5}, color:"#4A4F73" } },
          x:{ grid:{color:"#EEF0FA"}, ticks:{ font:{family:"Poppins", size:10}, color:"#9DA2C3",
              callback:(v)=> MONEDA+(v/1000).toFixed(0)+"k" } } } }
    });
    return ()=>{ if(chartRef.current) chartRef.current.destroy(); };
  }, [labels, data, counts]);

  if(labels.length===0){
    return (
      <div className="panel">
        <div className="panel-head"><div><p className="panel-title">Rendimiento por tipo de apuesta</p><p className="panel-sub">Utilidad neta agrupada por mercado (categorías estilo Betano)</p></div></div>
        <div className="empty-state">Aún no hay apuestas resueltas en este período.</div>
      </div>
    );
  }

  return (
    <div className="panel">
      <div className="panel-head">
        <div><p className="panel-title">Rendimiento por tipo de apuesta</p><p className="panel-sub">Utilidad neta agrupada por mercado — para ver qué tipo te genera más ganancias</p></div>
      </div>
      <div className="chart-wrap" style={{minHeight: Math.max(200, labels.length*42)}}><canvas ref={canvasRef}></canvas></div>
    </div>
  );
}

function TopApuestas({betsFiltrados}){
  const {mejores, peores} = useMemo(()=>{
    const settled = betsFiltrados.filter(b=>b.balance!==null);
    const ordenado = [...settled].sort((a,b)=>b.balance-a.balance);
    return { mejores: ordenado.slice(0,3), peores: ordenado.slice(-3).reverse() };
  }, [betsFiltrados]);

  const Row = ({b}) => (
    <div style={{display:"flex", alignItems:"center", justifyContent:"space-between", padding:"8px 0", borderBottom:"1px solid #F2F3FA"}}>
      <div style={{minWidth:0, marginRight:10}}>
        <div style={{fontSize:12.5, fontWeight:500, color:"var(--ink)", whiteSpace:"nowrap", overflow:"hidden", textOverflow:"ellipsis", maxWidth:230}}>{b.partido}</div>
        <div style={{fontSize:11, color:"var(--ink-soft)", marginTop:1, whiteSpace:"nowrap", overflow:"hidden", textOverflow:"ellipsis", maxWidth:230}}>{b.tipoApuesta} · {b.detalle}</div>
        <div style={{fontSize:11, color:"var(--ink-soft)", marginTop:2}}>{fmtDate(b.fecha)} · cuota {b.cuota.toFixed(2)}</div>
      </div>
      <div className={b.balance>=0 ? "bal-pos" : "bal-neg"} style={{fontSize:12.5, flexShrink:0}}>{b.balance>=0?"+":""}{fmtMoney(b.balance)}</div>
    </div>
  );

  if(mejores.length===0){
    return (
      <div className="panel">
        <div className="panel-head"><div><p className="panel-title">Mejores y peores apuestas</p><p className="panel-sub">Ordenadas por balance individual</p></div></div>
        <div className="empty-state">Aún no hay apuestas resueltas en este período.</div>
      </div>
    );
  }

  return (
    <div className="panel">
      <div className="panel-head"><div><p className="panel-title">Mejores y peores apuestas</p><p className="panel-sub">Ordenadas por balance individual</p></div></div>
      <div style={{fontSize:11, fontWeight:600, color:"var(--win)", textTransform:"uppercase", letterSpacing:"0.04em", marginBottom:2, marginTop:4}}>Top ganadoras</div>
      {mejores.map((b,i)=><Row key={"m"+i} b={b} />)}
      <div style={{fontSize:11, fontWeight:600, color:"var(--lose)", textTransform:"uppercase", letterSpacing:"0.04em", marginBottom:2, marginTop:12}}>Peores pérdidas</div>
      {peores.map((b,i)=><Row key={"p"+i} b={b} />)}
    </div>
  );
}

/* ============ HISTORIAL ============ */
function Historial({betsFiltrados, busqueda, setBusqueda, filtroResultado, setFiltroResultado, onUpdateResultado, onDeleteBet, months, mesSel, onSetCapital}){
  const [editingId, setEditingId] = useState(null);

  const resultado = useMemo(()=>{
    let list = [...betsFiltrados].reverse();
    if(filtroResultado === "ganadas") list = list.filter(b=>b.resultado==="GANADA");
    if(filtroResultado === "perdidas") list = list.filter(b=>b.resultado==="PERDIDO");
    if(filtroResultado === "pendientes") list = list.filter(b=>b.resultado==="PENDIENTE");
    if(busqueda.trim()){
      const q = busqueda.trim().toLowerCase();
      list = list.filter(b=> b.partido.toLowerCase().includes(q) || b.tipoApuesta.toLowerCase().includes(q) || b.detalle.toLowerCase().includes(q));
    }
    return list;
  }, [betsFiltrados, busqueda, filtroResultado]);

  return (
    <React.Fragment>
      <div className="toolbar">
        <div className="toolbar-left">
          <div className="search-box">
            <IconSearch color="#9DA2C3" />
            <input placeholder="Buscar por partido, pronóstico..." value={busqueda} onChange={e=>setBusqueda(e.target.value)} />
          </div>
          <CapitalButton months={months} mesSel={mesSel} onSetCapital={onSetCapital} />
        </div>
        <div className="result-filters">
          <div className={"rf-chip" + (filtroResultado==="todas" ? " active-all":"")} onClick={()=>setFiltroResultado("todas")}>Todas</div>
          <div className={"rf-chip" + (filtroResultado==="ganadas" ? " active-win":"")} onClick={()=>setFiltroResultado("ganadas")}>Ganadas</div>
          <div className={"rf-chip" + (filtroResultado==="perdidas" ? " active-lose":"")} onClick={()=>setFiltroResultado("perdidas")}>Perdidas</div>
          <div className={"rf-chip" + (filtroResultado==="pendientes" ? " active-all":"")} onClick={()=>setFiltroResultado("pendientes")}>Pendientes</div>
        </div>
      </div>

      <div className="table-panel">
        <div className="table-scroll">
          <table>
            <thead>
              <tr>
                <th style={{width:96}}>Fecha</th>
                <th style={{width:90}}>Deporte</th>
                <th style={{width:170}}>Partido</th>
                <th style={{width:150}}>Tipo</th>
                <th>Detalle</th>
                <th style={{width:66}}>Cuota</th>
                <th style={{width:104}}>Apostado</th>
                <th style={{width:140}}>Resultado</th>
                <th style={{width:110}}>Balance</th>
                <th style={{width:130}}>Saldo acumulado</th>
                <th style={{width:80}}></th>
              </tr>
            </thead>
            <tbody>
              {resultado.map((b)=>(
                <tr key={b.id}>
                  <td><span className="date-badge">{fmtDate(b.fecha)}</span></td>
                  <td><span className="deporte-badge">{deporteLabel(b.deporte)}</span></td>
                  <td className="pron-cell"><div className="pron-text">{b.partido}</div></td>
                  <td><span className="tipo-badge">{b.tipoApuesta}</span></td>
                  <td className="pron-cell"><div className="pron-text-sub">{b.detalle}</div></td>
                  <td className="num-cell">{b.cuota.toFixed(2)}</td>
                  <td className="num-cell">{fmtMoney(b.apostado)}</td>
                  <td>
                    {editingId === b.id ? (
                      <div className="result-edit">
                        <button className={"opt-pending" + (b.resultado==="PENDIENTE"?" chosen":"")}
                          onClick={()=>{onUpdateResultado(b.id,"PENDIENTE"); setEditingId(null);}}>Pendiente</button>
                        <button className={"opt-win" + (b.resultado==="GANADA"?" chosen":"")}
                          onClick={()=>{onUpdateResultado(b.id,"GANADA"); setEditingId(null);}}>Ganada</button>
                        <button className={"opt-lose" + (b.resultado==="PERDIDO"?" chosen":"")}
                          onClick={()=>{onUpdateResultado(b.id,"PERDIDO"); setEditingId(null);}}>Perdida</button>
                      </div>
                    ) : (
                      <span className={"badge " + (b.resultado==="GANADA"?"win":b.resultado==="PERDIDO"?"lose":"pending")}>
                        {b.resultado==="GANADA" ? "Ganada" : b.resultado==="PERDIDO" ? "Perdida" : "Pendiente"}
                      </span>
                    )}
                  </td>
                  <td className={b.balance===null ? "bal-pending" : (b.balance>=0 ? "bal-pos" : "bal-neg")}>
                    {b.balance===null ? "—" : (b.balance>=0?"+":"") + fmtMoney(b.balance)}
                  </td>
                  <td className="num-cell">{fmtMoney(b.acumulado)}</td>
                  <td>
                    <div style={{display:"flex", gap:6}}>
                      <button className="row-edit-btn" title="Editar resultado"
                        onClick={()=>setEditingId(editingId===b.id ? null : b.id)}>
                        {editingId===b.id ? <IconClose color="var(--ink-soft)" /> : <IconPencil color="var(--ink-soft)" />}
                      </button>
                      <button className="row-edit-btn row-delete-btn" title="Eliminar apuesta"
                        onClick={()=>{ if(window.confirm("¿Eliminar esta apuesta? Esta acción no se puede deshacer.")) onDeleteBet(b.id); }}>
                        <IconTrash color="var(--lose)" />
                      </button>
                    </div>
                  </td>
                </tr>
              ))}
              {resultado.length===0 && (
                <tr><td colSpan="11"><div className="empty-state">No encontramos apuestas que coincidan con este filtro.</div></td></tr>
              )}
            </tbody>
          </table>
        </div>
        <div className="table-foot">
          <span>{resultado.length} apuesta{resultado.length!==1?"s":""} mostrada{resultado.length!==1?"s":""}</span>
          <span>Ordenado por fecha, más reciente primero</span>
        </div>
      </div>
    </React.Fragment>
  );
}

/* ============ AUTENTICACIÓN ============ */
function AuthGate(){
  const [loading, setLoading] = useState(true);
  const [session, setSession] = useState(null);
  const [mode, setMode] = useState("login"); // login | register | forgot | reset

  useEffect(()=>{
    supabaseClient.auth.getSession().then(({data})=>{
      setSession(data.session);
      setLoading(false);
    });
    const { data: listener } = supabaseClient.auth.onAuthStateChange((event, newSession)=>{
      setSession(newSession);
      if(event === "PASSWORD_RECOVERY") setMode("reset");
    });
    return ()=> listener.subscription.unsubscribe();
  }, []);

  if(loading){
    return (
      <div className="auth-shell">
        <div className="auth-loading">Cargando…</div>
      </div>
    );
  }

  if(!session || mode === "reset"){
    return (
      <div className="auth-shell">
        <div className="auth-card">
          <div className="auth-brand">
            <div className="brand-mark"><IconTicket color="#fff" /></div>
            <div>
              <div className="auth-brand-name">Bankroll</div>
              <div className="auth-brand-sub">Registro NEO VIP</div>
            </div>
          </div>
          {mode === "login" && <LoginForm onSwitch={setMode} />}
          {mode === "register" && <RegisterForm onSwitch={setMode} />}
          {mode === "forgot" && <ForgotForm onSwitch={setMode} />}
          {mode === "reset" && <ResetForm onDone={()=>setMode("login")} />}
        </div>
      </div>
    );
  }

  return <App session={session} />;
}

function LoginForm({onSwitch}){
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [error, setError] = useState("");
  const [loading, setLoading] = useState(false);

  async function submit(e){
    e.preventDefault();
    setError(""); setLoading(true);
    const { error } = await supabaseClient.auth.signInWithPassword({ email: email.trim(), password });
    setLoading(false);
    if(error) setError(traducirErrorAuth(error.message));
  }

  return (
    <form onSubmit={submit}>
      <p className="auth-title">Inicia sesión</p>
      <div className="field-row">
        <label className="field-label">Correo</label>
        <input className="field-input" type="email" required value={email} onChange={e=>setEmail(e.target.value)} placeholder="tucorreo@ejemplo.com" />
      </div>
      <div className="field-row">
        <label className="field-label">Contraseña</label>
        <input className="field-input" type="password" required value={password} onChange={e=>setPassword(e.target.value)} placeholder="••••••••" />
      </div>
      {error && <div className="field-error">{error}</div>}
      <button className="btn-primary" style={{width:"100%", justifyContent:"center", marginTop:6}} disabled={loading}>
        {loading ? "Ingresando…" : "Iniciar sesión"}
      </button>
      <div className="auth-links">
        <span className="auth-link" onClick={()=>onSwitch("forgot")}>¿Olvidaste tu contraseña?</span>
        <span className="auth-link" onClick={()=>onSwitch("register")}>Crear una cuenta</span>
      </div>
    </form>
  );
}

function RegisterForm({onSwitch}){
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");
  const [password2, setPassword2] = useState("");
  const [error, setError] = useState("");
  const [ok, setOk] = useState(false);
  const [loading, setLoading] = useState(false);

  async function submit(e){
    e.preventDefault();
    setError("");
    if(password.length < 6){ setError("La contraseña debe tener al menos 6 caracteres"); return; }
    if(password !== password2){ setError("Las contraseñas no coinciden"); return; }
    setLoading(true);
    const { error } = await supabaseClient.auth.signUp({ email: email.trim(), password });
    setLoading(false);
    if(error){ setError(traducirErrorAuth(error.message)); return; }
    setOk(true);
  }

  if(ok){
    return (
      <div>
        <p className="auth-title">Revisa tu correo</p>
        <p className="auth-sub">Te enviamos un link de confirmación a <b>{email}</b>. Ábrelo para activar tu cuenta y luego vuelve a iniciar sesión aquí.</p>
        <button className="btn-primary" style={{width:"100%", justifyContent:"center", marginTop:6}} onClick={()=>onSwitch("login")}>Volver a inicio de sesión</button>
      </div>
    );
  }

  return (
    <form onSubmit={submit}>
      <p className="auth-title">Crea tu cuenta</p>
      <div className="field-row">
        <label className="field-label">Correo</label>
        <input className="field-input" type="email" required value={email} onChange={e=>setEmail(e.target.value)} placeholder="tucorreo@ejemplo.com" />
      </div>
      <div className="field-row">
        <label className="field-label">Contraseña</label>
        <input className="field-input" type="password" required value={password} onChange={e=>setPassword(e.target.value)} placeholder="Mínimo 6 caracteres" />
      </div>
      <div className="field-row">
        <label className="field-label">Repite la contraseña</label>
        <input className="field-input" type="password" required value={password2} onChange={e=>setPassword2(e.target.value)} placeholder="••••••••" />
      </div>
      {error && <div className="field-error">{error}</div>}
      <button className="btn-primary" style={{width:"100%", justifyContent:"center", marginTop:6}} disabled={loading}>
        {loading ? "Creando…" : "Crear cuenta"}
      </button>
      <div className="auth-links">
        <span className="auth-link" onClick={()=>onSwitch("login")}>Ya tengo cuenta</span>
      </div>
    </form>
  );
}

function ForgotForm({onSwitch}){
  const [email, setEmail] = useState("");
  const [error, setError] = useState("");
  const [ok, setOk] = useState(false);
  const [loading, setLoading] = useState(false);

  async function submit(e){
    e.preventDefault();
    setError(""); setLoading(true);
    const { error } = await supabaseClient.auth.resetPasswordForEmail(email.trim(), {
      redirectTo: window.location.origin + window.location.pathname,
    });
    setLoading(false);
    if(error){ setError(traducirErrorAuth(error.message)); return; }
    setOk(true);
  }

  if(ok){
    return (
      <div>
        <p className="auth-title">Revisa tu correo</p>
        <p className="auth-sub">Si <b>{email}</b> tiene una cuenta, te llegará un link para elegir una contraseña nueva.</p>
        <button className="btn-primary" style={{width:"100%", justifyContent:"center", marginTop:6}} onClick={()=>onSwitch("login")}>Volver a inicio de sesión</button>
      </div>
    );
  }

  return (
    <form onSubmit={submit}>
      <p className="auth-title">Recupera tu contraseña</p>
      <p className="auth-sub">Te enviaremos un link para elegir una nueva.</p>
      <div className="field-row">
        <label className="field-label">Correo</label>
        <input className="field-input" type="email" required value={email} onChange={e=>setEmail(e.target.value)} placeholder="tucorreo@ejemplo.com" />
      </div>
      {error && <div className="field-error">{error}</div>}
      <button className="btn-primary" style={{width:"100%", justifyContent:"center", marginTop:6}} disabled={loading}>
        {loading ? "Enviando…" : "Enviar link de recuperación"}
      </button>
      <div className="auth-links">
        <span className="auth-link" onClick={()=>onSwitch("login")}>Volver a inicio de sesión</span>
      </div>
    </form>
  );
}

function ResetForm({onDone}){
  const [password, setPassword] = useState("");
  const [password2, setPassword2] = useState("");
  const [error, setError] = useState("");
  const [loading, setLoading] = useState(false);

  async function submit(e){
    e.preventDefault();
    setError("");
    if(password.length < 6){ setError("La contraseña debe tener al menos 6 caracteres"); return; }
    if(password !== password2){ setError("Las contraseñas no coinciden"); return; }
    setLoading(true);
    const { error } = await supabaseClient.auth.updateUser({ password });
    setLoading(false);
    if(error){ setError(traducirErrorAuth(error.message)); return; }
    onDone();
  }

  return (
    <form onSubmit={submit}>
      <p className="auth-title">Elige una contraseña nueva</p>
      <div className="field-row">
        <label className="field-label">Contraseña nueva</label>
        <input className="field-input" type="password" required value={password} onChange={e=>setPassword(e.target.value)} placeholder="Mínimo 6 caracteres" />
      </div>
      <div className="field-row">
        <label className="field-label">Repite la contraseña</label>
        <input className="field-input" type="password" required value={password2} onChange={e=>setPassword2(e.target.value)} placeholder="••••••••" />
      </div>
      {error && <div className="field-error">{error}</div>}
      <button className="btn-primary" style={{width:"100%", justifyContent:"center", marginTop:6}} disabled={loading}>
        {loading ? "Guardando…" : "Actualizar contraseña"}
      </button>
    </form>
  );
}

function traducirErrorAuth(msg){
  const m = (msg || "").toLowerCase();
  if(m.includes("invalid login credentials")) return "Correo o contraseña incorrectos.";
  if(m.includes("user already registered") || m.includes("already registered")) return "Ese correo ya tiene una cuenta.";
  if(m.includes("email not confirmed")) return "Confirma tu correo antes de iniciar sesión (revisa tu bandeja de entrada).";
  if(m.includes("password should be at least")) return "La contraseña debe tener al menos 6 caracteres.";
  if(m.includes("rate limit")) return "Demasiados intentos. Espera un momento y vuelve a intentar.";
  return "No pudimos completar la acción: " + msg;
}

/* ============ RENDER ============ */
const RootApp = () => {
  return <AuthGate />;
};
const root = ReactDOM.createRoot(document.getElementById("root"));
root.render(<RootApp />);
</script>
</body>
</html>
