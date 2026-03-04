<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Ultra Modern</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@300;400;500;600;700&display=swap');
        
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Space Grotesk', sans-serif; }
        body { background: radial-gradient(circle at top right, #0a192f, #000); color: #fff; height: 100vh; overflow: hidden; }

        .glass-card { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(25px); border: 1px solid rgba(255, 255, 255, 0.08); border-radius: 2rem; }
        .blue-glow { box-shadow: 0 0 20px rgba(59, 130, 246, 0.3); }
        .gold-glow { box-shadow: 0 0 20px rgba(234, 179, 8, 0.3); }
        
        .page-container { flex: 1; overflow-y: auto; scroll-behavior: smooth; padding-bottom: 120px; }
        .page { display: none; padding: 20px; animation: slideUp 0.4s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }

        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }

        .nav-btn { position: relative; transition: 0.3s; }
        .active-nav { color: #3b82f6; transform: translateY(-5px); }
        .active-nav::after { content: ''; position: absolute; bottom: -8px; left: 50%; transform: translateX(-50%); width: 5px; height: 5px; background: #3b82f6; border-radius: 50%; }

        .stat-badge { background: linear-gradient(135deg, rgba(59, 130, 246, 0.2), rgba(37, 99, 235, 0.05)); border: 1px solid rgba(59, 130, 246, 0.3); }
        input { background: rgba(255,255,255,0.05) !important; border: 1px solid rgba(255,255,255,0.1) !important; color: white !important; }
        input:focus { border-color: #3b82f6 !important; outline: none; }
    </style>
</head>
<body class="flex flex-col">

    <header class="p-6 flex justify-between items-center z-50">
        <div>
            <p class="text-[10px] text-gray-400 font-bold tracking-[0.3em] uppercase">Security Level 4</p>
            <h1 onclick="adminTap()" class="text-2xl font-bold tracking-tighter uppercase cursor-pointer">MINT<span class="text-blue-500">CREST</span></h1>
        </div>
        <div id="v-status" class="stat-badge px-4 py-2 rounded-2xl text-[10px] font-bold text-blue-400 uppercase italic">Verifying Node...</div>
    </header>

    <section id="auth-ui" class="fixed inset-0 z-[3000] bg-black flex items-center justify-center p-8">
        <div class="w-full max-w-sm">
            <div class="text-center mb-10">
                <div class="w-20 h-20 bg-blue-600 rounded-full mx-auto mb-4 blue-glow flex items-center justify-center text-3xl">🔱</div>
                <h2 class="text-3xl font-bold tracking-tighter">SECURE LOGIN</h2>
            </div>
            <div class="glass-card p-8 shadow-2xl">
                <input type="text" id="user-name" placeholder="Agent Username" class="w-full p-5 rounded-2xl mb-4 text-center font-bold uppercase">
                <button onclick="login()" class="w-full bg-blue-600 hover:bg-blue-500 p-5 rounded-2xl font-bold text-[12px] uppercase tracking-widest shadow-xl transition-all active:scale-95">Access Terminal</button>
            </div>
        </div>
    </section>

    <div id="app-ui" class="hidden page-container">
        
        <div id="p-home" class="page active-page">
            <div class="glass-card p-8 mb-6 blue-glow border-t-4 border-blue-500">
                <div class="flex justify-between items-start mb-4">
                    <p class="text-[11px] text-blue-400 font-bold uppercase tracking-widest">Total Valuation</p>
                    <span class="text-xs bg-green-500/20 text-green-400 px-2 py-1 rounded-lg">Active</span>
                </div>
                <h2 class="text-5xl font-bold tracking-tighter mb-6" id="v-bal">₨ 0</h2>
                
                <div id="timer-box" class="hidden stat-badge p-5 rounded-3xl flex justify-between items-center">
                    <div>
                        <p class="text-[9px] text-gray-400 uppercase font-bold">Mining Signal</p>
                        <div id="countdown" class="text-xl font-bold text-yellow-500 font-mono">00:00:00</div>
                    </div>
                    <div class="w-10 h-10 rounded-full bg-yellow-500/10 flex items-center justify-center text-xl">⚡</div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <div onclick="changePage('wallet')" class="glass-card p-6 text-center cursor-pointer active:scale-95 transition-all">
                    <div class="text-3xl mb-2">💎</div>
                    <p class="text-[10px] font-bold uppercase text-gray-400">Deposit</p>
                </div>
                <div onclick="changePage('withdraw')" class="glass-card p-6 text-center cursor-pointer active:scale-95 transition-all">
                    <div class="text-3xl mb-2">🚀</div>
                    <p class="text-[10px] font-bold uppercase text-gray-400">Withdraw</p>
                </div>
            </div>

            <div onclick="changePage('invest')" class="glass-card p-6 flex items-center justify-between border-l-4 border-blue-500 cursor-pointer active:scale-95">
                <div>
                    <h4 class="font-bold text-sm uppercase">Global Fleet Nodes</h4>
                    <p class="text-[10px] text-gray-500 uppercase">Explore 25 Investment Tiers</p>
                </div>
                <div class="text-2xl">🔥</div>
            </div>
        </div>

        <div id="p-invest" class="page">
            <p class="text-center text-[10px] text-blue-400 font-bold uppercase tracking-[0.3em] mb-2">Marketplace</p>
            <h2 class="text-center font-bold text-3xl mb-8 tracking-tighter">FLEET NODES</h2>
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-activity" class="page">
            <h2 class="text-2xl font-bold mb-6 tracking-tighter">TRANSACTION LEDGER</h2>
            <div id="user-history" class="space-y-3"></div>
        </div>

        <div id="p-wallet" class="page">
            <div class="glass-card p-8 text-center border-t-4 border-blue-600">
                <h3 class="font-bold text-blue-400 mb-6 uppercase tracking-widest">Inward Funding</h3>
                <div class="grid grid-cols-1 gap-2 mb-8">
                    <div class="stat-badge p-4 rounded-2xl">
                        <p class="text-[8px] text-gray-500 font-bold uppercase">Option A (EasyPaisa)</p>
                        <p class="font-bold text-blue-300">03379827882</p>
                    </div>
                    <div class="stat-badge p-4 rounded-2xl">
                        <p class="text-[8px] text-gray-500 font-bold uppercase">Option B (JazzCash)</p>
                        <p class="font-bold text-blue-300">03705519562</p>
                    </div>
                </div>
                <input type="number" id="dep-amount" placeholder="PKR Amount" class="w-full p-5 rounded-2xl mb-4 text-center font-bold">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full p-5 rounded-2xl mb-8 text-center font-bold uppercase">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 p-5 rounded-2xl font-bold uppercase tracking-widest active:scale-95 shadow-lg">Confirm Protocol</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <div class="glass-card p-8 text-center border-t-4 border-red-600">
                <h3 class="font-bold text-red-500 mb-8 uppercase tracking-widest">Asset Extraction</h3>
                <input type="number" id="wd-amount" placeholder="Min Amount 500" class="w-full p-5 rounded-2xl mb-4 text-center font-bold">
                <input type="text" id="wd-method" placeholder="Account No & Details" class="w-full p-5 rounded-2xl mb-10 text-center font-bold uppercase">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 p-5 rounded-2xl font-bold uppercase active:scale-95 shadow-xl shadow-red-600/30">Authorize Withdrawal</button>
            </div>
        </div>

        <div id="p-more" class="page text-center">
            <div class="glass-card p-12 mb-8 gold-glow">
                <div id="wheel" class="text-7xl mb-8 transition-all duration-[4s] inline-block">🛸</div><br>
                <button onclick="spinNow()" class="bg-yellow-600 px-10 py-4 rounded-2xl font-bold text-[10px] uppercase shadow-lg">Daily Bonus Probe</button>
            </div>
            <button onclick="logout()" class="text-red-500 text-[10px] font-bold uppercase opacity-40 hover:opacity-100 transition-all">Disconnect Session</button>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass-card mx-6 my-6 p-4 flex justify-around items-center fixed bottom-0 left-0 right-0 z-[2500] border-white/10 shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="nav-btn active-nav text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn text-2xl">📊</button>
        <button onclick="changePage('activity')" id="n-activity" class="nav-btn text-2xl">📑</button>
        <button onclick="changePage('more')" id="n-more" class="nav-btn text-2xl">⚙️</button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-[#000] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-2xl font-bold text-blue-500">SYSTEM CORE</h2>
            <button onclick="closeAdmin()" class="bg-red-500/20 text-red-500 px-6 py-2 rounded-2xl font-bold text-xs uppercase">Exit</button>
        </div>

        <div class="space-y-6">
            <div class="glass-card p-6 border-l-4 border-blue-500">
                <h3 class="text-xs font-bold text-gray-400 mb-4 uppercase">User Management</h3>
                <input type="text" id="adm-user" placeholder="User Name" class="w-full p-4 rounded-xl mb-2 text-xs uppercase">
                <input type="number" id="adm-val" placeholder="Amount" class="w-full p-4 rounded-xl mb-4 text-xs">
                <div class="grid grid-cols-2 gap-2">
                    <button onclick="manualEdit('add')" class="bg-green-600 py-3 rounded-xl text-[10px] font-bold uppercase">Add Balance</button>
                    <button onclick="manualEdit('rem')" class="bg-red-600 py-3 rounded-xl text-[10px] font-bold uppercase">Set Penalty</button>
                </div>
                <button onclick="resetUserPlan()" class="w-full mt-2 bg-yellow-600 py-3 rounded-xl text-[10px] font-bold uppercase">Reset User Plan</button>
            </div>

            <div class="glass-card p-6 border-l-4 border-purple-500">
                <h3 class="text-xs font-bold text-gray-400 mb-4 uppercase">Announcement Engine</h3>
                <input type="text" id="adm-note" placeholder="Write System Message..." class="w-full p-4 rounded-xl mb-3 text-xs">
                <button onclick="broadcastNote()" class="w-full bg-purple-600 py-3 rounded-xl text-[10px] font-bold uppercase">Update Broadcast</button>
            </div>

            <h3 class="text-sm font-bold text-yellow-500 uppercase px-2">Approval Queue</h3>
            <div id="adm-requests" class="space-y-3"></div>
            
            <h3 class="text-sm font-bold text-blue-500 uppercase px-2">Live Agent Database</h3>
            <div id="adm-users-list" class="space-y-2 pb-20"></div>
        </div>
    </div>

    <script>
        // CONFIGURATION
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        
        let user = null, tapCount = 0, timerInt = null;
        const plans = []; for(let i=1; i<=25; i++) { plans.push({ id:`p${i}`, n: i<=5?"Bronze":i<=15?"Silver":i<=20?"Gold":"Nebula", p: i*1000, r: 4+(i*0.4) }); }

        window.onload = () => { 
            const saved = localStorage.getItem('mc_auth');
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            syncGlobalNote();
        };

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase();
            if(!name) return;
            try {
                const ref = db.collection("users").doc(name);
                const doc = await ref.get();
                if(!doc.exists) { await ref.set({ name: name, balance: 0, activePlanId: null, lastRequestTime: Date.now(), time: Date.now() }); }
                localStorage.setItem('mc_auth', name);
                ref.onSnapshot(d => { 
                    user = d.data(); 
                    if(user) { 
                        document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                        document.getElementById('v-status').innerText = user.balance > 10000 ? "🔱 ELITE AGENT" : "🛰️ STANDARD AGENT";
                        startCountdown(); renderPlans(); checkAutoProfit(); 
                    }
                });
                document.getElementById('auth-ui').classList.add('hidden'); 
                document.getElementById('app-ui').classList.remove('hidden'); 
                document.getElementById('bottom-nav').classList.remove('hidden');
                syncHistory();
            } catch(e) { alert("Core Connection Failed."); }
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); if(!l) return; l.innerHTML = '';
            plans.forEach(p => {
                const isOwned = user.activePlanId === p.id;
                l.innerHTML += `<div onclick="${isOwned?'':`buy(${p.p},'${p.n}',${p.r},'${p.id}')`}" class="glass-card p-6 flex justify-between items-center transition-all ${isOwned?'border-blue-500 border-2 bg-blue-600/10 shadow-lg':''}">
                    <div class="text-left"><h4 class="font-bold text-[11px] text-blue-400 uppercase tracking-widest">${p.n} ${p.id.substring(1)}</h4><p class="text-[9px] text-gray-500 font-bold">${p.r}% PROFIT / 24H</p></div>
                    <div class="text-right"><span class="text-lg font-bold">₨ ${p.p.toLocaleString()}</span></div>
                </div>`;
            });
        }

        async function buy(p, n, rate, id) {
            if(user.activePlanId) return alert("Sweetie, one node at a time!");
            if(user.balance < p) return alert("Insufficient Credit!");
            if(confirm("Engage "+n+" Node?")) {
                await db.collection("users").doc(user.name).update({ balance: user.balance-p, activePlanId: id, dailyRate: rate, lastPrice: p, lastRequestTime: Date.now() });
            }
        }

        function startCountdown() {
            if(!user.activePlanId) return;
            document.getElementById('timer-box').classList.remove('hidden');
            if(timerInt) clearInterval(timerInt);
            timerInt = setInterval(() => {
                const diff = (user.lastRequestTime + 86400000) - Date.now();
                const cd = document.getElementById('countdown');
                if(diff <= 0) { cd.innerText = "READY TO COLLECT! ⚡"; cd.classList.add('text-green-500'); return; }
                const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000);
                cd.innerText = `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
            }, 1000);
        }

        async function checkAutoProfit() {
            if(!user.activePlanId || Date.now() - user.lastRequestTime < 86400000) return;
            const amt = Math.floor((user.dailyRate/100)*user.lastPrice);
            await db.collection("requests").add({ user: user.name, amount: amt, type: "NODE PROFIT", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastRequestTime: Date.now() });
        }

        function syncHistory() {
            db.collection("requests").where("user", "==", user.name).orderBy("time", "desc").limit(20).onSnapshot(snap => {
                const list = document.getElementById('user-history'); if(!list) return;
                list.innerHTML = '';
                snap.forEach(d => { const r = d.data(); list.innerHTML += `<div class="glass-card p-4 flex justify-between items-center text-[10px] font-bold uppercase mb-1"><div>${r.type}<br><span class="opacity-30 text-[7px]">${new Date(r.time).toLocaleTimeString()}</span></div><div class="text-right">₨ ${r.amount}<br><span class="${r.status==='approved'?'text-green-500':'text-yellow-500'} text-[8px]">${r.status}</span></div></div>`; });
            });
        }

        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); 
            document.getElementById('p-'+p).classList.add('active-page'); 
            document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-nav')); 
            document.getElementById('n-'+p).classList.add('active-nav'); 
        }

        function adminTap() { tapCount++; if(tapCount>=5) { if(prompt("Security Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        
        // ADMIN CORE FUNCTIONS
        function syncAdmin() {
            db.collection("requests").where("status","==","pending").onSnapshot(snap => {
                const l = document.getElementById('adm-requests'); l.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); l.innerHTML += `<div class="glass-card p-4 text-[10px] font-bold mb-2 flex justify-between items-center border-l-4 border-yellow-500"><div>${d.user}<br>${d.type} - ₨ ${d.amount}<br>${d.tid||d.details||''}</div><button onclick="hndl('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-4 py-2 rounded-xl text-[8px] uppercase">Verify</button></div>`; });
            });
            db.collection("users").onSnapshot(snap => {
                const l = document.getElementById('adm-users-list'); l.innerHTML = '';
                snap.forEach(doc => { const u = doc.data(); l.innerHTML += `<div class="glass-card p-4 flex justify-between items-center text-[9px] font-bold uppercase"><div>${u.name} (₨ ${u.balance||0})</div><button onclick="db.collection('users').doc('${u.name}').delete()" class="text-red-500">❌</button></div>`; });
            });
        }
        async function hndl(id, u, amt, act, type) { const ref = db.collection("users").doc(u); const d = (await ref.get()).data(); if(type!=="WITHDRAW") await ref.update({ balance: (d.balance||0) + amt }); await db.collection("requests").doc(id).update({ status: act }); }
        async function manualEdit(act) { const u = document.getElementById('adm-user').value.toUpperCase(), v = parseInt(document.getElementById('adm-val').value); const ref = db.collection("users").doc(u); const d = (await ref.get()).data(); await ref.update({ balance: act==='add'?(d.balance+v):(d.balance-v) }); alert("Done!"); }
        async function resetUserPlan() { const u = document.getElementById('adm-user').value.toUpperCase(); await db.collection("users").doc(u).update({ activePlanId: null }); alert("Plan Cleared!"); }
        function broadcastNote() { db.collection("settings").doc("broadcast").set({ msg: document.getElementById('adm-note').value }); alert("Pushed!"); }
        function syncGlobalNote() { db.collection("settings").doc("broadcast").onSnapshot(d => { const bar = document.getElementById('global-note'); if(d.exists && d.data().msg) { bar.classList.remove('hidden'); document.getElementById('note-text').innerText = d.data().msg; } else { bar.classList.add('hidden'); } }); }

        async function submitWithdraw() { const a = parseInt(document.getElementById('wd-amount').value), m = document.getElementById('wd-method').value; if(a < 500 || user.balance < a) return alert("Security: Balance Check Failed"); await db.collection("requests").add({ user: user.name, amount: a, details: m, type: "WITHDRAW", status: "pending", time: Date.now() }); await db.collection("users").doc(user.name).update({ balance: user.balance - a }); alert("Withdrawal Protocol Initiated!"); changePage('home'); }
        async function submitDeposit() { const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value; await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "DEPOSIT", status: "pending", time: Date.now() }); alert("Data Received for Verification!"); changePage('home'); }
        function logout() { localStorage.removeItem('mc_auth'); location.reload(); }
        function spinNow() { document.getElementById('wheel').style.transform = `rotate(${Math.floor(2000+Math.random()*2000)}deg)`; }
    </script>
</body>
</html
