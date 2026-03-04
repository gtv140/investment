<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Professional Edition</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(35px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .trading-pulse { height: 2px; background: #3b82f6; width: 0%; animation: pulse 3s infinite; box-shadow: 0 0 15px #3b82f6; }
        @keyframes pulse { 0% { width: 0%; opacity: 0; } 50% { width: 100%; opacity: 1; } 100% { width: 0%; opacity: 0; } }
        .badge-pending { background: rgba(234, 179, 8, 0.1); color: #eab308; padding: 2px 6px; border-radius: 4px; font-size: 8px; font-weight: 800; text-transform: uppercase; }
        .badge-approved { background: rgba(34, 197, 94, 0.1); color: #22c55e; padding: 2px 6px; border-radius: 4px; font-size: 8px; font-weight: 800; text-transform: uppercase; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="p-5 flex justify-between items-center border-b border-white/5 bg-black/50 backdrop-blur-md sticky top-0 z-[500]">
        <h1 onclick="adminTap()" class="text-xl font-black italic tracking-tighter uppercase select-none">MINT<span class="text-blue-500 text-2xl">CREST</span></h1>
        <div class="flex gap-2">
            <button onclick="toggleLang()" id="lang-btn" class="bg-white/5 border border-white/10 px-3 py-1.5 rounded-xl text-[9px] font-black uppercase">URDU</button>
            <div id="v-status" class="bg-blue-600/20 text-blue-400 border border-blue-500/20 px-3 py-1.5 rounded-xl text-[9px] font-black uppercase tracking-tighter italic">BRONZE</div>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 class="text-5xl font-black italic mb-2 tracking-tighter uppercase">SECURE<span class="text-blue-500 block text-3xl">VAULT</span></h1>
            <p class="text-[8px] text-gray-500 uppercase tracking-[0.5em] mb-12">Authorized Access Only</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Agent ID" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-4 uppercase text-white focus:border-blue-500 transition-all">
                <input type="text" id="ref-code" placeholder="REF CODE (OPTIONAL)" class="w-full bg-white/5 p-4 rounded-xl border border-white/5 outline-none text-center text-[9px] mb-8 uppercase text-gray-400">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95 shadow-xl shadow-blue-600/20 transition-transform">Authorize Login</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="mb-8">
                <div class="flex justify-between text-[7px] font-black uppercase text-blue-400 mb-2 tracking-widest">
                    <span id="txt-status">System Operational</span>
                    <span>Live Market Pulse</span>
                </div>
                <div class="w-full bg-white/5 h-[2px] rounded-full overflow-hidden">
                    <div class="trading-pulse"></div>
                </div>
            </div>

            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-2xl relative overflow-hidden">
                <p class="text-[10px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest" id="txt-bal">Available Balance</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                
                <div id="timer-box" class="mt-8 hidden bg-blue-600/5 p-5 rounded-3xl border border-blue-500/10">
                    <div class="flex justify-between items-center mb-1">
                        <p class="text-[8px] text-gray-500 uppercase font-black" id="txt-timer">Next Profit Signal In:</p>
                        <span class="animate-ping w-1 h-1 bg-yellow-500 rounded-full"></span>
                    </div>
                    <div id="countdown" class="text-2xl font-black text-yellow-500 tracking-[0.2em] font-mono italic">00:00:00</div>
                </div>

                <div class="mt-8 flex justify-between items-center">
                    <span id="v-tier" class="text-[8px] bg-white/5 text-gray-400 px-4 py-2 rounded-full font-bold uppercase border border-white/5 italic">No Active Fleet</span>
                    <button onclick="copyRef()" class="text-[9px] font-black text-blue-400 uppercase underline decoration-2 underline-offset-4 tracking-tighter">Copy Invite</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2rem] text-center active:scale-95">
                    <span class="text-2xl block mb-2">📥</span>
                    <span class="text-[10px] font-black uppercase tracking-widest" id="btn-dep">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2rem] text-center active:scale-95">
                    <span class="text-2xl block mb-2">📤</span>
                    <span class="text-[10px] font-black uppercase tracking-widest text-red-500" id="btn-wd">Withdraw</span>
                </button>
                <button onclick="changePage('invest')" class="glass p-8 rounded-[2rem] text-center active:scale-95">
                    <span class="text-2xl block mb-2">💹</span>
                    <span class="text-[10px] font-black uppercase tracking-widest text-blue-400" id="btn-inv">Fleets</span>
                </button>
                <button onclick="changePage('more')" class="glass p-8 rounded-[2rem] text-center active:scale-95">
                    <span class="text-2xl block mb-2">🎡</span>
                    <span class="text-[10px] font-black uppercase tracking-widest text-yellow-500" id="btn-spin">Spin</span>
                </button>
            </div>

            <a href="https://chat.whatsapp.com/YOUR_GROUP_LINK" class="mt-6 glass p-6 rounded-[2.5rem] flex justify-between items-center border border-green-500/20 active:scale-[0.98] transition-transform">
                <div class="flex items-center gap-4">
                    <div class="bg-green-600 p-2.5 rounded-2xl shadow-lg shadow-green-600/20">📱</div>
                    <div>
                        <h4 class="text-[11px] font-black uppercase tracking-tighter">Global Community</h4>
                        <p class="text-[8px] text-gray-500 font-bold uppercase">Signals & Support Updates</p>
                    </div>
                </div>
                <div class="text-[10px] font-black text-green-500 border-b-2 border-green-500/30">JOIN</div>
            </a>
        </div>

        <div id="p-invest" class="page p-6 pb-20">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-500 text-2xl tracking-widest">Active Fleets</h2>
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3rem] border-t-8 border-red-600 shadow-2xl">
                <h3 class="text-center font-black uppercase text-xs mb-8 italic tracking-widest text-red-500">Asset Extraction</h3>
                <input type="number" id="wd-amount" placeholder="Min Amount: 500" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/5 outline-none focus:border-red-500">
                <input type="text" id="wd-method" placeholder="Account Details" class="w-full bg-white/5 p-5 rounded-2xl mb-10 text-center uppercase font-bold border border-white/5 outline-none">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-2xl font-black text-[11px] uppercase shadow-xl shadow-red-600/20 active:scale-95">Send Request</button>
            </div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400 tracking-widest">Vault Ledger</h2>
            <div id="user-history" class="space-y-3 pb-20"></div>
        </div>

        <div id="p-more" class="page p-6 text-center">
            <div class="glass p-12 rounded-[3.5rem] shadow-2xl">
                <h3 class="text-yellow-500 font-black text-[12px] uppercase mb-8 italic tracking-widest">Quantum Spin</h3>
                <div id="wheel" class="text-8xl mb-10 transition-all duration-[4s] ease-out inline-block">🎡</div><br>
                <button onclick="spinNow()" class="bg-yellow-600 px-12 py-4 rounded-2xl font-black text-[10px] uppercase shadow-lg shadow-yellow-600/20 active:scale-95">Extract Luck</button>
            </div>
            <button onclick="logout()" class="mt-12 text-red-500 text-[10px] font-black uppercase tracking-[0.3em] opacity-40 hover:opacity-100 transition-opacity">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-blue-600 text-center shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase italic tracking-widest">Capital Injection</h3>
                <div class="bg-black/50 p-5 rounded-2xl mb-8 text-[10px] font-black border border-white/5 space-y-1">
                    <p class="text-blue-400">EASYPAISA: 03379827882</p>
                    <p class="text-blue-400">JAZZCASH: 03705519562</p>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/5">
                <input type="text" id="dep-trx" placeholder="Transaction TID" class="w-full bg-white/5 p-5 rounded-2xl mb-10 text-center font-bold uppercase border border-white/5">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase shadow-xl">Verify Capital</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[400] rounded-t-[4rem] border-t border-white/10">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-3xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-3xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-3xl">📜</button>
        <button onclick="changePage('more')" id="n-more" class="text-3xl">⚙️</button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Super Admin</h2>
            <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-5 py-2 rounded-xl text-[10px] font-black uppercase border border-red-500/20">Exit</button>
        </div>
        <div id="adm-requests" class="space-y-4 mb-20"></div>
    </div>

    <script>
        // FIREBASE CONFIG (Sahi wala)
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        
        let user = null, tapCount = 0, timerInt = null, lang = "en";

        const plans = [
            {id:"p1", n:"Starter Fleet", p:200, r:3}, 
            {id:"p2", n:"Silver Fleet", p:1000, r:4},
            {id:"p3", n:"Gold Master", p:5000, r:6}, 
            {id:"p4", n:"👑 CROWN ROYAL", p:25000, r:22},
            {id:"p5", n:"⚡ FLASH NODE", p:1000, r:15}
        ];

        window.onload = () => { if(localStorage.getItem('mc_auth')) { document.getElementById('user-name').value = localStorage.getItem('mc_auth'); login(); } };

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase();
            const refCode = document.getElementById('ref-code').value.trim().toUpperCase();
            if(!name) return;
            localStorage.setItem('mc_auth', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            
            if(!doc.exists) {
                await ref.set({ name: name, balance: 0, profit: 0, lastSpin: 0, activePlanId: null, dailyRate: 0, lastRequestTime: Date.now(), time: Date.now(), referredBy: refCode || "NONE" });
                if(refCode) await db.collection("requests").add({ user: refCode, amount: 0, type: "NEW REFERRAL: "+name, status: "approved", time: Date.now() });
            }
            
            db.collection("users").doc(name).onSnapshot(d => { 
                user = d.data(); 
                updateUI(); 
                startCountdown(); 
                checkAndSendProfitRequest(); 
            });
            
            document.getElementById('auth-ui').classList.add('hidden'); 
            document.getElementById('app-ui').classList.remove('hidden'); 
            document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
            syncHistory();
        }

        function updateUI() {
            document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
            document.getElementById('v-tier').innerText = user.tier || "No Active Fleet";
            const badge = document.getElementById('v-status');
            if(user.balance > 10000) { badge.innerText = "GOLD"; badge.className = "bg-yellow-500/20 text-yellow-500 border border-yellow-500/20 px-3 py-1.5 rounded-xl text-[9px] font-black"; }
            else if(user.balance > 5000) { badge.innerText = "SILVER"; badge.className = "bg-gray-400/20 text-gray-400 border border-gray-400/20 px-3 py-1.5 rounded-xl text-[9px] font-black"; }
        }

        function startCountdown() {
            if(!user.activePlanId) return;
            document.getElementById('timer-box').classList.remove('hidden');
            if(timerInt) clearInterval(timerInt);
            timerInt = setInterval(() => {
                const diff = (user.lastRequestTime + 86400000) - Date.now();
                if(diff <= 0) { document.getElementById('countdown').innerText = "SIGNAL READY! ⚡"; return; }
                const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000);
                document.getElementById('countdown').innerText = `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
            }, 1000);
        }

        async function checkAndSendProfitRequest() {
            if(!user.activePlanId) return;
            if(Date.now() - user.lastRequestTime >= 86400000) {
                const pAmt = Math.floor((user.dailyRate/100)*user.lastPrice);
                await db.collection("requests").add({ user: user.name, amount: pAmt, type: "DAILY PROFIT", status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ lastRequestTime: Date.now() });
                alert("Sweetie, your daily profit signal was sent to Admin!");
            }
        }

        async function buy(p, n, rate, id) {
            if(user.activePlanId) return alert("Sweetie, you can only manage ONE active fleet at a time!");
            if(user.balance < p) return alert("Insufficient Capital, sweetie!");
            if(confirm("Authorize "+n+" Activation?")) {
                await db.collection("users").doc(user.name).update({ balance: user.balance-p, activePlanId: id, tier: n, dailyRate: rate, lastPrice: p, lastRequestTime: Date.now() });
                alert("Fleet Authorized! Timer Started.");
            }
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); l.innerHTML = '';
            plans.forEach(p => { 
                const isOwned = user.activePlanId === p.id;
                l.innerHTML += `<div onclick="${isOwned ? '' : `buy(${p.p},'${p.n}',${p.r},'${p.id}')`}" class="glass p-8 rounded-[2.5rem] flex justify-between items-center ${isOwned ? 'border-blue-500 border-2' : 'border-white/5'}"><div><h4 class="font-black text-[10px] text-blue-400 uppercase tracking-tighter">${p.n} ${isOwned?'(ACTIVE)':''}</h4><p class="text-[8px] text-gray-500 font-bold uppercase">${p.r}% Daily Yield</p></div><p class="font-black text-sm italic">₨ ${p.p}</p></div>`;
            });
        }

        function toggleLang() {
            lang = lang === "en" ? "ur" : "en";
            document.getElementById('lang-btn').innerText = lang === "en" ? "URDU" : "ENGLISH";
            document.getElementById('txt-bal').innerText = lang === "en" ? "Available Balance" : "دستیاب بیلنس";
            document.getElementById('txt-status').innerText = lang === "en" ? "System Operational" : "سسٹم ٹھیک کام کر رہا ہے";
            document.getElementById('btn-dep').innerText = lang === "en" ? "Deposit" : "ڈیپازٹ";
            document.getElementById('btn-wd').innerText = lang === "en" ? "Withdraw" : "ودڈرال";
            document.getElementById('btn-inv').innerText = lang === "en" ? "Fleets" : "پلانز";
            document.getElementById('btn-spin').innerText = lang === "en" ? "Spin" : "اسپن";
        }

        function copyRef() {
            const link = `MintCrest Gold Login: Use Code [ ${user.name} ] for Bonus!`;
            navigator.clipboard.writeText(link); alert("Invite Code Copied, sweetie!");
        }

        async function submitWithdraw() { const a = parseInt(document.getElementById('wd-amount').value), m = document.getElementById('wd-method').value; if(!a || a < 500) return alert("Min 500"); if(user.balance < a) return alert("Low balance"); await db.collection("requests").add({ user: user.name, amount: a, details: m, type: "WITHDRAWAL", status: "pending", time: Date.now() }); await db.collection("users").doc(user.name).update({ balance: user.balance - a }); alert("Pending!"); changePage('home'); }
        async function submitDeposit() { const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value; if(!a || !t) return alert("Fill all"); await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "DEPOSIT", status: "pending", time: Date.now() }); alert("Submitted!"); changePage('home'); }
        function spinNow() { if(Date.now() - (user.lastSpin || 0) < 86400000) return alert("Try tomorrow"); document.getElementById('wheel').style.transform = `rotate(${Math.floor(4000+Math.random()*4000)}deg)`; setTimeout(async () => { const win = Math.floor(Math.random()*150)+50; await db.collection("requests").add({ user: user.name, amount: win, type: "SPIN WIN", status: "pending", time: Date.now() }); await db.collection("users").doc(user.name).update({ lastSpin: Date.now() }); alert("Won Rs "+win); }, 4000); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('n-'+p).classList.add('active-tab'); }
        function syncHistory() { db.collection("requests").where("user", "==", user.name).orderBy("time", "desc").onSnapshot(snap => { const list = document.getElementById('user-history'); list.innerHTML = ''; snap.forEach(d => { const r = d.data(); list.innerHTML += `<div class="glass p-5 rounded-2xl flex justify-between items-center text-[9px] font-black uppercase mb-1"><div>${r.type}</div><div>₨ ${r.amount} <span class="${r.status==='approved'?'text-green-500':'text-yellow-500'}">${r.status}</span></div></div>`; }); }); }
        function adminTap() { tapCount++; if(tapCount>=5) { if(prompt("Master Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function syncAdmin() { db.collection("requests").where("status","==","pending").onSnapshot(snap => { const list = document.getElementById('adm-requests'); list.innerHTML = ''; snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 text-[10px] font-black uppercase mb-3 border-l-4 border-yellow-500 flex justify-between items-center"><div>${d.user}<br>${d.type} - Rs ${d.amount}<br>REF: ${d.tid||d.details||'None'}</div><div class="flex gap-2"><button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-4 py-2 rounded-xl text-[8px]">YES</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-red-600 px-4 py-2 rounded-xl text-[8px]">NO</button></div></div>`; }); }); }
        async function handle(id, u, amt, act, type) { const ref = db.collection(\"users\").doc(u); const doc = await ref.get(); if(act==='approved') { if(type !== \"WITHDRAWAL\") await ref.update({ balance: (doc.data().balance||0) + amt }); } else { if(type === \"WITHDRAWAL\") await ref.update({ balance: (doc.data().balance||0) + amt }); } await db.collection(\"requests\").doc(id).update({ status: act }); }
        function logout() { localStorage.removeItem('mc_auth'); location.reload(); }
    </script>
</body>
</html>
