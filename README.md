<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Global Asset Network</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000205; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(40px); border: 1px solid rgba(255,255,255,0.06); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: slideUp 0.5s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        .premium-card { background: linear-gradient(135deg, #0f172a 0%, #1e3a8a 100%); border: 1px solid rgba(59, 130, 246, 0.4); box-shadow: 0 20px 50px rgba(0,0,0,0.5); }
        .ticker-wrap { background: rgba(59, 130, 246, 0.1); border-bottom: 1px solid rgba(255,255,255,0.05); }
        @keyframes scroll { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-scroll { display: inline-block; white-space: nowrap; animation: scroll 20s linear infinite; }
        .special-card { background: linear-gradient(135deg, #1e1b4b 0%, #4338ca 100%); border: 1px solid #6366f1; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="ticker-wrap h-8 flex items-center overflow-hidden z-[500]">
        <div class="animate-scroll text-[9px] font-black uppercase tracking-[0.2em] text-blue-400">
            BTC/USD: $68,432.10 (+1.2%) • GOLD/OZ: $2,145.00 • MINTCREST PROTOCOL: ACTIVE • SECURE TRANSACTIONS: 100% • 
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000205] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-6xl font-black italic tracking-tighter mb-1 uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[9px] uppercase tracking-[0.6em] mb-12 font-bold opacity-60">Elite Financial Hub</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Enter Username" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-4 focus:border-blue-500 transition-all text-sm">
                <input type="text" id="ref-by" placeholder="Invite Code (Optional)" class="w-full bg-white/5 p-4 rounded-xl border border-white/5 outline-none text-center text-[10px] mb-8 font-bold opacity-40">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[11px] uppercase tracking-widest active:scale-95 shadow-xl transition-all">Establish Secure Link</button>
            </div>
            <p class="mt-8 text-[8px] text-gray-700 uppercase font-black">Powered by DeepSec Intelligence</p>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div class="p-8 pb-4 flex justify-between items-center">
            <div>
                <p class="text-[8px] font-black text-blue-500 uppercase tracking-widest mb-1">Encrypted Node</p>
                <h1 class="text-2xl font-black tracking-tighter" id="welcome-msg">Welcome, User</h1>
            </div>
            <div class="flex gap-2">
                <button onclick="changePage('promo')" class="glass p-3 rounded-2xl text-xl">🎟️</button>
                <button onclick="window.open('https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3')" class="glass p-3 rounded-2xl text-xl">💬</button>
            </div>
        </div>

        <div id="p-home" class="page active-page p-6">
            <div class="premium-card p-10 rounded-[3rem] mb-6 relative overflow-hidden">
                <div class="absolute right-[-20px] top-[-20px] opacity-10 text-9xl font-black">MC</div>
                <p class="text-[10px] text-blue-300 font-extrabold uppercase tracking-widest opacity-80">Portfolio Vault</p>
                <h2 class="text-5xl font-black tracking-tighter mt-1" id="v-bal">₨ 0</h2>
                
                <div id="timer-box" class="hidden mt-8 bg-black/40 p-4 rounded-2xl border border-white/5">
                    <div class="flex justify-between text-[8px] font-black uppercase mb-2">
                        <span>Cycle Status</span>
                        <span id="timer-txt" class="text-blue-400">00:00:00</span>
                    </div>
                    <div class="w-full h-1.5 bg-white/10 rounded-full overflow-hidden">
                        <div id="progress-bar" class="h-full bg-blue-600 transition-all duration-1000" style="width: 0%"></div>
                    </div>
                </div>

                <div class="mt-8 grid grid-cols-2 gap-4">
                    <div class="bg-white/5 p-4 rounded-2xl">
                        <p class="text-[7px] text-gray-400 uppercase font-black">Total Yield</p>
                        <span id="v-profit" class="text-xs font-bold text-green-400">₨ 0</span>
                    </div>
                    <div class="bg-white/5 p-4 rounded-2xl">
                        <p class="text-[7px] text-gray-400 uppercase font-black">Fleet Status</p>
                        <span id="tier-tag" class="text-[9px] font-black text-blue-400 uppercase italic">Inactive</span>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] flex flex-col items-center border-b-4 border-blue-600 shadow-xl active:scale-95">
                    <span class="text-3xl mb-2">💎</span>
                    <span class="text-[10px] font-black uppercase tracking-widest">Fund</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] flex flex-col items-center border-b-4 border-red-600 shadow-xl active:scale-95">
                    <span class="text-3xl mb-2">🚀</span>
                    <span class="text-[10px] font-black uppercase tracking-widest">Payout</span>
                </button>
            </div>
        </div>

        <div id="p-invest" class="page p-6 pb-20">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-500 text-xl tracking-tighter">Trading Fleets</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-4"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400 tracking-widest">Security Ledger</h2>
            <div id="user-history" class="space-y-3"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-4">
            <div class="glass p-8 rounded-[3.5rem] border-t-4 border-blue-600 text-center">
                <p class="text-[9px] font-black text-blue-500 uppercase italic mb-3">Your Referral Node Link</p>
                <div id="ref-link" class="text-[10px] font-bold opacity-50 mb-4 truncate px-4 bg-white/5 py-2 rounded-lg">---</div>
                <button onclick="copyRef()" class="bg-blue-600 px-10 py-3 rounded-2xl font-black text-[10px] uppercase shadow-lg">Copy Link</button>
            </div>
            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-[10px] font-black text-red-500 uppercase tracking-widest">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3rem] border-t-8 border-blue-600">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm text-center">Capitalize Node</h3>
                <div class="space-y-2 mb-10 text-[9px] font-black">
                    <div class="glass p-4 rounded-xl flex justify-between"><span>JazzCash/SadaPay</span><span class="text-blue-400">03705519562</span></div>
                    <div class="glass p-4 rounded-xl flex justify-between"><span>EasyPaisa</span><span class="text-green-400">03379827882</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-black outline-none border border-white/5">
                <input type="text" id="dep-trx" placeholder="Transaction ID" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center font-black uppercase outline-none border border-white/5">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[11px] uppercase tracking-widest shadow-xl">Confirm Capital</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3rem] border-t-8 border-red-600">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm text-center">Withdraw Funds</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-black outline-none border border-white/5">
                <input type="text" id="wd-acc" placeholder="Account No & Title" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center font-bold outline-none border border-white/5">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[11px] uppercase tracking-widest shadow-xl">Request Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-black z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-500 uppercase tracking-tighter">MCG GOD MODE</h2>
            <button onclick="closeAdmin()" class="bg-red-500 px-4 py-2 rounded-xl text-[9px] font-black uppercase">Exit</button>
        </div>
        <div class="glass p-8 rounded-[3rem] mb-6">
            <input type="text" id="adm-u" placeholder="Username" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-[10px] font-black border border-white/10 outline-none">
            <input type="number" id="adm-v" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-[10px] font-black border border-white/10 outline-none">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="manualMod('balance')" class="bg-blue-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Balance</button>
                <button onclick="manualMod('profit')" class="bg-green-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Profit</button>
            </div>
        </div>
        <div id="adm-requests" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[3rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-2 active-tab"><span class="text-2xl">🏛️</span><span class="text-[8px] font-black uppercase">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-2"><span class="text-2xl">🗺️</span><span class="text-[8px] font-black uppercase">Fleets</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-2"><span class="text-2xl">📑</span><span class="text-[8px] font-black uppercase">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-2"><span class="text-2xl">🛡️</span><span class="text-[8px] font-black uppercase">Firm</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            {n:"Trial Fleet", p:100, r:2, d:30}, {n:"Micro I", p:200, r:3, d:30}, {n:"Micro II", p:500, r:3.2, d:30},
            {n:"Bronze Core", p:1000, r:3.5, d:30}, {n:"Silver X", p:2500, r:4, d:30}, {n:"Gold Prime", p:5000, r:4.5, d:30},
            {n:"Platinum", p:7500, r:5, d:30}, {n:"Diamond", p:10000, r:5.5, d:30}, {n:"Elite Node", p:15000, r:6, d:30},
            {n:"Royal Fleet", p:20000, r:7, d:30}, {n:"Master X", p:30000, r:8, d:30}, {n:"Zenith", p:50000, r:10, d:30},
            {n:"⚡ FLASH 24H", p:500, r:15, s:true}, {n:"🚀 ROCKET", p:12000, r:20, s:true}, {n:"👑 CROWN", p:25000, r:25, s:true},
            {n:"🔥 NOVA", p:2000, r:18, s:true}, {n:"🌌 GALAXY", p:100000, r:35, s:true}, {n:"🦅 HAWK", p:3500, r:12, d:30},
            {n:"🧿 ORACLE", p:8000, r:14, d:30}, {n:"🔱 ZEUS", p:150000, r:45, s:true}
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive" });
            document.getElementById('welcome-msg').innerText = `Welcome back, ${name} 👋`;
            document.getElementById('ref-link').innerText = window.location.origin + window.location.pathname + "?ref=" + name;
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
                    if(user.activeTier > 0) document.getElementById('timer-box').classList.remove('hidden');
                } 
            });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center mb-2 text-[10px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px]">${new Date(d.time).toLocaleString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="text-blue-500">${d.status}</span></div></div>`; });
            });
            setInterval(updateTimer, 1000);
        }

        function updateTimer() {
            if (user && user.activeTier > 0) {
                const next = (user.lastReqTime || Date.now()) + 86400000;
                const diff = next - Date.now();
                if(diff > 0) {
                    const h = Math.floor(diff/3600000); const m = Math.floor((diff%3600000)/60000); const s = Math.floor((diff%60000)/1000);
                    document.getElementById('timer-txt').innerText = `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
                    document.getElementById('progress-bar').style.width = (100 - (diff/86400000*100)) + "%";
                } else { document.getElementById('timer-txt').innerText = "READY FOR PROFIT"; document.getElementById('progress-bar').style.width = "100%"; }
            }
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                const isS = p.s === true;
                const style = isS ? 'special-card' : 'glass';
                const body = isS ? `<p class="text-[9px] text-blue-200 font-bold mt-1 uppercase">Special Yield: ${p.r}%</p>` : `<p class="text-[9px] text-green-400 font-bold uppercase">${p.r}% Daily</p><p class="text-[7px] text-gray-500 mt-1 uppercase">⏳ ${p.d} Days</p>`;
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="${style} p-7 rounded-[2.5rem] flex justify-between items-center active:scale-95 transition-all mb-2 cursor-pointer shadow-xl">
                    <div><h4 class="font-black text-[11px] uppercase">${p.n}</h4>${body}</div>
                    <div class="text-right"><p class="font-black text-[13px]">₨ ${p.p.toLocaleString()}</p></div>
                </div>`;
            });
        }

        async function buy(p, roi, t) {
            if(user.balance < p) return alert("Capital Required!");
            if(confirm("Link fleet "+t+"?")) { 
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: t, lastReqTime: Date.now() }); 
                alert("Protocol Established!"); changePage('activity');
            }
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value;
            if(!a || !t) return alert("Fill all fields!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "deposit", status: "pending", time: Date.now() });
            alert("Deposit Logged, sweetie! 😘"); changePage('activity');
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value;
            if(!a || !acc || parseInt(a) > user.balance) return alert("Insufficient Balance!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); alert("Request Sent!"); changePage('activity');
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function copyRef() { navigator.clipboard.writeText(document.getElementById('ref-link').innerText); alert("Link Copied!"); }

        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Command Code:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 rounded-2xl flex justify-between items-center text-[9px] font-black uppercase mb-3"><div>${d.user}<br>₨ ${d.amount} (${d.type})</div><div class="flex gap-2"><button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-1 rounded">OK</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-red-500 px-3 py-1 rounded">NO</button></div></div>`; });
            });
        }
        async function manualMod(f) {
            const u = document.getElementById('adm-u').value; const v = document.getElementById('adm-v').value;
            const ref = db.collection("users").doc(u); const d = await ref.get();
            if(d.exists) await ref.update({ [f]: (d.data()[f]||0) + parseInt(v) }); alert("Done!");
        }
        async function handle(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get(); 
            if(act==='approved' && (type==='deposit')) await ref.update({ balance: (doc.data().balance||0) + amt });
            else if(act==='rejected' && type==='withdraw') await ref.update({ balance: (doc.data().balance||0) + amt });
            await db.collection("requests").doc(id).update({ status: act }); 
        }

        window.onload = () => { const s = localStorage.getItem('mc_user'); if(s) { document.getElementById('user-name').value = s; login(); } };
    </script>
</body>
</html>
