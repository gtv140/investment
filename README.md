<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Ultimate Node v7.0</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 110px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); backdrop-filter: blur(12px); }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 20px; font-weight: 800; transition: 0.3s; border: none; cursor: pointer; }
        .btn-prime:active { transform: scale(0.95); }
        input, select { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 16px; border-radius: 16px; width: 100%; outline: none; font-size: 13px; margin-bottom: 10px; }
        .animate-marquee { display: inline-block; animation: marquee 20s linear infinite; white-space: nowrap; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .admin-card { border: 1px solid rgba(59, 130, 246, 0.3); background: rgba(59, 130, 246, 0.05); }
    </style>
</head>
<body>

    <div id="lock-screen" class="fixed inset-0 z-[1000000] bg-[var(--bg)] hidden flex flex-col items-center justify-center p-8 text-center">
        <div class="w-20 h-20 bg-blue-600/20 rounded-full flex items-center justify-center mb-6 animate-pulse text-4xl">🔑</div>
        <h1 id="lock-title" class="text-2xl font-black uppercase text-blue-500 mb-2">Restricted</h1>
        <p id="lock-msg" class="text-[10px] font-bold opacity-60 uppercase tracking-widest leading-relaxed">Security Check Active.</p>
    </div>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)]">
        <div class="p-4 flex justify-between items-center">
            <div class="flex items-center gap-2">
                <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg cursor-pointer">M</div>
                <div>
                    <h1 class="text-[12px] font-black uppercase tracking-tighter">MintCrest <span class="text-blue-500">Gold</span></h1>
                    <p id="display-user" class="text-[8px] font-bold text-blue-400 uppercase tracking-widest italic">@Initializing</p>
                </div>
            </div>
            <div id="top-bal" class="text-[11px] font-black bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full border border-blue-500/20 italic">₨ 0</div>
        </div>
    </header>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8 text-center">
        <div class="w-20 h-20 bg-blue-600 rounded-[2rem] mb-6 flex items-center justify-center text-5xl font-black text-white italic shadow-2xl">M</div>
        <h1 class="text-3xl font-black italic mb-8 uppercase text-white">Elite Access</h1>
        <div class="w-full max-w-[300px] space-y-4">
            <input type="text" id="user-login-name" placeholder="USERNAME" class="text-center font-bold uppercase">
            <input type="text" id="ref-by" placeholder="INVITE CODE (OPTIONAL)" class="text-center font-bold uppercase text-[10px]">
            <button onclick="login()" class="w-full p-4 btn-prime uppercase text-[11px] tracking-widest">Connect Node</button>
        </div>
    </section>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="glass p-2.5 rounded-2xl overflow-hidden border-blue-500/10">
                <p id="v-news" class="animate-marquee text-[10px] font-black uppercase text-blue-400 italic">Welcome sweetie to the most trusted node. Promo system & Daily bonus active!</p>
            </div>

            <div class="p-8 rounded-[3.5rem] bg-gradient-to-br from-blue-600 to-indigo-900 text-white shadow-2xl relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-60">Mining Balance</p>
                <h2 class="text-5xl font-black tracking-tighter my-2" id="v-bal">₨ 0.00</h2>
                
                <div id="active-timer-box" class="hidden mt-4 bg-black/30 p-4 rounded-2xl border border-white/10 flex justify-between items-center">
                    <span class="text-[9px] font-black uppercase italic">Next Profit Cycle:</span>
                    <span id="p-timer" class="text-xs font-black tracking-widest text-blue-300">00:00:00</span>
                </div>

                <div class="mt-6 flex gap-3">
                   <button onclick="claimDaily()" id="daily-btn" class="flex-1 bg-white/10 backdrop-blur-md p-3 rounded-xl border border-white/10 text-[9px] font-black uppercase">🎁 Daily Bonus</button>
                   <button onclick="logout()" class="flex-1 bg-red-500/10 backdrop-blur-md p-3 rounded-xl border border-red-500/20 text-[9px] font-black uppercase text-red-400">Logout</button>
                </div>
            </div>

            <div class="glass p-6 rounded-[2.5rem] border-blue-500/20">
                <h3 class="text-[10px] font-black uppercase text-blue-500 mb-3">Promo & Referral</h3>
                <div class="flex gap-2 mb-4">
                    <input type="text" id="promo-input" placeholder="ENTER CODE" class="mb-0 text-center uppercase font-black text-[10px]">
                    <button onclick="applyPromo()" class="btn-prime px-6 text-[10px] uppercase">Apply</button>
                </div>
                <div class="p-4 bg-blue-600/5 rounded-2xl text-center border border-blue-500/10">
                    <p class="text-[7px] font-black opacity-50 uppercase mb-1">Your Invite Link ID</p>
                    <p id="my-ref-id" class="text-lg font-black tracking-[0.3em] text-blue-400 italic">-----</p>
                </div>
            </div>

            <h3 class="text-[11px] font-black uppercase text-blue-500 px-2 italic">Active Mining Nodes</h3>
            <div id="plans-grid" class="grid grid-cols-1 gap-4 pb-20"></div>
        </div>

        <div id="p-history" class="page space-y-6">
            <h3 class="text-xl font-black text-center uppercase text-blue-500 italic">Activity Logs</h3>
            <div id="user-logs" class="space-y-3 pb-20"></div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] space-y-4">
                <h3 class="text-center font-black uppercase text-blue-500">Add Liquidity</h3>
                <p class="text-[9px] text-center opacity-50">EasyPaisa / JazzCash: 03379827882</p>
                <input type="number" id="dep-amt" placeholder="Amount (₨)">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                <button onclick="sendDeposit()" class="w-full p-4 btn-prime text-[11px] uppercase">Verify Deposit</button>
            </div>
            <div class="glass p-8 rounded-[3rem] space-y-4">
                <h3 class="text-center font-black uppercase text-red-500">Withdraw Funds</h3>
                <input type="number" id="wit-amt" placeholder="Amount (Min 500)">
                <input type="text" id="wit-acc" placeholder="Account Number & Name">
                <button onclick="sendWithdraw()" class="w-full p-4 bg-red-600/20 text-red-500 rounded-2xl text-[11px] font-black uppercase border border-red-500/20">Extract Cash</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-5 flex justify-around items-center rounded-t-[3.5rem] z-[4000]">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1">🏠<span class="text-[7px] font-black uppercase">Home</span></button>
        <button onclick="changePage('wallet')" class="flex flex-col items-center gap-1">💸<span class="text-[7px] font-black uppercase">Wallet</span></button>
        <button onclick="changePage('history')" class="flex flex-col items-center gap-1">📜<span class="text-[7px] font-black uppercase">Logs</span></button>
        <button onclick="adminTap()" class="flex flex-col items-center gap-1">🛠️<span class="text-[7px] font-black uppercase">Admin</span></button>
    </nav>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-8 border-b border-blue-500/20 pb-4">
            <h2 class="text-2xl font-black text-blue-500 uppercase italic">God Control</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-6 py-2 rounded-xl text-[10px] font-black">CLOSE</button>
        </div>
        
        <div class="grid grid-cols-1 gap-6">
            <div class="admin-card p-6 rounded-3xl space-y-4">
                <h4 class="text-[10px] font-black uppercase text-blue-400">System Architect</h4>
                <input type="number" id="adm-daily-amt" placeholder="Daily Bonus Amt">
                <input type="number" id="adm-ref-bonus" placeholder="Referral Bonus Amt">
                <button onclick="updateSystem()" class="w-full p-3 btn-prime text-[9px] uppercase">Update Settings</button>
                <button onclick="toggleMaintenance()" id="m-btn" class="w-full p-3 bg-blue-600/10 text-blue-400 rounded-xl text-[9px] font-black uppercase border border-blue-500/20">Maintenance: OFF</button>
            </div>

            <div class="admin-card p-6 rounded-3xl space-y-4">
                <h4 class="text-[10px] font-black uppercase text-blue-400">Promo Creator</h4>
                <div class="flex gap-2">
                    <input type="text" id="adm-p-code" placeholder="CODE" class="mb-0">
                    <input type="number" id="adm-p-reward" placeholder="₨" class="mb-0">
                </div>
                <button onclick="adminAddPromo()" class="w-full p-3 btn-prime text-[9px] uppercase">Deploy Code</button>
                <div id="adm-promo-list" class="space-y-2 max-h-40 overflow-y-auto"></div>
            </div>

            <div class="admin-card p-6 rounded-3xl space-y-4">
                <h4 class="text-[10px] font-black uppercase text-blue-400 italic">User God-View</h4>
                <div id="adm-user-list" class="space-y-3"></div>
            </div>

            <div class="admin-card p-6 rounded-3xl mb-24 space-y-4">
                <h4 class="text-[10px] font-black uppercase text-yellow-500">Pending Approvals</h4>
                <div id="adm-req-list" class="space-y-4"></div>
            </div>
        </div>
    </div>

    <script>
        // Firebase Configuration
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
        let user = null; let tapCount = 0; let maintenance = false;

        async function login() {
            const n = document.getElementById('user-login-name').value.trim().toLowerCase();
            const rb = document.getElementById('ref-by').value.trim().toLowerCase();
            if(!n) return alert("Sweetie, enter username!");
            localStorage.setItem('mc_user', n);
            enterApp(n, rb);
        }

        async function enterApp(n, rb) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('main-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-user').innerText = "@" + n.toUpperCase();
            document.getElementById('my-ref-id').innerText = n.toUpperCase();

            const userRef = db.collection("users").doc(n);
            const d = await userRef.get();
            if(!d.exists) {
                await userRef.set({ name: n, balance: 0, banned: false, referredBy: rb || null, lastDaily: 0 });
            }

            userRef.onSnapshot(doc => {
                user = doc.data();
                if(user.banned) showLock("ACCESS DENIED", "You have been banned sweetie! 😘");
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                if(user.purchaseTime) startCountdown(user.purchaseTime);
                checkDailyStatus();
            });

            syncPlans();
            syncLogs(n);
            
            // Sync Settings
            db.collection("settings").doc("system").onSnapshot(d => {
                if(d.exists) {
                    maintenance = d.data().maintenance;
                    if(maintenance && localStorage.getItem('is_admin') !== 'true') showLock("SYSTEM UPGRADE", "Wait sweetie, optimizing servers...");
                    document.getElementById('m-btn').innerText = "Maintenance: " + (maintenance?"ON":"OFF");
                }
            });
        }

        function checkDailyStatus() {
            const today = new Date().toDateString();
            const last = user.lastDaily ? new Date(user.lastDaily).toDateString() : "";
            const btn = document.getElementById('daily-btn');
            if(today === last) { btn.innerText = "✅ Claimed"; btn.disabled = true; btn.style.opacity = "0.5"; }
        }

        async function claimDaily() {
            const s = await db.collection("settings").doc("system").get();
            const r = s.data().dailyBonus || 10;
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(r), lastDaily: Date.now() });
            await addLog(user.name, `Daily Reward: ₨ ${r}`, 'profit');
            alert(`Sweetie, ₨ ${r} bonus claimed! 😘`);
        }

        async function applyPromo() {
            const code = document.getElementById('promo-input').value.trim().toUpperCase();
            const ref = db.collection("promos").doc(code);
            const d = await ref.get();
            if(!d.exists) return alert("Invalid Code!");
            const used = user.usedPromos || [];
            if(used.includes(code)) return alert("Already Used!");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(d.data().reward), usedPromos: firebase.firestore.FieldValue.arrayUnion(code) });
            await addLog(user.name, `Promo Applied: ${code} (+₨ ${d.data().reward})`, 'finance');
            alert("Promo Success!");
        }

        function startCountdown(st) {
            document.getElementById('active-timer-box').classList.remove('hidden');
            const timerEl = document.getElementById('p-timer');
            setInterval(() => {
                const diff = (st + 86400000) - Date.now();
                if(diff <= 0) { timerEl.innerText = "PROFIT READY"; timerEl.classList.add("text-green-400"); }
                else {
                    const h = Math.floor(diff/3600000); const m = Math.floor((diff%3600000)/60000); const s = Math.floor((diff%60000)/1000);
                    timerEl.innerText = `${h}:${m}:${s}`;
                }
            }, 1000);
        }

        // --- GOD MODE CORE ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Key:")==="mint786") { localStorage.setItem('is_admin','true'); document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        async function syncAdm() {
            // Promo List
            db.collection("promos").onSnapshot(s => {
                const list = document.getElementById('adm-promo-list'); list.innerHTML = '';
                s.forEach(doc => list.innerHTML += `<div class="glass p-2 flex justify-between text-[8px] uppercase font-black"><span>${doc.id} (₨ ${doc.data().reward})</span><button onclick="deletePromo('${doc.id}')" class="text-red-500">DEL</button></div>`);
            });
            // Request List
            db.collection("requests").where("status","==","pending").onSnapshot(s => {
                const list = document.getElementById('adm-req-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-2xl border-l-4 border-yellow-500 flex justify-between items-center">
                        <div><p class="text-[10px] font-black uppercase">${r.user}</p><h4 class="text-xl font-black">₨ ${r.amount}</h4><p class="text-[7px] opacity-40">${r.type} | ${r.tid || r.account}</p></div>
                        <button onclick="approveReq('${doc.id}', '${r.user}', ${r.amount}, '${r.type}')" class="bg-blue-600 px-4 py-2 rounded-xl text-[8px] font-black uppercase">Approve</button>
                    </div>`;
                });
            });
            // User God View
            db.collection("users").onSnapshot(s => {
                const list = document.getElementById('adm-user-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const u = doc.data();
                    list.innerHTML += `<div class="glass p-3 rounded-2xl flex justify-between items-center text-[9px] font-black uppercase border-blue-500/10">
                        <div><p>${u.name}</p><p class="text-blue-500">₨ ${u.balance}</p></div>
                        <div class="flex gap-2"><button onclick="editBal('${u.name}')" class="text-blue-400">EDIT</button><button onclick="toggleBan('${u.name}', ${u.banned})" class="${u.banned?'text-green-500':'text-red-500'}">${u.banned?'UNBAN':'BAN'}</button></div>
                    </div>`;
                });
            });
        }

        async function approveReq(id, u, a, type) {
            const batch = db.batch();
            if(type === 'Deposit') {
                batch.update(db.collection("users").doc(u), { balance: firebase.firestore.FieldValue.increment(a) });
                // Check Referral
                const uDoc = await db.collection("users").doc(u).get();
                if(uDoc.data().referredBy && !uDoc.data().refBonusPaid) {
                    const sys = await db.collection("settings").doc("system").get();
                    const bonus = sys.data().refBonus || 50;
                    batch.update(db.collection("users").doc(uDoc.data().referredBy), { balance: firebase.firestore.FieldValue.increment(bonus) });
                    batch.update(db.collection("users").doc(u), { refBonusPaid: true });
                    batch.set(db.collection("logs").doc(), { user: uDoc.data().referredBy, msg: `Ref Bonus (Friend Dep): ₨ ${bonus}`, time: Date.now(), type: 'profit' });
                }
            }
            batch.update(db.collection("requests").doc(id), { status: 'approved' });
            await batch.commit();
            alert("Transaction Success!");
        }

        async function updateSystem() {
            const d = parseInt(document.getElementById('adm-daily-amt').value);
            const r = parseInt(document.getElementById('adm-ref-bonus').value);
            await db.collection("settings").doc("system").set({ dailyBonus: d || 10, refBonus: r || 50 }, { merge: true });
            alert("System Config Updated!");
        }

        async function adminAddPromo() {
            const c = document.getElementById('adm-p-code').value.toUpperCase();
            const r = parseInt(document.getElementById('adm-p-reward').value);
            if(c && r) await db.collection("promos").doc(c).set({ reward: r });
            alert("Promo Deployed!");
        }

        async function deletePromo(id) { await db.collection("promos").doc(id).delete(); }
        async function toggleBan(u, b) { await db.collection("users").doc(u).update({ banned: !b }); }
        async function editBal(u) { const a = prompt("Set balance for " + u); if(a) await db.collection("users").doc(u).update({ balance: parseInt(a) }); }
        async function toggleMaintenance() { await db.collection("settings").doc("system").set({ maintenance: !maintenance }, { merge: true }); }

        // --- UTILS ---
        async function sendDeposit() {
            const a = document.getElementById('dep-amt').value; const t = document.getElementById('dep-tid').value;
            if(a && t) await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, status: 'pending', type: 'Deposit', time: Date.now() });
            alert("Sent for approval!");
        }
        async function sendWithdraw() {
            const a = parseInt(document.getElementById('wit-amt').value);
            if(a < 500 || a > user.balance) return alert("Invalid Amount!");
            await db.collection("requests").add({ user: user.name, amount: a, account: document.getElementById('wit-acc').value, status: 'pending', type: 'Withdrawal', time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            alert("Withdrawal Initiated!");
        }
        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    g.innerHTML += `<div class="glass p-6 rounded-[2.5rem] border-l-4 border-blue-500">
                        <div class="flex justify-between"><div><p class="text-[9px] uppercase font-black opacity-40">${p.name}</p><h4 class="text-xl font-black">₨ ${p.price}</h4></div><span class="text-blue-500 text-[9px] font-black">${p.profit}% DAILY</span></div>
                        <button onclick="buyNode('${doc.id}', ${p.price})" class="w-full mt-4 btn-prime p-4 text-[10px] uppercase">Activate</button>
                    </div>`;
                });
            });
        }
        async function buyNode(id, pr) {
            if(user.balance < pr) return alert("No Liquidity!");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-pr), purchaseTime: Date.now() });
            await addLog(user.name, `Node Active: ₨ ${pr}`, 'buy');
            alert("Mining Started!");
        }
        async function addLog(u, m, t) { await db.collection("logs").add({ user: u, msg: m, type: t, time: Date.now() }); }
        function syncLogs(n) {
            db.collection("logs").where("user","==","n").orderBy("time","desc").onSnapshot(s => {
                const l = document.getElementById('user-logs'); l.innerHTML = '';
                s.forEach(doc => { const d = doc.data(); l.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between border-l-2 border-blue-500 text-[9px] font-black uppercase"><p>${d.msg}</p></div>`; });
            });
        }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function showLock(t, m) { const s = document.getElementById('lock-screen'); s.classList.remove('hidden'); document.getElementById('lock-title').innerText = t; document.getElementById('lock-msg').innerText = m; }
        function logout() { localStorage.clear(); location.reload(); }
        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user'), ""); }
    </script>
</body>
</html>
