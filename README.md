<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Private Equity Hub</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000205; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(40px); border: 1px solid rgba(255,255,255,0.06); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.15), transparent); }
        .page { display: none; animation: slideUp 0.5s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        .premium-card { background: linear-gradient(135deg, #0f172a 0%, #1e3a8a 100%); border: 1px solid rgba(59, 130, 246, 0.4); position: relative; overflow: hidden; }
        .ticker-wrap { background: rgba(59, 130, 246, 0.1); border-bottom: 1px solid rgba(255,255,255,0.05); }
        .kyc-badge { background: linear-gradient(to right, #22c55e, #10b981); color: black; font-size: 8px; font-weight: 900; padding: 2px 8px; border-radius: 5px; }
        @keyframes scroll { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-scroll { display: inline-block; white-space: nowrap; animation: scroll 15s linear infinite; }
        .special-offer { background: linear-gradient(145deg, #1e1b4b, #312e81); border: 1px solid #6366f1; box-shadow: 0 10px 30px rgba(79, 70, 229, 0.2); }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="ticker-wrap h-8 flex items-center overflow-hidden z-[500]">
        <div class="animate-scroll text-[9px] font-black uppercase tracking-[0.2em] text-blue-400">
            BTC/USD: $64,231.50 (+2.4%) • XAU/USD (GOLD): $2,184.20 • MCG SECURITY PROTOCOL: ACTIVE • TOTAL PAYOUTS TODAY: ₨ 1,450,200 •
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000205] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-6xl font-black italic tracking-tighter mb-1 uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[9px] uppercase tracking-[0.6em] mb-12 font-bold opacity-60 italic">Private Financial Network</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Protocol Username" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-4 focus:border-blue-500 transition-all text-sm">
                <input type="text" id="ref-by" placeholder="Access Key (Invite)" class="w-full bg-white/5 p-4 rounded-xl border border-white/5 outline-none text-center text-[10px] mb-8 font-bold opacity-50">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[11px] uppercase tracking-widest active:scale-95 transition-all shadow-xl">Establish Link</button>
            </div>
            <p class="mt-8 text-[8px] text-gray-600 uppercase font-black">Encrypted with 256-Bit SSL Security</p>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div class="p-8 pb-4 flex justify-between items-center">
            <div>
                <div class="flex items-center gap-2 mb-1">
                    <span id="kyc-label" class="text-[7px] font-black bg-yellow-500/20 text-yellow-500 px-2 py-0.5 rounded uppercase">Unverified</span>
                    <span class="text-[7px] font-black text-blue-500 uppercase tracking-widest">Premium Node</span>
                </div>
                <h1 class="text-2xl font-black tracking-tighter" id="welcome-txt">Identity Required</h1>
            </div>
            <div class="flex gap-2">
                <button onclick="changePage('kyc')" class="glass p-3 rounded-2xl text-xl">🛡️</button>
                <button onclick="window.open('https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3')" class="glass p-3 rounded-2xl text-xl">💬</button>
            </div>
        </div>

        <div id="p-home" class="page active-page p-6">
            <div class="premium-card p-10 rounded-[3rem] mb-6 shadow-2xl">
                <div class="flex justify-between items-start mb-10">
                    <div>
                        <p class="text-[10px] text-blue-300 font-extrabold uppercase tracking-widest opacity-80">Total Capital</p>
                        <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                    </div>
                    <div class="text-right">
                        <p class="text-[8px] font-black text-green-400 uppercase">Live Yield</p>
                        <p id="v-profit" class="text-lg font-black tracking-tighter text-white">₨ 0</p>
                    </div>
                </div>

                <div id="timer-box" class="hidden bg-black/40 p-4 rounded-2xl border border-white/5 mb-2">
                    <div class="flex justify-between text-[8px] font-black uppercase mb-2">
                        <span>Cycle Progress</span>
                        <span id="timer-txt" class="text-blue-400">00:00:00</span>
                    </div>
                    <div class="w-full h-1.5 bg-white/10 rounded-full overflow-hidden">
                        <div id="progress-bar" class="h-full bg-blue-600 transition-all duration-1000" style="width: 0%"></div>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] flex flex-col items-center border-b-4 border-blue-600 shadow-xl active:scale-95 transition-all">
                    <span class="text-3xl mb-2">💳</span>
                    <span class="text-[10px] font-black uppercase tracking-widest">Capitalize</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] flex flex-col items-center border-b-4 border-red-600 shadow-xl active:scale-95 transition-all">
                    <span class="text-3xl mb-2">🏧</span>
                    <span class="text-[10px] font-black uppercase tracking-widest">Liquidate</span>
                </button>
            </div>

            <div class="glass p-6 rounded-[2.5rem] flex justify-around items-center">
                <div class="text-center">
                    <p class="text-[10px] font-black uppercase mb-1">2FA</p>
                    <p class="text-[7px] text-green-500 font-bold">ACTIVE</p>
                </div>
                <div class="w-[1px] h-8 bg-white/10"></div>
                <div class="text-center" onclick="changePage('more')">
                    <p class="text-[10px] font-black uppercase mb-1">REFERRAL</p>
                    <p id="ref-count" class="text-[7px] text-blue-500 font-bold">0 NODES</p>
                </div>
                <div class="w-[1px] h-8 bg-white/10"></div>
                <div class="text-center">
                    <p class="text-[10px] font-black uppercase mb-1">SLA</p>
                    <p class="text-[7px] text-blue-500 font-bold">99.9% UP</p>
                </div>
            </div>
        </div>

        <div id="p-invest" class="page p-6 pb-20">
            <div class="flex items-center justify-between mb-8">
                <h2 class="font-black italic uppercase text-blue-500 text-xl tracking-tighter">Trading Fleets</h2>
                <span class="text-[8px] font-black opacity-40 uppercase">Market: Open</span>
            </div>
            <div id="plans-list" class="grid grid-cols-1 gap-4"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400 tracking-widest">Ledger Audit</h2>
            <div id="user-history" class="space-y-3"></div>
        </div>

        <div id="p-kyc" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] text-center border-t-4 border-yellow-500">
                <h2 class="text-xl font-black uppercase mb-2">Identity Vault</h2>
                <p class="text-[9px] text-gray-500 mb-8 uppercase font-bold tracking-widest">Verify identity for higher limits</p>
                <div class="space-y-4">
                    <input type="text" placeholder="Full Name as per ID" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 text-[11px] font-bold outline-none">
                    <label class="block w-full bg-white/5 p-8 rounded-2xl border border-dashed border-white/20 text-center cursor-pointer">
                        <span class="text-[9px] font-black opacity-40 uppercase">📸 Upload CNIC / Passport</span>
                        <input type="file" class="hidden">
                    </label>
                    <button onclick="alert('Verification submitted, sweetie!')" class="w-full bg-yellow-600 py-5 rounded-2xl font-black text-[10px] uppercase shadow-xl">Verify Me</button>
                </div>
            </div>
        </div>

        <div id="p-more" class="page p-6 space-y-6">
            <div class="glass p-8 rounded-[3.5rem] border-t-4 border-blue-600 text-center">
                <h3 class="text-blue-500 font-black text-[10px] uppercase mb-4 italic">Network Referral ID</h3>
                <div id="my-ref-id" class="text-3xl font-black py-2 tracking-tighter text-white">---</div>
                <button onclick="copyRef()" class="mt-4 bg-blue-600 px-10 py-3 rounded-2xl font-black text-[10px] uppercase shadow-lg">Copy Link</button>
            </div>
            
            <div class="grid grid-cols-1 gap-3">
                <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" class="glass p-6 rounded-[2.5rem] flex justify-between items-center px-10 border-l-4 border-green-500">
                    <span class="text-[10px] font-black uppercase tracking-widest text-green-400">Institutional WhatsApp</span>
                    <span>↗️</span>
                </a>
                <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-[10px] font-black text-red-500 uppercase tracking-widest">Disconnect Session</button>
            </div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-blue-600">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm text-center">Inward Remittance</h3>
                <div class="space-y-3 mb-10 text-[10px] font-black">
                    <div class="glass p-4 rounded-xl flex justify-between border-l-2 border-blue-500"><span>JAZZCASH/SADAPAY</span><span class="text-blue-400">03705519562</span></div>
                    <div class="glass p-4 rounded-xl flex justify-between border-l-2 border-green-500"><span>EASYPAISA</span><span class="text-green-400">03379827882</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none border border-white/10">
                <input type="text" id="dep-trx" placeholder="Transaction TID" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center font-bold uppercase outline-none border border-white/10">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[11px] uppercase tracking-widest shadow-xl">Confirm Capital</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm text-center">Liquidation Request</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none border border-white/10">
                <input type="text" id="wd-acc" placeholder="Destination Bank/Wallet Details" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center text-[11px] font-bold outline-none border border-white/10">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[11px] uppercase tracking-widest shadow-xl">Initiate Payout</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-2 active-tab"><span class="text-2xl">🏛️</span><span class="text-[8px] font-black uppercase">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-2"><span class="text-2xl">🗺️</span><span class="text-[8px] font-black uppercase">Fleets</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-2"><span class="text-2xl">📑</span><span class="text-[8px] font-black uppercase">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-2"><span class="text-2xl">🛡️</span><span class="text-[8px] font-black uppercase">Firm</span></button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-black z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-500 italic uppercase tracking-tighter">MCG GOD MODE 6.0</h2>
            <button onclick="closeAdmin()" class="bg-red-500 px-4 py-2 rounded-xl text-[9px] font-black uppercase">Close</button>
        </div>

        <div class="glass p-6 rounded-[2rem] mb-6 grid grid-cols-2 gap-4">
            <div class="text-center"><p class="text-[10px] opacity-40 uppercase">Total Nodes</p><h3 id="adm-count" class="text-xl font-black">0</h3></div>
            <div class="text-center"><p class="text-[10px] opacity-40 uppercase">Live Queue</p><h3 id="adm-pending" class="text-xl font-black text-yellow-500">0</h3></div>
        </div>

        <div class="glass p-8 rounded-[3rem] mb-6">
            <h3 class="text-[10px] font-black uppercase mb-4 text-center opacity-40">Direct Asset Injection</h3>
            <input type="text" id="adm-u" placeholder="Target Username" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-[10px] font-black border border-white/10 outline-none">
            <input type="number" id="adm-v" placeholder="Amount (PKR)" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-[10px] font-black border border-white/10 outline-none">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="manualMod('balance')" class="bg-blue-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Balance</button>
                <button onclick="manualMod('profit')" class="bg-green-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Profit</button>
            </div>
            <button onclick="manualMod('alert')" class="w-full bg-yellow-600 py-3 rounded-xl font-black text-[9px] uppercase mt-2">Personal Notification</button>
        </div>

        <div id="adm-requests" class="space-y-4"></div>
    </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            {n:"Trial Fleet", p:100, r:2, d:30}, {n:"Micro I", p:200, r:3, d:30}, {n:"Micro II", p:500, r:3.2, d:30},
            {n:"Bronze", p:1000, r:3.5, d:30}, {n:"Silver", p:2500, r:4, d:30}, {n:"Gold Core", p:5000, r:4.5, d:30},
            {n:"Platinum Elite", p:7500, r:5, d:30}, {n:"Diamond Vault", p:10000, r:5.5, d:30}, {n:"Titanium", p:15000, r:6, d:30},
            {n:"Royal Fleet", p:20000, r:7, d:30}, {n:"Prime Node", p:30000, r:8, d:30}, {n:"Zenith X", p:50000, r:10, d:30},
            {n:"⚡ FLASH EX", p:500, r:15, s:true}, {n:"🚀 MARS PRO", p:12000, r:20, s:true}, {n:"👑 EMPEROR", p:25000, r:25, s:true},
            {n:"🪐 NEBULA", p:50000, r:30, s:true}, {n:"🛸 COSMIC", p:100000, r:40, s:true}, {n:"🔥 SOLARIS", p:2000, r:18, s:true},
            {n:"🧿 ORACLE", p:8000, r:14, d:30}, {n:"🔱 ZEUS ULTRA", p:200000, r:50, s:true}
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Unlinked", kyc: false });
            document.getElementById('welcome-txt').innerText = `Welcome Back, ${name}`;
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); document.getElementById('my-ref-id').innerText = name;
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { 
                if(doc.exists) { 
                    user = doc.data(); 
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); 
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); 
                    document.getElementById('tier-tag').innerText = user.tierName;
                    if(user.kyc) { 
                        document.getElementById('kyc-label').innerText = "Verified ✅"; 
                        document.getElementById('kyc-label').className = "text-[7px] font-black bg-green-500/20 text-green-500 px-2 py-0.5 rounded uppercase"; 
                    }
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
                const diff = next - Date.now()) + 86400000;
                const diff = next - Date.now();
                if(diff > 0) {
                    const h = Math.floor(diff/3600000); const m = Math.floor((diff%3600000)/60000); const s = Math.floor((diff%60000)/1000);
                    document.getElementById('timer-txt').innerText = `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
                    const perc = 100 - (diff / 86400000 * 100);
                    document.getElementById('progress-bar').style.width = perc + "%";
                } else { document.getElementById('timer-txt').innerText = "READY"; document.getElementById('progress-bar').style.width = "100%"; }
            }
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                const isS = p.s === true;
                const style = isS ? 'special-offer' : 'glass';
                const yieldInfo = isS ? `<p class="text-[9px] text-indigo-300 font-bold uppercase mt-1">Total Yield: ${p.r}%</p>` : `<p class="text-[9px] text-green-400 font-bold uppercase">${p.r}% Daily</p><p class="text-[7px] text-gray-500 font-bold mt-1">⏳ ${p.d} Days</p>`;
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="${style} p-7 rounded-[2.5rem] flex justify-between items-center active:scale-95 transition-all mb-2 cursor-pointer shadow-lg">
                    <div><h4 class="font-black text-[12px] uppercase">${p.n}</h4>${yieldInfo}</div>
                    <div class="text-right"><p class="font-black text-[15px]">₨ ${p.p.toLocaleString()}</p></div>
                </div>`;
            });
        }

        async function buy(p, roi, t) {
            if(user.balance < p) return alert("Capital Required!");
            if(confirm("Establish protocol with "+t+"?")) { 
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: t, lastReqTime: Date.now() }); 
                alert("Protocol Established!"); changePage('activity');
            }
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value;
            if(!a || !t) return alert("Fields Mandatory!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "deposit", status: "pending", time: Date.now() });
            alert("Capital Logged, sweetie! 😘"); changePage('activity');
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value;
            if(!a || !acc || parseInt(a) > user.balance) return alert("Insufficient Balance!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); alert("Request Authorized!"); changePage('activity');
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw'&&p!=='kyc') document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function copyRef() { const u = user.name; navigator.clipboard.writeText(window.location.origin + window.location.pathname + "?ref=" + u); alert("Invitation Link Copied!"); }

        // --- GOD MODE ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("God Mode Access Code:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        async function syncAdmin() {
            db.collection("users").onSnapshot(snap => { document.getElementById('adm-count').innerText = snap.size; });
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                document.getElementById('adm-pending').innerText = snap.size;
                const list = document.getElementById('adm-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 rounded-2xl flex justify-between items-center text-[10px] font-black uppercase mb-3"><div>${d.user}<br>₨ ${d.amount} (${d.type})</div><div class="flex gap-2"><button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-1 rounded">Approve</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-red-500 px-3 py-1 rounded">Reject</button></div></div>`; });
            });
        }
        async function manualMod(f) {
            const u = document.getElementById('adm-u').value; const v = document.getElementById('adm-v').value;
            const ref = db.collection("users").doc(u);
            if(f==='alert') await ref.update({ alert: v });
            else { const d = await ref.get(); if(d.exists) await ref.update({ [f]: (d.data()[f]||0) + parseInt(v) }); }
            alert("Asset Injected!");
        }
        async function handle(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get(); 
            if(act==='approved' && (type==='deposit' || type.includes('Profit'))) await ref.update({ balance: (doc.data().balance||0) + amt });
            else if(act==='rejected' && type==='withdraw') await ref.update({ balance: (doc.data().balance||0) + amt });
            await db.collection("requests").doc(id).update({ status: act }); 
        }

        window.onload = () => { const s = localStorage.getItem('mc_user'); if(s) { document.getElementById('user-name').value = s; login(); } };
    </script>
</body>
</html>
