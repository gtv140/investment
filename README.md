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
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; white-space: nowrap; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
    </style>
</head>
<body class="h-screen flex flex-col vibrant-bg">

    <div id="promo-ticker" class="hidden bg-white/50 backdrop-blur-md border-b border-blue-100 py-2 overflow-hidden z-[500]">
        <div class="animate-marquee text-[10px] font-black uppercase tracking-widest text-blue-600 italic">
            📢 <span id="ticker-text">Syncing System Updates...</span>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-white flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 class="text-4xl font-black italic tracking-tighter mb-2 uppercase text-slate-800">MINT<span class="text-blue-600">CREST</span></h1>
            <div class="glass-light p-10 rounded-[3rem] border-t-4 border-blue-600">
                <input type="text" id="user-name" placeholder="Legal Full Name" class="w-full bg-slate-50 p-5 rounded-2xl border border-slate-100 outline-none text-center font-bold mb-5 text-slate-800">
                <button onclick="login()" class="w-full bg-slate-900 text-white py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95 shadow-xl">Secure Access</button>
            </div>
        </div>
    </section>

    <header class="p-6 flex justify-between items-center sticky top-0 z-[500] bg-white/60 backdrop-blur-md border-b border-gray-200/50">
        <h1 class="text-xl font-black italic tracking-tighter uppercase text-slate-800">MINT<span class="text-blue-600 text-2xl">CREST</span></h1>
        <div onclick="adminTap()" class="bg-blue-100 p-2.5 rounded-xl border border-blue-200 active:scale-90 transition-all cursor-pointer">
            <i class="fa-solid fa-fingerprint text-blue-600 text-lg"></i>
        </div>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="card-blue p-8 rounded-[2.5rem] mb-6 relative overflow-hidden">
                <p class="text-[10px] opacity-80 font-black mb-1 uppercase tracking-widest text-white">Active Balance</p>
                <h2 class="text-5xl font-black tracking-tighter mb-2" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="text-[9px] font-black uppercase text-blue-100 italic mb-6">System Ready</div>
                <div class="flex items-center justify-between border-t border-white/20 pt-6">
                    <div><p class="text-[8px] opacity-70 font-black uppercase">Profit</p><p id="v-profit" class="text-lg font-black text-white">₨ 0</p></div>
                    <div class="text-right"><p class="text-[8px] opacity-70 font-black uppercase">Fleet</p><p id="tier-tag" class="text-[10px] font-black uppercase italic text-blue-100">Inactive</p></div>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass-light p-6 rounded-[2rem] flex flex-col items-center gap-2 border-b-4 border-blue-500 active:scale-95"><i class="fa-solid fa-wallet text-blue-600"></i><span class="text-[9px] font-black uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass-light p-6 rounded-[2rem] flex flex-col items-center gap-2 border-b-4 border-rose-500 active:scale-95"><i class="fa-solid fa-paper-plane text-rose-600"></i><span class="text-[9px] font-black uppercase">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-600">Available Plans</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-slate-800">History</h2>
            <div id="user-history" class="space-y-2 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-4">
            <div class="glass-light p-8 rounded-[2.5rem] border-l-8 border-purple-600 text-center">
                <h3 class="text-purple-600 font-black text-[10px] uppercase mb-4">🎁 Redeem Promo Code</h3>
                <input type="text" id="promo-input" placeholder="ENTER CODE" class="w-full bg-white p-4 rounded-xl text-center font-black border outline-none mb-3 uppercase">
                <button onclick="applyPromo()" class="w-full bg-purple-600 text-white py-4 rounded-xl font-black text-[9px] uppercase active:scale-95">Apply</button>
            </div>

            <div class="glass-light p-6 rounded-[2.5rem] border-l-8 border-blue-600">
                <h3 class="text-blue-600 font-black text-[10px] uppercase mb-2 italic">🏢 MintCrest Enterprise (LLC)</h3>
                <p class="text-[8px] font-bold text-slate-600 leading-relaxed">
                    Registration: 04296474 | Status: Active<br>
                    Location: Valiant House, 12 Knoll Rise, Orpington, BR6 0PG
                </p>
            </div>

            <div class="glass-light p-6 rounded-[2.5rem] border-l-8 border-slate-400">
                <h3 class="text-slate-800 font-black text-[10px] uppercase mb-2">📜 Privacy Policy</h3>
                <p class="text-[7.5px] text-slate-500 italic">User data is audited by our internal compliance team for secure payout processing.</p>
            </div>

            <button onclick="logout()" class="w-full glass-light p-6 rounded-[2.5rem] text-center text-[10px] font-black text-rose-500 uppercase">Logout</button>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass-light p-10 rounded-[3rem] border-l-8 border-rose-500 text-center">
                <h3 class="font-black text-rose-500 mb-6 uppercase text-sm italic">Payout Terminal</h3>
                <select id="wd-method" class="w-full bg-white p-5 rounded-2xl mb-4 text-[10px] font-black border outline-none">
                    <option value="JazzCash">JazzCash</option>
                    <option value="EasyPaisa">EasyPaisa</option>
                    <option value="SadaPay">SadaPay</option>
                    <option value="Bank Transfer">Bank Transfer</option>
                </select>
                <input type="number" id="wd-amt" placeholder="Amount" class="w-full bg-white p-5 rounded-2xl mb-4 text-center font-bold">
                <input type="text" id="wd-acc-name" placeholder="Account Title" class="w-full bg-white p-5 rounded-2xl mb-4 text-center text-[10px] font-bold">
                <input type="text" id="wd-acc-num" placeholder="Account Number" class="w-full bg-white p-5 rounded-2xl mb-8 text-center text-[10px] font-bold">
                <button onclick="submitWithdraw()" class="w-full bg-rose-600 text-white py-6 rounded-2xl font-black text-[10px] uppercase shadow-xl active:scale-95">Request Payout</button>
            </div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass-light p-8 rounded-[2.5rem] space-y-4 text-center">
                <p class="text-[8px] font-black text-blue-600 uppercase">EasyPaisa: 03379827882</p>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white p-5 rounded-2xl border text-center font-bold">
                <input type="text" id="dep-trx" placeholder="Transaction ID" class="w-full bg-white p-5 rounded-2xl border text-center font-bold">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 text-white py-5 rounded-2xl font-black text-[10px] uppercase">Submit Deposit</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-white z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-600 uppercase italic">Master Console</h2>
            <button onclick="closeAdmin()" class="bg-rose-100 text-rose-600 px-6 py-2 rounded-xl text-[10px] font-black">Exit</button>
        </div>
        <div class="flex gap-2 mb-8 overflow-x-auto pb-2">
            <button onclick="showAdmTab('requests')" class="bg-blue-600 text-white px-6 py-3 rounded-xl text-[9px] font-black uppercase">Requests</button>
            <button onclick="showAdmTab('promos')" class="bg-purple-600 text-white px-6 py-3 rounded-xl text-[9px] font-black uppercase">Codes</button>
            <button onclick="showAdmTab('news')" class="bg-yellow-500 text-white px-6 py-3 rounded-xl text-[9px] font-black uppercase">News</button>
        </div>
        <div id="adm-sec-requests" class="adm-tab space-y-4"></div>
        <div id="adm-sec-promos" class="adm-tab hidden glass-light p-6 rounded-[2rem]">
            <input type="text" id="new-code" placeholder="Code Name" class="w-full p-4 rounded-xl border mb-3 text-[10px] font-bold uppercase">
            <input type="number" id="new-val" placeholder="Value" class="w-full p-4 rounded-xl border mb-4 text-[10px] font-bold">
            <button onclick="createPromo()" class="w-full bg-purple-600 text-white py-4 rounded-xl font-black text-[9px] uppercase">Create</button>
        </div>
        <div id="adm-sec-news" class="adm-tab hidden glass-light p-6 rounded-[2rem]">
            <textarea id="bc-msg" placeholder="Broadcast message..." class="w-full bg-white p-5 rounded-2xl text-[10px] border mb-4 h-24 font-bold"></textarea>
            <button onclick="sendBroadcast()" class="w-full bg-yellow-500 text-white py-4 rounded-xl font-black text-[10px] uppercase">Update</button>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden bg-white/80 backdrop-blur-xl border-t p-6 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[3.5rem]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center opacity-40"><i class="fa-solid fa-house-user text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center opacity-40"><i class="fa-solid fa-chart-line text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center opacity-40"><i class="fa-solid fa-receipt text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center opacity-40"><i class="fa-solid fa-building-shield text-xl"></i><span class="text-[8px] font-black uppercase mt-1">Firm</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const d = await ref.get();
            if(!d.exists) await ref.set({ name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, lastReqTime: Date.now(), tierROI: 0, tierName: "Inactive", usedPromos: [] });
            startSync(name); document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden'); changePage('home');
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit||0).toLocaleString(); document.getElementById('tier-tag').innerText = user.tierName; } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass-light p-4 rounded-2xl flex justify-between items-center mb-2 font-black text-[9px] uppercase"><div>${d.type}</div><div>₨ ${d.amount}<br><span class="text-blue-600">${d.status}</span></div></div>`; });
            });
            db.collection("app_data").doc("announcement").onSnapshot(doc => { if(doc.exists) { document.getElementById('promo-ticker').classList.remove('hidden'); document.getElementById('ticker-text').innerText = doc.data().message; } });
        }

        async function applyPromo() {
            const code = document.getElementById('promo-input').value.trim().toUpperCase(); if(!code) return;
            if(user.usedPromos && user.usedPromos.includes(code)) return alert("Already used!");
            const ref = await db.collection("promocodes").doc(code).get();
            if(ref.exists) {
                const val = ref.data().value;
                await db.collection("users").doc(user.name).update({ balance: (user.balance||0) + val, usedPromos: firebase.firestore.FieldValue.arrayUnion(code) });
                await db.collection("requests").add({ user: user.name, amount: val, type: "PROMO: "+code, status: "approved", time: Date.now() });
                alert("Bonus Applied! 🎁"); document.getElementById('promo-input').value = '';
            } else { alert("Invalid Code!"); }
        }

        async function createPromo() {
            const c = document.getElementById('new-code').value.trim().toUpperCase(); const v = parseInt(document.getElementById('new-val').value);
            if(c && v) { await db.collection("promocodes").doc(c).set({ value: v }); alert("Promo " + c + " created!"); }
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value; const m = document.getElementById('wd-method').value; const n = document.getElementById('wd-acc-name').value; const num = document.getElementById('wd-acc-num').value;
            if(!a || !n || !num) return alert("Fill all!");
            if(parseInt(a) > user.balance) return alert("Low Balance!");
            const detail = `🏦 ${m} | 👤 ${n} | 💳 ${num}`;
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: detail, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) });
            alert("Requested! 😘"); changePage('activity');
        }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { 
                    const d = doc.data(); 
                    list.innerHTML += `<div class="glass-light p-4 rounded-xl mb-2 text-[9px] font-black">
                        ${d.user} - Rs ${d.amount}<br>${d.acc || d.type}<br>
                        <button onclick="db.collection('requests').doc('${doc.id}').update({status:'approved'})" class="text-green-600 mr-4 font-bold">APPROVE</button>
                        <button onclick="db.collection('requests').doc('${doc.id}').update({status:'rejected'})" class="text-rose-600 font-bold">REJECT</button>
                    </div>`;
                });
            });
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('nav button').forEach(b=>b.classList.add('opacity-40')); document.getElementById('p-'+p).classList.add('active-page'); document.getElementById('n-'+p).classList.remove('opacity-40'); }
        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Pass:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function showAdmTab(t) { document.querySelectorAll('.adm-tab').forEach(s=>s.classList.add('hidden')); document.getElementById('adm-sec-'+t).classList.remove('hidden'); }
        async function sendBroadcast() { const m = document.getElementById('bc-msg').value; await db.collection("app_data").doc("announcement").set({ message: m }); alert("Updated!"); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        async function submitDeposit() { /* Standard deposit function logic */ alert("Deposit request sent!"); }
    </script>
</body>
</html>
