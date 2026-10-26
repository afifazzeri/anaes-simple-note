<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>MO ICU – Task Board</title>
<style>
  *{box-sizing:border-box}
  body{
    margin:0;
    font-family:system-ui,-apple-system,"Segoe UI",Roboto,sans-serif;
    background:#eef2f7; color:#0f172a;
    -webkit-text-size-adjust:100%;
  }

  /* ---------- HEADER ---------- */
  header{
    position:sticky; top:0; z-index:20;
    background:#0f172a; color:#fff;
    padding:12px 18px;
    display:flex; flex-wrap:wrap; gap:12px;
    align-items:center; justify-content:space-between;
    box-shadow:0 2px 12px rgba(0,0,0,.18);
  }
  h1{font-size:17px; margin:0; letter-spacing:.3px; white-space:nowrap}
  h1 span{color:#38bdf8}
  .stats{display:flex; gap:8px; flex-wrap:wrap}
  .stat{
    display:flex; align-items:center; gap:6px;
    font-size:12px; padding:5px 11px; border-radius:999px;
    background:rgba(255,255,255,.08);
    border:1px solid rgba(255,255,255,.15);
    white-space:nowrap;
  }
  .stat b{font-size:14px}
  .stat.urgent b{color:#f87171}
  .stat.semi  b{color:#fbbf24}
  .stat.done  b{color:#4ade80}
  #reset{
    background:transparent; color:#cbd5e1;
    border:1px solid rgba(255,255,255,.25);
    padding:6px 12px; border-radius:8px;
    font-size:12px; cursor:pointer;
  }
  #reset:hover{background:rgba(255,255,255,.1); color:#fff}

  /* ---------- OVERVIEW TABLE ---------- */
  .overview{
    margin:16px 16px 0;
    background:#fff;
    border:1px solid #e2e8f0;
    border-radius:12px;
    box-shadow:0 1px 3px rgba(15,23,42,.07);
    overflow:hidden;
  }
  .overview h2{
    font-size:12px; margin:0; padding:10px 14px;
    background:#f1f5f9; border-bottom:1px solid #e2e8f0;
    text-transform:uppercase; letter-spacing:.7px; color:#475569;
    display:flex; justify-content:space-between; align-items:center; gap:8px;
  }
  .overview h2 small{
    text-transform:none; letter-spacing:0; font-weight:500;
    color:#94a3b8; font-size:11px;
  }
  .table-wrap{overflow-x:auto}
  table{width:100%; border-collapse:collapse; font-size:12.5px; min-width:680px}
  thead th{
    text-align:left; padding:8px 12px; font-size:10px;
    text-transform:uppercase; letter-spacing:.6px;
    color:#64748b; background:#f8fafc;
    border-bottom:1px solid #e2e8f0; white-space:nowrap;
  }
  tbody td{
    padding:8px 12px; border-bottom:1px solid #f1f5f9;
    vertical-align:top;
  }
  tbody tr:last-child td{border-bottom:none}
  tbody tr:hover{background:#f8fafc}
  tbody tr.row-urgent{background:#fef2f2}
  tbody tr.row-urgent:hover{background:#fee2e2}
  .bed-cell{font-weight:800; white-space:nowrap}
  tr.row-urgent .bed-cell{color:#dc2626}
  td.dim{color:#94a3b8}
  td.time{font-weight:700; color:#475569; white-space:nowrap}
  td.done-list{color:#64748b; font-size:11.5px; max-width:260px}
  .badge{
    font-size:9.5px; font-weight:800; letter-spacing:.5px;
    text-transform:uppercase; padding:2px 7px; border-radius:999px; color:#fff;
    display:inline-block;
  }
  .badge.urgent{background:#dc2626}
  .badge.semi  {background:#f59e0b}

  /* ---------- GRID ---------- */
  main{
    padding:12px 16px 16px;
    display:grid;
    grid-template-columns:repeat(auto-fill,minmax(320px,1fr));
    gap:14px;
    align-items:start;
  }

  /* ---------- BED CARD ---------- */
  .bed{
    background:#fff;
    border:1px solid #e2e8f0;
    border-top:4px solid #cbd5e1;
    border-radius:12px;
    padding:12px;
    display:flex; flex-direction:column; gap:10px;
    box-shadow:0 1px 3px rgba(15,23,42,.07);
  }
  .bed.has-urgent{border-top-color:#dc2626}

  .bed-head{display:flex; align-items:center; justify-content:space-between}
  .bed-no{font-weight:800; font-size:15px; letter-spacing:.3px; color:#0f172a}
  .bed.has-urgent .bed-no{color:#dc2626}

  .fields{display:grid; grid-template-columns:1fr 1fr; gap:8px}
  label{
    font-size:10px; font-weight:700; color:#64748b;
    text-transform:uppercase; letter-spacing:.6px;
    display:flex; flex-direction:column; gap:3px;
  }

  input[type=text], input[type=time], select{
    width:100%; min-width:0;
    padding:7px 8px;
    border:1px solid #cbd5e1; border-radius:8px;
    font:inherit; font-size:13px;
    background:#f8fafc; color:#0f172a;
  }
  input:focus, select:focus{
    outline:2px solid #38bdf8; outline-offset:-1px;
    border-color:#38bdf8; background:#fff;
  }

  /* ---------- TASK FORM ---------- */
  .task-form{
    display:flex; flex-direction:column; gap:6px;
    border-top:1px dashed #e2e8f0; padding-top:10px;
  }
  .task-form .row{
    display:grid; grid-template-columns:1fr 1fr auto; gap:6px;
  }
  .task-form button{
    padding:7px 14px; border:none; border-radius:8px;
    background:#0ea5e9; color:#fff; font-weight:700; font-size:13px;
    cursor:pointer;
  }
  .task-form button:hover{background:#0284c7}
  .task-form button:active{transform:scale(.97)}

  /* ---------- TASK LIST ---------- */
  .task-list{
    list-style:none; margin:0; padding:0;
    display:flex; flex-direction:column; gap:6px;
  }
  .task{
    display:flex; align-items:flex-start; gap:8px;
    padding:8px 9px; border-radius:9px;
    background:#f8fafc;
    border-left:4px solid #cbd5e1;
  }
  .task.p-urgent{border-left-color:#dc2626; background:#fef2f2}
  .task.p-semi  {border-left-color:#f59e0b; background:#fffbeb}
  .task.done{opacity:.45}
  .task.done .task-text{text-decoration:line-through}

  .task input[type=checkbox]{
    margin-top:2px; width:16px; height:16px;
    accent-color:#0ea5e9; cursor:pointer; flex:none;
  }
  .task-body{flex:1; min-width:0}
  .task-text{font-size:13px; line-height:1.35; word-break:break-word}
  .task-meta{display:flex; flex-wrap:wrap; gap:6px; align-items:center; margin-top:4px}
  .time{font-size:11px; font-weight:700; color:#475569}
  .del{
    background:none; border:none; color:#94a3b8;
    font-size:19px; line-height:1; cursor:pointer;
    padding:0 2px; flex:none;
  }
  .del:hover{color:#dc2626}
  .empty{font-size:12px; font-style:italic; color:#94a3b8; padding:2px 4px}

  footer{
    text-align:center; font-size:11px; color:#94a3b8;
    padding:10px 16px 26px;
  }

  @media print{
    header{position:static; background:#fff; color:#000}
    .task-form, .del, #reset, footer{display:none}
    .overview{margin:8px 0; box-shadow:none}
    main{grid-template-columns:repeat(3,1fr); padding:8px 0}
    .bed{break-inside:avoid; box-shadow:none}
    table{min-width:0; font-size:10px}
  }
</style>
</head>
<body>

<header>
  <h1>🫀 MO <span>ICU</span> – Task Board</h1>
  <div class="stats">
    <span class="stat urgent">Urgent <b id="c-urgent">0</b></span>
    <span class="stat semi">Semi-urgent <b id="c-semi">0</b></span>
    <span class="stat done">Done <b id="c-done">0</b></span>
  </div>
  <button id="reset" type="button">Reset all</button>
</header>

<!-- ---------- OVERVIEW TABLE ---------- -->
<section class="overview">
  <h2>Shift Overview <small>click a row to jump to that bed</small></h2>
  <div class="table-wrap">
    <table>
      <thead>
        <tr>
          <th>Bed</th>
          <th>Patient</th>
          <th>RN</th>
          <th>Priority</th>
          <th>Next Task</th>
          <th>Time</th>
          <th>Done</th>
        </tr>
      </thead>
      <tbody id="overview-body"></tbody>
    </table>
  </div>
</section>

<main id="beds"></main>

<footer>Data is saved automatically in this browser. Refresh-safe.</footer>

<script>
(function () {
  "use strict";

  const BED_COUNT   = 9;
  const STORAGE_KEY = "mo-icu-board-v1";

  /* ---------------- STATE ---------------- */
  function defaultState() {
    const beds = {};
    for (let i = 1; i <= BED_COUNT; i++) {
      beds[i] = { patient: "", rn: "", tasks: [] };
    }
    return { beds };
  }

  function loadState() {
    try {
      const raw = localStorage.getItem(STORAGE_KEY);
      if (!raw) return defaultState();
      const parsed = JSON.parse(raw);
      const s = defaultState();
      if (parsed && parsed.beds) {
        for (let i = 1; i <= BED_COUNT; i++) {
          const b = parsed.beds[i];
          if (b) {
            s.beds[i] = {
              patient: typeof b.patient === "string" ? b.patient : "",
              rn:      typeof b.rn === "string" ? b.rn : "",
              tasks:   Array.isArray(b.tasks) ? b.tasks : []
            };
          }
        }
      }
      return s;
    } catch (e) {
      return defaultState();
    }
  }

  let state = loadState();

  function save() {
    try { localStorage.setItem(STORAGE_KEY, JSON.stringify(state)); } catch (e) {}
  }

  const uid = () =>
    Date.now().toString(36) + Math.random().toString(36).slice(2, 7);

  /* ---------------- SORTING ---------------- */
  // 1) undone before done
  // 2) urgent before semi-urgent
  // 3) earliest time first (no time = last)
  const PRIORITY_RANK = { urgent: 0, semi: 1 };

  function sortTasks(tasks) {
    return tasks.slice().sort((a, b) => {
      if (a.done !== b.done) return a.done ? 1 : -1;
      const ra = PRIORITY_RANK[a.priority] ?? 1;
      const rb = PRIORITY_RANK[b.priority] ?? 1;
      if (ra !== rb) return ra - rb;
      const ta = a.time || "99:99";
      const tb = b.time || "99:99";
      return ta.localeCompare(tb);
    });
  }

  /* ---------------- BUILD UI ---------------- */
  const bedsEl = document.getElementById("beds");

  function buildBoard() {
    bedsEl.innerHTML = "";
    for (let i = 1; i <= BED_COUNT; i++) {
      bedsEl.appendChild(buildBed(i));
      renderTasks(i);
    }
    updateStats();
  }

  function buildBed(n) {
    const bed = state.beds[n];

    const section = document.createElement("section");
    section.className = "bed";
    section.id = "bed-" + n;
    section.innerHTML = `
      <div class="bed-head"><span class="bed-no">Bed ${n}</span></div>

      <div class="fields">
        <label>Patient<input class="in-patient" type="text" placeholder="Name" autocomplete="off"></label>
        <label>RN<input class="in-rn" type="text" placeholder="Name" autocomplete="off"></label>
      </div>

      <form class="task-form" autocomplete="off">
        <input class="in-task" type="text" placeholder="New task (e.g. ABG, adjust noradrenaline)" required>
        <div class="row">
          <select class="in-priority">
            <option value="urgent">🔴 Urgent</option>
            <option value="semi" selected>🟡 Semi-urgent</option>
          </select>
          <input class="in-time" type="time" title="Time to do it">
          <button type="submit">Add</button>
        </div>
      </form>

      <ul class="task-list"></ul>
    `;

    /* patient + RN */
    const patientInput = section.querySelector(".in-patient");
    const rnInput      = section.querySelector(".in-rn");
    patientInput.value = bed.patient;
    rnInput.value      = bed.rn;

    patientInput.addEventListener("input", e => {
      bed.patient = e.target.value; save(); updateStats();
    });
    rnInput.addEventListener("input", e => {
      bed.rn = e.target.value; save(); updateStats();
    });

    /* add task */
    const form        = section.querySelector(".task-form");
    const taskInput   = section.querySelector(".in-task");
    const prioritySel = section.querySelector(".in-priority");
    const timeInput   = section.querySelector(".in-time");

    form.addEventListener("submit", e => {
      e.preventDefault();
      const text = taskInput.value.trim();
      if (!text) return;

      bed.tasks.push({
        id: uid(),
        text: text,
        priority: prioritySel.value,
        time: timeInput.value,
        done: false
      });

      taskInput.value = "";
      timeInput.value = "";
      save();
      renderTasks(n);
      updateStats();
      taskInput.focus();
    });

    return section;
  }

  /* ---------------- RENDER TASKS ---------------- */
  function renderTasks(n) {
    const bed = state.beds[n];
    const ul  = document.querySelector("#bed-" + n + " .task-list");
    if (!ul) return;

    ul.innerHTML = "";
    const tasks = sortTasks(bed.tasks);

    if (tasks.length === 0) {
      const li = document.createElement("li");
      li.className = "empty";
      li.textContent = "No tasks yet";
      ul.appendChild(li);
      return;
    }

    for (const t of tasks) {
      const li = document.createElement("li");
      li.className = "task p-" + (t.priority === "urgent" ? "urgent" : "semi") +
                     (t.done ? " done" : "");

      /* checkbox */
      const cb = document.createElement("input");
      cb.type = "checkbox";
      cb.checked = !!t.done;
      cb.title = "Mark as done";
      cb.addEventListener("change", () => {
        t.done = cb.checked;
        save();
        renderTasks(n);
        updateStats();
      });

      /* body */
      const body = document.createElement("div");
      body.className = "task-body";

      const txt = document.createElement("div");
      txt.className = "task-text";
      txt.textContent = t.text;

      const meta = document.createElement("div");
      meta.className = "task-meta";

      const badge = document.createElement("span");
      badge.className = "badge " + (t.priority === "urgent" ? "urgent" : "semi");
      badge.textContent = t.priority === "urgent" ? "Urgent" : "Semi-urgent";
      meta.appendChild(badge);

      if (t.time) {
        const timeEl = document.createElement("span");
        timeEl.className = "time";
        timeEl.textContent = "⏰ " + t.time;
        meta.appendChild(timeEl);
      }

      body.appendChild(txt);
      body.appendChild(meta);

      /* delete */
      const del = document.createElement("button");
      del.type = "button";
      del.className = "del";
      del.textContent = "×";
      del.title = "Delete task";
      del.addEventListener("click", () => {
        bed.tasks = bed.tasks.filter(x => x.id !== t.id);
        save();
        renderTasks(n);
        updateStats();
      });

      li.appendChild(cb);
      li.appendChild(body);
      li.appendChild(del);
      ul.appendChild(li);
    }
  }

  /* ---------------- OVERVIEW TABLE ---------------- */
  function makeCell(text, cls) {
    const td = document.createElement("td");
    if (cls) td.className = cls;
    td.textContent = text;
    return td;
  }

  function truncate(s, n) {
    return s.length > n ? s.slice(0, n - 1) + "…" : s;
  }

  function renderTable() {
    const tbody = document.getElementById("overview-body");
    if (!tbody) return;
    tbody.innerHTML = "";

    for (let i = 1; i <= BED_COUNT; i++) {
      const bed = state.beds[i];

      // unfinished tasks, already sorted urgent-first then by time
      const pending = sortTasks(bed.tasks).filter(t => !t.done);
      const next    = pending[0] || null;

      const hasUrgent = pending.some(t => t.priority === "urgent");
      const hasSemi   = pending.some(t => t.priority === "semi");

      const doneTasks = bed.tasks.filter(t => t.done);

      const tr = document.createElement("tr");
      if (hasUrgent) tr.className = "row-urgent";

      /* Bed */
      const tdBed = document.createElement("td");
      tdBed.className = "bed-cell";
      tdBed.textContent = "Bed " + i;
      tr.appendChild(tdBed);

      /* Patient / RN */
      tr.appendChild(makeCell(bed.patient || "—", bed.patient ? "" : "dim"));
      tr.appendChild(makeCell(bed.rn || "—", bed.rn ? "" : "dim"));

      /* Priority (highest pending priority for this bed) */
      const tdPri = document.createElement("td");
      if (hasUrgent) {
        const b = document.createElement("span");
        b.className = "badge urgent";
        b.textContent = "Urgent";
        tdPri.appendChild(b);
      } else if (hasSemi) {
        const b = document.createElement("span");
        b.className = "badge semi";
        b.textContent = "Semi-urgent";
        tdPri.appendChild(b);
      } else {
        tdPri.textContent = "—";
        tdPri.className = "dim";
      }
      tr.appendChild(tdPri);

      /* Next task + time */
      const tdNext = document.createElement("td");
      const tdTime = document.createElement("td");
      if (next) {
        tdNext.textContent = next.text;
        if (next.time) {
          tdTime.textContent = next.time;
          tdTime.className = "time";
        } else {
          tdTime.textContent = "—";
          tdTime.className = "dim";
        }
      } else if (bed.tasks.length > 0) {
        tdNext.textContent = "All done ✅";
        tdNext.className = "dim";
        tdTime.textContent = "—";
        tdTime.className = "dim";
      } else {
        tdNext.textContent = "—";
        tdNext.className = "dim";
        tdTime.textContent = "—";
        tdTime.className = "dim";
      }
      tr.appendChild(tdNext);
      tr.appendChild(tdTime);

      /* Done */
      const tdDone = document.createElement("td");
      tdDone.className = "done-list";
      if (doneTasks.length === 0) {
        tdDone.textContent = "—";
        tdDone.classList.add("dim");
      } else {
        const list = doneTasks.map(t => truncate(t.text, 40)).join(" · ");
        tdDone.textContent = list;
        tdDone.title = doneTasks.map(t => t.text).join("\n");
      }
      tr.appendChild(tdDone);

      /* Click row → scroll to bed card */
      tr.style.cursor = "pointer";
      tr.addEventListener("click", () => {
        const card = document.getElementById("bed-" + i);
        if (card) card.scrollIntoView({ behavior: "smooth", block: "center" });
      });

      tbody.appendChild(tr);
    }
  }

  /* ---------------- STATS / HIGHLIGHTS ---------------- */
  function updateStats() {
    let urgent = 0, semi = 0, done = 0;

    for (let i = 1; i <= BED_COUNT; i++) {
      const bed = state.beds[i];
      let hasUrgent = false;

      for (const t of bed.tasks) {
        if (t.done) { done++; continue; }
        if (t.priority === "urgent") { urgent++; hasUrgent = true; }
        else { semi++; }
      }

      const el = document.getElementById("bed-" + i);
      if (el) el.classList.toggle("has-urgent", hasUrgent);
    }

    document.getElementById("c-urgent").textContent = urgent;
    document.getElementById("c-semi").textContent   = semi;
    document.getElementById("c-done").textContent   = done;

    renderTable();
  }

  /* ---------------- RESET ---------------- */
  document.getElementById("reset").addEventListener("click", () => {
    if (!confirm("Clear ALL patients, RNs and tasks?")) return;
    state = defaultState();
    save();
    buildBoard();
  });

  /* ---------------- GO ---------------- */
  buildBoard();
})();
</script>
</body>
</html>