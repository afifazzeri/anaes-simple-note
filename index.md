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

  /* ---------- GRID ---------- */
  main{
    padding:16px;
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
  .badge{
    font-size:9.5px; font-weight:800; letter-spacing:.5px;
    text-transform:uppercase; padding:2px 7px; border-radius:999px; color:#fff;
  }
  .badge.urgent{background:#dc2626}
  .badge.semi  {background:#f59e0b}
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
    main{grid-template-columns:repeat(3,1fr)}
    .bed{break-inside:avoid; box-shadow:none}
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

<main id="beds"></main>

<footer>Data is saved automatically in this browser. Add <b>?bed=3</b> style filters? No – keep it simple. 😊</footer>

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

    patientInput.addEventListener("input", e => { bed.patient = e.target.value; save(); });
    rnInput.addEventListener("input",      e => { bed.rn      = e.target.value; save(); });

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