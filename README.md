
<!doctype html>
<html lang="en">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width,initial-scale=1">
<title>Snakes & Ladders</title>
<style>
:root{--ink:#203047;--cream:#fff9ec;--green:#287c62;--gold:#f3c75b}
*{box-sizing:border-box}
body{margin:0;background:linear-gradient(135deg,#e8f5ee,#fff2d5);font-family:system-ui,-apple-system,Segoe UI,sans-serif;color:var(--ink);min-height:100vh}
main{max-width:900px;margin:auto;padding:22px 14px 36px}
.top{text-align:center;margin-bottom:16px}
.top h1{margin:0;font-size:clamp(2rem,6vw,3.2rem);color:#245c4a}
.top p{margin:5px 0;color:#566477}
.layout{display:grid;grid-template-columns:minmax(0,1fr) 250px;gap:18px;align-items:start}
.board{display:grid;grid-template-columns:repeat(10,1fr);gap:3px;background:#285b4c;padding:7px;border-radius:18px;box-shadow:0 12px 28px #254b3b33;aspect-ratio:1}
.cell{position:relative;border-radius:5px;display:flex;align-items:center;justify-content:center;min-width:0;font-weight:800;font-size:clamp(9px,2vw,15px);overflow:hidden}
.cell:nth-child(odd){background:#fff4d8}
.cell:nth-child(even){background:#cce8d6}
.cell .num{position:absolute;top:2px;left:4px;opacity:.65;font-size:.75em}
.cell .mark{font-size:clamp(12px,2.7vw,24px);filter:drop-shadow(0 1px 1px #fff)}
.tokens{position:absolute;bottom:2px;right:2px;display:flex;gap:1px;flex-wrap:wrap;justify-content:flex-end;max-width:90%}
.token{width:clamp(10px,2vw,17px);height:clamp(10px,2vw,17px);border:2px solid white;border-radius:50%;box-shadow:0 1px 3px #3338}
.panel{background:#fffdf7;border:1px solid #eadfc8;border-radius:18px;padding:16px;box-shadow:0 8px 20px #314b3310}
.panel h2{font-size:1.1rem;margin:0 0 10px}
.players{display:grid;gap:8px;margin-bottom:16px}
.player{padding:9px 10px;border:2px solid transparent;border-radius:12px;background:#f4f3ec;display:flex;align-items:center;gap:9px}
.player.active{border-color:var(--green);background:#e8f5ed}
.dot{width:15px;height:15px;border-radius:50%;border:2px solid white;box-shadow:0 0 0 1px #7776}
.player small{display:block;color:#687386}
.dicebox{text-align:center;padding:12px;background:#f8f1df;border-radius:14px;margin:10px 0}
.die{font-size:58px;line-height:1.1}
.roll{width:100%;border:0;border-radius:12px;padding:13px;background:#287c62;color:white;font-weight:800;font-size:1rem;cursor:pointer;box-shadow:0 4px 0 #17543f}
.roll:disabled{opacity:.5;cursor:not-allowed}
.message{min-height:52px;margin:12px 0;font-weight:650;line-height:1.35}
.reset{width:100%;padding:10px;border-radius:11px;border:1px solid #c9d2c9;background:white;color:#30483e;font-weight:700;cursor:pointer}
.rules{font-size:.88rem;color:#657164;line-height:1.5;margin-top:14px}
.legend{display:flex;gap:12px;flex-wrap:wrap;font-size:.83rem;margin-top:12px;color:#526356}
@media(max-width:680px){
.layout{grid-template-columns:1fr}
.panel{order:-1}
.players{grid-template-columns:repeat(2,minmax(0,1fr))}
.board{max-width:600px;margin:auto;width:100%}
.panel{padding:13px}
.rules{margin-top:8px}
}
</style>
</head>
<body>
<main>
<header class="top">
<h1>🐍 Snakes & Ladders 🪜</h1>
<p>Roll the dice, climb the ladders, and watch out for snakes!</p>
</header>
<section class="layout">
<div>
<div class="board" id="board" aria-label="Snakes and Ladders game board"></div>
<div class="legend">
<span>🪜 Ladder = climb up</span>
<span>🐍 Snake = slide down</span>
</div>
</div>
<aside class="panel">
<h2>Players</h2>
<div class="players" id="players"></div>
<div class="dicebox">
<div class="die" id="die">⚀</div>
<div id="turn">Player 1's turn</div>
</div>
<button class="roll" id="roll">🎲 Roll the dice</button>
<div class="message" id="message" aria-live="polite">Player 1 goes first. Roll to begin!</div>
<button class="reset" id="reset">↻ New game</button>
<div class="rules">
<b>How to play</b><br>
• 2–4 players take turns rolling one die.<br>
• Land at the bottom of a ladder to climb up.<br>
• Land on a snake's head and slide down.<br>
• You need the exact roll to reach 100.
</div>
</aside>
</section>
</main>
<script>
const ladders={3:22,8:30,20:41,28:55,36:57,51:72,71:92,80:99};
const snakes={17:7,54:34,62:19,64:60,87:24,93:73,95:75,98:79};
const colors=['#e34f55','#3478d4','#e5a52e','#8a59bd'];
const faces=['⚀','⚁','⚂','⚃','⚄','⚅'];

let count=2,positions=[0,0],turn=0,finished=false;

const board=document.getElementById('board');
const playersEl=document.getElementById('players');

function cellOrder(){
let a=[];
for(let row=9;row>=0;row--){
let nums=Array.from({length:10},(_,i)=>row*10+i+1);
if((9-row)%2===1)nums.reverse();
a.push(...nums);
}
return a;
}

function renderBoard(){
board.innerHTML='';
for(const n of cellOrder()){
const c=document.createElement('div');
c.className='cell';
c.id='cell'+n;

const num=document.createElement('span');
num.className='num';
num.textContent=n;
c.append(num);

if(ladders[n]){
let m=document.createElement('span');
m.className='mark';
m.textContent='🪜';
c.append(m);
c.title='Ladder to '+ladders[n];
}

if(snakes[n]){
let m=document.createElement('span');
m.className='mark';
m.textContent='🐍';
c.append(m);
c.title='Snake down to '+snakes[n];
}

const ts=document.createElement('div');
ts.className='tokens';
ts.id='tokens'+n;
c.append(ts);
board.append(c);
}
}

function renderPlayers(){
playersEl.innerHTML='';

for(let i=0;i<count;i++){
const p=document.createElement('div');
p.className='player'+(i===turn&&!finished?' active':'');
p.innerHTML=`<span class="dot" style="background:${colors[i]}"></span><div><b>Player ${i+1}</b><small>Square ${positions[i]||'Start'}</small></div>`;
playersEl.append(p);
}

document.querySelectorAll('.tokens').forEach(x=>x.innerHTML='');

positions.forEach((pos,i)=>{
if(pos){
const t=document.createElement('span');
t.className='token';
t.style.background=colors[i];
t.title='Player '+(i+1);
document.getElementById('tokens'+pos).append(t);
}
});

document.getElementById('turn').textContent=finished?'Game over':`Player ${turn+1}'s turn`;
}

function say(s){
document.getElementById('message').textContent=s;
}

function next(){
turn=(turn+1)%count;
renderPlayers();
}

document.getElementById('roll').addEventListener('click',()=>{
if(finished)return;

const roll=Math.floor(Math.random()*6)+1;
document.getElementById('die').textContent=faces[roll-1];

const name='Player '+(turn+1);

if(positions[turn]+roll>100){
say(`${name} rolled ${roll}, but needs an exact roll to reach 100.`);
next();
return;
}

positions[turn]+=roll;
let msg=`${name} rolled ${roll} and moved to ${positions[turn]}.`;

if(ladders[positions[turn]]){
let dest=ladders[positions[turn]];
msg+=` Ladder! Climbs to ${dest}.`;
positions[turn]=dest;
}
else if(snakes[positions[turn]]){
let dest=snakes[positions[turn]];
msg+=` Snake! Slides down to ${dest}.`;
positions[turn]=dest;
}

renderPlayers();

if(positions[turn]===100){
finished=true;
document.getElementById('roll').disabled=true;
say(`🏆 ${name} wins! Congratulations!`);
renderPlayers();
return;
}

say(msg);
next();
});

document.getElementById('reset').addEventListener('click',()=>{
count=2;
positions=[0,0];
turn=0;
finished=false;
document.getElementById('die').textContent='⚀';
document.getElementById('roll').disabled=false;
renderBoard();
renderPlayers();
say('Player 1 goes first. Roll to begin!');
});

renderBoard();
renderPlayers();
</script>
</body>
</html>
