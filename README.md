<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Overlord v23.0</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 100px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); backdrop-filter: blur(15px); }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #2563eb, #3b82f6); color: white; border-radius: 18px; font-weight: 800; border: none; cursor: pointer; transition: 0.3s; }
        input, select { background: #161b22; border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 14px; width: 100%; outline: none; font-size: 13px; margin-bottom: 10px; }
        .admin-section { background: rgba(37, 99, 235, 0.05); border: 1px solid rgba(37, 99, 235, 0.1); border-radius: 24px; padding: 16px; margin-bottom: 20px; }
        .ban-overlay { position: fixed; inset: 0; background: #000; z-index: 999999; display: flex; flex-direction: column; items: center; justify-content: center; text-align: center; padding: 20px; }
        .u-row { transition: 0.2s; } .u-row:active { background: rgba(255,255,255,0.05); }
    </style>
</head>
<body>

    <div id="ban-screen" class="hidden ban-overlay">
        <i class="fa-solid fa-user-slash text-red-600 text-6xl mb-4"></i>
        <h1 class="text-2xl font-black uppercase text-red-500">Account Banned</h1>
        <p class="opacity-50 text-[12px] italic mt-2">Access denied by the Administrator. 😘</p>
    </div>

    <div id="maint-screen" class="hidden ban-overlay bg-indigo-950">
        <i class="fa-solid fa-wrench text-blue-500 text-6xl mb-4 animate-bounce"></i>
        <h1 class="text-2xl font-black uppercase text-white">Maintenance</h1>
        <p class="opacity-50 text-[12px] italic mt-2">Upgrading the system for you, sweetie! 😘</p>
    </div>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/90 backdrop-blur-xl border-b border-[var(--border)] p-4 flex justify-between items-center">
        <div class="flex items-center gap-3">
            <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg cursor-pointer">M</div>
            <div>
                <h1 class="text-[12px] font-black uppercase">MintCrest <span class="text-blue-500">Gold</span></h1>
                <p id="display-user" class="text-[8px] font-bold text-blue-400 uppercase italic">@User</p>
            </div>
        </div>
        <div class="flex items-center gap-3">
            <div id="top-bal" class="text-[10px] font-black bg-blue-600/10 text-blue-500 px-4 py-2 rounded-full border border-blue-500/20">₨ 0</div>
            <button onclick="logout()" class="w-9 h-9 rounded-2xl bg-red-600/10 text-red-500 border border-red-500/20 flex items-center justify-center"><i class="fa-solid fa-power-off"></i></button>
        </div>
    </header>

    <main id="app-ui" class="hidden p-4 space-y-6">
        <div id="p-home" class="page active-page space-y-6">
            <div id="global-notice-bar" class="hidden glass p-3 rounded-2xl text-[10px] font-black uppercase text-blue-400 border-blue-500/20 italic"><i class="fa-solid fa-bolt mr-2"></i> <span id="ntc-txt"></span></div>
            
            <div class="p-8 rounded-[3rem] bg-gradient-to-br from-blue-700 to-indigo-950 text-white shadow-2xl relative overflow-hidden">
                <div class="absolute -top-10 -right-10 w-32 h-32 bg-white/10 rounded-full blur-3xl"></div>
                <p class="text-[10px] font-black uppercase opacity-60">Your Balance</p>
                <h2 class="text-5xl font-black tracking-tighter my-2" id="v-bal">₨ 0.00</h2>
                <div class="mt-6 flex gap-3">
                   <button onclick="changePage('wallet')" class="flex-1 bg-white/10 p-3 rounded-2xl text-[10px] font-black uppercase">Deposit</button>
                   <button onclick="changePage('withdraw')" class="flex-1 bg-blue-400/20 p-3 rounded-2xl text-[10px] font-black uppercase">Withdraw</button>
                </div>
            </div>

            <div class="glass p-5 rounded-[2.5rem] flex gap-2">
                <input id="p-code-in" placeholder="PROMO CODE" class="m-0 text-[10px] font-black uppercase border-blue-500/10">
                <button onclick="applyPromo()" class="bg-blue-600 px-6 rounded-xl text-[10px] font-black uppercase italic">Apply</button>
            </div>

            <div id="plans-grid" class="grid grid-cols-1 gap-4 pb-24"></div>
        </div>

        <div id="p-history" class="page space-y-4 pb-24 text-center"><h3 class="font-black uppercase text-blue-500 italic">Statement Logs</h3><div id="user-logs-list" class="space-y-3"></div></div>

        <div id="p-wallet" class="page"><div class="glass p-8 rounded-[3.5rem] space-y-6 text-center">
            <h3 class="font-black uppercase text-blue-500 italic">Add Funds</h3>
            <div id="qr-area" class="hidden p-4 bg-white rounded-3xl mx-auto w-fit mb-4"><img id="qr-img" class="w-44 h-44 shadow-lg"></div>
            <div class="flex gap-2"><button onclick="showQR('Jazz')" class="flex-1 glass p-4 rounded-2xl text-[10px] font-black uppercase border-blue-500/20">JazzCash</button><button onclick="showQR('Easy')" class="flex-1 glass p-4 rounded-2xl text-[10px] font-black uppercase border-blue-500/20">EasyPaisa</button></div>
            <div class="p-3 bg-blue-600/5 rounded-xl border border-blue-500/10"><p class="text-[10px] uppercase opacity-40 mb-1">Send to Number</p><h4 id="pay-num" class="text-lg font-black text-blue-400 italic">Select Method</h4></div>
            <input id="d-amt" type="number" placeholder="Amount (Min 200)"><input id="d-tid" placeholder="TID Number"><button onclick="sendDeposit()" class="w-full p-4 btn-prime text-[11px] font-black uppercase italic">Submit for Verification</button>
        </div></div>

        <div id="p-withdraw" class="page"><div class="glass p-8 rounded-[3.5rem] space-y-6 text-center">
            <h3 class="font-black uppercase text-blue-500 italic">Withdrawal</h3>
            <input id="w-amt" type="number" placeholder="Amount (Min 100)"><input id="w-acc" placeholder="Account Number"><button onclick="sendWithdraw()" class="w-full p-4 btn-prime text-[11px] font-black uppercase italic">Request Funds</button>
        </div></div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6 pb-32">
        <div class="flex justify-between items-center mb-6">
            <div><h2 class="text-xl font-black text-blue-500 italic uppercase">Admin Hub</h2><p class="text-[8px] font-black opacity-40 uppercase">Total Members: <span id="adm-count" class="text-green-500">0</span></p></div>
            <button onclick="closeAdmin()" class="bg-red-600/10 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black uppercase border border-red-500/20">EXIT</button>
        </div>

        <div class="admin-section">
            <h4 class="text-[10px] font-black uppercase text-red-500 mb-4">System Power</h4>
            <div class="flex gap-2">
                <button onclick="toggleMaint(true)" class="flex-1 bg-red-600/10 border border-red-600/20 p-3 rounded-xl text-[9px] font-black uppercase">Start Maintenance</button>
                <button onclick="toggleMaint(false)" class="flex-1 bg-green-600/10 border border-green-600/20 p-3 rounded-xl text-[9px] font-black uppercase">End Maintenance</button>
            </div>
            <input id="adm-notice" placeholder="Global Notice Message" class="mt-4 text-[11px]">
            <button onclick="saveNotice()" class="w-full p-3 bg-white/5 border border-white/20 rounded-xl text-[9px] font-black uppercase">Update Notice</button>
        </div>

        <div class="admin-section">
            <h4 class="text-[10px] font-black uppercase text-blue-400 mb-4 flex justify-between">User Management <i class="fa-solid fa-magnifying-glass"></i></h4>
            <input id="u-search" onkeyup="filterUsers()" placeholder="Search username..." class="text-[11px]">
            <div id="adm-user-list" class="space-y-1 max-h-64 overflow-y-auto pr-2"></div>
        </div>

        <div class="admin-section">
            <h4 class="text-[10px] font-black uppercase text-purple-400 mb-4">Create Investment Node</h4>
            <input id="pl-name" placeholder="Plan Name (e.g. Starter)">
            <div class="flex gap-2"><input id="pl-price" type="number" placeholder="Price"><input id="pl-profit" type="number" placeholder="Daily %"></div>
            <button onclick="createPlan()" class="w-full p-3 bg-purple-600/20 text-purple-400 rounded-xl text-[9px] font-black uppercase">Build Plan</button>
        </div>

        <div class="admin-section">
            <h4 class="text-[10px] font-black uppercase text-yellow-500 mb-4">Payments & Promo Codes</h4>
            <div class="flex gap-2"><input id="adm-jqr" placeholder="Jazz QR Link"><input id="adm-eqr" placeholder="Easy QR Link"></div>
            <div class="flex gap-2"><input id="adm-jnum" placeholder="Jazz Number"><input id="adm-enum" placeholder="Easy Number"></div>
            <button onclick="savePaymentSettings()" class="w-full p-3 btn-prime text-[9px] font-black mb-6">Save Methods</button>
            
            <input id="adm-pcode" placeholder="Promo Code Name"><input id="adm-pval" type="number" placeholder="Value (PKR)">
            <button onclick="addPromo()" class="w-full p-3 bg-yellow-600/20 text-yellow-500 rounded-xl text-[9px] font-black uppercase">Add Promo Code</button>
        </div>

        <h4 class="text-[11px] font-black uppercase text-green-500 mb-4 px-2 italic">Requests Queue</h4>
        <div id="adm-req-list" class="space-y-4"></div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8">
        <div class="w-20 h-20 bg-blue-600 rounded-[2rem] mb-12 flex items-center justify-center text-5xl font-black italic text-white shadow-2xl shadow-blue-600/30">M</div>
        <input id="l-name" placeholder="YOUR USERNAME" class="max-w-[320px] text-center font-black uppercase italic tracking-widest border-blue-500/20">
        <button onclick="login()" class="w-full max-w-[320px] p-5 btn-prime uppercase text-[12px] font-black tracking-widest shadow-2xl transition-all active:scale-95">Unlock Portal</button>
    </section>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-5 flex justify-around items-center rounded-t-[3.5rem] z-[4000]">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-cube"></i><span class="text-[7px] font-black uppercase">Nodes</span></button>
        <button onclick="changePage('wallet')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-plus-circle text-blue-500"></i><span class="text-[7px] font-black uppercase">Funding</span></button>
        <button onclick="changePage('history')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-clock-rotate-left"></i><span class="text-[7px] font-black uppercase">History</span></button>
    </nav>

    <script>
        const firebaseConfig = { 
            apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", 
            authDomain: "investment-84f4e.firebaseapp.com", 
            projectId: "investment-84f4e", 
            storageBucket: "investment-84f4e.firebasestorage.app", 
            messagingSenderId: "975293889308", 
            appId: "1:975293889308:web:6d034a99cc966c75ff58d9" 
        };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let tapCount = 0; let settings = {};

        function login() {
            const n = document.getElementById('l-name').value.trim().toLowerCase();
            if(n.length < 3) return alert("Enter valid name! 😘");
            localStorage.setItem('mc_session', n);
            enterApp(n);
        }

        async function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('main-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-user').innerText = "@" + n.toUpperCase();

            db.collection("settings").doc("global").onSnapshot(d => {
                settings = d.data() || {};
                if(settings.maintenance) document.getElementById('maint-screen').classList.remove('hidden');
                else document.getElementById('maint-screen').classList.add('hidden');
                if(settings.notice) {
                    document.getElementById('global-notice-bar').classList.remove('hidden');
                    document.getElementById('ntc-txt').innerText = settings.notice;
                } else document.getElementById('global-notice-bar').classList.add('hidden');
            });

            db.collection("users").doc(n).onSnapshot(doc => {
                if(!doc.exists) return db.collection("users").doc(n).set({ name: n, balance: 0, status: 'active', joined: Date.now() });
                user = doc.data();
                if(user.status === 'banned') document.getElementById('ban-screen').classList.remove('hidden');
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            });

            syncPlans(); syncLogs();
        }

        // --- ADMINISTRATION ---
        async function toggleMaint(v) { await db.collection("settings").doc("global").update({ maintenance: v }); }
        async function toggleBan(u, s) { await db.collection("users").doc(u).update({ status: s === 'active' ? 'banned' : 'active' }); }
        async function saveNotice() { await db.collection("settings").doc("global").update({ notice: document.getElementById('adm-notice').value }); alert("Notice Updated! 😘"); }
        async function createPlan() {
            const n = document.getElementById('pl-name').value; const p = parseInt(document.getElementById('pl-price').value); const pr = parseInt(document.getElementById('pl-profit').value);
            await db.collection("plans").add({ name: n, price: p, profit: pr }); alert("Node Created! 😘");
        }
        async function savePaymentSettings() {
            await db.collection("settings").doc("global").update({ 
                jazzQR: document.getElementById('adm-jqr').value, easyQR: document.getElementById('adm-eqr').value,
                jazzNum: document.getElementById('adm-jnum').value, easyNum: document.getElementById('adm-enum').value
            }); alert("Payments Updated! 😘");
        }
        async function addPromo() {
            const c = document.getElementById('adm-pcode').value.toUpperCase(); const v = parseInt(document.getElementById('adm-pval').value);
            await db.collection("promos").doc(c).set({ value: v, usedBy: [] }); alert("Promo Added! 😘");
        }

        function filterUsers() {
            const q = document.getElementById('u-search').value.toLowerCase();
            document.querySelectorAll('.u-row').forEach(r => { r.style.display = r.getAttribute('data-name').includes(q) ? 'flex' : 'none'; });
        }

        async function syncAdm() {
            db.collection("users").onSnapshot(s => {
                document.getElementById('adm-count').innerText = s.size;
                const l = document.getElementById('adm-user-list'); l.innerHTML = '';
                s.forEach(doc => {
                    const u = doc.data();
                    l.innerHTML += `<div class="u-row flex justify-between items-center p-3 border-b border-white/5 uppercase text-[9px] font-bold" data-name="${u.name}">
                        <span>${u.name} (₨ ${u.balance})</span>
                        <div class="flex gap-2">
                            <button onclick="toggleBan('${u.name}', '${u.status}')" class="${u.status==='active'?'text-red-500':'text-green-500'} font-black uppercase text-[7px]">${u.status==='active'?'BAN':'UNBAN'}</button>
                            <button onclick="db.collection('users').doc('${u.name}').update({balance: parseInt(prompt('Set New Balance:'))})" class="text-blue-500 text-[12px]"><i class="fa-solid fa-pen-to-square"></i></button>
                        </div>
                    </div>`;
                });
            });
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-req-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-3xl border-l-4 border-yellow-500 mb-2">
                        <p class="text-[9px] font-black uppercase text-blue-400">${r.user} | ${r.type}</p>
                        <h4 class="text-xl font-black">₨ ${r.amount}</h4>
                        <p class="text-[8px] opacity-40 font-bold">${r.tid || r.acc}</p>
                        <button onclick="approveReq('${doc.id}', '${r.user}', ${r.amount}, '${r.type}')" class="w-full bg-blue-600 p-2 rounded-xl text-[9px] font-black uppercase mt-3 shadow-lg">Approve</button>
                    </div>`;
                });
            });
        }

        async function approveReq(id, u, a, t) {
            if(t==='Deposit') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").doc(id).update({ status: 'approved' });
            await db.collection("logs").doc(id).update({ status: 'approved' });
            alert("Done! 😘");
        }

        // --- USER SYSTEM ---
        async function applyPromo() {
            const c = document.getElementById('p-code-in').value.toUpperCase();
            const d = await db.collection("promos").doc(c).get();
            if(!d.exists) return alert("Wrong Code! 😘");
            const data = d.data();
            if(data.usedBy.includes(user.name)) return alert("Used Already! 😘");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(data.value) });
            await db.collection("promos").doc(c).update({ usedBy: firebase.firestore.FieldValue.arrayUnion(user.name) });
            alert(`Sweetie, ₨ ${data.value} Gifted! 😘`);
        }

        async function buyPlan(id, price, name) {
            if(user.balance < price) return alert("Low Funds! 😘");
            if(confirm(`Activate ${name}?`)) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-price) });
                await db.collection("logs").add({ user: user.name, msg: `Node Active: ${name}`, status: 'approved', time: Date.now() });
                alert("Success! 🚀");
            }
        }

        async function sendDeposit() {
            const a = parseInt(document.getElementById('d-amt').value); const t = document.getElementById('d-tid').value;
            if(!a || a < 200 || !t) return alert("Min 200 + TID! 😘");
            const id = Date.now().toString();
            await db.collection("requests").doc(id).set({ user: user.name, amount: a, tid: t, status: 'pending', type: 'Deposit', time: Date.now() });
            await db.collection("logs").doc(id).set({ user: user.name, msg: `Deposit: ₨ ${a}`, status: 'pending', time: Date.now() });
            alert("Wait for verification! 😘");
        }

        async function sendWithdraw() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value;
            if(!a || a < 100 || user.balance < a) return alert("Check Balance! 😘");
            const id = Date.now().toString();
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").doc(id).set({ user: user.name, amount: a, acc: acc, status: 'pending', type: 'Withdraw', time: Date.now() });
            await db.collection("logs").doc(id).set({ user: user.name, msg: `Withdraw: ₨ ${a}`, status: 'pending', time: Date.now() });
            alert("Requested! 😘");
        }

        // HELPERS
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function showQR(t) { 
            document.getElementById('qr-area').classList.remove('hidden'); 
            document.getElementById('qr-img').src = (t==='Jazz'?settings.jazzQR:settings.easyQR);
            document.getElementById('pay-num').innerText = (t==='Jazz'?settings.jazzNum:settings.easyNum);
        }
        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => { const p = doc.data(); g.innerHTML += `<div class="glass p-6 rounded-[2.8rem] flex justify-between items-center border-l-4 border-blue-500 shadow-xl"><div><p class="text-[9px] font-black opacity-40 uppercase">${p.name}</p><h4 class="text-2xl font-black italic">₨ ${p.price}</h4><p class="text-[8px] font-bold text-blue-400">${p.profit}% Profit</p></div><button onclick="buyPlan('${doc.id}', ${p.price}, '${p.name}')" class="btn-prime px-7 py-4 text-[11px] font-black uppercase">Activate</button></div>`; });
            });
        }
        function syncLogs() {
            db.collection("logs").where("user", "==", user.name).orderBy("time", "desc").limit(10).onSnapshot(s => {
                const l = document.getElementById('user-logs-list'); l.innerHTML = '';
                s.forEach(doc => { const d = doc.data(); l.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center border-l-2 ${d.status==='approved'?'border-green-500':'border-yellow-500'}"><p class="text-[10px] font-black uppercase">${d.msg}</p><span class="text-[8px] font-black uppercase ${d.status==='approved'?'text-green-500':'text-yellow-500'}">${d.status}</span></div>`; });
            });
        }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); window.scrollTo(0,0); }
        function logout() { localStorage.removeItem('mc_session'); location.reload(); }
        window.onload = () => { const s = localStorage.getItem('mc_session'); if(s) enterApp(s); }
    </script>
</body>
</html>
