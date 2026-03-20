<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Overlord v150.0</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700;900&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #2f81f7; --danger: #f85149; --success: #238636; }
        body { font-family: 'Inter', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 100px; user-select: none; overflow-x: hidden; }
        
        .glass { background: var(--card); border: 1px solid var(--border); border-radius: 12px; }
        .page { display: none; }
        .active-page { display: block; animation: fadeIn 0.3s ease; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        
        .btn-core { background: var(--accent); color: white; border-radius: 8px; font-weight: 700; transition: 0.2s; border: none; cursor: pointer; }
        .btn-core:active { opacity: 0.8; transform: scale(0.98); }
        .btn-danger { background: var(--danger); }
        
        input, select { background: #161b22; border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 8px; width: 100%; outline: none; margin-bottom: 12px; font-size: 13px; }
        .nav-item { color: #8b949e; cursor: pointer; transition: 0.2s; font-size: 18px; }
        .nav-item.active { color: var(--accent); }
        
        /* Admin Scrollbar */
        #admin-ui::-webkit-scrollbar { width: 4px; }
        #admin-ui::-webkit-scrollbar-thumb { background: var(--border); }
    </style>
</head>
<body oncontextmenu="return false;">

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/95 backdrop-blur-md border-b border-[var(--border)] p-5 flex justify-between items-center">
        <div class="flex items-center gap-3">
            <div onclick="adminTap()" class="w-8 h-8 bg-blue-600 rounded flex items-center justify-center font-black text-white cursor-pointer">M</div>
            <h1 class="text-[12px] font-black uppercase tracking-tighter">MintCrest <span class="text-blue-500">Gold</span></h1>
        </div>
        <div id="top-bal" class="text-[11px] font-bold bg-white/5 px-4 py-1.5 rounded-full border border-white/10">PKR 0</div>
    </header>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-8 rounded-2xl bg-gradient-to-br from-blue-700 to-blue-900 shadow-xl border border-white/10">
                <p class="text-[9px] font-bold uppercase opacity-60 tracking-widest mb-1">Current Liquidity</p>
                <h2 class="text-4xl font-black tracking-tighter mb-8" id="v-bal">PKR 0.00</h2>
                <div class="flex gap-3">
                   <button onclick="changePage('wallet')" class="flex-1 bg-white text-blue-900 py-3 rounded-lg font-black text-[10px] uppercase">Deposit</button>
                   <button onclick="changePage('withdraw')" class="flex-1 bg-black/30 py-3 rounded-lg font-black text-[10px] uppercase border border-white/10">Withdraw</button>
                </div>
            </div>

            <div id="profit-section" class="hidden glass p-6 border-l-4 border-blue-500 bg-blue-500/5">
                <div class="flex justify-between items-center mb-4">
                    <p class="text-[10px] font-bold uppercase opacity-60">Mining Active</p>
                    <p id="p-timer" class="text-[10px] font-black text-blue-500">Syncing</p>
                </div>
                <button id="claim-profit-btn" disabled onclick="requestProfit()" class="w-full py-3 bg-white/5 border border-white/10 text-[10px] font-black uppercase rounded-lg">Calculating Rewards</button>
            </div>

            <div class="grid grid-cols-2 gap-3">
                <button onclick="changePage('nodes')" class="glass p-5 text-center"><i class="fa-solid fa-microchip mb-2 opacity-40"></i><p class="text-[9px] font-bold uppercase">Nodes</p></button>
                <button onclick="changePage('promo')" class="glass p-5 text-center"><i class="fa-solid fa-ticket mb-2 opacity-40"></i><p class="text-[9px] font-bold uppercase">Promos</p></button>
            </div>

            <div class="glass p-6 space-y-4">
                <h4 class="text-[11px] font-black uppercase opacity-40">Institutional Information</h4>
                <div class="grid grid-cols-1 gap-2">
                    <button onclick="changePage('about')" class="text-left text-[11px] font-bold py-2 border-b border-white/5 flex justify-between">About MintCrest <i class="fa-solid fa-chevron-right opacity-30"></i></button>
                    <button onclick="changePage('privacy')" class="text-left text-[11px] font-bold py-2 flex justify-between">Legal Agreement <i class="fa-solid fa-chevron-right opacity-30"></i></button>
                </div>
                <button onclick="logout()" class="w-full py-3 text-red-500 font-black text-[10px] uppercase mt-4">Terminate Session</button>
            </div>
        </div>

        <div id="p-about" class="page space-y-4">
            <h3 class="text-xl font-black">Corporate Profile</h3>
            <div class="glass p-6 text-[12px] leading-relaxed opacity-70 font-medium">
                MintCrest Gold is a regulated digital asset management firm. We specialize in high-frequency cloud mining operations providing institutional-grade returns to retail investors. All transactions are manually audited.
            </div>
            <button onclick="changePage('home')" class="btn-core w-full py-3 text-[10px]">Return</button>
        </div>

        <div id="p-privacy" class="page space-y-4">
            <h3 class="text-xl font-black">Legal Terms</h3>
            <div class="glass p-6 text-[12px] leading-relaxed opacity-70 font-medium">
                Assets are indexed to gold-backed liquidity pools. Withdrawals are subject to 24-hour verification. Fraudulent activity results in immediate permanent account termination without refund.
            </div>
            <button onclick="changePage('home')" class="btn-core w-full py-3 text-[10px]">Return</button>
        </div>

        <div id="p-nodes" class="page space-y-4 pb-20"><div id="plans-grid" class="space-y-4"></div></div>

        <div id="p-history" class="page space-y-4 pb-20"><div id="history-list" class="space-y-3"></div></div>

        <div id="p-promo" class="page space-y-4">
            <h3 class="text-xl font-black">Redeem Voucher</h3>
            <input id="u-promo-code" placeholder="Enter Promo Code">
            <button onclick="redeemPromo()" class="btn-core w-full py-4 text-[11px] uppercase">Apply Code</button>
        </div>

        <div id="p-wallet" class="page"><div class="glass p-8 space-y-5">
            <h3 class="text-lg font-black uppercase">Capital Deposit</h3>
            <div id="gate-nums" class="space-y-2"></div>
            <input id="d-amt" type="number" placeholder="Amount (PKR)">
            <input id="d-tid" placeholder="Transaction ID">
            <input id="d-proof-file" type="file" class="text-[10px]">
            <button onclick="sendDeposit()" class="btn-core w-full py-4 uppercase text-[10px]">Submit Evidence</button>
        </div></div>

        <div id="p-withdraw" class="page"><div class="glass p-8 space-y-5">
            <h3 class="text-lg font-black uppercase text-red-500">Liquidation Request</h3>
            <input id="w-amt" type="number" placeholder="PKR Amount">
            <input id="w-acc" placeholder="Payment Details (Acc/Title)">
            <button onclick="sendWithdraw()" class="btn-core bg-red-600 w-full py-4 uppercase text-[10px]">Request Payout</button>
        </div></div>

    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#0d1117] z-[100000] hidden overflow-y-auto p-6 pb-40">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-2xl font-black text-blue-500 uppercase tracking-tighter italic">Overlord Dashboard</h2>
            <button onclick="closeAdmin()" class="px-4 py-2 bg-white/5 rounded-lg border border-white/10 font-black">Exit</button>
        </div>

        <div class="grid grid-cols-2 gap-3 mb-8">
            <div class="glass p-4"><p id="adm-total-users" class="text-xl font-black">0</p><p class="text-[8px] uppercase opacity-40">Total Accounts</p></div>
            <div class="glass p-4"><p id="adm-total-bal" class="text-xl font-black text-green-500">0</p><p class="text-[8px] uppercase opacity-40">Platform Liability</p></div>
        </div>

        <div class="glass p-5 mb-8">
            <h4 class="text-[10px] font-black uppercase text-purple-400 mb-4">Account Archive (Ban/Delete/Credit)</h4>
            <div id="adm-user-list" class="space-y-3 max-h-[300px] overflow-y-auto pr-2"></div>
            <div class="mt-6 p-4 bg-white/5 rounded-lg border border-white/5">
                <h5 class="text-[9px] font-black uppercase mb-3">Direct Credit Injunction</h5>
                <input id="adm-inj-user" placeholder="Username">
                <input id="adm-inj-amt" type="number" placeholder="Amount">
                <button onclick="manualInjunction()" class="btn-core w-full py-3 text-[9px] bg-purple-600 uppercase">Inject Balance</button>
            </div>
        </div>

        <div class="glass p-5 mb-8">
            <h4 class="text-[10px] font-black uppercase text-blue-400 mb-4">Resource Management</h4>
            <div class="grid grid-cols-2 gap-2 mb-4">
                <input id="adm-p-name" placeholder="Node Name">
                <input id="adm-p-price" type="number" placeholder="Price">
            </div>
            <input id="adm-p-profit" type="number" placeholder="Daily Profit">
            <button onclick="createNode()" class="btn-core w-full py-3 mb-6 text-[9px] uppercase">Deploy Node</button>
            
            <h5 class="text-[9px] font-black uppercase mb-3 text-yellow-500">Active Node Inventory (Delete Only)</h5>
            <div id="adm-node-list" class="space-y-2 mb-8"></div>

            <h5 class="text-[9px] font-black uppercase mb-3 text-yellow-500">Promo Code Generator</h5>
            <input id="adm-promo-code" placeholder="CODE (e.g. MEGA100)">
            <input id="adm-promo-amt" type="number" placeholder="Bonus Value">
            <button onclick="createPromo()" class="btn-core w-full py-3 bg-yellow-600 text-black text-[9px] uppercase">Create Voucher</button>
        </div>

        <h4 class="text-xl font-black uppercase mb-6 italic">Pending Authorization</h4>
        <div id="adm-profit-list" class="space-y-4 mb-10"></div>
        <div id="adm-req-list" class="space-y-8"></div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full bg-[#0d1117] border-t border-[var(--border)] p-6 flex justify-around items-center z-[4000]">
        <i onclick="changePage('home')" class="nav-item fa-solid fa-house active"></i>
        <i onclick="changePage('nodes')" class="nav-item fa-solid fa-gem"></i>
        <i onclick="changePage('history')" class="nav-item fa-solid fa-chart-bar"></i>
        <i onclick="adminTap()" class="nav-item fa-solid fa-shield-halved"></i>
    </nav>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-black flex flex-col items-center justify-center p-10 text-center">
        <div class="w-16 h-16 bg-blue-600 rounded-xl mb-8 flex items-center justify-center text-3xl font-black italic text-white shadow-2xl">M</div>
        <h2 class="text-2xl font-black uppercase tracking-tighter mb-8">System Access</h2>
        <input id="l-name" placeholder="IDENTIFICATION NAME" class="text-center font-bold uppercase max-w-[300px]">
        <button onclick="login()" class="w-full max-w-[300px] btn-core py-4 uppercase text-[11px] tracking-widest">Verify ID</button>
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
        let user = null; let tapCount = 0; let settings = {};

        function login() {
            const n = document.getElementById('l-name').value.trim().toLowerCase();
            if(n.length < 3) return alert("Valid ID required.");
            db.collection("users").doc(n).get().then(doc => {
                if(doc.exists && doc.data().status === 'banned') return alert("This account is permanently terminated.");
                if(!doc.exists) {
                    db.collection("users").doc(n).set({ name: n, balance: 0, status: 'active', joinDate: Date.now() });
                }
                localStorage.setItem('mc_session', n); enterApp(n);
            });
        }

        function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('main-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            
            db.collection("users").doc(n).onSnapshot(doc => {
                user = doc.data();
                if(user.status === 'banned') logout();
                document.getElementById('v-bal').innerText = "PKR " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "PKR " + (user.balance || 0).toLocaleString();
                updateProfitTimer();
            });
            
            db.collection("settings").doc("global").onSnapshot(d => {
                settings = d.data() || {};
                document.getElementById('gate-nums').innerHTML = `
                    <div class="p-4 bg-white/5 rounded border border-white/5"><p class="text-[8px] opacity-30 font-black">JAZZCASH</p><h4 class="font-bold text-sm">${settings.jazzNum || '---'}</h4></div>
                    <div class="p-4 bg-white/5 rounded border border-white/5"><p class="text-[8px] opacity-30 font-black">EASYPAISA</p><h4 class="font-bold text-sm">${settings.easyNum || '---'}</h4></div>
                `;
            });
            syncPlans(); syncHistory(n);
        }

        // --- PROFIT CORE ---
        function updateProfitTimer() {
            if(!user.activePlan) return document.getElementById('profit-section').classList.add('hidden');
            document.getElementById('profit-section').classList.remove('hidden');
            const diff = Date.now() - (user.lastProfitRequest || user.planStartTime);
            const waitTime = 24 * 60 * 60 * 1000;

            if(diff >= waitTime) {
                const btn = document.getElementById('claim-profit-btn');
                btn.disabled = false; btn.innerText = "COLLECT PROFIT DATA";
                btn.className = "w-full py-3 bg-blue-600 text-white text-[10px] font-black uppercase rounded-lg";
                document.getElementById('p-timer').innerText = "READY";
            } else {
                const rem = waitTime - diff;
                const h = Math.floor(rem / 3600000);
                document.getElementById('p-timer').innerText = h + "H REMAINING";
                document.getElementById('claim-profit-btn').disabled = true;
            }
        }

        async function requestProfit() {
            await db.collection("requests").add({ user: user.name, amount: user.planDailyProfit, type: 'Profit Request', status: 'pending', time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastProfitRequest: Date.now() });
            alert("Data logged for verification.");
        }

        // --- PROMO SYSTEM ---
        async function redeemPromo() {
            const code = document.getElementById('u-promo-code').value.trim().toUpperCase();
            const snap = await db.collection("promos").doc(code).get();
            if(!snap.exists) return alert("Invalid Voucher.");
            const p = snap.data();
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(p.amount) });
            await db.collection("requests").add({ user: user.name, amount: p.amount, type: 'Promo Code: '+code, status: 'approved', time: Date.now() });
            alert("Voucher Applied! PKR " + p.amount);
            document.getElementById('u-promo-code').value = '';
        }

        // --- OVERLORD ADMIN LOGIC ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Auth:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        async function createNode() {
            await db.collection("plans").add({ 
                name: document.getElementById('adm-p-name').value, 
                price: parseInt(document.getElementById('adm-p-price').value), 
                dailyProfit: parseInt(document.getElementById('adm-p-profit').value) 
            });
            alert("Node Deployed.");
        }

        async function createPromo() {
            const code = document.getElementById('adm-promo-code').value.toUpperCase();
            const amt = parseInt(document.getElementById('adm-promo-amt').value);
            await db.collection("promos").doc(code).set({ amount: amt });
            alert("Voucher Created.");
        }

        function syncAdm() {
            // User List with Controls
            db.collection("users").onSnapshot(s => {
                const ul = document.getElementById('adm-user-list'); ul.innerHTML = '';
                let totalB = 0;
                s.forEach(doc => { 
                    const u = doc.data(); totalB += u.balance;
                    ul.innerHTML += `<div class="bg-white/5 p-3 rounded flex justify-between items-center text-[10px]">
                        <div><p class="${u.status==='banned'?'text-red-500':'text-gray-300'} font-bold">${u.name.toUpperCase()}</p><p class="opacity-40">PKR ${u.balance}</p></div>
                        <div class="flex gap-2">
                            <button onclick="toggleBan('${u.name}', '${u.status}')" class="px-3 py-1 bg-red-600/20 text-red-500 rounded">${u.status==='banned'?'Unban':'Ban'}</button>
                            <button onclick="deleteAccount('${u.name}')" class="px-3 py-1 bg-red-900 rounded">Del</button>
                        </div>
                    </div>`;
                });
                document.getElementById('adm-total-users').innerText = s.size;
                document.getElementById('adm-total-bal').innerText = "PKR " + totalB.toLocaleString();
            });

            // Pending Actions
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-req-list');
                const pList = document.getElementById('adm-profit-list');
                list.innerHTML = ''; pList.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    const html = `<div class="glass p-5 border-l-4 ${r.type==='Deposit'?'border-blue-500':'border-red-500'}">
                        <p class="text-[9px] font-bold opacity-40">${r.user.toUpperCase()} | ${r.type}</p>
                        <h4 class="text-2xl font-black italic">PKR ${r.amount}</h4>
                        <p class="text-[9px] opacity-40 mb-4">${r.tid || ''}</p>
                        ${r.proof ? `<img src="${r.proof}" class="w-full rounded mb-4 border border-white/5">` : ''}
                        <div class="flex gap-2">
                            <button onclick="approveReq('${doc.id}', '${r.user}', ${r.amount}, '${r.type}')" class="flex-1 bg-green-700 py-3 rounded text-[9px] font-bold uppercase">Approve</button>
                            <button onclick="rejectReq('${doc.id}', '${r.user}', ${r.amount}, '${r.type}')" class="flex-1 bg-red-700 py-3 rounded text-[9px] font-bold uppercase">Reject</button>
                        </div>
                    </div>`;
                    if(r.type === 'Profit Request') pList.innerHTML += html;
                    else list.innerHTML += html;
                });
            });

            // Node List (Delete Only)
            db.collection("plans").onSnapshot(s => {
                const nl = document.getElementById('adm-node-list'); nl.innerHTML = '';
                s.forEach(doc => {
                    nl.innerHTML += `<div class="flex justify-between items-center bg-white/5 p-2 rounded text-[9px]">
                        <span>${doc.data().name} (PKR ${doc.data().price})</span>
                        <button onclick="deleteNode('${doc.id}')" class="text-red-500 font-bold">DELETE</button>
                    </div>`;
                });
            });
        }

        async function deleteNode(id) { if(confirm("Terminate this node?")) await db.collection("plans").doc(id).delete(); }
        async function toggleBan(u, s) { await db.collection("users").doc(u).update({ status: s === 'banned' ? 'active' : 'banned' }); }
        async function deleteAccount(u) { if(confirm("Delete data for "+u+"?")) await db.collection("users").doc(u).delete(); }
        
        async function approveReq(id, u, a, type) {
            if(type === 'Deposit' || type === 'Profit Request') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").doc(id).update({ status: 'approved' });
        }
        async function rejectReq(id, u, a, type) {
            if(type === 'Withdraw') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").doc(id).update({ status: 'rejected' });
        }

        // --- UTILS ---
        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    g.innerHTML += `<div class="glass p-6 flex justify-between items-center">
                        <div class="text-left"><p class="text-[9px] font-bold opacity-40 uppercase">${p.name}</p><h4 class="text-xl font-black">PKR ${p.price}</h4><p class="text-[9px] text-blue-500 font-bold mt-1">PKR ${p.dailyProfit} Return/24H</p></div>
                        <button onclick="buyPlan('${doc.id}', ${p.price}, ${p.dailyProfit}, '${p.name}')" class="btn-core px-6 py-3 text-[10px] uppercase">Initialize</button>
                    </div>`;
                });
            });
        }

        async function buyPlan(id, price, profit, name) {
            if(user.balance < price) return alert("Insufficient Liquidity.");
            if(confirm("Confirm Node Deployment?")) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-price), activePlan: name, planDailyProfit: profit, planStartTime: Date.now(), lastProfitRequest: Date.now() });
                await db.collection("requests").add({ user: user.name, amount: price, type: 'Activated: '+name, status: 'approved', time: Date.now() });
                alert("Node Online.");
            }
        }

        function syncHistory(n) {
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const h = document.getElementById('history-list'); h.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data(); const st = r.status==='approved'?'text-green-500':'text-yellow-500';
                    h.innerHTML += `<div class="glass p-5 flex justify-between items-center"><div class="text-left"><p class="text-[8px] font-bold opacity-40 uppercase">${r.type}</p><h4 class="text-sm font-black">PKR ${r.amount}</h4></div><div class="text-[8px] font-black uppercase ${st}">${r.status}</div></div>`;
                });
            });
        }

        async function sendDeposit() {
            const a = parseInt(document.getElementById('d-amt').value); const t = document.getElementById('d-tid').value; const file = document.getElementById('d-proof-file').files[0];
            if(!a || !t || !file) return alert("Incomplete Documentation.");
            const reader = new FileReader(); reader.readAsDataURL(file);
            reader.onload = async () => { await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: reader.result, status: 'pending', type: 'Deposit', time: Date.now() }); alert("Received."); changePage('home'); };
        }

        async function sendWithdraw() {
            const a = parseInt(document.getElementById('w-amt').value);
            if(user.balance < a) return alert("Liquidity Error.");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").add({ user: user.name, amount: a, tid: document.getElementById('w-acc').value, status: 'pending', type: 'Withdraw', time: Date.now() });
            alert("Verification Started."); changePage('home');
        }

        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); 
            document.querySelectorAll('.nav-item').forEach(nv => nv.classList.remove('active'));
            document.getElementById('p-'+p).classList.add('active-page');
            window.scrollTo(0,0); 
        }

        function logout() { localStorage.removeItem('mc_session'); location.reload(); }
        window.onload = () => { if(localStorage.getItem('mc_session')) enterApp(localStorage.getItem('mc_session')); }
        setInterval(() => { if(user) updateProfitTimer(); }, 30000);
    </script>
</body>
</html>
        
