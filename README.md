<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Premium Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(35px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-gradient { background: linear-gradient(135deg, #3b82f6, #1d4ed8); }
        .badge-pending { background: rgba(234, 179, 8, 0.1); color: #eab308; padding: 2px 6px; border-radius: 4px; font-size: 8px; font-weight: 800; text-transform: uppercase; }
        .badge-approved { background: rgba(34, 197, 94, 0.1); color: #22c55e; padding: 2px 6px; border-radius: 4px; font-size: 8px; font-weight: 800; text-transform: uppercase; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-5xl font-black italic mb-2 tracking-tighter cursor-pointer uppercase select-none">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-12">Authorized Digital Vault</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Agent Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 uppercase text-white focus:border-blue-500">
                <button onclick="login()" class="w-full btn-gradient py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95 shadow-lg">Access Vault</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mb-6">
                <div>
                    <p class="text-[8px] text-gray-500 uppercase font-black tracking-widest">Active Agent</p>
                    <h3 class="text-sm font-black text-blue-400 uppercase" id="dash-user">GUEST</h3>
                </div>
                <a href="https://chat.whatsapp.com/YOUR_LINK_HERE" class="bg-green-600/20 text-green-500 p-2 px-4 rounded-full text-[9px] font-black uppercase border border-green-500/20 flex items-center gap-2">
                    <span>Group</span> 📱
                </a>
            </div>
            
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-xl relative overflow-hidden">
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Current Balance</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                
                <div id="timer-box" class="mt-6 hidden">
                    <p class="text-[8px] text-gray-500 uppercase font-black mb-1">Next Profit Request In:</p>
                    <div id="countdown" class="text-xl font-black text-yellow-500 tracking-widest font-mono">00:00:00</div>
                </div>
                
                <div class="mt-6 flex gap-3">
                    <span id="v-tier" class="text-[8px] bg-white/5 text-gray-400 px-4 py-2 rounded-full font-bold uppercase border border-white/5 italic">No Active Fleet</span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2rem] text-center active:scale-95">
                    <span class="text-2xl">💰</span>
                    <span class="text-[9px] font-black block mt-2 uppercase tracking-widest">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2rem] text-center active:scale-95">
                    <span class="text-2xl">📤</span>
                    <span class="text-[9px] font-black block mt-2 uppercase tracking-widest text-red-400">Withdraw</span>
                </button>
                <button onclick="changePage('more')" class="glass p-8 rounded-[2rem] text-center active:scale-95">
                    <span class="text-2xl">🎡</span>
                    <span class="text-[9px] font-black block mt-2 uppercase tracking-widest text-yellow-500">Spin</span>
                </button>
                <button onclick="changePage('activity')" class="glass p-8 rounded-[2rem] text-center active:scale-95">
                    <span class="text-2xl">📋</span>
                    <span class="text-[9px] font-black block mt-2 uppercase tracking-widest text-blue-400">Ledger</span>
                </button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-xl tracking-widest">Global Fleets</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3rem] border-t-8 border-red-600 shadow-xl">
                <h3 class="text-red-500 font-black text-[12px] uppercase mb-6 italic text-center">Payout Request</h3>
                <input type="number" id="wd-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold outline-none border border-white/5">
                <input type="text" id="wd-method" placeholder="Acc Title & Number" class="w-full bg-white/5 p-4 rounded-xl mb-8 text-center font-bold uppercase outline-none border border-white/5">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-2xl font-black text-[10px] uppercase shadow-lg shadow-red-600/20 active:scale-95">Verify Withdrawal</button>
            </div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400">Vault History</h2>
            <div id="user-history" class="space-y-2 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6">
            <div class="glass p-10 rounded-[3rem] text-center border-t-4 border-yellow-500 shadow-xl">
                <h3 class="text-yellow-500 font-black text-[12px] uppercase mb-4 italic">Lucky Wheel</h3>
                <div id="wheel" class="text-6xl mb-6 transition-all duration-[4s] inline-block">🎡</div><br>
                <button onclick="spinNow()" class="bg-yellow-600 px-8 py-3 rounded-xl font-black text-[9px] uppercase shadow-lg active:scale-95">Spin Now</button>
            </div>
            <button onclick="logout()" class="w-full glass p-5 rounded-[2rem] text-red-500 font-black text-[10px] uppercase tracking-widest border border-red-500/10">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3rem] border-t-8 border-blue-600 text-center shadow-xl">
                <h3 class="font-black text-blue-500 mb-6 uppercase italic">Funding Terminal</h3>
                <div class="bg-white/5 p-4 rounded-xl mb-4 text-[9px] font-black uppercase border border-white/5">EP: 03379827882 | JC: 03705519562</div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold outline-none border border-white/5">
                <input type="text" id="dep-trx" placeholder="Transaction TID" class="w-full bg-white/5 p-4 rounded-xl mb-8 text-center font-bold uppercase outline-none border border-white/5">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase shadow-lg shadow-blue-600/20">Verify Funding</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] border-t border-white/5">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-2xl">📜</button>
        <button onclick="changePage('more')" id="n-more" class="text-2xl">⚙️</button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8"><h2 class="text-xl font-black text-blue-500 uppercase italic">Admin Console</h2><button onclick="closeAdmin()" class="bg-red-500 text-white px-4 py-2 rounded-xl text-[10px] font-black uppercase">Close</button></div>
        <h4 class="text-[10px] font-black text-yellow-500 uppercase mb-4 tracking-widest">Pending Verification</h4>
        <div id="adm-requests" class="space-y-3 mb-12"></div>
        <h4 class="text-[10px] font-black text-gray-500 uppercase mb-4 tracking-widest">User Registry</h4>
        <div id="adm-users-list" class="space-y-2 pb-24"></div>
    </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0; let timerInterval = null;

        const plans = [
            {id:"p1", n:"Starter Node", p:200, r:3}, {id:"p2", n:"Micro Fleet", p:500, r:3.5},
            {id:"p3", n:"Bronze Asset", p:1000, r:4}, {id:"p4", n:"Silver Vault", p:5000, r:5},
            {id:"p5", n:"👑 CROWN MASTER", p:25000, r:22}, {id:"p6", n:"⚡ FLASH NODE", p:1000, r:15}
        ];

        window.onload = () => { if(localStorage.getItem('mc_auth')) { document.getElementById('user-name').value = localStorage.getItem('mc_auth'); login(); } };

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_auth', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, lastSpin: 0, activePlanId: null, dailyRate: 0, lastRequestTime: Date.now(), time: Date.now() });
            
            db.collection("users").doc(name).onSnapshot(d => { 
                user = d.data(); 
                updateUI(); 
                startCountdown();
                checkAndSendProfitRequest(); 
            });
            
            db.collection("requests").where("user", "==", name).orderBy("time", "desc").limit(10).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(d => { const r = d.data(); list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center text-[8px] font-black uppercase"><div>${r.type}</div><div>₨ ${r.amount} <span class="badge-${r.status}">${r.status}</span></div></div>`; });
            });
            
            document.getElementById('dash-user').innerText = name;
            document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        // --- COUNTDOWN TIMER ---
        function startCountdown() {
            if(!user.activePlanId) return;
            document.getElementById('timer-box').classList.remove('hidden');
            if(timerInterval) clearInterval(timerInterval);

            timerInterval = setInterval(() => {
                const now = Date.now();
                const nextRequest = (user.lastRequestTime || 0) + (24 * 60 * 60 * 1000);
                const diff = nextRequest - now;

                if (diff <= 0) {
                    document.getElementById('countdown').innerText = "REQUEST READY! ⚡";
                    return;
                }

                const h = Math.floor(diff / (1000 * 60 * 60));
                const m = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));
                const s = Math.floor((diff % (1000 * 60)) / 1000);
                document.getElementById('countdown').innerText = `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
            }, 1000);
        }

        async function checkAndSendProfitRequest() {
            if(!user.activePlanId) return;
            const now = Date.now();
            if (now - (user.lastRequestTime || 0) >= 24 * 60 * 60 * 1000) {
                const profitAmt = Math.floor((user.dailyRate / 100) * user.lastPrice);
                await db.collection("requests").add({ user: user.name, amount: profitAmt, type: "DAILY PROFIT", status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ lastRequestTime: Date.now() });
                alert("Sweetie, your daily profit request has been sent to Admin!");
            }
        }

        async function buy(p, n, rate, id) {
            if(user.activePlanId) return alert("Sweetie, you already have an active plan! You can only buy one plan at a time.");
            if(user.balance < p) return alert("Insufficient balance, sweetie!");
            
            if(confirm("Activate "+n+"?")) {
                await db.collection("users").doc(user.name).update({
                    balance: user.balance - p, activePlanId: id, tier: n, dailyRate: rate, lastPrice: p, lastRequestTime: Date.now()
                });
                alert("Plan Activated! Timer Started.");
            }
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); l.innerHTML = '';
            plans.forEach(p => { 
                const isOwned = user && user.activePlanId === p.id;
                l.innerHTML += `<div onclick="${isOwned ? '' : `buy(${p.p},'${p.n}',${p.r},'${p.id}')`}" class="glass p-6 rounded-[2.5rem] flex justify-between items-center mb-3 border-l-8 ${isOwned ? 'border-green-500 opacity-60' : 'border-blue-500/20'} cursor-pointer">
                    <div>
                        <h4 class="font-black text-[10px] text-blue-400 uppercase">${p.n} ${isOwned ? '(ACTIVE)' : ''}</h4>
                        <p class="text-[7px] text-gray-500 uppercase font-black">${p.r}% Daily Returns</p>
                    </div>
                    <p class="font-black text-[12px]">₨ ${p.p.toLocaleString()}</p>
                </div>`;
            });
        }

        // --- STANDARD UTILS ---
        async function submitWithdraw() {
            const a = parseInt(document.getElementById('wd-amount').value), m = document.getElementById('wd-method').value;
            if(!a || a < 500) return alert("Min 500!"); if(user.balance < a) return alert("Low balance!");
            await db.collection("requests").add({ user: user.name, amount: a, details: m, type: "WITHDRAWAL", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            alert("Withdrawal Pending!"); changePage('activity');
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value;
            if(!a || !t) return alert("Fill all!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "DEPOSIT", status: "pending", time: Date.now() });
            alert("Deposit Submitted!"); changePage('activity');
        }

        function updateUI() { 
            document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString(); 
            document.getElementById('v-tier').innerText = user.tier || "No Active Fleet";
        }

        function spinNow() {
            if(Date.now() - (user.lastSpin || 0) < 86400000) return alert("Try tomorrow, sweetie!");
            document.getElementById('wheel').style.transform = `rotate(${Math.floor(4000+Math.random()*4000)}deg)`;
            setTimeout(async () => {
                const win = Math.floor(Math.random()*150)+50;
                await db.collection("requests").add({ user: user.name, amount: win, type: "SPIN WIN", status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ lastSpin: Date.now() });
                alert("You won Rs "+win+"! Admin will verify soon.");
            }, 4000);
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('n-'+p).classList.add('active-tab'); }
        function adminTap() { tapCount++; if(tapCount>=5) { if(prompt("Master Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        function syncAdmin() {
            db.collection("requests").where("status","==","pending").onSnapshot(snap => {
                const list = document.getElementById('adm-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 text-[9px] font-black uppercase mb-3 border-l-4 border-yellow-500 flex justify-between items-center"><div>${d.user}<br><span class="text-yellow-500">${d.type} - Rs ${d.amount}</span></div><div class="flex gap-2"><button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-4 py-2 rounded-xl">YES</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-red-600 px-4 py-2 rounded-xl">NO</button></div></div>`; });
            });
            db.collection("users").onSnapshot(snap => {
                const list = document.getElementById('adm-users-list'); list.innerHTML = '';
                snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-4 flex justify-between items-center text-[9px] font-black uppercase mb-2"><div>${u.name}<br>BAL: ${u.balance}</div><button onclick="deleteUser('${u.name}')" class="text-red-500">DELETE</button></div>`; });
            });
        }

        async function handle(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            if(act==='approved') {
                if(type !== "WITHDRAWAL") await ref.update({ balance: (doc.data().balance||0) + amt });
            } else {
                if(type === "WITHDRAWAL") await ref.update({ balance: (doc.data().balance||0) + amt });
            }
            await db.collection("requests").doc(id).update({ status: act }); alert("Action Finalized!");
        }

        async function deleteUser(name) { if(confirm("Delete user forever?")) await db.collection("users").doc(name).delete(); }
        function logout() { localStorage.removeItem('mc_auth'); location.reload(); }
    </script>
</body>
</html>
