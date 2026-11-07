<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Diwali Countdown By Jai Aggarwal</title>
<style>
:root{--gold:#e7bd65;--light:#ffe6a6;--muted:#b8ad98}
*{box-sizing:border-box}
body{margin:0;min-height:100vh;color:#fff8e8;font-family:Georgia,serif;background:radial-gradient(ellipse at 50% 0%,#493515 0%,transparent 42%),#090909}
.wrap{width:min(1050px,92%);margin:auto;padding:34px 0 55px}
header{text-align:center;padding:28px 10px}
.eyebrow{color:var(--gold);letter-spacing:.28em;text-transform:uppercase;font:11px Arial}
h1{color:var(--light);font-size:clamp(30px,7vw,54px);margin:15px 0 10px}
.subtitle{color:var(--muted);font:14px Arial;line-height:1.6}
.ornament{color:var(--gold);font-size:25px;margin:12px}
.toolbar{display:flex;justify-content:center;margin:10px 0 25px}
button{border:1px solid var(--gold);color:#171006;background:var(--gold);border-radius:999px;padding:12px 20px;font-weight:bold;cursor:pointer}
.grid{display:grid;grid-template-columns:repeat(auto-fit,minmax(260px,1fr));gap:17px}
.event{border:1px solid #8e6b32;border-radius:17px;padding:20px;background:linear-gradient(145deg,#211a0f,#100f0c);box-shadow:0 12px 35px #0008}
.event h2{color:var(--light);font-size:22px;margin:0;overflow-wrap:anywhere}
.date-label{color:var(--muted);font:12px Arial;margin:12px 0 18px}
.timer{display:grid;grid-template-columns:repeat(4,1fr);gap:7px}
.unit{text-align:center;padding:12px 3px;background:#ffffff0b;border-radius:10px}
.number{display:block;color:var(--light);font:bold 25px Arial}
.unit small{color:var(--muted);font:10px Arial;text-transform:uppercase}
.event-actions{display:flex;justify-content:flex-end;margin-top:16px}
.remove{background:transparent;color:#d8bd8b;border-color:#6c5734;padding:7px 12px;font-size:12px}
.empty{color:var(--muted);text-align:center;grid-column:1/-1;padding:25px}
footer{text-align:center;color:#a99b80;font:12px Arial;margin-top:35px;line-height:1.8}
dialog{width:min(420px,90%);border:1px solid var(--gold);border-radius:16px;background:#17130c;color:#fff8e8;padding:23px}
dialog::backdrop{background:#000b}
dialog h2{color:var(--light)}
label{display:block;color:var(--muted);font:13px Arial;margin:13px 0 6px}
input{width:100%;padding:12px;border-radius:8px;border:1px solid #806638;background:#090909;color:white}
.form-actions{display:flex;gap:10px;justify-content:flex-end;margin-top:20px}
.cancel{background:transparent;color:var(--gold)}
</style>
</head>
<body>
<main class="wrap">
<header>
<div class="eyebrow">A little magic, counting down</div>
<div class="ornament">✦ ✧ ✦</div>
<h1>Diwali Countdown</h1>
<div class="subtitle">By Jai Aggarwal<br>Every moment brings us closer to something special.</div>
</header>
<div class="toolbar"><button id="addBtn">＋ Add an event</button></div>
<section class="grid" id="events" aria-live="polite"></section>
<footer>Made with ✦ by Jai Aggarwal<br>Countdowns use your phone's local time.</footer>
</main>
<dialog id="eventDialog">
<form id="eventForm">
<h2>Add a countdown</h2>
<label for="eventName">Event name</label>
<input id="eventName" maxlength="45" placeholder="e.g. Birthday" required>
<label for="eventDate">Date and time</label>
<input id="eventDate" type="datetime-local" required>
<div class="form-actions">
<button class="cancel" id="cancelBtn" type="button">Cancel</button>
<button type="submit">Save event</button>
</div>
</form>
</dialog>
<script>
const KEY="jai-countdown-events-v1";
const defaults=[
{id:"diwali-2026",name:"Diwali 2026",date:"2026-11-08T00:00"},
{id:"new-year-2027",name:"New Year 2027",date:"2027-01-01T00:00"}
];
let events;
try{const saved=JSON.parse(localStorage.getItem(KEY));events=Array.isArray(saved)?saved:defaults}
catch{events=defaults}
const eventsEl=document.getElementById("events");
const dialog=document.getElementById("eventDialog");
const form=document.getElementById("eventForm");
function save(){localStorage.setItem(KEY,JSON.stringify(events))}
function render(){
eventsEl.innerHTML="";
if(!events.length){eventsEl.innerHTML='<p class="empty">No events yet. Tap Add an event to create one.</p>';return}
events.forEach(event=>{
const card=document.createElement("article");card.className="event";
const date=new Date(event.date);
card.innerHTML='<div class="event-top"><h2></h2></div><div class="date-label"></div><div class="timer"><div class="unit"><span class="number" data-unit="days">00</span><small>Days</small></div><div class="unit"><span class="number" data-unit="hours">00</span><small>Hours</small></div><div class="unit"><span class="number" data-unit="minutes">00</span><small>Minutes</small></div><div class="unit"><span class="number" data-unit="seconds">00</span><small>Seconds</small></div></div><div class="event-actions"><button class="remove" type="button">Remove</button></div>';
card.querySelector("h2").textContent=event.name;
card.querySelector(".date-label").textContent=Number.isNaN(date.getTime())?"Invalid date":date.toLocaleString([],{dateStyle:"medium",timeStyle:"short"});
card.querySelector(".remove").addEventListener("click",()=>{events=events.filter(x=>x.id!==event.id);save();render()});
eventsEl.appendChild(card);
});
updateTimers();
}
function updateTimers(){
const now=Date.now();
[...eventsEl.querySelectorAll(".event")].forEach((card,index)=>{
const diff=new Date(events[index].date).getTime()-now;
const v=diff<=0?{days:0,hours:0,minutes:0,seconds:0}:{days:Math.floor(diff/86400000),hours:Math.floor(diff/3600000)%24,minutes:Math.floor(diff/60000)%60,seconds:Math.floor(diff/1000)%60};
Object.entries(v).forEach(([k,n])=>card.querySelector('[data-unit="'+k+'"]').textContent=String(n).padStart(2,"0"));
if(diff<=0)card.querySelector(".date-label").textContent="The event time has arrived!";
});
}
document.getElementById("addBtn").addEventListener("click",()=>{form.reset();dialog.showModal()});
document.getElementById("cancelBtn").addEventListener("click",()=>dialog.close());
form.addEventListener("submit",e=>{
e.preventDefault();
const name=document.getElementById("eventName").value.trim();
const date=document.getElementById("eventDate").value;
if(!name||!date)return;
events.push({id:String(Date.now()),name,date});save();render();dialog.close();
});
render();
setInterval(updateTimers,1000);
</script>
</body>
</html># diwali-countdown
Diwali Countdown By Jai Aggarwal 
