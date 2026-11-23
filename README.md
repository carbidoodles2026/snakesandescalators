
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Snakes & Ladders Adventure</title>
<style>
*{box-sizing:border-box}
body{
 margin:0;
 font-family:Arial,sans-serif;
 background:linear-gradient(135deg,#182848,#4b6cb7);
 color:white;
 text-align:center;
 padding:18px;
}
h1{margin:8px 0;color:#ffe66d;text-shadow:2px 3px #222}
.panel{
 max-width:1050px;margin:15px auto;
 background:#ffffff18;padding:16px;
 border:1px solid #ffffff35;border-radius:18px;
}
.controls{display:flex;flex-wrap:wrap;gap:10px;justify-content:center;align-items:center}
button,select,input{
 padding:11px 15px;border:0;border-radius:10px;
 font-size:15px;
}
button{background:#ffcf40;font-weight:bold;cursor:pointer}
button:disabled{opacity:.5;cursor:not-allowed}
#board{
 width:min(94vw,700px);aspect-ratio:1;
 margin:20px auto;display:grid;
 grid-template-columns:repeat(10,1fr);
 grid-template-rows:repeat(10,1fr);
 position:relative;
 border:7px solid #6b3d20;
 border-radius:10px;overflow:hidden;
 background:#fff;
 box-shadow:0 10px 30px #0008;
}
.tile{
 position:relative;
 display:flex;justify-content:center;align-items:flex-start;
 padding:3px;font-weight:bold;color:#252525;
 border:1px solid #ffffff80;
 font-size:clamp(9px,1.8vw,16px);
}
.tile:nth-child(4n+1){background:#ffda79}
.tile:nth-child(4n+2){background:#7bed9f}
.tile:nth-child(4n+3){background:#70a1ff}
.tile:nth-child(4n){background:#ff7979}
.tile span{position:absolute;top:3px;left:4px}
.token{
 position:absolute;bottom:2px;left:50%;
 transform:translateX(-50%);
 width:65%;height:65%;border-radius:50%;
 border:2px solid white;background:#333;
 display:flex;align-items:center;justify-content:center;
 overflow:hidden;font-size:clamp(10px,2vw,20px);
 box-shadow:0 2px 5px #0008;
 z-index:5;
}
.token img{width:100%;height:100%;object-fit:cover}
.overlay{
 position:absolute;inset:0;pointer-events:none;
 width:100%;height:100%;z-index:2;
}
.snake{fill:none;stroke:#087f46;stroke-width:5;stroke-linecap:round}
.snakeHead{fill:#16a05d;stroke:#064c2d;stroke-width:2}
.ladder{stroke:#85502b;stroke-width:3;stroke-linecap:round}
.ladderRung{stroke:#bd8245;stroke-width:2}
.escalator{stroke:#e6a23c;stroke-width:5}
#dice{
 width:80px;height:80px;margin:12px auto;
 background:white;color:#222;border-radius:15px;
 display:grid;place-items:center;
 font-size:48px;
 box-shadow:0 6px 12px #0006;
 border:3px solid #ddd;
}
.rolling{animation:roll .65s infinite}
@keyframes roll{
 0%{transform:rotate(0) scale(1)}
 25%{transform:rotate(90deg) scale(.85)}
 50%{transform:rotate(180deg) scale(1.1)}
 75%{transform:rotate(270deg) scale(.85)}
 100%{transform:rotate(360deg) scale(1)}
}
.players{display:flex;flex-wrap:wrap;justify-content:center;gap:12px}
.playerCard{
 background:#ffffff20;padding:10px;border-radius:12px;
 min-width:125px;border:2px solid transparent;
}
.playerCard.active{border-color:#ffe66d;background:#ffffff35}
.avatar{
 width:45px;height:45px;border-radius:50%;object-fit:cover;
 background:#fff;color:#222;display:grid;place-items:center;
 margin:auto auto 5px;font-size:24px;
}
#status{font-size:19px;font-weight:bold;color:#ffe66d;min-height:28px}
#log{max-height:130px;overflow:auto;text-align:left;font-size:14px}
@media(max-width:500px){
 body{padding:5px}
 .panel{padding:9px}
 #board{width:98vw}
}
</style>
</head>
<body>

<h1>🐍 SNAKES & LADDERS 🪜</h1>
<p>Roll the dice. Climb the ladders. Avoid the snakes!</p>

<div class="panel">
 <div class="controls">
  <label>Players:
   <select id="playerCount">
    <option value="2">2 Players</option>
    <option value="3">3 Players</option>
    <option value="4">4 Players</option>
   </select>
  </label>
  <button onclick="setupPlayers()">Create Players</button>
  <button onclick="resetGame()">New Game</button>
 </div>
 <div id="playersSetup" class="players" style="margin-top:15px"></div>
</div>

<div class="panel">
 <div id="status">Set up your players to begin!</div>
 <div id="dice">⚄</div>
 <button id="rollButton" onclick="rollDice()" disabled>🎲 ROLL DICE</button>
 <div id="diceResult"></div>
 <div class="players" id="playerCards" style="margin-top:15px"></div>
</div>

<div id="board"></div>

<div class="panel">
 <h3>Game History</h3>
 <div id="log">Welcome to Snakes & Ladders!</div>
</div>

<script>
const snakes={
 17:7,34:12,47:26,62:19,88:53,95:75,99:78
};
const ladders={
 3:22,8:30,15:44,28:55,36:57,51:72,71:91,80:100
};
const colors=["#ff4757","#2ed573","#1e90ff","#ffa502"];
const symbols=["🦊","🐼","🐯","🐸"];
let players=[];
let current=0;
let busy=false;
let finished=false;

function setupPlayers(){
 const count=Number(document.getElementById("playerCount").value);
 players=Array.from({length:count},(_,i)=>({
  name:"Player "+(i+1),
  position:0,
  color:colors[i],
  photo:null
 }));
 renderSetup();
 renderCards();
 createBoard();
 document.getElementById("rollButton").disabled=false;
 document.getElementById("status").textContent="Player 1's turn!";
 current=0;finished=false;busy=false;
}

function renderSetup(){
 const box=document.getElementById("playersSetup");
 box.innerHTML="";
 players.forEach((p,i)=>{
  const card=document.createElement("div");
  card.className="playerCard";
  card.innerHTML=`
   <div class="avatar" id="preview${i}">${symbols[i]}</div>
   <input type="text" value="${p.name}" aria-label="Player ${i+1} name"
    onchange="players[${i}].name=this.value;renderCards()"
    style="width:115px;margin:5px">
   <br>
   <label style="font-size:12px">Upload photo
    <input type="file" accept="image/*" onchange="uploadPhoto(event,${i})" style="width:120px;font-size:11px">
   </label>`;
  box.appendChild(card);
 });
}

function uploadPhoto(event,i){
 const file=event.target.files[0];
 if(!file)return;
 if(!file.type.startsWith("image/"))return alert("Please select an image.");
 const reader=new FileReader();
 reader.onload=e=>{
  players[i].photo=e.target.result;
  document.getElementById("preview"+i).innerHTML=
   `<img src="${e.target.result}" class="avatar">`;
  renderCards();
  updateTokens();
 };
 reader.readAsDataURL(file);
}

function createBoard(){
 const board=document.getElementById("board");
 board.innerHTML="";
 // Standard serpentine numbered board
 for(let row=9;row>=0;row--){
  for(let col=0;col<10;col++){
   let num=row%2===1?row*10+10-col:row*10+col+1;
   const tile=document.createElement("div");
   tile.className="tile";
   tile.id="tile"+num;
   tile.innerHTML=`<span>${num}</span>`;
   board.appendChild(tile);
  }
 }
 drawRoutes();
 updateTokens();
}

function tileCenter(n){
 const row=Math.floor((n-1)/10);
 const col=row%2===0?(n-1)%10:9-(n-1)%10;
 return {x:(col+.5)*10,y:(9-row+.5)*10};
}

function drawRoutes(){
 const board=document.getElementById("board");
 const svg=document.createElementNS("http://www.w3.org/2000/svg","svg");
 svg.setAttribute("class","overlay");
 svg.setAttribute("viewBox","0 0 100 100");
 const line=(x1,y1,x2,y2,cls)=>{
  const el=document.createElementNS("http://www.w3.org/2000/svg","line");
  el.setAttribute("x1",x1);el.setAttribute("y1",y1);
  el.setAttribute("x2",x2);el.setAttribute("y2",y2);
  el.setAttribute("class",cls);svg.appendChild(el);
 };
 Object.entries(ladders).forEach(([a,b])=>{
  const p=tileCenter(+a),q=tileCenter(b);
  line(p.x-1,p.y,q.x-1,q.y,"ladder");
  line(p.x+1,p.y,q.x+1,q.y,"ladder");
  for(let t=.12;t<.95;t+=.15){
   const x=p.x+(q.x-p.x)*t;
   const y=p.y+(q.y-p.y)*t;
   line(x-1,y,x+1,y,"ladderRung");
  }
  if(b-a>=25) {
   line(p.x,p.y,q.x,q.y,"escalator");
  }
 });
 Object.entries(snakes).forEach(([a,b])=>{
  const p=tileCenter(+a),q=tileCenter(b);
  const midX=(p.x+q.x)/2+5;
  const midY=(p.y+q.y)/2;
  const path=document.createElementNS("http://www.w3.org/2000/svg","path");
  path.setAttribute("d",`M ${p.x} ${p.y} Q ${midX} ${midY} ${q.x} ${q.y}`);
  path.setAttribute("class","snake");
  svg.appendChild(path);
  const head=document.createElementNS("http://www.w3.org/2000/svg","circle");
  head.setAttribute("cx",p.x);head.setAttribute("cy",p.y);
  head.setAttribute("r","2.7");head.setAttribute("class","snakeHead");
  svg.appendChild(head);
  const eye=document.createElementNS("http://www.w3.org/2000/svg","circle");
  eye.setAttribute("cx",p.x-0.7);eye.setAttribute("cy",p.y-.5);
  eye.setAttribute("r",".4");eye.setAttribute("fill","white");
  svg.appendChild(eye);
 });
 board.appendChild(svg);
}

function renderCards(){
 const box=document.getElementById("playerCards");
 box.innerHTML="";
 players.forEach((p,i)=>{
  const card=document.createElement("div");
  card.className="playerCard"+(i===current&&!finished?" active":"");
  card.innerHTML=`
   <div class="avatar">${p.photo?
    `<img src="${p.photo}" style="width:100%;height:100%;object-fit:cover">`:symbols[i]}</div>
   <b>${escapeHTML(p.name)}</b><br>
   Position: ${p.position||"Start"}<br>
   <small>${i===current&&!finished?"YOUR TURN":""}</small>`;
  box.appendChild(card);
 });
}

function escapeHTML(s){
 return String(s).replace(/[&<>"']/g,c=>({
  "&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"
 }[c]));
}

function updateTokens(){
 document.querySelectorAll(".token").forEach(t=>t.remove());
 players.forEach((p,i)=>{
  if(p.position===0)return;
  const tile=document.getElementById("tile"+p.position);
  if(!tile)return;
  const token=document.createElement("div");
  token.className="token";
  token.style.background=p.color;
  token.style.left=(50+(i-1.5)*15)+"%";
  token.innerHTML=p.photo?
   `<img src="${p.photo}">`:symbols[i];
  tile.appendChild(token);
 });
}

function wait(ms){return new Promise(resolve=>setTimeout(resolve,ms));}

async function rollDice(){
 if(busy||finished)return;
 busy=true;
 document.getElementById("rollButton").disabled=true;
 const dice=document.getElementById("dice");
 dice.classList.add("rolling");
 const result=1+Math.floor(Math.random()*6);
 for(let i=0;i<12;i++){
  dice.textContent=["⚀","⚁","⚂","⚃","⚄","⚅"][Math.floor(Math.random()*6)];
  await wait(65);
 }
 dice.textContent=["⚀","⚁","⚂","⚃","⚄","⚅"][result-1];
 dice.classList.remove("rolling");
 document.getElementById("diceResult").textContent="You rolled a "+result+"!";
 await wait(400);
 await movePlayer(result);
}

async function movePlayer(steps){
 const p=players[current];
 if(p.position+steps>100){
  log(p.name+" needs an exact roll to reach 100!");
  nextTurn();
  return;
 }
 for(let i=0;i<steps;i++){
  p.position++;
  updateTokens();renderCards();
  await wait(230);
 }
 await wait(350);
 if(ladders[p.position]){
  const old=p.position;
  log("🪜 "+p.name+" climbs a wooden ladder!");
  await wait(500);
  p.position=ladders[old];
  updateTokens();renderCards();
  await wait(500);
  log(p.name+" climbed from "+old+" to "+p.position+"!");
 }
 else if(snakes[p.position]){
  const old=p.position;
  log("🐍 Oh no! A snake caught "+p.name+"!");
  await wait(600);
  p.position=snakes[old];
  updateTokens();renderCards();
  await wait(500);
  log(p.name+" slid down from "+old+" to "+p.position+"!");
 }
 if(p.position===100){
  finished=true;
  document.getElementById("status").textContent="🏆 "+p.name+" WINS!";
  log("🏆 "+p.name+" is the champion!");
  document.getElementById("rollButton").disabled=true;
  renderCards();
  busy=false;
  return;
 }
 nextTurn();
}

function nextTurn(){
 current=(current+1)%players.length;
 renderCards();
 document.getElementById("status").textContent=players[current].name+"'s turn!";
 document.getElementById("rollButton").disabled=false;
 busy=false;
}

function log(message){
 const box=document.getElementById("log");
 const item=document.createElement("div");
 item.textContent="• "+message;
 box.prepend(item);
}

function resetGame(){
 if(!players.length){setupPlayers();return;}
 players.forEach(p=>p.position=0);
 current=0;busy=false;finished=false;
 document.getElementById("dice").textContent="⚄";
 document.getElementById("diceResult").textContent="";
 document.getElementById("log").innerHTML="New game started!";
 document.getElementById("status").textContent=players[0].name+"'s turn!";
 document.getElementById("rollButton").disabled=false;
 renderCards();updateTokens();
}

setupPlayers();
</script>
</body>
</html>
