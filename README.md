<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>MAMİ01 GG İŞLEMLERİ</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0/css/all.min.css" rel="stylesheet">
    <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@400;700;900&family=Rajdhani:wght@300;500;700&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Rajdhani', sans-serif;
            background-color: #0f0c29;
            overflow-x: hidden;
        }
        
        .font-orbitron {
            font-family: 'Orbitron', sans-serif;
        }

        /* Animated Background */
        .bg-animate {
            background: linear-gradient(-45deg, #0f0c29, #302b63, #24243e, #1a1a2e);
            background-size: 400% 400%;
            animation: gradientBG 15s ease infinite;
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: -2;
        }

        @keyframes gradientBG {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        /* Floating Particles */
        .particles {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: -1;
            overflow: hidden;
        }

        .particle {
            position: absolute;
            width: 4px;
            height: 4px;
            background: rgba(139, 92, 246, 0.5);
            border-radius: 50%;
            animation: float 20s infinite linear;
            box-shadow: 0 0 10px rgba(139, 92, 246, 0.8);
        }

        @keyframes float {
            0% { transform: translateY(100vh) translateX(0); opacity: 0; }
            10% { opacity: 1; }
            90% { opacity: 1; }
            100% { transform: translateY(-10vh) translateX(100px); opacity: 0; }
        }

        /* Glassmorphism Card */
        .glass-card {
            background: rgba(255, 255, 255, 0.03);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 1px solid rgba(255, 255, 255, 0.1);
            box-shadow: 0 8px 32px 0 rgba(31, 38, 135, 0.37);
            transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
        }

        .glass-card:hover {
            background: rgba(255, 255, 255, 0.08);
            transform: translateY(-2px);
            border-color: rgba(139, 92, 246, 0.5);
            box-shadow: 0 12px 40px 0 rgba(139, 92, 246, 0.4);
        }

        .glass-card.selected {
            background: linear-gradient(135deg, rgba(139, 92, 246, 0.3), rgba(59, 130, 246, 0.3));
            border-color: rgba(139, 92, 246, 0.8);
            box-shadow: 0 0 20px rgba(139, 92, 246, 0.6);
        }

        /* Custom Checkbox */
        .custom-checkbox {
            appearance: none;
            width: 24px;
            height: 24px;
            border: 2px solid rgba(255, 255, 255, 0.3);
            border-radius: 6px;
            position: relative;
            cursor: pointer;
            transition: all 0.3s ease;
            pointer-events: none; /* Checkbox'a tıklanmasını engelle, kart tıklaması çalışsın */
        }

        .glass-card.selected .custom-checkbox {
            background: linear-gradient(135deg, #8b5cf6, #3b82f6);
            border-color: transparent;
        }

        .glass-card.selected .custom-checkbox::after {
            content: '\f00c';
            font-family: 'Font Awesome 6 Free';
            font-weight: 900;
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            color: white;
            font-size: 14px;
        }

        /* Neon Glow Text */
        .neon-text {
            text-shadow: 0 0 10px rgba(139, 92, 246, 0.5),
                         0 0 20px rgba(139, 92, 246, 0.3),
                         0 0 30px rgba(59, 130, 246, 0.3);
        }

        /* Button Pulse */
        .btn-pulse {
            animation: pulse-glow 2s infinite;
        }

        @keyframes pulse-glow {
            0% { box-shadow: 0 0 0 0 rgba(139, 92, 246, 0.7); }
            70% { box-shadow: 0 0 0 15px rgba(139, 92, 246, 0); }
            100% { box-shadow: 0 0 0 0 rgba(139, 92, 246, 0); }
        }

        /* Scrollbar */
        ::-webkit-scrollbar {
            width: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #0f0c29; 
        }
        ::-webkit-scrollbar-thumb {
            background: linear-gradient(to bottom, #8b5cf6, #3b82f6); 
            border-radius: 4px;
        }

        /* Success Animation */
        .checkmark {
            width: 50px;
            height: 50px;
            border-radius: 50%;
            display: block;
            stroke-width: 2;
            stroke: #fff;
            stroke-miterlimit: 10;
            box-shadow: inset 0px 0px 0px #7ac142;
            animation: fill .4s ease-in-out .4s forwards, scale .3s ease-in-out .9s both;
        }
        
        .checkmark__circle {
            stroke-dasharray: 166;
            stroke-dashoffset: 166;
            stroke-width: 2;
            stroke-miterlimit: 10;
            stroke: #7ac142;
            fill: none;
            animation: stroke 0.6s cubic-bezier(0.65, 0, 0.45, 1) forwards;
        }
        
        .checkmark__check {
            transform-origin: 50% 50%;
            stroke-dasharray: 48;
            stroke-dashoffset: 48;
            animation: stroke 0.3s cubic-bezier(0.65, 0, 0.45, 1) 0.8s forwards;
        }
        
        @keyframes stroke {
            100% { stroke-dashoffset: 0; }
        }
        @keyframes scale {
            0%, 100% { transform: none; }
            50% { transform: scale3d(1.1, 1.1, 1); }
        }
        @keyframes fill {
            100% { box-shadow: inset 0px 0px 0px 30px #7ac142; }
        }

        /* Glitch Effect for Title */
        .glitch {
            position: relative;
            color: white;
        }
        .glitch::before, .glitch::after {
            content: attr(data-text);
            position: absolute;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
        }
        .glitch::before {
            left: 2px;
            text-shadow: -1px 0 #ff00c1;
            clip: rect(44px, 450px, 56px, 0);
            animation: glitch-anim 5s infinite linear alternate-reverse;
        }
        .glitch::after {
            left: -2px;
            text-shadow: -1px 0 #00fff9;
            clip: rect(44px, 450px, 56px, 0);
            animation: glitch-anim2 5s infinite linear alternate-reverse;
        }
        @keyframes glitch-anim {
            0% { clip: rect(30px, 9999px, 10px, 0); }
            5% { clip: rect(70px, 9999px, 90px, 0); }
            10% { clip: rect(20px, 9999px, 50px, 0); }
            100% { clip: rect(60px, 9999px, 30px, 0); }
        }
        @keyframes glitch-anim2 {
            0% { clip: rect(10px, 9999px, 80px, 0); }
            5% { clip: rect(90px, 9999px, 20px, 0); }
            10% { clip: rect(40px, 9999px, 60px, 0); }
            100% { clip: rect(20px, 9999px, 70px, 0); }
        }
    </style>
</head>
<body class="text-white min-h-screen flex flex-col items-center pb-20">

    <!-- Background Elements -->
    <div class="bg-animate"></div>
    <div class="particles" id="particles"></div>

    <!-- Header -->
    <header class="w-full max-w-4xl px-4 pt-10 pb-6 text-center relative z-10">
        <div class="inline-block mb-2 px-4 py-1 rounded-full border border-purple-500/30 bg-purple-500/10 text-purple-300 text-sm font-bold tracking-widest uppercase">
            Premium Services
        </div>
        <h1 class="text-5xl md:text-7xl font-black font-orbitron mb-4 tracking-tighter glitch neon-text" data-text="MAMİ01">
            MAMİ01
        </h1>
        <h2 class="text-2xl md:text-3xl font-bold text-transparent bg-clip-text bg-gradient-to-r from-blue-400 to-purple-400 tracking-widest uppercase">
            GG İŞLEMLERİ
        </h2>
        <p class="mt-4 text-gray-400 text-sm md:text-base max-w-lg mx-auto">
            İhtiyacınız olan işlemleri seçin, otomatik olarak DM için hazırlansın.
        </p>
    </header>

    <!-- Main Content -->
    <main class="w-full max-w-3xl px-4 relative z-10 space-y-4">
        
        <!-- Service List -->
        <div id="service-list" class="space-y-3">
            <!-- Items will be injected here via JS -->
        </div>

        <!-- Action Area -->
        <div class="mt-8 sticky bottom-6 z-50">
            <div class="glass-card rounded-2xl p-4 md:p-6 flex flex-col md:flex-row items-center justify-between gap-4 border-t border-purple-500/50 shadow-[0_0_50px_rgba(139,92,246,0.3)]">
                <div class="text-center md:text-left">
                    <p class="text-gray-400 text-sm uppercase tracking-wider">Seçilen İşlem</p>
                    <p id="selected-count" class="text-2xl font-bold text-white font-orbitron">0 ADET</p>
                </div>
                
                <button onclick="submitOrder()" class="group relative w-full md:w-auto px-8 py-4 bg-gradient-to-r from-purple-600 to-blue-600 rounded-xl font-bold text-lg uppercase tracking-wider overflow-hidden transition-all hover:scale-105 btn-pulse">
                    <div class="absolute inset-0 bg-white/20 translate-y-full group-hover:translate-y-0 transition-transform duration-300"></div>
                    <span class="relative flex items-center justify-center gap-2">
                        <i class="fab fa-instagram"></i>
                        DM Gönder
                    </span>
                </button>
            </div>
        </div>

    </main>

    <!-- Success Modal -->
    <div id="success-modal" class="fixed inset-0 z-[100] hidden flex items-center justify-center px-4">
        <div class="absolute inset-0 bg-black/80 backdrop-blur-sm" onclick="closeModal()"></div>
        <div class="glass-card rounded-3xl p-8 max-w-sm w-full text-center relative transform scale-90 opacity-0 transition-all duration-300" id="modal-content">
            <div class="checkmark-container mb-6 flex justify-center">
                <svg class="checkmark" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 52 52">
                    <circle class="checkmark__circle" cx="26" cy="26" r="25" fill="none"/>
                    <path class="checkmark__check" fill="none" d="M14.1 27.2l7.1 7.2 16.7-16.8"/>
                </svg>
            </div>
            <h3 class="text-2xl font-bold text-white mb-2 font-orbitron">Hazır!</h3>
            <p class="text-gray-300 mb-6">Seçtiğiniz işlemler panoya kopyalandı. Instagram DM'e yönlendiriliyorsunuz...</p>
            <div class="w-full bg-gray-700 rounded-full h-1.5 mb-2 overflow-hidden">
                <div class="bg-gradient-to-r from-purple-500 to-blue-500 h-1.5 rounded-full" style="width: 0%" id="progress-bar"></div>
            </div>
        </div>
    </div>

    <script>
        // Data
        const services = [
            { id: 1, title: "Coin & Para", icon: "fa-coins", color: "text-yellow-400" },
            { id: 2, title: "King Rank", icon: "fa-crown", color: "text-yellow-300" },
            { id: 3, title: "Tampon Sökme", icon: "fa-car-crash", color: "text-red-400" },
            { id: 4, title: "KROM: Far, Kaliper, Cam, Çakar, Araba, Jant", icon: "fa-spray-can", color: "text-gray-300" },
            { id: 5, title: "Gövde Kiti (Başka Arabadaki Parçayı Başka Arabaya Takma)", icon: "fa-car-side", color: "text-blue-400" },
            { id: 6, title: "HP: 99, 300, 324, 400, 414, 925, 1695, Durmayan Araba, Özel HP", icon: "fa-tachometer-alt", color: "text-green-400" },
            { id: 7, title: "SANZIMAN: 1E-20, 1E-30", icon: "fa-cogs", color: "text-orange-400" },
            { id: 8, title: "Siren", icon: "fa-bullhorn", color: "text-red-500" },
            { id: 9, title: "Araba Kopyalama", icon: "fa-copy", color: "text-purple-400" },
            { id: 10, title: "Çizim & Logo Kopyalama", icon: "fa-paint-brush", color: "text-pink-400" },
            { id: 11, title: "Özel ID", icon: "fa-fingerprint", color: "text-cyan-400" },
            { id: 12, title: "Premium Jant Takma", icon: "fa-dharmachakra", color: "text-indigo-400" }
        ];

        let selectedServices = new Set();

        // Initialize
        document.addEventListener('DOMContentLoaded', () => {
            renderServices();
            createParticles();
        });

        // Render List
        function renderServices() {
            const container = document.getElementById('service-list');
            container.innerHTML = services.map(service => `
                <div onclick="toggleService(${service.id})" 
                     class="glass-card rounded-xl p-4 cursor-pointer flex items-center gap-4 group select-none ${selectedServices.has(service.id) ? 'selected' : ''}"
                     id="service-${service.id}">
                    
                    <div class="w-12 h-12 rounded-lg bg-white/5 flex items-center justify-center border border-white/10 group-hover:border-purple-500/50 transition-colors">
                        <i class="fas ${service.icon} ${service.color} text-xl"></i>
                    </div>
                    
                    <div class="flex-1">
                        <h3 class="font-bold text-lg text-white group-hover:text-purple-300 transition-colors">${service.title}</h3>
                    </div>
                    
                    <div class="relative">
                        <div class="custom-checkbox"></div>
                    </div>
                </div>
            `).join('');
        }

        // Toggle Selection - DÜZELTİLMİŞ VERSİYON
        function toggleService(id) {
            const element = document.getElementById(`service-${id}`);
            
            if (selectedServices.has(id)) {
                // Seçiliyse kaldır
                selectedServices.delete(id);
                element.classList.remove('selected');
            } else {
                // Seçili değilse ekle
                selectedServices.add(id);
                element.classList.add('selected');
                createRipple(element);
            }
            
            updateCounter();
        }

        // Update Counter
        function updateCounter() {
            const count = selectedServices.size;
            const counterEl = document.getElementById('selected-count');
            
            // Animate number change
            counterEl.style.transform = 'scale(1.2)';
            setTimeout(() => {
                counterEl.innerText = `${count} ADET`;
                counterEl.style.transform = 'scale(1)';
            }, 100);
        }

        // Create Ripple Effect
        function createRipple(element) {
            const ripple = document.createElement('div');
            ripple.className = 'absolute inset-0 bg-purple-500/20 rounded-xl animate-ping pointer-events-none';
            element.style.position = 'relative';
            element.appendChild(ripple);
            setTimeout(() => ripple.remove(), 500);
        }

        // Submit Order
        async function submitOrder() {
            if (selectedServices.size === 0) {
                // Shake animation for empty selection
                const btn = document.querySelector('button');
                btn.classList.add('animate-bounce');
                setTimeout(() => btn.classList.remove('animate-bounce'), 1000);
                return;
            }

            // Generate Text
            const selectedItems = services.filter(s => selectedServices.has(s.id));
            const date = new Date().toLocaleDateString('tr-TR');
            
            let message = `🎮 MAMİ01 GG İŞLEM TALEBİ 🎮\n`;
            message += `📅 Tarih: ${date}\n`;
            message += `━━━━━━━━━━━━━━━\n\n`;
            
            selectedItems.forEach((item, index) => {
                message += `${index + 1}. ${item.title}\n`;
            });
            
            message += `\n━━━━━━━━━━━━━━━\n`;
            message += `Bu işlemleri ne karşılığında yaparsın?`;

            // Copy to Clipboard
            try {
                await navigator.clipboard.writeText(message);
                showSuccessModal();
            } catch (err) {
                console.error('Clipboard failed', err);
                // Fallback
                const textArea = document.createElement("textarea");
                textArea.value = message;
                document.body.appendChild(textArea);
                textArea.select();
                document.execCommand("Copy");
                textArea.remove();
                showSuccessModal();
            }
        }

        // Show Success Modal & Redirect
        function showSuccessModal() {
            const modal = document.getElementById('success-modal');
            const content = document.getElementById('modal-content');
            const progressBar = document.getElementById('progress-bar');
            
            modal.classList.remove('hidden');
            // Trigger reflow
            void modal.offsetWidth;
            
            content.classList.remove('scale-90', 'opacity-0');
            content.classList.add('scale-100', 'opacity-100');
            
            // Progress bar animation
            setTimeout(() => {
                progressBar.style.transition = 'width 2s linear';
                progressBar.style.width = '100%';
            }, 100);

            // Redirect after 2 seconds
            setTimeout(() => {
                window.location.href = "https://www.instagram.com/karikatur0171?igsh=MTJ1azNxenVocXd5";
            }, 2000);
        }

        function closeModal() {
            const modal = document.getElementById('success-modal');
            const content = document.getElementById('modal-content');
            
            content.classList.remove('scale-100', 'opacity-100');
            content.classList.add('scale-90', 'opacity-0');
            
            setTimeout(() => {
                modal.classList.add('hidden');
            }, 300);
        }

        // Particle System
        function createParticles() {
            const container = document.getElementById('particles');
            const particleCount = 30;
            
            for (let i = 0; i < particleCount; i++) {
                const particle = document.createElement('div');
                particle.className = 'particle';
                
                // Random positioning and timing
                const left = Math.random() * 100;
                const delay = Math.random() * 20;
                const duration = 15 + Math.random() * 20;
                
                particle.style.left = `${left}%`;
                particle.style.animationDelay = `${delay}s`;
                particle.style.animationDuration = `${duration}s`;
                
                container.appendChild(particle);
            }
        }
    </script>
</body>
</html>

