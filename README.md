<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Master v13.0</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 110px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); backdrop-filter: blur(12px); }
        .page { display: none; animation: fadeIn 0.3s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 20px; font-weight: 800; border: none; cursor: pointer; transition: 0.2s; }
        .btn-prime:active { transform: scale(0.95); }
        input, select, textarea { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 16px; width: 100%; outline: none; font-size: 12px; margin-bottom: 10px; }
        .status-pill { font-size: 8px; font-weight: 900; padding: 4px 10px; border-radius: 20px; text-transform: uppercase; }
        .pending { background: rgba(245, 158, 11, 0.1); color: #f59e0b; }
        .approved { background: rgba(16, 185, 129, 0.1); color: #10b981; }
    </style>
</head>
<body>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)]">
        <div class="p-4 flex justify-between items-center">
            <div class="flex items-center gap-2">
                <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg cursor-pointer">M</div>
                <div><h1 class="text-[12px] font-black uppercase">MintCrest <span class="text-blue-500">Gold</span></h1><p id="display-user" class="text-[8px] font-bold text-blue-400 uppercase">@Verified</p></div>
            </div>
            <div id="top-bal" class="text-[11px] font-black bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full border border-blue-500/20 italic">₨ 0</div>
        </div>
    </header>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-8 rounded-[3.5rem] bg-gradient-to-br from-blue-600 to-indigo-900 text-white shadow-2xl relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-60">Mining Balance</p>
                <h2 class="text-5xl font-black tracking-tighter my-2" id="v-bal">₨ 0.00</h2>
                <div class="mt-6 flex gap-3">
                   <button onclick="changePage('wallet')" class="flex-1 bg-white/10 p-3 rounded-xl text-[9px] font-black uppercase italic">Deposit</button>
                   <button onclick="changePage('withdraw')" class="flex-1 bg-blue-400/20 p-3 rounded-xl text-[9px] font-black uppercase italic">Withdraw</button>
                </div>
            </div>

            <div class="glass p-4 rounded-3xl border-blue-500/10"><div id="proof-feed" class="text-[9px] font-black uppercase italic text-center text-blue-400">Syncing Node Data...</div></div>

            <h3 class="text-[11px] font-black uppercase text-blue-500 px-2 italic">Active Mining Nodes</h3>
            <div id="plans-grid" class="grid grid-cols-1 gap-4 pb-20"></div>
        </div>

        <div id="p-withdraw" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] space-y-4">
                <h3 class="text-center font-black uppercase text-blue-500 italic">Request Payout</h3>
                <select id="w-method"><option value="JazzCash">JazzCash</option><option value="EasyPaisa">EasyPaisa</option></select>
                <input type="number" id="w-amt" placeholder="Amount (Min 500)">
                <input type="text" id="w-acc" placeholder="Account Number">
                <button onclick="sendWithdraw()" class="w-full p-4 btn-prime text-[11px] uppercase italic">Confirm Payout</button>
            </div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] space-y-6">
                <h3 class="text-center font-black uppercase text-blue-500">Deposit Hub</h3>
                <div class="grid grid-cols-2 gap-3">
                    <div onclick="showQR('Jazz')" class="glass p-4 rounded-2xl text-center cursor-pointer border-blue-500/20"><p class="text-[10px] font-black">JAZZCASH</p></div>
                    <div onclick="showQR('Easy')" class="glass p-4 rounded-2xl text-center cursor-pointer border-blue-500/20"><p class="text-[10px] font-black">EASYPAISA</p></div>
                </div>
                <div id="qr-box" class="hidden text-center p-4 bg-white rounded-3xl animate-bounce">
                    <img id="qr-img-src" src="" class="w-48 h-48 mx-auto rounded-xl">
                    <p class="text-black text-[9px] font-black mt-2">M. Nazim: 03379827882</p>
                </div>
                <input type="number" id="d-amt" placeholder="Amount">
                <input type="text" id="d-tid" placeholder="Transaction ID (TID)">
                <button onclick="sendDeposit()" class="w-full p-4 btn-prime text-[11px] uppercase">Verify Payment</button>
            </div>
        </div>

        <div id="p-history" class="page space-y-4 pb-20">
            <h3 class="text-center font-black uppercase text-blue-500 italic">Transaction History</h3>
            <div id="user-logs-list" class="space-y-3"></div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-5 flex justify-around items-center rounded-t-[3.5rem] z-[4000]">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-gem"></i><span class="text-[7px] font-black uppercase">Nodes</span></button>
        <button onclick="changePage('wallet')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-plus-circle"></i><span class="text-[7px] font-black uppercase">Add</span></button>
        <button onclick="changePage('history')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-clock-rotate-left"></i><span class="text-[7px] font-black uppercase">Logs</span></button>
    </nav>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6 pb-28">
        <div class="flex justify-between items-center mb-8 border-b border-blue-500/20 pb-4">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Admin Authority</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-6 py-2 rounded-xl text-[10px] font-black uppercase">Exit</button>
        </div>

        <div class="glass p-6 rounded-3xl mb-8 space-y-4 border-blue-500/30">
            <h4 class="text-[10px] font-black uppercase text-blue-400 italic"><i class="fa-solid fa-plus mr-2"></i>Create New Node</h4>
            <input id="plan-n" placeholder="Plan Name (e.g. Starter Gold)">
            <input id="plan-p" type="number" placeholder="Price (₨)">
            <input id="plan-pr" type="number" placeholder="Daily Profit (%)">
            <button onclick="createPlan()" class="w-full p-4 btn-prime text-[10px] uppercase italic">Add Node to System</button>
        </div>

        <h4 class="text-[10px] font-black uppercase text-red-400 italic mb-4">Manage Active Nodes</h4>
        <div id="adm-plans-list" class="space-y-3 mb-8"></div>

        <div class="glass p-6 rounded-3xl mb-8 space-y-4">
            <h4 class="text-[10px] font-black uppercase text-blue-400 italic">User & Wallet Control</h4>
            <div class="flex gap-2">
                <input id="adm-u-n" placeholder="User Name" class="mb-0">
                <input id="adm-u-b" placeholder="New Bal" class="mb-0">
            </div>
            <button onclick="editBal()" class="w-full p-3 bg-blue-600 rounded-xl text-[9px] font-black uppercase">Update Balance</button>
            <div class="pt-4 border-t border-white/5 space-y-2">
                <input id="q-j-link" placeholder="JazzCash QR Link" class="mb-0">
                <input id="q-e-link" placeholder="EasyPaisa QR Link" class="mb-0">
                <button onclick="updateQRs()" class="w-full p-3 bg-white/5 border border-white/10 rounded-xl text-[9px] font-black uppercase">Save QR Links</button>
            </div>
        </div>

        <h4 class="text-[10px] font-black uppercase text-yellow-500 mb-4 italic">Pending Approvals</h4>
        <div id="adm-req-list" class="space-y-4"></div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8">
        <div class="w-20 h-20 bg-blue-600 rounded-[2.5rem] mb-10 flex items-center justify-center text-5xl font-black italic text-white shadow-2xl">M</div>
        <input type="text" id="l-name" placeholder="ENTER USERNAME" class="max-w-[300px] text-center font-black uppercase">
        <button onclick="login()" class="w-full max-w-[300px] p-4 btn-prime uppercase text-[11px] tracking-widest">Authorize Access</button>
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
        let user = null; let tapCount = 0; let qrJ = ''; let qrE = '';

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
                if(!doc.exists) return db.collection("users").doc(n).set({ name: n, balance: 0 });
                user = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            });

            db.collection("settings").doc("qrs").onSnapshot(d => { if(d.exists) { qrJ = d.data().jazz; qrE = d.data().easy; } });

            syncPlans();
            syncLogs(n);
            startLiveFeed();
        }

        // Plan System Functions
        async function createPlan() {
            const n = document.getElementById('plan-n').value;
            const p = parseInt(document.getElementById('plan-p').value);
            const pr = parseInt(document.getElementById('plan-pr').value);
            if(!n || !p || !pr) return alert("Fill all plan details!");
            await db.collection("plans").add({ name: n, price: p, profit: pr, created: Date.now() });
            alert("Node Added Sweetie! 😘");
            document.getElementById('plan-n').value = ''; document.getElementById('plan-p').value = ''; document.getElementById('plan-pr').value = '';
        }

        async function deletePlan(id) {
            if(confirm("Are you sure you want to delete this node?")) {
                await db.collection("plans").doc(id).delete();
                alert("Node Removed!");
            }
        }

        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); const admL = document.getElementById('adm-plans-list');
                g.innerHTML = ''; if(admL) admL.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    // User View
                    g.innerHTML += `<div class="glass p-6 rounded-[2.5rem] border-l-4 border-blue-500 flex justify-between items-center">
                        <div><p class="text-[9px] font-black uppercase opacity-40">${p.name}</p><h4 class="text-xl font-black italic">₨ ${p.price}</h4><p class="text-[8px] font-bold text-blue-400">${p.profit}% Daily Profit</p></div>
                        <button onclick="buyNode('${doc.id}', ${p.price})" class="btn-prime px-6 py-3 text-[10px] uppercase font-black">Buy</button>
                    </div>`;
                    // Admin View
                    if(admL) admL.innerHTML += `<div class="glass p-3 rounded-2xl flex justify-between items-center border border-white/5">
                        <p class="text-[10px] font-black uppercase">${p.name} (₨ ${p.price})</p>
                        <button onclick="deletePlan('${doc.id}')" class="text-red-500 text-xs"><i class="fa-solid fa-trash"></i></button>
                    </div>`;
                });
            });
        }

        // Wallet & QRs
        function showQR(type) {
            document.getElementById('qr-box').classList.remove('hidden');
            document.getElementById('qr-img-src').src = (type === 'Jazz' ? qrJ : qrE);
        }

        async function updateQRs() {
            const j = document.getElementById('q-j-link').value;
            const e = document.getElementById('q-e-link').value;
            await db.collection("settings").doc("qrs").set({ jazz: j, easy: e });
            alert("QR Links Saved!");
        }

        // Requests
        async function sendDeposit() {
            const a = document.getElementById('d-amt').value; const t = document.getElementById('d-tid').value;
            if(!a || !t) return alert("Enter details!");
            const id = Date.now().toString();
            await db.collection("requests").doc(id).set({ user: user.name, amount: parseInt(a), tid: t, status: 'pending', type: 'Deposit', time: Date.now() });
            await db.collection("logs").doc(id).set({ user: user.name, msg: `Deposit: ₨ ${a}`, status: 'pending', time: Date.now() });
            alert("Verification started sweetie! 😘");
        }

        async function sendWithdraw() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value;
            if(a < 500 || !acc || user.balance < a) return alert("Invalid amount/balance!");
            const id = Date.now().toString();
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").doc(id).set({ user: user.name, amount: a, acc: acc, status: 'pending', type: 'Withdraw', time: Date.now() });
            await db.collection("logs").doc(id).set({ user: user.name, msg: `Withdraw: ₨ ${a}`, status: 'pending', time: Date.now() });
            alert("Withdrawal requested!");
        }

        // Admin Authority
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("God Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        async function editBal() {
            const n = document.getElementById('adm-u-n').value.toLowerCase();
            const b = parseInt(document.getElementById('adm-u-b').value);
            await db.collection("users").doc(n).update({ balance: b });
            alert("Balance Updated!");
        }

        async function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const l = document.getElementById('adm-req-list'); l.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    l.innerHTML += `<div class="glass p-4 rounded-2xl border-l-4 border-yellow-500">
                        <p class="text-[10px] font-black text-blue-400 uppercase">${r.user} | ${r.type}</p>
                        <h4 class="text-xl font-black">₨ ${r.amount}</h4>
                        <p class="text-[8px] opacity-60 mb-3 italic">Info: ${r.tid || r.acc}</p>
                        <div class="flex gap-2">
                            <button onclick="approveReq('${doc.id}', '${r.user}', ${r.amount}, '${r.type}')" class="flex-1 bg-blue-600 p-2 rounded-xl text-[9px] font-black uppercase">Approve</button>
                            <button onclick="db.collection('requests').doc('${doc.id}').delete()" class="p-2 text-red-500"><i class="fa-solid fa-trash"></i></button>
                        </div>
                    </div>`;
                });
            });
        }

        async function approveReq(id, u, a, type) {
            if(type === 'Deposit') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").doc(id).update({ status: 'approved' });
            await db.collection("logs").doc(id).update({ status: 'approved' });
            alert("Success!");
        }

        // Utils
        function syncLogs(n) {
            db.collection("logs").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const l = document.getElementById('user-logs-list'); l.innerHTML = '';
                s.forEach(doc => {
                    const d = doc.data();
                    l.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center border-l-2 border-blue-500">
                        <p class="text-[10px] font-black uppercase">${d.msg}</p>
                        <span class="status-pill ${d.status || 'approved'}">${d.status || 'Success'}</span>
                    </div>`;
                });
            });
        }

        function startLiveFeed() {
            const names = ['Arif', 'Sana', 'Hamza', 'Dua', 'Zoya'];
            setInterval(() => {
                const n = names[Math.floor(Math.random()*names.length)];
                document.getElementById('proof-feed').innerText = `${n}*** Withdraw ₨ ${(Math.floor(Math.random()*50)+1)*100} Successful!`;
            }, 5000);
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user')); }
    </script>
</body>
</html>
