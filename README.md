<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no"/>
<title>SACCO Payment Tracker</title>

<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="apple-mobile-web-app-title" content="SACCO App">
<meta name="theme-color" content="#1a6b3c">
<link rel="apple-touch-icon" href="https://cdn-icons-png.flaticon.com/512/6165/6165579.png">

<link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@600;700&family=Plus+Jakarta+Sans:wght@300;400;500;600&display=swap" rel="stylesheet"/>

<style>
  :root {
    --bg: #f5f3ee;
    --surface: #ece9e2;
    --card: #ffffff;
    --border: #ddd9d0;
    --accent: #1a6b3c;
    --accent-light: #e8f5ee;
    --accent2: #c8860a;
    --accent2-light: #fef3e0;
    --danger: #c0392b;
    --danger-light: #fdecea;
    --text: #1a1a1a;
    --muted: #888074;
    --paid-bg: #1a6b3c;
    --unpaid-bg: #e5e1d8;
    --radius: 10px;
    --shadow: 0 2px 12px rgba(0,0,0,.07);
  }

  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    font-family: 'Plus Jakarta Sans', sans-serif;
    background: var(--bg);
    color: var(--text);
    min-height: 100vh;
    padding: 28px 18px;
    -webkit-tap-highlight-color: transparent;
  }

  /* ── Header ── */
  .app-header {
    max-width: 1280px;
    margin: 0 auto 28px;
    display: flex;
    align-items: center;
    justify-content: space-between;
    flex-wrap: wrap;
    gap: 16px;
  }

  .logo-area { display: flex; align-items: center; gap: 14px; }
  .logo-icon {
    width: 48px; height: 48px;
    background: var(--accent);
    border-radius: 12px;
    display: flex; align-items: center; justify-content: center;
    font-size: 1.4rem;
    flex-shrink: 0;
    box-shadow: 0 4px 12px rgba(26,107,60,.25);
  }
  .group-name-input {
    background: transparent;
    border: none;
    border-bottom: 1.5px solid var(--border);
    color: var(--text);
    font-family: 'Playfair Display', serif;
    font-size: clamp(1.4rem, 3vw, 2rem);
    font-weight: 700;
    outline: none;
    min-width: 180px;
    max-width: 320px;
    transition: border-color .2s;
  }
  .group-name-input:focus { border-color: var(--accent); }

  /* ── Stats ── */
  .stats-bar {
    max-width: 1280px;
    margin: 0 auto 24px;
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
    gap: 14px;
  }
  .stat-card {
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 16px 18px;
    box-shadow: var(--shadow);
    position: relative;
    overflow: hidden;
  }
  .stat-card::before { content: ''; position: absolute; top: 0; left: 0; right: 0; height: 3px; }
  .stat-card.green::before { background: var(--accent); }
  .stat-card.gold::before  { background: var(--accent2); }
  .stat-card.red::before   { background: var(--danger); }
  .stat-card.blue::before  { background: #2563eb; }

  .stat-label { font-size: 0.7rem; text-transform: uppercase; letter-spacing: 1px; color: var(--muted); margin-bottom: 6px; }
  .stat-value { font-size: 1.4rem; font-weight: 700; }
  .stat-value.green { color: var(--accent); }

  /* ── Table & Layout ── */
  .table-wrap {
    max-width: 1280px;
    margin: 0 auto 32px;
    background: var(--card);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    overflow-x: auto;
    box-shadow: var(--shadow);
  }

  table { width: 100%; border-collapse: collapse; font-size: 0.85rem; }
  th { padding: 12px 13px; text-align: left; font-size: 0.68rem; text-transform: uppercase; color: var(--muted); border-bottom: 1.5px solid var(--border); }
  td { padding: 10px 13px; border-bottom: 1px solid #f0ece4; vertical-align: middle; }

  /* ── UI Elements ── */
  .btn {
    display: inline-flex; align-items: center; gap: 6px;
    padding: 9px 16px; border-radius: 8px; font-size: 0.82rem; font-weight: 600;
    border: none; cursor: pointer; transition: all .15s; white-space: nowrap;
  }
  .btn-primary { background: var(--accent); color: #fff; }
  .btn-danger { background: var(--danger-light); color: var(--danger); }
  .btn-week { background: var(--accent-light); color: var(--accent); border: 1.5px solid #b7ddc8; }

  .pay-check { display: none; }
  .check-box {
    width: 26px; height: 26px; border-radius: 7px; border: 2px solid var(--border);
    background: var(--unpaid-bg); display: flex; align-items: center; justify-content: center;
    transition: all .18s; position: relative; cursor: pointer;
  }
  .pay-check:checked + .check-label .check-box { background: var(--paid-bg); border-color: var(--paid-bg); }
  
  .amt-input {
    width: 82px; background: #f5f3ee; border: 1px solid var(--border);
    border-radius: 5px; font-size: 0.75rem; padding: 4px 6px; text-align: right; margin-top: 5px;
  }

  /* ── Modals & Overlay ── */
  .overlay {
    display: none; position: fixed; inset: 0; background: rgba(0,0,0,.45);
    backdrop-filter: blur(3px); z-index: 100; align-items: center; justify-content: center; padding: 24px;
  }
  .overlay.open { display: flex; }
  .modal { background: var(--card); border-radius: 14px; padding: 28px; width: 100%; max-width: 420px; }

  /* ── Toast ── */
  .toast {
    position: fixed; bottom: 24px; right: 24px; background: var(--accent); color: #fff;
    border-radius: 10px; padding: 12px 20px; font-size: 0.83rem; opacity: 0; transition: .3s; z-index: 200;
  }
  .toast.show { opacity: 1; transform: translateY(-10px); }

  /* (Additional original styles maintained for UI consistency) */
</style>
</head>
<body>

<header class="app-header">
  <div class="logo-area">
    <div class="logo-icon">🌱</div>
    <div>
      <input class="group-name-input" id="groupName" placeholder="SACCO Name..." />
      <div style="font-size: 0.78rem; color: var(--muted);">Weekly Payment Tracker</div>
    </div>
  </div>
  <div style="display: flex; gap: 10px;">
    <button class="btn btn-primary" style="background: var(--accent2);" onclick="exportCSV()">⬇ Export</button>
    <button class="btn btn-danger" onclick="clearAll()">🗑 Clear</button>
  </div>
</header>

<div class="stats-bar" id="statsBar">
  <div class="stat-card green"><div class="stat-label">Members</div><div class="stat-value" id="statMembers">0</div></div>
  <div class="stat-card gold"><div class="stat-label">Collected</div><div class="stat-value" id="statTotal">UGX 0</div></div>
  <div class="stat-card blue"><div class="stat-label">Weeks</div><div class="stat-value" id="statWeeks">0</div></div>
  <div class="stat-card red"><div class="stat-label">Unpaid</div><div class="stat-value" id="statUnpaid">0</div></div>
</div>

<div style="max-width: 1280px; margin: 0 auto 18px; display: flex; gap: 10px; flex-wrap: wrap;">
  <button class="btn btn-primary" onclick="openAddMember()">+ Add Member</button>
  <button class="btn btn-week" onclick="toggleWeekStrip()">📅 Auto-Generate Weeks</button>
  <button class="btn btn-primary" style="background:#555" onclick="openAddColumn()">+ Custom Column</button>
  <input id="searchInput" placeholder="Search..." oninput="renderTable()" style="margin-left:auto; padding: 8px; border-radius:8px; border:1px solid var(--border);" />
</div>

<div class="week-strip" id="weekStrip" style="display:none; background: var(--accent-light); padding: 15px; border-radius: 10px; margin-bottom: 20px; gap: 15px; align-items: flex-end; flex-wrap: wrap;">
  <div><label style="display:block; font-size: 10px;">START DATE</label><input type="date" id="weekStart"></div>
  <div><label style="display:block; font-size: 10px;">HOW MANY WEEKS?</label><input type="number" id="weekCount" value="4" style="width:60px"></div>
  <div><label style="display:block; font-size: 10px;">UGX PER WEEK</label><input type="number" id="weekAmount" value="10000" style="width:100px"></div>
  <button class="btn btn-primary" onclick="generateWeeks()">Create Columns</button>
</div>

<div class="table-wrap">
  <table id="mainTable">
    <thead><tr id="headerRow"></tr></thead>
    <tbody id="tableBody"></tbody>
  </table>
</div>

<div class="overlay" id="memberModal">
  <div class="modal">
    <h3 id="memberModalTitle">Add Member</h3><br>
    <input id="memberName" placeholder="Full Name" style="width:100%; padding:10px; margin-bottom:10px;"><br>
    <input id="memberPhone" placeholder="Phone Number" style="width:100%; padding:10px; margin-bottom:20px;">
    <div style="text-align:right;">
      <button class="btn" onclick="closeModal('memberModal')">Cancel</button>
      <button class="btn btn-primary" onclick="saveMember()">Save</button>
    </div>
  </div>
</div>

<div class="overlay" id="columnModal">
  <div class="modal">
    <h3>Add Custom Column</h3><br>
    <input id="colName" placeholder="Column Name (e.g. Reg Fee)" style="width:100%; padding:10px; margin-bottom:10px;"><br>
    <input id="colAmount" type="number" placeholder="Default Amount" style="width:100%; padding:10px; margin-bottom:20px;">
    <div style="text-align:right;">
      <button class="btn" onclick="closeModal('columnModal')">Cancel</button>
      <button class="btn btn-primary" onclick="saveColumn()">Add</button>
    </div>
  </div>
</div>

<div class="toast" id="toast"></div>

<script>
// ── Application State ──
let state = {
  groupName: '',
  members: [],
  columns: [],
  payments: {} 
};
let editingMemberId = null;
let nextMid = 1, nextCid = 1;

// ── Persistence Engine ──
function save() {
  try {
    const data = JSON.stringify({ state, nextMid, nextCid });
    localStorage.setItem('sacco_v2_storage', data);
  } catch(e) { console.error("Save Error", e); }
}

function load() {
  try {
    const data = localStorage.getItem('sacco_v2_storage');
    if (data) {
      const parsed = JSON.parse(data);
      state = parsed.state;
      nextMid = parsed.nextMid || 1;
      nextCid = parsed.nextCid || 1;
    }
  } catch(e) { console.error("Load Error", e); }
}

// ── Core Logic ──
function fmt(n) { return Number(n).toLocaleString('en-UG'); }
function esc(s) { return String(s||'').replace(/</g,'&lt;'); }

function getP(mid, cid) {
  if (!state.payments[mid]) state.payments[mid] = {};
  if (!state.payments[mid][cid]) {
    const col = state.columns.find(c => c.id === cid);
    state.payments[mid][cid] = { paid: false, amount: col ? col.defaultAmount : 0 };
  }
  return state.payments[mid][cid];
}

function renderTable() {
  const q = document.getElementById('searchInput').value.toLowerCase();
  const members = state.members.filter(m => m.name.toLowerCase().includes(q));

  // Build Headers
  let hHtml = `<th>#</th><th>Member</th>`;
  state.columns.forEach(c => {
    hHtml += `<th style="text-align:center;">${esc(c.name)}<br><small>UGX ${fmt(c.defaultAmount)}</small> <button onclick="removeColumn('${c.id}')" style="border:none; background:none; color:red; cursor:pointer;">×</button></th>`;
  });
  hHtml += `<th>Total</th><th>Actions</th>`;
  document.getElementById('headerRow').innerHTML = hHtml;

  // Build Rows
  const body = document.getElementById('tableBody');
  if (members.length === 0) {
    body.innerHTML = `<tr><td colspan="100" style="text-align:center; padding:40px;">No members found.</td></tr>`;
    renderStats(); return;
  }

  body.innerHTML = members.map((m, i) => {
    let rowTotal = 0;
    const cells = state.columns.map(c => {
      const p = getP(m.id, c.id);
      if (p.paid) rowTotal += p.amount;
      const cbId = `cb_${m.id}_${c.id}`;
      return `<td style="text-align:center;">
        <input type="checkbox" class="pay-check" id="${cbId}" ${p.paid ? 'checked' : ''} onchange="togglePaid('${m.id}','${c.id}',this.checked)">
        <label class="check-label" for="${cbId}"><div class="check-box"></div></label>
        <input class="amt-input" type="number" value="${p.amount}" onchange="setAmt('${m.id}','${c.id}',this.value)">
      </td>`;
    }).join('');

    return `<tr>
      <td>${i+1}</td>
      <td><strong>${esc(m.name)}</strong><br><small>${esc(m.phone)}</small></td>
      ${cells}
      <td style="color:var(--accent); font-weight:bold;">UGX ${fmt(rowTotal)}</td>
      <td>
        <button onclick="openEditMember('${m.id}')">✏️</button>
        <button onclick="removeMember('${m.id}')">🗑</button>
      </td>
    </tr>`;
  }).join('');
  
  renderStats();
}

function renderStats() {
  let total = 0, unpaid = 0;
  state.members.forEach(m => {
    state.columns.forEach(c => {
      const p = getP(m.id, c.id);
      if (p.paid) total += p.amount; else unpaid++;
    });
  });
  document.getElementById('statMembers').innerText = state.members.length;
  document.getElementById('statTotal').innerText = 'UGX ' + fmt(total);
  document.getElementById('statWeeks').innerText = state.columns.length;
  document.getElementById('statUnpaid').innerText = unpaid;
}

// ── Actions ──
function togglePaid(mid, cid, checked) {
  getP(mid, cid).paid = checked;
  save(); renderTable();
}

function setAmt(mid, cid, val) {
  getP(mid, cid).amount = parseFloat(val) || 0;
  save(); renderTable();
}

function saveMember() {
  const name = document.getElementById('memberName').value.trim();
  const phone = document.getElementById('memberPhone').value.trim();
  if (!name) return alert("Name is required");

  if (editingMemberId) {
    const m = state.members.find(x => x.id === editingMemberId);
    m.name = name; m.phone = phone;
  } else {
    state.members.push({ id: 'm' + nextMid++, name, phone });
  }
  closeModal('memberModal'); save(); renderTable(); toast("Member Saved");
}

function saveColumn() {
  const name = document.getElementById('colName').value.trim();
  const amount = parseFloat(document.getElementById('colAmount').value) || 0;
  if (!name) return alert("Name required");
  
  const id = 'c' + nextCid++;
  state.columns.push({ id, name, defaultAmount: amount });
  closeModal('columnModal'); save(); renderTable();
}

function generateWeeks() {
  const startVal = document.getElementById('weekStart').value;
  const count = parseInt(document.getElementById('weekCount').value) || 1;
  const amt = parseFloat(document.getElementById('weekAmount').value) || 0;
  if (!startVal) return alert("Select start date");

  let d = new Date(startVal);
  for (let i = 0; i < count; i++) {
    const label = `Wk ${i+1} (${d.toLocaleDateString('en-UG', {day:'2-digit', month:'short'})})`;
    state.columns.push({ id: 'c' + nextCid++, name: label, defaultAmount: amt });
    d.setDate(d.getDate() + 7);
  }
  save(); renderTable(); toggleWeekStrip(); toast("Weeks Created");
}

function exportCSV() {
  let csv = "Name,Phone," + state.columns.map(c => c.name).join(",") + ",Total\n";
  state.members.forEach(m => {
    let row = `${m.name},${m.phone}`;
    let total = 0;
    state.columns.forEach(c => {
      const p = getP(m.id, c.id);
      row += `,${p.paid ? p.amount : 0}`;
      if (p.paid) total += p.amount;
    });
    csv += `${row},${total}\n`;
  });
  const blob = new Blob([csv], { type: 'text/csv' });
  const url = window.URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.setAttribute('hidden', '');
  a.setAttribute('href', url);
  a.setAttribute('download', 'sacco_report.csv');
  document.body.appendChild(a);
  a.click();
  document.body.removeChild(a);
}

function clearAll() {
  if (confirm("Delete EVERYTHING?")) {
    localStorage.removeItem('sacco_v2_storage');
    location.reload();
  }
}

// ── UI Helpers ──
function openAddMember() { editingMemberId = null; document.getElementById('memberName').value=''; openModal('memberModal'); }
function openEditMember(id) { editingMemberId = id; const m = state.members.find(x=>x.id===id); document.getElementById('memberName').value=m.name; openModal('memberModal'); }
function openModal(id) { document.getElementById(id).classList.add('open'); }
function closeModal(id) { document.getElementById(id).classList.remove('open'); }
function toggleWeekStrip() { const s = document.getElementById('weekStrip'); s.style.display = s.style.display === 'none' ? 'flex' : 'none'; }
function toast(m) { const t = document.getElementById('toast'); t.innerText = m; t.classList.add('show'); setTimeout(() => t.classList.remove('show'), 2000); }
function removeMember(id) { state.members = state.members.filter(m=>m.id!==id); save(); renderTable(); }
function removeColumn(id) { state.columns = state.columns.filter(c=>c.id!==id); save(); renderTable(); }

// ── Init ──
window.onload = () => {
  load();
  document.getElementById('groupName').value = state.groupName || '';
  document.getElementById('groupName').oninput = (e) => { state.groupName = e.target.value; save(); };
  renderTable();
};
</script>
</body>
</html>
