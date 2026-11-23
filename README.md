<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>Snakes, Ladders & Escalators</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@500;600;700&family=Plus+Jakarta+Sans:wght@500;600;700&display=swap" rel="stylesheet">
  <style>
    :root {
      --dice-size: 68px;
      --dice-half: 34px;
    }

    @media (min-width: 640px) {
      :root {
        --dice-size: 76px;
        --dice-half: 38px;
      }
    }

    body {
      font-family: 'Plus Jakarta Sans', sans-serif;
      background: radial-gradient(circle at top left, #fef3c7 0%, #e0f2fe 45%, #ede9fe 100%);
      min-height: 100vh;
      overflow-x: hidden;
      user-select: none;
      -webkit-tap-highlight-color: transparent;
    }

    h1, h2, h3, .font-display {
      font-family: 'Fredoka', cursive, sans-serif;
    }

    /* 3D Realistic White Dice Styling */
    .dice-scene {
      width: var(--dice-size);
      height: var(--dice-size);
      perspective: 600px;
      margin: 0 auto;
    }

    .dice-cube {
      width: 100%;
      height: 100%;
      position: relative;
      transform-style: preserve-3d;
      transition: transform 0.95s cubic-bezier(0.22, 1.15, 0.36, 1);
    }

    .dice-cube.rolling {
      animation: diceTumble 0.75s linear infinite;
    }

    @keyframes diceTumble {
      0%   { transform: rotateX(0deg) rotateY(0deg) rotateZ(0deg); }
      25%  { transform: rotateX(190deg) rotateY(95deg) rotateZ(45deg); }
      50%  { transform: rotateX(360deg) rotateY(220deg) rotateZ(135deg); }
      75%  { transform: rotateX(540deg) rotateY(310deg) rotateZ(240deg); }
      100% { transform: rotateX(720deg) rotateY(360deg) rotateZ(360deg); }
    }

    .dice-face {
      position: absolute;
      width: 100%;
      height: 100%;
      background: linear-gradient(145deg, #ffffff 0%, #f1f5f9 75%, #e2e8f0 100%);
      border-radius: 14px;
      border: 2px solid #cbd5e1;
      box-shadow:
        inset 0 3px 6px rgba(255, 255, 255, 0.95),
        inset 0 -4px 8px rgba(15, 23, 42, 0.14),
        0 4px 10px rgba(15, 23, 42, 0.15);
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      grid-template-rows: repeat(3, 1fr);
      padding: 10px;
      gap: 3px;
      backface-visibility: hidden;
    }

    .pip {
      width: 100%;
      height: 100%;
      border-radius: 9999px;
      background: radial-gradient(circle at 35% 35%, #334155, #0f172a 75%);
      box-shadow: inset 0 2px 3px rgba(0, 0, 0, 0.7), 0 1px 1px rgba(255, 255, 255, 0.8);
      transform: scale(0.84);
    }

    .pip.red {
      background: radial-gradient(circle at 35% 35%, #ef4444, #b91c1c 75%);
      transform: scale(1.02);
    }

    /* Face transforms */
    .face-1 { transform: rotateY(0deg) translateZ(var(--dice-half)); }
    .face-2 { transform: rotateY(90deg) translateZ(var(--dice-half)); }
    .face-3 { transform: rotateX(90deg) translateZ(var(--dice-half)); }
    .face-4 { transform: rotateX(-90deg) translateZ(var(--dice-half)); }
    .face-5 { transform: rotateY(-90deg) translateZ(var(--dice-half)); }
    .face-6 { transform: rotateY(180deg) translateZ(var(--dice-half)); }

    /* Board Tile Aesthetics */
    .board-wrapper {
      aspect-ratio: 1 / 1;
      width: 100%;
      max-width: 650px;
      position: relative;
      border-radius: 20px;
      box-shadow:
        0 20px 40px -12px rgba(15, 23, 42, 0.25),
        0 0 0 6px #ffffff,
        0 0 0 10px #fbbf24;
      background: #ffffff;
      overflow: hidden;
    }

    .board-grid {
      display: grid;
      grid-template-columns: repeat(10, 1fr);
      grid-template-rows: repeat(10, 1fr);
      width: 100%;
      height: 100%;
    }

    .tile {
      position: relative;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      padding: 2px 4px;
      border: 0.5px solid rgba(255, 255, 255, 0.7);
      font-weight: 700;
      transition: filter 0.2s ease;
    }

    .tile-num {
      font-family: 'Fredoka', sans-serif;
      font-size: clamp(0.62rem, 1.8vw, 0.92rem);
      line-height: 1;
      color: rgba(15, 23, 42, 0.78);
      text-shadow: 0 1px 0 rgba(255, 255, 255, 0.7);
      z-index: 2;
    }

    /* Escalator Step Animation */
    @keyframes escalatorDash {
      to {
        stroke-dashoffset: -24;
      }
    }

    .escalator-steps {
      animation: escalatorDash 1.1s linear infinite;
    }

    /* Player Avatar Token */
    .player-token {
      position: absolute;
      width: 7.4%;
      height: 7.4%;
      border-radius: 9999px;
      transform: translate(-50%, -50%);
      transition: left 0.24s cubic-bezier(0.34, 1.56, 0.64, 1), top 0.24s cubic-bezier(0.34, 1.56, 0.64, 1);
      z-index: 30;
      box-shadow: 0 4px 10px rgba(0, 0, 0, 0.35), 0 0 0 2.5px #ffffff;
      display: flex;
      align-items: center;
      justify-content: center;
      overflow: hidden;
      pointer-events: none;
    }

    .player-token.sliding {
      transition: left 0.75s cubic-bezier(0.25, 1, 0.5, 1), top 0.75s cubic-bezier(0.25, 1, 0.5, 1);
      z-index: 40;
    }

    .player-token.active-turn {
      z-index: 35;
      animation: tokenPulse 1.4s ease-in-out infinite;
    }

    @keyframes tokenPulse {
      0%, 100% { box-shadow: 0 4px 10px rgba(0, 0, 0, 0.35), 0 0 0 3px #ffffff, 0 0 0 6px rgba(250, 204, 21, 0.85); }
      50%      { box-shadow: 0 6px 14px rgba(0, 0, 0, 0.45), 0 0 0 3px #ffffff, 0 0 0 10px rgba(250, 204, 21, 0.25); }
    }

    .player-token img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }

    /* Confetti Canvas */
    #confettiCanvas {
      position: fixed;
      inset: 0;
      pointer-events: none;
      z-index: 60;
    }
  </style>
</head>
<body class="text-slate-800 antialiased">

  <!-- Confetti Overlay for Victory -->
  <canvas id="confettiCanvas"></canvas>

  <!-- ==================== SETUP SCREEN ==================== -->
  <div id="setupScreen" class="min-h-screen flex items-center justify-center p-4 sm:p-6">
    <div class="w-full max-w-2xl bg-white/95 backdrop-blur-md rounded-3xl shadow-2xl border-4 border-amber-300 p-6 sm:p-8">
      <div class="text-center mb-6">
        <div class="inline-flex items-center gap-2 px-4 py-1.5 rounded-full bg-amber-100 text-amber-800 font-semibold text-xs sm:text-sm mb-2">
          <span>🐍 Cartoon Snakes</span>
          <span>•</span>
          <span>🪜 Wooden Ladders</span>
          <span>•</span>
          <span>🛗 Mega Escalators</span>
        </div>
        <h1 class="text-3xl sm:text-4xl font-bold text-slate-900 tracking-tight">
          Snakes &amp; Ladders Deluxe
        </h1>
        <p class="text-slate-500 text-sm sm:text-base mt-1">
          Customize 2 to 4 players and upload your own photos from your gallery!
        </p>
      </div>

      <!-- Player Count Selector -->
      <div class="mb-6">
        <label class="block text-sm font-bold text-slate-700 mb-2 text-center">How many players?</label>
        <div class="grid grid-cols-3 gap-3 max-w-sm mx-auto" id="playerCountButtons">
          <button type="button" data-count="2" class="count-btn py-2.5 rounded-2xl font-display font-bold text-lg border-2 border-indigo-600 bg-indigo-600 text-white shadow-md transition">
            2 Players
          </button>
          <button type="button" data-count="3" class="count-btn py-2.5 rounded-2xl font-display font-bold text-lg border-2 border-slate-200 bg-slate-50 text-slate-700 hover:border-indigo-300 transition">
            3 Players
          </button>
          <button type="button" data-count="4" class="count-btn py-2.5 rounded-2xl font-display font-bold text-lg border-2 border-slate-200 bg-slate-50 text-slate-700 hover:border-indigo-300 transition">
            4 Players
          </button>
        </div>
      </div>

      <!-- Player Customization Cards -->
      <div id="playerSetupList" class="grid grid-cols-1 sm:grid-cols-2 gap-4 mb-6">
        <!-- Populated dynamically by JS -->
      </div>

      <!-- Game Rule Toggle -->
      <div class="flex items-center justify-between bg-slate-50 rounded-2xl p-3.5 mb-6 border border-slate-200">
        <div>
          <p class="text-sm font-bold text-slate-800">Exact Roll to Finish (Tile 100)</p>
          <p class="text-xs text-slate-500">Bounce back extra steps if you roll over 100</p>
        </div>
        <label class="relative inline-flex items-center cursor-pointer">
          <input type="checkbox" id="exactRollToggle" class="sr-only peer" checked>
          <div class="w-11 h-6 bg-slate-300 peer-focus:outline-none rounded-full peer peer-checked:after:translate-x-full peer-checked:after:border-white after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:border-slate-300 after:border after:rounded-full after:h-5 after:w-5 after:transition-all peer-checked:bg-emerald-500"></div>
        </label>
      </div>

      <button id="startGameBtn" type="button" class="w-full py-4 rounded-2xl bg-gradient-to-r from-emerald-500 via-teal-500 to-cyan-600 hover:from-emerald-600 hover:to-cyan-700 text-white font-display font-bold text-xl shadow-lg shadow-emerald-500/30 active:scale-[0.99] transition cursor-pointer">
        🎮 Start Game!
      </button>
    </div>
  </div>

  <!-- ==================== GAME SCREEN ==================== -->
  <div id="gameScreen" class="hidden min-h-screen flex flex-col lg:flex-row items-center justify-center gap-4 lg:gap-8 p-3 sm:p-6 max-w-6xl mx-auto">

    <!-- Left / Top: Board Area -->
    <div class="w-full max-w-[650px] flex flex-col items-center">
      <!-- Top Mobile Bar -->
      <div class="w-full flex items-center justify-between mb-2.5 px-1">
        <button id="backToSetupBtn" type="button" class="px-3 py-1.5 rounded-xl bg-white/90 hover:bg-white text-slate-700 font-semibold text-xs sm:text-sm shadow border border-slate-200 transition">
          ← Change Players
        </button>
        <div class="flex items-center gap-2">
          <span class="hidden sm:inline-flex items-center gap-1.5 text-xs font-semibold bg-white/80 px-3 py-1.5 rounded-xl border border-slate-200 text-slate-600">
            <span>🪜 Ladder</span>
            <span>•</span>
            <span>🛗 Escalator</span>
            <span>•</span>
            <span>🐍 Snake</span>
          </span>
          <button id="muteBtn" type="button" class="p-2 rounded-xl bg-white/90 hover:bg-white text-slate-700 shadow border border-slate-200 transition" title="Toggle Sound">
            🔊
          </button>
        </div>
      </div>

      <!-- The 10x10 Board -->
      <div class="board-wrapper" id="boardWrapper">
        <!-- 100 Colorful Grid Tiles -->
        <div id="boardGrid" class="board-grid"></div>

        <!-- Custom SVG Layer for Snakes, Wooden Ladders & Escalators -->
        <svg id="boardOverlay" viewBox="0 0 1000 1000" class="absolute inset-0 w-full h-full pointer-events-none z-20"></svg>

        <!-- Player Tokens Layer -->
        <div id="tokensLayer" class="absolute inset-0 pointer-events-none z-30"></div>
      </div>
    </div>

    <!-- Right / Bottom: Controls, Dice & Scoreboard -->
    <div class="w-full max-w-[650px] lg:w-96 flex flex-col gap-3 sm:gap-4">

      <!-- Status Banner -->
      <div id="statusBanner" class="bg-white/95 backdrop-blur rounded-2xl p-3.5 sm:p-4 shadow-lg border-2 border-indigo-200 text-center">
        <p id="statusTitle" class="font-display font-bold text-lg sm:text-xl text-slate-900">Player 1's Turn!</p>
        <p id="statusSubtitle" class="text-xs sm:text-sm text-slate-500 mt-0.5">Tap the dice or button below to roll</p>
      </div>

      <!-- 3D Dice & Roll Action Card -->
      <div class="bg-white/95 backdrop-blur rounded-3xl p-4 sm:p-6 shadow-xl border-2 border-amber-200 flex flex-row lg:flex-col items-center justify-around lg:justify-center gap-4">
        <div id="diceClickArea" class="cursor-pointer p-2 rounded-2xl hover:bg-slate-50 transition" title="Click to roll dice">
          <div class="dice-scene">
            <div id="diceCube" class="dice-cube">
              <!-- Face 1 -->
              <div class="dice-face face-1">
                <div></div><div></div><div></div>
                <div></div><div class="pip red"></div><div></div>
                <div></div><div></div><div></div>
              </div>
              <!-- Face 2 -->
              <div class="dice-face face-2">
                <div class="pip"></div><div></div><div></div>
                <div></div><div></div><div></div>
                <div></div><div></div><div class="pip"></div>
              </div>
              <!-- Face 3 -->
              <div class="dice-face face-3">
                <div class="pip"></div><div></div><div></div>
                <div></div><div class="pip"></div><div></div>
                <div></div><div></div><div class="pip"></div>
              </div>
              <!-- Face 4 -->
              <div class="dice-face face-4">
                <div class="pip"></div><div></div><div class="pip"></div>
                <div></div><div></div><div></div>
                <div class="pip"></div><div></div><div class="pip"></div>
              </div>
              <!-- Face 5 -->
              <div class="dice-face face-5">
                <div class="pip"></div><div></div><div class="pip"></div>
                <div></div><div class="pip"></div><div></div>
                <div class="pip"></div><div></div><div class="pip"></div>
              </div>
              <!-- Face 6 -->
              <div class="dice-face face-6">
                <div class="pip"></div><div></div><div class="pip"></div>
                <div class="pip"></div><div></div><div class="pip"></div>
                <div class="pip"></div><div></div><div class="pip"></div>
              </div>
            </div>
          </div>
        </div>

        <div class="flex flex-col items-center w-full max-w-[210px]">
          <button id="rollBtn" type="button" class="w-full py-3.5 px-6 rounded-2xl bg-gradient-to-r from-indigo-600 to-violet-600 hover:from-indigo-700 hover:to-violet-700 disabled:opacity-50 disabled:cursor-not-allowed text-white font-display font-bold text-lg sm:text-xl shadow-lg shadow-indigo-500/30 active:scale-95 transition cursor-pointer">
            🎲 Roll Dice
          </button>
          <span id="lastRollBadge" class="mt-2 text-xs font-semibold text-slate-400">
            Ready to roll
          </span>
        </div>
      </div>

      <!-- Player Roster / Live Standings -->
      <div class="bg-white/95 backdrop-blur rounded-3xl p-4 shadow-lg border border-slate-200">
        <h3 class="font-display font-bold text-sm uppercase tracking-wider text-slate-400 mb-2.5 px-1">
          Players &amp; Positions
        </h3>
        <div id="scoreboardList" class="grid grid-cols-2 lg:grid-cols-1 gap-2">
          <!-- Populated dynamically -->
        </div>
      </div>

    </div>
  </div>

  <!-- ==================== VICTORY MODAL ==================== -->
  <div id="victoryModal" class="hidden fixed inset-0 z-50 bg-slate-900/60 backdrop-blur-sm flex items-center justify-center p-4">
    <div class="bg-white rounded-3xl max-w-md w-full p-6 sm:p-8 text-center shadow-2xl border-4 border-amber-400 transform transition-all">
      <div class="text-5xl mb-3">🏆👑🎉</div>
      <div id="winnerAvatarBox" class="w-24 h-24 rounded-full mx-auto mb-4 border-4 border-amber-400 shadow-lg overflow-hidden flex items-center justify-center bg-amber-100"></div>
      <h2 id="winnerNameText" class="font-display font-bold text-3xl text-slate-900">Player 1 Wins!</h2>
      <p class="text-slate-500 text-sm sm:text-base mt-1 mb-6">
        Reached Tile 100 and conquered the snakes &amp; escalators!
      </p>
      <div class="flex flex-col sm:flex-row gap-3">
        <button id="playAgainBtn" type="button" class="flex-1 py-3.5 px-5 rounded-2xl bg-emerald-500 hover:bg-emerald-600 text-white font-display font-bold text-lg shadow-md transition cursor-pointer">
          🔄 Play Again
        </button>
        <button id="newPlayersBtn" type="button" class="flex-1 py-3.5 px-5 rounded-2xl bg-slate-100 hover:bg-slate-200 text-slate-800 font-display font-bold text-lg transition cursor-pointer">
          👥 Change Players
        </button>
      </div>
    </div>
  </div>

  <script>
    /**
     * =========================================================================
     * 1. SOUND ENGINE (Web Audio API - No external audio files needed)
     * =========================================================================
     */
    class SoundFX {
      constructor() {
        this.ctx = null;
        this.muted = false;
      }

      init() {
        if (!this.ctx) {
          const AudioContext = window.AudioContext || window.webkitAudioContext;
          if (AudioContext) this.ctx = new AudioContext();
        }
        if (this.ctx && this.ctx.state === 'suspended') {
          this.ctx.resume();
        }
      }

      playTone(freq, type, duration, delay = 0, gainVal = 0.15, slideTo = null) {
        if (this.muted) return;
        this.init();
        if (!this.ctx) return;

        const now = this.ctx.currentTime + delay;
        const osc = this.ctx.createOscillator();
        const gain = this.ctx.createGain();

        osc.type = type;
        osc.frequency.setValueAtTime(freq, now);
        if (slideTo) {
          osc.frequency.exponentialRampToValueAtTime(slideTo, now + duration);
        }

        gain.gain.setValueAtTime(gainVal, now);
        gain.gain.exponentialRampToValueAtTime(0.001, now + duration);

        osc.connect(gain);
        gain.connect(this.ctx.destination);

        osc.start(now);
        osc.stop(now + duration);
      }

      diceRoll() {
        for (let i = 0; i < 6; i++) {
          this.playTone(180 + Math.random() * 220, 'triangle', 0.06, i * 0.09, 0.12);
        }
      }

      stepHop(stepIndex) {
        const base = 360 + (stepIndex % 6) * 35;
        this.playTone(base, 'sine', 0.11, 0, 0.14, base * 1.25);
      }

      ladderClimb() {
        const notes = [330, 392, 440, 523, 659, 784];
        notes.forEach((n, idx) => {
          this.playTone(n, 'triangle', 0.14, idx * 0.09, 0.16);
        });
      }

      escalatorRide() {
        // Smooth futuristic hum + ascending chime
        this.playTone(220, 'sawtooth', 0.75, 0, 0.06, 580);
        const notes = [440, 554, 659, 880];
        notes.forEach((n, idx) => {
          this.playTone(n, 'sine', 0.18, idx * 0.16, 0.14);
        });
      }

      snakeBite() {
        // Cartoonish descending whistle + hiss
        this.playTone(620, 'sawtooth', 0.65, 0, 0.14, 130);
        this.playTone(480, 'sine', 0.65, 0.05, 0.12, 95);
      }

      victory() {
        const melody = [523.25, 659.25, 783.99, 1046.5, 783.99, 1046.5];
        melody.forEach((n, i) => {
          this.playTone(n, 'triangle', i === melody.length - 1 ? 0.5 : 0.16, i * 0.14, 0.2);
        });
      }
    }

    const sfx = new SoundFX();

    /**
     * =========================================================================
     * 2. BOARD CONFIGURATION (Snakes, Wooden Ladders & Long Escalators)
     * =========================================================================
     * Standard ladders are moderate climbs.
     * Long climbs (>= 35 tiles jump) automatically render as sleek ESCALATORS!
     */
    const JUMPS = {
      // Ladders & Escalators (start -> end where end > start)
      3:  { to: 22, type: 'ladder' },
      8:  { to: 26, type: 'ladder' },
      14: { to: 55, type: 'escalator' }, // +41 tiles Long Escalator!
      20: { to: 38, type: 'ladder' },
      28: { to: 84, type: 'escalator' }, // +56 tiles Mega Escalator!
      40: { to: 59, type: 'ladder' },
      51: { to: 67, type: 'ladder' },
      63: { to: 81, type: 'ladder' },
      71: { to: 91, type: 'ladder' },

      // Cartoon Snakes (head -> tail where tail < head)
      17: { to: 7,  type: 'snake', palette: { body: '#ef4444', stripe: '#fde047', belly: '#fecaca' } },
      34: { to: 12, type: 'snake', palette: { body: '#8b5cf6', stripe: '#67e8f9', belly: '#ddd6fe' } },
      47: { to: 25, type: 'snake', palette: { body: '#10b981', stripe: '#fef08a', belly: '#a7f3d0' } },
      62: { to: 19, type: 'snake', palette: { body: '#f97316', stripe: '#ffffff', belly: '#fed7aa' } },
      64: { to: 42, type: 'snake', palette: { body: '#ec4899', stripe: '#fde047', belly: '#fbcfe8' } },
      87: { to: 36, type: 'snake', palette: { body: '#06b6d4', stripe: '#f43f5e', belly: '#cffafe' } },
      95: { to: 56, type: 'snake', palette: { body: '#eab308', stripe: '#7c3aed', belly: '#fef9c3' } },
      98: { to: 78, type: 'snake', palette: { body: '#e11d48', stripe: '#38bdf8', belly: '#ffe4e6' } }
    };

    // Vibrant cheerful color palette for tiles
    const TILE_COLORS = [
      '#ffe4e6', // Rose 100
      '#fef3c7', // Amber 100
      '#dcfce7', // Emerald 100
      '#e0f2fe', // Sky 100
      '#f3e8ff', // Purple 100
      '#ffedd5', // Orange 100
      '#ccfbf1', // Teal 100
      '#fce7f3', // Pink 100
      '#fef9c3', // Yellow 100
      '#e0e7ff'  // Indigo 100
    ];

    /**
     * =========================================================================
     * 3. PLAYER STATE & SETUP UI
     * =========================================================================
     */
    const DEFAULT_COLORS = ['#ef4444', '#2563eb', '#10b981', '#f59e0b'];
    const DEFAULT_EMOJIS = ['🦁', '🚀', '🦄', '🐼'];

    let playerCount = 2;
    let playerConfigs = [
      { name: 'Player 1', color: DEFAULT_COLORS[0], photo: null, emoji: DEFAULT_EMOJIS[0] },
      { name: 'Player 2', color: DEFAULT_COLORS[1], photo: null, emoji: DEFAULT_EMOJIS[1] },
      { name: 'Player 3', color: DEFAULT_COLORS[2], photo: null, emoji: DEFAULT_EMOJIS[2] },
      { name: 'Player 4', color: DEFAULT_COLORS[3], photo: null, emoji: DEFAULT_EMOJIS[3] }
    ];

    let players = [];
    let currentPlayerIndex = 0;
    let isAnimating = false;
    let exactRollRequired = true;

    const setupScreen = document.getElementById('setupScreen');
    const gameScreen = document.getElementById('gameScreen');
    const playerSetupList = document.getElementById('playerSetupList');
    const countButtons = document.querySelectorAll('.count-btn');

    function renderSetupCards() {
      playerSetupList.innerHTML = '';
      for (let i = 0; i < playerCount; i++) {
        const cfg = playerConfigs[i];
        const card = document.createElement('div');
        card.className = 'bg-slate-50 rounded-2xl p-4 border-2 border-slate-200 flex items-center gap-3.5 shadow-sm';

        const avatarPreview = cfg.photo
          ? `<img src="${cfg.photo}" alt="${cfg.name}" class="w-full h-full object-cover" />`
          : `<span class="text-2xl">${cfg.emoji}</span>`;

        card.innerHTML = `
          <div class="relative shrink-0">
            <div class="w-16 h-16 rounded-full flex items-center justify-center overflow-hidden shadow-md border-4" style="background-color: ${cfg.color}; border-color: ${cfg.color};">
              ${avatarPreview}
            </div>
            <label class="absolute -bottom-1 -right-1 w-7 h-7 rounded-full bg-indigo-600 hover:bg-indigo-700 text-white flex items-center justify-center shadow cursor-pointer text-xs" title="Upload photo from gallery">
              📷
              <input type="file" accept="image/*" class="hidden photo-input" data-index="${i}" />
            </label>
          </div>
          <div class="flex-1 min-w-0">
            <label class="text-[11px] font-bold uppercase tracking-wider text-slate-400">Player ${i + 1}</label>
            <input type="text" maxlength="14" value="${cfg.name}" data-index="${i}"
              class="name-input w-full mt-0.5 px-3 py-1.5 rounded-xl bg-white border border-slate-300 font-bold text-slate-800 text-sm focus:outline-none focus:border-indigo-500"
              placeholder="Enter name" />
            <div class="flex items-center justify-between mt-2">
              <div class="flex items-center gap-1.5">
                ${DEFAULT_COLORS.map(c => `
                  <button type="button" data-index="${i}" data-color="${c}"
                    class="color-dot w-5 h-5 rounded-full border-2 ${cfg.color === c ? 'border-slate-900 scale-110' : 'border-white'} shadow-sm transition cursor-pointer"
                    style="background-color: ${c};"></button>
                `).join('')}
              </div>
              ${cfg.photo ? `<button type="button" data-index="${i}" class="clear-photo text-[11px] font-semibold text-rose-500 hover:underline cursor-pointer">Remove photo</button>` : `<span class="text-[11px] text-slate-400">Tap 📷 for photo</span>`}
            </div>
          </div>
        `;
        playerSetupList.appendChild(card);
      }

      // Attach listeners
      document.querySelectorAll('.name-input').forEach(inp => {
        inp.addEventListener('input', (e) => {
          const idx = Number(e.target.dataset.index);
          playerConfigs[idx].name = e.target.value.trim() || `Player ${idx + 1}`;
        });
      });

      document.querySelectorAll('.color-dot').forEach(btn => {
        btn.addEventListener('click', (e) => {
          const idx = Number(e.target.dataset.index);
          playerConfigs[idx].color = e.target.dataset.color;
          renderSetupCards();
        });
      });

      document.querySelectorAll('.photo-input').forEach(inp => {
        inp.addEventListener('change', (e) => {
          const idx = Number(e.target.dataset.index);
          const file = e.target.files && e.target.files[0];
          if (!file) return;
          const reader = new FileReader();
          reader.onload = (ev) => {
            // Resize image via canvas to keep memory ultra-light and crisp
            const img = new Image();
            img.onload = () => {
              const canvas = document.createElement('canvas');
              const size = 180;
              canvas.width = size;
              canvas.height = size;
              const ctx = canvas.getContext('2d');
              const minSide = Math.min(img.width, img.height);
              const sx = (img.width - minSide) / 2;
              const sy = (img.height - minSide) / 2;
              ctx.drawImage(img, sx, sy, minSide, minSide, 0, 0, size, size);
              playerConfigs[idx].photo = canvas.toDataURL('image/jpeg', 0.85);
              renderSetupCards();
            };
            img.src = ev.target.result;
          };
          reader.readAsDataURL(file);
        });
      });

      document.querySelectorAll('.clear-photo').forEach(btn => {
        btn.addEventListener('click', (e) => {
          const idx = Number(e.target.dataset.index);
          playerConfigs[idx].photo = null;
          renderSetupCards();
        });
      });
    }

    countButtons.forEach(btn => {
      btn.addEventListener('click', () => {
        playerCount = Number(btn.dataset.count);
        countButtons.forEach(b => {
          const active = Number(b.dataset.count) === playerCount;
          b.className = active
            ? 'count-btn py-2.5 rounded-2xl font-display font-bold text-lg border-2 border-indigo-600 bg-indigo-600 text-white shadow-md transition'
            : 'count-btn py-2.5 rounded-2xl font-display font-bold text-lg border-2 border-slate-200 bg-slate-50 text-slate-700 hover:border-indigo-300 transition';
        });
        renderSetupCards();
      });
    });

    renderSetupCards();

    /**
     * =========================================================================
     * 4. BOARD GEOMETRY & SVG RENDERING (Snakes, Wooden Ladders & Escalators)
     * =========================================================================
     */
    const boardGrid = document.getElementById('boardGrid');
    const boardOverlay = document.getElementById('boardOverlay');

    // Returns { row (0 top to 9 bottom), col (0 left to 9 right), cx (0-1000), cy (0-1000) }
    function getTileCoords(tileNum) {
      const clamped = Math.max(1, Math.min(100, tileNum));
      const zeroIdx = clamped - 1;
      const rowFromBottom = Math.floor(zeroIdx / 10);
      const row = 9 - rowFromBottom;
      const colInRow = zeroIdx % 10;
      const col = (rowFromBottom % 2 === 0) ? colInRow : (9 - colInRow);

      const cx = col * 100 + 50;
      const cy = row * 100 + 50;
      return { row, col, cx, cy };
    }

    function buildBoardTiles() {
      boardGrid.innerHTML = '';
      for (let r = 0; r < 10; r++) {
        const rowFromBottom = 9 - r;
        for (let c = 0; c < 10; c++) {
          const colInRow = (rowFromBottom % 2 === 0) ? c : (9 - c);
          const tileNum = rowFromBottom * 10 + colInRow + 1;

          const tile = document.createElement('div');
          tile.className = 'tile';
          const colorIdx = (tileNum * 3 + r) % TILE_COLORS.length;
          tile.style.backgroundColor = TILE_COLORS[colorIdx];

          let badge = '';
          if (tileNum === 1) {
            tile.style.backgroundColor = '#bbf7d0';
            badge = `<span class="text-[8px] sm:text-[10px] font-extrabold text-emerald-800 uppercase tracking-tighter">START</span>`;
          } else if (tileNum === 100) {
            tile.style.backgroundColor = '#fde047';
            badge = `<span class="text-[9px] sm:text-[11px] font-extrabold text-amber-900">👑 GOAL</span>`;
          } else if (JUMPS[tileNum]) {
            const j = JUMPS[tileNum];
            if (j.type === 'escalator') {
              badge = `<span class="text-[8px] sm:text-[10px] font-bold text-cyan-800">🛗→${j.to}</span>`;
            } else if (j.type === 'ladder') {
              badge = `<span class="text-[8px] sm:text-[10px] font-bold text-amber-800">🪜→${j.to}</span>`;
            } else {
              badge = `<span class="text-[8px] sm:text-[10px] font-bold text-rose-700">🐍→${j.to}</span>`;
            }
          }

          tile.innerHTML = `
            <span class="tile-num">${tileNum}</span>
            <div class="self-end leading-none z-10">${badge}</div>
          `;
          boardGrid.appendChild(tile);
        }
      }
    }

    function renderBoardOverlay() {
      let svgHTML = `
        <defs>
          <filter id="dropShadow" x="-20%" y="-20%" width="140%" height="140%">
            <feDropShadow dx="0" dy="5" stdDeviation="4" flood-color="#0f172a" flood-opacity="0.28"/>
          </filter>
          <linearGradient id="woodRail" x1="0%" y1="0%" x2="100%" y2="0%">
            <stop offset="0%" stop-color="#92400e"/>
            <stop offset="50%" stop-color="#d97706"/>
            <stop offset="100%" stop-color="#78350f"/>
          </linearGradient>
          <linearGradient id="metalRail" x1="0%" y1="0%" x2="100%" y2="0%">
            <stop offset="0%" stop-color="#334155"/>
            <stop offset="50%" stop-color="#94a3b8"/>
            <stop offset="100%" stop-color="#1e293b"/>
          </linearGradient>
          <linearGradient id="glassSide" x1="0%" y1="0%" x2="0%" y2="100%">
            <stop offset="0%" stop-color="#38bdf8" stop-opacity="0.45"/>
            <stop offset="100%" stop-color="#0284c7" stop-opacity="0.25"/>
          </linearGradient>
        </defs>
      `;

      // 1. Draw Ladders & Escalators first (underneath snakes)
      Object.entries(JUMPS).forEach(([startStr, jump]) => {
        const start = Number(startStr);
        if (jump.type === 'ladder') {
          svgHTML += drawWoodenLadder(start, jump.to);
        } else if (jump.type === 'escalator') {
          svgHTML += drawEscalator(start, jump.to);
        }
      });

      // 2. Draw Catchy Cartoon Snakes on top
      Object.entries(JUMPS).forEach(([startStr, jump]) => {
        const start = Number(startStr);
        if (jump.type === 'snake') {
          svgHTML += drawCartoonSnake(start, jump.to, jump.palette);
        }
      });

      boardOverlay.innerHTML = svgHTML;
    }

    function drawWoodenLadder(fromTile, toTile) {
      const p1 = getTileCoords(fromTile);
      const p2 = getTileCoords(toTile);
      const dx = p2.cx - p1.cx;
      const dy = p2.cy - p1.cy;
      const len = Math.hypot(dx, dy);
      const nx = (-dy / len) * 20; // half-width of ladder = 20
      const ny = (dx / len) * 20;

      const r1x1 = p1.cx + nx, r1y1 = p1.cy + ny;
      const r1x2 = p2.cx + nx, r1y2 = p2.cy + ny;
      const r2x1 = p1.cx - nx, r2y1 = p1.cy - ny;
      const r2x2 = p2.cx - nx, r2y2 = p2.cy - ny;

      const steps = Math.max(4, Math.floor(len / 34));
      let rungs = '';
      for (let i = 1; i < steps; i++) {
        const t = i / steps;
        const lx = r1x1 + (r1x2 - r1x1) * t;
        const ly = r1y1 + (r1y2 - r1y1) * t;
        const rx = r2x1 + (r2x2 - r2x1) * t;
        const ry = r2y1 + (r2y2 - r2y1) * t;
        rungs += `
          <line x1="${lx}" y1="${ly}" x2="${rx}" y2="${ry}" stroke="#78350f" stroke-width="9" stroke-linecap="round"/>
          <line x1="${lx}" y1="${ly}" x2="${rx}" y2="${ry}" stroke="#fbbf24" stroke-width="4" stroke-linecap="round"/>
        `;
      }

      return `
        <g filter="url(#dropShadow)">
          ${rungs}
          <line x1="${r1x1}" y1="${r1y1}" x2="${r1x2}" y2="${r1y2}" stroke="url(#woodRail)" stroke-width="11" stroke-linecap="round"/>
          <line x1="${r2x1}" y1="${r2y1}" x2="${r2x2}" y2="${r2y2}" stroke="url(#woodRail)" stroke-width="11" stroke-linecap="round"/>
        </g>
      `;
    }

    function drawEscalator(fromTile, toTile) {
      const p1 = getTileCoords(fromTile);
      const p2 = getTileCoords(toTile);
      const dx = p2.cx - p1.cx;
      const dy = p2.cy - p1.cy;
      const len = Math.hypot(dx, dy);
      const nx = (-dy / len) * 26;
      const ny = (dx / len) * 26;

      const r1x1 = p1.cx + nx, r1y1 = p1.cy + ny;
      const r1x2 = p2.cx + nx, r1y2 = p2.cy + ny;
      const r2x1 = p1.cx - nx, r2y1 = p1.cy - ny;
      const r2x2 = p2.cx - nx, r2y2 = p2.cy - ny;

      return `
        <g filter="url(#dropShadow)">
          <!-- Escalator Metallic Track Bed -->
          <line x1="${p1.cx}" y1="${p1.cy}" x2="${p2.cx}" y2="${p2.cy}" stroke="#1e293b" stroke-width="46" stroke-linecap="round"/>
          <line x1="${p1.cx}" y1="${p1.cy}" x2="${p2.cx}" y2="${p2.cy}" stroke="#475569" stroke-width="38" stroke-linecap="round"/>
          <!-- Animated Moving Steps -->
          <line x1="${p1.cx}" y1="${p1.cy}" x2="${p2.cx}" y2="${p2.cy}"
                stroke="#e2e8f0" stroke-width="30" stroke-dasharray="8 16" class="escalator-steps"/>
          <!-- Yellow Safety Side Strips -->
          <line x1="${r1x1}" y1="${r1y1}" x2="${r1x2}" y2="${r1y2}" stroke="#facc15" stroke-width="12" stroke-linecap="round"/>
          <line x1="${r2x1}" y1="${r2y1}" x2="${r2x2}" y2="${r2y2}" stroke="#facc15" stroke-width="12" stroke-linecap="round"/>
          <!-- Black Rubber Handrails -->
          <line x1="${r1x1}" y1="${r1y1}" x2="${r1x2}" y2="${r1y2}" stroke="#0f172a" stroke-width="6" stroke-linecap="round"/>
          <line x1="${r2x1}" y1="${r2y1}" x2="${r2x2}" y2="${r2y2}" stroke="#0f172a" stroke-width="6" stroke-linecap="round"/>
          <!-- Entry & Exit Landing Plates -->
          <circle cx="${p1.cx}" cy="${p1.cy}" r="22" fill="#0ea5e9" stroke="#ffffff" stroke-width="3"/>
          <text x="${p1.cx}" y="${p1.cy + 5}" text-anchor="middle" fill="#ffffff" font-size="16" font-weight="bold">UP</text>
          <circle cx="${p2.cx}" cy="${p2.cy}" r="18" fill="#10b981" stroke="#ffffff" stroke-width="3"/>
        </g>
      `;
    }

    function drawCartoonSnake(headTile, tailTile, palette) {
      const head = getTileCoords(headTile);
      const tail = getTileCoords(tailTile);

      const dx = tail.cx - head.cx;
      const dy = tail.cy - head.cy;
      const len = Math.hypot(dx, dy);
      const perpX = -dy / len;
      const perpY = dx / len;

      // Create a smooth S-curve using cubic Bezier control points
      const waveAmp = Math.min(95, Math.max(45, len * 0.24));
      const cp1x = head.cx + dx * 0.32 + perpX * waveAmp;
      const cp1y = head.cy + dy * 0.32 + perpY * waveAmp;
      const cp2x = head.cx + dx * 0.68 - perpX * waveAmp;
      const cp2y = head.cy + dy * 0.68 - perpY * waveAmp;

      const pathD = `M ${head.cx} ${head.cy} C ${cp1x} ${cp1y}, ${cp2x} ${cp2y}, ${tail.cx} ${tail.cy}`;

      // Calculate head angle pointing away from first curve segment
      const headAngle = Math.atan2(head.cy - cp1y, head.cx - cp1x) * (180 / Math.PI);

      return `
        <g filter="url(#dropShadow)">
          <!-- Dark Outline for Cartoon Pop -->
          <path d="${pathD}" fill="none" stroke="#0f172a" stroke-width="30" stroke-linecap="round"/>
          <!-- Main Snake Body -->
          <path d="${pathD}" fill="none" stroke="${palette.body}" stroke-width="23" stroke-linecap="round"/>
          <!-- Playful Stripes / Spots along spine -->
          <path d="${pathD}" fill="none" stroke="${palette.stripe}" stroke-width="14" stroke-dasharray="10 22" stroke-linecap="round"/>
          <!-- Highlight shine -->
          <path d="${pathD}" fill="none" stroke="#ffffff" stroke-opacity="0.35" stroke-width="5" stroke-linecap="round"/>

          <!-- Cartoon Snake Head -->
          <g transform="translate(${head.cx}, ${head.cy}) rotate(${headAngle})">
            <!-- Forked Red Tongue -->
            <path d="M 18 0 L 34 -6 L 29 0 L 34 6 Z" fill="#ef4444" stroke="#7f1d1d" stroke-width="1.5"/>
            <!-- Head Shape -->
            <ellipse cx="4" cy="0" rx="22" ry="18" fill="${palette.body}" stroke="#0f172a" stroke-width="3.5"/>
            <!-- Cheeks -->
            <circle cx="8" cy="-11" r="4" fill="#fda4af" opacity="0.8"/>
            <circle cx="8" cy="11" r="4" fill="#fda4af" opacity="0.8"/>
            <!-- Big Googly Cartoon Eyes -->
            <circle cx="8" cy="-8" r="6.5" fill="#ffffff" stroke="#0f172a" stroke-width="2"/>
            <circle cx="10" cy="-8" r="3" fill="#0f172a"/>
            <circle cx="11" cy="-9" r="1.1" fill="#ffffff"/>

            <circle cx="8" cy="8" r="6.5" fill="#ffffff" stroke="#0f172a" stroke-width="2"/>
            <circle cx="10" cy="8" r="3" fill="#0f172a"/>
            <circle cx="11" cy="7" r="1.1" fill="#ffffff"/>
          </g>
        </g>
      `;
    }

    /**
     * =========================================================================
     * 5. GAMEPLAY, TOKENS & STEP-BY-STEP ANIMATION
     * =========================================================================
     */
    const tokensLayer = document.getElementById('tokensLayer');
    const scoreboardList = document.getElementById('scoreboardList');
    const statusTitle = document.getElementById('statusTitle');
    const statusSubtitle = document.getElementById('statusSubtitle');
    const rollBtn = document.getElementById('rollBtn');
    const diceCube = document.getElementById('diceCube');
    const lastRollBadge = document.getElementById('lastRollBadge');

    // Offsets so multiple tokens on the exact same tile don't cover each other
    const TOKEN_OFFSETS = [
      { dx: -16, dy: -16 },
      { dx:  16, dy: -16 },
      { dx: -16, dy:  16 },
      { dx:  16, dy:  16 }
    ];

    function initGame() {
      exactRollRequired = document.getElementById('exactRollToggle').checked;
      players = [];
      for (let i = 0; i < playerCount; i++) {
        players.push({
          id: i,
          name: playerConfigs[i].name || `Player ${i + 1}`,
          color: playerConfigs[i].color,
          photo: playerConfigs[i].photo,
          emoji: playerConfigs[i].emoji,
          pos: 1
        });
      }

      currentPlayerIndex = 0;
      isAnimating = false;
      rollBtn.disabled = false;

      buildBoardTiles();
      renderBoardOverlay();
      renderTokens();
      renderScoreboard();
      updateTurnUI();
      setDiceFace(1);
    }

    function renderTokens() {
      tokensLayer.innerHTML = '';
      players.forEach((p, idx) => {
        const el = document.createElement('div');
        el.id = `token-${idx}`;
        el.className = `player-token ${idx === currentPlayerIndex ? 'active-turn' : ''}`;
        el.style.backgroundColor = p.color;
        el.style.borderColor = '#ffffff';

        if (p.photo) {
          el.innerHTML = `<img src="${p.photo}" alt="${p.name}" />`;
        } else {
          el.innerHTML = `<span class="text-sm sm:text-base">${p.emoji}</span>`;
        }

        tokensLayer.appendChild(el);
        positionTokenElement(idx, p.pos, false);
      });
    }

    function positionTokenElement(playerIdx, tileNum, isSliding = false) {
      const el = document.getElementById(`token-${playerIdx}`);
      if (!el) return;

      if (isSliding) {
        el.classList.add('sliding');
      } else {
        el.classList.remove('sliding');
      }

      const coords = getTileCoords(tileNum);
      // Check if others share this tile to apply neat quadrant offset
      const sameTilePlayers = players.filter(pl => pl.pos === tileNum);
      let offsetX = 0, offsetY = 0;
      if (sameTilePlayers.length > 1) {
        const subIdx = playerIdx % TOKEN_OFFSETS.length;
        offsetX = TOKEN_OFFSETS[subIdx].dx;
        offsetY = TOKEN_OFFSETS[subIdx].dy;
      }

      const leftPct = ((coords.cx + offsetX) / 1000) * 100;
      const topPct = ((coords.cy + offsetY) / 1000) * 100;
      el.style.left = `${leftPct}%`;
      el.style.top = `${topPct}%`;
    }

    function renderScoreboard() {
      scoreboardList.innerHTML = '';
      players.forEach((p, idx) => {
        const isTurn = idx === currentPlayerIndex;
        const avatarHtml = p.photo
          ? `<img src="${p.photo}" alt="${p.name}" class="w-full h-full object-cover" />`
          : `<span class="text-base">${p.emoji}</span>`;

        const row = document.createElement('div');
        row.className = `flex items-center justify-between p-2.5 rounded-2xl border-2 transition ${
          isTurn
            ? 'bg-indigo-50/90 border-indigo-500 shadow-sm'
            : 'bg-slate-50 border-slate-100'
        }`;

        row.innerHTML = `
          <div class="flex items-center gap-2.5 min-w-0">
            <div class="w-9 h-9 rounded-full shrink-0 overflow-hidden flex items-center justify-center border-2 border-white shadow" style="background-color: ${p.color};">
              ${avatarHtml}
            </div>
            <div class="truncate">
              <p class="font-bold text-sm text-slate-800 truncate">${p.name}</p>
              <p class="text-[11px] font-semibold ${isTurn ? 'text-indigo-600' : 'text-slate-400'}">
                ${isTurn ? '● Active Turn' : 'Waiting'}
              </p>
            </div>
          </div>
          <div class="px-2.5 py-1 rounded-xl bg-white border border-slate-200 font-display font-bold text-sm text-slate-800 shadow-xs">
            Tile ${p.pos}
          </div>
        `;
        scoreboardList.appendChild(row);
      });
    }

    function updateTurnUI() {
      const p = players[currentPlayerIndex];
      statusTitle.textContent = `${p.name}'s Turn!`;
      statusTitle.style.color = p.color;
      statusSubtitle.textContent = `Currently on Tile ${p.pos} — Roll the dice!`;

      document.querySelectorAll('.player-token').forEach((tok, idx) => {
        tok.classList.toggle('active-turn', idx === currentPlayerIndex);
      });
      renderScoreboard();
    }

    // Orient 3D CSS Cube so the rolled face faces front
    function setDiceFace(val) {
      const rotations = {
        1: 'rotateX(0deg) rotateY(0deg)',
        2: 'rotateX(0deg) rotateY(-90deg)',
        3: 'rotateX(-90deg) rotateY(0deg)',
        4: 'rotateX(90deg) rotateY(0deg)',
        5: 'rotateX(0deg) rotateY(90deg)',
        6: 'rotateX(0deg) rotateY(180deg)'
      };
      diceCube.style.transform = rotations[val] || rotations[1];
    }

    const sleep = (ms) => new Promise(res => setTimeout(res, ms));

    async function handleRollDice() {
      if (isAnimating) return;
      isAnimating = true;
      rollBtn.disabled = true;

      const activePlayer = players[currentPlayerIndex];
      const rolled = Math.floor(Math.random() * 6) + 1;

      // 1. Roll 3D Dice Animation
      sfx.diceRoll();
      statusTitle.textContent = `${activePlayer.name} is rolling...`;
      statusSubtitle.textContent = 'Good luck!';
      diceCube.classList.add('rolling');

      await sleep(800);

      diceCube.classList.remove('rolling');
      setDiceFace(rolled);
      lastRollBadge.textContent = `${activePlayer.name} rolled a ${rolled}!`;

      await sleep(300);

      // 2. Calculate Step-by-Step Path
      let targetPos = activePlayer.pos + rolled;
      const path = [];

      if (targetPos <= 100) {
        for (let step = activePlayer.pos + 1; step <= targetPos; step++) {
          path.push(step);
        }
      } else if (exactRollRequired) {
        // Walk up to 100, then bounce backward
        const overshoot = targetPos - 100;
        for (let step = activePlayer.pos + 1; step <= 100; step++) {
          path.push(step);
        }
        for (let b = 99; b >= 100 - overshoot; b--) {
          path.push(b);
        }
        statusSubtitle.textContent = `Rolled ${rolled} — Bounced back from 100!`;
      } else {
        for (let step = activePlayer.pos + 1; step <= 100; step++) {
          path.push(step);
        }
      }

      // 3. Animate Hopping Tile-by-Tile
      for (let i = 0; i < path.length; i++) {
        activePlayer.pos = path[i];
        sfx.stepHop(i);
        positionTokenElement(currentPlayerIndex, activePlayer.pos, false);
        renderScoreboard();
        await sleep(240);
      }

      // 4. Check for Win at Tile 100
      if (activePlayer.pos === 100) {
        sfx.victory();
        showVictoryModal(activePlayer);
        return;
      }

      // 5. Check for Ladder, Escalator, or Snake
      const jump = JUMPS[activePlayer.pos];
      if (jump) {
        await sleep(250);
        if (jump.type === 'escalator') {
          statusTitle.textContent = `🛗 MEGA ESCALATOR!`;
          statusSubtitle.textContent = `${activePlayer.name} rides up from ${activePlayer.pos} to ${jump.to}!`;
          sfx.escalatorRide();
        } else if (jump.type === 'ladder') {
          statusTitle.textContent = `🪜 CLIMBING LADDER!`;
          statusSubtitle.textContent = `${activePlayer.name} climbs from ${activePlayer.pos} to ${jump.to}!`;
          sfx.ladderClimb();
        } else {
          statusTitle.textContent = `🐍 OH NO, A SNAKE!`;
          statusSubtitle.textContent = `${activePlayer.name} slides down from ${activePlayer.pos} to ${jump.to}!`;
          sfx.snakeBite();
        }

        activePlayer.pos = jump.to;
        positionTokenElement(currentPlayerIndex, activePlayer.pos, true);
        renderScoreboard();
        await sleep(850);

        if (activePlayer.pos === 100) {
          sfx.victory();
          showVictoryModal(activePlayer);
          return;
        }
      }

      // Re-adjust offsets for all tokens after movement finishes
      players.forEach((_, idx) => positionTokenElement(idx, players[idx].pos, false));

      // 6. Next Player's Turn (Bonus roll on 6? Let's keep standard alternating turns, or bonus on 6!)
      if (rolled === 6) {
        statusTitle.textContent = `🎲 ${activePlayer.name} rolled a 6!`;
        statusSubtitle.textContent = `Bonus roll! Go again, ${activePlayer.name}!`;
        isAnimating = false;
        rollBtn.disabled = false;
      } else {
        currentPlayerIndex = (currentPlayerIndex + 1) % players.length;
        isAnimating = false;
        rollBtn.disabled = false;
        updateTurnUI();
      }
    }

    /**
     * =========================================================================
     * 6. VICTORY MODAL & CONFETTI CELEBRATION
     * =========================================================================
     */
    const victoryModal = document.getElementById('victoryModal');
    const winnerAvatarBox = document.getElementById('winnerAvatarBox');
    const winnerNameText = document.getElementById('winnerNameText');
    let confettiAnimId = null;

    function showVictoryModal(winner) {
      winnerNameText.textContent = `${winner.name} Wins!`;
      winnerAvatarBox.style.backgroundColor = winner.color;
      winnerAvatarBox.innerHTML = winner.photo
        ? `<img src="${winner.photo}" alt="${winner.name}" class="w-full h-full object-cover" />`
        : `<span class="text-5xl">${winner.emoji}</span>`;

      victoryModal.classList.remove('hidden');
      startConfetti();
    }

    function startConfetti() {
      const canvas = document.getElementById('confettiCanvas');
      const ctx = canvas.getContext('2d');
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;

      const pieces = Array.from({ length: 130 }, () => ({
        x: Math.random() * canvas.width,
        y: Math.random() * canvas.height - canvas.height,
        w: 8 + Math.random() * 8,
        h: 6 + Math.random() * 6,
        color: ['#ef4444', '#f59e0b', '#10b981', '#3b82f6', '#8b5cf6', '#ec4899'][Math.floor(Math.random() * 6)],
        vy: 2.5 + Math.random() * 4,
        vx: -2 + Math.random() * 4,
        rot: Math.random() * 360,
        vRot: -6 + Math.random() * 12
      }));

      function loop() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        pieces.forEach(p => {
          p.x += p.vx;
          p.y += p.vy;
          p.rot += p.vRot;
          if (p.y > canvas.height) p.y = -20;

          ctx.save();
          ctx.translate(p.x, p.y);
          ctx.rotate((p.rot * Math.PI) / 180);
          ctx.fillStyle = p.color;
          ctx.fillRect(-p.w / 2, -p.h / 2, p.w, p.h);
          ctx.restore();
        });
        confettiAnimId = requestAnimationFrame(loop);
      }
      loop();
    }

    function stopConfetti() {
      if (confettiAnimId) cancelAnimationFrame(confettiAnimId);
      const canvas = document.getElementById('confettiCanvas');
      const ctx = canvas.getContext('2d');
      ctx.clearRect(0, 0, canvas.width, canvas.height);
    }

    /**
     * =========================================================================
     * 7. EVENT LISTENERS
     * =========================================================================
     */
    document.getElementById('startGameBtn').addEventListener('click', () => {
      sfx.init();
      setupScreen.classList.add('hidden');
      gameScreen.classList.remove('hidden');
      initGame();
    });

    document.getElementById('backToSetupBtn').addEventListener('click', () => {
      stopConfetti();
      victoryModal.classList.add('hidden');
      gameScreen.classList.add('hidden');
      setupScreen.classList.remove('hidden');
    });

    document.getElementById('playAgainBtn').addEventListener('click', () => {
      stopConfetti();
      victoryModal.classList.add('hidden');
      initGame();
    });

    document.getElementById('newPlayersBtn').addEventListener('click', () => {
      stopConfetti();
      victoryModal.classList.add('hidden');
      gameScreen.classList.add('hidden');
      setupScreen.classList.remove('hidden');
    });

    rollBtn.addEventListener('click', handleRollDice);
    document.getElementById('diceClickArea').addEventListener('click', handleRollDice);

    document.getElementById('muteBtn').addEventListener('click', (e) => {
      sfx.muted = !sfx.muted;
      e.target.textContent = sfx.muted ? '🔇' : '🔊';
    });

    window.addEventListener('resize', () => {
      if (!gameScreen.classList.contains('hidden')) {
        players.forEach((_, idx) => positionTokenElement(idx, players[idx].pos, false));
      }
    });
  </script>
</body>
</html>
