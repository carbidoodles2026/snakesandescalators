
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Snakes & Ladders - The Classic Game</title>
<style>
:root {
  --ink:#26334a;
  --cream:#fffaf0;
  --panel:#ffffff;
  --accent:#ff9f43;
  --shadow:0 12px 30px rgba(38,51,74,.12);
}
*{box-sizing:border-box}
body{
  margin:0;
  font-family:system-ui,-apple-system,Segoe UI,sans-serif;
  color:var(--ink);
  background:
    radial-gradient(circle at 10% 10%,#fff0c9 0,transparent 28%),
    radial-gradient(circle at 90% 90%,#d9f4ed 0,transparent 30%),
    #f5f3ec;
  min-height:100vh;
}
button,input,select{font:inherit}
button{cursor:pointer}
header{
  text-align:center;
  padding:24px 15px 12px;
}
header h1{
  margin:0;
  font-size:clamp(1.8rem,4vw,3rem);
  letter-spacing:-1.5px;
  font-weight:900;
}
header h1 span{color:#f28b35}
header p{margin:7px 0 0;color:#738096}
.app{
  width:min(1450px,96%);
  margin:15px auto 35px;
  display:grid;
  grid-template-columns:minmax(0,1fr) 320px;
  gap:20px;
  align-items:start;
}
.board-card,.side-card{
  background:rgba(255,255,255,.9);
  border:1px solid rgba(255,255,255,.9);
  border-radius:24px;
  box-shadow:var(--shadow);
}
.board-card{padding:16px}
.board-top{
  display:flex;
  justify-content:space-between;
  align-items:center;
  gap:10px;
  margin:0 2px 13px;
}
.board-top h2{margin:0;font-size:1.1rem}
.pill{
  background:#edf5ef;
  color:#397a58;
  padding:7px 12px;
  border-radius:30px;
  font-size:.78rem;
  font-weight:800;
}
.board-wrap{
  position:relative;
  width:100%;
  aspect-ratio:1;
  border:7px solid #fff;
  border-radius:13px;
  overflow:hidden;
  background:#e9f1dc;
  box-shadow:0 5px 15px #26334a20;
}
.board{
  position:absolute;
  inset:0;
  display:grid;
  grid-template-columns:repeat(10,1fr);
  grid-template-rows:repeat(10,1fr);
}
.tile{
  position:relative;
  display:flex;
  align-items:flex-start;
  justify-content:flex-start;
  padding:3px 4px;
  font-size:clamp(8px,1.25vw,14px);
  font-weight:900;
  color:#53634f;
  border:1px solid rgba(74,95,68,.12);
}
.tile:nth-child(odd){background:#e4f0cf}
.tile:nth-child(even){background:#f8edc8}
.tile.special-start{background:#bdebd3}
.tile.special-finish{background:#ffe09b}
.tile-number{position:relative;z-index:1}
.overlay,.tokens{
  position:absolute;
  inset:0;
  width:100%;
  height:100%;
}
.overlay{z-index:2;pointer-events:none}
.tokens{z-index:3;pointer-events:none}
.snake-line{
  fill:none;
  stroke:#d84d45;
  stroke-width:17;
  stroke-linecap:round;
  stroke-linejoin:round;
  filter:drop-shadow(2px 3px 2px #0003);
}
.snake-belly{
  fill:none;
  stroke:#ffb4a4;
  stroke-width:4;
  stroke-dasharray:5 13;
  stroke-linecap:round;
}
.snake-head{fill:#d84d45;stroke:#8e302e;stroke-width:3}
.snake-eye{fill:white}
.snake-pupil{fill:#26334a}
.ladder-rail{
  fill:none;
  stroke:#9a622e;
  stroke-width:13;
  stroke-linecap:round;
  filter:drop-shadow(2px 3px 2px #0003);
}
.ladder-rung{
  stroke:#e6b46b;
  stroke-width:8;
  stroke-linecap:round;
}
.token{
  position:absolute;
  width:7.2%;
  height:7.2%;
  min-width:20px;
  min-height:20px;
  border-radius:50%;
  border:3px solid white;
  box-shadow:0 3px 7px #17203355;
  transform:translate(-50%,-50%);
  display:flex;
  justify-content:center;
  align-items:center;
  overflow:hidden;
  transition:left .22s linear,top .22s linear;
  background:var(--token-color);
}
.token img{width:100%;height:100%;object-fit:cover}
.token .initial{
  color:white;
  font-size:clamp(10px,1.4vw,17px);
  font-weight:900;
  text-shadow:0 1px 2px #0005;
}
.token.active-token{
  outline:3px solid #ffd34e;
  outline-offset:2px;
  z-index:5;
}
.start-label{
  position:absolute;
  bottom:4px;
  right:4px;
  z-index:1;
  font-size:8px;
  font-weight:900;
  color:#3b785a;
}
.sidebar{display:grid;gap:15px}
.side-card{padding:17px}
.side-card h3{margin:0 0 13px;font-size:1rem}
.setup-row{display:flex;gap:8px;align-items:center;margin-bottom:10px}
.setup-row label{font-size:.85rem;font-weight:750;flex:1}
select,input[type=text]{
  border:1px solid #dce2e8;
  background:#fff;
  border-radius:10px;
  padding:9px 10px;
  color:var(--ink);
  min-width:0;
}
select{max-width:120px}
.player-list{display:grid;gap:10px}
.player{
  display:grid;
  grid-template-columns:42px minmax(0,1fr);
  gap:10px;
  align-items:center;
  padding:10px;
  border:2px solid #edf0f3;
  border-radius:14px;
  transition:.2s;
}
.player.current{
  border-color:#ffb14f;
  background:#fff8e9;
  box-shadow:0 3px 12px #ffb14f25;
}
.avatar{
  width:42px;height:42px;
  border-radius:50%;
  border:3px solid white;
  box-shadow:0 1px 5px #0002;
  display:grid;place-items:center;
  color:white;font-weight:900;
  overflow:hidden;
  background:var(--player-color);
  font-size:1rem;
}
.avatar img{width:100%;height:100%;object-fit:cover}
.player-info{min-width:0}
.player-name{
  width:100%;
  padding:5px 7px!important;
  font-weight:800;
}
.player-meta{font-size:.73rem;color:#788397;margin-top:4px}
.photo-label{
  display:inline-block;
  margin-top:5px;
  font-size:.72rem;
  font-weight:750;
  color:#4c6b91;
  cursor:pointer;
}
.photo-label input{display:none}
.turn-box{
  text-align:center;
  background:#f4f7fb;
  border-radius:16px;
  padding:14px;
}
.turn-caption{
  color:#7a879a;
  font-size:.75rem;
  text-transform:uppercase;
  font-weight:850;
  letter-spacing:1px;
}
.turn-name{font-size:1.2rem;font-weight:900;margin-top:4px}
.dice-area{display:grid;place-items:center;padding:15px 0 10px}
.dice-scene{width:88px;height:88px;perspective:500px}
.dice{
  width:88px;height:88px;
  position:relative;
  transform-style:preserve-3d;
  transform:translateZ(-44px) rotateX(-18deg) rotateY(-25deg);
}
.dice.rolling{animation:tumble .65s ease-in-out infinite}
@keyframes tumble{
  0%{transform:translateZ(-44px) rotateX(0deg) rotateY(0deg) rotateZ(0deg)}
  25%{transform:translateZ(-44px) rotateX(100deg) rotateY(110deg) rotateZ(70deg)}
  50%{transform:translateZ(-44px) rotateX(220deg) rotateY(200deg) rotateZ(150deg)}
  75%{transform:translateZ(-44px) rotateX(310deg) rotateY(300deg) rotateZ(250deg)}
  100%{transform:translateZ(-44px) rotateX(360deg) rotateY(360deg) rotateZ(360deg)}
}
.face{
  position:absolute;
  width:88px;height:88px;
  border-radius:15px;
  background:linear-gradient(145deg,#fff,#e8edf4);
  border:2px solid #d9e0e9;
  box-shadow:inset 0 2px 5px #fff;
  display:grid;
  grid-template:repeat(3,1fr)/repeat(3,1fr);
  padding:12px;
  backface-visibility:hidden;
}
.face.front{transform:rotateY(0deg) translateZ(44px)}
.face.back{transform:rotateY(180deg) translateZ(44px)}
.face.right{transform:rotateY(90deg) translateZ(44px)}
.face.left{transform:rotateY(-90deg) translateZ(44px)}
.face.top{transform:rotateX(90deg) translateZ(44px)}
.face.bottom{transform:rotateX(-90deg) translateZ(44px)}
.dot{
  width:13px;height:13px;
  border-radius:50%;
  background:#29374d;
  align-self:center;justify-self:center;
}
.d1{grid-area:1/1}.d2{grid-area:1/3}.d3{grid-area:2/2}
.d4{grid-area:3/1}.d5{grid-area:3/3}.d6{grid-area:2/1}
.d7{grid-area:2/3}
.dice-result{font-size:.8rem;color:#778397;margin-top:7px;min-height:20px}
.roll-btn,.reset-btn{
  width:100%;border:0;border-radius:13px;
  padding:13px;font-weight:900;
  transition:transform .15s,filter .15s;
}
.roll-btn{background:linear-gradient(135deg,#ffb64f,#f28b35);color:#422c16;box-shadow:0 5px 0 #d77b29}
.roll-btn:hover:not(:disabled){filter:brightness(1.05);transform:translateY(-2px)}
.roll-btn:active:not(:disabled){transform:translateY(2px);box-shadow:0 2px 0 #d77b29}
.roll-btn:disabled{opacity:.5;cursor:not-allowed}
.reset-btn{margin-top:9px;background:#edf1f6;color:#53647b}
.status{
  min-height:42px;
  font-size:.83rem;
  line-height:1.4;
  color:#65748a;
  margin-top:10px;
  text-align:center;
}
.legend{display:flex;flex-wrap:wrap;gap:12px;font-size:.75rem;color:#65748a}
.legend span{display:flex;align-items:center;gap:5px}
.legend i{width:12px;height:12px;border-radius:4px;display:inline-block}
.rules{font-size:.78rem;line-height:1.55;color:#758195;margin:0;padding-left:18px}
.win-banner{
  display:none;
  position:fixed;inset:0;
  z-index:20;
  background:#18243acc;
  align-items:center;justify-content:center;
  padding:20px;
}
.win-banner.show{display:flex}
.win-content{
  background:white;
  border-radius:25px;
  padding:30px;
  text-align:center;
  width:min(380px,100%);
  box-shadow:0 20px 60px #0005;
  animation:pop .35s ease-out;
}
@keyframes pop{from{transform:scale(.7);opacity:0}to{transform:scale(1);opacity:1}}
.win-content .trophy{font-size:4rem}
.win-content h2{font-size:2rem;margin:8px 0}
.win-content p{color:#758195}
.win-content button{
  border:0;border-radius:12px;padding:12px 22px;
  background:#ffab48;font-weight:900;color:#422c16;
}
@media(max-width:900px){
  .app{grid-template-columns:1fr;width:min(700px,96%)}
  .sidebar{grid-template-columns:1fr 1fr}
  .sidebar .players-card{grid-row:span 2}
}
@media(max-width:520px){
  header{padding-top:17px}
  .app{width:96%;gap:12px;margin-top:8px}
  .board-card{padding:8px;border-radius:16px}
  .board-wrap{border-width:4px}
  .tile{padding:1px 2px}
  .sidebar{grid-template-columns:1fr}
  .sidebar .players-card{grid-row:auto}
  .side-card{padding:14px}
}
</style>
</head>
<body>

<header>
  <h1>🐍 Snakes <span>&</span> Ladders 🪜</h1>
  <p>The classic race to 100. Roll the dice and climb your way to victory!</p>
</header>

<main class="app">
  <section class="board-card">
    <div class="board-top">
      <h2>Game Board</h2>
      <span class="pill">100 TILES · CLASSIC MODE</span>
    </div>
    <div class="board-wrap">
      <div class="board" id="board"></div>
      <svg class="overlay" id="overlay" viewBox="0 0 1000 1000" preserveAspectRatio="none"></svg>
      <div class="tokens" id="tokens"></div>
    </div>
    <div class="legend" style="margin:13px 4px 2px">
      <span><i style="background:#d84d45"></i> Snakes: slide down</span>
      <span><i style="background:#b78345"></i> Ladders: climb up</span>
      <span><i style="background:#ffcf54"></i> Current player</span>
    </div>
  </section>

  <aside class="sidebar">
    <section class="side-card players-card">
      <h3>👥 Players</h3>
      <div class="setup-row">
        <label for="playerCount">Number of players</label>
        <select id="playerCount">
          <option value="2">2 Players</option>
          <option value="3">3 Players</option>
          <option value="4">4 Players</option>
        </select>
      </div>
      <div class="player-list" id="playerList"></div>
    </section>

    <section class="side-card">
      <h3>🎲 Your Turn</h3>
      <div class="turn-box">
        <div class="turn-caption">Playing now</div>
        <div class="turn-name" id="turnName">Player 1</div>
        <div class="dice-area">
          <div class="dice-scene">
            <div class="dice" id="dice">
              <div class="face front"><i class="dot d3"></i></div>
              <div class="face back"><i class="dot d1"></i><i class="dot d2"></i><i class="dot d3"></i><i class="dot d4"></i><i class="dot d5"></i><i class="dot d6"></i></div>
              <div class="face right"><i class="dot d1"></i><i class="dot d2"></i><i class="dot d3"></i><i class="dot d4"></i><i class="dot d5"></i></div>
              <div class="face left"><i class="dot d1"></i><i class="dot d2"></i></div>
              <div class="face top"><i class="dot d1"></i></div>
              <div class="face bottom"><i class="dot d1"></i><i class="dot d2"></i><i class="dot d4"></i><i class="dot d5"></i></div>
            </div>
          </div>
          <div class="dice-result" id="diceResult">Ready to roll!</div>
        </div>
        <button class="roll-btn" id="rollBtn">ROLL THE DICE</button>
        <button class="reset-btn" id="resetBtn">↻ Restart Game</button>
        <div class="status" id="status">Player 1, roll the dice to begin.</div>
      </div>
    </section>

    <section class="side-card">
      <h3>📜 How to Play</h3>
      <ol class="rules">
        <li>Take turns rolling the dice.</li>
        <li>Move forward by the number rolled.</li>
        <li>Land on a ladder to climb up.</li>
        <li>Land on a snake to slide down.</li>
        <li>You need the exact number to reach tile 100.</li>
        <li>First player to reach 100 wins!</li>
      </ol>
    </section>
  </aside>
</main>

<div class="win-banner" id="winBanner">
  <div class="win-content">
    <div class="trophy">🏆</div>
    <h2 id="winnerTitle">We have a winner!</h2>
    <p>Congratulations! You reached tile 100 and won the game.</p>
    <button id="playAgain">Play Again</button>
  </div>
</div>

<script>
const COLORS = ["#ef6461","#4285d4","#36a879","#a16ad1"];
const SNAKES = [
  {from:99,to:54,curve:-100},
  {from:95,to:72,curve:65},
  {from:87,to:36,curve:-80},
  {from:62,to:19,curve:65},
  {from:56,to:8,curve:-70},
  {from:48,to:26,curve:55},
  {from:16,to:6,curve:-35}
];
const LADDERS = [
  {from:2,to:23},
  {from:4,to:14},
  {from:9,to:31},
  {from:21,to:42},
  {from:28,to:84},
  {from:36,to:44},
  {from:51,to:67},
  {from:71,to:91},
  {from:80,to:100}
];

let players = [];
let current = 0;
let busy = false;
let gameOver = false;
let lastRoll = 1;

const $ = id => document.getElementById(id);
const wait = ms => new Promise(resolve => setTimeout(resolve,ms));

function coordinates(square){
  const rowFromBottom = Math.floor((square-1)/10);
  const positionInRow = (square-1)%10;
  const col = rowFromBottom%2===0 ? positionInRow : 9-positionInRow;
  const row = 9-rowFromBottom;
  return {x:col*100+50,y:row*100+50};
}

function buildBoard(){
  const board = $("board");
  board.innerHTML="";
  for(let visual=0;visual<100;visual++){
    const row = Math.floor(visual/10);
    const col = visual%10;
    const rowFromBottom = 9-row;
    const square = rowFromBottom%2===0
      ? rowFromBottom*10+col+1
      : rowFromBottom*10+(10-col);
    const tile = document.createElement("div");
    tile.className="tile";
    if(square===1)tile.classList.add("special-start");
    if(square===100)tile.classList.add("special-finish");
    tile.innerHTML=`<span class="tile-number">${square}</span>`;
    if(square===1)tile.innerHTML+=`<span class="start-label">START</span>`;
    if(square===100)tile.innerHTML+=`<span class="start-label" style="color:#9b6a13">FINISH</span>`;
    board.appendChild(tile);
  }
}

function svgEl(name,attrs,parent){
  const el=document.createElementNS("http://www.w3.org/2000/svg",name);
  Object.entries(attrs).forEach(([k,v])=>el.setAttribute(k,v));
  parent.appendChild(el);
  return el;
}

function drawConnections(){
  const svg=$("overlay");
  svg.innerHTML="";
  // Draw ladders first, so the snakes and tokens remain visually on top.
  LADDERS.forEach((ladder,index)=>{
    const a=coordinates(ladder.from),b=coordinates(ladder.to);
    const dx=b.x-a.x,dy=b.y-a.y;
    const len=Math.hypot(dx,dy);
    const px=-dy/len*12,py=dx/len*12;
    svgEl("line",{x1:a.x+px,y1:a.y+py,x2:b.x+px,y2:b.y+py,class:"ladder-rail"},svg);
    svgEl("line",{x1:a.x-px,y1:a.y-py,x2:b.x-px,y2:b.y-py,class:"ladder-rail"},svg);
    const rungs=Math.max(3,Math.floor(len/32));
    for(let i=1;i<rungs;i++){
      const t=i/rungs;
      const x=a.x+dx*t,y=a.y+dy*t;
      svgEl("line",{x1:x+px,y1:y+py,x2:x-px,y2:y-py,class:"ladder-rung"},svg);
    }
  });

  SNAKES.forEach((snake,index)=>{
    const a=coordinates(snake.from),b=coordinates(snake.to);
    const mx=(a.x+b.x)/2+snake.curve;
    const my=(a.y+b.y)/2-snake.curve*.45;
    const path=`M ${a.x} ${a.y} Q ${mx} ${my} ${b.x} ${b.y}`;
    svgEl("path",{d:path,class:"snake-line"},svg);
    svgEl("path",{d:path,class:"snake-belly"},svg);
    svgEl("circle",{cx:a.x,cy:a.y,r:18,class:"snake-head"},svg);
    svgEl("circle",{cx:a.x-6,cy:a.y-4,r:5,class:"snake-eye"},svg);
    svgEl("circle",{cx:a.x+6,cy:a.y-4,r:5,class:"snake-eye"},svg);
    svgEl("circle",{cx:a.x-5,cy:a.y-4,r:2.2,class:"snake-pupil"},svg);
    svgEl("circle",{cx:a.x+7,cy:a.y-4,r:2.2,class:"snake-pupil"},svg);
    // Small forked tongue
    svgEl("path",{d:`M ${a.x} ${a.y+13} l -4 8 m 4 -8 l 4 8`,stroke:"#ffb2a2","stroke-width":3,fill:"none","stroke-linecap":"round"},svg);
  });
}

function makePlayers(count){
  players=Array.from({length:count},(_,i)=>({
    name:`Player ${i+1}`,
    color:COLORS[i],
    position:0,
    photo:null
  }));
  current=0;
  renderPlayers();
  renderTokens();
  updateTurn();
}

function renderPlayers(){
  const list=$("playerList");
  list.innerHTML="";
  players.forEach((p,i)=>{
    const row=document.createElement("div");
    row.className="player"+(i===current?" current":"");
    const avatar=document.createElement("div");
    avatar.className="avatar";
    avatar.style.setProperty("--player-color",p.color);
    if(p.photo){
      const img=document.createElement("img");
      img.src=p.photo;
      avatar.appendChild(img);
    }else{
      avatar.textContent=p.name.trim().charAt(0).toUpperCase()||String(i+1);
    }

    const info=document.createElement("div");
    info.className="player-info";
    const name=document.createElement("input");
    name.type="text";
    name.maxLength=16;
    name.className="player-name";
    name.value=p.name;
    name.setAttribute("aria-label",`Player ${i+1} name`);
    name.addEventListener("input",()=>{
      p.name=name.value||`Player ${i+1}`;
      renderTokens();
      updateTurn();
      const initial=avatar.querySelector(".initial");
      if(!p.photo)avatar.textContent=p.name.trim().charAt(0).toUpperCase()||String(i+1);
    });

    const meta=document.createElement("div");
    meta.className="player-meta";
    meta.textContent=`${p.position===0?"At start":`Tile ${p.position}`} · ${p.position===100?"Winner": "In the game"}`;

    const photoLabel=document.createElement("label");
    photoLabel.className="photo-label";
    photoLabel.textContent=p.photo?"Change photo":"＋ Add photo";
    const file=document.createElement("input");
    file.type="file";
    file.accept="image/*";
    file.addEventListener("change",()=>{
      const selected=file.files&&file.files[0];
      if(!selected)return;
      if(!selected.type.startsWith("image/")){
        alert("Please select an image file.");
        return;
      }
      const reader=new FileReader();
      reader.onload=()=>{
        p.photo=reader.result;
        renderPlayers();
        renderTokens();
      };
      reader.readAsDataURL(selected);
    });
    photoLabel.appendChild(file);
    info.append(name,meta,photoLabel);
    row.append(avatar,info);
    list.appendChild(row);
  });
}

function renderTokens(){
  const container=$("tokens");
  container.innerHTML="";
  players.forEach((p,i)=>{
    const token=document.createElement("div");
    token.className="token"+(i===current?" active-token":"");
    token.style.setProperty("--token-color",p.color);
    token.dataset.player=i;
    if(p.photo){
      const img=document.createElement("img");
      img.src=p.photo;
      token.appendChild(img);
    }else{
      const initial=document.createElement("span");
      initial.className="initial";
      initial.textContent=p.name.trim().charAt(0).toUpperCase()||String(i+1);
      token.appendChild(initial);
    }
    container.appendChild(token);
  });
  positionTokens();
}

function positionTokens(){
  players.forEach((p,i)=>{
    const token=$("tokens").querySelector(`[data-player="${i}"]`);
    if(!token)return;
    const square=p.position===0?1:p.position;
    const c=coordinates(square);
    // Starting players are grouped just beside the starting square.
    const offsets=[
      [-12,-10],[12,-10],[-12,11],[12,11]
    ];
    let x=c.x,y=c.y;
    if(p.position===0){
      x+=offsets[i][0];
      y+=offsets[i][1];
    }else{
      const same=players.slice(0,i).filter(other=>other.position===p.position).length;
      x+=offsets[same%4][0];
      y+=offsets[same%4][1];
    }
    token.style.left=(x/10)+"%";
    token.style.top=(y/10)+"%";
    token.classList.toggle("active-token",i===current);
  });
}

function updateTurn(){
  if(!players.length)return;
  $("turnName").textContent=players[current].name||`Player ${current+1}`;
  renderPlayers();
  positionTokens();
}

function showStatus(message){$("status").textContent=message}

function setDiceFace(n){
  const dice=$("dice");
  const rotations={
    1:"rotateX(0deg) rotateY(0deg)",
    2:"rotateX(0deg) rotateY(-90deg)",
    3:"rotateX(-90deg) rotateY(0deg)",
    4:"rotateX(90deg) rotateY(0deg)",
    5:"rotateX(0deg) rotateY(90deg)",
    6:"rotateX(0deg) rotateY(180deg)"
  };
  dice.style.transform=`translateZ(-44px) ${rotations[n]}`;
  // Replace front face dots with the rolled number.
  const front=dice.querySelector(".front");
  const layouts={
    1:["d3"],
    2:["d1","d5"],
    3:["d1","d3","d5"],
    4:["d1","d2","d4","d5"],
    5:["d1","d2","d3","d4","d5"],
    6:["d1","d2","d3","d4","d5","d6"]
  };
  front.innerHTML="";
  layouts[n].forEach(cls=>{
    const dot=document.createElement("i");
    dot.className="dot "+cls;
    front.appendChild(dot);
  });
}

async function movePlayer(playerIndex,steps){
  const p=players[playerIndex];
  const destination=p.position+steps;
  if(destination>100){
    showStatus(`${p.name} needs an exact roll to reach 100.`);
    return false;
  }
  for(let square=p.position+1;square<=destination;square++){
    p.position=square;
    positionTokens();
    await wait(220);
  }
  renderPlayers();
  await wait(350);

  const ladder=LADDERS.find(x=>x.from===p.position);
  const snake=SNAKES.find(x=>x.from===p.position);

  if(ladder){
    showStatus(`🪜 ${p.name} landed on a ladder! Climbing from ${ladder.from} to ${ladder.to}.`);
    await wait(500);
    p.position=ladder.to;
    positionTokens();
    await wait(800);
    renderPlayers();
  }else if(snake){
    showStatus(`🐍 ${p.name} got caught by a snake! Sliding from ${snake.from} to ${snake.to}.`);
    await wait(500);
    p.position=snake.to;
    positionTokens();
    await wait(800);
    renderPlayers();
  }
  return true;
}

async function rollDice(){
  if(busy||gameOver)return;
  busy=true;
  $("rollBtn").disabled=true;
  const dice=$("dice");
  dice.classList.add("rolling");
  $("diceResult").textContent="Rolling...";
  showStatus(`${players[current].name} is rolling...`);

  await wait(1100);
  const roll=Math.floor(Math.random()*6)+1;
  lastRoll=roll;
  dice.classList.remove("rolling");
  setDiceFace(roll);
  $("diceResult").textContent=`You rolled a ${roll}!`;

  const playerIndex=current;
  const p=players[playerIndex];
  await wait(250);
  const moved=await movePlayer(playerIndex,roll);

  if(moved&&p.position===100){
    gameOver=true;
    $("winnerTitle").textContent=`${p.name} wins!`;
    $("winBanner").classList.add("show");
    showStatus(`🏆 ${p.name} reached tile 100 and won!`);
    busy=false;
    return;
  }

  current=(current+1)%players.length;
  updateTurn();
  showStatus(`${players[current].name}'s turn. Roll the dice!`);
  $("rollBtn").disabled=false;
  busy=false;
}

function restart(){
  busy=false;
  gameOver=false;
  current=0;
  players.forEach(p=>p.position=0);
  $("winBanner").classList.remove("show");
  $("dice").classList.remove("rolling");
  setDiceFace(1);
  $("diceResult").textContent="Ready to roll!";
  $("rollBtn").disabled=false;
  renderPlayers();
  renderTokens();
  updateTurn();
  showStatus(`${players[0].name}, roll the dice to begin.`);
}

$("playerCount").addEventListener("change",e=>{
  makePlayers(Number(e.target.value));
  restart();
});
$("rollBtn").addEventListener("click",rollDice);
$("resetBtn").addEventListener("click",restart);
$("playAgain").addEventListener("click",restart);

buildBoard();
drawConnections();
makePlayers(2);
setDiceFace(1);
showStatus("Player 1, roll the dice to begin.");
</script>
</body>
</html>
