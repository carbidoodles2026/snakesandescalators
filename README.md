<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Snakes & Ladders</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Baloo+2:wght@500;700;800&display=swap" rel="stylesheet">
<style>
*{box-sizing:border-box;-webkit-tap-highlight-color:transparent}
html,body{margin:0}
body{
  min-height:100vh;color:#fff;
  font-family:'Baloo 2','Fredoka','Comic Sans MS',system-ui,sans-serif;
  background:radial-gradient(circle at 20% 8%,#7c3aed 0,#312e81 45%,#1e1b4b 100%) fixed;
  touch-action:manipulation;
}
.hidden{display:none!important}
button{font-family:inherit;cursor:pointer}

/* ---------- SETUP ---------- */
#setup{display:flex;justify-content:center;padding:22px 14px 40px}
.setup-card{width:min(560px,100%);background:rgba(255,255,255,.12);backdrop-filter:blur(8px);border:2px solid rgba(255,255,255,.25);border-radius:26px;padding:22px 20px;box-shadow:0 20px 50px rgba(0,0,0,.4)}
.title{font-size:clamp(30px,7vw,46px);font-weight:800;margin:0 0 2px;text-align:center;line-height:1.05;
  background:linear-gradient(90deg,#fde047,#fb923c,#f472b6,#a78bfa,#38bdf8);-webkit-background-clip:text;background-clip:text;color:transparent;filter:drop-shadow(0 3px 0 rgba(0,0,0,.35))}
.sub{text-align:center;opacity:.85;margin:0 0 16px}
.count-row{display:flex;gap:10px;justify-content:center;margin-bottom:16px;align-items:center}
.count-row span{font-weight:700}
.cbtn{width:48px;height:48px;border-radius:14px;border:3px solid rgba(255,255,255,.4);background:rgba(255,255,255,.1);color:#fff;font-size:22px;font-weight:800;transition:.15s}
.cbtn.on{background:linear-gradient(135deg,#fbbf24,#f97316);border-color:#fff;transform:scale(1.08);box-shadow:0 6px 14px rgba(0,0,0,.35)}
.pcard{display:flex;gap:14px;align-items:center;background:rgba(0,0,0,.25);border-left:8px solid var(--pc);border-radius:18px;padding:10px 12px;margin-bottom:10px}
.avatar-pick{cursor:pointer;flex:none}
.avatar{width:70px;height:70px;border-radius:50%;border:4px solid var(--pc);background:#fff center/cover no-repeat;display:grid;place-items:center;font-size:28px;box-shadow:0 0 0 3px #fff,0 6px 12px rgba(0,0,0,.4);position:relative}
.avatar.empty{background:linear-gradient(135deg,rgba(255,255,255,.9),rgba(255,255,255,.6));color:#333}
.pinfo{flex:1;min-width:0}
.pname{width:100%;font:inherit;font-weight:700;font-size:18px;padding:7px 12px;border-radius:12px;border:2px solid rgba(255,255,255,.35);background:rgba(255,255,255,.92);color:#222}
.hint{display:block;font-size:12.5px;opacity:.8;margin-top:4px;line-height:1.2}
.linkbtn{background:none;border:none;color:#fca5a5;font-size:12.5px;font-weight:700;padding:0;text-decoration:underline}
.bigbtn{display:block;width:100%;margin-top:14px;padding:14px;border-radius:18px;border:none;font-size:24px;font-weight:800;color:#fff;
  background:linear-gradient(135deg,#22c55e,#16a34a);box-shadow:0 6px 0 #14532d,0 12px 20px rgba(0,0,0,.35);transition:.1s}
.bigbtn:active{transform:translateY(4px);box-shadow:0 2px 0 #14532d}

/* ---------- GAME ---------- */
.layout{display:flex;gap:20px;justify-content:center;align-items:flex-start;padding:22px 14px;flex-wrap:wrap}
.board-wrap{--bs:min(calc(100vw - 52px),calc(100vh - 60px),780px);width:var(--bs);margin:6px}
#board{position:relative;width:100%;aspect-ratio:1;border-radius:18px;background:#fff;
  box-shadow:0 0 0 9px #f59e0b,0 0 0 13px #92400e,0 20px 40px rgba(0,0,0,.55)}
#boardSvg{display:block;width:100%;height:100%;border-radius:14px}
#tokens{position:absolute;inset:0;pointer-events:none}
.token{position:absolute;width:4.8%;aspect-ratio:1;transform:translate(-50%,-50%);transition:left .24s linear,top .24s linear;z-index:5}
.token.nomove{transition:none}
.token.active{z-index:8}
.token .in{width:100%;height:100%;border-radius:50%;border:3px solid var(--pc);background:var(--pc) center/cover no-repeat;
  box-shadow:0 0 0 2px #fff,0 4px 8px rgba(0,0,0,.55);display:grid;place-items:center;font-weight:800;color:#fff;
  font-size:clamp(9px,1.9vw,17px);text-shadow:0 1px 2px rgba(0,0,0,.6)}
.token.nophoto .in{background:radial-gradient(circle at 32% 28%,rgba(255,255,255,.7),var(--pc) 55%)}
.token.active .in{animation:pulse 1s ease-in-out infinite}
@keyframes pulse{50%{box-shadow:0 0 0 2px #fff,0 0 0 7px rgba(255,255,255,.55),0 4px 8px rgba(0,0,0,.55)}}
.token .in.hop{animation:hop .24s ease-out}
@keyframes hop{0%{transform:translateY(0) scale(1)}45%{transform:translateY(-75%) scale(1.18)}100%{transform:translateY(0) scale(1)}}
.token .in.climb{animation:climb .45s ease-in-out infinite}
@keyframes climb{0%,100%{transform:rotate(-12deg) scale(1.1)}50%{transform:rotate(12deg) scale(1.1)}}
.token .in.ride{transform:scale(1.15)}
.token .in.slide{animation:slide .5s linear infinite}
@keyframes slide{to{transform:rotate(360deg) scale(.9)}}

#toast{position:absolute;left:50%;top:44%;transform:translate(-50%,-50%) scale(.6);opacity:0;z-index:20;pointer-events:none;
  background:rgba(0,0,0,.78);color:#fff;font-weight:800;font-size:clamp(20px,4.6vw,38px);padding:10px 22px;border-radius:22px;border:4px solid #fff;
  text-align:center;white-space:nowrap;transition:.25s}
#toast.show{opacity:1;transform:translate(-50%,-50%) scale(1)}

aside{width:min(350px,100%);display:flex;flex-direction:column;gap:12px}
.panel{background:rgba(255,255,255,.12);border:2px solid rgba(255,255,255,.22);border-radius:20px;padding:10px}
.prow{display:flex;align-items:center;gap:10px;padding:6px 8px;border-radius:14px;border:3px solid transparent;transition:.2s}
.prow.active{background:rgba(255,255,255,.2);border-color:var(--pc);transform:scale(1.02)}
.prow .av{width:42px;height:42px;border-radius:50%;border:3px solid var(--pc);background:var(--pc) center/cover no-repeat;flex:none;display:grid;place-items:center;font-weight:800;box-shadow:0 0 0 2px #fff}
.prow .nm{font-weight:800;font-size:17px;line-height:1}
.prow .tl{font-size:13px;opacity:.85}
.prow .bar{height:6px;border-radius:4px;background:rgba(255,255,255,.2);margin-top:3px;overflow:hidden}
.prow .bar i{display:block;height:100%;background:var(--pc);transition:width .3s}
.prow .meta{flex:1;min-width:0}

.dice-panel{text-align:center}
.turn{font-weight:800;font-size:18px;margin-bottom:2px}
.turn b{color:var(--pc)}
.scene{width:130px;height:130px;margin:0 auto;perspective:650px;position:relative;cursor:pointer}
.shadow{position:absolute;left:50%;bottom:6px;width:70px;height:14px;border-radius:50%;background:rgba(0,0,0,.45);filter:blur(4px);transform:translateX(-50%)}
.shadow.bounce{animation:shadow 1.6s ease-out}
@keyframes shadow{0%{transform:translateX(-50%) scale(1)}18%{transform:translateX(-50%) scale(.55);opacity:.5}36%{transform:translateX(-50%) scale(1)}52%{transform:translateX(-50%) scale(.75);opacity:.7}68%{transform:translateX(-50%) scale(1)}82%{transform:translateX(-50%) scale(.9)}100%{transform:translateX(-50%) scale(1)}}
.dwrap{position:absolute;inset:0;display:grid;place-items:center;padding-bottom:14px}
.dwrap.bounce{animation:dbounce 1.6s ease-out}
@keyframes dbounce{0%{transform:translateY(-6px)}18%{transform:translateY(-70px)}36%{transform:translateY(0)}52%{transform:translateY(-30px)}68%{transform:translateY(0)}82%{transform:translateY(-8px)}100%{transform:translateY(0)}}
.tilt{width:76px;height:76px;transform-style:preserve-3d;transform:rotateX(-24deg) rotateY(-28deg)}
.cube{width:76px;height:76px;position:relative;transform-style:preserve-3d}
.face{position:absolute;inset:0;border-radius:14px;
  background:radial-gradient(circle at 30% 25%,#fff 0,#f6f6f6 55%,#dcdcdc 100%);
  box-shadow:inset 0 0 9px rgba(0,0,0,.2),inset 0 -3px 6px rgba(0,0,0,.08);
  display:grid;grid-template:repeat(3,1fr)/repeat(3,1fr);padding:11px;backface-visibility:hidden}
.pip{place-self:center;width:84%;height:84%;border-radius:50%;
  background:radial-gradient(circle at 35% 30%,#666 0,#000 60%);box-shadow:inset 0 -1px 2px rgba(255,255,255,.25)}
.rollbtn{width:100%;margin-top:4px;padding:12px;border-radius:16px;border:none;font-size:22px;font-weight:800;color:#fff;
  background:linear-gradient(135deg,#f43f5e,#e11d48);box-shadow:0 5px 0 #881337,0 10px 16px rgba(0,0,0,.3)}
.rollbtn:active:not(:disabled){transform:translateY(3px);box-shadow:0 2px 0 #881337}
.rollbtn:disabled{filter:grayscale(.6) brightness(.8);cursor:not-allowed}
.rolled{min-height:24px;font-weight:700;margin-top:6px}
#log{max-height:110px;overflow:auto;font-size:14px;line-height:1.35}
#log div{padding:2px 4px;border-bottom:1px dashed rgba(255,255,255,.15)}
.btnrow{display:flex;gap:8px}
.smallbtn{flex:1;padding:9px;border-radius:12px;border:2px solid rgba(255,255,255,.35);background:rgba(255,255,255,.1);color:#fff;font-weight:700;font-size:15px}

/* ---------- WINNER ---------- */
#winner{position:fixed;inset:0;background:rgba(10,5,40,.82);display:grid;place-items:center;z-index:100;overflow:hidden}
.wcard{text-align:center;background:linear-gradient(160deg,#4c1d95,#1e3a8a);border:4px solid #fde047;border-radius:30px;padding:26px 30px;max-width:92vw;position:relative;z-index:2;animation:pop .5s cubic-bezier(.2,1.6,.4,1)}
@keyframes pop{from{transform:scale(.3);opacity:0}}
.wcard .big{width:120px;height:120px;border-radius:50%;margin:0 auto 8px;border:6px solid var(--pc);background:var(--pc) center/cover no-repeat;box-shadow:0 0 0 4px #fff,0 0 40px #fde047;display:grid;place-items:center;font-size:50px;font-weight:800}
.wcard h2{margin:0;font-size:34px}
.wcard p{margin:4px 0 14px;opacity:.9}
.conf{position:absolute;top:-20px;width:10px;height:16px;animation:fall linear infinite;z-index:1}
@keyframes fall{to{transform:translateY(110vh) rotate(720deg)}}
.esc{animation:escmove .6s linear infinite}
.escg{animation:escgroove .6s linear infinite}
@keyframes escmove{from{stroke-dashoffset:0}to{stroke-dashoffset:-16}}
@keyframes escgroove{from{stroke-dashoffset:-7}to{stroke-dashoffset:-23}}
.tongue{transform-box:view-box;animation:flick 1.2s ease-in-out infinite}
@keyframes flick{0%,70%,100%{opacity:1}80%{opacity:.2}}
</style>
</head>
<body>

<!-- ================= SETUP ================= -->
<div id="setup">
  <div class="setup-card">
    <h1 class="title">🐍 Snakes &amp; Ladders 🪜</h1>
    <p class="sub">Pumili ng bilang ng players at lagyan ng photo mula sa gallery!</p>
    <div class="count-row">
      <span>Players:</span>
      <button class="cbtn" data-n="2">2</button>
      <button class="cbtn" data-n="3">3</button>
      <button class="cbtn" data-n="4">4</button>
    </div>
    <div id="playerCards"></div>
    <button class="bigbtn" id="startBtn">▶ Start Game</button>
  </div>
</div>

<!-- ================= GAME ================= -->
<div id="game" class="hidden">
  <div class="layout">
    <div class="board-wrap">
      <div id="board">
        <div id="boardHost"></div>
        <div id="tokens"></div>
        <div id="toast"></div>
      </div>
    </div>
    <aside>
      <div class="panel" id="players"></div>
      <div class="panel dice-panel">
        <div class="turn" id="turnLabel"></div>
        <div class="scene" id="scene" title="Click to roll">
          <div class="shadow" id="shadow"></div>
          <div class="dwrap" id="dwrap"><div class="tilt"><div class="cube" id="cube"></div></div></div>
        </div>
        <div class="rolled" id="rolled">&nbsp;</div>
        <button class="rollbtn" id="rollBtn">🎲 Roll the dice!</button>
      </div>
      <div class="panel" id="log"></div>
      <div class="btnrow">
        <button class="smallbtn" id="restartBtn">↻ Restart</button>
        <button class="smallbtn" id="setupBtn">⚙ Change players</button>
      </div>
    </aside>
  </div>
</div>

<!-- ================= WINNER ================= -->
<div id="winner" class="hidden">
  <div class="wcard" id="wcard"></div>
</div>

<script>
/* =====================================================
   CONFIG
===================================================== */
const SIZE = 1000, S = 100;
const SNAKES  = {16:6, 47:26, 49:11, 62:19, 64:60, 87:24, 93:73, 95:75, 98:78};   // head -> tail
const LADDERS = {2:23, 8:34, 20:77, 28:84, 36:44, 51:67, 71:91, 4:56};            // bottom -> top
const ESCALATOR_MIN_ROWS = 5;   // ladder rises >= this many rows becomes an escalator
const COLORS = ['#ef4444','#3b82f6','#22c55e','#f59e0b'];

const SNAKE_STYLES = [
  {a:'#22c55e', b:'#fde047', pat:'spots',   acc:null,    eyes:'googly'},
  {a:'#a855f7', b:'#f9a8d4', pat:'stripes', acc:null,    eyes:'googly'},
  {a:'#fb923c', b:'#ef4444', pat:'stripes', acc:'bow',   eyes:'googly'},
  {a:'#14b8a6', b:'#bef264', pat:'spots',   acc:null,    eyes:'shades'},
  {a:'#3b82f6', b:'#67e8f9', pat:'stripes', acc:null,    eyes:'googly'},
  {a:'#ec4899', b:'#fde68a', pat:'spots',   acc:'crown', eyes:'googly'},
  {a:'#ef4444', b:'#fde047', pat:'stripes', acc:null,    eyes:'shades'},
  {a:'#84cc16', b:'#166534', pat:'spots',   acc:null,    eyes:'googly'},
  {a:'#8b5cf6', b:'#fdba74', pat:'stripes', acc:'hat',   eyes:'googly'},
];

/* =====================================================
   BOARD GEOMETRY
===================================================== */
function center(n){
  const idx = n - 1, row = Math.floor(idx / 10), c = idx % 10;
  const col = row % 2 === 0 ? c : 9 - c;
  return { x: col * S + S / 2, y: (9 - row) * S + S / 2, row, col };
}
const f1 = v => v.toFixed(1);

/* ---------- Cartoon snake ---------- */
const SNAKE_DATA = {};   // head -> {pts}
function snakeSVG(head, tail, st, idx){
  const A = center(head), B = center(tail);
  const dx = B.x - A.x, dy = B.y - A.y, len = Math.hypot(dx, dy);
  const ux = dx / len, uy = dy / len, px = -uy, py = ux;
  const waves = Math.max(1.5, len / 150);
  const amp = Math.min(55, 18 + len * 0.07);
  const sgn = idx % 2 ? 1 : -1;
  const N = Math.max(30, Math.round(len / 5));
  const pts = [];
  for (let i = 0; i <= N; i++){
    const t = i / N;
    const env = Math.pow(Math.sin(Math.PI * t), 0.8);
    const off = Math.sin(t * waves * Math.PI * 2) * amp * env * sgn;
    pts.push({ x: A.x + dx * t + px * off, y: A.y + dy * t + py * off });
  }
  SNAKE_DATA[head] = { pts };
  const rad = i => 4.5 + 13.5 * Math.pow(1 - i / N, 0.7);

  let out = '';
  // soft shadow
  for (let i = 0; i <= N; i += 2){
    const p = pts[i];
    out += `<circle cx="${f1(p.x + 5)}" cy="${f1(p.y + 8)}" r="${f1(rad(i) + 3)}" fill="rgba(0,0,0,.22)"/>`;
  }
  // outline
  for (let i = N; i >= 0; i--){
    const p = pts[i];
    out += `<circle cx="${f1(p.x)}" cy="${f1(p.y)}" r="${f1(rad(i) + 3.5)}" fill="#16202e"/>`;
  }
  // body
  for (let i = N; i >= 0; i--){
    const p = pts[i], r = rad(i);
    let col = st.a;
    if (st.pat === 'stripes' && Math.floor(i / 5) % 2) col = st.b;
    out += `<circle cx="${f1(p.x)}" cy="${f1(p.y)}" r="${f1(r)}" fill="${col}"/>`;
    if (st.pat === 'spots' && i % 9 === 4 && i > 3) {
      out += `<circle cx="${f1(p.x)}" cy="${f1(p.y)}" r="${f1(r * 0.5)}" fill="${st.b}" stroke="#16202e" stroke-opacity=".35" stroke-width="1.5"/>`;
    }
  }
  // glossy highlight
  for (let i = N; i >= 2; i -= 1){
    const p = pts[i], r = rad(i);
    out += `<circle cx="${f1(p.x - r * .3)}" cy="${f1(p.y - r * .35)}" r="${f1(r * .3)}" fill="#fff" opacity=".18"/>`;
  }
  // tail tip curl
  const pt = pts[N];
  out += `<circle cx="${f1(pt.x)}" cy="${f1(pt.y)}" r="4" fill="${st.b}" stroke="#16202e" stroke-width="2"/>`;

  // head
  const p0 = pts[0], p4 = pts[Math.min(5, N)];
  const ang = Math.atan2(p0.y - p4.y, p0.x - p4.x) * 180 / Math.PI;
  let eyes;
  if (st.eyes === 'shades'){
    eyes = `
      <ellipse cx="13" cy="-11" rx="8.5" ry="7.5" fill="#111" stroke="#fff" stroke-width="1.5"/>
      <ellipse cx="13" cy="11"  rx="8.5" ry="7.5" fill="#111" stroke="#fff" stroke-width="1.5"/>
      <rect x="10" y="-4" width="6" height="8" fill="#111"/>
      <ellipse cx="10" cy="-13" rx="2.6" ry="1.6" fill="#fff" opacity=".8"/>
      <ellipse cx="10" cy="9"   rx="2.6" ry="1.6" fill="#fff" opacity=".8"/>`;
  } else {
    eyes = `
      <circle cx="13" cy="-12" r="9" fill="#fff" stroke="#16202e" stroke-width="2.5"/>
      <circle cx="13" cy="12"  r="9" fill="#fff" stroke="#16202e" stroke-width="2.5"/>
      <circle cx="16.5" cy="-11" r="4.6" fill="#111"/><circle cx="16.5" cy="13" r="4.6" fill="#111"/>
      <circle cx="18" cy="-12.8" r="1.7" fill="#fff"/><circle cx="18" cy="11.2" r="1.7" fill="#fff"/>`;
  }
  out += `<g transform="translate(${f1(p0.x)},${f1(p0.y)}) rotate(${f1(ang)})">
    <g class="tongue">
      <path d="M32 0 L50 0 M50 0 L58 -7 M50 0 L58 7" stroke="#16202e" stroke-width="7" stroke-linecap="round" stroke-linejoin="round" fill="none"/>
      <path d="M32 0 L50 0 M50 0 L58 -7 M50 0 L58 7" stroke="#ff2d55" stroke-width="3.5" stroke-linecap="round" stroke-linejoin="round" fill="none"/>
    </g>
    <ellipse cx="10" cy="0" rx="27" ry="22" fill="${st.a}" stroke="#16202e" stroke-width="4"/>
    <ellipse cx="17" cy="0" rx="16" ry="11" fill="${st.b}" opacity=".35"/>
    <circle cx="-2" cy="-17" r="4.5" fill="#ff7aa8" opacity=".65"/><circle cx="-2" cy="17" r="4.5" fill="#ff7aa8" opacity=".65"/>
    ${eyes}
    <circle cx="33" cy="-4" r="1.9" fill="#16202e"/><circle cx="33" cy="4" r="1.9" fill="#16202e"/>
  </g>`;

  // upright accessories
  const hx = p0.x, hy = p0.y - 24;
  if (st.acc === 'crown'){
    out += `<g transform="translate(${f1(hx)},${f1(hy)})"><path d="M-14 4 L-14 -12 L-7 -4 L0 -15 L7 -4 L14 -12 L14 4 Z" fill="#fbbf24" stroke="#16202e" stroke-width="3" stroke-linejoin="round"/><circle cx="0" cy="-1" r="2.6" fill="#ef4444"/></g>`;
  } else if (st.acc === 'bow'){
    out += `<g transform="translate(${f1(hx)},${f1(hy + 4)})"><path d="M0 0 L-16 -9 L-16 9 Z M0 0 L16 -9 L16 9 Z" fill="#ec4899" stroke="#16202e" stroke-width="3" stroke-linejoin="round"/><circle cx="0" cy="0" r="4.5" fill="#f9a8d4" stroke="#16202e" stroke-width="2.5"/></g>`;
  } else if (st.acc === 'hat'){
    out += `<g transform="translate(${f1(hx)},${f1(hy + 4)})"><rect x="-10" y="-20" width="20" height="20" rx="2" fill="#1f2937" stroke="#16202e" stroke-width="3"/><rect x="-10" y="-7" width="20" height="5" fill="#ef4444"/><rect x="-18" y="-2" width="36" height="6" rx="3" fill="#1f2937" stroke="#16202e" stroke-width="3"/></g>`;
  }
  return out;
}

/* ---------- Wooden ladder ---------- */
function ladderSVG(s, e){
  const A = center(s), B = center(e);
  const dx = B.x - A.x, dy = B.y - A.y, len = Math.hypot(dx, dy);
  const ux = dx / len, uy = dy / len, px = -uy, py = ux, hw = 14;
  const line = (pa, pb, stroke, w, extra='') => `<line x1="${pa.x}" y1="${pa.y}" x2="${pb.x}" y2="${pb.y}" stroke="${stroke}" stroke-width="${w}" stroke-linecap="round" ${extra}/>`;
  const R = sgn => ({ a:{x:f1(A.x+px*hw*sgn), y:f1(A.y+py*hw*sgn)}, b:{x:f1(B.x+px*hw*sgn), y:f1(B.y+py*hw*sgn)} });
  const L = R(-1), Rr = R(1);
  let o = '';
  // shadow
  o += `<g transform="translate(6,8)" opacity=".28">${line(L.a,L.b,'#000',12)}${line(Rr.a,Rr.b,'#000',12)}</g>`;
  // rails
  for (const r of [L, Rr]){
    o += line(r.a, r.b, '#4a2a10', 13);
    o += line(r.a, r.b, '#b9783a', 8.5);
    o += line({x:+r.a.x-1.6,y:+r.a.y-1.6}, {x:+r.b.x-1.6,y:+r.b.y-1.6}, '#e8b277', 2.4);
  }
  // rungs
  const n = Math.max(3, Math.floor(len / 26));
  for (let k = 1; k <= n; k++){
    const t = k / (n + 1);
    const cx = A.x + dx * t, cy = A.y + dy * t;
    const a = { x: f1(cx - px * hw), y: f1(cy - py * hw) }, b = { x: f1(cx + px * hw), y: f1(cy + py * hw) };
    o += line(a, b, '#4a2a10', 10);
    o += line(a, b, '#d9984f', 6);
    o += line({x:+a.x-1,y:+a.y-1.5}, {x:+b.x-1,y:+b.y-1.5}, '#f0c48f', 1.6);
    o += `<circle cx="${a.x}" cy="${a.y}" r="2" fill="#3a1f0a"/><circle cx="${b.x}" cy="${b.y}" r="2" fill="#3a1f0a"/>`;
  }
  return o;
}

/* ---------- Escalator (for very long climbs) ---------- */
function escalatorSVG(s, e){
  const A = center(s), B = center(e);
  const dx = B.x - A.x, dy = B.y - A.y, len = Math.hypot(dx, dy);
  const ux = dx / len, uy = dy / len, px = -uy, py = ux;
  const ang = Math.atan2(dy, dx) * 180 / Math.PI;
  const ln = (x1,y1,x2,y2,stroke,w,extra='') => `<line x1="${f1(x1)}" y1="${f1(y1)}" x2="${f1(x2)}" y2="${f1(y2)}" stroke="${stroke}" stroke-width="${w}" ${extra}/>`;
  let o = '';
  o += ln(A.x+6, A.y+9, B.x+6, B.y+9, 'rgba(0,0,0,.28)', 50, 'stroke-linecap="round"');
  // side handrails
  for (const sg of [-1, 1]){
    const ox = px * 24 * sg, oy = py * 24 * sg;
    o += ln(A.x+ox, A.y+oy, B.x+ox, B.y+oy, '#1b2631', 10, 'stroke-linecap="round"');
    o += ln(A.x+ox-1.5, A.y+oy-1.5, B.x+ox-1.5, B.y+oy-1.5, '#7f8c9a', 2.5, 'stroke-linecap="round"');
  }
  // bed
  o += ln(A.x, A.y, B.x, B.y, '#34495e', 42);
  o += ln(A.x, A.y, B.x, B.y, '#aab7b8', 34);
  // moving steps
  o += ln(A.x, A.y, B.x, B.y, '#f4f6f7', 34, 'class="esc" stroke-dasharray="7 9"');
  o += ln(A.x, A.y, B.x, B.y, '#6c7a89', 34, 'class="escg" stroke-dasharray="1.6 14.4"');
  // end plates
  for (const P of [A, B]){
    o += `<g transform="translate(${f1(P.x)},${f1(P.y)}) rotate(${f1(ang)})">
      <rect x="-7" y="-24" width="14" height="48" rx="3" fill="#f1c40f" stroke="#1b2631" stroke-width="3"/>
      <path d="M-7 -14 H7 M-7 -4 H7 M-7 6 H7 M-7 16 H7" stroke="#1b2631" stroke-width="2.2"/>
    </g>`;
  }
  // direction arrow badge near the base
  const bx = A.x + ux * 40, by = A.y + uy * 40;
  o += `<g transform="translate(${f1(bx)},${f1(by)}) rotate(${f1(ang)})"><circle r="11" fill="#22c55e" stroke="#fff" stroke-width="2.5"/><path d="M-4 -5 L5 0 L-4 5 Z" fill="#fff"/></g>`;
  return o;
}

/* ---------- Build the whole board ---------- */
function buildBoard(){
  let svg = `<svg id="boardSvg" viewBox="0 0 ${SIZE} ${SIZE}" xmlns="http://www.w3.org/2000/svg">
    <defs><linearGradient id="gloss" x1="0" y1="0" x2="0" y2="1">
      <stop offset="0" stop-color="#fff" stop-opacity=".45"/><stop offset=".5" stop-color="#fff" stop-opacity=".05"/><stop offset="1" stop-color="#000" stop-opacity=".14"/>
    </linearGradient></defs>`;
  for (let n = 1; n <= 100; n++){
    const c = center(n), x = c.x - 50, y = c.y - 50;
    const hue = (c.col * 37 + c.row * 23) % 360;
    const light = 58 + ((c.row + c.col) % 2) * 9;
    svg += `<rect x="${x}" y="${y}" width="100" height="100" fill="hsl(${hue} 82% ${light}%)"/>`;
    svg += `<rect x="${x+2}" y="${y+2}" width="96" height="96" rx="10" fill="url(#gloss)" stroke="rgba(255,255,255,.6)" stroke-width="2"/>`;
    svg += `<text x="${x+10}" y="${y+27}" font-size="22" font-weight="800" fill="rgba(0,0,0,.42)" font-family="Baloo 2, Comic Sans MS, sans-serif">${n}</text>`;
    if (n === 1)   svg += `<text x="${c.x}" y="${c.y+22}" text-anchor="middle" font-size="22" font-weight="800" fill="rgba(0,0,0,.6)" font-family="Baloo 2, sans-serif">START</text>`;
    if (n === 100) svg += `<text x="${c.x}" y="${c.y+20}" text-anchor="middle" font-size="52">🏆</text>`;
  }
  // ladders & escalators
  for (const [s, e] of Object.entries(LADDERS)){
    const rows = center(+e).row - center(+s).row;
    svg += rows >= ESCALATOR_MIN_ROWS ? escalatorSVG(+s, +e) : ladderSVG(+s, +e);
  }
  // snakes (on top)
  Object.entries(SNAKES).forEach(([h, t], i) => { svg += snakeSVG(+h, +t, SNAKE_STYLES[i % SNAKE_STYLES.length], i); });
  svg += '</svg>';
  $('#boardHost').innerHTML = svg;
}

/* =====================================================
   HELPERS
===================================================== */
const $ = s => document.querySelector(s);
const sleep = ms => new Promise(r => setTimeout(r, ms));
const ease = t => t < .5 ? 2*t*t : 1 - Math.pow(-2*t + 2, 2) / 2;

/* =====================================================
   SETUP SCREEN
===================================================== */
const cfg = {
  count: 2,
  players: [0,1,2,3].map(i => ({ name: 'Player ' + (i + 1), photo: null }))
};

function processPhoto(file){
  return new Promise((res, rej) => {
    const url = URL.createObjectURL(file), img = new Image();
    img.onload = () => {
      const sz = 192, c = document.createElement('canvas'); c.width = c.height = sz;
      const m = Math.min(img.width, img.height);
      c.getContext('2d').drawImage(img, (img.width - m) / 2, (img.height - m) / 2, m, m, 0, 0, sz, sz);
      URL.revokeObjectURL(url); res(c.toDataURL('image/jpeg', .85));
    };
    img.onerror = rej; img.src = url;
  });
}

function renderSetup(){
  document.querySelectorAll('.cbtn').forEach(b => b.classList.toggle('on', +b.dataset.n === cfg.count));
  const wrap = $('#playerCards'); wrap.innerHTML = '';
  for (let i = 0; i < cfg.count; i++){
    const p = cfg.players[i];
    const card = document.createElement('div');
    card.className = 'pcard'; card.style.setProperty('--pc', COLORS[i]);
    card.innerHTML = `
      <label class="avatar-pick"><div class="avatar ${p.photo ? '' : 'empty'}">${p.photo ? '' : '📷'}</div>
        <input type="file" accept="image/*" hidden></label>
      <div class="pinfo">
        <input class="pname" maxlength="12" placeholder="Name">
        <span class="hint">Tap the circle to choose a photo from your gallery</span>
        ${p.photo ? '<button class="linkbtn">Remove photo</button>' : ''}
      </div>`;
    const av = card.querySelector('.avatar');
    if (p.photo) av.style.backgroundImage = `url(${p.photo})`;
    const nm = card.querySelector('.pname'); nm.value = p.name;
    nm.addEventListener('input', () => p.name = nm.value);
    card.querySelector('input[type=file]').addEventListener('change', async e => {
      const f = e.target.files[0]; if (!f) return;
      try { p.photo = await processPhoto(f); renderSetup(); } catch { alert('Hindi ma-load yung photo. Try another one.'); }
    });
    const rm = card.querySelector('.linkbtn');
    if (rm) rm.addEventListener('click', () => { p.photo = null; renderSetup(); });
    wrap.appendChild(card);
  }
}
document.querySelectorAll('.cbtn').forEach(b => b.addEventListener('click', () => { cfg.count = +b.dataset.n; renderSetup(); }));

/* =====================================================
   DICE
===================================================== */
const PIPS = {1:[4],2:[0,8],3:[0,4,8],4:[0,2,6,8],5:[0,2,4,6,8],6:[0,2,3,5,6,8]};
const FACE_T = {1:'rotateY(0deg)',6:'rotateY(180deg)',2:'rotateY(90deg)',5:'rotateY(-90deg)',3:'rotateX(90deg)',4:'rotateX(-90deg)'};
const BASE = {1:[0,0],2:[0,-90],3:[-90,0],4:[90,0],5:[0,90],6:[0,180]};   // cube rotation that shows value on the front
let rx = 0, ry = 0;

function buildDice(){
  const cube = $('#cube'); cube.innerHTML = '';
  for (let v = 1; v <= 6; v++){
    const face = document.createElement('div'); face.className = 'face';
    face.style.transform = `${FACE_T[v]} translateZ(38px)`;
    for (let k = 0; k < 9; k++){
      const cell = document.createElement('div');
      if (PIPS[v].includes(k)){ const p = document.createElement('div'); p.className = 'pip'; cell.style.display = 'grid'; cell.appendChild(p); }
      face.appendChild(cell);
    }
    cube.appendChild(face);
  }
  rx = 0; ry = 0;
  cube.style.transition = 'none'; cube.style.transform = 'rotateX(0deg) rotateY(0deg)';
}
function align(cur, base, minAdd){ let t = base; while (t < cur + minAdd) t += 360; return t; }
async function rollDiceAnim(v){
  const cube = $('#cube'), dur = 1600;
  rx = align(rx, BASE[v][0], 720 + 360 * Math.floor(Math.random() * 2));
  ry = align(ry, BASE[v][1], 720 + 360 * Math.floor(Math.random() * 3));
  const dw = $('#dwrap'), sh = $('#shadow');
  dw.classList.remove('bounce'); sh.classList.remove('bounce'); void dw.offsetWidth;
  dw.classList.add('bounce'); sh.classList.add('bounce');
  cube.style.transition = `transform ${dur}ms cubic-bezier(.2,.8,.25,1)`;
  cube.style.transform = `rotateX(${rx}deg) rotateY(${ry}deg)`;
  await sleep(dur + 80);
}

/* =====================================================
   GAME STATE
===================================================== */
let players = [], turn = 0, busy = false, over = false;

function offsetFor(i, n){
  const t = { 2: [[-21,0],[21,0]], 3: [[-21,-17],[21,-17],[0,21]], 4: [[-21,-21],[21,-21],[-21,21],[21,21]] };
  return t[n][i];
}
function tileXY(pl, tile){
  const c = center(tile), o = offsetFor(pl.idx, players.length);
  return { x: c.x + o[0], y: c.y + o[1] };
}
function placeToken(pl, x, y){ pl.el.style.left = (x / 10) + '%'; pl.el.style.top = (y / 10) + '%'; }
function setTokenToTile(pl, tile, instant){
  if (instant) pl.el.classList.add('nomove');
  const p = tileXY(pl, tile); placeToken(pl, p.x, p.y);
  if (instant){ void pl.el.offsetWidth; pl.el.classList.remove('nomove'); }
}
function hop(pl){ const i = pl.el.firstChild; i.classList.remove('hop'); void i.offsetWidth; i.classList.add('hop'); }

function animateAlong(pl, pts, dur, mode){
  return new Promise(res => {
    const o = offsetFor(pl.idx, players.length);
    const path = pts.map(p => ({ x: p.x + o[0], y: p.y + o[1] }));
    const inner = pl.el.firstChild;
    pl.el.classList.add('nomove'); inner.classList.remove('hop'); inner.classList.add(mode);
    const t0 = performance.now();
    (function frame(now){
      const t = Math.min(1, (now - t0) / dur), e = ease(t);
      const f = e * (path.length - 1), i = Math.min(path.length - 2, Math.floor(f)), k = f - i;
      const a = path[i], b = path[i + 1];
      placeToken(pl, a.x + (b.x - a.x) * k, a.y + (b.y - a.y) * k);
      if (t < 1) requestAnimationFrame(frame);
      else { inner.classList.remove(mode); pl.el.classList.remove('nomove'); res(); }
    })(t0);
  });
}

let toastTimer;
function toast(msg){
  const t = $('#toast'); t.textContent = msg; t.classList.add('show');
  clearTimeout(toastTimer); toastTimer = setTimeout(() => t.classList.remove('show'), 1500);
}
function log(msg){
  const l = $('#log'); const d = document.createElement('div'); d.textContent = msg; l.prepend(d);
}

function initials(name){ return (name.trim()[0] || '?').toUpperCase(); }

function buildTokens(){
  const layer = $('#tokens'); layer.innerHTML = '';
  players.forEach(pl => {
    const el = document.createElement('div');
    el.className = 'token' + (pl.photo ? '' : ' nophoto');
    el.style.setProperty('--pc', pl.color);
    const inner = document.createElement('div'); inner.className = 'in';
    if (pl.photo) inner.style.backgroundImage = `url(${pl.photo})`; else inner.textContent = initials(pl.name);
    el.appendChild(inner); layer.appendChild(el); pl.el = el;
  });
}

function renderPanel(){
  const box = $('#players'); box.innerHTML = '';
  players.forEach((pl, i) => {
    const row = document.createElement('div');
    row.className = 'prow' + (i === turn && !over ? ' active' : ''); row.style.setProperty('--pc', pl.color);
    const av = document.createElement('div'); av.className = 'av';
    if (pl.photo) av.style.backgroundImage = `url(${pl.photo})`; else av.textContent = initials(pl.name);
    const meta = document.createElement('div'); meta.className = 'meta';
    meta.innerHTML = `<div class="nm"></div><div class="tl">Tile ${pl.pos} / 100</div><div class="bar"><i style="width:${pl.pos}%"></i></div>`;
    meta.querySelector('.nm').textContent = pl.name;
    row.append(av, meta); box.appendChild(row);
  });
  const cur = players[turn];
  $('#turnLabel').innerHTML = over ? 'Game over!' : `Turn: <b style="--pc:${cur.color};color:${cur.color}"></b>`;
  if (!over) $('#turnLabel b').textContent = cur.name;
  players.forEach((pl, i) => pl.el.classList.toggle('active', i === turn && !over));
}

/* =====================================================
   TURN LOGIC
===================================================== */
function stepPath(pos, roll){
  const arr = []; let p = pos, dir = 1;
  for (let i = 0; i < roll; i++){ if (p === 100) dir = -1; p += dir; arr.push(p); }
  return arr;
}

async function takeTurn(){
  if (busy || over) return;
  busy = true; $('#rollBtn').disabled = true;
  const pl = players[turn];
  const v = 1 + Math.floor(Math.random() * 6);
  $('#rolled').innerHTML = '&nbsp;';
  await rollDiceAnim(v);
  $('#rolled').textContent = `${pl.name} rolled a ${v}!`;
  log(`${pl.name} rolled ${v}`);
  await sleep(300);

  // walk tile by tile
  const path = stepPath(pl.pos, v);
  if (pl.pos + v > 100) toast('Sobra! Bounce back ↩');
  for (const t of path){
    pl.pos = t; setTokenToTile(pl, t); hop(pl); renderPanel();
    await sleep(260);
  }
  await sleep(150);

  // snake / ladder / escalator
  if (SNAKES[pl.pos]){
    const head = pl.pos, tail = SNAKES[head];
    toast('🐍 Nakagat ng ahas!'); log(`${pl.name} got bitten! ${head} → ${tail}`);
    await sleep(600);
    const pts = SNAKE_DATA[head].pts;
    await animateAlong(pl, pts, Math.max(1100, pts.length * 22), 'slide');
    pl.pos = tail; setTokenToTile(pl, tail, true); hop(pl);
  } else if (LADDERS[pl.pos]){
    const base = pl.pos, top = LADDERS[base];
    const A = center(base), B = center(top);
    const rows = B.row - A.row, esc = rows >= ESCALATOR_MIN_ROWS;
    toast(esc ? '🛗 Escalator! Taas na!' : '🪜 Umakyat!'); log(`${pl.name} ${esc ? 'rode the escalator' : 'climbed the ladder'}: ${base} → ${top}`);
    await sleep(600);
    const len = Math.hypot(B.x - A.x, B.y - A.y);
    await animateAlong(pl, [A, B], esc ? Math.max(1400, len * 3.6) : Math.max(1200, len * 4.4), esc ? 'ride' : 'climb');
    pl.pos = top; setTokenToTile(pl, top, true); hop(pl);
  }
  renderPanel();
  await sleep(250);

  if (pl.pos === 100){ over = true; renderPanel(); showWinner(pl); busy = false; return; }

  if (v === 6){ toast('🎉 6! Roll ulit!'); log(`${pl.name} gets another turn`); }
  else turn = (turn + 1) % players.length;
  renderPanel();
  busy = false; $('#rollBtn').disabled = false;
}

/* =====================================================
   WINNER / FLOW
===================================================== */
function showWinner(pl){
  const w = $('#winner'), card = $('#wcard');
  card.innerHTML = `<div class="big" style="--pc:${pl.color}">${pl.photo ? '' : initials(pl.name)}</div>
    <h2>🏆 <span id="wn"></span> wins!</h2><p>Nakarating sa tile 100!</p>
    <div class="btnrow"><button class="smallbtn" id="againBtn">↻ Play again</button><button class="smallbtn" id="newBtn">⚙ New setup</button></div>`;
  const big = card.querySelector('.big'); big.style.background = pl.photo ? `url(${pl.photo}) center/cover` : pl.color;
  $('#wn').textContent = pl.name;
  w.querySelectorAll('.conf').forEach(c => c.remove());
  const cols = ['#f43f5e','#fbbf24','#22c55e','#3b82f6','#a855f7','#fff'];
  for (let i = 0; i < 90; i++){
    const c = document.createElement('div'); c.className = 'conf';
    c.style.left = Math.random() * 100 + '%'; c.style.background = cols[i % cols.length];
    c.style.animationDuration = (2 + Math.random() * 3) + 's'; c.style.animationDelay = (-Math.random() * 4) + 's';
    w.appendChild(c);
  }
  w.classList.remove('hidden');
  $('#againBtn').onclick = () => { w.classList.add('hidden'); startGame(); };
  $('#newBtn').onclick = () => { w.classList.add('hidden'); goSetup(); };
}

function startGame(){
  players = cfg.players.slice(0, cfg.count).map((p, i) => ({
    idx: i, name: (p.name.trim() || 'Player ' + (i + 1)), photo: p.photo, color: COLORS[i], pos: 1, el: null
  }));
  turn = 0; busy = false; over = false;
  $('#setup').classList.add('hidden'); $('#game').classList.remove('hidden');
  $('#log').innerHTML = ''; $('#rolled').innerHTML = '&nbsp;'; $('#rollBtn').disabled = false;
  buildBoard(); buildTokens(); buildDice();
  players.forEach(pl => setTokenToTile(pl, 1, true));
  renderPanel(); log('Game started! Roll the dice 🎲');
}
function goSetup(){ $('#game').classList.add('hidden'); $('#setup').classList.remove('hidden'); renderSetup(); }

$('#startBtn').addEventListener('click', startGame);
$('#rollBtn').addEventListener('click', takeTurn);
$('#scene').addEventListener('click', () => { if (!$('#rollBtn').disabled) takeTurn(); });
$('#restartBtn').addEventListener('click', () => { if (confirm('Restart the game?')) startGame(); });
$('#setupBtn').addEventListener('click', goSetup);

renderSetup();
</script>
</body>
</html>
