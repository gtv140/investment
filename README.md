<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Global Investment</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        .light { --bg: #f8fafc; --card: #ffffff; --text: #0f172a; --border: #e2e8f0; --accent: #2563eb; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); transition: 0.3s; padding-bottom: 100px; }
        .glass { background: var(--card); border: 1px solid var(--border); box-shadow: 0 4px 20px rgba(0,0,0,0.1); }
        .page { display: none; animation: slideUp 0.4s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 16px; font-weight: 800; }
        input { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 14px; width: 100%; outline: none; font-size: 13px; }
        .nav-icon { opacity: 0.4; transition: 0.3s; }
        .nav-active { opacity: 1; color: var(--accent); transform: scale(1.1); }
        .plan-card { position: relative; overflow: hidden; border-radius: 2rem; transition: 0.3s; }
        .plan-card:active { transform: scale(0.96); }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body>

    <header class="p-4 flex justify-between items-center sticky top-0 bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)] z-[5000]">
        <div class="flex items-center gap-2">
            <div onclick="adminTap()" class="w-8 h-8 bg-blue-600 rounded-lg flex items-center justify-center font-black text-white italic">M</div>
            <span class="font-black text-xs uppercase tracking-tighter">MintCrest <span class="text-blue-500">Gold</span></span>
        </div>
        <div class="flex items-center gap-4">
            <button onclick="toggleTheme()" id="theme-btn" class="text-lg">🌙</button>
            <div id="top-bal" class="text-[9px] font-black bg-blue-600/10 text-blue-500 px-3 py-1 rounded-full border border-blue-500/20 italic">₨ 0</div>
        </div>
    </header>

    <main id="app-ui" class="p-4 space-y-6">

        <div id="p-home" class="page active-page space-y-6">
            <div class="bg-blue-600/5 border border-blue-500/10 p-2 rounded-xl overflow-hidden whitespace-nowrap">
                <p class="animate-marquee inline-block text-[8px] font-black uppercase text-blue-400 italic">
                    🔥 Limited Time Offer: Use Promo Code 'GOLD786' for 5% Bonus! | Minimum Deposit ₨ 200 | Trusted Node Active...
                </p>
            </div>

            <div class="glass p-5 rounded-[2.5rem] flex gap-2 items-center">
                <input type="text" id="promo-input" placeholder="Enter Promo Code" class="flex-1 !p-3">
                <button onclick="applyPromo()" class="btn-prime px-6 py-3 text-[10px] uppercase">Apply</button>
            </div>

            <div id="active-timer-box" class="hidden glass p-5 rounded-[2.5rem] border-l-4 border-green-500 bg-green-500/5">
                <p class="text-[8px] font-black uppercase opacity-60">Plan Active: <span id="active-plan-name">Starter</span></p>
                <div class="flex justify-between items-end mt-2">
                    <h3 id="countdown" class="text-2xl font-black italic tracking-widest text-green-500">00:00:00</h3>
                    <p class="text-[8px] font-bold opacity-40">AUTO-PROFIT NODE</p>
                </div>
            </div>

            <div class="flex gap-2">
                <button onclick="showPlans('normal')" id="tab-normal" class="flex-1 p-3 rounded-2xl bg-blue-600 text-white text-[10px] font-black uppercase shadow-lg">20 Standard Plans</button>
                <button onclick="showPlans('special')" id="tab-special" class="flex-1 p-3 rounded-2xl glass text-[10px] font-black uppercase opacity-50">5 Special Offers</button>
            </div>

            <div id="plans-grid" class="grid grid-cols-1 gap-4">
                </div>
        </div>

        <div id="p-about" class="page space-y-4">
            <h2 class="text-xl font-black uppercase italic text-blue-500">Company Identity</h2>
            <div class="glass p-6 rounded-[2.5rem] space-y-4 text-[11px] leading-relaxed opacity-80 font-bold">
                <p>MintCrest Gold is a premier digital asset management firm founded in 2026. We specialize in automated high-frequency trading and liquidity nodes.</p>
                <p><span class="text-blue-500">Registration:</span> UK Companies House #774829 (Certified)</p>
                <p><span class="text-blue-500">Mission:</span> To provide secure, transparent, and consistent daily yields for small to medium-scale investors.</p>
                <div class="h-[1px] bg-white/5 w-full"></div>
                <h4 class="text-blue-400 uppercase">Privacy Policy</h4>
                <p>Your data is encrypted using AES-256 bit protocols. We never share user identity with third parties. Withdrawals are processed through secure gateways only.</p>
            </div>
        </div>

        </main>

    <nav class="fixed bottom-0 w-full glass border-t border-[var(--border)] p-4 flex justify-around items-center rounded-t-[2.5rem] z-[4000]">
        <button onclick="changePage('home')" id="n-home" class="nav-active nav-icon flex flex-col items-center gap-1">🏠<span class="text-[7px] font-black">NODE</span></button>
        <button onclick="changePage('about')" id="n-about" class="nav-icon flex flex-col items-center gap-1">🏢<span class="text-[7px] font-black">LEGAL</span></button>
        <button onclick="changePage('wallet')" id="n-wallet" class="nav-icon flex flex-col items-center gap-1">📥<span class="text-[7px] font-black">DEPOSIT</span></button>
        <button onclick="changePage('withdraw')" id="n-withdraw" class="nav-icon flex flex-col items-center gap-1">📤<span class="text-[7px] font-black">PAYOUT</span></button>
    </nav>

    <script>
        // --- 1. THE REVOLUTIONARY PLAN DATA ---
        const NORMAL_PLANS = [];
        for(let i=1; i<=20; i++) {
            let price = 200 + (i-1)*500;
            let profit = (3 + (i*0.2)).toFixed(1); // Profit starts at 3%
            let days = 10 + i;
            NORMAL_PLANS.push({ name: `Standard v${i}`, price, profit, days, type: 'normal' });
        }

        const SPECIAL_PLANS = [
            { name: "Gold Flash", price: 5000, profit: "15", days: "No Limit", type: 'special' },
            { name: "Admin Choice", price: 10000, profit: "25", days: "No Limit", type: 'special' },
            { name: "Whale Node", price: 50000, profit: "40", days: "No Limit", type: 'special' },
            { name: "Elite Stake", price: 100000, profit: "60", days: "No Limit", type: 'special' },
            { name: "Prime Legacy", price: 500000, profit: "85", days: "No Limit", type: 'special' }
        ];

        // --- 2. THE LOGIC ---
        function showPlans(category) {
            const grid = document.getElementById('plans-grid');
            const data = category === 'normal' ? NORMAL_PLANS : SPECIAL_PLANS;
            
            // UI Toggle
            document.getElementById('tab-normal').className = category === 'normal' ? 'flex-1 p-3 rounded-2xl bg-blue-600 text-white text-[10px] font-black uppercase shadow-lg' : 'flex-1 p-3 rounded-2xl glass text-[10px] font-black uppercase opacity-50';
            document.getElementById('tab-special').className = category === 'special' ? 'flex-1 p-3 rounded-2xl bg-blue-600 text-white text-[10px] font-black uppercase shadow-lg' : 'flex-1 p-3 rounded-2xl glass text-[10px] font-black uppercase opacity-50';

            grid.innerHTML = '';
            data.forEach(p => {
                grid.innerHTML += `
                <div class="glass plan-card p-6 border-l-4 ${category==='special'?'border-yellow-500':'border-blue-500'}">
                    <div class="flex justify-between items-start">
                        <div>
                            <h3 class="text-sm font-black uppercase italic">${p.name}</h3>
                            <p class="text-[18px] font-black text-blue-500 mt-1">₨ ${p.price.toLocaleString()}</p>
                        </div>
                        <div class="text-right">
                            <span class="bg-blue-600/10 text-blue-500 px-3 py-1 rounded-full text-[9px] font-black">${p.profit}% DAILY</span>
                            ${p.days !== 'No Limit' ? `<p class="text-[8px] font-bold opacity-40 mt-2 uppercase tracking-widest">${p.days} DAYS TERM</p>` : ''}
                        </div>
                    </div>
                    <button onclick="buyPlan('${p.name}', ${p.price}, '${p.days}')" class="w-full mt-4 btn-prime p-3 text-[9px] uppercase tracking-widest">Activate Node ⚡</button>
                </div>`;
            });
        }

        // --- 3. COUNTDOWN & BUYING ---
        async function buyPlan(name, price, days) {
            if(user.balance < price) return alert("Sweetie, balance kam hai! ₨ 200 wala try karein. 😘");
            
            if(confirm(`Confirm Activation: ${name} for ₨ ${price}?`)) {
                // Subtract Balance
                await db.collection("users").doc(user.name).update({
                    balance: firebase.firestore.FieldValue.increment(-price),
                    activePlan: { name, price, expiry: Date.now() + (86400000 * (parseInt(days) || 365)) }
                });
                alert("Node Activated! Earnings started. 😘");
                startCountdown(Date.now() + 86400000); // 24hr timer till next profit
            }
        }

        function startCountdown(endTime) {
            document.getElementById('active-timer-box').classList.remove('hidden');
            const timer = setInterval(() => {
                const now = Date.now();
                const diff = endTime - now;
                if(diff <= 0) {
                    clearInterval(timer);
                    document.getElementById('countdown').innerText = "PROFIT READY!";
                    return;
                }
                const h = Math.floor(diff/3600000);
                const m = Math.floor((diff%3600000)/60000);
                const s = Math.floor((diff%60000)/1000);
                document.getElementById('countdown').innerText = `${h.toString().padStart(2,'0')}:${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`;
            }, 1000);
        }

        // --- 4. PROMO CODES ---
        async function applyPromo() {
            const code = document.getElementById('promo-input').value.toUpperCase();
            if(code === 'GOLD786') {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(50) });
                alert("Promo Applied! ₨ 50 added as bonus. 😘");
                document.getElementById('promo-input').value = "";
            } else {
                alert("Invalid or Expired Code, sweetie! 😘");
            }
        }

        // --- 5. THEME TOGGLE ---
        function toggleTheme() {
            const html = document.getElementById('main-html');
            const btn = document.getElementById('theme-btn');
            if(html.classList.contains('dark')) {
                html.classList.replace('dark', 'light');
                btn.innerText = "☀️";
            } else {
                html.classList.replace('light', 'dark');
                btn.innerText = "🌙";
            }
        }

        // Page Control
        function changePage(p) {
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page'));
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-icon').forEach(n => n.classList.remove('nav-active'));
            document.getElementById('n-'+p).classList.add('nav-active');
        }

        window.onload = () => {
            showPlans('normal');
            // Check for existing plan to show timer...
        }
    </script>
</body>
</html>
