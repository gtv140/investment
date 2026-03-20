<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Monarch Edition</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --blue: #2f81f7; --gold: #f2bc1b; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); -webkit-user-select: none; user-select: none; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); border-radius: 24px; box-shadow: 0 15px 35px rgba(0,0,0,0.4); }
        .page { display: none; }
        .active-page { display: block; animation: slideIn 0.4s ease-out; }
        @keyframes slideIn { from { opacity: 0; transform: scale(0.95); } to { opacity: 1; transform: scale(1); } }
        input, select { background: #161b22; border: 1px solid var(--border); color: white; padding: 16px; border-radius: 14px; width: 100%; outline: none; margin-bottom: 12px; font-size: 14px; }
        input:focus { border-color: var(--blue); }
        .btn-monarch { background: linear-gradient(135deg, #2f81f7, #1e3a8a); color: white; font-weight: 800; border-radius: 14px; transition: 0.3s; cursor: pointer; border: none; }
        .btn-monarch:active { transform: scale(0.96); }
        #toast { visibility: hidden; min-width: 280px; background: var(--blue); color: #fff; text-align: center; border-radius: 12px; padding: 18px; position: fixed; z-index: 100000; left: 50%; transform: translateX(-50%); bottom: 110px; font-weight: 800; box-shadow: 0 20px 60px rgba(0,0,0,0.6); }
        #toast.show { visibility: visible; animation: pop 0.4s forwards; }
        @keyframes pop { from { bottom: 0; opacity: 0; } to { bottom: 110px; opacity: 1; } }
    </style>
</head>
<body>

    <div id="toast">SYSTEM NOTIFICATION</div>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[#010409]/95 backdrop-blur-md border-b border-[#30363d] p-5 flex justify-between items-center">
        <div class="flex items-center gap-4">
            <div onclick="adminTap()" class="w-11 h-11 bg-gradient-to-br from-blue-600 to-indigo-800 rounded-2xl flex items-center justify-center shadow-lg shadow-blue-500/20">
                <i class="fa-solid fa-crown text-white"></i>
            </div>
            <div>
                <h1 class="text-[14px] font-black tracking-tighter italic">MINTCREST <span class="text-blue-500 uppercase text-[10px]">Gold</span></h1>
                <p id="u-tag" class="text-[8px] font-bold text-gray-500 uppercase tracking-widest">Global Node</p>
            </div>
        </div>
        <div class="bg-white/5 px-4 py-2 rounded-xl border border-white/5 flex items-center gap-2">
            <i class="fa-solid fa-gem text-blue-500 text-xs"></i>
            <span id="top-bal" class="text-sm font-black">₨ 0</span>
        </div>
    </header>

    <main id="app-ui" class="hidden p-5 pb-32">
        <div id="p-home" class="page active-page space-y-6">
            <div class="glass p-8 bg-gradient-to-br from-[#0d1117] to-[#161b22] relative overflow-hidden">
                <div class="absolute -right-10 -top-10 opacity-5"><i class="fa-solid fa-coins text-[12rem]"></i></div>
                <p class="text-[10px] font-black uppercase tracking-[4px] text-gray-500 mb-2">Available Assets</p>
                <h2 class="text-5xl font-black tracking-tighter mb-10 italic" id="v-bal">₨ 0.00</h2>
                <div class="flex gap-4">
                    <button onclick="changePage('wallet')" class="flex-1 py-4 btn-monarch text-[11px] uppercase tracking-widest">Deposit</button>
                    <button onclick="changePage('withdraw')" class="flex-1 py-4 bg-white/5 border border-white/10 rounded-2xl font-black text-[11px] uppercase text-white">Withdraw</button>
                </div>
            </div>

            <div id="node-active-box" class="hidden glass p-6 border-l-[6px] border-blue-500 bg-blue-500/5 flex justify-between items-center">
                <div>
                    <p class="text-[9px] font-black uppercase text-blue-500 mb-1">Active Mining Node</p>
                    <h4 id="active-plan-name" class="text-lg font-black italic">--</h4>
                </div>
                <button id="claim-profit-btn" disabled onclick="requestProfit()" class="px-6 py-3 bg-white/5 rounded-xl text-[10px] font-black uppercase border border-white/10">Cycle: Pending</button>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <div onclick="changePage('nodes')" class="glass p-7 flex flex-col items-center gap-3 border-b-4 border-blue-600 active:scale-95 transition"><i class="fa-solid fa-server text-blue-500 text-2xl"></i><p class="text-[10px] font-black uppercase">Nodes</p></div>
                <div onclick="claimDaily()" class="glass p-7 flex flex-col items-center gap-3 border-b-4 border-gold active:scale-95 transition"><i class="fa-solid fa-gift text-gold text-2xl"></i><p class="text-[10px] font-black uppercase">Bonus</p></div>
            </div>

            <div class="glass p-2">
                <button onclick="changePage('about')" class="w-full flex justify-between p-4 text-xs font-bold opacity-60">Company Profile <i class="fa-solid fa-chevron-right"></i></button>
                <button onclick="changePage('privacy')" class="w-full flex justify-between p-4 text-xs font-bold opacity-60">Security Terms <i class="fa-solid fa-chevron-right"></i></button>
            </div>
            <button onclick="logout()" class="w-full py-4 text-red-500 font-black text-[10px] uppercase tracking-widest opacity-40">Terminate Secure Session</button>
        </div>

        <div id="p-nodes" class="page space-y-6"><h3 class="text-2xl font-black italic text-center uppercase text-blue-500">Node Inventory</h3><div id="plans-grid" class="space-y-4"></div></div>
        <div id="p-history" class="page space-y-4"><h3 class="text-2xl font-black italic uppercase">Transactions</h3><div id="history-list" class="space-y-3"></div></div>
        <div id="p-wallet" class="page"><div class="glass p-8 space-y-6"><h3 class="text-xl font-black uppercase italic">Fund Wallet</h3><div id="gate-nums" class="space-y-3"></div><input id="d-amt" type="number" placeholder="Amount (PKR)"><input id="d-tid" placeholder="Transaction ID"><input id="d-proof-file" type="file" accept="image/*" class="text-[10px] p-2 bg-white/5 border-dashed border-white/10"><button onclick="sendDeposit()" class="w-full py-5 btn-monarch uppercase text-[11px] tracking-widest">Submit Transmission</button></div></div>
        <div id="p-withdraw" class="page"><div class="glass p-8 space-y-6"><h3 class="text-xl font-black uppercase italic text-red-500">Withdraw Funds</h3><input id="w-amt" type="number" placeholder="Amount (Min 500)"><input id="w-acc" placeholder="Account Title & Number"><button onclick="sendWithdraw()" class="w-full py-5 bg-red-600 rounded-2xl font-black uppercase text-[11px] tracking-widest text-white shadow-xl shadow-red-900/20">Authorize Cashout</button></div></div>
        <div id="p-about" class="page p-6 space-y-4"><button onclick="changePage('home')" class="text-blue-500 text-xs font-bold italic"><- BACK</button><h3 class="text-2xl font-black italic">ABOUT US</h3><p class="text-xs opacity-60 leading-relaxed">MintCrest Gold is a regulated asset management firm based in London, specializing in AI-driven crypto mining and institutional liquidity provision. Your funds are protected by our sovereign reserve fund.</p></div>
        <div id="p-privacy" class="page p-6 space-y-4"><button onclick="changePage('home')" class="text-blue-500 text-xs font-bold italic"><- BACK</button><h3 class="text-2xl font-black italic">SECURITY POLICY</h3><p class="text-xs opacity-60 leading-relaxed">1. Two-factor withdrawal verification.<br>2. 256-bit SSL Data Encryption.<br>3. Zero-Logs session management.<br>4. Immediate ban for fraudulent activities.</p></div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6 pb-40">
        <div class="flex justify-between items-center mb-8 pb-4 border-b border-white/10">
            <h2 class="text-2xl font-black text-blue-500 italic uppercase">God Mode Console</h2>
            <button onclick="closeAdmin()" class="px-5 py-2 bg-white/5 text-[10px] font-black rounded-xl border border-white/10">EXIT</button>
        </div>

        <div id="adm-stats" class="grid grid-cols-2 gap-4 mb-8"></div>

        <div class="glass p-6 mb-8 border-l-8 border-gold">
            <h4 class="text-[10px] font-black uppercase text-gold mb-4">Payment Gateway Setup</h4>
            <input id="adm-jazz-num" placeholder="New JazzCash Number">
            <input id="adm-easy-num" placeholder="New EasyPaisa Number">
            <button onclick="updateGateway()" class="w-full py-3 bg-yellow-600 rounded-xl font-black text-[10px] uppercase">Update Numbers</button>
        </div>

        <div class="glass p-6 mb-8 border-l-8 border-blue-500">
            <h4 class="text-[10px] font-black uppercase text-blue-500 mb-4">Node Factory (Create Plans)</h4>
            <input id="adm-p-name" placeholder="Node Name (e.g. Diamond)">
            <input id="adm-p-price" type="number" placeholder="Price (PKR)">
            <input id="adm-p-profit" type="number" placeholder="Daily Profit (PKR)">
            <button onclick="createPlan()" class="w-full py-4 bg-blue-600 rounded-xl font-black text-[10px] uppercase">Deploy New Node</button>
            <div id="adm-plan-list" class="mt-4 space-y-2"></div>
        </div>

        <div class="glass p-6 mb-8 border-l-8 border-green-500 bg-green-500/5">
            <h4 class="text-[11px] font-black text-green-500 uppercase mb-5 italic">Pending Approvals</h4>
            <div id="adm-req-list" class="space-y-6"></div>
        </div>

        <div class="glass p-6 mb-8 border-l-8 border-purple-500">
            <h4 class="text-[11px] font-black text-purple-500 uppercase mb-5">User Overlord Center</h4>
            <div id="adm-user-list" class="space-y-2 mb-6 max-h-[300px] overflow-y-auto"></div>
            <div class="space-y-3 bg-white/5 p-4 rounded-2xl">
                <input id="adm-inj-user" placeholder="User Name">
                <input id="adm-inj-amt" type="number" placeholder="New Balance (Total)">
                <button onclick="forceBalance()" class="w-full py-4 bg-purple-600 rounded-xl font-black text-[10px] uppercase">Force Update Balance</button>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass bg-[#0d1117]/90 backdrop-blur-2xl border-t border-white/5 p-6 flex justify-around items-center z-[4000] rounded-t-[40px] shadow-2xl">
        <div onclick="changePage('home')" class="nav-item active flex flex-col items-center gap-1.5 cursor-pointer text-blue-500"><i class="fa-solid fa-house-chimney text-xl"></i><span class="text-[9px] font-black uppercase">Home</span></div>
        <div onclick="changePage('nodes')" class="nav-item flex flex-col items-center gap-1.5 cursor-pointer text-gray-500"><i class="fa-solid fa-microchip text-xl"></i><span class="text-[9px] font-black uppercase">Nodes</span></div>
        <div onclick="changePage('history')" class="nav-item flex flex-col items-center gap-1.5 cursor-pointer text-gray-500"><i class="fa-solid fa-receipt text-xl"></i><span class="text-[9px] font-black uppercase">Ledger</span></div>
    </nav>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[#010409] flex flex-col items-center justify-center p-10">
        <div class="w-28 h-28 bg-gradient-to-tr from-blue-600 to-blue-800 rounded-[2.8rem] mb-12 flex items-center justify-center text-5xl font-black italic text-white shadow-2xl shadow-blue-500/20">M</div>
        <h2 class="text-3xl font-black uppercase tracking-tighter mb-12 italic">MintCrest <span class="text-blue-500">Gold</span></h2>
        <div class="w-full max-w-[320px] space-y-4">
            <input id="l-name" placeholder="SYSTEM IDENTITY" class="text-center font-black uppercase py-5 bg-white/5 border-white/10 rounded-2xl">
            <button onclick="login()" class="w-full py-6 btn-monarch uppercase text-[12px] tracking-widest shadow-2xl">Authorize Entry</button>
        </div>
    </section>

    <script>
        // --- FIREBASE INIT ---
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

        // --- AUTH & SETUP ---
        function login() {
            const n = document.getElementById('l-name').value.trim().toLowerCase();
            if(n.length < 3) return showToast("INVALID ID");
            db.collection("users").doc(n).get().then(doc => {
                if(doc.exists && doc.data().status === 'banned') return showToast("ACCESS TERMINATED");
                if(!doc.exists) db.collection("users").doc(n).set({ name: n, balance: 0, status: 'active', joinDate: Date.now() });
                localStorage.setItem('mc_session', n); enterApp(n);
            });
        }

        function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('main-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('u-tag').innerText = "ID: " + n.toUpperCase();

            db.collection("users").doc(n).onSnapshot(doc => {
                user = doc.data();
                if(!user || user.status === 'banned') logout();
                document.getElementById('v-bal').innerText = "₨ " + user.balance.toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + user.balance.toLocaleString();
                if(user.activePlan) {
                    document.getElementById('active-plan-name').innerText = user.activePlan;
                    updateProfitTimer();
                }
            });
            db.collection("settings").doc("gateways").onSnapshot(d => {
                const s = d.data() || { jazz: '---', easy: '---' };
                document.getElementById('gate-nums').innerHTML = `<div class="p-4 bg-white/5 rounded-2xl border border-white/5 flex justify-between"><span>JazzCash</span><span class="font-black text-blue-500">${s.jazz}</span></div><div class="p-4 bg-white/5 rounded-2xl border border-white/5 flex justify-between"><span>EasyPaisa</span><span class="font-black text-gold">${s.easy}</span></div>`;
            });
            syncPlans(); syncHistory(n);
        }

        // --- GOD MODE ADMIN COMMANDS ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("GOD CODE:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        function syncAdm() {
            // User Controls
            db.collection("users").onSnapshot(s => {
                const stats = document.getElementById('adm-stats');
                const list = document.getElementById('adm-user-list');
                let totalB = 0; list.innerHTML = '';
                s.forEach(doc => {
                    const u = doc.data(); totalB += u.balance;
                    list.innerHTML += `<div class="p-4 bg-white/5 rounded-2xl flex justify-between items-center text-[10px]">
                        <div><p class="font-black ${u.status==='banned'?'text-red-500':'text-blue-400'}">${u.name.toUpperCase()}</p><p class="opacity-40">₨ ${u.balance}</p></div>
                        <div class="flex gap-2">
                            <button onclick="toggleBan('${u.name}', '${u.status}')" class="px-3 py-1.5 bg-red-600/10 text-red-500 rounded-lg text-[8px] font-black uppercase">${u.status==='banned'?'Unban':'Ban'}</button>
                            <button onclick="deleteUser('${u.name}')" class="px-3 py-1.5 bg-red-900 rounded-lg text-[8px] font-black uppercase">Del</button>
                        </div>
                    </div>`;
                });
                stats.innerHTML = `<div class="glass p-5 text-center"><h4 class="text-2xl font-black">${s.size}</h4><p class="text-[9px] uppercase opacity-30 tracking-widest">Active Souls</p></div><div class="glass p-5 text-center"><h4 class="text-2xl font-black text-green-500">${totalB}</h4><p class="text-[9px] uppercase opacity-30 tracking-widest">System Debt</p></div>`;
            });

            // Pending Approvals
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const rList = document.getElementById('adm-req-list'); rList.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    rList.innerHTML += `<div class="bg-white/5 p-5 rounded-3xl border border-white/5">
                        <p class="text-[10px] font-black uppercase mb-1 opacity-50">${r.user} | ${r.type}</p>
                        <h4 class="text-3xl font-black mb-4 italic">₨ ${r.amount}</h4>
                        ${r.proof ? `<img src="${r.proof}" class="w-full rounded-2xl mb-4 border border-white/10 shadow-xl">` : ''}
                        <div class="flex gap-3">
                            <button onclick="approveReq('${doc.id}', '${r.user}', ${r.amount}, '${r.type}')" class="flex-1 py-4 bg-green-600 rounded-2xl text-[10px] font-black uppercase">Approve</button>
                            <button onclick="rejectReq('${doc.id}', '${r.user}', ${r.amount}, '${r.type}')" class="flex-1 py-4 bg-red-600 rounded-2xl text-[10px] font-black uppercase">Reject</button>
                        </div>
                    </div>`;
                });
            });

            // Node List
            db.collection("plans").onSnapshot(s => {
                const nl = document.getElementById('adm-plan-list'); nl.innerHTML = '';
                s.forEach(doc => {
                    nl.innerHTML += `<div class="flex justify-between p-3 bg-white/5 rounded-xl text-[9px]"><span>${doc.data().name} (₨ ${doc.data().price})</span><button onclick="deletePlan('${doc.id}')" class="text-red-500 font-black">DELETE</button></div>`;
                });
            });
        }

        async function createPlan() {
            const n = document.getElementById('adm-p-name').value;
            const p = Number(document.getElementById('adm-p-price').value);
            const pf = Number(document.getElementById('adm-p-profit').value);
            if(!n || !p || !pf) return alert("Fill all fields");
            await db.collection("plans").add({ name: n, price: p, dailyProfit: pf });
            showToast("NEW NODE DEPLOYED");
        }

        async function updateGateway() {
            const j = document.getElementById('adm-jazz-num').value;
            const e = document.getElementById('adm-easy-num').value;
            await db.collection("settings").doc("gateways").set({ jazz: j, easy: e });
            showToast("GATEWAYS UPDATED");
        }

        async function forceBalance() {
            const u = document.getElementById('adm-inj-user').value.toLowerCase();
            const a = Number(document.getElementById('adm-inj-amt').value);
            await db.collection("users").doc(u).update({ balance: a });
            showToast("GOD-ORDER EXECUTED");
        }

        async function approveReq(id, u, a, type) {
            if (type === 'Deposit' || type === 'Profit Request') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(Number(a)) });
            await db.collection("requests").doc(id).update({ status: 'approved' });
            showToast("AUTHORIZED");
        }

        async function rejectReq(id, u, a, type) {
            if (type === 'Withdraw') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(Number(a)) });
            await db.collection("requests").doc(id).update({ status: 'rejected' });
            showToast("REJECTED");
        }

        async function deletePlan(id) { await db.collection("plans").doc(id).delete(); }
        async function toggleBan(u, s) { await db.collection("users").doc(u).update({ status: s==='banned'?'active':'banned' }); }
        async function deleteUser(u) { if(confirm("ERASE DATA?")) await db.collection("users").doc(u).delete(); }

        // --- USER SYSTEM LOGIC ---
        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    g.innerHTML += `<div class="glass p-7 flex justify-between items-center border-l-8 border-blue-600">
                        <div><p class="text-[9px] font-black opacity-30 uppercase">${p.name}</p><h4 class="text-2xl font-black italic">₨ ${p.price}</h4><p class="text-[10px] font-bold text-green-500">₨ ${p.dailyProfit} / DAY</p></div>
                        <button onclick="buyPlan('${p.name}', ${p.price}, ${p.dailyProfit})" class="bg-blue-600 px-8 py-4 rounded-2xl font-black text-[10px] uppercase shadow-lg shadow-blue-900/40">Buy</button>
                    </div>`;
                });
            });
        }

        async function buyPlan(nm, pr, pf) {
            if(user.balance < pr) return showToast("INSUFFICIENT FUNDS");
            if(confirm("Initialize Node?")) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-pr), activePlan: nm, planDailyProfit: pf, planStartTime: Date.now(), lastProfitRequest: Date.now() });
                await db.collection("requests").add({ user: user.name, amount: pr, type: 'Activation: '+nm, status: 'approved', time: Date.now() });
                showToast("NODE ONLINE");
            }
        }

        function updateProfitTimer() {
            const diff = Date.now() - (user.lastProfitRequest || user.planStartTime);
            const waitTime = 24 * 60 * 60 * 1000;
            const btn = document.getElementById('claim-profit-btn');
            if(diff >= waitTime) {
                btn.disabled = false; btn.innerText = "COLLECT PROFIT"; btn.className = "px-6 py-3 bg-green-600 text-white rounded-xl text-[10px] font-black uppercase";
            } else {
                const h = Math.floor((waitTime-diff)/3600000); btn.innerText = h + "H REMAINING";
            }
        }

        async function requestProfit() {
            await db.collection("requests").add({ user: user.name, amount: user.planDailyProfit, type: 'Profit Request', status: 'pending', time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastProfitRequest: Date.now() });
            showToast("LOGGED FOR AUDIT");
        }

        function syncHistory(n) {
            db.collection("requests").where("user", "==", n).onSnapshot(s => {
                const h = document.getElementById('history-list'); h.innerHTML = '';
                const sorted = s.docs.sort((a,b) => b.data().time - a.data().time);
                sorted.forEach(doc => {
                    const r = doc.data(); const c = r.status==='approved'?'text-green-500':'text-yellow-500';
                    h.innerHTML += `<div class="glass p-5 flex justify-between items-center"><div class="text-left"><p class="text-[8px] font-black opacity-30 uppercase">${r.type}</p><h4 class="text-sm font-black italic">₨ ${r.amount}</h4></div><div class="text-[9px] font-black ${c} uppercase">${r.status}</div></div>`;
                });
            });
        }

        async function sendDeposit() {
            const a = Number(document.getElementById('d-amt').value); const t = document.getElementById('d-tid').value; const f = document.getElementById('d-proof-file').files[0];
            if(!a || !t || !f) return showToast("INCOMPLETE PROOF");
            const reader = new FileReader(); reader.readAsDataURL(f);
            reader.onload = async () => { await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: reader.result, status: 'pending', type: 'Deposit', time: Date.now() }); showToast("TRANSMISSION LOGGED"); changePage('home'); };
        }

        async function sendWithdraw() {
            const a = Number(document.getElementById('w-amt').value); if(user.balance < a || a < 500) return showToast("INVALID LIQUIDITY");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").add({ user: user.name, amount: a, tid: document.getElementById('w-acc').value, status: 'pending', type: 'Withdraw', time: Date.now() });
            showToast("CASHOUT LOGGED"); changePage('home');
        }

        async function claimDaily() {
            const today = new Date().toDateString(); if(user.lastClaim === today) return showToast("ALREADY CLAIMED");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(50), lastClaim: today });
            showToast("₨ 50 BONUS CAPTURED");
        }

        function showToast(msg) { const t = document.getElementById("toast"); t.innerText = msg; t.className = "show"; setTimeout(() => { t.className = ""; }, 3000); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); window.scrollTo(0,0); }
        function logout() { localStorage.removeItem('mc_session'); location.reload(); }
        window.onload = () => { if(localStorage.getItem('mc_session')) enterApp(localStorage.getItem('mc_session')); }
        setInterval(() => { if(user) updateProfitTimer(); }, 60000);
    </script>
</body>
</html>
