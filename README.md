<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>مرصاد | غرفة القيادة</title>
<link href="https://fonts.googleapis.com/css2?family=Alexandria:wght@400;600;700;800&family=JetBrains+Mono:wght@500;700&display=swap" rel="stylesheet">
<link rel="stylesheet" href="https://unpkg.com/leaflet@1.9.4/dist/leaflet.css"/>
<script src="https://unpkg.com/leaflet@1.9.4/dist/leaflet.js"></script>
<style>
*{margin:0;padding:0;box-sizing:border-box}
body{font-family:'Alexandria',sans-serif;background:#070b14;color:#f8fafc;direction:rtl;padding:1rem;min-height:100vh}
.container{max-width:1600px;margin:0 auto}

/* Header */
.ops-header{background:linear-gradient(135deg,#0f172a,#111827);border:1px solid rgba(59,130,246,.2);border-radius:12px;padding:.9rem 1.4rem;margin-bottom:.9rem;display:flex;flex-wrap:wrap;justify-content:space-between;align-items:center;gap:.8rem;box-shadow:0 4px 20px rgba(0,0,0,.4)}
.ops-title-group{display:flex;align-items:center;gap:12px}
.ops-logo{font-size:1.9rem}
.ops-title{font-size:1.28rem;font-weight:800;color:#f8fafc}
.ops-subtitle{font-size:.78rem;color:#94a3b8;margin-top:2px}
.header-right{display:flex;align-items:center;gap:10px;flex-wrap:wrap}
.badge-live{display:inline-flex;align-items:center;gap:7px;background:rgba(16,185,129,.12);border:1px solid rgba(16,185,129,.35);padding:5px 12px;border-radius:9999px;font-size:.76rem;font-weight:700;color:#34d399}
.pulse-dot{width:7px;height:7px;border-radius:50%;background:#10b981;box-shadow:0 0 8px #10b981;animation:pulse 1.8s infinite}
@keyframes pulse{0%,100%{opacity:1;transform:scale(1)}50%{opacity:.3;transform:scale(1.3)}}
.time-chip{font-family:'JetBrains Mono',monospace;font-size:.82rem;color:#cbd5e1;background:rgba(30,41,59,.6);border:1px solid rgba(148,163,184,.2);padding:5px 12px;border-radius:8px;direction:ltr}

/* KPI */
.kpi-container{display:grid;grid-template-columns:repeat(7,1fr);gap:10px;margin-bottom:.9rem}
.kpi-card{background:linear-gradient(145deg,rgba(15,23,42,.9),rgba(30,41,59,.75));border:1px solid rgba(75,85,99,.3);border-radius:10px;padding:.75rem .9rem;box-shadow:0 4px 12px rgba(0,0,0,.25)}
.kpi-card.kpi-total{border-right:3px solid #38bdf8}
.kpi-card.kpi-safe{border-right:3px solid #10b981}
.kpi-card.kpi-moderate{border-right:3px solid #facc15}
.kpi-card.kpi-high{border-right:3px solid #f97316}
.kpi-card.kpi-critical{border-right:3px solid #ef4444;background:linear-gradient(145deg,rgba(239,68,68,.12),rgba(30,41,59,.85))}
.kpi-card.kpi-sensors{border-right:3px solid #6366f1}
.kpi-card.kpi-alerts{border-right:3px solid #ec4899}
.kpi-label{font-size:.72rem;color:#94a3b8;font-weight:600;margin-bottom:.2rem;white-space:nowrap}
.kpi-val{font-size:1.45rem;font-weight:800;color:#f8fafc;line-height:1.2}
.kpi-sub{font-size:.65rem;color:#64748b;margin-top:.2rem}

/* Alert */
.critical-alert-banner{background:linear-gradient(90deg,#7f1d1d,#991b1b,#b91c1c);border:1px solid #ef4444;color:#fff;padding:10px 16px;border-radius:9px;margin-bottom:.9rem;display:flex;align-items:center;justify-content:space-between;font-weight:700;box-shadow:0 0 20px rgba(220,38,38,.35);flex-wrap:wrap;gap:10px}
.alert-content{display:flex;align-items:center;gap:12px}
.alert-badge{background:rgba(0,0,0,.35);padding:5px 14px;border-radius:6px;font-size:.8rem;font-family:'JetBrains Mono',monospace}

/* Map */
#map{height:560px;border-radius:11px;border:1px solid rgba(51,65,85,.6);margin-bottom:6px;background:#0a1628}
.map-legend{display:flex;justify-content:space-around;align-items:center;background:rgba(15,23,42,.85);padding:8px 14px;border-radius:8px;border:1px solid rgba(51,65,85,.4);font-size:.78rem;flex-wrap:wrap;gap:8px;margin-bottom:1.5rem}
.legend-item{display:flex;align-items:center;gap:6px}
.legend-swatch{display:inline-block;width:14px;height:14px;border-radius:3px}

/* Table */
.streets-table-container{background:rgba(15,23,42,.9);border:1px solid rgba(51,65,85,.6);border-radius:11px;padding:.8rem 1rem;margin-top:.9rem}
.table-title-row{display:flex;justify-content:space-between;align-items:center;margin-bottom:.6rem;flex-wrap:wrap;gap:8px}
.table-title{display:flex;align-items:center;gap:8px;font-size:1.05rem;font-weight:800}
.table-scroll{overflow-x:auto}
.custom-table{width:100%;border-collapse:collapse;font-size:.82rem;text-align:right}
.custom-table th{background:rgba(30,41,59,.8);color:#94a3b8;padding:8px 12px;font-weight:700;border-bottom:1px solid rgba(75,85,99,.4);white-space:nowrap}
.custom-table td{padding:9px 12px;border-bottom:1px solid rgba(51,65,85,.3);color:#e2e8f0;vertical-align:middle}
.custom-table tr:hover{background:rgba(51,65,85,.25)}
.status-pill{display:inline-block;padding:3px 9px;border-radius:9999px;font-size:.72rem;font-weight:700;white-space:nowrap}
.pill-safe{background:rgba(16,185,129,.15);color:#34d399;border:1px solid rgba(16,185,129,.35)}
.pill-moderate{background:rgba(250,204,21,.15);color:#facc15;border:1px solid rgba(250,204,21,.35)}
.pill-high{background:rgba(249,115,22,.18);color:#fb923c;border:1px solid rgba(249,115,22,.4)}
.pill-critical{background:rgba(239,68,68,.2);color:#f87171;border:1px solid rgba(239,68,68,.45);font-weight:800;animation:blink 1.5s infinite}
@keyframes blink{0%,100%{opacity:1}50%{opacity:.65}}
.sensor-online{color:#34d399;font-weight:700}
.mini-progress-bg{background:rgba(51,65,85,.5);height:6px;width:75px;border-radius:9999px;overflow:hidden;display:inline-block;vertical-align:middle;margin-left:6px}
.mini-progress-bar{height:100%;border-radius:9999px;transition:width .5s}

/* Vehicles */
.section-title{text-align:center;color:#f8fafc;margin:2rem 0 .5rem;font-size:1.5rem;font-weight:800}
.section-subtitle{text-align:center;color:#94a3b8;margin-bottom:1.5rem;font-size:.9rem}
.controls-strip{display:flex;gap:1rem;align-items:center;flex-wrap:wrap;background:rgba(15,23,42,.8);border:1px solid rgba(51,65,85,.6);border-radius:10px;padding:1rem 1.2rem;margin-bottom:1.2rem}
.control-group{flex:1;min-width:240px}
.control-group.compact{flex:0 0 auto;min-width:auto;text-align:center}
.control-label{display:block;font-size:.85rem;color:#94a3b8;margin-bottom:.5rem;font-weight:600}
select{width:100%;padding:8px 12px;background:rgba(30,41,59,.8);border:1px solid rgba(71,85,105,.6);border-radius:8px;color:#f8fafc;font-family:inherit;font-size:.88rem;cursor:pointer}
.depth-readout{font-family:'JetBrains Mono',monospace;font-size:1.4rem;font-weight:700;color:#38bdf8;padding:8px 16px;background:rgba(56,189,248,.1);border:1px solid rgba(56,189,248,.35);border-radius:8px;display:inline-block}
.depth-status-badge{display:inline-block;padding:5px 14px;border-radius:9999px;font-size:.82rem;font-weight:700;margin-right:10px;transition:all .3s}
.info-banner{background:rgba(56,189,248,.08);border:1px solid rgba(56,189,248,.3);color:#7dd3fc;padding:12px 18px;border-radius:9px;margin-bottom:1.2rem;font-size:.9rem;display:flex;justify-content:space-between;align-items:center;flex-wrap:wrap;gap:10px}
.vehicles-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:1.2rem;margin-bottom:1.5rem}
.vehicle-card{background:rgba(15,23,42,.9);border:1px solid rgba(51,65,85,.6);border-radius:14px;padding:1.2rem;transition:all .3s;overflow:hidden}
.vehicle-card.status-safe{border-color:rgba(16,185,129,.5);box-shadow:0 0 20px rgba(16,185,129,.15)}
.vehicle-card.status-caution{border-color:rgba(250,204,21,.5);box-shadow:0 0 20px rgba(250,204,21,.15)}
.vehicle-card.status-danger{border-color:rgba(239,68,68,.6);box-shadow:0 0 25px rgba(239,68,68,.25);animation:cardpulse 2s infinite alternate}
@keyframes cardpulse{from{box-shadow:0 0 15px rgba(239,68,68,.15)}to{box-shadow:0 0 30px rgba(239,68,68,.4)}}
.vehicle-header{display:flex;justify-content:space-between;align-items:center;margin-bottom:1rem;flex-wrap:wrap;gap:8px}
.vehicle-title-group{display:flex;align-items:center;gap:8px}
.vehicle-title{font-weight:800;font-size:1.05rem}
.sim-badge{background:rgba(148,163,184,.15);color:#94a3b8;padding:4px 10px;border-radius:9999px;font-size:.7rem;font-weight:700;border:1px solid rgba(148,163,184,.3)}
.vehicle-scene{position:relative;width:100%;height:200px;border-radius:10px;overflow:hidden;background:linear-gradient(180deg,#0a1628,#0f2035);margin-bottom:1rem;border:1px solid rgba(30,41,59,.8)}
.water-level-box{position:absolute;top:10px;left:10px;background:rgba(0,0,0,.65);border:1px solid rgba(56,189,248,.4);color:#e0f2fe;padding:5px 12px;border-radius:8px;font-size:.78rem;font-weight:700;font-family:'JetBrains Mono',monospace;z-index:5}
.water{position:absolute;bottom:0;left:0;right:0;background:linear-gradient(180deg,rgba(14,165,233,.75),rgba(2,132,199,.95));transition:height .8s cubic-bezier(.4,0,.2,1);z-index:2;border-top:2px solid rgba(125,211,252,.6);pointer-events:none}
.water::before{content:'';position:absolute;top:-3px;left:0;right:0;height:6px;background:linear-gradient(90deg,transparent,rgba(255,255,255,.5),transparent);animation:shimmer 3s infinite}
@keyframes shimmer{0%,100%{opacity:.3;transform:translateX(-20px)}50%{opacity:.7;transform:translateX(20px)}}
.vehicle-svg{position:absolute;bottom:8px;left:50%;transform:translateX(-50%);z-index:3;filter:drop-shadow(0 4px 8px rgba(0,0,0,.6))}
.ground-line{position:absolute;bottom:8px;left:0;right:0;height:2px;background:rgba(148,163,184,.4);z-index:1}
.risk-row{background:rgba(15,23,42,.7);border:1px solid rgba(51,65,85,.6);border-radius:10px;padding:.8rem 1rem;display:flex;justify-content:space-between;align-items:center;margin-bottom:.6rem}
.risk-row:last-child{margin-bottom:0}
.risk-label{font-size:.88rem;color:#94a3b8;font-weight:600}
.risk-value{font-size:1.6rem;font-weight:800;font-family:'JetBrains Mono',monospace;line-height:1}
.level-pill{display:inline-block;padding:6px 16px;border-radius:9999px;font-size:.88rem;font-weight:800}
.badge-safe{background:rgba(16,185,129,.15);color:#34d399;border:1px solid rgba(16,185,129,.5)}
.badge-caution{background:rgba(250,204,21,.15);color:#facc15;border:1px solid rgba(250,204,21,.5)}
.badge-danger{background:rgba(239,68,68,.2);color:#f87171;border:1px solid rgba(239,68,68,.6);animation:blink 1.5s infinite}
.note-card{background:rgba(15,23,42,.9);border:1px solid rgba(51,65,85,.6);border-radius:11px;padding:1rem 1.4rem;margin-top:1.5rem}
.note-card b{color:#fbbf24}
.note-card span{color:#94a3b8;font-size:.85rem}
.footer{text-align:center;padding:2rem 0 1rem;color:#64748b;font-size:.8rem;border-top:1px solid rgba(51,65,85,.4);margin-top:2rem}
.footer b{color:#94a3b8}

@media(max-width:1024px){.kpi-container{grid-template-columns:repeat(4,1fr)}#map{height:480px}.vehicles-grid{grid-template-columns:repeat(2,1fr)}}
@media(max-width:768px){body{padding:.6rem}.ops-header{flex-direction:column;align-items:flex-start}.kpi-container{grid-template-columns:repeat(2,1fr);gap:6px}#map{height:380px}.vehicles-grid{grid-template-columns:1fr}.controls-strip{flex-direction:column;align-items:stretch}}
</style>
</head>
<body>
<div class="container">

  <div class="ops-header">
    <div class="ops-title-group">
      <span class="ops-logo">🛡️</span>
      <div>
        <h1 class="ops-title">مِـرْصَـاد</h1>
        <p class="ops-subtitle">غرفة القيادة والتحكم الموحدة • رصد شبكة الطرق الحضرية الذكية</p>
      </div>
    </div>
    <div class="header-right">
      <div class="badge-live"><span class="pulse-dot"></span> بث حي مباشر للحساسات</div>
      <div class="time-chip" id="clock">🕒 --</div>
    </div>
  </div>

  <div class="kpi-container" id="kpiContainer"></div>
  <div id="alertBanner"></div>

  <div id="map"></div>
  <div class="map-legend">
    <div class="legend-item"><span class="legend-swatch" style="background:#10b981"></span><b>أخضر:</b> طبيعي (&lt; 7 سم)</div>
    <div class="legend-item"><span class="legend-swatch" style="background:#facc15"></span><b>أصفر:</b> متوسط (7-15 سم)</div>
    <div class="legend-item"><span class="legend-swatch" style="background:#f97316"></span><b>برتقالي:</b> مرتفع (16-24 سم)</div>
    <div class="legend-item"><span class="legend-swatch" style="background:#ef4444"></span><b>أحمر:</b> حرج (≥ 25 سم)</div>
  </div>

  <div class="streets-table-container">
    <div class="table-title-row">
      <div class="table-title"><span style="font-size:1.2rem">📊</span><h3>جدول الرصد الميداني الشامل</h3></div>
      <div style="font-size:.76rem;color:#94a3b8">تحديث حي ومباشر</div>
    </div>
    <div class="table-scroll">
      <table class="custom-table">
        <thead><tr>
          <th>الشارع</th><th>مستوى المياه %</th><th>العمق</th><th>الحالة</th>
          <th>الحساس</th><th>التصريف</th><th>الإجراء</th><th>آخر تحديث</th>
        </tr></thead>
        <tbody id="tableBody"></tbody>
      </table>
    </div>
  </div>

  <h2 class="section-title">🚦 مصفوفة أمان عبور المركبات</h2>
  <p class="section-subtitle">يتم ربط مستوى المياه تلقائياً بالشارع المختار — وشاهد مستوى الماء بالنسبة لكل مركبة</p>

  <div class="controls-strip">
    <div class="control-group">
      <label class="control-label">📍 اختر الشارع (يُحدَّد مستوى المياه تلقائياً)</label>
      <select id="streetSelect"></select>
    </div>
    <div class="control-group compact">
      <label class="control-label">💧 مستوى المياه المرصود</label>
      <div>
        <span class="depth-readout" id="depthReadout">-- سم</span>
        <span class="depth-status-badge" id="depthStatusBadge">--</span>
      </div>
    </div>
  </div>

  <div class="info-banner" id="infoBanner">📍 اختر الشارع لعرض التفاصيل</div>
  <div class="vehicles-grid" id="vehiclesGrid"></div>

  <div class="note-card">
    <b>⚠️ ملاحظة مهمة</b><br>
    <span>النسب أعلاه محاكاة برمجية لعرض فكرة المشروع، وليست تقييماً حقيقياً لسلامة القيادة.</span>
  </div>

  <div class="footer">
    🛡️ <b>مِـرْصَـاد</b> — نظام رصد الفيضانات وإدارة المخاطر الحضرية<br>
    <span style="font-size:.72rem">© 2025 Mirsad Operations Command Center</span>
  </div>

</div>

<script>
// ============================================================
// 1. قاعدة بيانات 10 شوارع
// ============================================================
const BASE_STREETS = [
  {id:"ST-01",name:"طريق الملك فهد (قطاع النفق المركزي)",
   coords:[[24.6980,46.6800],[24.7060,46.6825],[24.7145,46.6850],[24.7230,46.6875],[24.7310,46.6900]],
   sensor_loc:[24.7145,46.6850],base_depth:34,sensor_status:"متصل (رادار مزدوج)",
   drainage:"4 مضخات غاطسة تعمل بأقصى طاقة",closure:"مغلق بقرار الدفاع المدني (تحويل إلى الدائري)"},
  {id:"ST-02",name:"طريق مكة المكرمة (خريص - تقاطع العليا)",
   coords:[[24.7080,46.6580],[24.7100,46.6720],[24.7120,46.6860],[24.7140,46.7000],[24.7160,46.7140]],
   sensor_loc:[24.7120,46.6860],base_depth:21,sensor_status:"متصل (ألتراسونيك)",
   drainage:"مضختان قيد التشغيل",closure:"مسار الشاحنات فقط - تنبيه للسيدان"},
  {id:"ST-03",name:"طريق التخصصي (منخفض وادي حنيفة)",
   coords:[[24.6850,46.6620],[24.6980,46.6670],[24.7110,46.6720],[24.7240,46.6770],[24.7370,46.6820]],
   sensor_loc:[24.7110,46.6720],base_depth:11,sensor_status:"متصل (هيدروليكي)",
   drainage:"تصريف طبيعي مستمر",closure:"سالك مع تخفيف السرعة"},
  {id:"ST-04",name:"طريق العروبة (نفق تقاطع التخصصي)",
   coords:[[24.7200,46.6600],[24.7215,46.6730],[24.7230,46.6860],[24.7245,46.6990],[24.7260,46.7120]],
   sensor_loc:[24.7215,46.6730],base_depth:28,sensor_status:"متصل (رادار ليزري)",
   drainage:"3 مضخات طوارئ تعمل",closure:"إغلاق جزئي لحارات النفق السفلية"},
  {id:"ST-05",name:"طريق الملك عبدالله (المسار السطحي والخدمة)",
   coords:[[24.7350,46.6500],[24.7370,46.6680],[24.7390,46.6860],[24.7410,46.7040],[24.7430,46.7220]],
   sensor_loc:[24.7390,46.6860],base_depth:4,sensor_status:"متصل (رادار)",
   drainage:"جاهزية الاستعداد",closure:"سالك ومفتوح بالكامل"},
  {id:"ST-06",name:"طريق الإمام سعود بن عبدالعزيز بن محمد",
   coords:[[24.7550,46.6550],[24.7570,46.6720],[24.7590,46.6890],[24.7610,46.7060],[24.7630,46.7230]],
   sensor_loc:[24.7590,46.6890],base_depth:2,sensor_status:"متصل (ألتراسونيك)",
   drainage:"تصريف طبيعي",closure:"سالك ومفتوح بالكامل"},
  {id:"ST-07",name:"طريق الأمير تركي بن عبدالعزيز الأول",
   coords:[[24.7000,46.6450],[24.7150,46.6480],[24.7300,46.6510],[24.7450,46.6540],[24.7600,46.6570]],
   sensor_loc:[24.7300,46.6510],base_depth:17,sensor_status:"متصل (مسبار ضغط)",
   drainage:"مضخة هيدروليكية تعمل",closure:"تحذير من تجمعات مياه جانبية"},
  {id:"ST-08",name:"طريق أبي بكر الصديق (تقاطع مخرج 6)",
   coords:[[24.7300,46.7020],[24.7450,46.7060],[24.7600,46.7100],[24.7750,46.7140],[24.7900,46.7180]],
   sensor_loc:[24.7600,46.7100],base_depth:8,sensor_status:"متصل (ألتراسونيك)",
   drainage:"قنوات السيول سالكة",closure:"سالك مع تنبيه مروري"},
  {id:"ST-09",name:"طريق الملك عبدالعزيز (نفق تقاطع العروبة)",
   coords:[[24.7050,46.7050],[24.7200,46.7080],[24.7350,46.7110],[24.7500,46.7140],[24.7650,46.7170]],
   sensor_loc:[24.7200,46.7080],base_depth:3,sensor_status:"متصل (رادار)",
   drainage:"تصريف طبيعي",closure:"سالك ومفتوح بالكامل"},
  {id:"ST-10",name:"طريق عثمان بن عفان (المخرج الشمالي)",
   coords:[[24.7320,46.7200],[24.7470,46.7240],[24.7620,46.7280],[24.7770,46.7320],[24.7920,46.7360]],
   sensor_loc:[24.7620,46.7280],base_depth:1,sensor_status:"متصل (رادار ليزري)",
   drainage:"تصريف طبيعي",closure:"سالك ومفتوح بالكامل"}
];

// ============================================================
// 2. محاكاة البيانات الحية
// ============================================================
function generateLiveData(){
  const now = new Date();
  const seed = now.getMinutes()*60 + Math.floor(now.getSeconds()/10);
  return BASE_STREETS.map((s,i)=>{
    const pseudo = (Math.abs(Math.sin(seed+i)*100)%3)-1;
    const delta = Math.round(pseudo);
    const depth = Math.max(0,Math.min(50,s.base_depth+delta));
    const pct = Math.min(100,Math.round((depth/35)*100));
    let status,color,pill;
    if(depth<7){status="طبيعي";color="#10b981";pill="pill-safe";}
    else if(depth<=15){status="ارتفاع متوسط";color="#facc15";pill="pill-moderate";}
    else if(depth<=24){status="ارتفاع مرتفع";color="#f97316";pill="pill-high";}
    else{status="حرج / معرض للغرق";color="#ef4444";pill="pill-critical";}
    const sec = 3+Math.floor(Math.abs(Math.sin(seed*2+i))*18);
    return {...s,depth,pct,status,status_color:color,pill_class:pill,last_seen:`قبل ${sec} ثوانٍ`};
  });
}
let streetsData = generateLiveData();

// ============================================================
// 3. الساعة
// ============================================================
function updateClock(){
  const d=new Date(),pad=n=>String(n).padStart(2,'0');
  document.getElementById('clock').textContent =
    `🕒 ${d.getFullYear()}-${pad(d.getMonth()+1)}-${pad(d.getDate())} | ${pad(d.getHours())}:${pad(d.getMinutes())}:${pad(d.getSeconds())}`;
}
setInterval(updateClock,1000);updateClock();

// ============================================================
// 4. KPI + Alert
// ============================================================
function renderKPI(){
  const total=streetsData.length;
  const safe=streetsData.filter(s=>s.status==="طبيعي").length;
  const mod=streetsData.filter(s=>s.status==="ارتفاع متوسط").length;
  const high=streetsData.filter(s=>s.status==="ارتفاع مرتفع").length;
  const crit=streetsData.filter(s=>s.status.includes("حرج")).length;
  const sensors=streetsData.filter(s=>s.sensor_status.includes("متصل")).length;
  const alerts=high+crit;

  document.getElementById('kpiContainer').innerHTML=`
    <div class="kpi-card kpi-total"><div class="kpi-label">🛣️ إجمالي الشوارع</div><div class="kpi-val" style="color:#38bdf8">${total}</div><div class="kpi-sub">شبكة محاور رئيسية</div></div>
    <div class="kpi-card kpi-safe"><div class="kpi-label">🟢 طبيعية</div><div class="kpi-val" style="color:#10b981">${safe}</div><div class="kpi-sub">${Math.round(safe/total*100)}% آمنة</div></div>
    <div class="kpi-card kpi-moderate"><div class="kpi-label">🟡 متوسط</div><div class="kpi-val" style="color:#facc15">${mod}</div><div class="kpi-sub">7 - 15 سم</div></div>
    <div class="kpi-card kpi-high"><div class="kpi-label">🟠 مرتفع</div><div class="kpi-val" style="color:#fb923c">${high}</div><div class="kpi-sub">16 - 24 سم</div></div>
    <div class="kpi-card kpi-critical"><div class="kpi-label">🔴 حرج</div><div class="kpi-val" style="color:#f87171">${crit}</div><div class="kpi-sub">≥ 25 سم</div></div>
    <div class="kpi-card kpi-sensors"><div class="kpi-label">📡 الحساسات</div><div class="kpi-val" style="color:#818cf8">${sensors}/${total}</div><div class="kpi-sub">تغطية 100%</div></div>
    <div class="kpi-card kpi-alerts"><div class="kpi-label">🚨 تنبيهات نشطة</div><div class="kpi-val" style="color:#f472b6">${alerts}</div><div class="kpi-sub">تدخل ميداني</div></div>
  `;

  const critStreets=streetsData.filter(s=>s.status.includes("حرج"));
  const banner=document.getElementById('alertBanner');
  if(critStreets.length>0){
    const names=critStreets.map(s=>`<b>${s.name}</b> (${s.depth} سم)`).join(" • ");
    banner.innerHTML=`<div class="critical-alert-banner">
      <div class="alert-content"><span style="font-size:1.6rem">⚠️</span>
        <div><div style="font-size:1.02rem">إنذار طارئ: تم رصد ${critStreets.length} شوارع في مرحلة الغمر الحرج</div>
        <div style="font-size:.83rem;opacity:.95;margin-top:2px">${names} — يتطلب تدخلاً فورياً</div></div></div>
      <div class="alert-badge">ALERT-LVL-4</div></div>`;
  } else banner.innerHTML='';
}

// ============================================================
// 5. الخريطة
// ============================================================
let map, mapLayers=[];
function initMap(){
  map=L.map('map',{center:[24.7350,46.6850],zoom:12,zoomControl:true});
  L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png',{
    attribution:'&copy; OpenStreetMap &copy; CartoDB',subdomains:'abcd',maxZoom:19
  }).addTo(map);
}
function renderMap(){
  mapLayers.forEach(l=>map.removeLayer(l));mapLayers=[];
  streetsData.forEach(s=>{
    const isCrit=s.status.includes("حرج");
    const pl=L.polyline(s.coords,{color:s.status_color,weight:isCrit?7:5,opacity:isCrit?.95:.85}).addTo(map);
    pl.bindTooltip(`<div style="font-family:Alexandria;direction:rtl;text-align:right;font-size:12px">
      <b>${s.name}</b><br>الحالة: <span style="color:${s.status_color};font-weight:bold">${s.status}</span><br>
      العمق: <b>${s.depth} سم</b> (${s.pct}%)</div>`,{sticky:true});
    pl.bindPopup(`<div style="font-family:Alexandria;direction:rtl;text-align:right;min-width:210px">
      <h4 style="margin:0 0 5px 0;color:#0f172a;font-size:14px;font-weight:800">${s.name}</h4>
      <hr style="margin:4px 0;border:0;border-top:1px solid #cbd5e1">
      <b>المعرّف:</b> ${s.id}<br>
      <b>الحالة:</b> <span style="color:${s.status_color};font-weight:bold">${s.status}</span><br>
      <b>منسوب المياه:</b> <b style="color:${s.status_color}">${s.depth} سم</b> (${s.pct}%)<br>
      <b>التصريف:</b> ${s.drainage}<br>
      <b>الإجراء:</b> <span style="color:#b91c1c;font-weight:bold">${s.closure}</span><br>
      <b>آخر قراءة:</b> ${s.last_seen}</div>`,{maxWidth:330});
    mapLayers.push(pl);

    const circle=L.circleMarker(s.sensor_loc,{radius:8,color:'#fff',weight:2,fillColor:s.status_color,fillOpacity:1}).addTo(map);
    circle.bindTooltip(`حساس ${s.id} - ${s.depth} سم`,{direction:'top'});
    circle.bindPopup(`<div style="font-family:Alexandria;direction:rtl;text-align:right;min-width:200px">
      <h4 style="margin:0 0 4px 0;color:${s.status_color};font-size:13px;font-weight:800">📡 حساس: ${s.id}</h4>
      <b>الموقع:</b> ${s.name}<br>
      <b>العمق:</b> <b style="color:${s.status_color}">${s.depth} سم</b><br>
      <b>الحالة:</b> ${s.sensor_status}</div>`,{maxWidth:320});
    mapLayers.push(circle);
  });
}

// ============================================================
// 6. الجدول
// ============================================================
function renderTable(){
  document.getElementById('tableBody').innerHTML=streetsData.map(s=>{
    const cc=s.closure.includes('مغلق')?'#f87171':'#cbd5e1';
    const cw=s.closure.includes('مغلق')?'800':'500';
    return `<tr>
      <td style="font-weight:700"><span style="color:#94a3b8;font-size:.75rem;font-family:'JetBrains Mono';margin-left:6px">${s.id}</span>${s.name}</td>
      <td><div style="display:inline-flex;align-items:center">
        <div class="mini-progress-bg"><div class="mini-progress-bar" style="width:${s.pct}%;background:${s.status_color}"></div></div>
        <span style="font-family:'JetBrains Mono';font-weight:700;font-size:.75rem;color:${s.status_color}">${s.pct}%</span></div></td>
      <td><b style="font-family:'JetBrains Mono';color:${s.status_color}">${s.depth} سم</b></td>
      <td><span class="status-pill ${s.pill_class}">${s.status}</span></td>
      <td><span class="sensor-online">🟢 ${s.sensor_status}</span></td>
      <td style="font-size:.75rem;color:#94a3b8">${s.drainage}</td>
      <td style="font-size:.75rem;color:${cc};font-weight:${cw}">${s.closure}</td>
      <td style="font-family:'JetBrains Mono';font-size:.74rem;color:#64748b">${s.last_seen}</td>
    </tr>`;
  }).join('');
}

// ============================================================
// 7. مصفوفة المركبات
// ============================================================
const VEHICLES=[
  {icon:"🚗",name:"سيارة سيدان",desc:"حد الأمان: 12 سم | ممنوع: +18 سم",clearance:15,svgType:"sedan"},
  {icon:"🚙",name:"دفاع رباعي (SUV)",desc:"حد الأمان: 20 سم | ممنوع: +35 سم",clearance:23,svgType:"suv"},
  {icon:"🚚",name:"شاحنة / دفاع مدني",desc:"ممر مسموح: 28 سم | خطر: +48 سم",clearance:30,svgType:"truck"}
];

function getVehicleSVG(type,color){
  if(type==="sedan"){
    return `<svg width="180" height="80" viewBox="0 0 180 80" xmlns="http://www.w3.org/2000/svg">
      <path d="M 20,55 L 25,35 Q 30,27 45,27 L 120,27 Q 135,27 145,37 L 160,55 Z" fill="${color}" stroke="#475569" stroke-width="1.5"/>
      <path d="M 50,27 L 60,15 Q 65,12 75,12 L 105,12 Q 115,12 120,17 L 130,27 Z" fill="#64748b" stroke="#475569" stroke-width="1.5"/>
      <path d="M 62,15 L 72,15 L 68,27 L 55,27 Z" fill="#94a3b8" opacity="0.7"/>
      <path d="M 108,15 L 118,15 L 125,27 L 112,27 Z" fill="#94a3b8" opacity="0.7"/>
      <circle cx="55" cy="60" r="13" fill="#1e293b" stroke="#475569" stroke-width="2"/>
      <circle cx="55" cy="60" r="6" fill="#334155"/>
      <circle cx="135" cy="60" r="13" fill="#1e293b" stroke="#475569" stroke-width="2"/>
      <circle cx="135" cy="60" r="6" fill="#334155"/>
      <rect x="155" y="40" width="8" height="6" rx="2" fill="#fbbf24"/>
    </svg>`;
  } else if(type==="suv"){
    return `<svg width="180" height="100" viewBox="0 0 180 100" xmlns="http://www.w3.org/2000/svg">
      <path d="M 15,75 L 20,45 Q 25,37 40,37 L 140,37 Q 155,37 165,50 L 170,75 Z" fill="${color}" stroke="#475569" stroke-width="1.5"/>
      <path d="M 35,37 L 40,20 Q 45,15 55,15 L 125,15 Q 135,15 140,23 L 145,37 Z" fill="#64748b" stroke="#475569" stroke-width="1.5"/>
      <rect x="45" y="21" width="30" height="14" rx="2" fill="#94a3b8" opacity="0.7"/>
      <rect x="85" y="21" width="30" height="14" rx="2" fill="#94a3b8" opacity="0.7"/>
      <rect x="120" y="21" width="20" height="14" rx="2" fill="#94a3b8" opacity="0.7"/>
      <circle cx="50" cy="80" r="17" fill="#1e293b" stroke="#475569" stroke-width="2"/>
      <circle cx="50" cy="80" r="8" fill="#334155"/>
      <circle cx="140" cy="80" r="17" fill="#1e293b" stroke="#475569" stroke-width="2"/>
      <circle cx="140" cy="80" r="8" fill="#334155"/>
      <rect x="70" y="10" width="40" height="6" rx="2" fill="#334155"/>
    </svg>`;
  } else {
    return `<svg width="200" height="110" viewBox="0 0 200 110" xmlns="http://www.w3.org/2000/svg">
      <rect x="10" y="30" width="100" height="60" rx="4" fill="#475569" stroke="#334155" stroke-width="1.5"/>
      <line x1="25" y1="30" x2="25" y2="90" stroke="#334155" stroke-width="1"/>
      <line x1="45" y1="30" x2="45" y2="90" stroke="#334155" stroke-width="1"/>
      <line x1="65" y1="30" x2="65" y2="90" stroke="#334155" stroke-width="1"/>
      <line x1="85" y1="30" x2="85" y2="90" stroke="#334155" stroke-width="1"/>
      <path d="M 115,90 L 115,50 Q 115,43 122,43 L 155,43 Q 165,43 170,55 L 175,75 L 175,90 Z" fill="${color}" stroke="#475569" stroke-width="1.5"/>
      <rect x="122" y="50" width="25" height="18" rx="2" fill="#94a3b8" opacity="0.7"/>
      <circle cx="35" cy="100" r="15" fill="#1e293b" stroke="#475569" stroke-width="2"/>
      <circle cx="35" cy="100" r="7" fill="#334155"/>
      <circle cx="70" cy="100" r="15" fill="#1e293b" stroke="#475569" stroke-width="2"/>
      <circle cx="70" cy="100" r="7" fill="#334155"/>
      <circle cx="150" cy="100" r="15" fill="#1e293b" stroke="#475569" stroke-width="2"/>
      <circle cx="150" cy="100" r="7" fill="#334155"/>
      <rect x="145" y="35" width="12" height="8" rx="2" fill="#ef4444"/>
    </svg>`;
  }
}

function calcRisk(depth,clearance){
  if(depth===0)return{score:0,level:"منخفض",color:"#34d399",badge:"badge-safe",card:"status-safe"};
  const score=Math.round(Math.min(100,(depth/clearance)*55+Math.max(0,depth-clearance)*1.2));
  if(score>=80)return{score,level:"حرج",color:"#ef4444",badge:"badge-danger",card:"status-danger"};
  if(score>=55)return{score,level:"مرتفع",color:"#fb923c",badge:"badge-caution",card:"status-caution"};
  if(score>=30)return{score,level:"متوسط",color:"#facc15",badge:"badge-caution",card:"status-caution"};
  return{score,level:"منخفض",color:"#34d399",badge:"badge-safe",card:"status-safe"};
}

function hexToRgb(hex){
  const r=/^#?([a-f\d]{2})([a-f\d]{2})([a-f\d]{2})$/i.exec(hex);
  return r?`${parseInt(r[1],16)},${parseInt(r[2],16)},${parseInt(r[3],16)}`:"148,163,184";
}

// تحويل العمق إلى ارتفاع بكسل في المشهد (200px ارتفاع)
function depthToPixels(depth){
  const sceneH=200, groundOffset=8, maxVisualDepth=60;
  const maxWaterH=sceneH-groundOffset-30;
  const ratio=Math.min(1,depth/maxVisualDepth);
  return ratio*maxWaterH;
}

function renderVehicles(){
  const sel=document.getElementById('streetSelect');
  const street=streetsData.find(s=>s.name===sel.value);
  if(!street)return;

  const depth=street.depth;
  const color=street.status_color;

  document.getElementById('depthReadout').textContent=depth+' سم';
  const badge=document.getElementById('depthStatusBadge');
  badge.textContent=street.status;
  badge.style.background=`rgba(${hexToRgb(color)},0.15)`;
  badge.style.color=color;
  badge.style.border=`1px solid ${color}80`;

  document.getElementById('infoBanner').innerHTML=`
    <div>📍 <b>${sel.value}</b> — مستوى المياه المرصود: <b style="color:${color};font-family:'JetBrains Mono'">${depth} سم</b></div>
    <div style="font-size:.82rem;color:#94a3b8">🕒 آخر تحديث: ${street.last_seen}</div>`;

  document.getElementById('vehiclesGrid').innerHTML=VEHICLES.map(v=>{
    const risk=calcRisk(depth,v.clearance);
    const waterH=depthToPixels(depth);
    const carColor=risk.score>=80?"#f1f5f9":risk.score>=55?"#e2e8f0":"#cbd5e1";
    return `<div class="vehicle-card ${risk.card}">
      <div class="vehicle-header">
        <div class="vehicle-title-group">
          <span style="font-size:1.4rem">${v.icon}</span>
          <span class="vehicle-title">${v.name}</span>
        </div>
        <span class="sim-badge">محاكاة</span>
      </div>
      <div class="vehicle-scene">
        <div class="ground-line"></div>
        <div class="water-level-box">💧 المياه: ${depth} سم</div>
        <div class="water" style="height:${waterH}px"></div>
        <div class="vehicle-svg">${getVehicleSVG(v.svgType,carColor)}</div>
      </div>
      <div class="risk-row">
        <span class="risk-label">نسبة الخطر</span>
        <span class="risk-value" style="color:${risk.color}">${risk.score}%</span>
      </div>
      <div class="risk-row">
        <span class="risk-label">المستوى</span>
        <span class="level-pill ${risk.badge}">${risk.level}</span>
      </div>
    </div>`;
  }).join('');
}

// ============================================================
// 8. القائمة
// ============================================================
function populateStreetSelect(){
  const sel=document.getElementById('streetSelect');
  const prev=sel.value;
  sel.innerHTML=streetsData.map(s=>{
    const icon=s.status==="طبيعي"?"🟢":s.status.includes("متوسط")?"🟡":s.status.includes("مرتفع")?"🟠":"🔴";
    return `<option value="${s.name}">${icon} ${s.name} — ${s.depth} سم</option>`;
  }).join('');
  if(prev&&[...sel.options].some(o=>o.value===prev))sel.value=prev;
  sel.onchange=()=>renderVehicles();
}

// ============================================================
// 9. التحديث الدوري
// ============================================================
function refreshData(){
  const sel=document.getElementById('streetSelect');
  const current=sel.value;
  streetsData=generateLiveData();
  renderKPI();
  renderMap();
  renderTable();
  populateStreetSelect();
  if(current&&[...sel.options].some(o=>o.value===current))sel.value=current;
  renderVehicles();
}

// ============================================================
// 10. التهيئة
// ============================================================
window.addEventListener('DOMContentLoaded',()=>{
  try{
    initMap();
    renderKPI();
    renderMap();
    renderTable();
    populateStreetSelect();
    renderVehicles();
    setInterval(refreshData,10000);
  }catch(err){
    console.error("خطأ في التهيئة:",err);
    alert("حدث خطأ: "+err.message);
  }
});
</script>
</body>
</html>
