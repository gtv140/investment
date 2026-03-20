<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | v170.0 Ultimate</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --blue: #2f81f7; --green: #238636; --red: #da3633; }
        
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); -webkit-user-select: none; user-select: none; transition: 0.3s; }
        .glass { background: var(--card); border: 1px solid var(--border); border-radius: 20px; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
        
        /* Smooth Page Transitions */
        .page { display: none; transform: translateY(10px); opacity: 0; }
        .active-page { display: block; animation: fadeInUp 0.4s forwards; }
        @keyframes fadeInUp { to { transform: translateY(0); opacity: 1; } }

        .btn-modern { background: var(--blue); color: white; border-radius: 12px; font-weight: 700; transition: 0.3s; border: none; cursor: pointer; display: flex; items-center; justify-center; gap: 8px; }
        .btn-modern:active { transform: scale(0.95); opacity: 0.8; }
        
        input { background: #161b22; border: 1px solid var(--border); color: var(--text); padding: 15px; border-radius: 12px; width: 100%; outline: none; margin-bottom: 15px; font-size: 14px; }
        input:focus { border-color: var(--blue); box-shadow: 0 0 10px rgba(47,129,247,0.2); }

        .nav-item { color: #8b949e; cursor: pointer; transition: 0.3s; display: flex; flex-direction: column; align-items: center; gap: 4px; }
        .nav-item.active { color: var(--blue); }
        .nav-item.active i { transform: scale(1.2); }

        /* Notification Toast */
        #toast { visibility: hidden; min-width: 250px; background-color: var(--blue); color: #fff; text-align: center; border-radius: 10px; padding: 16px; position: fixed; z-index: 10000; left: 50%; transform: translateX(-50%); bottom: 100px; font-weight: 800; font-size: 12px; box-shadow: 0 10px 40px rgba(0,0,0,0.5); }
        #toast.show { visibility: visible; animation: fadein 0.5s, fadeout 0.5s 2.5s; }
        @keyframes fadein { from {bottom: 0; opacity: 0;} to {bottom: 100px; opacity: 1;} }
        @keyframes fadeout { from {bottom: 100px; opacity: 1;} to {bottom: 0; opacity: 0;} }
    </style>
</head>
<body oncontextmenu="return false;">

    <div id="toast">SYSTEM NOTIFICATION</div>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/90 backdrop-blur-xl border-b border-[var(--border)] p-5 flex justify-between items-center">
        <div class="flex items-center gap-3">
            <div onclick="adminTap()" class="w-9 h-9 bg-blue-600 rounded-xl flex items-center justify-center font-black text-white italic shadow-lg">M</div>
            <div>
                <h1 class="text-[12px] font-black uppercase italic tracking-tighter">MintCrest <span class="text-blue-500">Gold</span></h1>
                <p id="u-tag" class="text-[8px] font-bold text-gray-500 uppercase tracking-widest">Global Account</p>
            </div>
        </div>
        <div id="top-bal" class="text-[12px] font-black bg-white/5 px-4 py-2 rounded-xl border border-white/5">₨ 0</div>
    </header>

    <main id="app-ui" class="hidden p-4 space-y-6 pb-24">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="glass p-8 bg-gradient-to-br from-[#1d4ed8] to-[#1e3a8a] text-white relative overflow-hidden">
                <div class="absolute top-0 right-0 p-4 opacity-10"><i class="fa-solid fa-shield-halved text-6xl"></i></div>
                <p class="text-[10px] font-black uppercase opacity-60 tracking-widest mb-1">Available Liquidity</p>
                <h2 class="text-5xl font-extrabold tracking-tighter mb-8" id="v-bal">₨ 0.00</h2>
                <div class="flex gap-3">
                   <button onclick="changePage('wallet')" class="flex-1 bg-white text-blue-900 py-4 rounded-xl font-black text-[10px] uppercase shadow-xl">Deposit</button>
                   <button onclick="changePage('withdraw')" class="flex-1 bg-blue-400/20 py-4 rounded-xl font-black text-[10px] uppercase border border-white/20 backdrop-blur-md">Withdraw</button>
                </div>
            </div>

            <div id="node-active-box" class="hidden glass p-6 border-l-8 border-green-500 bg-green-500/5">
                <div class="flex justify-between items-center mb-4">
                    <div class="flex items-center gap-2">
                        <div class="w-2 h-2 bg-green-500 rounded-full animate-pulse"></div>
                        <p class="text-[10px] font-black uppercase text-green-500">Node Online</p>
                    </div>
                    <p id="p-timer" class="text-[10px] font-black opacity-50">Syncing...</p>
                </div>
                <button id="claim-profit-btn" disabled onclick="requestProfit()" class="w-full py-4 btn-modern text-[10px] uppercase">Awaiting 24h Data</button>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <div onclick="changePage('nodes')" class="glass p-6 text-center active:scale-95 transition cursor-pointer">
                    <i class="fa-solid fa-microchip text-blue-500 text-2xl mb-2"></i>
                    <p class="text-[9px] font-black uppercase">Nodes Store</p>
                </div>
                <div onclick="claimDaily()" class="glass p-6 text-center active:scale-95 transition cursor-pointer">
                    <i class="fa-solid fa-gift text-yellow-500 text-2xl mb-2"></i>
                    <p class="text-[9px] font-black uppercase">Daily Reward</p>
                </div>
            </div>

            <div class="glass p-6 space-y-4">
                <div class="flex items-center gap-3 text-blue-500 mb-2">
                    <i class="fa-solid fa-circle-info"></i>
                    <h4 class="text-[10px] font-black uppercase">Information Center</h4>
                </div>
                <button onclick="changePage('about')" class="w-full flex justify-between items-center py-2 text-[11px] font-bold border-b border-white/5 opacity-70">About Company <i class="fa-solid fa-angle-right"></i></button>
                <button onclick="changePage('privacy')" class="w-full flex justify-between items-center py-2 text-[11px] font-bold opacity-70">Privacy Policy <i class="fa-solid fa-angle-right"></i></button>
            </div>
            
            <button onclick="logout()" class="w-full py-4 text-red-500 font-black text-[10px] uppercase">Sign Out Security Session</button>
        </div>

        <div id="p-history" class="page space-y-4">
            <h3 class="text-xl font-black italic">TRANSACTION LEDGER</h3>
            <div id="history-list" class="space-y-3"></div>
        </div>

        <div id="p-nodes" class="page space-y-6">
            <h3 class="text-xl font-black italic text-center text-blue-500 uppercase">Premium Mining Nodes</h3>
            <div id="plans-grid" class="space-y-4"></div>
        </div>

        <div id="p-wallet" class="page"><div class="glass p-8 space-y-6">
            <h3 class="text-lg font-black uppercase italic">Fund Wallet</h3>
            <div id="gate-nums" class="space-y-3"></div>
            <input id="d-amt" type="number" placeholder="Enter PKR Amount">
            <input id="d-tid" placeholder="Transaction ID (TID)">
            <p class="text-[9px] font-bold opacity-40 uppercase mb-2">Upload Transfer Proof</p>
            <input id="d-proof-file" type="file" accept="image/*" class="text-[10px]">
            <button onclick="sendDeposit()" class="w-full py-5 btn-modern uppercase text-[11px] tracking-widest">Confirm Transmission</button>
        </div></div>

        <div id="p-withdraw" class="page"><div class="glass p-8 space-y-6">
            <h3 class="text-lg font-black uppercase italic text-red-500">Liquidation Request</h3>
            <input id="w-amt" type="number" placeholder="Enter Amount">
            <input id="w-acc" placeholder="Account Name & Number">
            <button onclick="sendWithdraw()" class="w-full py-5 btn-modern bg-red-600 uppercase text-[11px] tracking-widest">Request Cashout</button>
        </div></div>

    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6 pb-40">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-2xl font-black text-blue-500 italic">SYSTEM OVERLORD</h2>
            <button onclick="closeAdmin()" class="px-5 py-2 bg-red-600/10 text-red-500 border border-red-500/20 rounded-xl font-black">CLOSE</button>
        </div>

        <div class="grid grid-cols-2 gap-4 mb-8">
            <div class="glass p-5 text-center"><p id="adm-total-users" class="text-2xl font-black">0</p><p class="text-[9px] uppercase opacity-40">Total Users</p></div>
            <div class="glass p-5 text-center"><p id="adm-total-bal" class="text-2xl font-black text-green-500">0</p><p class="text-[9px] uppercase opacity-40">Liability</p></div>
        </div>

        <div class="glass p-6 border-l-8 border-green-500 mb-8 bg-green-500/5">
            <h4 class="text-[11px] font-black text-green-500 uppercase mb-5">Pending Profits</h4>
            <div id="adm-profit-list" class="space-y-4"></div>
        </div>

        <div class="glass p-6 border-l-8 border-purple-500 mb-8">
            <h4 class="text-[11px] font-black text-purple-500 uppercase mb-5">User Management</h4>
            <div id="adm-user-list" class="space-y-3 max-h-[300px] overflow-y-auto mb-6 pr-2"></div>
            <input id="adm-inj-user" placeholder="Target Username">
            <input id="adm-inj-amt" type="number" placeholder="Bonus Amount">
            <button onclick="manualInjunction()" class="w-full py-4 bg-purple-600 rounded-xl font-black text-[10px] uppercase">Inject Credit</button>
        </div>

        <div id="adm-req-list" class="space-y-8"></div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass bg-[#0d1117]/80 backdrop-blur-2xl border-t border-[var(--border)] p-6 flex justify-around items-center z-[4000] rounded-t-[35px]">
        <div onclick="changePage('home')" class="nav-item active"><i class="fa-solid fa-house-user text-xl"></i><span class="text-[9px] font-bold uppercase">Home</span></div>
        <div onclick="changePage('nodes')" class="nav-item"><i class="fa-solid fa-server text-xl"></i><span class="text-[9px] font-bold uppercase">Nodes</span></div>
        <div onclick="changePage('history')" class="nav-item"><i class="fa-solid fa-receipt text-xl"></i><span class="text-[9px] font-bold uppercase">Ledger</span></div>
    </nav>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-black flex flex-col items-center justify-center p-12 text-center">
        <div class="w-20 h-20 bg-blue-600 rounded-[2rem] mb-10 flex items-center justify-center text-4xl font-black italic text-white shadow-2xl">M</div>
        <h2 class="text-3xl font-black uppercase tracking-tighter mb-10 italic">MintCrest <span class="text-blue-500">Gold</span></h2>
        <input id="l-name" placeholder="VERIFICATION NAME" class="text-center font-bold uppercase py-5 max-w-[320px]">
        <button onclick="login()" class="w-full max-w-[320px] py-6 btn-modern uppercase text-[11px] tracking-widest shadow-2xl">Access Vault</button>
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
        let user = null; let tapCount = 0;

        // --- AUTH & CORE ---
        function login() {
            const n = document.getElementById('l-name').value.trim().toLowerCase();
            if(n.length < 3) return showToast("INVALID ID PROVIDED");
            db.collection("users").doc(n).get().then(doc => {
                if(doc.exists && doc.data().status === 'banned') return showToast("ACCOUNT TERMINATED");
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
            document.getElementById('u-tag').innerText = "@" + n.toUpperCase();

            db.collection("users").doc(n).onSnapshot(doc => {
                user = doc.data();
                if(user.status === 'banned') logout();
                animateBalance(user.balance);
                updateProfitTimer();
            });
            
            db.collection("settings").doc("global").onSnapshot(d => {
                const s = d.data() || {};
                document.getElementById('gate-nums').innerHTML = `
                    <div class="p-4 bg-white/5 rounded-xl border border-white/5"><p class="text-[8px] opacity-30 font-black">JAZZCASH</p><h4 class="font-bold">${s.jazzNum || '03...'}</h4></div>
                    <div class="p-4 bg-white/5 rounded-xl border border-white/5"><p class="text-[8px] opacity-30 font-black">EASYPAISA</p><h4 class="font-bold">${s.easyNum || '03...'}</h4></div>
                `;
            });
            syncPlans(); syncHistory(n);
        }

        function animateBalance(val) {
            document.getElementById('v-bal').innerText = "₨ " + val.toLocaleString();
            document.getElementById('top-bal').innerText = "₨ " + val.toLocaleString();
        }

        // --- CRITICAL FIX: APPROVAL ENGINE ---
        async function approveReq(id, u, a, type) {
            try {
                const userRef = db.collection("users").doc(u.toLowerCase());
                if (type === 'Deposit' || type === 'Profit Request' || type === 'Injunction') {
                    await userRef.update({ balance: firebase.firestore.FieldValue.increment(Number(a)) });
                }
                await db.collection("requests").doc(id).update({ status: 'approved' });
                showToast("TRANSACTION AUTHORIZED");
            } catch (e) { showToast("SYSTEM ERROR"); }
        }

        async function rejectReq(id, u, a, type) {
            if (type === 'Withdraw') await db.collection("users").doc(u.toLowerCase()).update({ balance: firebase.firestore.FieldValue.increment(Number(a)) });
            await db.collection("requests").doc(id).update({ status: 'rejected' });
            showToast("TRANSACTION DENIED");
        }

        // --- PROFIT LOGIC ---
        function updateProfitTimer() {
            if(!user.activePlan) return document.getElementById('node-active-box').classList.add('hidden');
            document.getElementById('node-active-box').classList.remove('hidden');
            const diff = Date.now() - (user.lastProfitRequest || user.planStartTime);
            const waitTime = 24 * 60 * 60 * 1000;

            if(diff >= waitTime) {
                const btn = document.getElementById('claim-profit-btn');
                btn.disabled = false; btn.innerText = "COLLECT DAILY PROFIT";
                btn.className = "w-full py-4 bg-green-600 text-white text-[10px] font-black uppercase rounded-xl shadow-lg shadow-green-600/20";
                document.getElementById('p-timer').innerText = "DATA READY";
            } else {
                const h = Math.floor((waitTime - diff) / 3600000);
                document.getElementById('p-timer').innerText = h + "H REMAINING";
                document.getElementById('claim-profit-btn').disabled = true;
            }
        }

        async function requestProfit() {
            await db.collection("requests").add({ user: user.name, amount: user.planDailyProfit, type: 'Profit Request', status: 'pending', time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastProfitRequest: Date.now() });
            showToast("PROFIT LOGGED FOR REVIEW");
        }

        // --- ADMIN MONARCH COMMANDS ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("ACCESS CODE:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        function syncAdm() {
            db.collection("users").onSnapshot(s => {
                const ul = document.getElementById('adm-user-list'); ul.innerHTML = '';
                let tb = 0;
                s.forEach(doc => { 
                    const u = doc.data(); tb += u.balance;
                    ul.innerHTML += `<div class="bg-white/5 p-4 rounded-xl flex justify-between items-center text-[10px]">
                        <div><p class="font-bold ${u.status==='banned'?'text-red-500':'text-blue-400'}">${u.name.toUpperCase()}</p><p class="opacity-40">₨ ${u.balance}</p></div>
                        <div class="flex gap-2">
                            <button onclick="toggleBan('${u.name}', '${u.status}')" class="px-3 py-1 bg-red-600/20 rounded">${u.status==='banned'?'Unban':'Ban'}</button>
                            <button onclick="deleteAccount('${u.name}')" class="px-3 py-1 bg-red-900 rounded">Del</button>
                        </div>
                    </div>`;
                });
                document.getElementById('adm-total-users').innerText = s.size;
                document.getElementById('adm-total-bal').innerText = "₨ " + tb.toLocaleString();
            });

            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-req-list');
                const pList = document.getElementById('adm-profit-list');
                list.innerHTML = ''; pList.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    const html = `<div class="glass p-6 border-l-8 ${r.type==='Deposit'?'border-blue-500':'border-red-500'}">
                        <p class="text-[9px] font-bold opacity-40 uppercase">${r.user} | ${r.type}</p>
                        <h4 class="text-3xl font-black italic">₨ ${r.amount}</h4>
                        <p class="text-[10px] opacity-40 mb-4">${r.tid || ''}</p>
                        ${r.proof ? `<img src="${r.proof}" class="w-full rounded-xl mb-4 border border-white/5">` : ''}
                        <div class="flex gap-3">
                            <button onclick="approveReq('${doc.id}', '${r.user}', ${r.amount}, '${r.type}')" class="flex-1 bg-green-600 py-4 rounded-xl text-[10px] font-black uppercase">Approve</button>
                            <button onclick="rejectReq('${doc.id}', '${r.user}', ${r.amount}, '${r.type}')" class="flex-1 bg-red-600 py-4 rounded-xl text-[10px] font-black uppercase">Reject</button>
                        </div>
                    </div>`;
                    if(r.type === 'Profit Request') pList.innerHTML += html;
                    else list.innerHTML += html;
                });
            });
        }

        async function manualInjunction() {
            const u = document.getElementById('adm-inj-user').value.toLowerCase();
            const a = Number(document.getElementById('adm-inj-amt').value);
            await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").add({ user: u, amount: a, type: 'Injunction', status: 'approved', time: Date.now() });
            showToast("CREDIT INJECTED");
        }

        async function toggleBan(u, s) { await db.collection("users").doc(u).update({ status: s === 'banned' ? 'active' : 'banned' }); }
        async function deleteAccount(u) { if(confirm("DELETE PERMANENTLY?")) await db.collection("users").doc(u).delete(); }

        // --- UI UTILS ---
        function showToast(msg) {
            const t = document.getElementById("toast");
            t.innerText = msg; t.className = "show";
            setTimeout(() => { t.className = t.className.replace("show", ""); }, 3000);
        }

        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); 
            document.querySelectorAll('.nav-item').forEach(nv => nv.classList.remove('active'));
            document.getElementById('p-'+p).classList.add('active-page');
            const nav = Array.from(document.querySelectorAll('.nav-item')).find(n => n.onclick.toString().includes(p));
            if(nav) nav.classList.add('active');
            window.scrollTo(0,0); 
        }

        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    g.innerHTML += `<div class="glass p-8 flex justify-between items-center bg-white/5 border-l-[12px] border-blue-600">
                        <div class="text-left"><p class="text-[10px] font-black opacity-30 uppercase">${p.name}</p><h4 class="text-2xl font-black italic">₨ ${p.price}</h4><p class="text-[9px] font-bold text-blue-500 mt-2">₨ ${p.dailyProfit} DAILY PROFIT</p></div>
                        <button onclick="buyPlan('${doc.id}', ${p.price}, ${p.dailyProfit}, '${p.name}')" class="btn-modern px-8 py-4 text-[10px] uppercase">Initialize</button>
                    </div>`;
                });
            });
        }

        async function buyPlan(id, pr, pf, nm) {
            if(user.balance < pr) return showToast("INSUFFICIENT FUNDS");
            if(confirm("Confirm Deployment?")) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-pr), activePlan: nm, planDailyProfit: pf, planStartTime: Date.now(), lastProfitRequest: Date.now() });
                await db.collection("requests").add({ user: user.name, amount: pr, type: 'Activated: '+nm, status: 'approved', time: Date.now() });
                showToast("NODE DEPLOYED SUCCESSFULLY");
            }
        }

        function syncHistory(n) {
            db.collection("requests").where("user", "==", n).onSnapshot(s => {
                const h = document.getElementById('history-list'); h.innerHTML = '';
                if(s.empty) h.innerHTML = '<p class="text-center opacity-30 text-[10px] py-10">NO RECORDS FOUND</p>';
                const sorted = s.docs.sort((a,b) => b.data().time - a.data().time);
                sorted.forEach(doc => {
                    const r = doc.data(); const st = r.status==='approved'?'text-green-500':'text-yellow-500';
                    h.innerHTML += `<div class="glass p-5 flex justify-between items-center"><div class="text-left"><p class="text-[8px] opacity-40 font-bold uppercase">${r.type}</p><h4 class="text-sm font-black italic">₨ ${r.amount}</h4></div><div class="text-[8px] font-black uppercase ${st}">${r.status}</div></div>`;
                });
            });
        }

        async function sendDeposit() {
            const a = Number(document.getElementById('d-amt').value); const t = document.getElementById('d-tid').value; const f = document.getElementById('d-proof-file').files[0];
            if(!a || !t || !f) return showToast("ALL PROOFS REQUIRED");
            const r = new FileReader(); r.readAsDataURL(f);
            r.onload = async () => { await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: r.result, status: 'pending', type: 'Deposit', time: Date.now() }); showToast("DEPOSIT LOGGED"); changePage('home'); };
        }

        async function sendWithdraw() {
            const a = Number(document.getElementById('w-amt').value);
            if(user.balance < a) return showToast("INSUFFICIENT LIQUIDITY");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").add({ user: user.name, amount: a, tid: document.getElementById('w-acc').value, status: 'pending', type: 'Withdraw', time: Date.now() });
            showToast("CASHOUT REQUESTED"); changePage('home');
        }

        async function claimDaily() {
            const today = new Date().toDateString();
            if(user.lastClaim === today) return showToast("ALREADY CLAIMED TODAY");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(50), lastClaim: today });
            showToast("₨ 50 BONUS CAPTURED");
        }

        function logout() { localStorage.removeItem('mc_session'); location.reload(); }
        window.onload = () => { if(localStorage.getItem('mc_session')) enterApp(localStorage.getItem('mc_session')); }
        setInterval(() => { if(user) updateProfitTimer(); }, 30000);
    </script>
</body>
</html>
