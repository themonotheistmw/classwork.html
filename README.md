# classwork.html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Class Work &amp; Homework | Abu Bakar Memorial Trust</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
<style>
:root{--ink:#14264f;--gold:#c8a24a;--paper:#f7f4ec;--line:#ddd5c2;--mute:#666}
*{box-sizing:border-box}
body{margin:0;background:var(--paper);color:#222;font:16px/1.55 Georgia,serif}
.wrap{max-width:760px;margin:0 auto;padding:20px 14px 60px}
h1{color:var(--ink);font-size:1.6rem;margin:0 0 12px}
.topbar{display:flex;justify-content:space-between;align-items:center;gap:10px;margin-bottom:14px;color:var(--mute);font-size:.95rem;flex-wrap:wrap}
.tabs{display:flex;gap:8px;margin-bottom:18px}
.tab{padding:9px 14px;border:1px solid var(--gold);background:none;border-radius:8px;font:inherit;cursor:pointer;color:var(--ink)}
.tab.on{background:var(--ink);color:#fff;border-color:var(--ink)}
label{display:flex;flex-direction:column;gap:4px;margin-bottom:12px;font-weight:bold;color:var(--ink);font-size:.95rem}
input,select,textarea{padding:10px;border:1px solid var(--line);border-radius:8px;font:inherit;background:#fff;color:#222;width:100%}
.row{display:flex;gap:12px;flex-wrap:wrap;margin-bottom:12px}
.row label{flex:1;min-width:200px}
.btn{display:inline-block;padding:10px 16px;border:0;border-radius:8px;background:var(--gold);color:var(--ink);font:inherit;font-weight:bold;cursor:pointer;text-decoration:none;margin:4px 4px 4px 0}
.btn.alt{background:#fff;border:1px solid var(--gold)}
.msg{color:#555;min-height:1.4em;font-size:.95rem}
.card{width:700px;max-width:100%;background:#fff;border:1px solid var(--line);border-radius:10px;overflow:hidden}
.head{background:var(--ink);color:#fff;text-align:center;padding:18px;border-bottom:3px solid var(--gold)}
.school{color:#e0b962;font-size:.9rem;letter-spacing:.06em}
.ttl{font-size:1.4rem;font-weight:bold;margin:4px 0}
.body{padding:14px 22px}
.item{padding:12px 0;border-bottom:1px solid #eee5d2}
.item:last-child{border-bottom:0}
.subj{font-size:1.1rem;font-weight:bold;color:var(--ink)}
.by{font-size:.85rem;color:var(--mute);margin-bottom:4px}
.item p{margin:4px 0;font-size:.95rem}
.empty{color:var(--mute);font-style:italic;padding:10px 0}
#preview{overflow-x:auto}
#stage{position:fixed;left:-10000px;top:0;width:700px}
</style>
</head>
<body>
<div class="wrap">
  <h1>Class Work &amp; Homework</h1>

  <section id="login" hidden>
    <form id="lf">
      <label>Name <select name="user" required></select></label>
      <label>Password <input type="password" name="pass" required></label>
      <button class="btn" type="submit">Log in</button>
      <p id="msgL" class="msg"></p>
    </form>
  </section>

  <section id="app" hidden>
    <div class="topbar">
      <span id="who"></span>
      <button class="btn alt" id="logout">Log out</button>
    </div>
    <div class="tabs">
      <button id="tabEnter" class="tab on">Enter work</button>
      <button id="tabCards" class="tab">Class cards</button>
    </div>

    <section id="pageEnter">
      <form id="f">
        <label>Date <input type="date" name="date" required></label>
        <label>Class <select name="cls" required></select></label>
        <label>Subject <select name="subject" required></select></label>
        <label>Topic / lesson <input name="topic" required></label>
        <label>Classwork done <textarea name="classwork" rows="3" required></textarea></label>
        <label>Homework given <textarea name="homework" rows="3" required></textarea></label>
        <label>Homework due date <input type="date" name="due" required></label>
        <label>Remarks (optional) <textarea name="remarks" rows="2"></textarea></label>
        <button class="btn" type="submit">Save entry</button>
        <p id="msg1" class="msg"></p>
      </form>
    </section>

    <section id="pageCards" hidden>
      <div class="row">
        <label>Date <input type="date" id="cdate"></label>
        <label>Class <select id="ccls"></select></label>
      </div>
      <div class="row">
        <button class="btn" id="bImg">Save this class image</button>
        <button class="btn alt" id="bAll">Save all classes</button>
        <button class="btn alt" id="bShare">Share on WhatsApp</button>
        <a class="btn alt" id="bXls" target="_blank" rel="noopener">Download Excel</a>
      </div>
      <p id="msg2" class="msg"></p>
      <div id="preview"></div>
    </section>
  </section>
</div>
<div id="stage"></div>

<script>
const APPS_SCRIPT_URL = "PASTE_WEB_APP_URL_HERE";
const SHEET_ID = "PASTE_SHEET_ID_HERE";
const SCHOOL = "Abu Bakar Memorial Trust";

const TEACHERS = [
  "Mr. Owais Majeed Mir", "Mushtaq Ahmed Wani", "Refaz Ahmad Ganaie", "Abdul Majid Ganaie",
  "Asia Bano", "Ishrat Habib", "Ishrat Rashid", "Saima Rashid", "Inabat Ali",
  "Malik Shugufta", "Rameez Ahamad", "Mehraj Hasan Wani"
];

const BASIC = ["English","Urdu","Mathematics","Science","Ethics / Islamiyat","Al-Quran"];
const PRIMARY = [...BASIC,"Arabic","Computer","Kashmiri"];
const MIDDLE = [...PRIMARY,"Social Science / EVS"];
const EIGHTH = ["English","Urdu","Mathematics","Science","Ethics / Islamiyat","Al-Quran","Computer","Kashmiri","Social Science / EVS"];
const SUBJECTS = {
  "Nursery": BASIC, "LKG": BASIC, "UKG": BASIC,
  "1st": PRIMARY, "2nd": PRIMARY, "3rd": PRIMARY, "4th": PRIMARY, "5th": PRIMARY,
  "6th": MIDDLE, "7th": MIDDLE,
  "8th": EIGHTH
};
const CLASSES = Object.keys(SUBJECTS);
const subjectsFor = cls => SUBJECTS[cls] || [];

const $ = id => document.getElementById(id);
const esc = s => String(s ?? "").replace(/[&<>"']/g, c => ({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}[c]));
const today = () => new Date().toLocaleDateString("en-CA");
const fmt = iso => { const d = new Date(iso + "T00:00:00"); return isNaN(d) ? iso : d.toLocaleDateString("en-GB",{day:"numeric",month:"long",year:"numeric"}); };
const toBlob = c => new Promise(res => c.toBlob(res, "image/png"));
const fname = (cls, date) => `Class-${cls}-${date}.png`;

function fillSelect(sel, list, first){
  sel.innerHTML = `<option value="">${first}</option>` + list.map(v => `<option value="${esc(v)}">${esc(v)}</option>`).join("");
}

// Setup of dropdowns
const f = $("f");
fillSelect($("lf").user, TEACHERS, "Select your name");
fillSelect(f.cls, CLASSES, "Select class");
fillSelect(f.subject, [], "Select class first");
fillSelect($("ccls"), CLASSES, "Select class");
f.cls.addEventListener("change", () => fillSelect(f.subject, subjectsFor(f.cls.value), "Select subject"));
$("bXls").href = `https://docs.google.com/spreadsheets/d/${SHEET_ID}/export?format=xlsx`;

// Login session (kept on this device until logout)
let session = JSON.parse(localStorage.getItem("cwSession") || "null");

async function callGet(params){
  const r = await fetch(APPS_SCRIPT_URL + "?" + new URLSearchParams(params).toString());
  return r.json();
}
async function callPost(body){
  const r = await fetch(APPS_SCRIPT_URL, {
    method: "POST",
    headers: {"Content-Type": "text/plain;charset=utf-8"},
    body: JSON.stringify(body)
  });
  return r.json();
}

function showApp(){
  $("login").hidden = true;
  $("app").hidden = false;
  $("who").textContent = "Logged in as " + session.user;
  f.date.value = today();
  $("cdate").value = today();
}
function showLogin(){
  $("app").hidden = true;
  $("login").hidden = false;
}

$("lf").addEventListener("submit", async e => {
  e.preventDefault();
  const user = e.target.user.value, pass = e.target.pass.value;
  $("msgL").textContent = "Checking...";
  try {
    const j = await callGet({user, pass});
    if (!j.ok) { $("msgL").textContent = "Name or password is wrong."; return; }
    session = {user, pass};
    localStorage.setItem("cwSession", JSON.stringify(session));
    $("msgL").textContent = "";
    showApp();
  } catch (err) {
    $("msgL").textContent = "Could not reach the server. Check your internet connection.";
  }
});

$("logout").onclick = () => {
  localStorage.removeItem("cwSession");
  session = null;
  location.reload();
};

// Saving an entry
f.addEventListener("submit", async e => {
  e.preventDefault();
  const entry = Object.fromEntries(new FormData(f));
  const msg = $("msg1");
  msg.textContent = "Saving...";
  try {
    const j = await callPost({user: session.user, pass: session.pass, entry});
    if (!j.ok) { msg.textContent = "Not saved: " + j.error + "."; return; }
    const keep = {date: f.date.value, cls: f.cls.value};
    f.reset();
    f.date.value = keep.date;
    f.cls.value = keep.cls;
    fillSelect(f.subject, subjectsFor(keep.cls), "Select subject");
    msg.textContent = "Saved. Enter the next class when ready.";
  } catch (err) {
    msg.textContent = "Not saved. Check your internet connection and try again.";
  }
});

// Tabs
function show(which){
  const cards = which === "cards";
  $("pageEnter").hidden = cards;
  $("pageCards").hidden = !cards;
  $("tabEnter").classList.toggle("on", !cards);
  $("tabCards").classList.toggle("on", cards);
  if (cards) loadAndShow();
}
$("tabEnter").onclick = () => show("enter");
$("tabCards").onclick = () => show("cards");

// Loading entries for the cards
let entries = [];
async function loadEntries(){
  $("msg2").textContent = "Loading entries...";
  try {
    const j = await callGet({user: session.user, pass: session.pass});
    if (!j.ok) { $("msg2").textContent = "Could not load: " + j.error + "."; return false; }
    entries = j.entries;
    $("msg2").textContent = "";
    return true;
  } catch (err) {
    $("msg2").textContent = "Could not load entries. Check your internet connection.";
    return false;
  }
}
async function loadAndShow(){ if (await loadEntries()) showPreview(); }

// Building a class card
function buildCard(cls, date){
  const order = subjectsFor(cls);
  const list = entries
    .filter(e => e.Class === cls && e.Date === date)
    .sort((a, b) => order.indexOf(a.Subject) - order.indexOf(b.Subject));
  const card = document.createElement("div");
  card.className = "card";
  card.innerHTML = `
    <div class="head">
      <div class="school">${esc(SCHOOL)}</div>
      <div class="ttl">Class Work &amp; Homework</div>
      <div>Class ${esc(cls)}, ${esc(fmt(date))}</div>
    </div>
    <div class="body">${list.length ? list.map(e => `
      <div class="item">
        <div class="subj">${esc(e.Subject)}</div>
        <div class="by">${esc(e.Teacher)}</div>
        <p><b>Topic:</b> ${esc(e.Topic)}</p>
        <p><b>Class work:</b> ${esc(e.Classwork)}</p>
        <p><b>Homework:</b> ${esc(e.Homework)}</p>
        <p><b>Homework due:</b> ${esc(e["Homework Due"])}</p>
      </div>`).join("") : '<div class="empty">No entries for this class on this date.</div>'}
    </div>`;
  return card;
}

function showPreview(){
  const cls = $("ccls").value, date = $("cdate").value;
  const p = $("preview");
  p.innerHTML = "";
  if (!cls) { p.innerHTML = '<p class="empty">Select a class to see its card.</p>'; return; }
  p.appendChild(buildCard(cls, date));
}
$("ccls").addEventListener("change", showPreview);
$("cdate").addEventListener("change", showPreview);

// Image capture and saving
async function capture(cls, date){
  const stage = $("stage");
  stage.innerHTML = "";
  stage.appendChild(buildCard(cls, date));
  const canvas = await html2canvas(stage.firstChild, {scale: 2, backgroundColor: "#ffffff"});
  stage.innerHTML = "";
  return canvas;
}
function save(blob, name){
  const a = document.createElement("a");
  a.href = URL.createObjectURL(blob);
  a.download = name;
  a.click();
}
function selection(){
  const cls = $("ccls").value, date = $("cdate").value;
  if (!cls || !date) { $("msg2").textContent = "Select a date and a class first."; return null; }
  return {cls, date};
}

$("bImg").onclick = async () => {
  const s = selection(); if (!s) return;
  $("msg2").textContent = "Preparing image...";
  save(await toBlob(await capture(s.cls, s.date)), fname(s.cls, s.date));
  $("msg2").textContent = "Image saved.";
};

$("bAll").onclick = async () => {
  const date = $("cdate").value;
  if (!date) { $("msg2").textContent = "Select a date first."; return; }
  const classes = CLASSES.filter(c => entries.some(e => e.Class === c && e.Date === date));
  if (!classes.length) { $("msg2").textContent = "No entries for this date."; return; }
  for (const c of classes) {
    $("msg2").textContent = "Saving Class " + c + "...";
    save(await toBlob(await capture(c, date)), fname(c, date));
    await new Promise(r => setTimeout(r, 800));
  }
  $("msg2").textContent = `Saved ${classes.length} image(s).`;
};

$("bShare").onclick = async () => {
  const s = selection(); if (!s) return;
  $("msg2").textContent = "Preparing image...";
  const blob = await toBlob(await capture(s.cls, s.date));
  const file = new File([blob], fname(s.cls, s.date), {type: "image/png"});
  if (navigator.canShare && navigator.canShare({files: [file]})) {
    try {
      await navigator.share({files: [file], title: "Class " + s.cls});
      $("msg2").textContent = "Shared.";
    } catch (err) {
      $("msg2").textContent = "Sharing cancelled.";
    }
  } else {
    save(blob, file.name);
    $("msg2").textContent = "Sharing is not supported here. The image was saved; attach it in WhatsApp.";
  }
};

// Start
if (session) { showApp(); } else { showLogin(); }
</script>
</body>
</html>
