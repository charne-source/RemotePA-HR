<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>Remote PA — HR System</title>
<script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
<script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
<script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
<script src="https://cdn.jsdelivr.net/npm/@supabase/supabase-js@2"></script>
<script src="https://cdn.jsdelivr.net/npm/@emailjs/browser@3/dist/email.min.js"></script>
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@300;400;500;600;700&family=DM+Serif+Display:ital@0;1&display=swap" rel="stylesheet"/>
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
:root{
  --navy:#2E3191;--navy-dark:#1e2060;--navy-light:#ecedf9;--navy-mid:#4547b0;
  --orange:#F7A21B;--orange-dark:#d4880e;--orange-light:#fff4e0;
  --teal:#00AEEF;--teal-light:#e0f5fd;
  --text:#1a1c3a;--text-muted:#5a5d80;--border:#d8d9ee;
  --surface:#fff;--bg:#f4f5fb;--radius:10px;--radius-lg:16px;
  --danger:#c0392b;--danger-light:#fdf0ef;--warn-light:#fff8e7;
}
body{font-family:'DM Sans',sans-serif;background:var(--bg);color:var(--text);min-height:100vh}
input,select,textarea,button{font-family:inherit}
input,select,textarea{width:100%;padding:9px 12px;border:1.5px solid var(--border);border-radius:var(--radius);background:var(--surface);color:var(--text);font-size:14px;outline:none;transition:border-color .15s}
input:focus,select:focus,textarea:focus{border-color:var(--navy);box-shadow:0 0 0 3px rgba(46,49,145,.1)}
textarea{resize:vertical;min-height:72px}
label{font-size:13px;font-weight:500;color:var(--text-muted);display:block;margin-bottom:5px}
.field{margin-bottom:13px}
.btn{display:inline-flex;align-items:center;gap:6px;padding:9px 18px;border-radius:var(--radius);font-size:14px;font-weight:500;cursor:pointer;border:none;transition:all .15s;text-decoration:none}
.btn-primary{background:var(--navy);color:#fff}.btn-primary:hover{background:var(--navy-dark)}
.btn-orange{background:var(--orange);color:#fff}.btn-orange:hover{background:var(--orange-dark)}
.btn-secondary{background:var(--navy-light);color:var(--navy-dark);border:1px solid var(--border)}.btn-secondary:hover{background:#dfe0f5}
.btn-danger{background:var(--danger-light);color:var(--danger);border:1px solid #f5c6c0}.btn-danger:hover{background:#f8d7d3}
.btn-sm{padding:6px 12px;font-size:13px}
.badge{display:inline-flex;align-items:center;padding:3px 10px;border-radius:20px;font-size:12px;font-weight:500}
.badge-navy{background:var(--navy-light);color:var(--navy-dark)}
.badge-orange{background:var(--orange-light);color:var(--orange-dark)}
.badge-teal{background:var(--teal-light);color:#006b94}
.badge-green{background:#e8f5e9;color:#2e7d32}
.badge-amber{background:var(--warn-light);color:#7a5400}
.badge-red{background:var(--danger-light);color:var(--danger)}
.badge-gray{background:#eef1f0;color:#5a6068}
.card{background:var(--surface);border-radius:var(--radius-lg);border:1px solid var(--border)}
.card-header{padding:14px 18px;border-bottom:1px solid var(--border);display:flex;align-items:center;justify-content:space-between}
.card-body{padding:18px}
table{width:100%;border-collapse:collapse;font-size:14px}
th{text-align:left;font-size:12px;font-weight:600;color:var(--text-muted);padding:9px 13px;background:#f8f8fd;border-bottom:1px solid var(--border)}
td{padding:11px 13px;border-bottom:1px solid #f0f0fa;vertical-align:middle}
tr:last-child td{border-bottom:none}
tr:hover td{background:#fafafe}
.avatar{width:34px;height:34px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-weight:600;font-size:12px;flex-shrink:0}
.grid2{display:grid;grid-template-columns:1fr 1fr;gap:13px}
.stat-card{background:var(--surface);border-radius:var(--radius-lg);border:1px solid var(--border);padding:16px 18px}
.stat-value{font-size:28px;font-weight:700;font-family:'DM Serif Display',serif}
.stat-label{font-size:12px;color:var(--text-muted);margin-top:3px}
.tab-bar{display:flex;gap:3px;padding:3px;background:var(--navy-light);border-radius:var(--radius)}
.tab{padding:7px 14px;border-radius:7px;font-size:13px;font-weight:500;cursor:pointer;border:none;background:transparent;color:var(--text-muted);transition:all .15s}
.tab.active{background:var(--surface);color:var(--navy-dark);box-shadow:0 1px 4px rgba(46,49,145,.1)}
.empty{text-align:center;padding:40px 20px;color:var(--text-muted);font-size:14px}
.search-bar{position:relative}
.search-bar input{padding-left:35px}
.s-icon{position:absolute;left:10px;top:50%;transform:translateY(-50%);width:15px;height:15px;opacity:.4;pointer-events:none}
.tag{display:inline-flex;align-items:center;padding:2px 8px;border-radius:12px;font-size:11px;font-weight:500;background:var(--navy-light);color:var(--navy-dark)}
.progress-bar{height:5px;background:var(--border);border-radius:3px;overflow:hidden}
.progress-fill{height:100%;border-radius:3px}
.modal-overlay{position:fixed;inset:0;background:rgba(20,22,60,.5);z-index:1000;display:flex;align-items:center;justify-content:center;padding:20px}
.modal{background:var(--surface);border-radius:var(--radius-lg);width:100%;max-width:580px;max-height:92vh;overflow-y:auto}
.modal-header{padding:16px 20px;border-bottom:1px solid var(--border);display:flex;align-items:center;justify-content:space-between}
.modal-body{padding:20px}
.modal-footer{padding:13px 20px;border-top:1px solid var(--border);display:flex;justify-content:flex-end;gap:10px}
.sidebar{width:230px;background:var(--navy-dark);min-height:100vh;flex-shrink:0;position:fixed;top:0;left:0;height:100vh;overflow-y:auto;z-index:100}
.s-logo{padding:20px 16px 14px;border-bottom:1px solid rgba(255,255,255,.1)}
.s-logo .lr{font-size:19px;font-weight:700;color:#fff;letter-spacing:-.5px}
.s-logo .lp{font-size:19px;font-weight:700;color:var(--orange);letter-spacing:-.5px}
.s-logo .ls{font-size:10px;color:rgba(255,255,255,.3);margin-top:2px;letter-spacing:1px;text-transform:uppercase}
.n-sec{padding:14px 8px 2px}
.nav-item{display:flex;align-items:center;gap:9px;padding:8px 9px;border-radius:8px;cursor:pointer;font-size:13px;font-weight:500;color:rgba(255,255,255,.6);margin-bottom:2px;transition:all .12s;border:none;background:transparent;width:100%;text-align:left}
.nav-item:hover{background:rgba(255,255,255,.08);color:#fff}
.nav-item.active{background:rgba(247,162,27,.18);color:#fff;border-left:3px solid var(--orange);padding-left:6px}
.nav-item svg{width:17px;height:17px;flex-shrink:0}
.main{margin-left:230px;padding:26px;min-height:100vh}
.page-header{margin-bottom:22px;display:flex;align-items:center;justify-content:space-between}
.page-title{font-size:22px;font-weight:700;color:var(--text);font-family:'DM Serif Display',serif}
.page-sub{font-size:13px;color:var(--text-muted);margin-top:2px}
.toast{position:fixed;bottom:22px;right:22px;background:var(--navy-dark);color:#fff;padding:11px 18px;border-radius:var(--radius);font-size:14px;font-weight:500;z-index:2000;border-left:3px solid var(--orange)}
.divider{border:none;border-top:1px solid var(--border);margin:14px 0}
.err-msg{font-size:12px;color:var(--danger);margin-top:4px;display:none}
.err-msg.show{display:block}
input.inp-err{border-color:var(--danger)!important}
.info-box{background:var(--navy-light);border-radius:8px;padding:10px 13px;font-size:13px;color:var(--navy-dark);margin-bottom:13px}
.warn-box{background:var(--orange-light);border-radius:8px;padding:10px 13px;font-size:13px;color:var(--orange-dark);font-weight:500;margin-bottom:13px}
.upload-zone{border:2px dashed var(--border);border-radius:var(--radius);padding:20px;text-align:center;cursor:pointer;transition:border-color .15s;background:#fafafe}
.upload-zone:hover,.upload-zone.has-file{border-color:var(--navy);background:var(--navy-light)}
.loading-screen{min-height:100vh;display:flex;align-items:center;justify-content:center;flex-direction:column;gap:16px;background:var(--navy-dark)}
.spinner{width:40px;height:40px;border:3px solid rgba(255,255,255,.15);border-top-color:var(--orange);border-radius:50%;animation:spin .8s linear infinite}
@keyframes spin{to{transform:rotate(360deg)}}
.cal-grid{display:grid;grid-template-columns:repeat(7,1fr);gap:2px}
.cal-day-name{text-align:center;font-size:11px;font-weight:600;color:var(--text-muted);padding:6px 0}
.cal-day{min-height:68px;border-radius:7px;padding:5px;background:#fafafe;border:1px solid transparent;font-size:12px}
.cal-day.today{border-color:var(--orange);background:var(--orange-light)}
.cal-day-num{font-weight:600;font-size:13px;margin-bottom:2px}
.cal-event{border-radius:4px;padding:1px 5px;margin-bottom:2px;font-size:10px;font-weight:500;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
.cal-leave{background:#e8f5e9;color:#2e7d32}
.cal-bday{background:#fce4ec;color:#880e4f}
.cal-bh{background:var(--navy-light);color:var(--navy-dark)}
.ann-card{background:var(--surface);border:1px solid var(--border);border-radius:var(--radius-lg);padding:18px;margin-bottom:12px}
.auth-screen{min-height:100vh;display:flex}
.auth-left{width:400px;flex-shrink:0;background:var(--navy-dark);display:flex;flex-direction:column;justify-content:center;padding:52px 44px}
.auth-left h1{font-family:'DM Serif Display',serif;font-size:40px;line-height:1.1;color:#fff;margin-bottom:14px}
.auth-left h1 em{color:var(--orange);font-style:italic}
.auth-left p{font-size:14px;color:rgba(255,255,255,.5);line-height:1.7;margin-bottom:28px}
.auth-perks{display:flex;flex-direction:column;gap:9px}
.auth-perk{display:flex;align-items:center;gap:9px;font-size:13px;color:rgba(255,255,255,.6)}
.auth-perk-dot{width:7px;height:7px;border-radius:50%;background:var(--orange);flex-shrink:0}
.auth-right{flex:1;display:flex;align-items:center;justify-content:center;padding:36px;background:var(--bg)}
.auth-card{background:var(--surface);border-radius:var(--radius-lg);border:1px solid var(--border);width:100%;max-width:420px;overflow:hidden;box-shadow:0 8px 32px rgba(46,49,145,.1)}
.auth-card-top{background:var(--navy);padding:18px 26px;display:flex;align-items:center;gap:9px}
.auth-card-top h2{font-size:15px;font-weight:600;color:#fff}
.auth-card-body{padding:26px}
.auth-tabs{display:flex;margin-bottom:20px;border-bottom:2px solid var(--border)}
.auth-tab{flex:1;text-align:center;padding:9px;font-size:14px;font-weight:600;cursor:pointer;border:none;background:transparent;color:var(--text-muted);border-bottom:2px solid transparent;margin-bottom:-2px;transition:all .15s}
.auth-tab.active{color:var(--navy);border-bottom-color:var(--navy)}
.btn-auth{width:100%;padding:12px;background:var(--orange);color:#fff;border:none;border-radius:var(--radius);font-size:15px;font-weight:700;cursor:pointer;transition:background .15s;margin-top:4px}
.btn-auth:hover{background:var(--orange-dark)}
.btn-auth:disabled{background:#f0c97a;cursor:not-allowed}
@media(max-width:800px){.auth-left{display:none}.auth-right{padding:20px}}
</style>
</head>
<body>
<div id="root"></div>
<script type="text/babel">

var useState = React.useState;
var useEffect = React.useEffect;
var useRef = React.useRef;
var useCallback = React.useCallback;

/* ── CONFIG ────────────────────────────────────── */
var SUPABASE_URL = 'https://oydrccpehlsglpljwmrd.supabase.co';
var SUPABASE_KEY = 'sb_publishable_iUPeM89bC0MDeOZ21VMgqg_VUAk9VlY';
var sb = supabase.createClient(SUPABASE_URL, SUPABASE_KEY);

var ADMINS = ['charne@remote-pa.co', 'xander@remote-pa.co'];
var LEAVE_HR_1 = 'charne@remote-pa.co';
var LEAVE_HR_2 = 'xander@remote-pa.co';
var EJS_SVC = 'service_a9aosxg';
var EJS_TPL = 'template_t4owdpn';
var EJS_KEY = 'BmAUsYzWapn6tvcFr';

function isAdmin(email) {
  return ADMINS.indexOf((email || '').toLowerCase().trim()) !== -1;
}

function sendMail(to, subject, message) {
  try {
    emailjs.send(EJS_SVC, EJS_TPL, { to_email: to, subject: subject, message: message }, EJS_KEY);
  } catch(e) {
    console.warn('Email error', e);
  }
}

/* ── BANK HOLIDAYS ─────────────────────────────── */
var ALL_BH = [
  '2024-01-01','2024-03-29','2024-04-01','2024-05-06','2024-05-27','2024-08-26','2024-12-25','2024-12-26',
  '2025-01-01','2025-04-18','2025-04-21','2025-05-05','2025-05-26','2025-08-25','2025-12-25','2025-12-26',
  '2026-01-01','2026-04-03','2026-04-06','2026-05-04','2026-05-25','2026-08-31','2026-12-25','2026-12-28'
];
function isBH(d) { return ALL_BH.indexOf(d) !== -1; }

var LEAVE_TYPES = ['Paid Personal Leave','Paid Sick Leave','Unpaid Personal Leave','Unpaid Sick Leave'];
var DEPT_BADGE = {Operations:'badge-navy',Finance:'badge-amber',HR:'badge-teal',Admin:'badge-gray',Sales:'badge-orange',Marketing:'badge-navy',IT:'badge-teal',Management:'badge-red'};
var AV_COLORS = [['#2E3191','#ecedf9'],['#F7A21B','#fff4e0'],['#00AEEF','#e0f5fd'],['#7b3fa0','#f3e8fa'],['#c0392b','#fdf0ef'],['#2e7d32','#e8f5e9']];

function avColor(name) {
  var h = 0;
  for (var i = 0; i < name.length; i++) h = (h * 31 + name.charCodeAt(i)) & 0xffffffff;
  var pair = AV_COLORS[Math.abs(h) % AV_COLORS.length];
  return { bg: pair[0], fg: pair[1] };
}
function initials(name) {
  return name.split(' ').filter(Boolean).slice(0,2).map(function(w){return w[0].toUpperCase();}).join('');
}
function todayStr() { return new Date().toISOString().split('T')[0]; }
function fmtDate(ds) {
  if (!ds) return '—';
  var p = ds.split('-');
  return p[2]+'/'+p[1]+'/'+p[0];
}
function calcDays(start, end, half) {
  if (!start || !end) return 0;
  if (half) return 0.5;
  var d = new Date(start), e = new Date(end), n = 0;
  while (d <= e) {
    var day = d.getDay();
    var ds = d.toISOString().split('T')[0];
    if (day !== 0 && day !== 6 && !isBH(ds)) n++;
    d.setDate(d.getDate() + 1);
  }
  return n;
}
function calcBalance(startDate, usedDays) {
  var now = new Date();
  var month = now.getMonth(); // 0-based
  var accrualMonths = month + 1; // months elapsed this year so far (1=Jan,5=May)
  if (startDate) {
    var s = new Date(startDate);
    if (s.getFullYear() === now.getFullYear()) {
      accrualMonths = Math.max(1, now.getMonth() - s.getMonth() + 1);
    }
  }
  var accrued = Math.min(parseFloat((accrualMonths * (10/12)).toFixed(1)), 10);
  var remaining = parseFloat(Math.max(0, accrued - usedDays).toFixed(1));
  return { total: accrued, used: usedDays, remaining: remaining };
}

/* ── ICONS ─────────────────────────────────────── */
var I = {
  dash: React.createElement('svg',{viewBox:'0 0 24 24',fill:'none',stroke:'currentColor',strokeWidth:'1.8',strokeLinecap:'round',strokeLinejoin:'round'},React.createElement('rect',{x:'3',y:'3',width:'7',height:'7'}),React.createElement('rect',{x:'14',y:'3',width:'7',height:'7'}),React.createElement('rect',{x:'14',y:'14',width:'7',height:'7'}),React.createElement('rect',{x:'3',y:'14',width:'7',height:'7'})),
  emp: React.createElement('svg',{viewBox:'0 0 24 24',fill:'none',stroke:'currentColor',strokeWidth:'1.8',strokeLinecap:'round',strokeLinejoin:'round'},React.createElement('circle',{cx:'9',cy:'7',r:'3'}),React.createElement('path',{d:'M3 20v-2a6 6 0 0 1 12 0v2'}),React.createElement('circle',{cx:'17',cy:'8',r:'2.5'}),React.createElement('path',{d:'M21 20v-1.5a4 4 0 0 0-5-3.85'})),
  doc: React.createElement('svg',{viewBox:'0 0 24 24',fill:'none',stroke:'currentColor',strokeWidth:'1.8',strokeLinecap:'round',strokeLinejoin:'round'},React.createElement('path',{d:'M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z'}),React.createElement('polyline',{points:'14 2 14 8 20 8'}),React.createElement('line',{x1:'8',y1:'13',x2:'16',y2:'13'}),React.createElement('line',{x1:'8',y1:'17',x2:'12',y2:'17'})),
  cal: React.createElement('svg',{viewBox:'0 0 24 24',fill:'none',stroke:'currentColor',strokeWidth:'1.8',strokeLinecap:'round',strokeLinejoin:'round'},React.createElement('rect',{x:'3',y:'4',width:'18',height:'18',rx:'2'}),React.createElement('line',{x1:'16',y1:'2',x2:'16',y2:'6'}),React.createElement('line',{x1:'8',y1:'2',x2:'8',y2:'6'}),React.createElement('line',{x1:'3',y1:'10',x2:'21',y2:'10'})),
  ann: React.createElement('svg',{viewBox:'0 0 24 24',fill:'none',stroke:'currentColor',strokeWidth:'1.8',strokeLinecap:'round',strokeLinejoin:'round'},React.createElement('path',{d:'M22 17H2a3 3 0 0 0 3-3V9a7 7 0 0 1 14 0v5a3 3 0 0 0 3 3z'}),React.createElement('path',{d:'M9 17v1a3 3 0 0 0 6 0v-1'})),
  plus: React.createElement('svg',{viewBox:'0 0 24 24',fill:'none',stroke:'currentColor',strokeWidth:'2',strokeLinecap:'round'},React.createElement('line',{x1:'12',y1:'5',x2:'12',y2:'19'}),React.createElement('line',{x1:'5',y1:'12',x2:'19',y2:'12'})),
  x: React.createElement('svg',{viewBox:'0 0 24 24',fill:'none',stroke:'currentColor',strokeWidth:'2',strokeLinecap:'round'},React.createElement('line',{x1:'18',y1:'6',x2:'6',y2:'18'}),React.createElement('line',{x1:'6',y1:'6',x2:'18',y2:'18'})),
  chk: React.createElement('svg',{viewBox:'0 0 24 24',fill:'none',stroke:'currentColor',strokeWidth:'2.5',strokeLinecap:'round',strokeLinejoin:'round'},React.createElement('polyline',{points:'20 6 9 17 4 12'})),
  eye: React.createElement('svg',{viewBox:'0 0 24 24',fill:'none',stroke:'currentColor',strokeWidth:'1.8',strokeLinecap:'round',strokeLinejoin:'round'},React.createElement('path',{d:'M1 12s4-8 11-8 11 8 11 8-4 8-11 8-11-8-11-8z'}),React.createElement('circle',{cx:'12',cy:'12',r:'3'})),
  edit: React.createElement('svg',{viewBox:'0 0 24 24',fill:'none',stroke:'currentColor',strokeWidth:'1.8',strokeLinecap:'round',strokeLinejoin:'round'},React.createElement('path',{d:'M11 4H4a2 2 0 0 0-2 2v14a2 2 0 0 0 2 2h14a2 2 0 0 0 2-2v-7'}),React.createElement('path',{d:'M18.5 2.5a2.121 2.121 0 0 1 3 3L12 15l-4 1 1-4 9.5-9.5z'})),
  trash: React.createElement('svg',{viewBox:'0 0 24 24',fill:'none',stroke:'currentColor',strokeWidth:'1.8',strokeLinecap:'round',strokeLinejoin:'round'},React.createElement('polyline',{points:'3 6 5 6 21 6'}),React.createElement('path',{d:'M19 6l-1 14H6L5 6'}),React.createElement('path',{d:'M10 11v6m4-6v6'}),React.createElement('path',{d:'M9 6V4h6v2'})),
  search: React.createElement('svg',{viewBox:'0 0 24 24',fill:'none',stroke:'currentColor',strokeWidth:'2',strokeLinecap:'round'},React.createElement('circle',{cx:'11',cy:'11',r:'8'}),React.createElement('line',{x1:'21',y1:'21',x2:'16.65',y2:'16.65'})),
  out: React.createElement('svg',{viewBox:'0 0 24 24',fill:'none',stroke:'currentColor',strokeWidth:'1.8',strokeLinecap:'round',strokeLinejoin:'round'},React.createElement('path',{d:'M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4'}),React.createElement('polyline',{points:'16 17 21 12 16 7'}),React.createElement('line',{x1:'21',y1:'12',x2:'9',y2:'12'})),
  upload: React.createElement('svg',{viewBox:'0 0 24 24',fill:'none',stroke:'currentColor',strokeWidth:'1.8',strokeLinecap:'round',strokeLinejoin:'round'},React.createElement('path',{d:'M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4'}),React.createElement('polyline',{points:'17 8 12 3 7 8'}),React.createElement('line',{x1:'12',y1:'3',x2:'12',y2:'15'})),
  refresh: React.createElement('svg',{viewBox:'0 0 24 24',fill:'none',stroke:'currentColor',strokeWidth:'1.8',strokeLinecap:'round',strokeLinejoin:'round'},React.createElement('polyline',{points:'23 4 23 10 17 10'}),React.createElement('path',{d:'M20.49 15a9 9 0 1 1-2.12-9.36L23 10'})),
};

function Toast(props) {
  useEffect(function() { var t = setTimeout(props.onDone, 3000); return function(){ clearTimeout(t); }; }, []);
  return <div className="toast">✓ {props.msg}</div>;
}

function Spinner() {
  return <div className="loading-screen">
    <div style={{display:'flex',alignItems:'baseline'}}>
      <span style={{fontSize:28,fontWeight:700,color:'#fff'}}>Remote</span>
      <span style={{fontSize:28,fontWeight:700,color:'var(--orange)'}}>&nbsp;PA</span>
    </div>
    <div className="spinner"/>
    <div style={{color:'rgba(255,255,255,.4)',fontSize:13}}>Loading…</div>
  </div>;
}

function Modal(props) {
  return <div className="modal-overlay" onClick={function(e){if(e.target===e.currentTarget)props.onClose();}}>
    <div className="modal">
      <div className="modal-header">
        <span style={{fontWeight:600,fontSize:15}}>{props.title}</span>
        <button className="btn btn-sm btn-secondary" style={{padding:'4px 8px',minWidth:0}} onClick={props.onClose}>{I.x}</button>
      </div>
      <div className="modal-body">{props.children}</div>
      {props.footer && <div className="modal-footer">{props.footer}</div>}
    </div>
  </div>;
}

/* ── AUTH ──────────────────────────────────────── */
function AuthScreen(props) {
  var s = useState('login'); var mode = s[0]; var setMode = s[1];
  var f = useState({firstName:'',lastName:'',email:'',password:'',confirm:'',dept:''}); var form = f[0]; var setForm = f[1];
  var e = useState({}); var errs = e[0]; var setErrs = e[1];
  var l = useState(false); var loading = l[0]; var setLoading = l[1];
  var se = useState(''); var serverErr = se[0]; var setServerErr = se[1];

  function upd(k,v) { setForm(function(p){ var n=Object.assign({},p); n[k]=v; return n; }); }

  function validate() {
    var err = {};
    if (mode === 'register') {
      if (!form.firstName.trim()) err.firstName = 'Required';
      if (!form.lastName.trim()) err.lastName = 'Required';
    }
    if (!/^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(form.email)) err.email = 'Enter a valid email';
    if (form.password.length < 8) err.password = 'At least 8 characters';
    if (mode === 'register' && form.password !== form.confirm) err.confirm = 'Passwords do not match';
    setErrs(err);
    return Object.keys(err).length === 0;
  }

  async function submit() {
    setServerErr('');
    if (!validate()) return;
    setLoading(true);
    try {
      if (mode === 'register') {
        var chk = await sb.from('users').select('id').eq('email', form.email).maybeSingle();
        if (chk.data) { setServerErr('An account with this email already exists.'); setLoading(false); return; }
        var ins = await sb.from('users').insert({first_name:form.firstName,last_name:form.lastName,email:form.email,department:form.dept,is_admin:isAdmin(form.email)});
        if (ins.error) throw ins.error;
        var ud = {name:form.firstName+' '+form.lastName,email:form.email,dept:form.dept,isAdmin:isAdmin(form.email)};
        localStorage.setItem('rpa_user', JSON.stringify(ud));
        props.onLogin(ud);
      } else {
        var res = await sb.from('users').select('*').eq('email', form.email).maybeSingle();
        if (res.error || !res.data) { setServerErr('No account found. Please register first.'); setLoading(false); return; }
        var u = res.data;
        var ud2 = {name:u.first_name+' '+u.last_name,email:u.email,dept:u.department,isAdmin:u.is_admin};
        localStorage.setItem('rpa_user', JSON.stringify(ud2));
        props.onLogin(ud2);
      }
    } catch(err) { setServerErr(err.message || 'Something went wrong.'); }
    setLoading(false);
  }

  return <div className="auth-screen">
    <div className="auth-left">
      <div style={{marginBottom:32}}>
        <div style={{display:'flex',alignItems:'baseline',marginBottom:6}}>
          <span style={{fontSize:24,fontWeight:700,color:'#fff'}}>Remote</span>
          <span style={{fontSize:24,fontWeight:700,color:'var(--orange)'}}>&nbsp;PA</span>
        </div>
        <div style={{fontSize:10,color:'rgba(255,255,255,.3)',letterSpacing:'1.5px',textTransform:'uppercase'}}>HR System · By The Sales Centre</div>
      </div>
      <h1>Your team,<br/>all in one<br/><em>place</em></h1>
      <p>Internal HR system for Remote PA staff.</p>
      <div className="auth-perks">
        {['Employee profiles & records','Paid & unpaid leave tracking','10 days/year monthly accrual','UK bank holidays built in','Half day leave supported'].map(function(p){return <div key={p} className="auth-perk"><div className="auth-perk-dot"/>{p}</div>;})}
      </div>
    </div>
    <div className="auth-right">
      <div className="auth-card">
        <div className="auth-card-top">
          <div style={{width:8,height:8,borderRadius:'50%',background:'var(--orange)'}}/>
          <h2>{mode==='login'?'Sign in':'Create your account'}</h2>
        </div>
        <div className="auth-card-body">
          <div className="auth-tabs">
            <button className={'auth-tab'+(mode==='login'?' active':'')} onClick={function(){setMode('login');setErrs({});setServerErr('');}}>Sign in</button>
            <button className={'auth-tab'+(mode==='register'?' active':'')} onClick={function(){setMode('register');setErrs({});setServerErr('');}}>Register</button>
          </div>
          {mode==='register' && <>
            <div className="grid2">
              <div className="field"><label>First name</label><input value={form.firstName} onChange={function(e){upd('firstName',e.target.value);}} placeholder="Jane" className={errs.firstName?'inp-err':''}/>{errs.firstName&&<div className="err-msg show">{errs.firstName}</div>}</div>
              <div className="field"><label>Last name</label><input value={form.lastName} onChange={function(e){upd('lastName',e.target.value);}} placeholder="Smith" className={errs.lastName?'inp-err':''}/>{errs.lastName&&<div className="err-msg show">{errs.lastName}</div>}</div>
            </div>
            <div className="field"><label>Department</label>
              <select value={form.dept} onChange={function(e){upd('dept',e.target.value);}}>
                <option value="">Select department…</option>
                <option>Operations</option><option>Finance</option><option>HR</option><option>Admin</option><option>Sales</option><option>Marketing</option><option>IT</option><option>Management</option>
              </select>
            </div>
          </>}
          <div className="field"><label>Email address</label><input type="email" value={form.email} onChange={function(e){upd('email',e.target.value);}} placeholder="you@remote-pa.co" className={errs.email?'inp-err':''}/>{errs.email&&<div className="err-msg show">{errs.email}</div>}</div>
          <div className="field"><label>Password</label><input type="password" value={form.password} onChange={function(e){upd('password',e.target.value);}} placeholder="At least 8 characters" className={errs.password?'inp-err':''}/>{errs.password&&<div className="err-msg show">{errs.password}</div>}</div>
          {mode==='register' && <div className="field"><label>Confirm password</label><input type="password" value={form.confirm} onChange={function(e){upd('confirm',e.target.value);}} placeholder="Repeat password" className={errs.confirm?'inp-err':''}/>{errs.confirm&&<div className="err-msg show">{errs.confirm}</div>}</div>}
          {serverErr && <div style={{background:'var(--danger-light)',color:'var(--danger)',padding:'10px 13px',borderRadius:8,fontSize:13,marginBottom:12}}>{serverErr}</div>}
          <button className="btn-auth" onClick={submit} disabled={loading}>{loading?(mode==='login'?'Signing in…':'Creating account…'):(mode==='login'?'Sign in →':'Create account →')}</button>
          {mode==='login' && <div style={{textAlign:'center',marginTop:12,fontSize:13,color:'var(--text-muted)'}}>No account? <button onClick={function(){setMode('register');setErrs({});setServerErr('');}} style={{background:'none',border:'none',color:'var(--navy)',fontWeight:600,cursor:'pointer',fontSize:13}}>Register here</button></div>}
        </div>
      </div>
    </div>
  </div>;
}

/* ── CALENDAR ──────────────────────────────────── */
function CalendarWidget(props) {
  var employees = props.employees; var leaveRequests = props.leaveRequests;
  var vs = useState(new Date()); var viewDate = vs[0]; var setViewDate = vs[1];
  var yr = viewDate.getFullYear(); var mo = viewDate.getMonth();
  var firstDay = new Date(yr,mo,1).getDay();
  var dim = new Date(yr,mo+1,0).getDate();
  var offset = firstDay===0?6:firstDay-1;
  var MONTHS=['January','February','March','April','May','June','July','August','September','October','November','December'];
  var t = new Date();

  function getEvents(d) {
    var ds = yr+'-'+String(mo+1).padStart(2,'0')+'-'+String(d).padStart(2,'0');
    var ev = [];
    if (isBH(ds)) ev.push({type:'bh',label:'Bank Holiday'});
    leaveRequests.filter(function(l){return l.status==='Approved';}).forEach(function(l){
      if (!l.start_date||!l.end_date) return;
      if (ds>=l.start_date&&ds<=l.end_date) {
        var emp = employees.find(function(e){return e.id===l.employee_id;});
        if (emp) ev.push({type:'leave',label:(l.half_day?'½ ':'')+emp.first_name+' off'});
      }
    });
    employees.forEach(function(e){
      if (!e.dob) return;
      var p = e.dob.split('-');
      if (parseInt(p[1])-1===mo&&parseInt(p[2])===d) ev.push({type:'bday',label:'🎂 '+e.first_name});
    });
    return ev;
  }

  var cells = [];
  for (var i=0;i<offset;i++) cells.push(null);
  for (var d=1;d<=dim;d++) cells.push(d);

  return <div className="card" style={{marginBottom:18}}>
    <div className="card-header">
      <span style={{fontWeight:600,fontSize:14}}>Team calendar — {MONTHS[mo]} {yr}</span>
      <div style={{display:'flex',gap:5}}>
        <button className="btn btn-sm btn-secondary" onClick={function(){setViewDate(new Date(yr,mo-1,1));}}>‹</button>
        <button className="btn btn-sm btn-secondary" style={{fontSize:12}} onClick={function(){setViewDate(new Date());}}>Today</button>
        <button className="btn btn-sm btn-secondary" onClick={function(){setViewDate(new Date(yr,mo+1,1));}}>›</button>
      </div>
    </div>
    <div className="card-body" style={{paddingTop:10}}>
      <div className="cal-grid" style={{marginBottom:3}}>
        {['Mon','Tue','Wed','Thu','Fri','Sat','Sun'].map(function(d){return <div key={d} className="cal-day-name">{d}</div>;})}
      </div>
      <div className="cal-grid">
        {cells.map(function(d,idx){
          if (!d) return <div key={'e'+idx}/>;
          var isToday = yr===t.getFullYear()&&mo===t.getMonth()&&d===t.getDate();
          var ev = getEvents(d);
          return <div key={d} className={'cal-day'+(isToday?' today':'')}>
            <div className="cal-day-num" style={{color:isToday?'var(--orange-dark)':''}}>{d}</div>
            {ev.slice(0,2).map(function(e,j){return <div key={j} className={'cal-event '+(e.type==='leave'?'cal-leave':e.type==='bday'?'cal-bday':'cal-bh')}>{e.label}</div>;})}
            {ev.length>2&&<div style={{fontSize:9,color:'var(--text-muted)'}}>+{ev.length-2} more</div>}
          </div>;
        })}
      </div>
    </div>
  </div>;
}

/* ── DASHBOARD ─────────────────────────────────── */
function Dashboard(props) {
  var employees=props.employees,contracts=props.contracts,leaveRequests=props.leaveRequests,currentUser=props.currentUser,onRefresh=props.onRefresh,refreshing=props.refreshing;
  var today = todayStr();
  var todayParts = today.split('-');
  var tm = parseInt(todayParts[1]); var td = parseInt(todayParts[2]);
  var active = employees.filter(function(e){return e.status==='Active';}).length;
  var pending = leaveRequests.filter(function(l){return l.status==='Pending';}).length;
  var expiring = contracts.filter(function(c){if(!c.end_date)return false;var d=(new Date(c.end_date)-new Date())/864e5;return d>=0&&d<=60;}).length;
  var offToday = leaveRequests.filter(function(l){return l.status==='Approved'&&l.start_date<=today&&l.end_date>=today;});
  var birthdays = employees.filter(function(e){
    if(!e.dob)return false;
    var p=e.dob.split('-');
    return parseInt(p[1])===tm&&parseInt(p[2])===td;
  });
  var deptCounts = employees.reduce(function(a,e){if(e.department)a[e.department]=(a[e.department]||0)+1;return a;},{});

  return <div>
    <div className="page-header">
      <div>
        <div className="page-title">Good {new Date().getHours()<12?'morning':new Date().getHours()<18?'afternoon':'evening'}, {currentUser.name.split(' ')[0]} 👋</div>
        <div className="page-sub">Here's what's happening at Remote PA today</div>
      </div>
      <button className="btn btn-secondary" onClick={onRefresh} disabled={refreshing}>{I.refresh} {refreshing?'Refreshing…':'Refresh'}</button>
    </div>

    {birthdays.length>0 && <div style={{background:'#fce4ec',border:'1px solid #f48fb1',borderRadius:12,padding:'14px 18px',marginBottom:16,display:'flex',alignItems:'center',gap:12}}>
      <span style={{fontSize:22}}>🎂</span>
      <div>
        <div style={{fontWeight:600,color:'#880e4f',fontSize:14}}>Birthday{birthdays.length>1?'s':''} today!</div>
        <div style={{color:'#ad1457',fontSize:13}}>{birthdays.map(function(e){return e.first_name+' '+e.last_name;}).join(', ')} — wish them a happy birthday! 🎉</div>
      </div>
    </div>}

    {offToday.length>0 && <div style={{background:'var(--navy-light)',border:'1px solid #c5c7e8',borderRadius:12,padding:'12px 16px',marginBottom:16}}>
      <div style={{fontWeight:600,fontSize:13,color:'var(--navy-dark)',marginBottom:6}}>Out of office today</div>
      <div style={{display:'flex',gap:7,flexWrap:'wrap'}}>
        {offToday.map(function(l){var emp=employees.find(function(e){return e.id===l.employee_id;});return emp?<span key={l.id} className="badge badge-amber">{emp.first_name} {emp.last_name}{l.half_day?' ('+l.half_day_period+')':''}</span>:null;})}
      </div>
    </div>}

    <div style={{display:'grid',gridTemplateColumns:'repeat(4,1fr)',gap:13,marginBottom:18}}>
      {[{l:'Total employees',v:employees.length,c:'var(--navy)'},{l:'Active staff',v:active,c:'#2e7d32'},{l:'Pending leave',v:pending,c:'var(--orange-dark)'},{l:'Contracts expiring',v:expiring,c:'var(--danger)'}].map(function(s){
        return <div key={s.l} className="stat-card"><div className="stat-value" style={{color:s.c}}>{s.v}</div><div className="stat-label">{s.l}</div></div>;
      })}
    </div>

    <CalendarWidget employees={employees} leaveRequests={leaveRequests}/>

    <div style={{display:'grid',gridTemplateColumns:'1fr 1fr',gap:16}}>
      <div className="card">
        <div className="card-header"><span style={{fontWeight:600,fontSize:14}}>Recent employees</span></div>
        {employees.length===0?<div className="empty">No employees yet</div>:
          employees.slice(-5).reverse().map(function(e){var c=avColor(e.first_name+' '+e.last_name);return <div key={e.id} style={{display:'flex',alignItems:'center',gap:10,padding:'10px 16px',borderBottom:'1px solid #f0f0fa'}}><div className="avatar" style={{background:c.bg,color:c.fg}}>{initials(e.first_name+' '+e.last_name)}</div><div style={{flex:1}}><div style={{fontWeight:500,fontSize:13}}>{e.first_name} {e.last_name}</div><div style={{fontSize:12,color:'var(--text-muted)'}}>{e.job_title||'—'}</div></div><span className={'badge '+(DEPT_BADGE[e.department]||'badge-gray')}>{e.department||'—'}</span></div>;})}
      </div>
      <div className="card">
        <div className="card-header"><span style={{fontWeight:600,fontSize:14}}>Leave requests</span></div>
        {leaveRequests.length===0?<div className="empty">No leave requests</div>:
          leaveRequests.slice(-6).reverse().map(function(l){var emp=employees.find(function(e){return e.id===l.employee_id;});var days=calcDays(l.start_date,l.end_date,l.half_day);return <div key={l.id} style={{display:'flex',alignItems:'center',gap:10,padding:'10px 16px',borderBottom:'1px solid #f0f0fa'}}><div style={{flex:1}}><div style={{fontWeight:500,fontSize:13}}>{emp?emp.first_name+' '+emp.last_name:'—'}</div><div style={{fontSize:12,color:'var(--text-muted)'}}>{l.type} · {days} day{days!==1?'s':''}</div></div><span className={'badge '+(l.status==='Approved'?'badge-green':l.status==='Declined'?'badge-red':'badge-amber')}>{l.status}</span></div>;})}
      </div>
    </div>

    {Object.keys(deptCounts).length>0 && <div className="card" style={{marginTop:16}}>
      <div className="card-header"><span style={{fontWeight:600,fontSize:14}}>Headcount by department</span></div>
      <div className="card-body">
        {Object.entries(deptCounts).map(function(entry){var dept=entry[0],count=entry[1];var pct=Math.round((count/employees.length)*100);return <div key={dept} style={{marginBottom:10}}><div style={{display:'flex',justifyContent:'space-between',fontSize:13,marginBottom:4}}><span style={{fontWeight:500}}>{dept}</span><span style={{color:'var(--text-muted)'}}>{count} · {pct}%</span></div><div className="progress-bar"><div className="progress-fill" style={{width:pct+'%',background:'var(--navy-mid)'}}/></div></div>;})}
      </div>
    </div>}
  </div>;
}

/* ── EMPLOYEES ─────────────────────────────────── */
var EMP0 = {id:null,first_name:'',last_name:'',email:'',phone:'',dob:'',gender:'',nationality:'',address:'',city:'',postcode:'',department:'',job_title:'',start_date:'',employment_type:'Full-time',salary:'',emergency_name:'',emergency_phone:'',emergency_relation:'',notes:'',status:'Active'};

function EmployeesPage(props) {
  var employees=props.employees,setEmployees=props.setEmployees,toast=props.toast,currentUser=props.currentUser;
  var admin = isAdmin(currentUser.email);
  var ms = useState(false); var modal = ms[0]; var setModal = ms[1];
  var vs = useState(null); var viewModal = vs[0]; var setViewModal = vs[1];
  var fs = useState(EMP0); var form = fs[0]; var setForm = fs[1];
  var es = useState(null); var editing = es[0]; var setEditing = es[1];
  var ss = useState(''); var search = ss[0]; var setSearch = ss[1];
  var ts = useState('personal'); var tab = ts[0]; var setTab = ts[1];
  var sav = useState(false); var saving = sav[0]; var setSaving = sav[1];

  function upd(k,v){setForm(function(p){var n=Object.assign({},p);n[k]=v;return n;});}

  async function save(){
    if(!form.first_name||!form.last_name||!form.email){alert('Name and email required.');return;}
    setSaving(true);
    var payload={first_name:form.first_name,last_name:form.last_name,email:form.email,phone:form.phone,dob:form.dob,gender:form.gender,nationality:form.nationality,address:form.address,city:form.city,postcode:form.postcode,department:form.department,job_title:form.job_title,start_date:form.start_date,employment_type:form.employment_type,salary:form.salary?Number(form.salary):null,emergency_name:form.emergency_name,emergency_phone:form.emergency_phone,emergency_relation:form.emergency_relation,notes:form.notes,status:form.status};
    if(editing){
      var r=await sb.from('employees').update(payload).eq('id',editing);
      if(!r.error){setEmployees(function(prev){return prev.map(function(e){return e.id===editing?Object.assign({},e,payload):e;});});toast('Employee updated');}
      else alert('Error: '+r.error.message);
    }else{
      var r2=await sb.from('employees').insert(payload).select().single();
      if(!r2.error){setEmployees(function(prev){return prev.concat([r2.data]);});toast('Employee added');}
      else alert('Error: '+r2.error.message);
    }
    setSaving(false);setModal(false);setForm(EMP0);setEditing(null);
  }

  async function del(id){
    if(!confirm('Remove this employee?'))return;
    var r=await sb.from('employees').delete().eq('id',id);
    if(!r.error){setEmployees(function(prev){return prev.filter(function(e){return e.id!==id;});});toast('Employee removed');}
    else alert('Error: '+r.error.message);
  }

  function edit(emp){setForm(Object.assign({},emp,{salary:emp.salary||''}));setEditing(emp.id);setModal(true);setTab('personal');}

  var filtered = employees.filter(function(e){return (e.first_name+' '+e.last_name+' '+e.email+' '+e.department+' '+e.job_title).toLowerCase().indexOf(search.toLowerCase())!==-1;});

  return <div>
    <div className="page-header">
      <div><div className="page-title">Employees</div><div className="page-sub">{employees.length} staff member{employees.length!==1?'s':''}</div></div>
      {admin&&<button className="btn btn-primary" onClick={function(){setForm(EMP0);setEditing(null);setModal(true);setTab('personal');}}>{I.plus} Add employee</button>}
    </div>
    <div className="card">
      <div className="card-header">
        <div className="search-bar" style={{flex:1,maxWidth:280}}>
          <div className="s-icon">{I.search}</div>
          <input value={search} onChange={function(e){setSearch(e.target.value);}} placeholder="Search employees…" style={{paddingLeft:34}}/>
        </div>
      </div>
      {filtered.length===0?<div className="empty">{search?'No employees match':'No employees yet!'}</div>:
        <table>
          <thead><tr><th>Name</th><th>Job title</th><th>Department</th><th>Email</th><th>Status</th><th>Actions</th></tr></thead>
          <tbody>{filtered.map(function(e){
            var c=avColor(e.first_name+' '+e.last_name);
            var now=new Date();
            var isBday=e.dob&&(function(){var p=e.dob.split('-');return parseInt(p[1])-1===now.getMonth()&&parseInt(p[2])===now.getDate();})();
            return <tr key={e.id}>
              <td><div style={{display:'flex',alignItems:'center',gap:9}}><div className="avatar" style={{background:c.bg,color:c.fg}}>{initials(e.first_name+' '+e.last_name)}</div><div><div style={{fontWeight:500,fontSize:13}}>{e.first_name} {e.last_name}{isBday?' 🎂':''}</div><div style={{fontSize:11,color:'var(--text-muted)'}}>{admin?e.phone||'—':'••••••••'}</div></div></div></td>
              <td style={{fontSize:13}}>{e.job_title||'—'}</td>
              <td><span className={'badge '+(DEPT_BADGE[e.department]||'badge-gray')}>{e.department||'—'}</span></td>
              <td style={{fontSize:12,color:'var(--text-muted)'}}>{e.email}</td>
              <td><span className={'badge '+(e.status==='Active'?'badge-green':e.status==='On Leave'?'badge-amber':'badge-red')}>{e.status}</span></td>
              <td><div style={{display:'flex',gap:4}}>
                <button className="btn btn-secondary btn-sm" onClick={function(){setViewModal(e);}}>{I.eye}</button>
                {admin&&<button className="btn btn-secondary btn-sm" onClick={function(){edit(e);}}>{I.edit}</button>}
                {admin&&<button className="btn btn-danger btn-sm" onClick={function(){del(e.id);}}>{I.trash}</button>}
              </div></td>
            </tr>;
          })}</tbody>
        </table>}
    </div>

    {viewModal&&<Modal title={viewModal.first_name+' '+viewModal.last_name} onClose={function(){setViewModal(null);}} footer={<><button className="btn btn-secondary" onClick={function(){setViewModal(null);}}>Close</button>{admin&&<button className="btn btn-primary" onClick={function(){edit(viewModal);setViewModal(null);}}>Edit</button>}</>}>
      <div className="grid2">
        {[['Full name',viewModal.first_name+' '+viewModal.last_name],['Email',viewModal.email],['Phone',admin?viewModal.phone||'—':'🔒 Restricted'],['Date of birth',admin?fmtDate(viewModal.dob):'🔒 Restricted'],['Gender',admin?viewModal.gender||'—':'🔒 Restricted'],['Nationality',admin?viewModal.nationality||'—':'🔒 Restricted'],['Job title',viewModal.job_title||'—'],['Department',viewModal.department||'—'],['Start date',fmtDate(viewModal.start_date)],['Employment type',viewModal.employment_type],['Salary',admin?(viewModal.salary?'$'+Number(viewModal.salary).toLocaleString():'—'):'🔒 Restricted'],['Status',viewModal.status]].map(function(kv){return <div key={kv[0]}><label>{kv[0]}</label><div style={{fontSize:14,fontWeight:500,padding:'3px 0',color:kv[1]&&kv[1].toString().indexOf('🔒')===0?'var(--text-muted)':''}}>{kv[1]||'—'}</div></div>;})}
      </div>
      {admin&&viewModal.address&&<><hr className="divider"/><label>Address</label><div style={{fontSize:14,paddingTop:3}}>{[viewModal.address,viewModal.city,viewModal.postcode].filter(Boolean).join(', ')}</div></>}
      {admin&&viewModal.emergency_name&&<><hr className="divider"/><div style={{fontWeight:600,marginBottom:8,fontSize:14}}>Emergency contact</div><div className="grid2">{[['Name',viewModal.emergency_name],['Phone',viewModal.emergency_phone],['Relation',viewModal.emergency_relation]].map(function(kv){return <div key={kv[0]}><label>{kv[0]}</label><div style={{fontSize:14,fontWeight:500,padding:'3px 0'}}>{kv[1]||'—'}</div></div>;})}</div></>}
      {!admin&&<><hr className="divider"/><div style={{background:'var(--navy-light)',borderRadius:8,padding:'10px 13px',fontSize:13,color:'var(--text-muted)'}}>🔒 Personal details are only visible to HR administrators.</div></>}
    </Modal>}

    {modal&&<Modal title={editing?'Edit employee':'Add employee'} onClose={function(){setModal(false);setEditing(null);setForm(EMP0);}} footer={<><button className="btn btn-secondary" onClick={function(){setModal(false);setEditing(null);}}>Cancel</button><button className="btn btn-orange" onClick={save} disabled={saving}>{saving?'Saving…':'Save employee'}</button></>}>
      <div className="tab-bar" style={{marginBottom:16}}>{['personal','job','contact','emergency'].map(function(t){return <button key={t} className={'tab'+(tab===t?' active':'')} onClick={function(){setTab(t);}}>{t==='personal'?'Personal':t==='job'?'Job & Pay':t==='contact'?'Contact':'Emergency'}</button>;})}</div>
      {tab==='personal'&&<div>
        <div className="grid2"><div className="field"><label>First name *</label><input value={form.first_name} onChange={function(e){upd('first_name',e.target.value);}} placeholder="Jane"/></div><div className="field"><label>Last name *</label><input value={form.last_name} onChange={function(e){upd('last_name',e.target.value);}} placeholder="Smith"/></div></div>
        <div className="grid2"><div className="field"><label>Date of birth</label><input type="date" value={form.dob} onChange={function(e){upd('dob',e.target.value);}}/></div><div className="field"><label>Gender</label><select value={form.gender} onChange={function(e){upd('gender',e.target.value);}}><option value="">Select</option><option>Male</option><option>Female</option><option>Non-binary</option><option>Prefer not to say</option></select></div></div>
        <div className="field"><label>Nationality</label><input value={form.nationality} onChange={function(e){upd('nationality',e.target.value);}} placeholder="e.g. British"/></div>
        <div className="field"><label>Status</label><select value={form.status} onChange={function(e){upd('status',e.target.value);}}><option>Active</option><option>On Leave</option><option>Resigned</option><option>Terminated</option></select></div>
        <div className="field"><label>Notes</label><textarea value={form.notes} onChange={function(e){upd('notes',e.target.value);}} placeholder="Any notes…"/></div>
      </div>}
      {tab==='job'&&<div>
        <div className="grid2"><div className="field"><label>Job title</label><input value={form.job_title} onChange={function(e){upd('job_title',e.target.value);}} placeholder="e.g. Virtual Assistant"/></div><div className="field"><label>Department</label><select value={form.department} onChange={function(e){upd('department',e.target.value);}}><option value="">Select</option><option>Operations</option><option>Finance</option><option>HR</option><option>Admin</option><option>Sales</option><option>Marketing</option><option>IT</option><option>Management</option></select></div></div>
        <div className="grid2"><div className="field"><label>Start date</label><input type="date" value={form.start_date} onChange={function(e){upd('start_date',e.target.value);}}/></div><div className="field"><label>Employment type</label><select value={form.employment_type} onChange={function(e){upd('employment_type',e.target.value);}}><option>Full-time</option><option>Part-time</option><option>Contract</option><option>Freelance</option><option>Intern</option></select></div></div>
        <div className="field"><label>Annual salary (USD $)</label><input type="number" value={form.salary} onChange={function(e){upd('salary',e.target.value);}} placeholder="50000"/></div>
      </div>}
      {tab==='contact'&&<div>
        <div className="field"><label>Email address *</label><input type="email" value={form.email} onChange={function(e){upd('email',e.target.value);}} placeholder="jane@remote-pa.co"/></div>
        <div className="field"><label>Phone number</label><input value={form.phone} onChange={function(e){upd('phone',e.target.value);}} placeholder="+44 7700 000000"/></div>
        <div className="field"><label>Address</label><input value={form.address} onChange={function(e){upd('address',e.target.value);}} placeholder="123 High Street"/></div>
        <div className="grid2"><div className="field"><label>City</label><input value={form.city} onChange={function(e){upd('city',e.target.value);}} placeholder="London"/></div><div className="field"><label>Postcode</label><input value={form.postcode} onChange={function(e){upd('postcode',e.target.value);}} placeholder="SW1A 1AA"/></div></div>
      </div>}
      {tab==='emergency'&&<div>
        <p style={{fontSize:13,color:'var(--text-muted)',marginBottom:12}}>Emergency contact details.</p>
        <div className="field"><label>Contact name</label><input value={form.emergency_name} onChange={function(e){upd('emergency_name',e.target.value);}} placeholder="John Smith"/></div>
        <div className="grid2"><div className="field"><label>Phone</label><input value={form.emergency_phone} onChange={function(e){upd('emergency_phone',e.target.value);}} placeholder="+44 7700 000000"/></div><div className="field"><label>Relationship</label><input value={form.emergency_relation} onChange={function(e){upd('emergency_relation',e.target.value);}} placeholder="Spouse, Parent…"/></div></div>
      </div>}
    </Modal>}
  </div>;
}

/* ── CONTRACTS (document upload only) ─────────── */
function ContractsPage(props) {
  var employees=props.employees,contracts=props.contracts,setContracts=props.setContracts,toast=props.toast,currentUser=props.currentUser;
  var admin = isAdmin(currentUser.email);
  var ms=useState(false);var modal=ms[0];var setModal=ms[1];
  var dv=useState(null);var docView=dv[0];var setDocView=dv[1];
  var fs=useState({employee_id:'',doc_name:'',doc_data:'',notes:''});var form=fs[0];var setForm=fs[1];
  var sv=useState(false);var saving=sv[0];var setSaving=sv[1];
  var ss=useState('');var search=ss[0];var setSearch=ss[1];
  var fileRef = useRef();

  function upd(k,v){setForm(function(p){var n=Object.assign({},p);n[k]=v;return n;});}
  function handleFile(file){if(!file)return;var r=new FileReader();r.onload=function(ev){setForm(function(p){var n=Object.assign({},p);n.doc_name=file.name;n.doc_data=ev.target.result;return n;});};r.readAsDataURL(file);}

  async function save(){
    if(!form.employee_id){alert('Please select an employee.');return;}
    if(!form.doc_data){alert('Please upload a document.');return;}
    setSaving(true);
    var payload={employee_id:form.employee_id,doc_name:form.doc_name,doc_data:form.doc_data,notes:form.notes,uploaded_at:todayStr(),type:'Contract',status:'Active'};
    var r=await sb.from('contracts').insert(payload).select().single();
    if(!r.error){setContracts(function(prev){return prev.concat([r.data]);});toast('Contract uploaded');}
    else alert('Error: '+r.error.message);
    setSaving(false);setModal(false);setForm({employee_id:'',doc_name:'',doc_data:'',notes:''});
  }

  async function del(id){
    if(!confirm('Remove this contract?'))return;
    var r=await sb.from('contracts').delete().eq('id',id);
    if(!r.error){setContracts(function(prev){return prev.filter(function(c){return c.id!==id;});});toast('Removed');}
    else alert('Error: '+r.error.message);
  }

  var filtered=contracts.filter(function(c){var emp=employees.find(function(e){return e.id===c.employee_id;});if(!emp)return false;return (emp.first_name+' '+emp.last_name).toLowerCase().indexOf(search.toLowerCase())!==-1;});

  return <div>
    <div className="page-header">
      <div><div className="page-title">Contracts</div><div className="page-sub">Upload and store employee contract documents</div></div>
      {admin&&<button className="btn btn-primary" onClick={function(){setForm({employee_id:'',doc_name:'',doc_data:'',notes:''});setModal(true);}}>{I.plus} Upload contract</button>}
    </div>
    <div className="card">
      <div className="card-header"><div className="search-bar" style={{flex:1,maxWidth:280}}><div className="s-icon">{I.search}</div><input value={search} onChange={function(e){setSearch(e.target.value);}} placeholder="Search…" style={{paddingLeft:34}}/></div></div>
      {filtered.length===0?<div className="empty">No contract documents uploaded yet.</div>:
        <table>
          <thead><tr><th>Employee</th><th>Document</th><th>Notes</th><th>Uploaded</th><th>Actions</th></tr></thead>
          <tbody>{filtered.map(function(c){var emp=employees.find(function(e){return e.id===c.employee_id;});return <tr key={c.id}>
            <td style={{fontWeight:500,fontSize:13}}>{emp?emp.first_name+' '+emp.last_name:'—'}</td>
            <td><button className="btn btn-sm btn-secondary" style={{fontSize:12}} onClick={function(){setDocView(c);}}>📎 {c.doc_name||'View'}</button></td>
            <td style={{fontSize:13,color:'var(--text-muted)'}}>{c.notes||'—'}</td>
            <td style={{fontSize:12,color:'var(--text-muted)'}}>{fmtDate(c.uploaded_at)}</td>
            <td>{admin&&<button className="btn btn-danger btn-sm" onClick={function(){del(c.id);}}>{I.trash}</button>}</td>
          </tr>;})}
          </tbody>
        </table>}
    </div>
    {docView&&<Modal title={'Contract: '+(docView.doc_name||'Document')} onClose={function(){setDocView(null);}} footer={<button className="btn btn-secondary" onClick={function(){setDocView(null);}}>Close</button>}>
      <div style={{textAlign:'center'}}>{docView.doc_data&&docView.doc_data.indexOf('data:image')===0?<img src={docView.doc_data} style={{maxWidth:'100%',borderRadius:8}} alt="contract"/>:<div style={{padding:24,background:'var(--bg)',borderRadius:8,fontSize:14,color:'var(--text-muted)',textAlign:'center'}}>📎 <strong>{docView.doc_name}</strong><br/><a href={docView.doc_data} download={docView.doc_name} className="btn btn-primary" style={{marginTop:14,display:'inline-flex'}}>Download</a></div>}</div>
    </Modal>}
    {modal&&<Modal title="Upload contract" onClose={function(){setModal(false);}} footer={<><button className="btn btn-secondary" onClick={function(){setModal(false);}}>Cancel</button><button className="btn btn-orange" onClick={save} disabled={saving}>{saving?'Uploading…':'Upload'}</button></>}>
      <div className="field"><label>Employee *</label><select value={form.employee_id} onChange={function(e){upd('employee_id',e.target.value);}}><option value="">Select employee…</option>{employees.map(function(e){return <option key={e.id} value={e.id}>{e.first_name} {e.last_name}</option>;})}</select></div>
      <div className="field"><label>Contract document *</label>
        <div className={'upload-zone'+(form.doc_name?' has-file':'')} onClick={function(){fileRef.current.click();}} onDragOver={function(e){e.preventDefault();}} onDrop={function(e){e.preventDefault();handleFile(e.dataTransfer.files[0]);}}>
          <input ref={fileRef} type="file" style={{display:'none'}} accept="image/*,.pdf,.doc,.docx" onChange={function(e){handleFile(e.target.files[0]);}}/>
          {form.doc_name?<div style={{color:'var(--navy)',fontWeight:500}}>📎 {form.doc_name}</div>:<div>{I.upload}<div style={{fontSize:13,color:'var(--text-muted)',marginTop:8}}>Click or drag & drop<br/><small>PDF, Word, or image</small></div></div>}
        </div>
      </div>
      <div className="field"><label>Notes (optional)</label><textarea value={form.notes} onChange={function(e){upd('notes',e.target.value);}} placeholder="e.g. Signed contract, probation agreement…" style={{minHeight:60}}/></div>
    </Modal>}
  </div>;
}

/* ── LEAVE ─────────────────────────────────────── */
var LEV0 = {id:null,employee_id:'',type:'Paid Personal Leave',start_date:'',end_date:'',half_day:false,half_day_period:'Morning',reason:'',status:'Pending',doc_name:'',doc_data:''};

function LeavePage(props) {
  var employees=props.employees,leaveRequests=props.leaveRequests,setLeaveRequests=props.setLeaveRequests,toast=props.toast,currentUser=props.currentUser;
  var admin = isAdmin(currentUser.email);
  var ms=useState(false);var modal=ms[0];var setModal=ms[1];
  var fs=useState(LEV0);var form=fs[0];var setForm=fs[1];
  var ed=useState(null);var editing=ed[0];var setEditing=ed[1];
  var fl=useState('All');var filter=fl[0];var setFilter=fl[1];
  var dv=useState(null);var docView=dv[0];var setDocView=dv[1];
  var sv=useState(false);var saving=sv[0];var setSaving=sv[1];
  var fileRef=useRef();

  function upd(k,v){setForm(function(p){var n=Object.assign({},p);n[k]=v;return n;});}

  function getStartDate(id){var emp=employees.find(function(e){return e.id===id;});return emp?emp.start_date:null;}

  function getUsedPaid(id,exId){
    var yr=new Date().getFullYear();
    return leaveRequests.filter(function(l){return l.employee_id===id&&l.status==='Approved'&&l.id!==exId&&l.type.toLowerCase().indexOf('unpaid')===-1&&l.start_date&&l.start_date.indexOf(yr)===0;}).reduce(function(s,l){return parseFloat((s+calcDays(l.start_date,l.end_date,l.half_day)).toFixed(1));},0);
  }

  function handleFile(file){if(!file)return;var r=new FileReader();r.onload=function(ev){setForm(function(p){var n=Object.assign({},p);n.doc_name=file.name;n.doc_data=ev.target.result;return n;});};r.readAsDataURL(file);}

  async function save(){
    if(!form.employee_id){alert('Please select an employee.');return;}
    if(!form.start_date||!form.end_date){alert('Dates required.');return;}
    setSaving(true);
    var payload={employee_id:form.employee_id,type:form.type,start_date:form.start_date,end_date:form.end_date,half_day:form.half_day||false,half_day_period:form.half_day?form.half_day_period:null,reason:form.reason,status:form.status,doc_name:form.doc_name||null,doc_data:form.doc_data||null};
    if(editing){
      var r=await sb.from('leave_requests').update(payload).eq('id',editing);
      if(!r.error){setLeaveRequests(function(prev){return prev.map(function(l){return l.id===editing?Object.assign({},l,payload):l;});});toast('Updated');}
      else alert('Error: '+r.error.message);
    }else{
      var r2=await sb.from('leave_requests').insert(payload).select().single();
      if(!r2.error){
        setLeaveRequests(function(prev){return prev.concat([r2.data]);});
        var emp=employees.find(function(e){return e.id===form.employee_id;});
        var empName=emp?emp.first_name+' '+emp.last_name:'Unknown';
        var daysText=form.half_day?'Half day ('+form.half_day_period+')':calcDays(form.start_date,form.end_date,false)+' working day(s)';
        var msg='New leave request\n\nEmployee: '+empName+'\nType: '+form.type+'\nFrom: '+fmtDate(form.start_date)+'\nTo: '+fmtDate(form.end_date)+'\nDuration: '+daysText+'\nReason: '+(form.reason||'Not provided')+'\n\nLog in to approve: https://remotepa-hr.netlify.app';
        sendMail(LEAVE_HR_1,'Leave Request: '+empName,msg);
        sendMail(LEAVE_HR_2,'Leave Request: '+empName,msg);
        toast('Leave submitted — Charne & Xander notified 📧');
      }else alert('Error: '+r2.error.message);
    }
    setSaving(false);setModal(false);setForm(LEV0);setEditing(null);
  }

  async function del(id){
    if(!confirm('Delete this request?'))return;
    var r=await sb.from('leave_requests').delete().eq('id',id);
    if(!r.error){setLeaveRequests(function(prev){return prev.filter(function(l){return l.id!==id;});});toast('Deleted');}
    else alert('Error: '+r.error.message);
  }

  function edit(l){setForm(Object.assign({},l,{doc_name:l.doc_name||'',doc_data:l.doc_data||'',half_day:l.half_day||false,half_day_period:l.half_day_period||'Morning'}));setEditing(l.id);setModal(true);}

  async function updateStatus(id,status){
    var r=await sb.from('leave_requests').update({status:status}).eq('id',id);
    if(!r.error){
      setLeaveRequests(function(prev){return prev.map(function(l){return l.id===id?Object.assign({},l,{status:status}):l;});});
      var req=leaveRequests.find(function(l){return l.id===id;});
      if(req){
        var emp=employees.find(function(e){return e.id===req.employee_id;});
        if(emp&&emp.email){
          var dText=req.half_day?'Half day ('+req.half_day_period+')':calcDays(req.start_date,req.end_date,false)+' working day(s)';
          var approved=status==='Approved';
          var emsg='Hi '+emp.first_name+',\n\nYour leave request has been '+(approved?'APPROVED':'DECLINED')+'.\n\nType: '+req.type+'\nFrom: '+fmtDate(req.start_date)+'\nTo: '+fmtDate(req.end_date)+'\nDuration: '+dText+'\n\n'+(approved?'Enjoy your time off!':'Please contact HR if you have questions.')+'\n\nKind regards,\nRemote PA HR';
          sendMail(emp.email,(approved?'Leave Approved':'Leave Declined')+': '+req.type,emsg);
        }
      }
      toast(status==='Approved'?'Leave approved — employee notified 📧':'Leave declined — employee notified 📧');
    }else alert('Error: '+r.error.message);
  }

  var myRequests=admin?leaveRequests:leaveRequests.filter(function(l){var emp=employees.find(function(e){return e.email===currentUser.email;});return emp?l.employee_id===emp.id:false;});
  var filtered=myRequests.filter(function(l){return filter==='All'||l.status===filter;});
  var days=calcDays(form.start_date,form.end_date,form.half_day);
  var allBalances=employees.map(function(e){return Object.assign({emp:e},calcBalance(getStartDate(e.id),getUsedPaid(e.id,null)));});
  var balances=admin?allBalances:allBalances.filter(function(b){return b.emp.email===currentUser.email;});

  return <div>
    <div className="page-header">
      <div><div className="page-title">Leave management</div><div className="page-sub">10 days/year · monthly accrual · UK bank holidays excluded</div></div>
      <button className="btn btn-primary" onClick={function(){var myEmp=employees.find(function(e){return e.email===currentUser.email;});setForm(Object.assign({},LEV0,{employee_id:admin?'':myEmp?myEmp.id:''}));setEditing(null);setModal(true);}}>{I.plus} Request leave</button>
    </div>

    <div className="card" style={{marginBottom:16}}>
      <div className="card-header"><span style={{fontWeight:600,fontSize:14}}>Leave balances — {new Date().getFullYear()}</span><span style={{fontSize:12,color:'var(--text-muted)'}}>Paid leave · bank holidays excluded</span></div>
      {employees.length===0?<div className="empty">No employees yet</div>:
        <table>
          <thead><tr><th>Employee</th><th>Accrued this year</th><th>Used</th><th>Remaining</th><th>Usage</th></tr></thead>
          <tbody>{balances.map(function(b){var pct=b.total>0?Math.round((b.used/b.total)*100):0;var c=avColor(b.emp.first_name+' '+b.emp.last_name);return <tr key={b.emp.id}>
            <td><div style={{display:'flex',alignItems:'center',gap:9}}><div className="avatar" style={{background:c.bg,color:c.fg,width:28,height:28,fontSize:10}}>{initials(b.emp.first_name+' '+b.emp.last_name)}</div><span style={{fontWeight:500,fontSize:13}}>{b.emp.first_name} {b.emp.last_name}</span></div></td>
            <td style={{fontSize:13}}>{b.total} days</td>
            <td style={{fontSize:13}}>{b.used} days</td>
            <td><span style={{fontWeight:600,color:b.remaining<2?'var(--danger)':'var(--navy)',fontSize:13}}>{b.remaining} days</span></td>
            <td style={{width:140}}><div className="progress-bar"><div className="progress-fill" style={{width:Math.min(pct,100)+'%',background:pct>90?'var(--danger)':'var(--navy-mid)'}}/></div><span style={{fontSize:11,color:'var(--text-muted)'}}>{pct}%</span></td>
          </tr>;})}
          </tbody>
        </table>}
    </div>

    <div className="card">
      <div className="card-header">
        <span style={{fontWeight:600,fontSize:14}}>{admin?'All requests':'My requests'}</span>
        <div style={{display:'flex',gap:4}}>{['All','Pending','Approved','Declined'].map(function(s){return <button key={s} onClick={function(){setFilter(s);}} className="btn btn-sm" style={{background:filter===s?'var(--navy)':'',color:filter===s?'#fff':'',border:filter===s?'none':'1px solid var(--border)',fontSize:12}}>{s}{s!=='All'?<span style={{marginLeft:2,opacity:.8}}>({myRequests.filter(function(l){return l.status===s;}).length})</span>:''}</button>;})}</div>
      </div>
      {filtered.length===0?<div className="empty">No leave requests</div>:
        <table>
          <thead><tr><th>Employee</th><th>Type</th><th>From</th><th>To</th><th>Days</th><th>Doc</th><th>Status</th><th>Actions</th></tr></thead>
          <tbody>{filtered.map(function(l){
            var emp=employees.find(function(e){return e.id===l.employee_id;});
            var d=calcDays(l.start_date,l.end_date,l.half_day);
            return <tr key={l.id}>
              <td style={{fontWeight:500,fontSize:13}}>{emp?emp.first_name+' '+emp.last_name:'—'}</td>
              <td><span className="tag">{l.type}</span>{l.half_day&&<span className="badge badge-teal" style={{marginLeft:4,fontSize:10}}>½ {l.half_day_period}</span>}</td>
              <td style={{fontSize:12,color:'var(--text-muted)'}}>{fmtDate(l.start_date)}</td>
              <td style={{fontSize:12,color:'var(--text-muted)'}}>{fmtDate(l.end_date)}</td>
              <td style={{fontWeight:600,color:'var(--navy)',fontSize:13}}>{d}</td>
              <td>{l.doc_name?<button className="btn btn-sm btn-secondary" style={{fontSize:11,padding:'3px 7px'}} onClick={function(){setDocView(l);}}>📎</button>:<span style={{fontSize:11,color:'var(--text-muted)'}}>—</span>}</td>
              <td><span className={'badge '+(l.status==='Approved'?'badge-green':l.status==='Declined'?'badge-red':'badge-amber')}>{l.status}</span></td>
              <td><div style={{display:'flex',gap:3}}>
                {admin&&l.status==='Pending'&&<><button className="btn btn-sm" style={{background:'#e8f5e9',color:'#2e7d32',border:'1px solid #c8e6c9',padding:'4px 7px'}} onClick={function(){updateStatus(l.id,'Approved');}}>{I.chk}</button><button className="btn btn-danger btn-sm" style={{padding:'4px 7px'}} onClick={function(){updateStatus(l.id,'Declined');}}>{I.x}</button></>}
                {(admin||l.status==='Pending')&&<button className="btn btn-secondary btn-sm" style={{padding:'4px 7px'}} onClick={function(){edit(l);}}>{I.edit}</button>}
                {admin&&<button className="btn btn-danger btn-sm" style={{padding:'4px 7px'}} onClick={function(){del(l.id);}}>{I.trash}</button>}
              </div></td>
            </tr>;
          })}
          </tbody>
        </table>}
    </div>

    {docView&&<Modal title={'Document: '+(docView.doc_name||'')} onClose={function(){setDocView(null);}} footer={<button className="btn btn-secondary" onClick={function(){setDocView(null);}}>Close</button>}>
      <div style={{textAlign:'center'}}>{docView.doc_data&&docView.doc_data.indexOf('data:image')===0?<img src={docView.doc_data} style={{maxWidth:'100%',borderRadius:8}} alt="doc"/>:<div style={{padding:20,background:'var(--bg)',borderRadius:8,fontSize:14,color:'var(--text-muted)'}}>📎 <strong>{docView.doc_name}</strong><br/><a href={docView.doc_data} download={docView.doc_name} className="btn btn-primary" style={{marginTop:12,display:'inline-flex'}}>Download</a></div>}</div>
    </Modal>}

    {modal&&<Modal title={editing?'Edit leave request':'New leave request'} onClose={function(){setModal(false);setEditing(null);setForm(LEV0);}} footer={<><button className="btn btn-secondary" onClick={function(){setModal(false);setEditing(null);}}>Cancel</button><button className="btn btn-orange" onClick={save} disabled={saving}>{saving?'Submitting…':'Submit request'}</button></>}>
      {admin?<div className="field"><label>Employee *</label><select value={form.employee_id} onChange={function(e){upd('employee_id',e.target.value);}}><option value="">Select employee…</option>{employees.map(function(e){return <option key={e.id} value={e.id}>{e.first_name} {e.last_name}</option>;})}</select></div>
        :<div className="field"><label>Requesting for</label><div style={{padding:'9px 12px',background:'var(--bg)',borderRadius:'var(--radius)',fontSize:14,fontWeight:500,border:'1.5px solid var(--border)'}}>{currentUser.name}</div></div>}
      {form.employee_id&&(function(){var sd=getStartDate(form.employee_id);var used=getUsedPaid(form.employee_id,editing);var bal=calcBalance(sd,used);return <div className="info-box"><strong>Leave balance:</strong> {bal.remaining} days remaining of {bal.total} accrued · {bal.used} used this year</div>;})()}
      <div className="field"><label>Leave type</label><select value={form.type} onChange={function(e){upd('type',e.target.value);}}>{LEAVE_TYPES.map(function(t){return <option key={t}>{t}</option>;})}</select></div>
      <div style={{display:'flex',alignItems:'center',gap:12,background:'var(--navy-light)',borderRadius:8,padding:'11px 14px',marginBottom:13}}>
        <input type="checkbox" id="hd" checked={form.half_day} onChange={function(e){setForm(function(p){var n=Object.assign({},p);n.half_day=e.target.checked;if(e.target.checked)n.end_date=n.start_date;return n;});}} style={{width:16,height:16,accentColor:'var(--navy)',flexShrink:0,cursor:'pointer'}}/>
        <label htmlFor="hd" style={{color:'var(--navy-dark)',cursor:'pointer',marginBottom:0,fontWeight:600}}>Half day only</label>
        {form.half_day&&<select value={form.half_day_period} onChange={function(e){upd('half_day_period',e.target.value);}} style={{width:'auto',marginLeft:8}}><option>Morning</option><option>Afternoon</option></select>}
      </div>
      <div className="grid2">
        <div className="field"><label>Start date *</label><input type="date" value={form.start_date} onChange={function(e){setForm(function(p){var n=Object.assign({},p);n.start_date=e.target.value;if(p.half_day)n.end_date=e.target.value;return n;});}}/></div>
        {!form.half_day&&<div className="field"><label>End date *</label><input type="date" value={form.end_date} onChange={function(e){upd('end_date',e.target.value);}}/></div>}
      </div>
      {form.start_date&&days>0&&<div className="warn-box">📅 {form.half_day?'Half day ('+form.half_day_period+') — 0.5 days':days+' working day'+(days!==1?'s':'')+' — weekends & bank holidays excluded'}</div>}
      <div className="field"><label>Status</label><select value={form.status} onChange={function(e){upd('status',e.target.value);}}><option>Pending</option><option>Approved</option><option>Declined</option></select></div>
      <div className="field"><label>Reason (optional)</label><textarea value={form.reason} onChange={function(e){upd('reason',e.target.value);}} placeholder="Reason for leave…" style={{minHeight:56}}/></div>
      {form.type.toLowerCase().indexOf('sick')!==-1&&<div className="field">
        <label>Supporting document (sick leave)</label>
        <div className={'upload-zone'+(form.doc_name?' has-file':'')} onClick={function(){fileRef.current.click();}} onDragOver={function(e){e.preventDefault();}} onDrop={function(e){e.preventDefault();handleFile(e.dataTransfer.files[0]);}}>
          <input ref={fileRef} type="file" style={{display:'none'}} accept="image/*,.pdf,.doc,.docx" onChange={function(e){handleFile(e.target.files[0]);}}/>
          {form.doc_name?<div style={{color:'var(--navy)',fontWeight:500}}>📎 {form.doc_name} <button onClick={function(e){e.stopPropagation();setForm(function(p){var n=Object.assign({},p);n.doc_name='';n.doc_data='';return n;});}} style={{background:'none',border:'none',color:'var(--danger)',cursor:'pointer',marginLeft:8}}>✕</button></div>:<div>{I.upload}<div style={{fontSize:13,color:'var(--text-muted)',marginTop:6}}>Click or drag & drop<br/><small>PDF, image or Word</small></div></div>}
        </div>
      </div>}
      <div className="info-box" style={{marginTop:8,marginBottom:0}}>📧 Request will be sent to Charne and Xander for approval.</div>
    </Modal>}
  </div>;
}

/* ── ANNOUNCEMENTS ─────────────────────────────── */
var ANN0 = {id:null,title:'',body:'',priority:'Normal',send_email:true};

function AnnouncementsPage(props) {
  var employees=props.employees,announcements=props.announcements,setAnnouncements=props.setAnnouncements,toast=props.toast,currentUser=props.currentUser;
  var admin=isAdmin(currentUser.email);
  var ms=useState(false);var modal=ms[0];var setModal=ms[1];
  var fs=useState(ANN0);var form=fs[0];var setForm=fs[1];
  var ed=useState(null);var editing=ed[0];var setEditing=ed[1];
  var sv=useState(false);var saving=sv[0];var setSaving=sv[1];

  function upd(k,v){setForm(function(p){var n=Object.assign({},p);n[k]=v;return n;});}

  async function save(){
    if(!form.title.trim()||!form.body.trim()){alert('Title and message required.');return;}
    setSaving(true);
    var payload={title:form.title,body:form.body,priority:form.priority,send_email:form.send_email,date:todayStr()};
    if(editing){
      var r=await sb.from('announcements').update(payload).eq('id',editing);
      if(!r.error){setAnnouncements(function(prev){return prev.map(function(a){return a.id===editing?Object.assign({},a,payload):a;});});toast('Updated');}
      else alert('Error: '+r.error.message);
    }else{
      var r2=await sb.from('announcements').insert(payload).select().single();
      if(!r2.error){
        setAnnouncements(function(prev){return [r2.data].concat(prev);});
        if(form.send_email){
          employees.filter(function(e){return e.email;}).forEach(function(e){sendMail(e.email,form.title,form.body+'\n\n—\nRemote PA HR Team');});
          toast('Posted & emailed to '+employees.filter(function(e){return e.email;}).length+' employee(s) 📧');
        }else toast('Announcement posted');
      }else alert('Error: '+r2.error.message);
    }
    setSaving(false);setModal(false);setForm(ANN0);setEditing(null);
  }

  async function del(id){
    if(!confirm('Delete?'))return;
    var r=await sb.from('announcements').delete().eq('id',id);
    if(!r.error){setAnnouncements(function(prev){return prev.filter(function(a){return a.id!==id;});});toast('Deleted');}
    else alert('Error: '+r.error.message);
  }

  var priBadge={Normal:'badge-navy',Important:'badge-orange',Urgent:'badge-red'};

  return <div>
    <div className="page-header">
      <div><div className="page-title">Announcements</div><div className="page-sub">Post updates to the Remote PA team</div></div>
      {admin&&<button className="btn btn-primary" onClick={function(){setForm(ANN0);setEditing(null);setModal(true);}}>{I.plus} Post announcement</button>}
    </div>
    {announcements.length===0?<div className="card"><div className="empty">No announcements yet.</div></div>:
      announcements.map(function(a){return <div key={a.id} className="ann-card">
        <div style={{display:'flex',alignItems:'flex-start',justifyContent:'space-between',gap:12}}>
          <div style={{flex:1}}>
            <div style={{fontWeight:600,fontSize:15,marginBottom:4}}>{a.title}</div>
            <div style={{fontSize:12,color:'var(--text-muted)',marginBottom:8}}>{fmtDate(a.date)}{a.send_email&&<span style={{marginLeft:8,color:'var(--teal)'}}>📧 emailed to team</span>}</div>
            <div style={{fontSize:14,color:'var(--text-muted)',lineHeight:1.6,whiteSpace:'pre-wrap'}}>{a.body}</div>
          </div>
          <div style={{display:'flex',flexDirection:'column',alignItems:'flex-end',gap:8,flexShrink:0}}>
            <span className={'badge '+(priBadge[a.priority]||'badge-navy')}>{a.priority}</span>
            {admin&&<div style={{display:'flex',gap:4}}><button className="btn btn-secondary btn-sm" onClick={function(){setForm(Object.assign({},a));setEditing(a.id);setModal(true);}}>{I.edit}</button><button className="btn btn-danger btn-sm" onClick={function(){del(a.id);}}>{I.trash}</button></div>}
          </div>
        </div>
      </div>;})}
    {modal&&<Modal title={editing?'Edit announcement':'Post announcement'} onClose={function(){setModal(false);setEditing(null);setForm(ANN0);}} footer={<><button className="btn btn-secondary" onClick={function(){setModal(false);setEditing(null);}}>Cancel</button><button className="btn btn-orange" onClick={save} disabled={saving}>{saving?'Posting…':(editing?'Save':'Post announcement')}</button></>}>
      <div className="field"><label>Title *</label><input value={form.title} onChange={function(e){upd('title',e.target.value);}} placeholder="e.g. Office closure notice"/></div>
      <div className="field"><label>Priority</label><select value={form.priority} onChange={function(e){upd('priority',e.target.value);}}><option>Normal</option><option>Important</option><option>Urgent</option></select></div>
      <div className="field"><label>Message *</label><textarea value={form.body} onChange={function(e){upd('body',e.target.value);}} placeholder="Write your announcement…" style={{minHeight:120}}/></div>
      {!editing&&<div style={{display:'flex',alignItems:'flex-start',gap:10,background:'var(--teal-light)',borderRadius:8,padding:'12px 14px'}}>
        <input type="checkbox" id="se" checked={form.send_email} onChange={function(e){upd('send_email',e.target.checked);}} style={{width:16,height:16,marginTop:1,accentColor:'var(--navy)',flexShrink:0,cursor:'pointer'}}/>
        <label htmlFor="se" style={{color:'#006b94',cursor:'pointer',marginBottom:0}}><strong>Email to all employees</strong><br/><span style={{fontSize:12,fontWeight:400}}>Sends to {employees.filter(function(e){return e.email;}).length} employee(s) on record</span></label>
      </div>}
    </Modal>}
  </div>;
}

/* ── APP ───────────────────────────────────────── */
function App() {
  var us=useState(function(){try{var u=localStorage.getItem('rpa_user');return u?JSON.parse(u):null;}catch(e){return null;}});
  var user=us[0];var setUser=us[1];
  var ps=useState('dashboard');var page=ps[0];var setPage=ps[1];
  var es=useState([]);var employees=es[0];var setEmployees=es[1];
  var cs=useState([]);var contracts=cs[0];var setContracts=cs[1];
  var ls=useState([]);var leaveRequests=ls[0];var setLeaveRequests=ls[1];
  var as=useState([]);var announcements=as[0];var setAnnouncements=as[1];
  var ld=useState(false);var loading=ld[0];var setLoading=ld[1];
  var rf=useState(false);var refreshing=rf[0];var setRefreshing=rf[1];
  var ts=useState(null);var toastMsg=ts[0];var setToastMsg=ts[1];
  var toast=function(msg){setToastMsg(msg);};

  async function loadAll(quiet) {
    if(quiet)setRefreshing(true);else setLoading(true);
    try{
      var results=await Promise.all([
        sb.from('employees').select('*').order('created_at',{ascending:false}),
        sb.from('contracts').select('*').order('created_at',{ascending:false}),
        sb.from('leave_requests').select('*').order('created_at',{ascending:false}),
        sb.from('announcements').select('*').order('created_at',{ascending:false}),
      ]);
      if(results[0].data)setEmployees(results[0].data);
      if(results[1].data)setContracts(results[1].data);
      if(results[2].data)setLeaveRequests(results[2].data);
      if(results[3].data)setAnnouncements(results[3].data);
    }catch(e){console.error(e);}
    setLoading(false);setRefreshing(false);
  }

  useEffect(function(){if(user)loadAll(false);},[]);

  function handleLogin(u){setUser(u);loadAll(false);}
  function handleLogout(){localStorage.removeItem('rpa_user');setUser(null);setEmployees([]);setContracts([]);setLeaveRequests([]);setAnnouncements([]);}

  if(!user)return <AuthScreen onLogin={handleLogin}/>;
  if(loading)return <Spinner/>;

  var pendingCount=leaveRequests.filter(function(l){return l.status==='Pending';}).length;
  var urgentAnn=announcements.filter(function(a){return a.priority==='Urgent';}).length;

  var nav=[
    {id:'dashboard',label:'Dashboard',icon:I.dash},
    {id:'employees',label:'Employees',icon:I.emp},
    {id:'contracts',label:'Contracts',icon:I.doc},
    {id:'leave',label:'Leave',icon:I.cal},
    {id:'announcements',label:'Announcements',icon:I.ann},
  ];

  return <div style={{display:'flex'}}>
    <aside className="sidebar">
      <div className="s-logo">
        <div><span className="lr">Remote</span><span className="lp">&nbsp;PA</span></div>
        <div className="ls">HR System</div>
      </div>
      <nav>{nav.map(function(n){return <div key={n.id} className="n-sec" style={{paddingBottom:0}}>
        <button className={'nav-item'+(page===n.id?' active':'')} onClick={function(){setPage(n.id);}}>
          {n.icon}{n.label}
          {n.id==='leave'&&pendingCount>0&&<span style={{marginLeft:'auto',background:'var(--orange)',color:'#fff',fontSize:10,fontWeight:700,borderRadius:10,padding:'1px 6px'}}>{pendingCount}</span>}
          {n.id==='announcements'&&urgentAnn>0&&<span style={{marginLeft:'auto',background:'var(--danger)',color:'#fff',fontSize:10,fontWeight:700,borderRadius:10,padding:'1px 6px'}}>!</span>}
        </button>
      </div>;})}</nav>
      <div style={{position:'absolute',bottom:0,left:0,right:0,padding:'12px 14px',borderTop:'1px solid rgba(255,255,255,.1)'}}>
        <div style={{display:'flex',alignItems:'center',gap:8,marginBottom:6}}>
          {(function(){var c=avColor(user.name);return <div className="avatar" style={{background:c.bg,color:c.fg,width:28,height:28,fontSize:10}}>{initials(user.name)}</div>;})()}
          <div><div style={{fontSize:12,fontWeight:500,color:'#fff'}}>{user.name}</div><div style={{fontSize:10,color:'rgba(255,255,255,.4)'}}>{user.dept}</div></div>
        </div>
        <button onClick={handleLogout} className="nav-item" style={{color:'rgba(255,255,255,.4)',fontSize:12,padding:'6px 8px'}}>{I.out} Sign out</button>
      </div>
    </aside>
    <main className="main">
      {page==='dashboard'     &&<Dashboard employees={employees} contracts={contracts} leaveRequests={leaveRequests} currentUser={user} onRefresh={function(){loadAll(true);}} refreshing={refreshing}/>}
      {page==='employees'     &&<EmployeesPage employees={employees} setEmployees={setEmployees} toast={toast} currentUser={user}/>}
      {page==='contracts'     &&<ContractsPage employees={employees} contracts={contracts} setContracts={setContracts} toast={toast} currentUser={user}/>}
      {page==='leave'         &&<LeavePage employees={employees} leaveRequests={leaveRequests} setLeaveRequests={setLeaveRequests} toast={toast} currentUser={user}/>}
      {page==='announcements' &&<AnnouncementsPage employees={employees} announcements={announcements} setAnnouncements={setAnnouncements} toast={toast} currentUser={user}/>}
    </main>
    {toastMsg&&<Toast msg={toastMsg} onDone={function(){setToastMsg(null);}}/>}
  </div>;
}

ReactDOM.createRoot(document.getElementById('root')).render(React.createElement(App));
</script>
</body>
</html>
