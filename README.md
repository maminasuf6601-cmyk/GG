<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>Muzo'nun Yaramazlık Planı</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@500;600;700&family=Inter:wght@400;600;700&display=swap" rel="stylesheet">
    <style>
        * {
            box-sizing: border-box;
            user-select: none;
            -webkit-user-select: none;
            touch-action: none;
        }
        body, html {
            margin: 0;
            padding: 0;
            width: 100%;
            height: 100%;
            overflow: hidden;
            background-color: #1a2e1a;
            font-family: 'Inter', sans-serif;
        }
        .font-game {
            font-family: 'Fredoka', sans-serif;
        }
        canvas {
            display: block;
            width: 100%;
            height: 100%;
        }
        @keyframes pulse-danger {
            0%, 100% { box-shadow: inset 0 0 30px rgba(239, 68, 68, 0.0); }
            50% { box-shadow: inset 0 0 60px rgba(239, 68, 68, 0.55); }
        }
        .chase-vignette {
            animation: pulse-danger 1s infinite;
            pointer-events: none;
        }
        @keyframes popIn {
            0% { transform: scale(0.8); opacity: 0; }
            100% { transform: scale(1); opacity: 1; }
        }
        .modal-pop {
            animation: popIn 0.25s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
        }
        .joystick-base {
            background: rgba(255, 255, 255, 0.18);
            border: 2px solid rgba(255, 255, 255, 0.35);
            backdrop-filter: blur(4px);
        }
        .joystick-stick {
            background: rgba(255, 255, 255, 0.85);
            box-shadow: 0 4px 10px rgba(0,0,0,0.3);
        }
        .action-btn {
            transition: transform 0.08s ease, background-color 0.15s ease;
            box-shadow: 0 6px 0 rgba(0,0,0,0.35);
        }
        .action-btn:active, .action-btn.active {
            transform: translateY(4px);
            box-shadow: 0 2px 0 rgba(0,0,0,0.35);
        }
    </style>
