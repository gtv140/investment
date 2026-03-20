<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Pro Node v8.0</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 110px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); backdrop-filter: blur(12px); }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 20px; font-weight: 800; transition: 0.3s; border: none; cursor: pointer; }
        .btn-prime:active { transform: scale(0.95); }
        input, select, textarea { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 16px; border-radius: 16px; width: 100%; outline: none; font-size: 13px; margin-bottom: 10px; }
        .animate-marquee { display: inline-block; animation: marquee 20s linear infinite; white-space: nowrap; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .status-pending { color: #fbbf24; background: rgba(251, 191, 36, 0.1); }
        .status-approved { color: #10b981; background: rgba(16, 185, 129, 0.1); }
    </style>
</head>
<body>

    <div id="broadcast-ui" class="fixed inset-x-0 top-16 z-[6000] p-4 hidden">
        <div class="glass border-blue-500/50 p-4 rounded-2xl flex items-center gap-3 shadow-2xl">
            <span class="text-xl">📢</span>
            <p id="bc-msg" class="text-[11px] font-bold uppercase italic text-blue-400"></p>
            <button onclick="document.getElementById('broadcast-ui').style.display='none'" class="ml-auto opacity-50">✕</button>
        </div>
    </div>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)]">
        <div class="p-4 flex justify-between items-center">
            <div class="flex items-center gap-2">
                <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg cursor-pointer">M</div>
                <div><h1 class="text-[12px] font-black uppercase">MintCrest <span class="text-blue-500">Gold</span></h1><p id="display-user" class="text-[8px] font-bold text-blue-400 uppercase tracking-widest italic">@Guest</p></div>
            </div>
            <div id="top-bal" class="text-[11px] font-black bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full border border-blue-500/20 italic">₨ 0</div>
        </div>
    </header>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-8 rounded-[3.5rem] bg-gradient-to-br from-blue-600 to-indigo-900 text-white shadow-2xl relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-60 italic">Mining Wallet</p>
                <h2 class="text-5xl font-black tracking-tighter my-2" id="v-bal">₨ 0.00</h2>
                <div class="mt-6 flex gap-3">
                   <button onclick="claimDaily()" id="daily-btn" class="flex-1 bg-white/10 p-3 rounded-xl border border-white/10 text-[9px] font-black uppercase"><i class="fa-solid fa-gift"></i> Daily Bonus</button>
                   <button onclick="logout()" class="flex-1 bg-red-500/10 p-3 rounded-xl border border-red-500/20 text-[9px] font-black uppercase text-red-400">Exit</button>
                </div>
            </div>

            <div class="glass p-5 rounded-[2.5rem] border-blue-500/20">
                <div class="flex gap-2 mb-3">
                    <input type="text" id="promo-input" placeholder="PROMO CODE" class="mb-0 uppercase font-black">
                    <button onclick="applyPromo()" class="btn-prime px-6 text-[10px] uppercase">Apply</button>
                </div>
                <div class="text-center bg-blue-600/5 p-3 rounded-2xl">
                    <p class="text-[7px] font-black uppercase opacity-40">Your Invite ID</p>
                    <p id="my-ref-id" class="text-lg font-black text-blue-400 tracking-widest italic">-----</p>
                </div>
            </div>

            <h3 class="text-[11px] font-black uppercase text-blue-500 px-2 italic">Mining Nodes</h3>
            <div id="plans-grid" class="grid grid-cols-1 gap-4 pb-20"></div>
        </div>

        <div id="p-history" class="page space-y-6">
            <h3 class="text-xl font-black text-center uppercase text-blue-500 italic">Financial Activity</h3>
            <div class="flex justify-around mb-4 bg-blue-600/5 p-2 rounded-2xl">
                <button onclick="filterHistory('all')" class="text-[9px] font-black uppercase">All</button>
                <button onclick="filterHistory('finance')" class="text-[9px] font-black uppercase">Finances</button>
                <button onclick="filterHistory('profit')" class="text-[9px] font-black uppercase">Profits</button>
            </div>
            <div id="user-logs" class="space-y-3 pb-20">
                </div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] space-y-4">
                <h3 class="text-center font-black uppercase text-blue-500"><i class="fa-solid fa-building-columns"></i> Deposit</h3>
                <input type="number" id="dep-amt" placeholder="Amount">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                <button onclick="sendDeposit()" class="w-full p-4 btn-prime text-[11px] uppercase">Submit Deposit</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-5 flex justify-around items-center rounded-t-[3.5rem] z-[4000]">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-house"></i><span class="text-[7px] font-black uppercase">Home</span></button>
        <button onclick="changePage('wallet')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-wallet"></i><span class="text-[7px] font-black uppercase">Wallet</span></button>
        <button onclick="changePage('history')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-clock-rotate-left"></i><span class="text-[7px] font-black uppercase">Logs</span></button>
    </nav>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6 pb-20">
        <div class="flex justify-between items-center mb-8 border-b border-blue-500/20 pb-4">
            <h2 class="text-xl font-black text-blue-500 uppercase italic"><i class="fa-solid fa-shield-halved"></i> Master Console</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-4 py-2 rounded-xl text-[10px] font-black uppercase">Exit</button>
        </div>

        <div class="glass p-6 rounded-3xl mb-8 space-y-4 border-blue-500/30">
            <h4 class="text-[10px] font-black uppercase text-blue-400 italic"><i class="fa-solid fa-bullhorn"></i> Broadcast Engine</h4>
            <textarea id="adm-bc-text" placeholder="Type global broadcast message sweetie..." rows="2"></textarea>
            <button onclick="sendBroadcast()" class="w-full p-3 btn-prime text-[9px] uppercase">Fire Broadcast</button>
        </div>

        <div class="grid grid-cols-2 gap-4 mb-8">
            <div class="glass p-4 rounded-3xl text-center"><i class="fa-solid fa-users text-blue-500 mb-1"></i><p class="text-[8px] font-black opacity-40 uppercase">Total Users</p><h5 id="stat-users" class="font-black italic">--</h5></div>
            <div class="glass p-4 rounded-3xl text-center"><i class="fa-solid fa-circle-check text-green-500 mb-1"></i><p class="text-[8px] font-black opacity-40 uppercase">Approved</p><h5 id="stat-req" class="font-black italic">--</h5></div>
        </div>

        <h4 class="text-[10px] font-black uppercase text-yellow-500 italic mb-4"><i class="fa-solid fa-spinner"></i> Waiting Approvals</h4>
        <div id="adm-req-list" class="space-y-4 mb-8"></div>

        <div class="glass p-6 rounded-3xl mb-8 space-y-4">
            <h4 class="text-[10px] font-black uppercase text-blue-400 italic"><i class="fa-solid fa-ticket"></i> Promo Engine</h4>
            <div class="flex gap-2"><input id="adm-p-code" placeholder="CODE" class="mb-0"><input id="adm-p-val" placeholder="₨" class="mb-0"></div>
            <button onclick="adminAddPromo()" class="w-full p-3 btn-prime text-[9px] uppercase italic">Create Promo</button>
        </div>
    </div>

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
        let user = null; let tapCount = 0;

        async function login() {
            const n = document.getElementById('user-login-name').value.trim().toLowerCase();
            if(!n) return;
            localStorage.setItem('mc_user', n);
            enterApp(n);
        }

        async function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('main-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-user').innerText = "@" + n.toUpperCase();
            document.getElementById('my-ref-id').innerText = n.toUpperCase();

            db.collection("users").doc(n).onSnapshot(doc => {
                if(!doc.exists) return db.collection("users").doc(n).set({ name: n, balance: 0, lastDaily: 0 });
                user = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            });

            syncLogs(n);
            syncPlans();
            listenBroadcast();
        }

        // HISTORY SYSTEM
        function syncLogs(n) {
            db.collection("logs").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const l = document.getElementById('user-logs'); l.innerHTML = '';
                s.forEach(doc => {
                    const d = doc.data();
                    const statusClass = d.status === 'pending' ? 'status-pending' : 'status-approved';
                    l.innerHTML += `<div class="glass p-4 rounded-2xl border-l-4 ${d.type==='profit'?'border-green-500':'border-blue-500'}">
                        <div class="flex justify-between items-center">
                            <div><p class="text-[10px] font-black uppercase">${d.msg}</p><p class="text-[7px] opacity-40">${new Date(d.time).toLocaleString()}</p></div>
                            ${d.status ? `<span class="px-2 py-1 rounded-full text-[7px] font-black uppercase ${statusClass}">${d.status}</span>` : ''}
                        </div>
                    </div>`;
                });
            });
        }

        async function sendDeposit() {
            const a = document.getElementById('dep-amt').value; const t = document.getElementById('dep-tid').value;
            if(!a || !t) return;
            const logId = Date.now().toString();
            await db.collection("requests").doc(logId).set({ user: user.name, amount: parseInt(a), tid: t, status: 'pending', type: 'Deposit', time: Date.now() });
            await db.collection("logs").doc(logId).set({ user: user.name, msg: `Deposit: ₨ ${a}`, status: 'pending', type: 'finance', time: Date.now() });
            alert("Deposit submitted sweetie! 😘");
        }

        // ADMINISTRATION SYSTEM
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("God Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        async function syncAdm() {
            db.collection("users").get().then(s => document.getElementById('stat-users').innerText = s.size);
            db.collection("requests").where("status","==","approved").get().then(s => document.getElementById('stat-req').innerText = s.size);
            
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-req-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-2xl border-l-4 border-yellow-500">
                        <p class="text-[10px] font-black uppercase">${r.user} - ₨ ${r.amount}</p>
                        <p class="text-[8px] opacity-50 mb-3 italic">TID: ${r.tid}</p>
                        <div class="flex gap-2">
                            <button onclick="approveReq('${doc.id}', '${r.user}', ${r.amount})" class="flex-1 bg-blue-600 p-2 rounded-xl text-[8px] font-black uppercase">Approve</button>
                            <button onclick="db.collection('requests').doc('${doc.id}').delete()" class="p-2 text-red-500"><i class="fa-solid fa-trash"></i></button>
                        </div>
                    </div>`;
                });
            });
        }

        async function approveReq(id, u, a) {
            await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").doc(id).update({ status: 'approved' });
            await db.collection("logs").doc(id).update({ status: 'approved' });
            alert("Approved sweetie!");
        }

        async function sendBroadcast() {
            const msg = document.getElementById('adm-bc-text').value;
            if(!msg) return;
            await db.collection("settings").doc("broadcast").set({ text: msg, time: Date.now() });
            alert("Broadcast Fired! 🔥");
        }

        function listenBroadcast() {
            db.collection("settings").doc("broadcast").onSnapshot(d => {
                if(d.exists) {
                    const data = d.data();
                    if(Date.now() - data.time < 600000) { // 10 mins active
                        document.getElementById('broadcast-ui').style.display = 'block';
                        document.getElementById('bc-msg').innerText = data.text;
                    }
                }
            });
        }

        async function adminAddPromo() {
            const c = document.getElementById('adm-p-code').value.toUpperCase();
            const v = parseInt(document.getElementById('adm-p-val').value);
            if(c && v) await db.collection("promos").doc(c).set({ reward: v });
            alert("Promo Created!");
        }

        async function applyPromo() {
            const code = document.getElementById('promo-input').value.toUpperCase();
            const d = await db.collection("promos").doc(code).get();
            if(!d.exists) return alert("Wrong Code!");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(d.data().reward) });
            await db.collection("logs").add({ user: user.name, msg: `Promo: ${code} (+₨ ${d.data().reward})`, type: 'profit', time: Date.now() });
            alert("Sweetie, bonus added! 😘");
        }

        async function claimDaily() {
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(20), lastDaily: Date.now() });
            await db.collection("logs").add({ user: user.name, msg: `Daily Reward: ₨ 20`, type: 'profit', time: Date.now() });
            alert("Daily bonus claimed!");
        }

        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    g.innerHTML += `<div class="glass p-6 rounded-[2.5rem] border-l-4 border-blue-500">
                        <div class="flex justify-between items-center mb-4">
                            <div><p class="text-[9px] font-black uppercase opacity-40">${p.name}</p><h4 class="text-xl font-black">₨ ${p.price}</h4></div>
                            <span class="text-blue-500 text-[9px] font-black uppercase italic">${p.profit}% Daily</span>
                        </div>
                        <button onclick="buyNode('${doc.id}', ${p.price})" class="w-full btn-prime p-4 text-[10px] uppercase font-black tracking-widest">Activate Node</button>
                    </div>`;
                });
            });
        }

        async function buyNode(id, pr) {
            if(user.balance < pr) return alert("Insufficient balance!");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-pr) });
            await db.collection("logs").add({ user: user.name, msg: `Node Active: ₨ ${pr}`, type: 'finance', time: Date.now() });
            alert("Mining started sweetie!");
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function logout() { localStorage.clear(); location.reload(); }
        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user')); }
    </script>
</body>
</html>
