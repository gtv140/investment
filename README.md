<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Luxury Asset Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        
        :root {
            --primary: #00f2fe;
            --secondary: #4facfe;
            --accent: #7000ff;
            --bg: #010409;
        }

        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: white; overflow-x: hidden; }
        
        /* Glassmorphism Ultra */
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.1); box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.37); }
        .neon-glow { border: 1px solid rgba(0, 242, 254, 0.3); box-shadow: 0 0 20px rgba(0, 242, 254, 0.1); }
        
        /* Gradient Mastery */
        .grad-main { background: linear-gradient(135deg, #00f2fe 0%, #4facfe 100%); }
        .grad-dark { background: linear-gradient(135deg, #0f172a 0%, #1e293b 100%); }
        .grad-neon { background: linear-gradient(135deg, #7000ff 0%, #00f2fe 100%); }
        .grad-gold { background: linear-gradient(135deg, #f6d365 0%, #fda085 100%); }
        
        /* Smooth Scrolling & Animations */
        .app-shell { height: 100vh; overflow-y: auto; scroll-behavior: smooth; -webkit-overflow-scrolling: touch; }
        .page { display: none; animation: slideUp 0.5s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        
        /* Navigation Styling */
        .nav-btn { transition: all 0.4s ease; opacity: 0.3; filter: grayscale(1); }
        .active-tab { opacity: 1; filter: grayscale(0); transform: translateY(-8px); color: var(--primary); }
        
        /* Status Badges */
        .status-badge { font-size: 8px; font-weight: 900; padding: 4px 10px; border-radius: 50px; text-transform: uppercase; }
        .badge-pending { background: rgba(255, 193, 7, 0.2); color: #ffc107; border: 1px solid rgba(255, 193, 7, 0.3); }
        .badge-approved { background: rgba(0, 255, 135, 0.2); color: #00ff87; border: 1px solid rgba(0, 255, 135, 0.3); }
        
        /* Custom Inputs */
        input, select, textarea { background: rgba(255,255,255,0.05) !important; border: 1px solid rgba(255,255,255,0.1) !important; color: white !important; transition: 0.3s; }
        input:focus { border-color: var(--primary) !important; box-shadow: 0 0 15px rgba(0, 242, 254, 0.2); }
        
        .marquee-container { background: rgba(0, 242, 254, 0.05); border-bottom: 1px solid rgba(0, 242, 254, 0.1); }
        .marquee-text { display: inline-block; animation: scroll 25s linear infinite; white-space: nowrap; font-size: 10px; font-weight: 800; letter-spacing: 1px; }
        @keyframes scroll { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="app-shell">

    <div id="ticker-wrap" class="hidden fixed top-0 w-full z-[1000] backdrop-blur-xl marquee-container py-3">
        <div class="marquee-text text-cyan-400">
            ✦ SYSTEM STATUS: OPERATIONAL ✦ 10% REGULATION TAX APPLIED ON ALL WITHDRAWALS ✦ NEW FLASH PLANS ACTIVATED ✦ CONTACT SUPPORT FOR VIP UPGRADES ✦
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[#010409] flex flex-col items-center justify-center p-8">
        <div class="absolute inset-0 bg-gradient-to-b from-cyan-500/10 to-transparent pointer-events-none"></div>
        <h1 onclick="adminTap()" class="text-6xl font-black italic tracking-tighter mb-2 uppercase bg-gradient-to-br from-white via-cyan-300 to-blue-600 bg-clip-text text-transparent animate-pulse">MINTCREST</h1>
        <p class="text-cyan-500/50 text-[9px] uppercase tracking-[0.6em] mb-12 font-extrabold italic">Global Asset Authority</p>
        
        <div class="glass p-10 rounded-[4rem] w-full max-w-sm neon-glow">
            <input type="text" id="user-name" placeholder="IDENTIFICATION NAME" class="w-full p-6 rounded-3xl text-center font-black mb-6 uppercase tracking-widest text-[11px]">
            <button onclick="login()" class="w-full grad-main py-6 rounded-3xl font-black text-[11px] uppercase tracking-[0.2em] text-white shadow-[0_10px_30px_rgba(0,242,254,0.3)] active:scale-95 transition-all">Unlock Node</button>
        </div>
    </section>

    <main id="app-ui" class="hidden pt-20 pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="grad-dark p-10 rounded-[4rem] mb-8 relative overflow-hidden neon-glow">
                <div class="absolute -right-10 -top-10 w-40 h-40 grad-main opacity-20 blur-[80px]"></div>
                <p class="text-[10px] text-cyan-400 font-black mb-2 uppercase tracking-[0.2em]">Available Capital</p>
                <h2 class="text-6xl font-black tracking-tighter text-white mb-6" id="v-bal">₨ 0</h2>
                
                <div class="flex justify-between items-center border-t border-white/10 pt-6">
                    <div class="bg-white/5 px-6 py-3 rounded-2xl border border-white/10">
                        <p class="text-[8px] text-gray-500 font-bold uppercase">Total Yield</p>
                        <p class="text-xl font-black text-cyan-400" id="v-profit">0</p>
                    </div>
                    <div class="text-right">
                        <p id="rank-badge" class="text-[9px] font-black text-white bg-cyan-600 px-4 py-1 rounded-full uppercase italic shadow-lg shadow-cyan-900">Standard</p>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[3rem] flex flex-col items-center gap-3 border-b-4 border-cyan-500 active:scale-95 transition-all">
                    <div class="w-12 h-12 grad-main rounded-2xl flex items-center justify-center text-2xl shadow-lg">📥</div>
                    <span class="text-[10px] font-black uppercase tracking-widest text-white">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[3rem] flex flex-col items-center gap-3 border-b-4 border-purple-500 active:scale-95 transition-all">
                    <div class="w-12 h-12 grad-neon rounded-2xl flex items-center justify-center text-2xl shadow-lg">📤</div>
                    <span class="text-[10px] font-black uppercase tracking-widest text-white">Withdraw</span>
                </button>
            </div>

            <button onclick="requestBonus()" class="w-full grad-gold p-8 rounded-[3.5rem] flex justify-between items-center shadow-2xl active:scale-95 group transition-all">
                <div class="text-left">
                    <h4 class="text-[12px] font-black uppercase text-black">Daily Asset Reward</h4>
                    <p class="text-[9px] font-bold text-orange-900 uppercase opacity-70">Claim your 24h cycle bonus</p>
                </div>
                <div class="w-12 h-12 bg-black/10 rounded-full flex items-center justify-center text-2xl group-hover:rotate-12 transition-transform">🎁</div>
            </button>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-cyan-400 text-2xl tracking-tighter">High-Yield Fleets</h2>
            <div id="plans-list" class="space-y-4 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <div class="flex justify-between items-center mb-8">
                <h2 class="font-black italic uppercase text-purple-400 text-lg tracking-widest">Asset Ledger</h2>
                <button onclick="clearHistory()" class="text-[9px] bg-red-500/10 border border-red-500/20 px-5 py-2 rounded-full text-red-500 font-black uppercase">Wipe Cache</button>
            </div>
            <div id="user-history" class="space-y-4"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6">
            <div class="glass p-8 rounded-[3.5rem] border-l-[10px] border-cyan-500 shadow-xl">
                <h3 class="text-cyan-400 font-black text-[12px] uppercase mb-3 italic tracking-widest">Protocol Intelligence</h3>
                <p class="text-[10px] text-gray-400 font-bold uppercase leading-relaxed">MintCrest Gold utilizes quantum liquidity pools to stabilize user capital. Our 10% regulation tax ensures the longevity of the global payout engine.</p>
            </div>

            <div class="glass p-8 rounded-[3.5rem] border-t-4 border-purple-600">
                <h3 class="text-purple-400 font-black text-[12px] uppercase mb-1 italic">Partnership Node</h3>
                <p class="text-[9px] text-gray-500 mb-6 font-bold uppercase italic tracking-tighter">5% Instant Revenue on Network Funding</p>
                <div class="flex gap-3">
                    <input type="text" id="ref-link" readonly class="flex-1 p-4 rounded-2xl text-[10px] font-black tracking-widest">
                    <button onclick="copyRef()" class="grad-neon px-8 rounded-2xl font-black text-[10px] uppercase shadow-lg shadow-purple-900">Copy</button>
                </div>
            </div>

            <div class="glass p-8 rounded-[3.5rem]">
                <h3 class="text-green-400 font-black text-[11px] uppercase mb-6 text-center italic">Secure Support Channel</h3>
                <textarea id="support-msg" placeholder="REASON FOR INQUIRY..." class="w-full p-5 rounded-3xl text-[10px] font-bold mb-4 h-28"></textarea>
                <button onclick="sendSupport()" class="w-full bg-green-500 py-5 rounded-3xl font-black text-[10px] uppercase text-black shadow-lg shadow-green-900">Dispatch Message</button>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[3rem] text-[10px] font-black text-red-500 uppercase italic border-red-500/20">Terminate Session 🔒</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[4rem] border-t-[12px] border-cyan-500">
                <h3 class="font-black text-cyan-400 mb-8 uppercase text-center text-lg italic tracking-widest">Funding Gateway</h3>
                <div class="space-y-4 mb-10">
                    <div class="bg-white/5 p-6 rounded-3xl flex justify-between items-center border border-white/10"><span class="text-[10px] font-black text-gray-500">JAZZ/SADA</span><b class="text-cyan-400 text-sm">03705519562</b></div>
                    <div class="bg-white/5 p-6 rounded-3xl flex justify-between items-center border border-white/10"><span class="text-[10px] font-black text-gray-500">EASYPAISA</span><b class="text-green-400 text-sm">03379827882</b></div>
                </div>
                <input type="number" id="dep-amount" placeholder="CAPITAL AMOUNT" class="w-full p-6 rounded-3xl mb-4 text-center font-black text-lg">
                <input type="text" id="dep-trx" placeholder="TID / TRANSACTION HASH" class="w-full p-6 rounded-3xl mb-8 text-center font-black uppercase text-[11px]">
                <button onclick="submitDeposit()" class="w-full grad-main py-6 rounded-3xl font-black text-[12px] uppercase text-white shadow-2xl">Confirm Capital</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[4rem] border-t-[12px] border-red-600 text-center shadow-2xl">
                <h3 class="font-black text-red-500 mb-2 uppercase text-xl italic tracking-tighter">Liquidation</h3>
                <p class="text-[9px] text-gray-500 mb-10 font-black uppercase italic opacity-60">Automatic 10% Protocol Tax Deducted</p>
                <input type="number" id="wd-amt" placeholder="AMOUNT (PKR)" class="w-full p-6 rounded-3xl mb-4 text-center font-black text-xl">
                <input type="text" id="wd-acc" placeholder="ACCOUNT DETAILS & TITLE" class="w-full p-6 rounded-3xl mb-10 text-center text-[10px] font-black">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-3xl font-black text-[12px] uppercase text-white shadow-xl shadow-red-900">Authorize Release</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[2000] rounded-t-[4.5rem] border-t border-white/10">
        <button onclick="changePage('home')" id="n-home" class="nav-btn active-tab flex flex-col items-center"><span class="text-2xl">💎</span><span class="text-[8px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn flex flex-col items-center"><span class="text-2xl">🚀</span><span class="text-[8px] font-black uppercase mt-1">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="nav-btn flex flex-col items-center"><span class="text-2xl">⚡</span><span class="text-[8px] font-black uppercase mt-1">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="nav-btn flex flex-col items-center"><span class="text-2xl">🏢</span><span class="text-[8px] font-black uppercase mt-1">Firm</span></button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-[#000814] z-[50000] hidden overflow-y-auto">
        <header class="p-8 border-b border-white/10 bg-[#000814]/80 backdrop-blur-2xl sticky top-0 flex justify-between items-center">
            <h2 class="text-2xl font-black text-cyan-400 uppercase italic tracking-tighter">Command Node</h2>
            <button onclick="closeAdmin()" class="bg-red-600 text-white px-8 py-3 rounded-2xl text-[10px] font-black uppercase">Lock</button>
        </header>
        <div class="p-6">
            <div class="grid grid-cols-4 gap-2 mb-8">
                <button onclick="showAdmTab('requests')" class="grad-main p-4 rounded-2xl text-[8px] font-black uppercase text-black">Reqs</button>
                <button onclick="showAdmTab('users')" class="glass p-4 rounded-2xl text-[8px] font-black uppercase">Users</button>
                <button onclick="showAdmTab('msg')" class="grad-neon p-4 rounded-2xl text-[8px] font-black uppercase">Inbox</button>
                <button onclick="showAdmTab('config')" class="grad-gold p-4 rounded-2xl text-[8px] font-black uppercase text-black">Tools</button>
            </div>
            <div id="adm-sec-requests" class="adm-tab space-y-4"></div>
            <div id="adm-sec-users" class="adm-tab hidden space-y-2"></div>
            <div id="adm-sec-msg" class="adm-tab hidden space-y-2"></div>
            <div id="adm-sec-config" class="adm-tab hidden space-y-6">
                <div class="glass p-8 rounded-[3rem] text-center">
                    <input type="text" id="adm-target" placeholder="USER NAME" class="w-full p-4 rounded-2xl mb-4 text-center font-bold">
                    <input type="number" id="adm-val" placeholder="VALUE" class="w-full p-4 rounded-2xl mb-6 text-center font-bold">
                    <div class="grid grid-cols-2 gap-3">
                        <button onclick="manualEdit('balance')" class="grad-main py-4 rounded-2xl text-black font-black text-[9px] uppercase">Add Balance</button>
                        <button onclick="manualEdit('profit')" class="bg-white py-4 rounded-2xl text-black font-black text-[9px] uppercase">Add Profit</button>
                    </div>
                    <textarea id="bc-msg" placeholder="NEW TICKER MESSAGE..." class="w-full p-4 rounded-2xl mt-6 text-[10px] h-20"></textarea>
                    <button onclick="sendBroadcast()" class="w-full grad-gold py-4 rounded-2xl mt-4 text-black font-black text-[9px] uppercase">Update News</button>
                </div>
            </div>
        </div>
    </div>

    <script>
        // Firebase initialization (Use your keys)
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3 }, { n: "Bronze-S", p: 1000, r: 3.5 }, { n: "Silver-X", p: 5000, r: 4.5 },
            { n: "Gold-X", p: 10000, r: 5.5 }, { n: "Diamond-Master", p: 50000, r: 8 },
            { n: "⚡ FLASH 24H", p: 500, r: 12, s: true }, { n: "🚀 ROCKET PRO", p: 12000, r: 18, s: true }, { n: "👑 CROWN KING", p: 25000, r: 22, s: true }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); if(saved) { document.getElementById('user-name').value = saved; login(); }
            listenForPromos();
        };

        function listenForPromos() { db.collection("app_data").doc("announcement").onSnapshot(doc => { if(doc.exists) { document.getElementById('ticker-wrap').classList.remove('hidden'); document.getElementById('ticker-text').innerText = doc.data().message; } }); }

        async function login() {
            const name = document.getElementById('user-name').value.trim().toLowerCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive" });
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); document.getElementById('ref-link').value = "https://mintcrest.gold/?node=" + name;
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { 
                user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); 
                document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); 
                let rank = "STANDARD"; if(user.activeTier >= 10000) rank = "GOLD PRO"; if(user.activeTier >= 50000) rank = "WHALE";
                document.getElementById('rank-badge').innerText = rank;
            } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                let items = []; snap.forEach(doc => { if(!localStorage.getItem('cl_'+doc.id)) items.push({id: doc.id, ...doc.data()}); }); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { 
                    list.innerHTML += `<div class="glass p-6 rounded-[2rem] flex justify-between items-center border-l-4 ${d.type.includes('dep')?'border-cyan-500':'border-purple-500'} mb-3"><div><p class="text-[10px] font-black uppercase text-white">${d.type}</p><p class="opacity-30 text-[8px] uppercase font-bold">${new Date(d.time).toLocaleTimeString()}</p></div><div class="text-right"><p class="font-black text-xs text-white">₨ ${d.amount}</p><span class="status-badge badge-${d.status}">${d.status}</span></div></div>`; 
                });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-8 rounded-[3rem] flex justify-between items-center active:scale-95 transition-transform border-b-2 border-white/5"><div><h4 class="font-black text-[12px] uppercase ${p.s ? 'text-cyan-400' : 'text-gray-400'}">${p.n}</h4><p class="text-[9px] text-purple-400 font-black uppercase tracking-widest">${p.r}% DAILY YIELD</p></div><div class="text-right font-black text-xl text-white">₨ ${p.p.toLocaleString()}</div></div>`;
            });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; if(!a || !t) return alert("All fields required! 😘");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "deposit", status: "pending", time: Date.now() });
            alert("Verification initiated! 😘"); changePage('activity');
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a || !acc || parseInt(a) > user.balance) return alert("Insufficient Capital! 😘");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw (10% Tax)", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); 
            alert("Liquidation Authorized! 😘"); changePage('activity');
        }

        async function requestBonus() {
            await db.collection("requests").add({ user: user.name, amount: 0, type: "Daily Bonus Request", status: "pending", time: Date.now() });
            alert("Reward request dispatched to Admin! 😘");
        }

        async function sendSupport() {
            const m = document.getElementById('support-msg').value.trim(); if(!m) return;
            await db.collection("support").add({ user: user.name, message: m, time: Date.now() });
            alert("Inquiry successfully transmitted! 😘"); document.getElementById('support-msg').value = '';
        }

        async function buy(p, roi, tName) {
            if(user.balance < p) { alert("Insufficient Node Balance! 😘"); changePage('wallet'); }
            else { if(confirm("Activate "+tName+"?")) { await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, lastReqTime: Date.now() }); await db.collection("requests").add({ user: user.name, amount: p, type: "Fleet Activation", status: "approved", time: Date.now() }); alert("Fleet Online! 😘"); changePage('activity'); } }
        }

        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); 
            document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); 
            document.getElementById('p-'+p).classList.add('active-page'); 
            if(document.getElementById('n-'+p)) document.getElementById('n-'+p).classList.add('active-tab'); 
            window.scrollTo(0,0);
        }

        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("System Override Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }
        async function sendBroadcast() { const m = document.getElementById('bc-msg').value.trim(); await db.collection("app_data").doc("announcement").set({ message: m }); alert("Broadcast updated! 😘"); }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    list.innerHTML += `<div class="glass p-6 rounded-3xl mb-4">
                        <div class="flex justify-between font-black uppercase text-[10px] mb-2"><span class="text-cyan-400">${d.user}</span><span>₨ ${d.amount}</span></div>
                        <p class="text-[8px] opacity-40 mb-4">${d.type} | ${d.tid||d.acc||''}</p>
                        <div class="flex gap-2">
                            <button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="flex-1 grad-main py-3 rounded-xl text-[9px] font-black uppercase text-black">Approve</button>
                            <button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="flex-1 bg-red-600 py-3 rounded-xl text-[9px] font-black uppercase">Reject</button>
                        </div>
                    </div>`;
                });
            });
            db.collection("users").onSnapshot(snap => { const list = document.getElementById('adm-sec-users'); list.innerHTML = ''; snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center text-[9px] font-black uppercase mb-2"><div>${u.name}<br>Bal: ${u.balance}</div><button onclick="deleteUser('${u.name}')" class="text-red-500">Del</button></div>`; }); });
            db.collection("support").onSnapshot(snap => { const list = document.getElementById('adm-sec-msg'); list.innerHTML = ''; snap.forEach(doc => { const m = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-2xl text-[9px] mb-2 font-bold"><p class="text-cyan-400 uppercase">${m.user}</p><p class="mt-1">${m.message}</p></div>`; }); });
        }

        async function handle(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            if(act==='approved') {
                if(type === "Daily Bonus Request") { const b = parseInt(prompt("Bonus Amount for "+u)); await ref.update({ balance: (doc.data().balance||0) + b }); await db.collection("requests").doc(id).update({ status: 'approved', amount: b }); return; }
                if(type==='deposit') { await ref.update({ balance: (doc.data().balance||0) + amt }); }
            } else if(act==='rejected' && type.includes('withdraw')) { await ref.update({ balance: (doc.data().balance||0) + amt }); }
            await db.collection("requests").doc(id).update({ status: act }); 
        }

        async function manualEdit(f) { const u = document.getElementById('adm-target').value.toLowerCase(); const v = parseInt(document.getElementById('adm-val').value); const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) await ref.update({ [f]: (d.data()[f]||0)+v }); alert("Done! 😘"); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function copyRef() { const c = document.getElementById('ref-link'); c.select(); document.execCommand('copy'); alert("Referral Link Copied! 😘"); }
        function clearHistory() { if(confirm("Clear local ledger?")) { db.collection("requests").where("user", "==", user.name).get().then(snap => { snap.forEach(doc => { localStorage.setItem('cl_'+doc.id, 'true'); }); location.reload(); }); } }
    </script>
</body>
</html>
