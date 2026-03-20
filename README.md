<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Goddess v16.0</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 100px; }
        .glass { background: var(--card); border: 1px solid var(--border); backdrop-filter: blur(15px); }
        .page { display: none; animation: fadeIn 0.3s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
        .btn-prime { background: linear-gradient(135deg, #2563eb, #3b82f6); color: white; border-radius: 16px; font-weight: 800; border: none; cursor: pointer; transition: 0.3s; }
        .btn-prime:active { transform: scale(0.95); }
        input, select, textarea { background: #161b22; border: 1px solid var(--border); color: var(--text); padding: 12px; border-radius: 12px; width: 100%; outline: none; font-size: 12px; margin-bottom: 8px; }
        .admin-section { background: rgba(37, 99, 235, 0.05); border: 1px solid rgba(37, 99, 235, 0.1); border-radius: 24px; padding: 16px; margin-bottom: 20px; }
        .user-row { border-bottom: 1px solid rgba(255,255,255,0.05); padding: 12px 0; }
        .user-row:last-child { border: none; }
    </style>
</head>
<body>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)] p-4 flex justify-between items-center">
        <div class="flex items-center gap-2">
            <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg cursor-pointer">M</div>
            <div><h1 class="text-[12px] font-black uppercase">MintCrest <span class="text-blue-500">Gold</span></h1><p id="display-user" class="text-[8px] font-bold text-blue-400 uppercase italic">@Verified</p></div>
        </div>
        <div id="top-bal" class="text-[11px] font-black bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full border border-blue-500/20 italic">₨ 0</div>
    </header>

    <main id="app-ui" class="hidden p-4 space-y-6">
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-8 rounded-[3rem] bg-gradient-to-br from-blue-700 to-indigo-950 text-white shadow-2xl relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-60 italic">Available Balance</p>
                <h2 class="text-5xl font-black tracking-tighter my-2" id="v-bal">₨ 0.00</h2>
                <div class="mt-6 flex gap-3">
                   <button onclick="changePage('wallet')" class="flex-1 bg-white/10 p-3 rounded-xl text-[9px] font-black uppercase italic">Deposit</button>
                   <button onclick="changePage('withdraw')" class="flex-1 bg-blue-400/20 p-3 rounded-xl text-[9px] font-black uppercase italic">Withdraw</button>
                </div>
            </div>
            <div id="plans-grid" class="grid grid-cols-1 gap-4 pb-20"></div>
        </div>

        <div id="p-history" class="page space-y-4 pb-20">
            <h3 class="text-center font-black uppercase text-blue-500 italic"><i class="fa-solid fa-receipt mr-2"></i>Transaction Logs</h3>
            <div id="user-logs-list" class="space-y-3"></div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] space-y-4">
                <h3 class="text-center font-black uppercase text-blue-500 italic"><i class="fa-solid fa-wallet mr-2"></i>Deposit Hub</h3>
                <div id="qr-area" class="hidden text-center p-4 bg-white rounded-2xl"><img id="qr-img" class="w-40 h-40 mx-auto"></div>
                <div class="flex gap-2">
                    <button onclick="showQR('Jazz')" class="flex-1 glass p-3 rounded-xl text-[10px] font-black">JAZZCASH</button>
                    <button onclick="showQR('Easy')" class="flex-1 glass p-3 rounded-xl text-[10px] font-black">EASYPAISA</button>
                </div>
                <input id="d-amt" type="number" placeholder="Amount">
                <input id="d-tid" placeholder="Transaction ID">
                <button onclick="sendDeposit()" class="w-full p-4 btn-prime text-[11px] font-black uppercase italic italic">Verify Funding</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] space-y-4">
                <h3 class="text-center font-black uppercase text-blue-500 italic"><i class="fa-solid fa-money-bill-transfer mr-2"></i>Request Payout</h3>
                <input id="w-amt" type="number" placeholder="Amount (Min 500)">
                <input id="w-acc" placeholder="Account Number">
                <select id="w-method"><option value="JazzCash">JazzCash</option><option value="EasyPaisa">EasyPaisa</option></select>
                <button onclick="sendWithdraw()" class="w-full p-4 btn-prime text-[11px] font-black uppercase italic">Withdraw Now</button>
            </div>
        </div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6 pb-32">
        <div class="flex justify-between items-center mb-8 border-b border-white/5 pb-4">
            <h2 class="text-xl font-black text-blue-500 uppercase italic"><i class="fa-solid fa-shield-halved mr-2"></i>Goddess Console</h2>
            <button onclick="closeAdmin()" class="bg-red-600/20 text-red-500 px-5 py-2 rounded-xl text-[10px] font-black uppercase border border-red-500/30">Exit</button>
        </div>

        <div class="admin-section">
            <h4 class="text-[11px] font-black uppercase text-blue-400 mb-4 flex items-center gap-2"><i class="fa-solid fa-users"></i> Registered Users Data</h4>
            <div id="adm-user-list" class="space-y-0 max-h-[400px] overflow-y-auto pr-2">
                </div>
        </div>

        <div class="admin-section">
            <h4 class="text-[11px] font-black uppercase text-green-400 mb-4 flex items-center gap-2"><i class="fa-solid fa-pen-to-square"></i> Modify User Balance</h4>
            <div class="flex gap-2">
                <input id="adm-un" placeholder="Username" class="mb-0">
                <input id="adm-ub" type="number" placeholder="New Bal" class="mb-0">
            </div>
            <button onclick="updateUserBal()" class="w-full p-3 btn-prime text-[9px] uppercase mt-3">Apply Changes</button>
        </div>

        <div class="admin-section">
            <h4 class="text-[11px] font-black uppercase text-purple-400 mb-4 flex items-center gap-2"><i class="fa-solid fa-folder-plus"></i> System Management</h4>
            <div class="space-y-2">
                <input id="p-name" placeholder="Plan Name">
                <div class="flex gap-2"><input id="p-price" type="number" placeholder="Price"><input id="p-profit" type="number" placeholder="Profit %"></div>
                <button onclick="addPlan()" class="w-full p-3 bg-white/5 border border-white/10 rounded-xl text-[9px] font-black uppercase">Add New Node</button>
            </div>
            <div class="mt-4 pt-4 border-t border-white/5 space-y-2">
                <input id="adm-jqr" placeholder="JazzCash QR Link">
                <input id="adm-eqr" placeholder="EasyPaisa QR Link">
                <button onclick="saveQRs()" class="w-full p-3 bg-blue-600/20 text-blue-400 rounded-xl text-[9px] font-black uppercase">Save Wallet Links</button>
            </div>
        </div>

        <h4 class="text-[11px] font-black uppercase text-yellow-500 mb-4 italic flex items-center gap-2"><i class="fa-solid fa-bell"></i> Pending Approvals</h4>
        <div id="adm-req-list" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-5 flex justify-around items-center rounded-t-[3.5rem] z-[4000]">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-microchip"></i><span class="text-[7px] font-black uppercase">Mining</span></button>
        <button onclick="changePage('wallet')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-wallet"></i><span class="text-[7px] font-black uppercase">Funding</span></button>
        <button onclick="changePage('history')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-clock-rotate-left"></i><span class="text-[7px] font-black uppercase">History</span></button>
    </nav>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8">
        <div class="w-20 h-20 bg-blue-600 rounded-[2rem] mb-10 flex items-center justify-center text-5xl font-black italic text-white shadow-2xl">M</div>
        <input id="l-name" placeholder="ENTER USERNAME" class="max-w-[300px] text-center font-black uppercase">
        <button onclick="login()" class="w-full max-w-[300px] p-4 btn-prime uppercase text-[11px] tracking-widest">Authorize Session</button>
    </section>

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
        let user = null; let tapCount = 0; let qrLinks = { jazz: '', easy: '' };

        function login() {
            const n = document.getElementById('l-name').value.trim().toLowerCase();
            if(n) { localStorage.setItem('mc_user', n); enterApp(n); }
        }

        async function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('main-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-user').innerText = "@" + n.toUpperCase();

            db.collection("users").doc(n).onSnapshot(doc => {
                if(!doc.exists) return db.collection("users").doc(n).set({ name: n, balance: 0, joined: Date.now() });
                user = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            });

            db.collection("settings").doc("qrs").onSnapshot(d => { if(d.exists) qrLinks = d.data(); });

            syncPlans();
            syncLogs();
        }

        // --- ADMINISTRATION FUNCTIONS ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Goddess Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        async function syncAdm() {
            // Real-time Users List
            db.collection("users").onSnapshot(s => {
                const list = document.getElementById('adm-user-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const u = doc.data();
                    list.innerHTML += `<div class="user-row flex justify-between items-center">
                        <div class="flex flex-col">
                            <span class="text-[10px] font-black uppercase text-white">${u.name}</span>
                            <span class="text-[8px] text-blue-400 font-bold italic">Bal: ₨ ${u.balance.toLocaleString()}</span>
                        </div>
                        <div class="flex gap-2">
                            <button onclick="copyToForm('${u.name}', ${u.balance})" class="text-[8px] bg-white/5 px-3 py-1 rounded-md border border-white/10 uppercase font-black">Edit</button>
                        </div>
                    </div>`;
                });
            });

            // Pending Requests
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const l = document.getElementById('adm-req-list'); l.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    l.innerHTML += `<div class="glass p-4 rounded-2xl border-l-4 border-yellow-500 mb-2">
                        <p class="text-[9px] font-black text-blue-400 uppercase">${r.user} | ${r.type}</p>
                        <h4 class="text-xl font-black">₨ ${r.amount}</h4>
                        <p class="text-[8px] opacity-40 italic">Info: ${r.tid || r.acc}</p>
                        <div class="flex gap-2 mt-3"><button onclick="approveReq('${doc.id}', '${r.user}', ${r.amount}, '${r.type}')" class="flex-1 bg-blue-600 p-2 rounded-xl text-[9px] font-black uppercase">Approve</button><button onclick="db.collection('requests').doc('${doc.id}').delete()" class="p-2 text-red-500"><i class="fa-solid fa-trash"></i></button></div>
                    </div>`;
                });
            });
        }

        function copyToForm(n, b) { document.getElementById('adm-un').value = n; document.getElementById('adm-ub').value = b; }

        async function updateUserBal() {
            const u = document.getElementById('adm-un').value.toLowerCase();
            const b = parseInt(document.getElementById('adm-ub').value);
            await db.collection("users").doc(u).update({ balance: b });
            alert("User balance updated!");
        }

        async function addPlan() {
            const n = document.getElementById('p-name').value;
            const p = parseInt(document.getElementById('p-price').value);
            const pr = parseInt(document.getElementById('p-profit').value);
            if(n && p && pr) await db.collection("plans").add({ name: n, price: p, profit: pr });
            alert("Plan added!");
        }

        async function saveQRs() {
            const j = document.getElementById('adm-jqr').value;
            const e = document.getElementById('adm-eqr').value;
            await db.collection("settings").doc("qrs").set({ jazz: j, easy: e });
            alert("Wallet links saved!");
        }

        async function approveReq(id, u, a, type) {
            if(type === 'Deposit') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").doc(id).update({ status: 'approved' });
            await db.collection("logs").doc(id).set({ user: u, msg: `${type}: ₨ ${a}`, status: 'approved', time: Date.now() });
            alert("Success!");
        }

        // --- USER FEATURES ---
        function showQR(t) { document.getElementById('qr-area').classList.remove('hidden'); document.getElementById('qr-img').src = (t==='Jazz'?qrLinks.jazz:qrLinks.easy); }

        async function sendDeposit() {
            const a = document.getElementById('d-amt').value; const t = document.getElementById('d-tid').value;
            if(!a || !t) return;
            const id = Date.now().toString();
            await db.collection("requests").doc(id).set({ user: user.name, amount: parseInt(a), tid: t, status: 'pending', type: 'Deposit', time: Date.now() });
            await db.collection("logs").doc(id).set({ user: user.name, msg: `Deposit: ₨ ${a}`, status: 'pending', time: Date.now() });
            alert("Sent for verification!");
        }

        async function sendWithdraw() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value;
            if(a < 500 || user.balance < a) return alert("Check amount/balance!");
            const id = Date.now().toString();
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").doc(id).set({ user: user.name, amount: a, acc: acc, status: 'pending', type: 'Withdraw', time: Date.now() });
            await db.collection("logs").doc(id).set({ user: user.name, msg: `Withdraw: ₨ ${a}`, status: 'pending', time: Date.now() });
            alert("Withdrawal requested!");
        }

        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    g.innerHTML += `<div class="glass p-6 rounded-[2.5rem] flex justify-between items-center border-l-4 border-blue-500">
                        <div><p class="text-[9px] font-black opacity-40 uppercase">${p.name}</p><h4 class="text-xl font-black italic">₨ ${p.price}</h4><p class="text-[8px] font-bold text-blue-400">${p.profit}% Daily</p></div>
                        <button class="btn-prime px-6 py-3 text-[10px] uppercase font-black italic">Activate</button>
                    </div>`;
                });
            });
        }

        function syncLogs() {
            db.collection("logs").where("user", "==", user.name).orderBy("time", "desc").limit(20).onSnapshot(s => {
                const l = document.getElementById('user-logs-list'); l.innerHTML = '';
                s.forEach(doc => {
                    const d = doc.data();
                    l.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center border-l-2 border-blue-600">
                        <p class="text-[10px] font-black uppercase">${d.msg}</p>
                        <span class="text-[8px] font-black uppercase ${d.status==='pending'?'text-yellow-500':'text-green-500'}">${d.status}</span>
                    </div>`;
                });
            });
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); window.scrollTo(0,0); }
        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user')); }
    </script>
</body>
</html>
