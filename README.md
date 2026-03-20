<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Prime Enterprise</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --blue: #2f81f7; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); border-radius: 12px; }
        .page { display: none; animation: fadeIn 0.3s ease-in-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        input { background: #161b22; border: 1px solid var(--border); color: white; padding: 12px; border-radius: 10px; width: 100%; outline: none; margin-bottom: 8px; font-size: 13px; }
        .btn-prime { background: linear-gradient(135deg, #2f81f7, #1e40af); color: white; font-weight: 700; border-radius: 10px; cursor: pointer; border: none; }
        .ticker-wrap { background: #0d1117; border-bottom: 1px solid var(--border); padding: 6px 0; overflow: hidden; position: sticky; top: 0; z-index: 6000; }
        .ticker { display: inline-block; white-space: nowrap; animation: marquee 30s linear infinite; font-size: 9px; font-weight: 800; color: #2f81f7; text-transform: uppercase; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        #toast { visibility: hidden; min-width: 200px; background: #2f81f7; color: #fff; text-align: center; border-radius: 4px; padding: 12px; position: fixed; z-index: 10000; left: 50%; transform: translateX(-50%); bottom: 90px; font-size: 10px; font-weight: 800; }
        #toast.show { visibility: visible; animation: fadein 0.5s, fadeout 0.5s 2.5s; }
        @keyframes fadein { from {bottom: 0; opacity: 0;} to {bottom: 90px; opacity: 1;} }
        @keyframes fadeout { from {bottom: 90px; opacity: 1;} to {bottom: 0; opacity: 0;} }
        .wa-float { position: fixed; bottom: 100px; right: 20px; background: #25d366; color: white; width: 50px; height: 50px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 24px; box-shadow: 0 4px 12px rgba(0,0,0,0.3); z-index: 4500; }
    </style>
</head>
<body onclick="handleSecretBypass()">

    <div id="toast">SYSTEM UPDATED</div>

    <div class="ticker-wrap">
        <div class="ticker">
            &bull; Payout of PKR 5,400 processed for @UserNode &bull; New Diamond Plan activated by @ProInvestor &bull; System Optimization Complete &bull; Commission of PKR 1,200 paid to @RefManager
        </div>
    </div>

    <div id="lock-screen" class="fixed inset-0 z-[1000000] bg-[#010409] hidden flex flex-col items-center justify-center p-10 text-center">
        <div class="w-12 h-12 border-2 border-blue-500 border-t-transparent rounded-full animate-spin mb-6"></div>
        <h1 class="text-xl font-bold text-blue-500 mb-2 uppercase tracking-widest">System Maintenance</h1>
        <p class="text-[9px] font-medium opacity-50 uppercase tracking-[2px]">The platform is currently undergoing scheduled upgrades.</p>
    </div>

    <header class="p-4 flex justify-between items-center bg-[#010409] border-b border-white/5 sticky top-0 z-[5000]">
        <div class="flex items-center gap-3">
            <div onclick="initAdminPanel()" class="w-10 h-10 bg-blue-600 rounded-lg flex items-center justify-center text-white font-bold text-lg">M</div>
            <div>
                <h1 class="text-[11px] font-extrabold uppercase tracking-tight">MINTCREST <span class="text-blue-500">GOLD</span></h1>
                <p id="user-id-tag" class="text-[7px] font-bold opacity-40 uppercase tracking-widest">Node Verified</p>
            </div>
        </div>
        <div id="top-bal" class="bg-blue-600/10 px-4 py-2 rounded-md border border-blue-500/20 text-blue-500 text-[10px] font-extrabold">PKR 0</div>
    </header>

    <main id="app-ui" class="hidden p-5 space-y-6 pb-32">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="glass p-8 bg-gradient-to-br from-blue-900 to-[#010409]">
                <p class="text-[9px] font-bold opacity-50 uppercase tracking-widest text-blue-400">Current Balance</p>
                <h2 class="text-4xl font-extrabold tracking-tighter mt-1" id="v-bal">PKR 0</h2>
                <div class="mt-8 flex gap-3">
                    <button onclick="changePage('wallet')" class="flex-1 py-4 bg-blue-500 text-white rounded-lg font-bold text-[9px] uppercase">Deposit</button>
                    <button onclick="changePage('withdraw')" class="flex-1 py-4 bg-white/5 border border-white/10 rounded-lg font-bold text-[9px] uppercase">Withdraw</button>
                </div>
            </div>
            <div id="plans-container" class="space-y-4"></div>
        </div>

        <div id="p-team" class="page space-y-6">
            <div class="glass p-6 text-center border-l-4 border-blue-500">
                <p class="text-[9px] font-bold opacity-40 uppercase mb-2">My Referral Link</p>
                <p id="ref-link" class="text-[10px] font-bold text-blue-500 break-all bg-white/5 p-3 rounded-lg">---</p>
                <button onclick="copyRef()" class="mt-2 text-[8px] uppercase font-bold text-blue-400">Copy Link</button>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <div class="glass p-5 text-center"><h4 id="team-size" class="text-xl font-bold">0</h4><p class="text-[8px] opacity-40 uppercase">Total Members</p></div>
                <div class="glass p-5 text-center"><h4 id="team-earn" class="text-xl font-bold text-green-500">PKR 0</h4><p class="text-[8px] opacity-40 uppercase">Total Team Profit</p></div>
            </div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <div id="gateway-list" class="space-y-2"></div>
            <div class="glass p-6 space-y-4">
                <input type="number" id="d-amount" placeholder="Amount (PKR)">
                <input type="text" id="d-tid" placeholder="TID (Transaction ID)">
                <input type="file" id="d-file" class="text-[10px]">
                <button onclick="submitDeposit()" class="w-full py-4 btn-prime uppercase text-[10px]">Verify Deposit</button>
            </div>
        </div>

        <div id="p-history" class="page space-y-3"><div id="log-list" class="space-y-2"></div></div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6 pb-40">
        <div class="flex justify-between items-center mb-8 border-b border-white/5 pb-4">
            <h2 class="text-lg font-bold text-blue-500 uppercase">Administrator Terminal</h2>
            <button onclick="closeAdmin()" class="text-[9px] font-bold uppercase opacity-50">Exit</button>
        </div>

        <div class="grid grid-cols-1 gap-4 mb-8">
            <button onclick="toggleMaintenance()" id="m-btn" class="py-4 border border-blue-500/20 rounded-lg font-bold text-[9px] uppercase">Lock System</button>
            <div class="grid grid-cols-2 gap-4">
                <div class="glass p-5 text-center"><h4 id="adm-users" class="text-xl font-bold">0</h4><p class="text-[8px] opacity-40 uppercase">Registered</p></div>
                <div class="glass p-5 text-center text-blue-500"><h4 id="adm-pending" class="text-xl font-bold">0</h4><p class="text-[8px] opacity-40 uppercase">Pending</p></div>
            </div>
        </div>

        <div class="glass p-6 mb-6">
            <h4 class="text-[9px] font-bold uppercase text-blue-500 mb-4">Investment Node Factory</h4>
            <input id="adm-n-name" placeholder="Plan Title">
            <input id="adm-n-price" type="number" placeholder="Entry Price">
            <input id="adm-n-profit" type="number" placeholder="Daily Yield">
            <button onclick="addNode()" class="w-full py-3 bg-blue-600 rounded-lg font-bold text-[9px]">DEPLOY PLAN</button>
        </div>

        <div id="adm-requests" class="space-y-4"></div>
    </div>

    <a href="https://wa.me/923705519562" class="wa-float" target="_blank"><i class="fa-brands fa-whatsapp"></i></a>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass bg-[#0d1117]/95 border-t border-white/5 p-6 flex justify-around items-center z-[4000] rounded-t-[30px]">
        <button onclick="changePage('home')" class="opacity-40"><i class="fa-solid fa-chart-pie text-xl"></i></button>
        <button onclick="changePage('team')" class="opacity-40"><i class="fa-solid fa-user-group text-xl"></i></button>
        <button onclick="changePage('wallet')" class="opacity-40"><i class="fa-solid fa-wallet text-xl"></i></button>
        <button onclick="changePage('history')" class="opacity-40"><i class="fa-solid fa-receipt text-xl"></i></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let tapCount = 0; let secretCount = 0; let mActive = false;

        // --- ADMIN SYSTEM ---
        function initAdminPanel() { tapCount++; if(tapCount >= 5) { if(prompt("Security Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function handleSecretBypass() { 
            if(!document.getElementById('lock-screen').classList.contains('hidden')) {
                secretCount++; 
                if(secretCount >= 5) { 
                    if(prompt("Override Key:")==="mint786") { 
                        document.getElementById('lock-screen').classList.add('hidden');
                        document.getElementById('admin-ui').classList.remove('hidden'); 
                        syncAdm();
                    } 
                    secretCount=0; 
                } 
            }
        }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        async function syncAdm() {
            db.collection("users").onSnapshot(s => document.getElementById('adm-users').innerText = s.size);
            db.collection("requests").where("status","==","pending").onSnapshot(s => {
                document.getElementById('adm-pending').innerText = s.size;
                const list = document.getElementById('adm-requests'); list.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    list.innerHTML += `<div class="glass p-5 border-l-4 border-blue-500 text-[10px] font-bold uppercase">
                        <p class="mb-2">${r.user} | ${r.type} | PKR ${r.amount}</p>
                        ${r.proof ? `<img src="${r.proof}" class="w-full rounded-lg mb-3">` : ''}
                        <div class="flex gap-2"><button onclick="approveDeposit('${doc.id}','${r.user}',${r.amount})" class="flex-1 py-3 bg-green-600 rounded-lg">APPROVE</button></div>
                    </div>`;
                });
            });
            db.collection("settings").doc("system").onSnapshot(d => {
                mActive = d.data()?.maintenance;
                document.getElementById('m-btn').innerText = mActive ? "SYSTEM LOCKED" : "SYSTEM ACTIVE";
            });
        }

        async function approveDeposit(id, u, a) {
            await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            // Referral Logic
            const d = await db.collection("users").doc(u).get();
            if(d.data().refBy && d.data().refBy !== 'Direct') {
                await db.collection("users").doc(d.data().refBy).update({ balance: firebase.firestore.FieldValue.increment(a * 0.10) });
            }
            await db.collection("requests").doc(id).update({ status: 'approved' });
            showToast("VERIFIED");
        }

        async function addNode() {
            const n = document.getElementById('adm-n-name').value;
            const p = Number(document.getElementById('adm-n-price').value);
            const pf = Number(document.getElementById('adm-n-profit').value);
            await db.collection("plans").add({ name: n, price: p, dailyProfit: pf });
            showToast("NODE DEPLOYED");
        }

        async function toggleMaintenance() { await db.collection("settings").doc("system").set({ maintenance: !mActive }); }

        // --- USER SYSTEM ---
        function syncUser(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                const bal = (user.balance || 0).toLocaleString();
                document.getElementById('v-bal').innerText = "PKR " + bal;
                document.getElementById('top-bal').innerText = "PKR " + bal;
                document.getElementById('ref-link').innerText = window.location.origin + "?ref=" + n;
                // Team Stats
                db.collection("users").where("refBy","==",n).onSnapshot(s => {
                    document.getElementById('team-size').innerText = s.size;
                    let total = 0; s.forEach(doc => total += (doc.data().balance * 0.1));
                    document.getElementById('team-earn').innerText = "PKR " + total.toLocaleString();
                });
            });
            db.collection("settings").doc("system").onSnapshot(d => {
                if(d.data()?.maintenance && n !== 'admin') document.getElementById('lock-screen').classList.remove('hidden');
                else document.getElementById('lock-screen').classList.add('hidden');
            });
            db.collection("plans").orderBy("price","asc").onSnapshot(s => {
                const cont = document.getElementById('plans-container'); cont.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    cont.innerHTML += `<div class="glass p-6 border-l-4 border-blue-500 flex justify-between items-center">
                        <div><p class="text-[8px] font-bold opacity-30 uppercase">${p.name}</p><h4 class="text-xl font-bold">PKR ${p.price}</h4><p class="text-[9px] text-green-500 font-bold">PKR ${p.dailyProfit}/Day</p></div>
                        <button onclick="buyPlan('${p.name}', ${p.price})" class="bg-blue-600 px-6 py-3 rounded-lg font-bold text-[9px]">ACQUIRE</button>
                    </div>`;
                });
            });
        }

        async function submitDeposit() {
            const a = Number(document.getElementById('d-amount').value); const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            if(!a || !t || !f) return alert("All verification proof required.");
            const r = new FileReader(); r.readAsDataURL(f);
            r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: r.result, type: 'Deposit', status: 'pending', time: Date.now() });
                showToast("SUBMITTED FOR AUDIT"); changePage('home');
            };
        }

        function copyRef() {
            const el = document.getElementById('ref-link');
            navigator.clipboard.writeText(el.innerText);
            showToast("LINK COPIED");
        }

        function showToast(m) { const t = document.getElementById("toast"); t.innerText = m; t.className = "show"; setTimeout(()=>t.className="", 3000); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        
        window.onload = () => { 
            const session = localStorage.getItem('mc_session');
            if(session) {
                document.getElementById('user-id-tag').innerText = "ID: " + session.toUpperCase();
                document.getElementById('app-ui').classList.remove('hidden');
                document.getElementById('bottom-nav').classList.remove('hidden');
                syncUser(session);
            }
        }
    </script>
</body>
</html>
