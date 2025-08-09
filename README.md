# Med
App
<!doctype html>
<html lang="ar">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>دفتر التوصيل - Saclina (ألوان)</title>
<!-- الربط مع ملف الـ manifest -->
<link rel="manifest" href="manifest.json">
<style>
  :root{
    --bg:#f6f7fb; --card:#fff; --accent:#2b6cb0; --danger:#e53e3e;
    --text:#222; --muted:#666;
  }
  *{box-sizing:border-box}
  body{
    font-family: "Segoe UI", Tahoma, Arial;
    background:var(--bg);
    color:var(--text);
    margin:0; padding:16px; direction:rtl;
  }
  header{display:flex;align-items:center;gap:12px}
  header h1{font-size:20px;margin:0}
  .card{background:var(--card);border-radius:10px;padding:12px;margin-top:12px;box-shadow:0 2px 6px rgba(20,20,40,0.06)}
  .grid{display:flex;gap:8px;flex-wrap:wrap}
  label{font-size:13px;color:var(--muted)}
  input[type="text"], input[type="number"], select{
    width:100%; padding:8px;border:1px solid #ddd;border-radius:6px;font-size:14px;
  }
  .col{flex:1;min-width:150px}
  .row{display:flex;gap:8px;align-items:end}
  button{padding:10px 12px;border:0;border-radius:8px;background:var(--accent);color:#fff;font-weight:600;cursor:pointer}
  button.secondary{background:#6b7280}
  .actions{display:flex;gap:8px;flex-wrap:wrap;margin-top:8px}
  table{width:100%;border-collapse:collapse;margin-top:12px}
  th,td{padding:8px;border-bottom:1px solid #eee;text-align:center;font-size:14px}
  th{background:#fafafa;color:var(--muted);font-weight:700}
  .small{font-size:12px;color:var(--muted)}
  .summary{display:flex;gap:12px;justify-content:flex-start;align-items:center;margin-top:10px;flex-wrap:wrap}
  .pill{background:#fff;padding:8px 12px;border-radius:999px;border:1px solid #eee;font-weight:700}
  .right{margin-left:auto}
  .color-swatch{width:18px;height:18px;border-radius:4px;display:inline-block;vertical-align:middle;margin-left:6px;border:1px solid #ccc}
  @media (max-width:720px){
    .row{flex-direction:column;align-items:stretch}
    table, th, td{font-size:12px}
  }
  /* status backgrounds */
  .status-complete{background:#e6f7ea}
  .status-pending{background:#fff0f0}
  .status-progress{background:#fffbe6}
  .status-over{background:#e8f3ff}
</style>
</head>
<body>

<header>
  <div style="width:44px;height:44px;border-radius:8px;background:#ffe6f0;display:inline-block"></div>
  <h1>دفتر التوصيل — تتبع المحلات والدفع (ألوان)</h1>
</header>

<div class="card">
  <div class="row">
    <div class="col">
      <label>اسم المحل</label>
      <input id="inputName" type="text" placeholder="مثال: محل أحمد">
    </div>
    <div class="col">
      <label>العنوان / الحي</label>
      <input id="inputAddress" type="text" placeholder="مثال: وسط المدينة">
    </div>
    <div class="col">
      <label>الهاتف (اختياري)</label>
      <input id="inputPhone" type="text" placeholder="05xx...">
    </div>
  </div>

  <div class="row" style="margin-top:8px">
    <div class="col">
      <label>المبلغ الأصلي (د.ج)</label>
      <input id="inputAmount" type="number" min="0" step="0.01" placeholder="مثال: 10000">
    </div>
    <div class="col">
      <label>نسبة الخصم (%)</label>
      <input id="inputDiscount" type="number" min="0" step="0.01" value="0">
    </div>
    <div class="col">
      <label>المبلغ المدفوع (د.ج)</label>
      <input id="inputPaid" type="number" min="0" step="0.01" value="0">
    </div>
  </div>

  <div class="row" style="margin-top:8px;align-items:center">
    <div style="flex:1">
      <label>الحالة</label>
      <select id="inputStatus">
        <option value="pending">قيد الانتظار</option>
        <option value="progress">جاري التنفيذ</option>
        <option value="complete">مكتمل</option>
      </select>
    </div>

    <div style="width:140px">
      <label>لون التمييز</label>
      <input id="inputColor" type="color" value="#ffd166" style="width:100%;height:40px;border-radius:6px;border:1px solid #ddd">
    </div>

    <div style="width:150px">
      <label>&nbsp;</label>
      <button id="btnAdd">أضف / حفظ</button>
    </div>
    <div style="width:140px">
      <label>&nbsp;</label>
      <button id="btnClear" class="secondary">تفريغ الحقول</button>
    </div>
  </div>

  <div class="summary">
    <div class="pill">عدد المحلات: <span id="count">0</span></div>
    <div class="pill">إجمالي الديون: <span id="totalDebt">0 د.ج</span></div>
    <div class="pill">إجمالي المدفوعات: <span id="totalPaid">0 د.ج</span></div>
    <div class="right actions">
      <button id="btnExport" class="secondary">تصدير CSV</button>
      <button id="btnReset" class="secondary">حذف الكل</button>
    </div>
  </div>
</div>

<div class="card" style="margin-top:12px">
  <table id="tbl">
    <thead>
      <tr>
        <th>#</th>
        <th>المحل</th>
        <th>الهاتف</th>
        <th>المبلغ بعد الخصم</th>
        <th>المدفوع</th>
        <th>المتبقي</th>
        <th>الحالة</th>
        <th>ملاحظات</th>
        <th>إجراءات</th>
      </tr>
    </thead>
    <tbody></tbody>
  </table>
  <div class="small" style="margin-top:8px;color:var(--muted)">
    * التطبيق يعمل أوفلاين ويحفظ بياناتك في جهازك. استخدم زر "تصدير CSV" لنسخ بياناتك خارجياً.
  </div>
</div>

<script>
(function(){
  const key = "delivery_app_data_colored_v1";
  let data = JSON.parse(localStorage.getItem(key) || "[]");
  const els = {
    name: document.getElementById("inputName"),
    addr: document.getElementById("inputAddress"),
    phone: document.getElementById("inputPhone"),
    amount: document.getElementById("inputAmount"),
    discount: document.getElementById("inputDiscount"),
    paid: document.getElementById("inputPaid"),
    status: document.getElementById("inputStatus"),
    color: document.getElementById("inputColor"),
    btnAdd: document.getElementById("btnAdd"),
    btnClear: document.getElementById("btnClear"),
    tblBody: document.querySelector("#tbl tbody"),
    count: document.getElementById("count"),
    totalDebt: document.getElementById("totalDebt"),
    totalPaid: document.getElementById("totalPaid"),
    btnExport: document.getElementById("btnExport"),
    btnReset: document.getElementById("btnReset")
  };

  let editIndex = -1;

  function calcAfterDiscount(amount, discount){
    amount = Number(amount)||0; discount = Number(discount)||0;
    return +(amount * (1 - discount/100)).toFixed(2);
  }

  function calcRemaining(afterDiscount, paid){
    afterDiscount = Number(afterDiscount)||0; paid = Number(paid)||0;
    return +(afterDiscount - paid).toFixed(2);
  }

  function save(){
    localStorage.setItem(key, JSON.stringify(data));
    render();
  }

  function addOrUpdate(){
    const name = els.name.value.trim();
    if(!name){ alert("أدخل اسم المحل"); els.name.focus(); return; }
    const addr = els.addr.value.trim();
    const phone = els.phone.value.trim();
    const amount = Number(els.amount.value) || 0;
    const discount = Number(els.discount.value) || 0;
    const paid = Number(els.paid.value) || 0;
    const status = els.status.value;
    const color = els.color.value || "#ffd166";
    const after = calcAfterDiscount(amount, discount);
    const remaining = calcRemaining(after, paid);
    const note = "";

    const record = { name, addr, phone, amount, discount, after, paid, remaining, status, note, color, date: new Date().toISOString() };

    if(editIndex >= 0){
      data[editIndex] = record;
      editIndex = -1;
      els.btnAdd.textContent = "أضف / حفظ";
    } else {
      data.push(record);
    }
    clearInputs();
    save();
  }

  function clearInputs(){
    els.name.value=""; els.addr.value=""; els.phone.value="";
    els.amount.value=""; els.discount.value="0"; els.paid.value="0";
    els.status.value="pending";
    els.color.value="#ffd166";
  }

  function render(){
    els.tblBody.innerHTML = "";
    let totalDebt = 0, totalPaid = 0;
    data.forEach((r,i)=>{
      const tr = document.createElement("tr");
      // row color by remaining/state
      let cls = "";
      if (r.remaining <= 0 && r.paid>0) cls = "status-complete";
      else if (r.status === "pending") cls = "status-pending";
      else if (r.status === "progress") cls = "status-progress";
      if (r.remaining < 0) cls = "status-over"; // overpaid
      tr.className = cls;

      const idxTd = `<td>${i+1}</td>`;
      const nameTd = `<td style="text-align:right">
        <div style="display:flex;align-items:center;justify-content:flex-end;gap:8px">
          <div style="width:12px;height:30px;background:${escapeHtml(r.color)};border-radius:4px;border:1px solid rgba(0,0,0,0.06)"></div>
          <div style="text-align:right">
            <div style="font-weight:700;color:${contrastColor(r.color)}">${escapeHtml(r.name)}</div>
            <div class="small">${escapeHtml(r.addr)}</div>
          </div>
        </div>
      </td>`;
      const phoneTd = `<td>${escapeHtml(r.phone||"-")}</td>`;
      const afterTd = `<td>${formatNumber(r.after)} د.ج</td>`;
      const paidTd = `<td>${formatNumber(r.paid)} د.ج</td>`;
      const remTd = `<td>${formatNumber(r.remaining)} د.ج</td>`;
      const statusLabel = (r.status==="complete")? "مكتمل": (r.status==="progress")? "جاري التنفيذ":"قيد الانتظار";
      const statusTd = `<td>${statusLabel}</td>`;
      const noteTd = `<td>${escapeHtml(r.note||"")}</td>`;
      const actionsTd = `<td>
        <button onclick="appEdit(${i})" style="margin-bottom:4px">تعديل</button><br>
        <button onclick="appDelete(${i})" style="background:#ff5c5c">حذف</button>
      </td>`;

      tr.innerHTML = idxTd + nameTd + phoneTd + afterTd + paidTd + remTd + statusTd + noteTd + actionsTd;
      els.tblBody.appendChild(tr);
      totalDebt += Number(r.remaining) || 0;
      totalPaid += Number(r.paid) || 0;
    });
    els.count.textContent = data.length;
    els.totalDebt.textContent = formatNumber(totalDebt) + " د.ج";
    els.totalPaid.textContent = formatNumber(totalPaid) + " د.ج";
  }

  // helper
  function formatNumber(n){ return Number(n).toLocaleString('en-US'); }
  function escapeHtml(text){ return String(text||"").replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;'); }

  // determine text color contrast (black/white) for readability
  function contrastColor(hex){
    if(!hex) return "#000";
    const c = hex.replace('#','');
    const r = parseInt(c.substr(0,2),16);
    const g = parseInt(c.substr(2,2),16);
    const b = parseInt(c.substr(4,2),16);
    // luminance
    const lum = (0.299*r + 0.587*g + 0.114*b)/255;
    return lum > 0.6 ? '#000' : '#fff';
  }

  // attach global functions for inline onclick
  window.appEdit = function(i){
    const r = data[i];
    editIndex = i;
    els.name.value = r.name;
    els.addr.value = r.addr;
    els.phone.value = r.phone;
    els.amount.value = r.amount;
    els.discount.value = r.discount;
    els.paid.value = r.paid;
    els.status.value = r.status;
    els.color.value = r.color || "#ffd166";
    els.btnAdd.textContent = "حفظ التعديل";
    window.scrollTo({top:0,behavior:'smooth'});
  }

  window.appDelete = function(i){
    if(!confirm("هل تريد حذف السجل؟")) return;
    data.splice(i,1);
    save();
  }

  // export CSV
  function toCSV(){
    if(data.length===0){ alert("لا توجد بيانات للتصدير"); return; }
    const header = ["المحل","العنوان","الهاتف","المبلغ_الأصلي","نسبة_الخصم","المبلغ_بعد_الخصم","المدفوع","المتبقي","الحالة","اللون","تاريخ"];
    const rows = data.map(r=>[
      r.name, r.addr, r.phone, r.amount, r.discount, r.after, r.paid, r.remaining,
      (r.status==="complete")?"مكتمل":(r.status==="progress")?"جاري التنفيذ":"قيد الانتظار",
      r.color, r.date
    ]);
    const csv = [header, ...rows].map(row=>row.map(cell=>`"${String(cell||"").replace(/"/g,'""')}"`).join(",")).join("\n");
    const blob = new Blob([csv], {type:"text/csv;charset=utf-8;"});
    const url = URL.createObjectURL(blob);
    const a = document.createElement('a');
    a.href = url;
    a.download = "delivery_data.csv";
    document.body.appendChild(a); a.click(); a.remove();
    URL.revokeObjectURL(url);
  }

  // reset all
  function resetAll(){
    if(!confirm("سيتم حذف كل السجلات. تابع؟")) return;
    data = [];
    save();
  }

  // events
  els.btnAdd.addEventListener("click", addOrUpdate);
  els.btnClear.addEventListener("click", clearInputs);
  els.btnExport.addEventListener("click", toCSV);
  els.btnReset.addEventListener("click", resetAll);

  // realtime calc when user types: show computed after & remaining as tooltip
  [els.amount, els.discount, els.paid].forEach(el=>{
    el.addEventListener("input", ()=>{
      const a = Number(els.amount.value)||0;
      const d = Number(els.discount.value)||0;
      const p = Number(els.paid.value)||0;
      const after = calcAfterDiscount(a,d);
      const rem = calcRemaining(after,p);
      els.btnAdd.title = `بعد الخصم: ${after} دج — المتبقي: ${rem} دج`;
    });
  });
  
  // *** الكود الجديد لتسجيل الـ Service Worker ***
  if ('serviceWorker' in navigator) {
    window.addEventListener('load', () => {
      navigator.serviceWorker.register('./sw.js').then(registration => {
        console.log('SW registered: ', registration);
      }).catch(registrationError => {
        console.log('SW registration failed: ', registrationError);
      });
    });
  }

  // initial render
  render();

})();
</script>

</body>
</html>
{
  "name": "دفتر التوصيل",
  "short_name": "دفتر التوصيل",
  "start_url": ".",
  "display": "standalone",
  "background_color": "#f6f7fb",
  "theme_color": "#2b6cb0",
  "description": "تطبيق لتتبع ديون المحلات والدفعات.",
  "icons": [
    {
      "src": "icon-192.png",
      "sizes": "192x192",
      "type": "image/png",
      "purpose": "any maskable"
    },
    {
      "src": "icon-512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "any maskable"
    }
  ]
}
const CACHE_NAME = 'delivery-app-cache-v2';
const urlsToCache = [
  '/',
  'index.html',
  'icon-192.png',
  'icon-512.png'
];

// حدث التثبيت: يتم فيه تخزين الملفات
self.addEventListener('install', event => {
  event.waitUntil(
    caches.open(CACHE_NAME)
      .then(cache => {
        console.log('Opened cache');
        return cache.addAll(urlsToCache);
      })
  );
});

// حدث الجلب: يتم فيه تقديم الملفات من ذاكرة التخزين المؤقت
self.addEventListener('fetch', event => {
  event.respondWith(
    caches.match(event.request)
      .then(response => {
        // إذا وجدنا الملف في الكاش، نرجعه
        if (response) {
          return response;
        }
        // وإلا، نجلبه من الشبكة
        return fetch(event.request);
      })
  );
});

// حدث التفعيل: يستخدم لتنظيف ذاكرة التخزين المؤقت القديمة
self.addEventListener('activate', event => {
  const cacheWhitelist = [CACHE_NAME];
  event.waitUntil(
    caches.keys().then(cacheNames => {
      return Promise.all(
        cacheNames.map(cacheName => {
          if (cacheWhitelist.indexOf(cacheName) === -1) {
            return caches.delete(cacheName);
          }
        })
      );
    })
  );
});<img width="1024" height="1024" alt="1000008184" src="https://github.com/user-attachments/assets/39993c59-e955-4af5-9961-bcda52bc95fe" />
