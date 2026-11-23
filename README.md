<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>Snakes, Ladders & Escalators — Neon Edition</title>
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@600;700&family=Plus+Jakarta+Sans:wght@500;600;700;800&display=swap" rel="stylesheet">
  <style>
    :root {
      --dice-size: 70px;
      --dice-half: 35px;
    }

    @media (min-width: 640px) {
      :root {
        --dice-size: 80px;
        --dice-half: 40px;
      }
    }

    body {
      font-family: 'Plus Jakarta Sans', sans-serif;
      background: radial-gradient(circle at 20% 15%, #1e1b4b 0%, #090d16 55%, #05070c 100%);
      color: #f8fafc;
      min-height: 100vh;
      overflow-x: hidden;
      user-select: none;
      -webkit-tap-highlight-color: transparent;
    }

    h1, h2, h3, .font-display {
      font-family: 'Fredoka', cursive, sans-serif;
    }

    /* ==================== 3D GLOSSY WHITE DICE WITH RED PIPS ==================== */
    .dice-scene {
      width: var(--dice-size);
      height: var(--dice-size);
      perspective: 700px;
      margin: 0 auto;
    }

    .dice-cube {
      width: 100%;
      height: 100%;
      position: relative;
      transform-style: preserve-3d;
      transition: transform 0.9s cubic-bezier(0.2, 1.18, 0.35, 1);
    }

    .dice-cube.rolling {
      animation: diceTumble 0.65s linear infinite;
    }

    @keyframes diceTumble {
      0%   { transform: rotateX(0deg) rotateY(0deg) rotateZ(0deg); }
      25%  { transform: rotateX(195deg) rotateY(105deg) rotateZ(45deg); }
      50%  { transform: rotateX(370deg) rotateY(225deg) rotateZ(140deg); }
      75%  { transform: rotateX(550deg) rotateY(315deg) rotateZ(250deg); }
      100% { transform: rotateX(720deg) rotateY(360deg) rotateZ(360deg); }
    }

    .dice-face {
      position: absolute;
      width: 100%;
      height: 100%;
      background: linear-gradient(145deg, #ffffff 0%, #f8fafc 65%, #e2e8f0 100%);
      border-radius: 15px;
      border: 2px solid #cbd5e1;
      box-shadow:
        inset 0 4px 7px rgba(255, 255, 255, 1),
        inset 0 -5px 9px rgba(15, 23, 42, 0.2),
        0 6px 14px rgba(0, 0, 0, 0.45);
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      grid-template-rows: repeat(3, 1fr);
      padding: 10px;
      gap: 3px;
      backface-visibility: hidden;
    }

    /* Classic Glossy Red Dots (Pips) */
    .pip {
      width: 100%;
      height: 100%;
      border-radius: 9999px;
      background: radial-gradient(circle at 35% 32%, #ff6b6b 0%, #ef4444 45%, #991b1b 100%);
      box-shadow:
        inset 0 2px 3px rgba(69, 10, 10, 0.75),
        0 1px 1px rgba(255, 255, 255, 0.9);
      transform: scale(0.86);
    }

    .pip.ace {
      transform: scale(1.12);
    }

    .face-1 { transform: rotateY(0deg) translateZ(var(--dice-half)); }
    .face-2 { transform: rotateY(90deg) translateZ(var(--dice-half)); }
    .face-3 { transform: rotateX(90deg) translateZ(var(--dice-half)); }
    .face-4 { transform: rotateX(-90deg) translateZ(var(--dice-half)); }
    .face-5 { transform: rotateY(-90deg) translateZ(var(--dice-half)); }
    .face-6 { transform: rotateY(180deg) translateZ(var(--dice-half)); }

    /* ==================== NEON BOARD & TILES ==================== */
    .board-wrapper {
      aspect-ratio: 1 / 1;
      width: 100%;
      max-width: 650px;
      position: relative;
      border-radius: 22px;
      background: #0b0f19;
      box-shadow:
        0 0 35px rgba(56, 189, 248, 0.35),
        0 0 70px rgba(236, 72, 153, 0.2),
        0 0 0 4px #22d3ee,
        0 0 0 8px #0f172a,
        0 0 0 11px #f43f5e;
      overflow: hidden;
    }

    .board-grid {
      display: grid;
      grid-template-columns: repeat(10, 1fr);
      grid-template-rows: repeat(10, 1fr);
      width: 100%;
      height: 100%;
      gap: 2px;
      padding: 2px;
      background: #090d16;
    }

    .tile {
      position: relative;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      padding: 3px 4px;
      border-radius: 6px;
      font-weight: 800;
      transition: transform 0.2s ease, filter 0.2s ease;
    }

    .tile-num {
      font-family: 'Fredoka', sans-serif;
      font-size: clamp(0.66rem, 1.85vw, 0.95rem);
      line-height: 1;
      color: #090d16;
      text-shadow: 0 1px 0 rgba(255, 255, 255, 0.65);
      z-index: 2;
    }

    /* Escalator Moving Steps Animation */
    @keyframes escalatorFlow {
      to {
        stroke-dashoffset: -28;
      }
    }

    .escalator-steps {
      animation: escalatorFlow 0.95s linear infinite;
    }

    /* ==================== PLAYER TOKENS ==================== */
    .player-token {
      position: absolute;
      width: 7.6%;
      height: 7.6%;
      border-radius: 9999px;
      transform: translate(-50%, -50%);
      transition: left 0.23s cubic-bezier(0.34, 1.56, 0.64, 1), top 0.23s cubic-bezier(0.34, 1.56, 0.64, 1);
      z-index: 30;
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.7), 0 0 0 2.5px #ffffff;
      display: flex;
      align-items: center;
      justify-content: center;
      overflow: hidden;
      pointer-events: none;
    }

    .player-token.sliding {
      transition: left 0.8s cubic-bezier(0.22, 1, 0.36, 1), top 0.8s cubic-bezier(0.22, 1, 0.36, 1);
      z-index: 40;
    }

    .player-token.active-turn {
      z-index: 35;
      animation: neonTokenPulse 1.3s ease-in-out infinite;
    }

    @keyframes neonTokenPulse {
      0%, 100% { box-shadow: 0 4px 12px rgba(0, 0, 0, 0.8), 0 0 0 3px #ffffff, 0 0 14px 4px #facc15; }
      50%      { box-shadow: 0 6px 16px rgba(0, 0, 0, 0.9), 0 0 0 3px #ffffff, 0 0 22px 8px #22d3ee; }
    }

    .player-token img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }

    #confettiCanvas {
      position: fixed;
      inset: 0;
      pointer-events: none;
      z-index: 60;
    }
  </style>
</head>
<body class="antialiased">

  <canvas id="confettiCanvas"></canvas>

  <!-- ==================== SETUP & LOBBY SCREEN ==================== -->
  <div id="setupScreen" class="min-h-screen flex items-center justify-center p-4 sm:p-6">
    <div class="w-full max-w-2xl bg-slate-900/90 backdrop-blur-xl rounded-3xl shadow-2xl border-2 border-cyan-400/50 p-5 sm:p-8">

      <!-- Header -->
      <div class="text-center mb-6">
        <div class="inline-flex flex-wrap justify-center items-center gap-2 px-4 py-1.5 rounded-full bg-fuchsia-500/20 border border-fuchsia-400/40 text-fuchsia-300 font-bold text-xs sm:text-sm mb-2">
          <span>🐍 Cartoon Vipers</span>
          <span>•</span>
          <span>🪜 Wooden Ladders</span>
          <span>•</span>
          <span>🛗 Mega Escalators</span>
        </div>
        <h1 class="text-3xl sm:text-4xl font-bold text-transparent bg-clip-text bg-gradient-to-r from-cyan-300 via-fuchsia-400 to-amber-300 tracking-tight">
          Snakes &amp; Ladders Neon
        </h1>
        <p class="text-slate-400 text-xs sm:text-sm mt-1">
          Play locally with friends or create an Online Room Code to play across devices!
        </p>
      </div>

      <!-- Mode Switcher Tabs -->
      <div class="grid grid-cols-2 gap-2 p-1.5 rounded-2xl bg-slate-950/80 border border-slate-800 mb-6">
        <button id="tabLocalBtn" type="button" class="py-2.5 rounded-xl font-display font-bold text-sm sm:text-base bg-gradient-to-r from-cyan-500 to-blue-600 text-white shadow-lg transition cursor-pointer">
          📱 Local Pass &amp; Play
        </button>
        <button id="tabOnlineBtn" type="button" class="py-2.5 rounded-xl font-display font-bold text-sm sm:text-base text-slate-400 hover:text-white transition cursor-pointer">
          🌐 Play Online (2-4P)
        </button>
      </div>

      <!-- ==================== LOCAL SETUP PANEL ==================== -->
      <div id="localSetupPanel">
        <div class="mb-5">
          <label class="block text-xs font-bold uppercase tracking-wider text-slate-400 mb-2 text-center">Number of Players</label>
          <div class="grid grid-cols-3 gap-3 max-w-sm mx-auto" id="playerCountButtons">
            <button type="button" data-count="2" class="count-btn py-2.5 rounded-2xl font-display font-bold text-base sm:text-lg border-2 border-cyan-400 bg-cyan-500/20 text-cyan-300 shadow transition cursor-pointer">
              2 Players
            </button>
            <button type="button" data-count="3" class="count-btn py-2.5 rounded-2xl font-display font-bold text-base sm:text-lg border-2 border-slate-800 bg-slate-950/60 text-slate-400 hover:border-slate-600 transition cursor-pointer">
              3 Players
            </button>
            <button type="button" data-count="4" class="count-btn py-2.5 rounded-2xl font-display font-bold text-base sm:text-lg border-2 border-slate-800 bg-slate-950/60 text-slate-400 hover:border-slate-600 transition cursor-pointer">
              4 Players
            </button>
          </div>
        </div>

        <div id="playerSetupList" class="grid grid-cols-1 sm:grid-cols-2 gap-3.5 mb-5"></div>

        <div class="flex items-center justify-between bg-slate-950/70 rounded-2xl p-3.5 mb-6 border border-slate-800">
          <div>
            <p class="text-sm font-bold text-slate-200">Exact Roll to Finish (Tile 100)</p>
            <p class="text-xs text-slate-400">Bounce back extra steps if you roll over 100</p>
          </div>
          <label class="relative inline-flex items-center cursor-pointer">
            <input type="checkbox" id="exactRollToggle" class="sr-only peer" checked>
            <div class="w-11 h-6 bg-slate-700 rounded-full peer peer-checked:after:translate-x-full after:content-[''] after:absolute after:top-[2px] after:left-[2px] after:bg-white after:rounded-full after:h-5 after:w-5 after:transition-all peer-checked:bg-emerald-500"></div>
          </label>
        </div>

        <button id="startLocalGameBtn" type="button" class="w-full py-4 rounded-2xl bg-gradient-to-r from-emerald-400 via-teal-500 to-cyan-500 hover:brightness-110 text-slate-950 font-display font-bold text-xl shadow-lg shadow-emerald-500/25 active:scale-[0.99] transition cursor-pointer">
          🎮 Start Local Game!
        </button>
      </div>

      <!-- ==================== ONLINE MULTIPLAYER PANEL ==================== -->
      <div id="onlineSetupPanel" class="hidden">
        <!-- Your Online Profile Card -->
        <div class="bg-slate-950/80 rounded-2xl p-4 border border-slate-800 mb-5">
          <p class="text-xs font-bold uppercase tracking-wider text-cyan-400 mb-3">1. Your Player Profile &amp; Gallery Photo</p>
          <div class="flex items-center gap-4">
            <div class="relative shrink-0">
              <div id="onlineAvatarPreview" class="w-16 h-16 rounded-full flex items-center justify-center overflow-hidden border-4 border-fuchsia-500 bg-fuchsia-600 shadow-lg">
                <span class="text-2xl">😎</span>
              </div>
              <label class="absolute -bottom-1 -right-1 w-7 h-7 rounded-full bg-cyan-400 hover:bg-cyan-300 text-slate-950 flex items-center justify-center shadow cursor-pointer text-xs font-bold" title="Upload photo from gallery">
                📷
                <input type="file" accept="image/*" id="onlinePhotoInput" class="hidden" />
              </label>
            </div>
            <div class="flex-1">
              <label class="text-xs text-slate-400 font-semibold">Your Display Name</label>
              <input type="text" id="onlineNameInput" maxlength="14" value="Player" placeholder="Enter your name"
                class="w-full mt-1 px-3.5 py-2 rounded-xl bg-slate-900 border border-slate-700 text-white font-bold text-sm focus:outline-none focus:border-cyan-400" />
              <p class="text-[11px] text-slate-400 mt-1">Tap 📷 to use your own photo on the board!</p>
            </div>
          </div>
        </div>

        <!-- Create or Join Room -->
        <div id="onlineLobbyActions" class="grid grid-cols-1 sm:grid-cols-2 gap-4">
          <!-- Create Room -->
          <div class="bg-slate-950/70 rounded-2xl p-4 border border-slate-800 flex flex-col justify-between">
            <div>
              <h3 class="font-display font-bold text-lg text-emerald-400">Create New Room</h3>
              <p class="text-xs text-slate-400 mt-1 mb-4">Generate a 5-letter room code and share it with 1 to 3 friends.</p>
            </div>
            <button id="createRoomBtn" type="button" class="w-full py-3 rounded-xl bg-gradient-to-r from-emerald-500 to-teal-500 hover:brightness-110 text-slate-950 font-display font-bold text-base shadow cursor-pointer">
              ✨ Create Room
            </button>
          </div>

          <!-- Join Room -->
          <div class="bg-slate-950/70 rounded-2xl p-4 border border-slate-800 flex flex-col justify-between">
            <div>
              <h3 class="font-display font-bold text-lg text-fuchsia-400">Join Friend's Room</h3>
              <p class="text-xs text-slate-400 mt-1 mb-2">Enter the 5-letter code from your friend:</p>
              <input type="text" id="joinCodeInput" maxlength="5" placeholder="e.g. K7P2M"
                class="w-full mb-3 px-3 py-2 rounded-xl bg-slate-900 border border-slate-700 text-center font-display font-bold text-lg tracking-widest uppercase text-amber-300 focus:outline-none focus:border-fuchsia-400" />
            </div>
            <button id="joinRoomBtn" type="button" class="w-full py-3 rounded-xl bg-gradient-to-r from-fuchsia-500 to-purple-600 hover:brightness-110 text-white font-display font-bold text-base shadow cursor-pointer">
              🚀 Join Room
            </button>
          </div>
        </div>

        <!-- Active Online Waiting Room -->
        <div id="onlineWaitingRoom" class="hidden bg-slate-950/90 rounded-2xl p-5 border-2 border-amber-400/60 mt-4">
          <div class="flex flex-wrap items-center justify-between gap-2 pb-3 border-b border-slate-800">
            <div>
              <span class="text-xs font-bold uppercase tracking-wider text-slate-400">Share This Room Code</span>
              <div class="flex items-center gap-2 mt-0.5">
                <span id="displayRoomCode" class="font-display font-bold text-3xl tracking-widest text-amber-300">-----</span>
                <button id="copyCodeBtn" type="button" class="px-2.5 py-1 rounded-lg bg-slate-800 hover:bg-slate-700 text-xs font-bold text-cyan-300 cursor-pointer">
                  📋 Copy
                </button>
              </div>
            </div>
            <button id="leaveRoomBtn" type="button" class="px-3 py-1.5 rounded-xl bg-rose-500/20 text-rose-300 hover:bg-rose-500/30 text-xs font-bold cursor-pointer">
              Leave Room
            </button>
          </div>

          <div class="my-4">
            <p class="text-xs font-bold uppercase tracking-wider text-slate-400 mb-2">Players in Lobby (<span id="lobbyPlayerCount">1</span>/4)</p>
            <div id="lobbyPlayersList" class="grid grid-cols-1 sm:grid-cols-2 gap-2"></div>
          </div>

          <button id="startOnlineMatchBtn" type="button" class="w-full py-3.5 rounded-2xl bg-gradient-to-r from-amber-400 to-orange-500 hover:brightness-110 text-slate-950 font-display font-bold text-lg shadow-lg cursor-pointer">
            🎮 Start Online Match Now!
          </button>
          <p id="waitingHostNote" class="hidden text-center text-xs text-cyan-300 mt-2 animate-pulse">
            Waiting for the room host to start the match...
          </p>
        </div>

        <p id="onlineStatusMsg" class="text-center text-xs text-rose-400 font-semibold mt-3 min-h-[18px]"></p>
      </div>

    </div>
  </div>

  <!-- ==================== GAME SCREEN ==================== -->
  <div id="gameScreen" class="hidden min-h-screen flex flex-col lg:flex-row items-center justify-center gap-4 lg:gap-8 p-3 sm:p-6 max-w-6xl mx-auto">

    <!-- Left / Top: Neon Board -->
    <div class="w-full max-w-[650px] flex flex-col items-center">
      <div class="w-full flex items-center justify-between mb-2.5 px-1">
        <button id="backToSetupBtn" type="button" class="px-3 py-1.5 rounded-xl bg-slate-900/90 hover:bg-slate-800 text-slate-200 font-semibold text-xs sm:text-sm shadow border border-slate-700 transition cursor-pointer">
          ← Exit Game
        </button>

        <div id="onlineRoomPill" class="hidden items-center gap-1.5 px-3 py-1 rounded-xl bg-amber-400/20 border border-amber-400/40 text-amber-300 text-xs font-bold">
          <span>🌐 Room:</span>
          <span id="inGameRoomCode" class="tracking-widest">-----</span>
        </div>

        <div class="flex items-center gap-2">
          <span class="hidden sm:inline-flex items-center gap-1.5 text-xs font-semibold bg-slate-900/90 px-3 py-1.5 rounded-xl border border-slate-700 text-slate-300">
            <span>🪜 Ladder</span>
            <span>•</span>
            <span>🛗 Escalator</span>
            <span>•</span>
            <span>🐍 Snake</span>
          </span>
          <button id="muteBtn" type="button" class="p-2 rounded-xl bg-slate-900/90 hover:bg-slate-800 text-white shadow border border-slate-700 transition cursor-pointer" title="Toggle Sound">
            🔊
          </button>
        </div>
      </div>

      <!-- The 10x10 Neon Board -->
      <div class="board-wrapper" id="boardWrapper">
        <div id="boardGrid" class="board-grid"></div>
        <svg id="boardOverlay" viewBox="0 0 1000 1000" class="absolute inset-0 w-full h-full pointer-events-none z-20"></svg>
        <div id="tokensLayer" class="absolute inset-0 pointer-events-none z-30"></div>
      </div>
    </div>

    <!-- Right / Bottom: Controls, 3D Dice & Scoreboard -->
    <div class="w-full max-w-[650px] lg:w-96 flex flex-col gap-3 sm:gap-4">

      <!-- Turn Status Banner -->
      <div id="statusBanner" class="bg-slate-900/95 backdrop-blur rounded-2xl p-3.5 sm:p-4 shadow-xl border-2 border-cyan-400/50 text-center">
        <p id="statusTitle" class="font-display font-bold text-lg sm:text-xl text-cyan-300">Player 1's Turn!</p>
        <p id="statusSubtitle" class="text-xs sm:text-sm text-slate-400 mt-0.5">Tap the dice or button to roll</p>
      </div>

      <!-- 3D Glossy White Dice with Red Pips -->
      <div class="bg-slate-900/95 backdrop-blur rounded-3xl p-4 sm:p-6 shadow-xl border-2 border-fuchsia-500/40 flex flex-row lg:flex-col items-center justify-around lg:justify-center gap-4">
        <div id="diceClickArea" class="cursor-pointer p-2.5 rounded-2xl hover:bg-slate-800/70 transition" title="Tap to roll dice">
          <div class="dice-scene">
            <div id="diceCube" class="dice-cube">
              <!-- Face 1 (Red Center Pip) -->
              <div class="dice-face face-1">
                <div></div><div></div><div></div>
                <div></div><div class="pip ace"></div><div></div>
                <div></div><div></div><div></div>
              </div>
              <!-- Face 2 (2 Red Pips) -->
              <div class="dice-face face-2">
                <div class="pip"></div><div></div><div></div>
                <div></div><div></div><div></div>
                <div></div><div></div><div class="pip"></div>
              </div>
              <!-- Face 3 (3 Red Pips) -->
              <div class="dice-face face-3">
                <div class="pip"></div><div></div><div></div>
                <div></div><div class="pip"></div><div></div>
                <div></div><div></div><div class="pip"></div>
              </div>
              <!-- Face 4 (4 Red Pips) -->
              <div class="dice-face face-4">
                <div class="pip"></div><div></div><div class="pip"></div>
                <div></div><div></div><div></div>
                <div class="pip"></div><div></div><div class="pip"></div>
              </div>
              <!-- Face 5 (5 Red Pips) -->
              <div class="dice-face face-5">
                <div class="pip"></div><div></div><div class="pip"></div>
                <div></div><div class="pip"></div><div></div>
                <div class="pip"></div><div></div><div class="pip"></div>
              </div>
              <!-- Face 6 (6 Red Pips) -->
              <div class="dice-face face-6">
                <div class="pip"></div><div></div><div class="pip"></div>
                <div class="pip"></div><div></div><div class="pip"></div>
                <div class="pip"></div><div></div><div class="pip"></div>
              </div>
            </div>
          </div>
        </div>

        <div class="flex flex-col items-center w-full max-w-[215px]">
          <button id="rollBtn" type="button" class="w-full py-3.5 px-6 rounded-2xl bg-gradient-to-r from-fuchsia-500 via-pink-500 to-amber-500 hover:brightness-110 disabled:opacity-40 disabled:cursor-not-allowed text-white font-display font-bold text-lg sm:text-xl shadow-lg shadow-fuchsia-500/30 active:scale-95 transition cursor-pointer">
            🎲 Roll Dice
          </button>
          <span id="lastRollBadge" class="mt-2 text-xs font-semibold text-slate-400">
            Ready to roll
          </span>
        </div>
      </div>

      <!-- Live Standings -->
      <div class="bg-slate-900/95 backdrop-blur rounded-3xl p-4 shadow-xl border border-slate-800">
        <h3 class="font-display font-bold text-xs uppercase tracking-wider text-slate-400 mb-2.5 px-1">
          Players &amp; Positions
        </h3>
        <div id="scoreboardList" class="grid grid-cols-2 lg:grid-cols-1 gap-2"></div>
      </div>

    </div>
  </div>

  <!-- ==================== VICTORY MODAL ==================== -->
  <div id="victoryModal" class="hidden fixed inset-0 z-50 bg-slate-950/80 backdrop-blur-md flex items-center justify-center p-4">
    <div class="bg-slate-900 rounded-3xl max-w-md w-full p-6 sm:p-8 text-center shadow-2xl border-4 border-amber-400">
      <div class="text-5xl mb-3">🏆👑🎉</div>
      <div id="winnerAvatarBox" class="w-24 h-24 rounded-full mx-auto mb-4 border-4 border-amber-400 shadow-lg overflow-hidden flex items-center justify-center bg-amber-500"></div>
      <h2 id="winnerNameText" class="font-display font-bold text-3xl text-amber-300">Player 1 Wins!</h2>
      <p class="text-slate-300 text-sm sm:text-base mt-1 mb-6">
        Conquered the neon snakes &amp; rode the escalators to Tile 100!
      </p>
      <div class="flex flex-col sm:flex-row gap-3">
        <button id="playAgainBtn" type="button" class="flex-1 py-3.5 px-5 rounded-2xl bg-gradient-to-r from-emerald-400 to-teal-500 hover:brightness-110 text-slate-950 font-display font-bold text-lg shadow-md transition cursor-pointer">
          🔄 Play Again
        </button>
        <button id="newPlayersBtn" type="button" class="flex-1 py-3.5 px-5 rounded-2xl bg-slate-800 hover:bg-slate-700 text-white font-display font-bold text-lg transition cursor-pointer">
          🏠 Main Menu
        </button>
      </div>
    </div>
  </div>

  <script type="module">
    /**
     * =========================================================================
     * 1. OPTIONAL ONLINE MULTIPLAYER ENGINE (Firebase Firestore + Fallback)
     * =========================================================================
     */
    import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
    import { getAuth, signInAnonymously, signInWithCustomToken } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";
    import { getFirestore, doc, setDoc, getDoc, updateDoc, onSnapshot } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";

    let db = null;
    let auth = null;
    let currentUserId = 'local-' + Math.random().toString(36).slice(2, 9);
    const appId = typeof __app_id !== 'undefined' ? __app_id : 'snakes-ladders-neon';

    async function initOnlineBackend() {
      try {
        if (typeof __firebase_config !== 'undefined' && __firebase_config) {
          const cfg = JSON.parse(__firebase_config);
          const app = initializeApp(cfg);
          auth = getAuth(app);
          db = getFirestore(app);
          if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
            await signInWithCustomToken(auth, __initial_auth_token);
          } else {
            await signInAnonymously(auth);
          }
          if (auth.currentUser) {
            currentUserId = auth.currentUser.uid;
          }
        }
      } catch (err) {
        console.warn("Online sync unavailable, running in local mode:", err);
      }
    }
    initOnlineBackend();

    /**
     * =========================================================================
     * 2. SOUND ENGINE (Web Audio API)
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

      playTone(freq, type, duration, delay = 0, gainVal = 0.14, slideTo = null) {
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
          this.playTone(200 + Math.random() * 240, 'triangle', 0.06, i * 0.085, 0.12);
        }
      }

      stepHop(stepIndex) {
        const base = 380 + (stepIndex % 6) * 40;
        this.playTone(base, 'sine', 0.11, 0, 0.13, base * 1.25);
      }

      ladderClimb() {
        [330, 392, 440, 523, 659, 784].forEach((n, idx) => {
          this.playTone(n, 'triangle', 0.14, idx * 0.09, 0.15);
        });
      }

      escalatorRide() {
        this.playTone(210, 'sawtooth', 0.78, 0, 0.06, 600);
        [440, 554, 659, 880].forEach((n, idx) => {
          this.playTone(n, 'sine', 0.18, idx * 0.16, 0.14);
        });
      }

      snakeBite() {
        this.playTone(640, 'sawtooth', 0.65, 0, 0.14, 120);
        this.playTone(490, 'sine', 0.65, 0.05, 0.12, 90);
      }

      victory() {
        [523.25, 659.25, 783.99, 1046.5, 783.99, 1046.5].forEach((n, i) => {
          this.playTone(n, 'triangle', i === 5 ? 0.5 : 0.16, i * 0.14, 0.2);
        });
      }
    }

    const sfx = new SoundFX();

    /**
     * =========================================================================
     * 3. BOARD JUMPS & VIBRANT NEON TILE PALETTE
     * =========================================================================
     */
    const JUMPS = {
      // Wooden Ladders (moderate climbs)
      4:  { to: 25, type: 'ladder' },
      9:  { to: 31, type: 'ladder' },
      21: { to: 42, type: 'ladder' },
      51: { to: 67, type: 'ladder' },
      72: { to: 91, type: 'ladder' },

      // Long Mall Escalators (big climbs >= 35 tiles!)
      13: { to: 54, type: 'escalator' }, // +41 tiles Escalator
      28: { to: 84, type: 'escalator' }, // +56 tiles Mega Escalator

      // Catchy Cartoon Vipers (inspired by bold comic green/neon snakes)
      17: { to: 6,  type: 'snake', palette: { body: '#22c55e', spot: '#14532d', belly: '#fde047', eye: '#facc15' } },
      37: { to: 15, type: 'snake', palette: { body: '#16a34a', spot: '#052e16', belly: '#fdba74', eye: '#fde047' } },
      49: { to: 29, type: 'snake', palette: { body: '#ec4899', spot: '#831843', belly: '#fef08a', eye: '#38bdf8' } },
      62: { to: 19, type: 'snake', palette: { body: '#10b981', spot: '#064e3b', belly: '#facc15', eye: '#fef08a' } },
      75: { to: 45, type: 'snake', palette: { body: '#a855f7', spot: '#3b0764', belly: '#67e8f9', eye: '#facc15' } },
      88: { to: 36, type: 'snake', palette: { body: '#22c55e', spot: '#14532d', belly: '#fde047', eye: '#fbbf24' } },
      97: { to: 65, type: 'snake', palette: { body: '#f43f5e', spot: '#881337', belly: '#fde047', eye: '#fef08a' } }
    };

    // High-contrast Neon Tile Backgrounds
    const NEON_TILE_STYLES = [
      { bg: '#22d3ee', glow: 'inset 0 0 10px rgba(255,255,255,0.55)' }, // Neon Cyan
      { bg: '#f472b6', glow: 'inset 0 0 10px rgba(255,255,255,0.55)' }, // Neon Pink
      { bg: '#a3e635', glow: 'inset 0 0 10px rgba(255,255,255,0.55)' }, // Electric Lime
      { bg: '#facc15', glow: 'inset 0 0 10px rgba(255,255,255,0.55)' }, // Neon Yellow
      { bg: '#c084fc', glow: 'inset 0 0 10px rgba(255,255,255,0.55)' }, // Neon Purple
      { bg: '#fb923c', glow: 'inset 0 0 10px rgba(255,255,255,0.55)' }, // Neon Orange
      { bg: '#34d399', glow: 'inset 0 0 10px rgba(255,255,255,0.55)' }, // Neon Mint
      { bg: '#38bdf8', glow: 'inset 0 0 10px rgba(255,255,255,0.55)' }  // Electric Sky
    ];

    /**
     * =========================================================================
     * 4. GAME STATE & SETUP UI (Local + Online)
     * =========================================================================
     */
    const DEFAULT_COLORS = ['#ef4444', '#3b82f6', '#10b981', '#f59e0b'];
    const DEFAULT_EMOJIS = ['🦁', '🚀', '🦄', '🐼'];

    let gameMode = 'local'; // 'local' or 'online'
    let playerCount = 2;
    let playerConfigs = [
      { name: 'Player 1', color: DEFAULT_COLORS[0], photo: null, emoji: DEFAULT_EMOJIS[0] },
      { name: 'Player 2', color: DEFAULT_COLORS[1], photo: null, emoji: DEFAULT_EMOJIS[1] },
      { name: 'Player 3', color: DEFAULT_COLORS[2], photo: null, emoji: DEFAULT_EMOJIS[2] },
      { name: 'Player 4', color: DEFAULT_COLORS[3], photo: null, emoji: DEFAULT_EMOJIS[3] }
    ];

    // Online Room State
    let onlineProfile = { name: 'Player', photo: null, emoji: '😎' };
    let activeRoomCode = null;
    let isRoomHost = false;
    let roomUnsubscribe = null;
    let lastProcessedTurnKey = '';

    // Active Match State
    let players = [];
    let currentPlayerIndex = 0;
    let isAnimating = false;
    let exactRollRequired = true;

    // DOM References
    const setupScreen = document.getElementById('setupScreen');
    const gameScreen = document.getElementById('gameScreen');
    const localSetupPanel = document.getElementById('localSetupPanel');
    const onlineSetupPanel = document.getElementById('onlineSetupPanel');
    const tabLocalBtn = document.getElementById('tabLocalBtn');
    const tabOnlineBtn = document.getElementById('tabOnlineBtn');
    const playerSetupList = document.getElementById('playerSetupList');

    // Helper: Compress gallery photo to crisp circular thumbnail (120x120) so it fits smoothly in memory & online rooms
    function compressGalleryImage(file, callback) {
      const reader = new FileReader();
      reader.onload = (ev) => {
        const img = new Image();
        img.onload = () => {
          const canvas = document.createElement('canvas');
          const size = 120;
          canvas.width = size;
          canvas.height = size;
          const ctx = canvas.getContext('2d');
          const minSide = Math.min(img.width, img.height);
          const sx = (img.width - minSide) / 2;
          const sy = (img.height - minSide) / 2;
          ctx.drawImage(img, sx, sy, minSide, minSide, 0, 0, size, size);
          callback(canvas.toDataURL('image/jpeg', 0.75));
        };
        img.src = ev.target.result;
      };
      reader.readAsDataURL(file);
    }

    // Tab Switching
    tabLocalBtn.addEventListener('click', () => {
      gameMode = 'local';
      tabLocalBtn.className = 'py-2.5 rounded-xl font-display font-bold text-sm sm:text-base bg-gradient-to-r from-cyan-500 to-blue-600 text-white shadow-lg transition cursor-pointer';
      tabOnlineBtn.className = 'py-2.5 rounded-xl font-display font-bold text-sm sm:text-base text-slate-400 hover:text-white transition cursor-pointer';
      localSetupPanel.classList.remove('hidden');
      onlineSetupPanel.classList.add('hidden');
    });

    tabOnlineBtn.addEventListener('click', () => {
      gameMode = 'online';
      tabOnlineBtn.className = 'py-2.5 rounded-xl font-display font-bold text-sm sm:text-base bg-gradient-to-r from-fuchsia-500 to-purple-600 text-white shadow-lg transition cursor-pointer';
      tabLocalBtn.className = 'py-2.5 rounded-xl font-display font-bold text-sm sm:text-base text-slate-400 hover:text-white transition cursor-pointer';
      onlineSetupPanel.classList.remove('hidden');
      localSetupPanel.classList.add('hidden');
    });

    // Render Local Setup Cards
    function renderSetupCards() {
      playerSetupList.innerHTML = '';
      for (let i = 0; i < playerCount; i++) {
        const cfg = playerConfigs[i];
        const card = document.createElement('div');
        card.className = 'bg-slate-950/75 rounded-2xl p-3.5 border border-slate-800 flex items-center gap-3.5';

        const avatarPreview = cfg.photo
          ? `<img src="${cfg.photo}" alt="${cfg.name}" class="w-full h-full object-cover" />`
          : `<span class="text-2xl">${cfg.emoji}</span>`;

        card.innerHTML = `
          <div class="relative shrink-0">
            <div class="w-15 h-15 w-16 h-16 rounded-full flex items-center justify-center overflow-hidden shadow-md border-4" style="background-color: ${cfg.color}; border-color: ${cfg.color};">
              ${avatarPreview}
            </div>
            <label class="absolute -bottom-1 -right-1 w-7 h-7 rounded-full bg-cyan-400 hover:bg-cyan-300 text-slate-950 flex items-center justify-center shadow cursor-pointer text-xs font-bold" title="Upload photo from gallery">
              📷
              <input type="file" accept="image/*" class="hidden photo-input" data-index="${i}" />
            </label>
          </div>
          <div class="flex-1 min-w-0">
            <label class="text-[11px] font-bold uppercase tracking-wider text-slate-400">Player ${i + 1}</label>
            <input type="text" maxlength="14" value="${cfg.name}" data-index="${i}"
              class="name-input w-full mt-0.5 px-3 py-1.5 rounded-xl bg-slate-900 border border-slate-700 font-bold text-white text-sm focus:outline-none focus:border-cyan-400"
              placeholder="Enter name" />
            <div class="flex items-center justify-between mt-2">
              <div class="flex items-center gap-1.5">
                ${DEFAULT_COLORS.map(c => `
                  <button type="button" data-index="${i}" data-color="${c}"
                    class="color-dot w-5 h-5 rounded-full border-2 ${cfg.color === c ? 'border-white scale-110' : 'border-transparent'} shadow-sm transition cursor-pointer"
                    style="background-color: ${c};"></button>
                `).join('')}
              </div>
              ${cfg.photo
                ? `<button type="button" data-index="${i}" class="clear-photo text-[11px] font-semibold text-rose-400 hover:underline cursor-pointer">Remove</button>`
                : `<span class="text-[11px] text-slate-400">Tap 📷 photo</span>`}
            </div>
          </div>
        `;
        playerSetupList.appendChild(card);
      }

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
          compressGalleryImage(file, (dataUrl) => {
            playerConfigs[idx].photo = dataUrl;
            renderSetupCards();
          });
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

    document.querySelectorAll('.count-btn').forEach(btn => {
      btn.addEventListener('click', () => {
        playerCount = Number(btn.dataset.count);
        document.querySelectorAll('.count-btn').forEach(b => {
          const active = Number(b.dataset.count) === playerCount;
          b.className = active
            ? 'count-btn py-2.5 rounded-2xl font-display font-bold text-base sm:text-lg border-2 border-cyan-400 bg-cyan-500/20 text-cyan-300 shadow transition cursor-pointer'
            : 'count-btn py-2.5 rounded-2xl font-display font-bold text-base sm:text-lg border-2 border-slate-800 bg-slate-950/60 text-slate-400 hover:border-slate-600 transition cursor-pointer';
        });
        renderSetupCards();
      });
    });

    renderSetupCards();

    // Online Profile Photo Upload
    document.getElementById('onlinePhotoInput').addEventListener('change', (e) => {
      const file = e.target.files && e.target.files[0];
      if (!file) return;
      compressGalleryImage(file, (dataUrl) => {
        onlineProfile.photo = dataUrl;
        document.getElementById('onlineAvatarPreview').innerHTML = `<img src="${dataUrl}" class="w-full h-full object-cover" />`;
      });
    });

    /**
     * =========================================================================
     * 5. ONLINE ROOM LOGIC (Create / Join / Real-time Sync)
     * =========================================================================
     */
    const onlineStatusMsg = document.getElementById('onlineStatusMsg');
    const onlineLobbyActions = document.getElementById('onlineLobbyActions');
    const onlineWaitingRoom = document.getElementById('onlineWaitingRoom');

    function generateRoomCode() {
      const chars = 'ABCDEFGHJKLMNPQRSTUVWXYZ23456789';
      let code = '';
      for (let i = 0; i < 5; i++) code += chars[Math.floor(Math.random() * chars.length)];
      return code;
    }

    function getRoomDocRef(code) {
      return doc(db, 'artifacts', appId, 'public', 'data', 'snakes_rooms', code);
    }

    document.getElementById('createRoomBtn').addEventListener('click', async () => {
      onlineStatusMsg.textContent = '';
      if (!db) {
        onlineStatusMsg.textContent = 'Online server is initializing or unavailable. Use Local Pass & Play or refresh!';
        return;
      }
      const nameVal = document.getElementById('onlineNameInput').value.trim() || 'Host';
      onlineProfile.name = nameVal;

      const code = generateRoomCode();
      activeRoomCode = code;
      isRoomHost = true;

      const initialPlayers = [{
        uid: currentUserId,
        name: onlineProfile.name,
        color: DEFAULT_COLORS[0],
        photo: onlineProfile.photo || null,
        emoji: DEFAULT_EMOJIS[0],
        pos: 1
      }];

      try {
        await setDoc(getRoomDocRef(code), {
          code,
          hostUid: currentUserId,
          status: 'waiting',
          currentPlayerIndex: 0,
          players: initialPlayers,
          lastMove: null,
          updatedAt: Date.now()
        });
        subscribeToRoom(code);
      } catch (err) {
        onlineStatusMsg.textContent = 'Could not create room. Please try again.';
      }
    });

    document.getElementById('joinRoomBtn').addEventListener('click', async () => {
      onlineStatusMsg.textContent = '';
      if (!db) {
        onlineStatusMsg.textContent = 'Online server unavailable. Please check your connection.';
        return;
      }
      const code = document.getElementById('joinCodeInput').value.trim().toUpperCase();
      if (code.length < 4) {
        onlineStatusMsg.textContent = 'Please enter a valid 5-letter room code.';
        return;
      }
      const nameVal = document.getElementById('onlineNameInput').value.trim() || 'Guest';
      onlineProfile.name = nameVal;

      try {
        const snap = await getDoc(getRoomDocRef(code));
        if (!snap.exists()) {
          onlineStatusMsg.textContent = 'Room not found! Check the code and try again.';
          return;
        }
        const data = snap.data();
        if (data.status !== 'waiting') {
          onlineStatusMsg.textContent = 'That match has already started!';
          return;
        }
        if (data.players.length >= 4) {
          onlineStatusMsg.textContent = 'Room is full (Max 4 players).';
          return;
        }

        const existingIdx = data.players.findIndex(p => p.uid === currentUserId);
        const updatedPlayers = [...data.players];
        if (existingIdx === -1) {
          const slot = updatedPlayers.length;
          updatedPlayers.push({
            uid: currentUserId,
            name: onlineProfile.name,
            color: DEFAULT_COLORS[slot % 4],
            photo: onlineProfile.photo || null,
            emoji: DEFAULT_EMOJIS[slot % 4],
            pos: 1
          });
          await updateDoc(getRoomDocRef(code), { players: updatedPlayers, updatedAt: Date.now() });
        }

        activeRoomCode = code;
        isRoomHost = (data.hostUid === currentUserId);
        subscribeToRoom(code);
      } catch (err) {
        onlineStatusMsg.textContent = 'Error joining room. Please try again.';
      }
    });

    function subscribeToRoom(code) {
      if (roomUnsubscribe) roomUnsubscribe();
      onlineLobbyActions.classList.add('hidden');
      onlineWaitingRoom.classList.remove('hidden');
      document.getElementById('displayRoomCode').textContent = code;

      roomUnsubscribe = onSnapshot(getRoomDocRef(code), async (snap) => {
        if (!snap.exists()) return;
        const room = snap.data();

        // Update Waiting Room Lobby UI
        if (room.status === 'waiting') {
          document.getElementById('lobbyPlayerCount').textContent = room.players.length;
          const listEl = document.getElementById('lobbyPlayersList');
          listEl.innerHTML = room.players.map((p, idx) => `
            <div class="flex items-center gap-2.5 p-2.5 rounded-xl bg-slate-900 border border-slate-800">
              <div class="w-9 h-9 rounded-full overflow-hidden flex items-center justify-center border-2 border-white shrink-0" style="background-color:${p.color};">
                ${p.photo ? `<img src="${p.photo}" class="w-full h-full object-cover"/>` : `<span>${p.emoji}</span>`}
              </div>
              <div class="truncate">
                <p class="font-bold text-sm text-white truncate">${p.name} ${p.uid === room.hostUid ? '👑' : ''}</p>
                <p class="text-[11px] text-slate-400">Player ${idx + 1}</p>
              </div>
            </div>
          `).join('');

          const startBtn = document.getElementById('startOnlineMatchBtn');
          const waitNote = document.getElementById('waitingHostNote');
          if (room.hostUid === currentUserId) {
            startBtn.classList.remove('hidden');
            waitNote.classList.add('hidden');
          } else {
            startBtn.classList.add('hidden');
            waitNote.classList.remove('hidden');
          }
        }

        // Transition into Active Match
        if (room.status === 'playing') {
          if (setupScreen && !setupScreen.classList.contains('hidden')) {
            setupScreen.classList.add('hidden');
            gameScreen.classList.remove('hidden');
            document.getElementById('onlineRoomPill').classList.remove('hidden');
            document.getElementById('onlineRoomPill').classList.add('flex');
            document.getElementById('inGameRoomCode').textContent = code;

            players = room.players.map((p, idx) => ({ ...p, id: idx }));
            currentPlayerIndex = room.currentPlayerIndex || 0;
            lastProcessedTurnKey = '';
            buildBoardTiles();
            renderBoardOverlay();
            renderTokens();
            renderScoreboard();
            updateTurnUI();
            setDiceFace(1);
          }

          // Animate incoming remote move if new
          if (room.lastMove && room.lastMove.moveId !== lastProcessedTurnKey) {
            lastProcessedTurnKey = room.lastMove.moveId;
            await executeAnimatedMove(
              room.lastMove.playerIdx,
              room.lastMove.rolled,
              room.lastMove.nextPlayerIndex
            );
          }
        }
      }, (error) => {
        console.error("Room snapshot error:", error);
      });
    }

    document.getElementById('copyCodeBtn').addEventListener('click', () => {
      const code = document.getElementById('displayRoomCode').textContent;
      const temp = document.createElement('input');
      temp.value = code;
      document.body.appendChild(temp);
      temp.select();
      document.execCommand('copy');
      document.body.removeChild(temp);
      document.getElementById('copyCodeBtn').textContent = '✅ Copied!';
      setTimeout(() => { document.getElementById('copyCodeBtn').textContent = '📋 Copy'; }, 1800);
    });

    document.getElementById('leaveRoomBtn').addEventListener('click', () => {
      if (roomUnsubscribe) roomUnsubscribe();
      activeRoomCode = null;
      onlineWaitingRoom.classList.add('hidden');
      onlineLobbyActions.classList.remove('hidden');
    });

    document.getElementById('startOnlineMatchBtn').addEventListener('click', async () => {
      if (!activeRoomCode || !db) return;
      await updateDoc(getRoomDocRef(activeRoomCode), {
        status: 'playing',
        currentPlayerIndex: 0,
        lastMove: null,
        updatedAt: Date.now()
      });
    });

    /**
     * =========================================================================
     * 6. BOARD GEOMETRY & CUSTOM ARTWORK (Cartoon Vipers, Ladders, Escalators)
     * =========================================================================
     */
    const boardGrid = document.getElementById('boardGrid');
    const boardOverlay = document.getElementById('boardOverlay');

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
          const styleObj = NEON_TILE_STYLES[(tileNum * 3 + r) % NEON_TILE_STYLES.length];
          tile.style.backgroundColor = styleObj.bg;
          tile.style.boxShadow = styleObj.glow;

          let badge = '';
          if (tileNum === 1) {
            tile.style.backgroundColor = '#4ade80';
            badge = `<span class="text-[8px] sm:text-[10px] font-black text-slate-950 uppercase">START</span>`;
          } else if (tileNum === 100) {
            tile.style.backgroundColor = '#fde047';
            badge = `<span class="text-[9px] sm:text-[11px] font-black text-slate-950">👑 100</span>`;
          } else if (JUMPS[tileNum]) {
            const j = JUMPS[tileNum];
            if (j.type === 'escalator') {
              badge = `<span class="text-[8px] sm:text-[10px] font-extrabold text-slate-950 bg-white/60 px-1 rounded">🛗→${j.to}</span>`;
            } else if (j.type === 'ladder') {
              badge = `<span class="text-[8px] sm:text-[10px] font-extrabold text-slate-950 bg-white/50 px-1 rounded">🪜→${j.to}</span>`;
            } else {
              badge = `<span class="text-[8px] sm:text-[10px] font-extrabold text-rose-950 bg-white/60 px-1 rounded">🐍→${j.to}</span>`;
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
          <filter id="comicShadow" x="-25%" y="-25%" width="150%" height="150%">
            <feDropShadow dx="0" dy="6" stdDeviation="4" flood-color="#020617" flood-opacity="0.55"/>
          </filter>
          <linearGradient id="woodGrain" x1="0%" y1="0%" x2="100%" y2="0%">
            <stop offset="0%" stop-color="#78350f"/>
            <stop offset="50%" stop-color="#d97706"/>
            <stop offset="100%" stop-color="#92400e"/>
          </linearGradient>
        </defs>
      `;

      // 1. Draw Wooden Ladders & Animated Mall Escalators first
      Object.entries(JUMPS).forEach(([startStr, jump]) => {
        const start = Number(startStr);
        if (jump.type === 'ladder') {
          svgHTML += drawWoodenLadder(start, jump.to);
        } else if (jump.type === 'escalator') {
          svgHTML += drawEscalator(start, jump.to);
        }
      });

      // 2. Draw Expressive Comic-Style Snakes on top
      Object.entries(JUMPS).forEach(([startStr, jump]) => {
        const start = Number(startStr);
        if (jump.type === 'snake') {
          svgHTML += drawComicViperSnake(start, jump.to, jump.palette);
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
      const nx = (-dy / len) * 21;
      const ny = (dx / len) * 21;

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
          <line x1="${lx}" y1="${ly}" x2="${rx}" y2="${ry}" stroke="#451a03" stroke-width="11" stroke-linecap="round"/>
          <line x1="${lx}" y1="${ly}" x2="${rx}" y2="${ry}" stroke="#f59e0b" stroke-width="5" stroke-linecap="round"/>
        `;
      }

      return `
        <g filter="url(#comicShadow)">
          ${rungs}
          <line x1="${r1x1}" y1="${r1y1}" x2="${r1x2}" y2="${r1y2}" stroke="#451a03" stroke-width="15" stroke-linecap="round"/>
          <line x1="${r1x1}" y1="${r1y1}" x2="${r1x2}" y2="${r1y2}" stroke="url(#woodGrain)" stroke-width="9" stroke-linecap="round"/>
          <line x1="${r2x1}" y1="${r2y1}" x2="${r2x2}" y2="${r2y2}" stroke="#451a03" stroke-width="15" stroke-linecap="round"/>
          <line x1="${r2x1}" y1="${r2y1}" x2="${r2x2}" y2="${r2y2}" stroke="url(#woodGrain)" stroke-width="9" stroke-linecap="round"/>
        </g>
      `;
    }

    function drawEscalator(fromTile, toTile) {
      const p1 = getTileCoords(fromTile);
      const p2 = getTileCoords(toTile);
      const dx = p2.cx - p1.cx;
      const dy = p2.cy - p1.cy;
      const len = Math.hypot(dx, dy);
      const nx = (-dy / len) * 28;
      const ny = (dx / len) * 28;

      const r1x1 = p1.cx + nx, r1y1 = p1.cy + ny;
      const r1x2 = p2.cx + nx, r1y2 = p2.cy + ny;
      const r2x1 = p1.cx - nx, r2y1 = p1.cy - ny;
      const r2x2 = p2.cx - nx, r2y2 = p2.cy - ny;

      return `
        <g filter="url(#comicShadow)">
          <!-- Sleek Mall Escalator Track -->
          <line x1="${p1.cx}" y1="${p1.cy}" x2="${p2.cx}" y2="${p2.cy}" stroke="#0f172a" stroke-width="52" stroke-linecap="round"/>
          <line x1="${p1.cx}" y1="${p1.cy}" x2="${p2.cx}" y2="${p2.cy}" stroke="#334155" stroke-width="42" stroke-linecap="round"/>
          <!-- Moving Metallic Steps -->
          <line x1="${p1.cx}" y1="${p1.cy}" x2="${p2.cx}" y2="${p2.cy}"
                stroke="#f8fafc" stroke-width="32" stroke-dasharray="9 19" class="escalator-steps"/>
          <!-- Neon Cyan Side Glass & Safety Trim -->
          <line x1="${r1x1}" y1="${r1y1}" x2="${r1x2}" y2="${r1y2}" stroke="#22d3ee" stroke-width="13" stroke-linecap="round"/>
          <line x1="${r2x1}" y1="${r2y1}" x2="${r2x2}" y2="${r2y2}" stroke="#22d3ee" stroke-width="13" stroke-linecap="round"/>
          <!-- Black Moving Handrails -->
          <line x1="${r1x1}" y1="${r1y1}" x2="${r1x2}" y2="${r1y2}" stroke="#090d16" stroke-width="6" stroke-linecap="round"/>
          <line x1="${r2x1}" y1="${r2y1}" x2="${r2x2}" y2="${r2y2}" stroke="#090d16" stroke-width="6" stroke-linecap="round"/>
          <!-- Glowing Boarding & Exit Pads -->
          <circle cx="${p1.cx}" cy="${p1.cy}" r="24" fill="#06b6d4" stroke="#ffffff" stroke-width="3.5"/>
          <text x="${p1.cx}" y="${p1.cy + 5}" text-anchor="middle" fill="#090d16" font-size="16" font-weight="900">UP</text>
          <circle cx="${p2.cx}" cy="${p2.cy}" r="19" fill="#10b981" stroke="#ffffff" stroke-width="3.5"/>
        </g>
      `;
    }

    /**
     * Custom Comic Viper Snake inspired by the user's reference image:
     * Bold dark outlines, expressive angry/grinning eyes, sharp white fangs,
     * forked red tongue, spotted scales, and ribbed belly!
     */
    function drawComicViperSnake(headTile, tailTile, palette) {
      const head = getTileCoords(headTile);
      const tail = getTileCoords(tailTile);

      const dx = tail.cx - head.cx;
      const dy = tail.cy - head.cy;
      const len = Math.hypot(dx, dy);
      const perpX = -dy / len;
      const perpY = dx / len;

      // Winding S-curve control points
      const waveAmp = Math.min(105, Math.max(52, len * 0.27));
      const cp1x = head.cx + dx * 0.30 + perpX * waveAmp;
      const cp1y = head.cy + dy * 0.30 + perpY * waveAmp;
      const cp2x = head.cx + dx * 0.70 - perpX * waveAmp;
      const cp2y = head.cy + dy * 0.70 - perpY * waveAmp;

      const pathD = `M ${head.cx} ${head.cy} C ${cp1x} ${cp1y}, ${cp2x} ${cp2y}, ${tail.cx} ${tail.cy}`;
      const headAngle = Math.atan2(head.cy - cp1y, head.cx - cp1x) * (180 / Math.PI);

      return `
        <g filter="url(#comicShadow)">
          <!-- Thick Comic Ink Outline -->
          <path d="${pathD}" fill="none" stroke="#090d16" stroke-width="34" stroke-linecap="round"/>
          <!-- Main Vibrant Viper Body -->
          <path d="${pathD}" fill="none" stroke="${palette.body}" stroke-width="26" stroke-linecap="round"/>
          <!-- Ribbed Belly Pattern -->
          <path d="${pathD}" fill="none" stroke="${palette.belly}" stroke-width="14" stroke-dasharray="8 12" stroke-linecap="round" opacity="0.9"/>
          <!-- Dark Scale Spots -->
          <path d="${pathD}" fill="none" stroke="${palette.spot}" stroke-width="18" stroke-dasharray="4 26" stroke-linecap="round" opacity="0.75"/>
          <!-- Glossy Comic Highlight -->
          <path d="${pathD}" fill="none" stroke="#ffffff" stroke-opacity="0.4" stroke-width="4" stroke-linecap="round"/>

          <!-- Expressive Mischievous Cartoon Viper Head -->
          <g transform="translate(${head.cx}, ${head.cy}) rotate(${headAngle})">
            <!-- Comic Action Lines near Head (like reference image) -->
            <polygon points="-14,-28 -26,-42 -18,-45" fill="#facc15" stroke="#090d16" stroke-width="2"/>
            <polygon points="-24,-20 -38,-28 -33,-34" fill="#facc15" stroke="#090d16" stroke-width="2"/>

            <!-- Long Forked Red Tongue -->
            <path d="M 22 2 Q 36 -6 48 0 L 41 3 L 48 9 Q 35 6 22 5 Z" fill="#ef4444" stroke="#090d16" stroke-width="2" stroke-linejoin="round"/>

            <!-- Lower Jaw / Open Grinning Mouth -->
            <ellipse cx="10" cy="4" rx="22" ry="16" fill="#7f1d1d" stroke="#090d16" stroke-width="3.5"/>
            <!-- Bottom Fangs -->
            <polygon points="18,12 22,4 25,11" fill="#ffffff" stroke="#090d16" stroke-width="1.5"/>

            <!-- Upper Snout & Brow Ridge -->
            <path d="M -18 -6 C -16 -24, 18 -24, 30 -4 C 24 4, 8 4, -14 2 Z" fill="${palette.body}" stroke="#090d16" stroke-width="3.5"/>

            <!-- Sharp Upper White Fangs -->
            <polygon points="16,-1 20,13 24,-1" fill="#ffffff" stroke="#090d16" stroke-width="1.8"/>
            <polygon points="6,-1 9,10 13,-1" fill="#ffffff" stroke="#090d16" stroke-width="1.8"/>

            <!-- Fierce Yellow Slit Eyes with Angry Brows -->
            <ellipse cx="8" cy="-11" rx="8" ry="6.5" fill="${palette.eye}" stroke="#090d16" stroke-width="2.5"/>
            <ellipse cx="10" cy="-11" rx="2.4" ry="4.5" fill="#090d16"/>
            <circle cx="11" cy="-13" r="1.3" fill="#ffffff"/>
            <!-- Angry Brow Line -->
            <path d="M 0 -18 L 16 -13" stroke="#090d16" stroke-width="3.5" stroke-linecap="round"/>

            <!-- Nostrils -->
            <circle cx="23" cy="-7" r="1.6" fill="#090d16"/>
          </g>
        </g>
      `;
    }

    /**
     * =========================================================================
     * 7. TOKENS, 3D DICE & MOVEMENT ANIMATION
     * =========================================================================
     */
    const tokensLayer = document.getElementById('tokensLayer');
    const scoreboardList = document.getElementById('scoreboardList');
    const statusTitle = document.getElementById('statusTitle');
    const statusSubtitle = document.getElementById('statusSubtitle');
    const rollBtn = document.getElementById('rollBtn');
    const diceCube = document.getElementById('diceCube');
    const lastRollBadge = document.getElementById('lastRollBadge');

    const TOKEN_OFFSETS = [
      { dx: -17, dy: -17 },
      { dx:  17, dy: -17 },
      { dx: -17, dy:  17 },
      { dx:  17, dy:  17 }
    ];

    function initLocalGame() {
      exactRollRequired = document.getElementById('exactRollToggle').checked;
      players = [];
      for (let i = 0; i < playerCount; i++) {
        players.push({
          id: i,
          uid: `local-${i}`,
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

      el.classList.toggle('sliding', isSliding);

      const coords = getTileCoords(tileNum);
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
            ? 'bg-cyan-500/15 border-cyan-400 shadow-md'
            : 'bg-slate-950/70 border-slate-800'
        }`;

        row.innerHTML = `
          <div class="flex items-center gap-2.5 min-w-0">
            <div class="w-9 h-9 rounded-full shrink-0 overflow-hidden flex items-center justify-center border-2 border-white shadow" style="background-color: ${p.color};">
              ${avatarHtml}
            </div>
            <div class="truncate">
              <p class="font-bold text-sm text-white truncate">${p.name}</p>
              <p class="text-[11px] font-semibold ${isTurn ? 'text-cyan-300' : 'text-slate-400'}">
                ${isTurn ? '● Active Turn' : 'Waiting'}
              </p>
            </div>
          </div>
          <div class="px-2.5 py-1 rounded-xl bg-slate-900 border border-slate-700 font-display font-bold text-sm text-amber-300">
            Tile ${p.pos}
          </div>
        `;
        scoreboardList.appendChild(row);
      });
    }

    function updateTurnUI() {
      const p = players[currentPlayerIndex];
      const isMyOnlineTurn = (gameMode === 'local') || (p.uid === currentUserId);

      statusTitle.textContent = isMyOnlineTurn ? `${p.name}'s Turn!` : `Waiting for ${p.name}...`;
      statusTitle.style.color = '#67e8f9';
      statusSubtitle.textContent = isMyOnlineTurn
        ? `Currently on Tile ${p.pos} — Tap dice to roll!`
        : `${p.name} is on Tile ${p.pos}`;

      rollBtn.disabled = !isMyOnlineTurn || isAnimating;

      document.querySelectorAll('.player-token').forEach((tok, idx) => {
        tok.classList.toggle('active-turn', idx === currentPlayerIndex);
      });
      renderScoreboard();
    }

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

    async function handleRollDiceClick() {
      if (isAnimating) return;
      const activePlayer = players[currentPlayerIndex];
      if (gameMode === 'online' && activePlayer.uid !== currentUserId) return;

      const rolled = Math.floor(Math.random() * 6) + 1;
      const nextIdx = (rolled === 6) ? currentPlayerIndex : (currentPlayerIndex + 1) % players.length;

      if (gameMode === 'online' && activeRoomCode && db) {
        isAnimating = true;
        rollBtn.disabled = true;
        const moveId = `${Date.now()}-${Math.random().toString(36).slice(2, 6)}`;
        lastProcessedTurnKey = moveId;

        // Calculate final destination to persist in Firestore
        const finalPos = computeDestinationPos(activePlayer.pos, rolled);
        const updatedPlayers = players.map((pl, idx) =>
          idx === currentPlayerIndex ? { ...pl, pos: finalPos } : pl
        );

        await updateDoc(getRoomDocRef(activeRoomCode), {
          players: updatedPlayers,
          currentPlayerIndex: nextIdx,
          lastMove: {
            moveId,
            playerIdx: currentPlayerIndex,
            rolled,
            nextPlayerIndex: nextIdx
          },
          updatedAt: Date.now()
        });

        await executeAnimatedMove(currentPlayerIndex, rolled, nextIdx);
      } else {
        await executeAnimatedMove(currentPlayerIndex, rolled, nextIdx);
      }
    }

    function computeDestinationPos(startPos, rolled) {
      let pos = startPos + rolled;
      if (pos > 100) {
        pos = exactRollRequired ? 100 - (pos - 100) : 100;
      }
      if (JUMPS[pos]) {
        pos = JUMPS[pos].to;
      }
      return pos;
    }

    async function executeAnimatedMove(playerIdx, rolled, nextPlayerIdx) {
      isAnimating = true;
      rollBtn.disabled = true;

      const activePlayer = players[playerIdx];

      // 1. 3D Dice Tumble
      sfx.diceRoll();
      statusTitle.textContent = `${activePlayer.name} is rolling...`;
      statusSubtitle.textContent = 'Watch the dice!';
      diceCube.classList.add('rolling');

      await sleep(780);

      diceCube.classList.remove('rolling');
      setDiceFace(rolled);
      lastRollBadge.textContent = `${activePlayer.name} rolled a ${rolled}!`;

      await sleep(280);

      // 2. Step-by-Step Hop Path
      let targetPos = activePlayer.pos + rolled;
      const path = [];

      if (targetPos <= 100) {
        for (let step = activePlayer.pos + 1; step <= targetPos; step++) path.push(step);
      } else if (exactRollRequired) {
        const overshoot = targetPos - 100;
        for (let step = activePlayer.pos + 1; step <= 100; step++) path.push(step);
        for (let b = 99; b >= 100 - overshoot; b--) path.push(b);
        statusSubtitle.textContent = `Rolled ${rolled} — Bounced back from 100!`;
      } else {
        for (let step = activePlayer.pos + 1; step <= 100; step++) path.push(step);
      }

      for (let i = 0; i < path.length; i++) {
        activePlayer.pos = path[i];
        sfx.stepHop(i);
        positionTokenElement(playerIdx, activePlayer.pos, false);
        renderScoreboard();
        await sleep(230);
      }

      // 3. Check Win at 100
      if (activePlayer.pos === 100) {
        sfx.victory();
        showVictoryModal(activePlayer);
        return;
      }

      // 4. Check Ladder, Escalator, or Cartoon Snake
      const jump = JUMPS[activePlayer.pos];
      if (jump) {
        await sleep(240);
        if (jump.type === 'escalator') {
          statusTitle.textContent = `🛗 MEGA ESCALATOR!`;
          statusSubtitle.textContent = `${activePlayer.name} rides up from ${activePlayer.pos} to ${jump.to}!`;
          sfx.escalatorRide();
        } else if (jump.type === 'ladder') {
          statusTitle.textContent = `🪜 CLIMBING LADDER!`;
          statusSubtitle.textContent = `${activePlayer.name} climbs from ${activePlayer.pos} to ${jump.to}!`;
          sfx.ladderClimb();
        } else {
          statusTitle.textContent = `🐍 VIPER BITE!`;
          statusSubtitle.textContent = `${activePlayer.name} slides down from ${activePlayer.pos} to ${jump.to}!`;
          sfx.snakeBite();
        }

        activePlayer.pos = jump.to;
        positionTokenElement(playerIdx, activePlayer.pos, true);
        renderScoreboard();
        await sleep(850);

        if (activePlayer.pos === 100) {
          sfx.victory();
          showVictoryModal(activePlayer);
          return;
        }
      }

      players.forEach((_, idx) => positionTokenElement(idx, players[idx].pos, false));

      // 5. Advance Turn
      currentPlayerIndex = nextPlayerIdx;
      isAnimating = false;
      updateTurnUI();

      if (rolled === 6) {
        statusTitle.textContent = `🎲 ${activePlayer.name} rolled a 6!`;
        statusSubtitle.textContent = `Bonus roll for ${activePlayer.name}!`;
      }
    }

    /**
     * =========================================================================
     * 8. VICTORY MODAL & CONFETTI
     * =========================================================================
     */
    const victoryModal = document.getElementById('victoryModal');
    let confettiAnimId = null;

    function showVictoryModal(winner) {
      document.getElementById('winnerNameText').textContent = `${winner.name} Wins!`;
      const box = document.getElementById('winnerAvatarBox');
      box.style.backgroundColor = winner.color;
      box.innerHTML = winner.photo
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

      const pieces = Array.from({ length: 140 }, () => ({
        x: Math.random() * canvas.width,
        y: Math.random() * canvas.height - canvas.height,
        w: 8 + Math.random() * 8,
        h: 6 + Math.random() * 6,
        color: ['#22d3ee', '#f472b6', '#a3e635', '#facc15', '#c084fc', '#fb923c'][Math.floor(Math.random() * 6)],
        vy: 2.5 + Math.random() * 4.5,
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
     * 9. NAVIGATION & BUTTON LISTENERS
     * =========================================================================
     */
    document.getElementById('startLocalGameBtn').addEventListener('click', () => {
      sfx.init();
      gameMode = 'local';
      setupScreen.classList.add('hidden');
      gameScreen.classList.remove('hidden');
      document.getElementById('onlineRoomPill').classList.add('hidden');
      initLocalGame();
    });

    function exitToMainMenu() {
      stopConfetti();
      if (roomUnsubscribe) roomUnsubscribe();
      activeRoomCode = null;
      victoryModal.classList.add('hidden');
      gameScreen.classList.add('hidden');
      setupScreen.classList.remove('hidden');
      onlineWaitingRoom.classList.add('hidden');
      onlineLobbyActions.classList.remove('hidden');
    }

    document.getElementById('backToSetupBtn').addEventListener('click', exitToMainMenu);
    document.getElementById('newPlayersBtn').addEventListener('click', exitToMainMenu);

    document.getElementById('playAgainBtn').addEventListener('click', async () => {
      stopConfetti();
      victoryModal.classList.add('hidden');
      if (gameMode === 'online' && activeRoomCode && db) {
        const resetPlayers = players.map(p => ({ ...p, pos: 1 }));
        await updateDoc(getRoomDocRef(activeRoomCode), {
          players: resetPlayers,
          currentPlayerIndex: 0,
          lastMove: null,
          updatedAt: Date.now()
        });
        players = resetPlayers;
        currentPlayerIndex = 0;
        isAnimating = false;
        renderTokens();
        updateTurnUI();
      } else {
        initLocalGame();
      }
    });

    rollBtn.addEventListener('click', handleRollDiceClick);
    document.getElementById('diceClickArea').addEventListener('click', handleRollDiceClick);

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
