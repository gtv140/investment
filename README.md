<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Secure Wealth</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow: hidden; }
        
        /* Glass Effect Upgrade */
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.37); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.15), transparent); }
        
        /* Smooth Animations */
        .page { display: none; animation: slideUp 0.4s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        
        /* Ticker Animation */
        .ticker-wrap { background: rgba(59, 130, 246, 0.1); overflow: hidden; white-space: nowrap; padding: 6px 0; border-bottom: 1px solid rgba(59, 130, 246, 0.2); }
        .ticker { display: inline-block; animation: ticker 40s linear infinite; font-size: 9px; font-weight: 800; text-transform: uppercase; letter-spacing: 1px; color: #60a5fa; }
        @keyframes ticker { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }

        /* Glowing Effects */
        .glow-blue { box-shadow: 0 0 20px rgba(59, 130, 246, 0.2); }
        .badge-pending { @apply bg-orange-500/10 text-orange-400 px-2 py-1 rounded text-[7px] font-bold uppercase; }
        .badge-approved { @apply bg-emerald-500/10 text-emerald-400 px-2 py-1 rounded text-[7px] font-bold uppercase; }
        .badge-rejected { @apply bg-rose-500/10 text-rose-400 px-2 py-1 rounded text-[7px] font-bold uppercase; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="ticker-wrap">
        <div class="ticker">
            💰 Market Status: Bullish • New Tier 25 Activated • User 'Zain' just withdrew ₨ 12,500 • Gold Reserves Up 4.2% • 256-bit SSL Encryption Active • Instant Payouts Enabled •
        </div>
    </div>

    <section id="auth-ui" class="h-full flex items-center justify-center p-6 bg-[#010409] z-[1000] absolute inset-0">
        <div class="glass w-full max-w-sm p-10 rounded-[3rem] text-center border-t-8 border-blue-600">
            <h1 onclick="adminTap()" class="text-4xl font-black italic tracking-tighter mb-1 cursor-pointer">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.5em] mb-12 text-center">Global Digital Custodian</p>
            <div class="space-y-4">
                <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold focus:border-blue-500 transition-all">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black shadow-lg active:scale-95 transition-all uppercase text-xs tracking-[0.2em]">Secure Login</button>
            </div>
        </div>
    </section>

    <div id="app-ui" class="hidden flex-1 overflow-y-auto pb-28">
        
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-8 rounded-[2.5rem] mb-6 border-l-4 border-blue-500 glow-blue relative overflow-hidden">
                <div class="absolute -top-10 -right-10 w-32 h-32 bg-blue-600/10 rounded-full blur-3xl"></div>
                <p class="text-[10px] text-gray-500 font-bold mb-1 uppercase tracking-widest">Net Asset Value (NAV)</p>
                <h2 class="text-5xl font-black tracking-tighter text-white" id="v-bal">₨ 0</h2>
                <div class="mt-4"><span class="text-[9px] bg-emerald-500/10 text-emerald-400 border border-emerald-500/20 px-3 py-1.5 rounded-full font-bold uppercase">Profit: <span id="v-profit">₨ 0</span></span></div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="changePage('wallet')" class="glass p-6 rounded-3xl text-center active:scale-95 transition border-b-2 border-blue-600">
                    <div class="bg-blue-600/20 w-10 h-10 rounded-full flex items-center justify-center mx-auto mb-2 text-xl">📥</div>
                    <span class="text-[10px] font-bold uppercase tracking-widest">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-6 rounded-3xl text-center active:scale-95 transition border-b-2 border-rose-600">
                    <div class="bg-rose-600/20 w-10 h-10 rounded-full flex items-center justify-center mx-auto mb-2 text-xl">📤</div>
                    <span class="text-[10px] font-bold uppercase tracking-widest">Withdraw</span>
                </button>
            </div>

            <div class="glass p-5 rounded-[2rem] border border-blue-500/10 flex items-center justify-between">
                <div class="flex items-center gap-3">
                    <div class="text-2xl">🛡️</div>
                    <div><h5 class="text-[10px] font-extrabold uppercase text-blue-400">Encrypted Vault</h5><p class="text-[8px] opacity-40">Assets insured by MintCrest Reserves</p></div>
                </div>
                <div class="text-[8px] font-bold text-emerald-400 bg-emerald-500/5 px-2 py-1 rounded">SECURE</div>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <div class="text-center mb-8">
                <h2 class="text-2xl font-black italic uppercase text-white">Investment <span class="text-blue-500">Tiers</span></h2>
                <p class="text-[8px] text-gray-500 uppercase tracking-widest">Select a market plan to grow assets</p>
            </div>
            <div id="plans-list" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-400 tracking-tighter">Activity Ledger</h2>
            <div id="user-history" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-4">
            <div class="glass p-6 rounded-3xl text-center border-t-2 border-blue-500">
                <p class="text-[9px] font-bold text-gray-500 mb-3 uppercase tracking-widest">Your Referral ID</p>
                <input type="text" id="ref-link" readonly class="w-full bg-white/5 p-4 rounded-xl text-[10px] text-center border border-white/5 mb-4 font-mono">
                <button onclick="copyRef()" class="w-full bg-blue-600 py-3 rounded-xl font-bold text-[10px] uppercase tracking-widest">Copy Network Link</button>
            </div>

            <div class="glass p-6 rounded-3xl">
                <h3 class="font-black text-xs mb-3 uppercase text-blue-400 italic">Institutional Profile</h3>
                <p class="text-[9px] leading-relaxed opacity-60 font-medium">MintCrest Gold Enterprise is a tier-one asset management firm. We utilize advanced commodity trading algorithms to provide consistent returns on gold equity. Our platform is trusted by over 50,000+ active investors globally.</p>
            </div>

            <div class="glass p-6 rounded-3xl border-l-4 border-rose-500/50">
                <h3 class="font-bold text-xs mb-3 uppercase text-rose-400">Privacy & Terms</h3>
                <p class="text-[8px] opacity-50 mb-1">• Funds are audited daily by MintCrest Compliance.</p>
                <p class="text-[8px] opacity-50 mb-1">• Withdrawal requests are processed within 1-24 hours.</p>
                <p class="text-[8px] opacity-50">• False transaction IDs lead to instant permanent ban.</p>
            </div>

            <a href="mailto:webhub262@gmail.com" class="block w-full glass p-5 rounded-2xl text-center text-[10px] font-bold text-blue-400">Official Support: webhub262@gmail.com</a>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3rem] text-center">
                <h3 class="font-black text-blue-500 mb-6 uppercase tracking-widest">Funding Gateway</h3>
                <div class="space-y-3 mb-8">
                    <div class="glass p-4 rounded-2xl flex justify-between items-center"><span class="text-[10px] font-bold text-red-500">JAZZCASH</span><span class="font-black">03705519562</span></div>
                    <div class="glass p-4 rounded-2xl flex justify-between items-center"><span class="text-[10px] font-bold text-emerald-500">EASYPAISA</span><span class="font-black">03705519562</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 mb-3 text-center font-bold">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 mb-6 text-center font-bold">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl">Complete Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 rounded-[3rem] border-t-8 border-rose-600">
                <h3 class="font-black text-rose-500 mb-6 text-center uppercase tracking-widest">Request Payout</h3>
                <input type="number" id="wd-amt" placeholder="Amount to Withdraw" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 mb-3 text-center font-bold">
                <input type="text" id="wd-acc" placeholder="Full Account & Title Info" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 mb-6 text-center font-bold text-[10px]">
                <button onclick="submitWithdraw()" class="w-full bg-rose-600 py-5 rounded-2xl font-black text-xs uppercase shadow-xl">Confirm Withdrawal</button>
            </div>
        </div>
    </div>

    <div id="admin-panel" class="fixed inset-0 bg-[#010409] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-500 italic uppercase">Admin Console</h2>
            <button onclick="closeAdmin()" class="bg-white/5 px-4 py-2 rounded-xl text-[10px] font-bold uppercase border border-white/10">Lock</button>
        </div>

        <div class="grid grid-cols-2 gap-4 mb-6">
            <button onclick="showAdminSection('requests')" class="glass p-5 rounded-2xl border-b-2 border-blue-500 font-bold text-[9px] uppercase tracking-widest">Approvals</button>
            <button onclick="showAdminSection('users')" class="glass p-5 rounded-2xl border-b-2 border-emerald-500 font-bold text-[9px] uppercase tracking-widest">User List</button>
        </div>

        <div id="adm-sec-requests" class="adm-sec space-y-4 mb-10"></div>
        <div id="adm-sec-users" class="adm-sec hidden space-y-3 mb-10"></div>

        <div class="glass p-6 rounded-[2.5rem] border border-blue-500/20">
            <h4 class="text-[9px] font-bold text-center mb-4 opacity-50 uppercase tracking-[0.3em]">Manual Asset Edit</h4>
            <input type="text" id="adm-user" placeholder="Enter Username" class="w-full bg-white/5 p-4 rounded-xl mb-2 text-center text-sm font-bold border border-white/5">
            <input type="number" id="adm-val" placeholder="Value (PKR)" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center font-bold border border-white/5">
            <div class="grid grid-cols-2 gap-2 mb-4">
                <button onclick="manualEdit('balance')" class="bg-blue-600 py-4 rounded-xl font-bold text-[9px] uppercase">Add Balance</button>
                <button onclick="manualEdit('profit')" class="bg-emerald-600 py-4 rounded-xl font-bold text-[9px] uppercase">Add Profit</button>
            </div>
            <button onclick="deleteUser()" class="w-full bg-rose-600/20 text-rose-500 py-4 rounded-xl font-bold text-[9px] uppercase">Delete Account</button>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-5 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[2.5rem]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 active-tab"><span class="text-xl">🏠</span><span class="text-[8px] font-bold uppercase mt-1">Home</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-1"><span class="text-xl">📈</span><span class="text-[8px] font-bold uppercase mt-1">Tiers</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-1"><span class="text-xl">📜</span><span class="text-[8px] font-bold uppercase mt-1">Logs</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-1"><span class="text-xl">💎</span><span class="text-[8px] font-bold uppercase mt-1">About</span></button>
    </nav>

    <script>
        // CONFIG
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;
        const plans = Array.from({length: 25}, (_, i) => ({ n: `Gold Asset Tier ${i+1}`, p: (i+1)*500, r: 35 + (i*5) }));

        window.onload = () => { const saved = localStorage.getItem('mintcrest_user'); if(saved) { document.getElementById('user-name').value = saved; login(); } };
        function adminTap() { tapCount++; if(tapCount >= 3) { tapCount = 0; if(prompt("System Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } } setTimeout(() => tapCount = 0, 2000); }

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mintcrest_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now() });
            startSync(name); document.getElementById('auth-ui').style.display = 'none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); document.getElementById('ref-link').value = window.location.origin + "/?ref=" + name;
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history');
                if (snap.empty) { list.innerHTML = '<p class="text-center opacity-20 text-[10px] py-20 tracking-widest">TRANSACTION ARCHIVE EMPTY</p>'; return; }
                let items = []; snap.forEach(doc => items.push(doc.data()));
                items.sort((a,b) => b.time - a.time);
                list.innerHTML = '';
                items.forEach(d => {
                    list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center border-l-2 ${d.type==='deposit'?'border-blue-500':'border-rose-500'} mb-2 text-[10px] font-bold uppercase tracking-tighter"><div>${d.type}<br><span class="opacity-40 text-[7px] font-medium">${new Date(d.time).toLocaleString()}</span></div><div class="text-right">₨ ${d.amount.toLocaleString()}<br><span class="badge-${d.status}">${d.status}</span></div></div>`;
                });
            });
        }

        function renderPlans() { const list = document.getElementById('plans-list'); list.innerHTML = ''; plans.forEach(p => { list.innerHTML += `<div onclick="buy(${p.p})" class="glass p-5 rounded-[2rem] flex justify-between items-center active:scale-95 mb-2 transition-all cursor-pointer border-b border-white/5"><div><h4 class="font-black text-[10px] uppercase text-blue-400">${p.n}</h4><p class="text-[9px] text-emerald-400 font-bold uppercase tracking-widest">ROI: ${p.r}%</p></div><p class="font-black text-sm">₨ ${p.p.toLocaleString()}</p></div>`; }); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(p!=='wallet'&&p!=='withdraw') document.getElementById('n-'+p).classList.add('active-tab'); }
        async function submitDeposit() { const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; if(!a||!t) return alert("All fields mandatory"); await db.collection("requests").add({user:user.name, amount:parseInt(a), tid:t, type:"deposit", status:"pending", time:Date.now()}); alert("Verification Request Sent!"); changePage('activity'); }
        async function submitWithdraw() { const a = document.getElementById('wd-amt').value; const acc = document.getElementById('wd-acc').value; if(!a||!acc||a>user.balance) return alert("Check balance/info"); await db.collection("requests").add({user:user.name, amount:parseInt(a), acc:acc, type:"withdraw", status:"pending", time:Date.now()}); await db.collection("users").doc(user.name).update({balance:user.balance-parseInt(a)}); alert("Withdrawal Logged!"); changePage('activity'); }
        async function buy(p) { if(user.balance<p) { alert("Insufficient Portfolio Funds"); changePage('wallet'); } else { await db.collection("users").doc(user.name).update({balance:user.balance-p}); alert("Success: Tier Locked!"); } }
        function copyRef() { const c = document.getElementById('ref-link'); c.select(); document.execCommand('copy'); alert("Link Copied to Clipboard!"); }

        // ADMIN ENGINE
        function showAdminSection(s) { document.querySelectorAll('.adm-sec').forEach(sec=>sec.classList.add('hidden')); document.getElementById('adm-sec-'+s).classList.remove('hidden'); }
        function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = snap.empty ? '<p class="text-center opacity-30 py-10 text-[10px]">ALL CLEAR</p>' : '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 rounded-2xl flex justify-between items-center border-l-4 ${d.type==='deposit'?'border-blue-500':'border-rose-500'} mb-2 text-[9px] uppercase font-bold text-white"><div class="text-left">${d.user}<br>₨ ${d.amount}<br><span class="opacity-50">${d.tid || d.acc}</span></div><div class="flex gap-2"><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'approved', '${d.type}')" class="bg-emerald-600 px-4 py-2 rounded-xl">YES</button><button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, 'rejected', '${d.type}')" class="bg-rose-600 px-4 py-2 rounded-xl">NO</button></div>                </div>`; 
            });
        }

        async function handleReq(id, target, amt, action, type) {
            const ref = db.collection("users").doc(target);
            try {
                if(action === 'approved' && type === 'deposit') { 
                    const d = await ref.get(); 
                    await ref.update({ balance: (d.data().balance || 0) + amt }); 
                }
                else if(action === 'rejected' && type === 'withdraw') { 
                    const d = await ref.get(); 
                    await ref.update({ balance: (d.data().balance || 0) + amt }); 
                }
                await db.collection("requests").doc(id).update({ status: action });
                alert("Transaction " + action);
            } catch (e) {
                alert("Error: " + e.message);
            }
        }

        async function manualEdit(f) { 
            const u = document.getElementById('adm-user').value.trim(); 
            const v = document.getElementById('adm-val').value; 
            if(!u || !v) return alert("Fill details!"); 
            const ref = db.collection("users").doc(u); 
            const d = await ref.get(); 
            if(d.exists) { 
                await ref.update({ [f]: (d.data()[f] || 0) + parseInt(v) }); 
                alert("Asset Adjusted!"); 
            } else {
                alert("User not found!");
            }
        }

        async function deleteUser() { 
            const u = document.getElementById('adm-user').value.trim(); 
            if(!u || !confirm("Freeze user account permanently?")) return; 
            await db.collection("users").doc(u).delete(); 
            alert("Account Wiped."); 
        }

        function closeAdmin() { 
            document.getElementById('admin-panel').classList.add('hidden'); 
        }
    </script>
</body>
</html>
