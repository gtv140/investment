<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Professional Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        .light { --bg: #f8fafc; --card: #ffffff; --text: #0f172a; --border: #e2e8f0; --accent: #2563eb; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); transition: 0.3s; padding-bottom: 100px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 16px; font-weight: 800; transition: 0.2s; }
        input, textarea, select { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 14px; width: 100%; outline: none; font-size: 13px; }
        .nav-active { color: var(--accent); transform: scale(1.1); opacity: 1 !important; }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; white-space: nowrap; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body>

    <div id="lock-screen" class="fixed inset-0 z-[1000000] bg-[var(--bg)] hidden flex flex-col items-center justify-center p-8 text-center">
        <div class="w-20 h-20 bg-red-500/20 rounded-full flex items-center justify-center mb-6 animate-pulse text-4xl">⚠️</div>
        <h1 id="lock-title" class="text-2xl font-black uppercase italic text-red-500 mb-2">Access Restricted</h1>
        <p id="lock-msg" class="text-[10px] font-bold opacity-60 uppercase tracking-widest leading-relaxed">System Optimization in Progress.</p>
    </div>

    <header id="main-header" class="hidden p-4 flex justify-between items-center sticky top-0 bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)] z-[5000]">
        <div class="flex items-center gap-2">
            <div onclick="adminTap()" class="w-9 h-9 bg-blue-600 rounded-xl flex items-center justify-center font-black text-white italic shadow-lg shadow-blue-500/20">M</div>
            <span class="font-black text-xs uppercase tracking-tighter">MintCrest <span class="text-blue-500">Gold</span></span>
        </div>
        <div class="flex items-center gap-3">
            <button onclick="toggleTheme()" class="w-8 h-8 glass rounded-full flex items-center justify-center text-sm">🌓</button>
            <div id="top-bal" class="text-[10px] font-black bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full border border-blue-500/20 italic">₨ 0</div>
            <button onclick="logout()" class="w-8 h-8 glass rounded-full flex items-center justify-center text-sm">🚪</button>
        </div>
    </header>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8 text-center">
        <div class="w-16 h-16 bg-blue-600 rounded-[1.5rem] mb-6 flex items-center justify-center text-4xl font-black text-white italic shadow-2xl">M</div>
        <h1 class="text-2xl font-black italic tracking-tighter mb-8 uppercase">Initialize Node</h1>
        <div class="w-full max-w-[300px] space-y-4">
            <input type="text" id="user-name" placeholder="Username" class="text-center font-bold uppercase">
            <input type="password" id="user-pass" placeholder="Password" class="text-center font-bold">
            <button onclick="login()" class="w-full p-4 btn-prime uppercase text-[10px] tracking-widest shadow-xl">Authorize Access</button>
        </div>
    </section>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="bg-blue-600/5 border border-blue-500/10 p-2 rounded-xl overflow-hidden">
                <p id="v-news" class="animate-marquee text-[8px] font-black uppercase text-blue-400 italic">Welcome! Nodes Active. Minimum Deposit ₨ 200.</p>
            </div>

            <div class="p-8 rounded-[2.5rem] bg-gradient-to-br from-blue-700 to-blue-900 text-white shadow-2xl relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-60 tracking-widest">Active Liquidity</p>
                <h2 class="text-4xl font-black tracking-tighter mt-1" id="v-bal">₨ 0.00</h2>
                <div class="mt-6 flex justify-between items-center bg-black/20 p-4 rounded-2xl backdrop-blur-md">
                    <span class="text-[8px] font-bold uppercase italic" id="countdown">Timer: --:--:--</span>
                    <button onclick="copyRef()" class="bg-white text-blue-800 px-4 py-2 rounded-xl text-[8px] font-black uppercase">Copy Invite</button>
                </div>
                <button onclick="claimDaily()" class="mt-4 w-full bg-white/10 p-3 rounded-xl text-[8px] font-black uppercase border border-white/10">🎁 Claim Daily Bonus</button>
            </div>

            <div class="glass p-5 rounded-[2rem] flex gap-2 items-center">
                <input type="text" id="promo-input" placeholder="Promo Code" class="flex-1 !p-3 font-bold uppercase">
                <button onclick="applyPromo()" class="btn-prime px-6 py-3 text-[9px] uppercase">Redeem</button>
            </div>

            <div id="plans-grid" class="grid grid-cols-1 gap-4 pb-20"></div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <div class="glass p-6 rounded-[2.5rem] space-y-4">
                <div class="p-4 bg-blue-600/10 border border-blue-500/20 rounded-2xl text-center">
                    <p class="text-[9px] font-black text-blue-500 uppercase">JazzCash / SadaPay</p>
                    <p class="text-lg font-black tracking-widest">03705519562</p>
                </div>
                <input type="number" id="dep-amt" placeholder="Amount (₨)">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                <button onclick="submitDep()" class="w-full p-4 btn-prime uppercase text-[10px] tracking-widest">Confirm Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] space-y-4">
                <input type="number" id="w-amt" placeholder="Withdrawal Amount">
                <select id="w-method">
                    <option value="JazzCash">JazzCash</option>
                    <option value="EasyPaisa">EasyPaisa</option>
                </select>
                <input type="text" id="w-acc" placeholder="Account Number">
                <button onclick="submitWith()" class="w-full p-4 btn-prime uppercase text-[10px] tracking-widest">Request Payout</button>
            </div>
        </div>

        <div id="p-history" class="page space-y-4">
            <h3 class="text-[10px] font-black uppercase opacity-40 italic text-center">Transaction Log</h3>
            <div id="history-list" class="space-y-3"></div>
        </div>

    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-4 flex justify-around items-center rounded-t-[2.5rem] z-[4000]">
        <button onclick="changePage('home')" id="n-home" class="nav-active flex flex-col items-center gap-1">🏠<span class="text-[7px] font-black">NODE</span></button>
        <button onclick="changePage('wallet')" id="n-wallet" class="opacity-40 flex flex-col items-center gap-1">📥<span class="text-[7px] font-black">FUND</span></button>
        <button onclick="changePage('withdraw')" id="n-withdraw" class="opacity-40 flex flex-col items-center gap-1">📤<span class="text-[7px] font-black">PAYOUT</span></button>
        <button onclick="changePage('history')" id="n-history" class="opacity-40 flex flex-col items-center gap-1">📜<span class="text-[7px] font-black">LOGS</span></button>
    </nav>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-500 uppercase">Admin Node</h2>
            <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>
        <div class="glass p-6 rounded-[2rem] space-y-4 mb-6">
            <h4 class="text-[10px] font-black uppercase text-blue-400">Add New Plan</h4>
            <input type="text" id="new-p-name" placeholder="Plan Name">
            <input type="number" id="new-p-price" placeholder="Price">
            <input type="number" id="new-p-profit" placeholder="Daily %">
            <button onclick="adminAddPlan()" class="w-full btn-prime p-4 text-[10px] uppercase">Create Plan</button>
        </div>
        <div id="adm-requests-list" class="space-y-4 pb-20"></div>
    </div>

    <script>
        // Firebase Config
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
        let user = null; let tapCount = 0;

        // Session & Auth
        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase();
            const p = document.getElementById('user-pass').value.trim();
            if(!n || !p) return alert("Fill credentials!");

            const ref = db.collection("users").doc(n);
            const d = await ref.get();
            const invitedBy = new URLSearchParams(window.location.search).get('ref') || "Direct";

            if(!d.exists) {
                await ref.set({ name: n, password: p, balance: 0, time: Date.now(), invitedBy: invitedBy, banned: false });
                alert("Account Created!");
            } else {
                if(d.data().password !== p) return alert("Wrong Password!");
                if(d.data().banned) return alert("Banned!");
            }
            localStorage.setItem('mc_user', n);
            enterApp(n);
        }

        function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('main-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            sync(n);
        }

        function logout() { localStorage.clear(); location.reload(); }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                const bal = (user.balance || 0).toLocaleString();
                document.getElementById('v-bal').innerText = "₨ " + bal;
                document.getElementById('top-bal').innerText = "₨ " + bal;
                if(user.purchaseTime) startTimer(user.purchaseTime);
            });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").limit(10).onSnapshot(s => {
                const list = document.getElementById('history-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center text-[10px] font-black border-l-4 ${x.status==='approved'?'border-green-500':'border-yellow-500'}">
                        <div><p class="uppercase">${x.type}</p></div>
                        <div class="text-right"><p>₨ ${x.amount}</p><p class="text-[7px] uppercase italic">${x.status}</p></div>
                    </div>`;
                });
            });
            renderPlans();
        }

        function renderPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const grid = document.getElementById('plans-grid'); grid.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    grid.innerHTML += `
                    <div class="glass p-6 rounded-[2rem] border-l-4 border-blue-500">
                        <div class="flex justify-between items-start">
                            <div><p class="text-[10px] font-black uppercase">${p.name}</p><h3 class="text-xl font-black">₨ ${p.price.toLocaleString()}</h3></div>
                            <span class="bg-blue-600/10 text-blue-500 px-3 py-1 rounded-full text-[8px] font-black">${p.profit}% DAILY</span>
                        </div>
                        <button onclick="buyNode('${doc.id}', ${p.price})" class="w-full mt-4 btn-prime p-3 text-[9px] uppercase">Activate Node</button>
                    </div>`;
                });
            });
        }

        async function buyNode(id, price) {
            if(user.balance < price) return alert("Insufficient Balance!");
            await db.collection("users").doc(user.name).update({
                balance: firebase.firestore.FieldValue.increment(-price),
                purchaseTime: Date.now()
            });
            alert("Mining Started!");
        }

        function startTimer(st) {
            setInterval(() => {
                const diff = (st + 86400000) - Date.now();
                if(diff <= 0) document.getElementById('countdown').innerText = "PROFIT READY!";
                else {
                    const h = Math.floor(diff/3600000); const m = Math.floor((diff%3600000)/60000); const s = Math.floor((diff%60000)/1000);
                    document.getElementById('countdown').innerText = `${h}:${m}:${s}`;
                }
            }, 1000);
        }

        async function submitDep() {
            const a = document.getElementById('dep-amt').value; const t = document.getElementById('dep-tid').value;
            if(!a || !t) return alert("Fill fields!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "deposit", status: "pending", time: Date.now() });
            alert("Submitted!");
        }

        async function submitWith() {
            const a = parseInt(document.getElementById('w-amt').value);
            if(a < 500 || a > user.balance) return alert("Invalid Amount!");
            await db.collection("requests").add({ user: user.name, amount: a, method: document.getElementById('w-method').value, account: document.getElementById('w-acc').value, type: "withdrawal", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            alert("Withdrawal Requested!");
        }

        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        
        async function adminAddPlan() {
            const n = document.getElementById('new-p-name').value;
            const p = parseInt(document.getElementById('new-p-price').value);
            const pr = parseFloat(document.getElementById('new-p-profit').value);
            await db.collection("plans").add({ name: n, price: p, profit: pr });
            alert("Plan Added!");
        }

        function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-requests-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-xl border-l-4 border-blue-500 flex justify-between">
                        <div class="text-[10px] font-bold uppercase"><p>${r.user} | ₨ ${r.amount}</p><p class="opacity-40">${r.type}</p></div>
                        <button onclick="appv('${doc.id}', '${r.user}', ${r.amount})" class="bg-blue-600 px-4 py-2 rounded-xl text-[8px] font-black">APPROVE</button>
                    </div>`;
                });
            });
        }
        async function appv(id, u, a) { await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) }); await db.collection("requests").doc(id).update({ status: 'approved' }); }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('nav-active')); document.getElementById('n-'+p).classList.add('nav-active'); }
        function toggleTheme() { document.getElementById('main-html').classList.toggle('dark'); document.getElementById('main-html').classList.toggle('light'); }
        function copyRef() { const link = window.location.origin + window.location.pathname + "?ref=" + user.name; navigator.clipboard.writeText(link); alert("Copied!"); }

        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user')); }
    </script>
</body>
</html>
