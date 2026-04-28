<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Elite Asset Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #F8FAFC; color: #1E293B; overflow-x: hidden; }
        .vibrant-bg { background: linear-gradient(-45deg, #EEF2FF, #FAF5FF, #F0FDF4, #FFFBEB); background-size: 400% 400%; animation: gradientBG 15s ease infinite; }
        @keyframes gradientBG { 0% { background-position: 0% 50%; } 50% { background-position: 100% 50%; } 100% { background-position: 0% 50%; } }
        .glass-light { background: rgba(255, 255, 255, 0.7); backdrop-filter: blur(15px); border: 1px solid rgba(255, 255, 255, 0.5); box-shadow: 0 10px 30px -10px rgba(0,0,0,0.05); }
        .card-blue { background: linear-gradient(135deg, #3B82F6 0%, #2563EB 100%); color: white; box-shadow: 0 20px 40px -10px rgba(59, 130, 246, 0.3); }
        .page { display: none; animation: slideUp 0.4s cubic-bezier(0.16, 1, 0.3, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); filter: blur(5px); } to { opacity: 1; transform: translateY(0); filter: blur(0); } }
        .input-field { width: 100%; background: #F1F5F9; padding: 16px; border-radius: 16px; border: 1px solid #E2E8F0; outline: none; transition: 0.3s; font-weight: 600; font-size: 12px; }
        .input-field:focus { border-color: #3B82F6; background: white; box-shadow: 0 0 0 4px rgba(59, 130, 246, 0.1); }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; white-space: nowrap; }
    </style>
</head>
<body class="h-screen flex flex-col vibrant-bg">

    <div id="promo-ticker" class="hidden bg-white/50 backdrop-blur-md border-b border-blue-100 py-2 overflow-hidden z-[500]">
        <div class="animate-marquee text-[10px] font-black uppercase tracking-widest text-blue-600 italic">
            📢 <span id="ticker-text">Syncing System Updates...</span>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-white flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-4xl font-black italic tracking-tighter mb-2 uppercase text-slate-800">MINT<span class="text-blue-600">CREST</span></h1>
            <p class="text-slate-400 text-[8px] uppercase tracking-[0.4em] mb-10 font-bold">Official Digital Asset Vault</p>
            
            <div id="login-form" class="glass-light p-8 rounded-[2.5rem] space-y-4">
                <input type="text" id="auth-username" placeholder="Username" class="input-field text-center">
                <input type="password" id="auth-password" placeholder="Security PIN" class="input-field text-center">
                <button onclick="handleAuth('login')" class="w-full bg-slate-900 text-white py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95 shadow-xl">Secure Access</button>
                <p class="text-[9px] font-bold text-slate-500">New Partner? <span class="text-blue-600 cursor-pointer" onclick="toggleAuth(false)">Create Account</span></p>
            </div>

            <div id="signup-form" class="hidden glass-light p-8 rounded-[2.5rem] space-y-4">
                <input type="text" id="reg-username" placeholder="Choose Username" class="input-field text-center">
                <input type="password" id="reg-password" placeholder="Set PIN" class="input-field text-center">
                <button onclick="handleAuth('signup')" class="w-full bg-blue-600 text-white py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95 shadow-xl">Register Fleet</button>
                <p class="text-[9px] font-bold text-slate-500">Member? <span class="text-blue-600 cursor-pointer" onclick="toggleAuth(true)">Login</span></p>
            </div>
        </div>
    </section>

    <header class="p-6 flex justify-between items-center sticky top-0 z-[500] bg-white/60 backdrop-blur-md border-b border-gray-200/50">
        <h1 class="text-xl font-black italic tracking-tighter uppercase text-slate-800">MINT<span class="text-blue-600 text-2xl">CREST</span></h1>
        <div onclick="adminTap()" class="bg-blue-100 p-2.5 rounded-xl border border-blue-200 active:scale-90 transition-all">
            <i class="fa-solid fa-fingerprint text-blue-600 text-lg"></i>
        </div>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="card-blue p-8 rounded-[2.5rem] mb-6 relative overflow-hidden">
                <p class="text-[10px] opacity-80 font-black mb-1 uppercase tracking-widest">Active Assets Pool</p>
                <h2 class="text-5xl font-black tracking-tighter mb-2" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="text-[9px] font-black uppercase text-blue-200 italic mb-6">System Ready</div>
                <div class="flex items-center justify-between border-t border-white/20 pt-6">
                    <div><p class="text-[8px] opacity-70 font-black uppercase">Net Profit</p><p id="v-profit" class="text-lg font-black text-white">₨ 0</p></div>
                    <div class="text-right"><p class="text-[8px] opacity-70 font-black uppercase tracking-tighter">Current Fleet</p><p id="tier-tag" class="text-[10px] font-black uppercase italic text-blue-100">Scanning...</p></div>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass-light p-6 rounded-[2rem] flex flex-col items-center gap-2 border-b-4 border-blue-500 active:scale-95"><i class="fa-solid fa-plus text-blue-600"></i><span class="text-[9px] font-black uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass-light p-6 rounded-[2rem] flex flex-col items-center gap-2 border-b-4 border-rose-500 active:scale-95"><i class="fa-solid fa-paper-plane text-rose-600"></i><span class="text-[9px] font-black uppercase">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-600 text-lg tracking-widest">Active Trading Fleet</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-slate-800">Security Ledger</h2>
            <div id="user-history" class="space-y-2 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6">
            <div class="glass-light p-8 rounded-[2.5rem] border-t-8 border-blue-600 shadow-2xl relative overflow-hidden">
                <div class="mb-6"><h3 class="text-blue-600 font-black text-xs uppercase tracking-[0.3em] italic">Corporate Registry</h3><p class="text-[7px] font-bold text-slate-400 uppercase mt-1">Status: Active & Licensed ✅</p></div>
                <div class="space-y-4">
                    <div><p class="text-[8px] font-black text-slate-400 uppercase mb-1">Company Name</p><p class="text-[10px] font-bold text-slate-800 uppercase">MintCrest Gold Enterprise Ltd.</p></div>
                    <div class="grid grid-cols-2 gap-4">
                        <div><p class="text-[8px] font-black text-slate-400 uppercase mb-1">License No.</p><p class="text-[9px] font-bold text-blue-600">#UK-778491-MCG</p></div>
                        <div><p class="text-[8px] font-black text-slate-400 uppercase mb-1">Incorporated</p><p class="text-[9px] font-bold text-slate-800">12 FEB 2021</p></div>
                    </div>
                    <div><p class="text-[8px] font-black text-slate-400 uppercase mb-1">Headquarters</p><p class="text-[9px] font-bold text-slate-600 italic leading-relaxed">Level 24, The Shard, 32 London Bridge St, London SE1 9SG, United Kingdom.</p></div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <div class="glass-light p-6 rounded-[2rem] text-center border-b-4 border-yellow-500">
                    <i class="fa-solid fa-gift text-yellow-500 mb-2"></i>
                    <input type="text" id="promo-input" placeholder="CODE" class="w-full bg-white mt-1 p-2 rounded-lg text-[9px] border border-slate-100 text-center uppercase font-bold">
                    <button onclick="applyPromoRequest()" class="mt-2 text-[8px] font-black text-yellow-600 uppercase">Activate</button>
                </div>
                <div class="glass-light p-6 rounded-[2rem] text-center border-b-4 border-green-500">
                    <i class="fa-brands fa-whatsapp text-green-500 mb-2"></i>
                    <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" class="inline-block mt-2 text-[8px] font-black text-green-600 uppercase">Official Hub</a>
                </div>
            </div>

            <button onclick="logout()" class="w-full p-4 text-[9px] font-black text-rose-500 uppercase italic opacity-40">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass-light p-8 rounded-[2.5rem] space-y-4 text-center">
                <p class="text-[10px] font-black text-blue-600 uppercase mb-4">Official Channels: 03705519562 / 03379827882</p>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="input-field text-center">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="input-field text-center">
                <input type="file" id="dep-proof" accept="image/*" class="hidden">
                <label for="dep-proof" class="block w-full bg-slate-50 p-5 rounded-2xl border-2 border-dashed border-slate-200 text-center cursor-pointer text-[9px] font-bold text-slate-400 uppercase italic">📸 Upload Receipt</label>
                <button onclick="submitDeposit()" class="w-full bg-blue-600 text-white py-6 rounded-2xl font-black text-[10px] uppercase shadow-xl">Verify Funding</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass-light p-8 rounded-[3rem] text-center border-l-8 border-rose-500">
                <h3 class="font-black text-rose-500 mb-6 uppercase text-sm italic">Payout Terminal</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="input-field text-center mb-4">
                <select id="wd-method" class="input-field mb-4 text-center">
                    <option value="JazzCash">JazzCash</option>
                    <option value="EasyPaisa">EasyPaisa</option>
                    <option value="SadaPay">SadaPay</option>
                </select>
                <input type="text" id="wd-acc-num" placeholder="Wallet Number" class="input-field text-center mb-4">
                <input type="text" id="wd-acc-name" placeholder="Account Name" class="input-field text-center mb-8">
                <button onclick="submitWithdraw()" class="w-full bg-rose-600 text-white py-6 rounded-2xl font-black text-[10px] uppercase shadow-xl">Authorize Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-white z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-blue-600 uppercase italic">Admin Console</h2>
            <button onclick="closeAdmin()" class="bg-rose-100 text-rose-600 px-6 py-2 rounded-xl text-[10px] font-black uppercase">Exit</button>
        </div>
        <div class="flex gap-2 mb-8 overflow-x-auto">
            <button onclick="showAdmTab('requests')" class="bg-blue-600 text-white px-6 py-3 rounded-xl text-[9px] font-black uppercase">Requests</button>
            <button onclick="showAdmTab('users')" class="bg-slate-100 text-slate-600 px-6 py-3 rounded-xl text-[9px] font-black uppercase">Database</button>
            <button onclick="showAdmTab('broadcast')" class="bg-yellow-500 text-white px-6 py-3 rounded-xl text-[9px] font-black uppercase">Broadcast</button>
        </div>
        <div id="adm-sec-requests" class="adm-tab space-y-3"></div>
        <div id="adm-sec-users" class="adm-tab hidden space-y-3"></div>
        <div id="adm-sec-broadcast" class="adm-tab hidden glass-light p-8 rounded-[2.5rem]">
            <textarea id="bc-msg" placeholder="Broadcast message..." class="input-field h-24 mb-4"></textarea>
            <button onclick="sendBroadcast()" class="w-full bg-yellow-500 text-white py-4 rounded-xl font-black text-[10px] uppercase">Deploy Message</button>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden bg-white/80 backdrop-blur-xl border-t border-slate-100 p-6 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[3.5rem] shadow-xl">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center"><i class="fa-solid fa-house-user text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center opacity-40"><i class="fa-solid fa-chart-line text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center opacity-40"><i class="fa-solid fa-receipt text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center opacity-40"><i class="fa-solid fa-building-shield text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Firm</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [{ n: "Micro-Elite I", p: 200, r: 3 }, { n: "Bronze-S", p: 1000, r: 3.5 }, { n: "Silver-X", p: 5000, r: 4.5 }, { n: "Gold-Master", p: 15000, r: 6 }, { n: "Royal-Grand", p: 100000, r: 10 }];

        window.onload = () => { if(localStorage.getItem('mc_user')) loginSuccess(localStorage.getItem('mc_user')); listenForPromos(); };

        function toggleAuth(showLogin) { document.getElementById('login-form').classList.toggle('hidden', !showLogin); document.getElementById('signup-form').classList.toggle('hidden', showLogin); }
        async function handleAuth(type) {
            const u = document.getElementById(type==='login'?'auth-username':'reg-username').value.trim();
            const p = document.getElementById(type==='login'?'auth-password':'reg-password').value.trim();
            if(!u || !p) return alert("Fill all fields, sweetie!");
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            if(type==='signup') { if(doc.exists) return alert("Taken!"); await ref.set({ name: u, password: p, balance: 0, profit: 0, time: Date.now(), tierName: "Inactive" }); alert("Ready! 😘"); location.reload(); }
            else { if(!doc.exists || doc.data().password !== p) return alert("Wrong Credentials!"); localStorage.setItem('mc_user', u); loginSuccess(u); }
        }

        function loginSuccess(name) { document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden'); startSync(name); renderPlans(); }
        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit||0).toLocaleString(); document.getElementById('tier-tag').innerText = user.tierName; } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass-light p-4 rounded-2xl flex justify-between items-center mb-2 text-[9px] uppercase font-black"><div>${d.type}<br><span class="opacity-30">${new Date(d.time).toLocaleTimeString()}</span></div><div class="text-right">Rs ${d.amount}<br><span class="text-blue-500">${d.status}</span></div></div>`; });
            });
        }

        function renderPlans() { const list = document.getElementById('plans-list'); list.innerHTML = ''; plans.forEach(p => { list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, '${p.n}')" class="glass-light p-5 rounded-[2rem] flex justify-between items-center active:scale-95 mb-2 border-l-4 border-slate-300"><div><h4 class="font-black text-[10px] uppercase">${p.n}</h4><p class="text-[8px] text-green-600 font-black">${p.r}% Daily</p></div><div class="font-black text-[11px]">₨ ${p.p.toLocaleString()}</div></div>`; }); }
        async function buy(p, roi, t) { if(user.balance < p) { alert("Low Funds!"); changePage('wallet'); } else { if(confirm("Activate "+t+"?")) await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: t, lastReqTime: Date.now() }); alert("Success! 🚀"); } }
        async function submitWithdraw() {
            const a = parseInt(document.getElementById('wd-amt').value); const m = document.getElementById('wd-method').value; const n = document.getElementById('wd-acc-num').value.trim(); const nm = document.getElementById('wd-acc-name').value.trim();
            if(!a || !n || a > user.balance) return alert("Check Details!");
            await db.collection("requests").add({ user: user.name, amount: a, method: m, details: `${nm} (${n})`, type: "Withdrawal", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - a }); alert("Sent! 💸"); changePage('activity');
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; const f = document.getElementById('dep-proof').files[0];
            if(!a || !t || !f) return alert("Incomplete!");
            const r = new FileReader(); r.readAsDataURL(f); r.onload = async () => { await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: r.result, type: "deposit", status: "pending", time: Date.now() }); alert("Sent! 😘"); changePage('activity'); };
        }

        function listenForPromos() { db.collection("app_data").doc("announcement").onSnapshot(doc => { if(doc.exists) { document.getElementById('promo-ticker').classList.remove('hidden'); document.getElementById('ticker-text').innerText = doc.data().message; } }); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.add('opacity-40')); document.getElementById('p-'+p).classList.add('active-page'); document.getElementById('n-'+p).classList.remove('opacity-40'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Terminal Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } setTimeout(()=>tapCount=0,3000); }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }
        async function sendBroadcast() { await db.collection("app_data").doc("announcement").set({ message: document.getElementById('bc-msg').value, time: Date.now() }); alert("Sent!"); }

        function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const l = document.getElementById('adm-sec-requests'); l.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    const info = d.details ? `<br>${d.method}: ${d.details}` : (d.tid ? `<br>TID: ${d.tid}` : '');
                    l.innerHTML += `<div class="glass-light p-4 rounded-xl text-[8px] font-black uppercase mb-2"><div>${d.user} | Rs ${d.amount}<br>${d.type}${info}</div><button onclick="handleAdm('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 text-white px-4 py-1 mt-2 rounded">Approve</button></div>`;
                });
            });
            db.collection("users").onSnapshot(snap => { const l = document.getElementById('adm-sec-users'); l.innerHTML = ''; snap.forEach(doc => { const u = doc.data(); l.innerHTML += `<div class="glass-light p-3 text-[8px] font-black uppercase mb-1 flex justify-between"><span>${u.name}</span><span>Rs ${u.balance}</span></div>`; }); });
        }

        async function handleAdm(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            if(act==='approved' && type==='deposit') await ref.update({ balance: (doc.data().balance||0) + amt });
            await db.collection("requests").doc(id).update({ status: act }); alert("Actioned!");
        }
    </script>
</body>
</html>