</head>
<body class="relative w-screen h-screen overflow-hidden">

    <!-- Oyun Alanı (Canvas) -->
    <canvas id="gameCanvas"></canvas>

    <!-- Kovalamaca Kırmızı Ekran Efekti -->
    <div id="chaseOverlay" class="fixed inset-0 hidden chase-vignette z-10"></div>

    <!-- Üst Bilgi Paneli (HUD) -->
    <div id="hud" class="fixed top-0 left-0 right-0 p-3 sm:p-4 flex flex-wrap items-start justify-between gap-2 z-20 pointer-events-none">
        <div class="flex flex-col gap-2 max-w-[68%] sm:max-w-md">
            <div class="bg-slate-900/85 backdrop-blur-md border border-slate-700/80 text-white px-3.5 py-2 rounded-2xl shadow-lg flex items-center gap-2.5">
                <span id="missionIcon" class="text-xl sm:text-2xl">🤫</span>
                <div>
                    <div class="text-[10px] sm:text-xs uppercase tracking-wider text-amber-400 font-bold">Mevcut Görev</div>
                    <div id="missionText" class="font-game text-sm sm:text-base font-semibold leading-tight">Melih'e görünmeden ahıra sız!</div>
                </div>
            </div>

            <div class="bg-slate-900/80 backdrop-blur-md border border-slate-700/70 px-3 py-1.5 rounded-xl shadow-md flex items-center gap-2 w-48 sm:w-56">
                <span class="text-xs font-bold text-sky-300">⚡ Enerji</span>
                <div class="flex-1 h-2.5 bg-slate-700 rounded-full overflow-hidden">
                    <div id="staminaBar" class="h-full bg-gradient-to-r from-sky-400 to-emerald-400 w-full transition-all duration-75"></div>
                </div>
            </div>
        </div>

        <div class="flex items-center gap-2 pointer-events-auto">
            <div id="stealthBadge" class="bg-emerald-950/90 border border-emerald-500/50 text-emerald-300 px-3 py-1.5 rounded-xl text-xs sm:text-sm font-bold shadow-md flex items-center gap-1.5">
                <span id="stealthDot" class="w-2.5 h-2.5 rounded-full bg-emerald-400 animate-pulse"></span>
                <span id="stealthText">Gizli</span>
            </div>

            <button id="muteBtn" class="bg-slate-900/85 hover:bg-slate-800 border border-slate-700 text-white p-2.5 rounded-xl shadow-md active:scale-95 transition" title="Sesi Aç/Kapat">
                <span id="muteIcon" class="text-lg leading-none">🔊</span>
            </button>
        </div>
    </div>

    <!-- Ortada Çıkan Uyarı / Etkileşim İpucu -->
    <div id="promptBanner" class="fixed top-24 left-1/2 -translate-x-1/2 z-20 pointer-events-none hidden">
        <div id="promptText" class="bg-amber-500 text-slate-950 font-game font-bold px-4 py-2 rounded-full shadow-xl border-2 border-amber-200 text-sm sm:text-base whitespace-nowrap animate-bounce">
            🔥 Ahırı yakmak için [E] veya [BOŞLUK] tuşuna basılı tut!
        </div>
    </div>

    <!-- Mobil & Dokunmatik Kontroller -->
    <div id="touchControls" class="fixed inset-x-0 bottom-0 p-4 sm:p-6 flex items-end justify-between z-20 pointer-events-none">
        <div id="joystickZone" class="relative w-28 h-28 sm:w-32 sm:h-32 rounded-full joystick-base pointer-events-auto flex items-center justify-center">
            <div id="joystickStick" class="w-12 h-12 sm:w-14 sm:h-14 rounded-full joystick-stick pointer-events-none"></div>
        </div>

        <div class="flex items-end gap-3 pointer-events-auto">
            <button id="btnSprint" class="action-btn w-16 h-16 sm:w-20 sm:h-20 rounded-full bg-sky-500 hover:bg-sky-400 border-2 border-sky-200 text-white font-game font-bold flex flex-col items-center justify-center shadow-lg">
                <span class="text-xl sm:text-2xl">👟</span>
                <span class="text-[11px] sm:text-xs">KOŞ</span>
            </button>

            <button id="btnIgnite" class="action-btn w-20 h-20 sm:w-24 sm:h-24 rounded-full bg-orange-600 hover:bg-orange-500 border-2 border-orange-200 text-white font-game font-bold flex flex-col items-center justify-center shadow-lg opacity-50">
                <span class="text-2xl sm:text-3xl">🔥</span>
                <span class="text-xs sm:text-sm">YAK</span>
            </button>
        </div>
    </div>

    <!-- Başlangıç & Oyun Sonu Modal Ekranı -->
    <div id="gameModal" class="fixed inset-0 bg-slate-950/75 backdrop-blur-sm z-30 flex items-center justify-center p-4">
        <div class="modal-pop bg-slate-900 border-2 border-slate-700 rounded-3xl max-w-md w-full p-6 sm:p-7 text-center shadow-2xl text-white">
            <div id="modalEmoji" class="text-5xl sm:text-6xl mb-3">😈🔥🐄</div>
            <h1 id="modalTitle" class="font-game text-2xl sm:text-3xl font-bold text-amber-400 mb-2">Muzo'nun Yaramazlık Planı</h1>
            <p id="modalDesc" class="text-slate-300 text-sm sm:text-base leading-relaxed mb-5">
                Sen köyün en şımarık çocuğu <strong class="text-amber-300">Muzo</strong>'sun! Büyüyen köyde ilerideki <strong class="text-rose-400">Melih</strong>'in evinin yanındaki 10 inekli ahıra gizlice sız, kibriti çakıp ahırı yak ve inekler sağa sola kaçışırken Melih seni yakalamadan güvenli bölgeye kaç!
            </p>

            <div id="controlsGuide" class="bg-slate-800/80 border border-slate-700/80 rounded-2xl p-3.5 mb-6 text-left text-xs sm:text-sm text-slate-300 space-y-1.5">
                <div class="flex items-center gap-2"><span class="text-base">🕹️</span> <span><strong>Hareket:</strong> WASD / Yön Tuşları (veya Sol Joystick)</span></div>
                <div class="flex items-center gap-2"><span class="text-base">🌿</span> <span><strong>Gizlenme:</strong> Çalılara girerek Melih'in gözünden kaybol</span></div>
                <div class="flex items-center gap-2"><span class="text-base">🔥</span> <span><strong>Ahırı Yak:</strong> Ahırın yanında <strong>E / Boşluk</strong> (veya YAK butonu) basılı tut</span></div>
                <div class="flex items-center gap-2"><span class="text-base">👟</span> <span><strong>Hızlı Koş:</strong> <strong>Shift</strong> tuşu (veya KOŞ butonu)</span></div>
            </div>

            <button id="startBtn" class="w-full py-3.5 px-6 rounded-2xl bg-gradient-to-r from-amber-500 to-orange-600 hover:from-amber-400 hover:to-orange-500 text-slate-950 font-game font-bold text-lg sm:text-xl shadow-lg shadow-orange-600/30 active:scale-95 transition cursor-pointer">
                Oyuna Başla!
            </button>
        </div>
    </div>

    <script>
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');

        const gameModal = document.getElementById('gameModal');
        const modalEmoji = document.getElementById('modalEmoji');
        const modalTitle = document.getElementById('modalTitle');
        const modalDesc = document.getElementById('modalDesc');
        const controlsGuide = document.getElementById('controlsGuide');
        const startBtn = document.getElementById('startBtn');
        const missionIcon = document.getElementById('missionIcon');
        const missionText = document.getElementById('missionText');
        const staminaBar = document.getElementById('staminaBar');
        const stealthBadge = document.getElementById('stealthBadge');
        const stealthDot = document.getElementById('stealthDot');
        const stealthText = document.getElementById('stealthText');
        const promptBanner = document.getElementById('promptBanner');
        const promptText = document.getElementById('promptText');
        const chaseOverlay = document.getElementById('chaseOverlay');
        const muteBtn = document.getElementById('muteBtn');
        const muteIcon = document.getElementById('muteIcon');

        const joystickZone = document.getElementById('joystickZone');
        const joystickStick = document.getElementById('joystickStick');
        const btnSprint = document.getElementById('btnSprint');
        const btnIgnite = document.getElementById('btnIgnite');

        // ==========================================
        // WEB AUDIO API İLE SENTEZLENMİŞ SES EFEKTLERİ
        // ==========================================
        class SoundEffects {
            constructor() {
                this.ctx = null;
                this.muted = false;
                this.stepTimer = 0;
            }

            init() {
                if (!this.ctx) {
                    const AudioCtx = window.AudioContext || window.webkitAudioContext;
                    this.ctx = new AudioCtx();
                }
                if (this.ctx.state === 'suspended') {
                    this.ctx.resume();
                }
            }

            toggleMute() {
                this.muted = !this.muted;
                muteIcon.textContent = this.muted ? '🔇' : '🔊';
            }

            playStep(isRunning) {
                if (this.muted || !this.ctx) return;
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                osc.type = 'triangle';
                const baseFreq = isRunning ? 140 : 105;
                osc.frequency.setValueAtTime(baseFreq + Math.random() * 25, this.ctx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(45, this.ctx.currentTime + 0.06);

                gain.gain.setValueAtTime(isRunning ? 0.08 : 0.04, this.ctx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.001, this.ctx.currentTime + 0.06);

                osc.connect(gain);
                gain.connect(this.ctx.destination);
                osc.start();
                osc.stop(this.ctx.currentTime + 0.06);
            }

            playMatchStrike() {
                if (this.muted || !this.ctx) return;
                const bufferSize = this.ctx.sampleRate * 0.08;
                const buffer = this.ctx.createBuffer(1, bufferSize, this.ctx.sampleRate);
                const data = buffer.getChannelData(0);
                for (let i = 0; i < bufferSize; i++) {
                    data[i] = (Math.random() * 2 - 1) * Math.exp(-i / (bufferSize * 0.4));
                }
                const noise = this.ctx.createBufferSource();
                noise.buffer = buffer;

                const filter = this.ctx.createBiquadFilter();
                filter.type = 'highpass';
                filter.frequency.value = 1200;

                const gain = this.ctx.createGain();
                gain.gain.value = 0.12;

                noise.connect(filter);
                filter.connect(gain);
                gain.connect(this.ctx.destination);
                noise.start();
            }

            playMoo() {
                if (this.muted || !this.ctx) return;
                const now = this.ctx.currentTime;
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                osc.type = 'sawtooth';
                const pitch = 95 + Math.random() * 35;
                osc.frequency.setValueAtTime(pitch, now);
                osc.frequency.linearRampToValueAtTime(pitch * 1.18, now + 0.18);
                osc.frequency.linearRampToValueAtTime(pitch * 0.85, now + 0.55);

                const filter = this.ctx.createBiquadFilter();
                filter.type = 'lowpass';
                filter.frequency.value = 420;

                gain.gain.setValueAtTime(0.001, now);
                gain.gain.linearRampToValueAtTime(0.09, now + 0.08);
                gain.gain.exponentialRampToValueAtTime(0.001, now + 0.58);

                osc.connect(filter);
                filter.connect(gain);
                gain.connect(this.ctx.destination);
                osc.start(now);
                osc.stop(now + 0.6);
            }

            playAlert() {
                if (this.muted || !this.ctx) return;
                const now = this.ctx.currentTime;
                const osc = this.ctx.createOscillator();
                const gain = this.ctx.createGain();
                osc.type = 'sawtooth';
                osc.frequency.setValueAtTime(320, now);
                osc.frequency.linearRampToValueAtTime(640, now + 0.15);
                osc.frequency.linearRampToValueAtTime(480, now + 0.35);

                gain.gain.setValueAtTime(0.18, now);
                gain.gain.exponentialRampToValueAtTime(0.001, now + 0.4);

                osc.connect(gain);
                gain.connect(this.ctx.destination);
                osc.start(now);
                osc.stop(now + 0.4);
            }

            playCaught() {
                if (this.muted || !this.ctx) return;
                const now = this.ctx.currentTime;
                const notes = [300, 250, 190, 130];
                notes.forEach((freq, idx) => {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();
                    osc.type = 'sawtooth';
                    osc.frequency.setValueAtTime(freq, now + idx * 0.12);
                    gain.gain.setValueAtTime(0.15, now + idx * 0.12);
                    gain.gain.exponentialRampToValueAtTime(0.001, now + (idx + 1) * 0.12);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start(now + idx * 0.12);
                    osc.stop(now + (idx + 1) * 0.12);
                });
            }

            playVictory() {
                if (this.muted || !this.ctx) return;
                const now = this.ctx.currentTime;
                const notes = [523.25, 659.25, 783.99, 1046.50];
                notes.forEach((freq, idx) => {
                    const osc = this.ctx.createOscillator();
                    const gain = this.ctx.createGain();
                    osc.type = 'triangle';
                    osc.frequency.setValueAtTime(freq, now + idx * 0.1);
                    gain.gain.setValueAtTime(0.16, now + idx * 0.1);
                    gain.gain.exponentialRampToValueAtTime(0.001, now + idx * 0.1 + 0.3);
                    osc.connect(gain);
                    gain.connect(this.ctx.destination);
                    osc.start(now + idx * 0.1);
                    osc.stop(now + idx * 0.1 + 0.3);
                });
            }
        }

        const sfx = new SoundEffects();
        muteBtn.addEventListener('click', () => sfx.toggleMute());

        // ==========================================
        // BÜYÜTÜLMÜŞ KÖY DÜNYASI (2600 x 1850)
        // ==========================================
        const WORLD = {
            width: 2600,
            height: 1850
        };

        let gameState = 'MENU';
        let lastTime = 0;
        let screenShake = 0;

        const camera = { x: 0, y: 0 };

        const keys = {};
        const touchInput = {
            dx: 0,
            dy: 0,
            sprint: false,
            ignite: false
        };

        // Oyuncu: Muzo
        const muzo = {
            x: 190,
            y: 1560,
            radius: 17,
            baseSpeed: 195,
            sprintSpeed: 320,
            stamina: 100,
            maxStamina: 100,
            exhausted: false,
            angle: -0.5,
            walkAnim: 0,
            inBush: false,
            speech: '',
            speechTimer: 0
        };

        // Düşman: Ev Sahibi Melih
        const melih = {
            x: 1620,
            y: 780,
            radius: 20,
            patrolSpeed: 132,
            chaseSpeed: 260,
            angle: Math.PI,
            visionRange: 270,
            visionAngle: Math.PI / 3.2,
            walkAnim: 0,
            state: 'PATROL',
            waypointIndex: 0,
            waitTimer: 0,
            speech: '',
            speechTimer: 0,
            waypoints: [
                { x: 1620, y: 780, wait: 1.1 },
                { x: 2150, y: 760, wait: 1.0 },
                { x: 2240, y: 1140, wait: 1.2 },
                { x: 1720, y: 1220, wait: 1.0 },
                { x: 1240, y: 1020, wait: 1.2 },
                { x: 1380, y: 620, wait: 1.0 }
            ]
        };

        // Güvenli Bölge (Köy Girişi)
        const safeZone = {
            x: 60,
            y: 1410,
            width: 270,
            height: 290,
            label: "GÜVENLİ BÖLGE (KAÇIŞ)"
        };

        // Hedef Ahır (10 İnekli Büyük Ahır)
        const barn = {
            x: 2060,
            y: 170,
            width: 300,
            height: 210,
            igniteRadius: 85,
            burnProgress: 0,
            isBurning: false,
            matchTick: 0
        };

        // Melih'in Evi
        const house = {
            x: 1580,
            y: 170,
            width: 290,
            height: 210
        };

        // Köydeki Diğer Evler (10 Adet Köy Evi & Yapı)
        const villageHouses = [
            { x: 160, y: 200, w: 230, h: 170, roof: '#2563eb', wall: '#f8fafc', label: "DEMİRCİ EVİ" },
            { x: 560, y: 170, w: 245, h: 175, roof: '#d97706', wall: '#fef3c7', label: "MUHTAR EVİ" },
            { x: 1020, y: 180, w: 235, h: 170, roof: '#0d9488', wall: '#f0fdfa', label: "KAHVEHANE" },
            { x: 210, y: 620, w: 225, h: 165, roof: '#059669', wall: '#f1f5f9', label: "FIRIN" },
            { x: 640, y: 660, w: 230, h: 170, roof: '#ea580c', wall: '#fff7ed', label: "BAKKAL" },
            { x: 240, y: 1030, w: 220, h: 160, roof: '#0284c7', wall: '#f0f9ff', label: "TERZİ EVİ" },
            { x: 740, y: 1380, w: 255, h: 180, roof: '#7c3aed', wall: '#f8fafc', label: "KÖY EVİ" },
            { x: 1280, y: 1450, w: 245, h: 175, roof: '#db2777', wall: '#fef2f2', label: "ÇOBAN EVİ" },
            { x: 1820, y: 1430, w: 255, h: 180, roof: '#4f46e5', wall: '#f1f5f9', label: "DEPO EVİ" },
            { x: 2210, y: 1390, w: 240, h: 175, roof: '#16a34a', wall: '#f0fdf4', label: "BAĞ EVİ" }
        ];

        let obstacles = []
