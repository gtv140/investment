<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Ultimate Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(40px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: slideUp 0.4s cubic-bezier(0.4, 0, 0.2, 1) forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        .badge-pending { background: rgba(234, 179, 8, 0.1); color: #eab308; padding: 4px 8px; border-radius: 6px; font-size: 8px; font-weight: 900; }
        .badge-approved { background: rgba(34, 197, 94, 0.1); color: #22c55e; padding: 4px 8px; border-radius: 6px; font-size: 8px; font-weight: 900; }
        .badge-rejected { background: rgba(239, 68, 68, 0.1); color: #ef4444; padding: 4px 8px; border-radius: 6px; font-size: 8px; font-weight: 900; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; white-space: nowrap; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="ticker-bar" class="bg-blue-600/10 border-b border-blue-500/20 py-2 overflow-hidden">
        <div class="animate-marquee text-[8px] font-black uppercase tracking-widest text-blue-400 italic">
            📢 <span id="ticker-msg">Welcome to MintCrest Gold - Secure your assets with 256-bit encryption - Contact Support for VIP nodes</span>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-6xl font-black italic mb-2 tracking-tighter cursor-pointer uppercase select-none">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.5em] mb-12 font-bold">The Gold Standard of Assets</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Agent Username" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-black mb-5 uppercase text-white focus:border-blue-500 transition-all">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95 shadow-xl shadow-blue-600/20">Authorize Session</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mb-6 px-2">
                <div>
                    <p class="text-[7px] text-gray-500 uppercase font-black tracking-widest">Global Identity</p>
                    <h3 class="text-md font-black text-blue-400 uppercase" id="dash-user">GUEST_AGENT</h3>
                </div>
            </div>
            
            <div class="glass p-12 rounded-[3.5rem] mb-8 border-l-[10px] border-blue-600 shadow-2xl relative overflow-hidden">
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-[0.2em]">Liquid Balance</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div class="mt-8 flex items-center gap-4">
                    <span class="text-[8px] bg-white/5 text-gray-400 px-5 py-2 rounded-full font-black uppercase border border-white/5">Profit: <span id="v-profit" class="text-green-400">₨ 0</span></span>
                    <span id="v-tier" class="text-[8px] bg-blue-500/10 text-blue-400 px-5 py-2 rounded-full font-black uppercase border border-blue-500/20 italic">No Node</span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 border-b-2 border-blue-500/20">
                    <span class="text-2xl">⚡</span>
                    <span class="text-[9px] font-black block mt-2 uppercase">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 border-b-2 border-red-500/20">
                    <span class="text-2xl">💎</span>
                    <span class="text-[9px] font-black block mt-2 uppercase text-red-400">Withdraw</span>
                </button>
                <button onclick="changePage('more')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95">
                    <span class="text-2xl">🎡</span>
                    <span class="text-[9px] font-black block mt-2 uppercase text-yellow-500">Lucky Spin</span>
                </button>
                <button onclick="changePage('promo')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95">
                    <span class="text-2xl">🎫</span>
                    <span class="text-[9px] font-black block mt-2 uppercase text-purple-400">Promo Code</span>
                </button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-500 text-xl tracking-widest">Active Fleets</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-12"></div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 shadow-2xl">
                <h3 class="text-red-500 font-black text-sm uppercase mb-8 italic text-center tracking-widest">Capital Extraction</h3>
                <input type="number" id="wd-amount" placeholder="Amount (Min 500)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold outline-none border border-white/5 text-white">
                <input type="text" id="wd-method" placeholder="Account Name & No" class="w-full bg-white/5 p-5 rounded-2xl mb-10 text-center font-bold uppercase outline-none border border-white/5 text-white">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[10px] uppercase shadow-xl shadow-red-600/30 active:scale-95 transition-all">Request Payout</button>
            </div>
        </div>

        <div id="p-promo" class="page p-6">
            <div class="glass p-12 rounded-[3.5rem] border-t-8 border-purple-600 text-center shadow-2xl">
                <h3 class="text-purple-500 font-black mb-8 uppercase italic tracking-[0.2em]">Code Injector</h3>
                <input type="text" id="promo-input" placeholder="VOUCHER CODE" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center font-black text-purple-400 border border-white/5 outline-none uppercase tracking-widest">
                <button onclick="submitPromo()" class="w-full bg-purple-600 py-6 rounded-2xl font-black text-[10px] uppercase shadow-xl shadow-purple-600/30">Verify Voucher</button>
            </div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400 tracking-widest">Vault Ledger</h2>
            <div id="user-history" class="space-y-3 pb-12"></div>
        </div>

        <div id="p-firm" class="page p-6 space-y-6 pb-24">
            <div class="glass p-8 rounded-[2.5rem] border-l-4 border-blue-500">
                <h4 class="text-blue-500 font-black uppercase text-[10px] mb-4 tracking-widest italic">About MintCrest Gold</h4>
                <p class="text-[10px] text-gray-400 leading-relaxed font-bold uppercase italic">Global leaders in high-yield node asset protection. Our mission is to democratize institutional-grade trading fleets for retail capital. Established 2026.</p>
            </div>
            <div class="glass p-8 rounded-[2.5rem] border-l-4 border-blue-500">
                <h4 class="text-blue-500 font-black uppercase text-[10px] mb-4 tracking-widest italic">Security Protocol</h4>
                <p class="text-[10px] text-gray-400 leading-relaxed font-bold uppercase italic">1. AES-256 Multi-Layer Encryption.<br>2. Decentralized request logging.<br>3. Instant withdrawal node validation.<br>4. Zero data exposure policy.</p>
            </div>
        </div>

        <div id="p-more" class="page p-6 space-y-8">
            <div class="glass p-12 rounded-[3.5rem] text-center border-t-4 border-yellow-500 shadow-2xl">
                <h3 class="text-yellow-500 font-black text-sm uppercase mb-6 italic tracking-widest">Lucky Spin</h3>
                <div id="wheel" class="text-7xl mb-10 transition-all duration-[4s] inline-block filter drop-shadow-2xl">🎡</div><br>
                <button onclick="spinNow()" class="bg-yellow-600 px-10 py-4 rounded-2xl font-black text-[10px] uppercase shadow-xl shadow-yellow-600/30 active:scale-95">Spin Now</button>
            </div>
            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-red-500 font-black text-[11px] uppercase tracking-[0.3em] border-red-500/20 shadow-xl">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-blue-600 shadow-2xl text-center">
                <h3 class="font-black text-blue-500 mb-8 uppercase italic tracking-widest">Capital Funding</h3>
                <div class="bg-white/5 p-6 rounded-2xl mb-8 text-[10px] font-black uppercase border border-white/5 leading-relaxed">
                    <span class="text-gray-500">EASYPAISA:</span> <span class="text-white">03379827882</span><br>
                    <span class="text-gray-500">JAZZCASH:</span> <span class="text-white">03705519562</span>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/5 outline-none">
                <input type="text" id="dep-trx" placeholder="Transaction TID" class="w-full bg-white/5 p-5 rounded-2xl mb-10 text-center font-bold uppercase border border-white/5 outline-none">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[10px] uppercase shadow-xl shadow-blue-600/30">Verify Deposit</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] border-t border-white/10">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-2xl filter grayscale hover:grayscale-0 transition-all">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl filter grayscale">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-2xl filter grayscale">📜</button>
        <button onclick="changePage('firm')" id="n-firm" class="text-2xl filter grayscale">🏢</button>
        <button onclick="changePage('more')" id="n-more" class="text-2xl filter grayscale">⚙️</button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-2xl font-black text-blue-500 uppercase italic tracking-tighter">Super Admin</h2>
            <button onclick="closeAdmin()" class="bg-red-500/20 text-red-500 px-6 py-3 rounded-2xl text-[10px] font-black uppercase">Close</button>
        </div>
        
        <div class="glass p-6 rounded-[2rem] mb-10 border-t-2 border-yellow-500/30">
            <h4 class="text-[10px] font-black text-yellow-500 uppercase mb-4 tracking-widest italic">Global Announcement</h4>
            <input type="text" id="adm-ticker-input" placeholder="Enter Ticker Message..." class="w-full bg-black/40 p-4 rounded-xl text-[10px] font-bold text-white mb-4 border border-white/10 outline-none">
            <button onclick="updateTicker()" class="w-full bg-yellow-600 py-4 rounded-xl text-[10px] font-black uppercase">Update Broadcast</button>
        </div>

        <h4 class="text-[10px] font-black text-blue-400 uppercase mb-4 tracking-widest italic">Action Queue</h4>
        <div id="adm-requests" class="space-y-4 mb-12"></div>

        <h4 class="text-[10px] font-black text-gray-400 uppercase mb-4 tracking-widest italic">User Management</h4>
        <div class="glass p-6 rounded-[2.5rem] mb-10 border-t-2 border-blue-500/30">
            <input type="text" id="adm-edit-user" placeholder="Exact Username" class="w-full bg-black/40 p-4 rounded-xl mb-4 text-[10px] font-black text-white uppercase border border-white/10">
            <div class="grid grid-cols-2 gap-4 mb-6">
                <div>
                    <label class="text-[7px] text-gray-500 font-black block mb-2 ml-1">SET BALANCE</label>
                    <input type="number" id="adm-edit-bal" class="w-full bg-black/40 p-4 rounded-xl text-[10px] font-black border border-white/10">
                </div>
                <div>
                    <label class="text-[7px] text-gray-500 font-black block mb-2 ml-1">SET PROFIT</label>
                    <input type="number" id="adm-edit-prof" class="w-full bg-black/40 p-4 rounded-xl text-[10px] font-black border border-white/10">
                </div>
            </div>
            <button onclick="manualSave()" class="w-full bg-blue-600 py-4 rounded-xl text-[10px] font-black uppercase">Commit User Overwrite</button>
        </div>

        <h4 class="text-[10px] font-black text-gray-600 uppercase mb-4 tracking-widest italic">Agent Registry</h4>
        <div id="adm-users-list" class="space-y-3 pb-24"></div>
    </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            {n:"Micro Node 1", p:200, r:3, d:30}, {n:"Micro Node 2", p:500, r:3.2, d:30}, {n:"Bronze Fleet", p:1000, r:3.5, d:45},
            {n:"Silver Node I", p:3000, r:4, d:45}, {n:"Silver Node II", p:5000, r:4.5, d:60}, {n:"Gold Master X", p:10000, r:5.5, d:60},
            {n:"Gold Master Pro", p:25000, r:7, d:90}, {n:"Platinum Fleet", p:50000, r:9, d:120}, {n:"Royal Asset", p:100000, r:12, d:150},
            {n:"👑 CROWN KING", p:25000, r:22, d:30}, {n:"⚡ FLASH NODE", p:1000, r:15, d:7}, {n:"VIP ALPHA", p:200000, r:18, d:365}
        ];

        window.onload = () => {
            const saved = localStorage.getItem('mc_agent_auth');
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            syncTicker();
        };

        function syncTicker() {
            db.collection("app_meta").doc("ticker").onSnapshot(doc => {
                if(doc.exists) document.getElementById('ticker-msg').innerText = doc.data().text;
            });
        }

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_agent_auth', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, lastSpin: 0, tier: "None", time: Date.now() });
            
            db.collection("users").doc(name).onSnapshot(d => { user = d.data(); updateUI(); });
            db.collection("requests").where("user", "==", name).orderBy("time", "desc").onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(d => { const r = d.data(); list.innerHTML += `<div class="glass p-5 rounded-2xl flex justify-between items-center text-[9px] font-black uppercase"><div class="opacity-70">${r.type}<br><span class="text-[7px] opacity-40">${new Date(r.time).toLocaleDateString()}</span></div><div class="text-right">₨ ${r.amount}<br><span class="badge-${r.status}">${r.status}</span></div></div>`; });
            });
            
            document.getElementById('dash-user').innerText = name;
            document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        async function submitWithdraw() {
            const a = parseInt(document.getElementById('wd-amount').value), m = document.getElementById('wd-method').value.trim();
            if(!a || !m || a < 500) return alert("Sweetie, Minimum withdraw Rs 500!");
            if(user.balance < a) return alert("Low funds, Sweetie!");
            await db.collection("requests").add({ user: user.name, amount: a, details: m, type: "WITHDRAWAL", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            alert("Withdrawal Logged! Awaiting node verification."); changePage('activity');
        }

        async function submitPromo() {
            const c = document.getElementById('promo-input').value.trim().toUpperCase(); if(!c) return;
            await db.collection("requests").add({ user: user.name, promo: c, type: "PROMO CODE", amount: 0, status: "pending", time: Date.now() });
            alert("Code submitted, Sweetie!"); document.getElementById('promo-input').value = '';
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value;
            if(!a || !t) return alert("Please fill all details!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "DEPOSIT", status: "pending", time: Date.now() });
            alert("Deposit Request Sent!"); changePage('activity');
        }

        function spinNow() {
            if(Date.now() - (user.lastSpin || 0) < 86400000) return alert("Come back in 24h, Sweetie!");
            const deg = 4000 + Math.random() * 4000;
            document.getElementById('wheel').style.transform = `rotate(${deg}deg)`;
            setTimeout(async () => {
                const win = Math.floor(Math.random()*150)+50;
                alert("Jackpot! You won Rs "+win+"! Admin will verify.");
                await db.collection("requests").add({ user: user.name, amount: win, type: "SPIN REWARD", status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ lastSpin: Date.now() });
            }, 4000);
        }

        function updateUI() { 
            document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString(); 
            document.getElementById('v-profit').innerText = "₨ " + (user.profit||0).toLocaleString(); 
            document.getElementById('v-tier').innerText = user.tier || "No Node";
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); l.innerHTML = '';
            plans.forEach(p => { l.innerHTML += `<div onclick="buy(${p.p},'${p.n}')" class="glass p-6 rounded-[2.5rem] flex justify-between items-center mb-3 active:scale-95 cursor-pointer border-l-8 border-blue-500/20"><div><h4 class="font-black text-[11px] text-blue-400 uppercase tracking-tighter">${p.n}</h4><p class="text-[8px] text-gray-500 uppercase font-black tracking-widest">${p.r}% ROI • ${p.d} Days</p></div><p class="font-black text-[13px] tracking-tighter">₨ ${p.p.toLocaleString()}</p></div>`; });
        }

        async function buy(p, n) { if(user.balance < p) return alert("Top up your vault, Sweetie!"); if(confirm("Activate "+n+"?")) { await db.collection("users").doc(user.name).update({ balance: user.balance-p, tier: n }); alert("Fleet Initialized!"); } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('n-'+p).classList.add('active-tab'); }
        function adminTap() { tapCount++; if(tapCount>=5) { if(prompt("Security Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        async function updateTicker() { const t = document.getElementById('adm-ticker-input').value.trim(); if(!t) return; await db.collection("app_meta").doc("ticker").set({ text: t }); alert("Broadcast Updated!"); }

        function syncAdmin() {
            db.collection("requests").where("status","==","pending").onSnapshot(snap => {
                const list = document.getElementById('adm-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 text-[9px] font-black uppercase mb-3 border-l-4 border-yellow-500 flex justify-between items-center"><div>${d.user}<br><span class="text-yellow-500">${d.type} - Rs ${d.amount}</span><br>REF: ${d.tid||d.promo||d.details||'None'}</div><div class="flex gap-2"><button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-4 py-2 rounded-xl">YES</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-red-600 px-4 py-2 rounded-xl">NO</button></div></div>`; });
            });
            db.collection("users").onSnapshot(snap => {
                const list = document.getElementById('adm-users-list'); list.innerHTML = '';
                snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-4 flex justify-between items-center text-[9px] font-black uppercase mb-2"><div>${u.name}<br><span class="text-blue-400">BAL: ${u.balance}</span></div><div class="flex gap-2"><button onclick="loadToEdit('${u.name}',${u.balance},${u.profit})" class="bg-blue-600 px-3 py-2 rounded-xl">EDIT</button><button onclick="deleteUser('${u.name}')" class="bg-red-600/20 text-red-500 px-3 py-2 rounded-xl">DEL</button></div></div>`; });
            });
        }

        async function handle(id, u, amt, act, type) {
            let bonus = 0; if(act==='approved') bonus = parseInt(prompt("Any Extra Bonus, Sweetie?", "0") || 0);
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            if(act==='approved') {
                if(type === "WITHDRAWAL") { /* Already holded */ }
                else { await ref.update({ balance: (doc.data().balance||0) + amt + bonus }); }
            } else {
                if(type === "WITHDRAWAL") await ref.update({ balance: (doc.data().balance||0) + amt });
            }
            await db.collection("requests").doc(id).update({ status: act }); alert("Done, Sweetie!");
        }

        function loadToEdit(n, b, p) { 
            document.getElementById('adm-edit-user').value = n;
            document.getElementById('adm-edit-bal').value = b;
            document.getElementById('adm-edit-prof').value = p;
        }

        async function manualSave() {
            const n = document.getElementById('adm-edit-user').value;
            const b = parseInt(document.getElementById('adm-edit-bal').value);
            const p = parseInt(document.getElementById('adm-edit-prof').value);
            if(!n) return;
            await db.collection("users").doc(n).update({ balance: b, profit: p }); alert("Registry Updated, Sweetie!");
        }

        async function deleteUser(name) { if(confirm("Erase "+name+" registry forever?")) await db.collection("users").doc(name).delete(); }
        function logout() { localStorage.removeItem('mc_agent_auth'); location.reload(); }
    </script>
</body>
</html>
