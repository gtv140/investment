<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Professional Edition</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --blue: #2f81f7; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); border-radius: 16px; }
        .page { display: none; animation: fadeIn 0.3s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        input, select { background: #161b22; border: 1px solid var(--border); color: white; padding: 14px; border-radius: 12px; width: 100%; outline: none; margin-bottom: 10px; font-size: 13px; }
        .btn-prime { background: linear-gradient(135deg, #2f81f7, #1e3a8a); color: white; font-weight: 700; border-radius: 12px; cursor: pointer; border: none; }
        #toast { visibility: hidden; min-width: 250px; background-color: #2f81f7; color: #fff; text-align: center; border-radius: 8px; padding: 16px; position: fixed; z-index: 2000000; left: 50%; transform: translateX(-50%); bottom: 30px; font-weight: 700; }
        #toast.show { visibility: visible; animation: fadein 0.5s, fadeout 0.5s 2.5s; }
        @keyframes fadein { from {bottom: 0; opacity: 0;} to {bottom: 30px; opacity: 1;} }
        @keyframes fadeout { from {bottom: 30px; opacity: 1;} to {bottom: 0; opacity: 0;} }
    </style>
</head>
<body onclick="handleSecretBypass()">

    <div id="toast">SYSTEM NOTIFICATION</div>

    <div id="lock-screen" class="fixed inset-0 z-[1000000] bg-[#010409] hidden flex flex-col items-center justify-center p-10 text-center">
        <div class="w-16 h-16 border-4 border-blue-500 border-t-transparent rounded-full animate-spin mb-6"></div>
        <h1 class="text-2xl font-bold text-blue-500 mb-2 uppercase tracking-tighter">System Maintenance</h1>
        <p class="text-[10px] font-medium opacity-50 uppercase tracking-[3px]">The platform is undergoing scheduled optimization. Please check back shortly.</p>
    </div>

    <header class="p-5 flex justify-between items-center sticky top-0 bg-[#010409]/95 backdrop-blur-md border-b border-white/5 z-[5000]">
        <div class="flex items-center gap-3">
            <div onclick="initAdminAccess()" class="w-10 h-10 bg-blue-600 rounded-xl flex items-center justify-center text-white font-bold">M</div>
            <div>
                <h1 class="text-[12px] font-extrabold uppercase tracking-tight">MINTCREST <span class="text-blue-500">GOLD</span></h1>
                <p id="id-tag" class="text-[7px] font-bold opacity-40 tracking-widest uppercase text-blue-400">Node Verified</p>
            </div>
        </div>
        <div id="top-bal" class="bg-blue-600/10 px-4 py-2 rounded-lg border border-blue-500/20 text-blue-500 text-[11px] font-bold">PKR 0</div>
    </header>

    <main id="app-ui" class="hidden p-5 space-y-6 pb-32">
        <div id="p-home" class="page active-page space-y-6">
            <div class="glass p-8 bg-gradient-to-br from-blue-900 to-[#0d1117] text-white">
                <p class="text-[9px] font-bold uppercase opacity-50 tracking-widest">Account Valuation</p>
                <h2 class="text-4xl font-extrabold tracking-tighter mt-1" id="v-bal">PKR 0</h2>
                <div class="mt-8 flex gap-3">
                    <button onclick="changePage('wallet')" class="flex-1 py-4 bg-blue-500 text-white rounded-xl font-bold text-[10px] uppercase">Add Funds</button>
                    <button onclick="changePage('withdraw')" class="flex-1 py-4 bg-white/5 border border-white/10 rounded-xl font-bold text-[10px] uppercase">Withdraw</button>
                </div>
            </div>
            <div id="plans-container" class="space-y-4"></div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <div id="gateway-info" class="space-y-2"></div>
            <div class="glass p-6 space-y-4">
                <input type="number" id="d-amount" placeholder="Deposit Amount">
                <input type="text" id="d-tid" placeholder="Transaction ID">
                <input type="file" id="d-file" class="text-[10px] text-gray-400">
                <button onclick="processDeposit()" class="w-full py-4 btn-prime uppercase text-[10px]">Submit Verification</button>
            </div>
        </div>

        <div id="p-history" class="page space-y-3"><div id="log-list" class="space-y-2"></div></div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6 pb-40">
        <div class="flex justify-between items-center mb-8 border-b border-white/5 pb-4">
            <h2 class="text-lg font-bold text-blue-500 uppercase">Management Terminal</h2>
            <button onclick="closeAdmin()" class="text-[10px] font-bold uppercase opacity-50">Close</button>
        </div>

        <div class="space-y-3 mb-8">
            <button onclick="toggleSystemLock()" id="lock-btn" class="w-full py-4 border border-blue-500/20 rounded-xl font-bold text-[10px] uppercase">Maintenance Mode</button>
            <div class="grid grid-cols-2 gap-4">
                <div class="glass p-5 text-center"><h4 id="adm-user-count" class="text-xl font-bold">0</h4><p class="text-[8px] opacity-40 uppercase">Accounts</p></div>
                <div class="glass p-5 text-center text-green-500"><h4 id="adm-pending-count" class="text-xl font-bold">0</h4><p class="text-[8px] opacity-40 uppercase">Pending</p></div>
            </div>
        </div>

        <div class="glass p-6 mb-6">
            <h4 class="text-[10px] font-bold uppercase text-blue-500 mb-4">Add Asset Node</h4>
            <input id="adm-plan-name" placeholder="Node Name">
            <input id="adm-plan-price" type="number" placeholder="Entry Price">
            <input id="adm-plan-yield" type="number" placeholder="Daily Yield">
            <button onclick="deployNode()" class="w-full py-3 bg-blue-600 rounded-lg font-bold text-[10px]">DEPLOY</button>
        </div>

        <div class="glass p-6 mb-6">
            <h4 class="text-[10px] font-bold uppercase text-blue-500 mb-4">Payment Infrastructure</h4>
            <input id="adm-jazz-num" placeholder="JazzCash Number">
            <input id="adm-easy-num" placeholder="EasyPaisa Number">
            <button onclick="saveGatewayNumbers()" class="w-full py-3 bg-blue-600 rounded-lg font-bold text-[10px]">SAVE CONFIG</button>
        </div>

        <div id="admin-request-queue" class="space-y-4"></div>

        <div class="glass p-6 mt-8 border border-purple-500/20">
            <h4 class="text-[10px] font-bold uppercase text-purple-500 mb-4">Account Override</h4>
            <input id="adm-target-user" placeholder="Target Username">
            <input id="adm-target-bal" type="number" placeholder="Set Balance">
            <button onclick="overrideUserBalance()" class="w-full py-3 bg-purple-600 rounded-lg font-bold text-[10px]">UPDATE ACCOUNT</button>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass bg-[#0d1117]/95 border-t border-white/5 p-6 flex justify-around items-center z-[5000] rounded-t-[30px]">
        <button onclick="changePage('home')" class="opacity-40 text-blue-500"><i class="fa-solid fa-chart-line text-xl"></i></button>
        <button onclick="changePage('wallet')" class="opacity-40"><i class="fa-solid fa-wallet text-xl"></i></button>
        <button onclick="changePage('history')" class="opacity-40"><i class="fa-solid fa-receipt text-xl"></i></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let currentUser = null; let clickTracker = 0; let secretTracker = 0; let maintenanceFlag = false;

        // --- CORE SYSTEM ---
        function initAdminAccess() { clickTracker++; if(clickTracker >= 5) { if(prompt("Access Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdminData(); } clickTracker=0; } }
        function handleSecretBypass() { 
            if(!document.getElementById('lock-screen').classList.contains('hidden')) {
                secretTracker++; 
                if(secretTracker >= 5) { 
                    if(prompt("Override Key:")==="mint786") { 
                        document.getElementById('lock-screen').classList.add('hidden');
                        document.getElementById('admin-ui').classList.remove('hidden'); 
                        syncAdminData();
                    } 
                    secretTracker=0; 
                } 
            }
        }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        async function syncAdminData() {
            db.collection("users").onSnapshot(s => document.getElementById('adm-user-count').innerText = s.size);
            db.collection("requests").where("status","==","pending").onSnapshot(s => {
                document.getElementById('adm-pending-count').innerText = s.size;
                const queue = document.getElementById('admin-request-queue'); queue.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    queue.innerHTML += `<div class="glass p-5 border-l-4 border-blue-500 text-[10px] font-bold uppercase">
                        <p class="mb-2">${r.user} | ${r.type} | PKR ${r.amount}</p>
                        ${r.proof ? `<img src="${r.proof}" class="w-full rounded-lg mb-3">` : ''}
                        <div class="flex gap-2">
                            <button onclick="approveTransaction('${doc.id}','${r.user}',${r.amount},'${r.type}')" class="flex-1 py-3 bg-green-600 rounded-lg text-[8px]">APPROVE</button>
                            <button onclick="db.collection('requests').doc('${doc.id}').update({status:'rejected'})" class="flex-1 py-3 bg-red-600 rounded-lg text-[8px]">REJECT</button>
                        </div>
                    </div>`;
                });
            });
            db.collection("settings").doc("system").onSnapshot(d => {
                maintenanceFlag = d.data()?.maintenance;
                const btn = document.getElementById('lock-btn');
                btn.innerText = "System Status: " + (maintenanceFlag ? "LOCKED" : "ACTIVE");
                btn.className = maintenanceFlag ? "w-full py-4 bg-red-600/10 text-red-500 border border-red-500/20 rounded-xl font-bold text-[10px] uppercase" : "w-full py-4 bg-green-600/10 text-green-500 border border-green-500/20 rounded-xl font-bold text-[10px] uppercase";
            });
        }

        async function deployNode() {
            const n = document.getElementById('adm-plan-name').value;
            const p = Number(document.getElementById('adm-plan-price').value);
            const y = Number(document.getElementById('adm-plan-yield').value);
            if(n && p && y) { await db.collection("plans").add({ name: n, price: p, dailyProfit: y }); triggerToast("NODE DEPLOYED"); }
        }

        async function saveGatewayNumbers() {
            await db.collection("settings").doc("gateways").set({ jazz: document.getElementById('adm-jazz-num').value, easy: document.getElementById('adm-easy-num').value });
            triggerToast("CONFIG UPDATED");
        }

        async function toggleSystemLock() { await db.collection("settings").doc("system").set({ maintenance: !maintenanceFlag }); }

        async function overrideUserBalance() {
            const u = document.getElementById('adm-target-user').value.toLowerCase();
            const b = Number(document.getElementById('adm-target-bal').value);
            await db.collection("users").doc(u).update({ balance: b });
            triggerToast("ACCOUNT MODIFIED");
        }

        async function approveTransaction(id, u, a, type) {
            if(type === 'Deposit') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").doc(id).update({ status: 'approved' });
            triggerToast("TRANSACTION FINALIZED");
        }

        function syncUserEnvironment(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                currentUser = d.data();
                const bal = (currentUser.balance || 0).toLocaleString();
                document.getElementById('v-bal').innerText = "PKR " + bal;
                document.getElementById('top-bal').innerText = "PKR " + bal;
            });
            db.collection("settings").doc("system").onSnapshot(d => {
                if(d.data()?.maintenance && n !== 'admin') document.getElementById('lock-screen').classList.remove('hidden');
                else document.getElementById('lock-screen').classList.add('hidden');
            });
            db.collection("settings").doc("gateways").onSnapshot(d => {
                const g = d.data();
                document.getElementById('gateway-info').innerHTML = `<div class="p-4 glass flex justify-between text-[11px] font-bold"><span>JazzCash</span><span>${g.jazz}</span></div><div class="p-4 glass flex justify-between text-[11px] font-bold"><span>EasyPaisa</span><span>${g.easy}</span></div>`;
            });
            db.collection("plans").orderBy("price","asc").onSnapshot(s => {
                const cont = document.getElementById('plans-container'); cont.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    cont.innerHTML += `<div class="glass p-6 border-l-4 border-blue-500 flex justify-between items-center">
                        <div><p class="text-[8px] font-bold opacity-40 uppercase">${p.name}</p><h4 class="text-xl font-extrabold">PKR ${p.price}</h4><p class="text-[9px] text-green-500 font-bold">Yield: PKR ${p.dailyProfit}/Day</p></div>
                        <button onclick="initiateNodePurchase('${p.name}', ${p.price})" class="bg-blue-600 px-6 py-3 rounded-xl font-bold text-[9px]">ACQUIRE</button>
                    </div>`;
                });
            });
        }

        async function processDeposit() {
            const a = Number(document.getElementById('d-amount').value); const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            if(!a || !t || !f) return alert("Verification data missing.");
            const r = new FileReader(); r.readAsDataURL(f);
            r.onload = async () => {
                await db.collection("requests").add({ user: currentUser.name, amount: a, tid: t, proof: r.result, type: 'Deposit', status: 'pending', time: Date.now() });
                triggerToast("SUBMITTED FOR AUDIT"); changePage('home');
            };
        }

        function triggerToast(m) { const t = document.getElementById("toast"); t.innerText = m; t.className = "show"; setTimeout(()=>t.className="", 3000); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        
        window.onload = () => { 
            const session = localStorage.getItem('mc_session');
            if(session) {
                document.getElementById('id-tag').innerText = "NODE: " + session.toUpperCase();
                document.getElementById('app-ui').classList.remove('hidden');
                document.getElementById('bottom-nav').classList.remove('hidden');
                syncUserEnvironment(session);
            }
        }
    </script>
</body>
</html>
