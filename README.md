<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Monarch Ultimate</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --blue: #2f81f7; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); overflow-x: hidden; padding-bottom: 100px; }
        .glass { background: var(--card); border: 1px solid var(--border); border-radius: 28px; }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        input, select { background: #161b22; border: 1px solid var(--border); color: white; padding: 16px; border-radius: 16px; width: 100%; outline: none; margin-bottom: 12px; font-size: 13px; }
        .btn-monarch { background: linear-gradient(135deg, #2f81f7, #1e3a8a); color: white; font-weight: 800; border-radius: 18px; cursor: pointer; border: none; transition: 0.2s; }
        .btn-monarch:active { transform: scale(0.96); }
        .ticker-wrap { background: rgba(47, 129, 247, 0.05); border-top: 1px solid var(--border); padding: 8px 0; overflow: hidden; white-space: nowrap; }
        .ticker { display: inline-block; animation: marquee 25s linear infinite; font-size: 10px; font-weight: 900; color: #2f81f7; text-transform: uppercase; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
    </style>
</head>
<body>

    <div id="lock-screen" class="fixed inset-0 z-[1000000] bg-[#010409] hidden flex flex-col items-center justify-center p-10 text-center">
        <div class="w-24 h-24 bg-blue-600/10 rounded-full flex items-center justify-center mb-6 animate-bounce text-5xl">⚡</div>
        <h1 class="text-2xl font-black text-blue-500 mb-2 uppercase">Core Optimization</h1>
        <p class="text-[10px] font-bold opacity-40 uppercase tracking-[3px]">Nodes are refreshing, sweetie! 😘</p>
    </div>

    <header class="p-5 flex justify-between items-center sticky top-0 bg-[#010409]/90 backdrop-blur-xl border-b border-white/5 z-[5000]">
        <div class="flex items-center gap-3">
            <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center text-white italic font-black shadow-lg">M</div>
            <div>
                <h1 class="text-[12px] font-black uppercase tracking-tighter italic">MintCrest <span class="text-blue-500">Gold</span></h1>
                <p id="user-id-tag" class="text-[7px] font-bold opacity-40 uppercase tracking-widest">Global Node</p>
            </div>
        </div>
        <div class="flex items-center gap-3">
            <div class="bg-blue-600/10 border border-blue-500/20 px-4 py-2 rounded-full flex items-center gap-2">
                <i class="fa-solid fa-gem text-blue-500 text-[10px]"></i>
                <span id="top-bal" class="text-[11px] font-black italic text-blue-500">₨ 0</span>
            </div>
        </div>
    </header>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[#010409] flex flex-col items-center justify-center p-8">
        <div class="w-20 h-20 bg-blue-600 rounded-[2rem] mb-8 flex items-center justify-center text-4xl font-black text-white italic shadow-2xl">M</div>
        <h2 class="text-2xl font-black italic mb-10 uppercase">Secure Login</h2>
        <div class="w-full max-w-[320px] space-y-4">
            <input type="text" id="l-user" placeholder="USERNAME" class="text-center font-black uppercase">
            <input type="password" id="l-pass" placeholder="PASSWORD" class="text-center font-black uppercase">
            <button onclick="login()" class="w-full py-5 btn-monarch uppercase text-[12px] tracking-widest shadow-xl">Authorize Access</button>
        </div>
    </section>

    <main id="app-ui" class="hidden p-5 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-9 rounded-[3rem] bg-gradient-to-br from-blue-700 to-blue-900 text-white shadow-2xl relative overflow-hidden">
                <div class="absolute -right-5 -top-5 opacity-10"><i class="fa-solid fa-coins text-[12rem]"></i></div>
                <p class="text-[10px] font-black uppercase opacity-60 tracking-[4px]">Liquid Assets</p>
                <h2 class="text-5xl font-black tracking-tighter mt-2 italic" id="v-bal">₨ 0.00</h2>
                <div class="mt-8 flex gap-3">
                    <button onclick="changePage('wallet')" class="flex-1 bg-white text-blue-900 py-4 rounded-2xl font-black text-[10px] uppercase">Deposit</button>
                    <button onclick="changePage('withdraw')" class="flex-1 bg-black/20 border border-white/10 py-4 rounded-2xl font-black text-[10px] uppercase">Cashout</button>
                </div>
            </div>

            <div id="mining-status" class="hidden glass p-6 border-l-[8px] border-blue-500 bg-blue-500/5 flex justify-between items-center">
                <div>
                    <p class="text-[9px] font-black uppercase text-blue-500 mb-1">Active Mining</p>
                    <h4 id="active-plan-name" class="text-lg font-black italic">--</h4>
                </div>
                <button id="claim-btn" disabled onclick="requestProfit()" class="px-6 py-3 bg-white/5 rounded-xl text-[10px] font-black uppercase border border-white/10 opacity-30">Cycle Pending</button>
            </div>

            <div id="plans-grid" class="space-y-4"></div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <h3 class="text-center font-black uppercase text-[10px] tracking-widest opacity-40 italic">Global Funding Nodes</h3>
            <div id="gateways-display" class="space-y-3"></div>
            <div class="glass p-8 space-y-4">
                <input type="number" id="dep-amt" placeholder="Amount (PKR)">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                <input type="file" id="dep-file" class="text-[10px] p-3 bg-white/5 border-dashed border-white/10 rounded-2xl">
                <button onclick="submitDep()" class="w-full py-5 btn-monarch uppercase text-[11px] tracking-widest shadow-xl">Confirm Transmission</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-6">
            <h3 class="text-center font-black uppercase text-[10px] tracking-widest opacity-40 text-red-500">Asset Liquidation</h3>
            <div class="glass p-8 space-y-4">
                <input type="number" id="w-amt" placeholder="Min ₨ 500">
                <input type="text" id="w-acc" placeholder="Account Number">
                <input type="text" id="w-name" placeholder="Account Title">
                <button onclick="submitWith()" class="w-full py-5 bg-red-600 rounded-2xl font-black text-[11px] uppercase text-white">Authorize Payout</button>
            </div>
        </div>

        <div id="p-history" class="page space-y-4">
            <h3 class="text-[10px] font-black uppercase opacity-40 italic">Transaction Ledger</h3>
            <div id="history-list" class="space-y-3"></div>
        </div>

    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6 pb-40">
        <div class="flex justify-between items-center mb-10 border-b border-white/5 pb-5">
            <h2 class="text-2xl font-black text-blue-500 italic uppercase">God Mode Console</h2>
            <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black uppercase">Exit System</button>
        </div>

        <div class="grid grid-cols-2 gap-4 mb-10">
            <div class="glass p-6 text-center border-b-4 border-blue-500"><h4 id="adm-count" class="text-3xl font-black">0</h4><p class="text-[8px] font-bold uppercase opacity-40">Souls Active</p></div>
            <div onclick="toggleMaintenance()" class="glass p-6 text-center border-b-4 border-red-500"><h4 id="m-status" class="text-xl font-black uppercase">OFF</h4><p class="text-[8px] font-bold uppercase opacity-40">Lockdown Mode</p></div>
        </div>

        <div class="glass p-8 mb-8 border-l-[10px] border-blue-600">
            <h4 class="text-[10px] font-black uppercase mb-5 text-blue-500">Node Factory</h4>
            <input id="adm-p-name" placeholder="Plan Name">
            <input id="adm-p-price" type="number" placeholder="Price">
            <input id="adm-p-profit" type="number" placeholder="Daily Profit">
            <button onclick="createPlan()" class="w-full py-4 bg-blue-600 rounded-xl font-black text-[10px] uppercase">Deploy Node</button>
            <div id="adm-plans-list" class="mt-4 space-y-2"></div>
        </div>

        <div class="glass p-8 mb-8 border-l-[10px] border-yellow-500">
            <h4 class="text-[10px] font-black uppercase mb-5 text-yellow-500 italic">Financial Gates</h4>
            <input id="adm-jazz" placeholder="JazzCash Number">
            <input id="adm-easy" placeholder="EasyPaisa Number">
            <button onclick="updateGates()" class="w-full py-4 bg-yellow-600 rounded-xl font-black text-[10px] uppercase">Update Numbers</button>
        </div>

        <div class="space-y-6" id="adm-requests"></div>
    </div>

    <div class="fixed bottom-[90px] w-full ticker-wrap z-[4000]">
        <div class="ticker">💸 ₨ 4,500 withdrawn by @UserNode 💸 ₨ 12,000 withdrawn by @EliteSoul 💸 ₨ 2,800 withdrawn by @GoldMiner 💸</div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass bg-[#0d1117]/95 backdrop-blur-2xl border-t border-white/5 p-6 flex justify-around items-center z-[5000] rounded-t-[40px] shadow-2xl">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1.5 opacity-40"><i class="fa-solid fa-house-chimney text-xl"></i><span class="text-[8px] font-black uppercase">Home</span></button>
        <button onclick="changePage('wallet')" class="flex flex-col items-center gap-1.5 opacity-40"><i class="fa-solid fa-wallet text-xl"></i><span class="text-[8px] font-black uppercase">Fund</span></button>
        <button onclick="changePage('withdraw')" class="flex flex-col items-center gap-1.5 opacity-40"><i class="fa-solid fa-money-bill-transfer text-xl"></i><span class="text-[8px] font-black uppercase">Pay</span></button>
        <button onclick="changePage('history')" class="flex flex-col items-center gap-1.5 opacity-40"><i class="fa-solid fa-receipt text-xl"></i><span class="text-[8px] font-black uppercase">Logs</span></button>
    </nav>

    <script>
        // --- FIREBASE INITIALIZATION ---
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let tapCount = 0; let mActive = false;

        // --- AUTH & LOGIN ---
        async function login() {
            const u = document.getElementById('l-user').value.trim().toLowerCase();
            const p = document.getElementById('l-pass').value.trim();
            if(!u || !p) return alert("Credentials required, sweetie! 😘");
            const ref = db.collection("users").doc(u);
            const doc = await ref.get();
            if(!doc.exists) {
                const refBy = new URLSearchParams(window.location.search).get('ref') || "Direct";
                await ref.set({ name: u, password: p, balance: 0, refBy: refBy, time: Date.now() });
                alert("Node Created! 😘");
            } else if(doc.data().password !== p) return alert("Wrong Key! 😘");
            localStorage.setItem('mc_session', u);
            location.reload();
        }

        function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('user-id-tag').innerText = "ID: " + n.toUpperCase();
            sync(n);
        }

        // --- CORE SYNC ---
        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                if(user.banned) location.reload();
                const bal = (user.balance || 0).toLocaleString();
                document.getElementById('v-bal').innerText = "₨ " + bal;
                document.getElementById('top-bal').innerText = "₨ " + bal;
                if(user.activePlan) {
                    document.getElementById('mining-status').classList.remove('hidden');
                    document.getElementById('active-plan-name').innerText = user.activePlan;
                    updateProfitTimer();
                }
            });
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    g.innerHTML += `<div class="glass p-7 border-l-8 border-blue-600 flex justify-between items-center">
                        <div><p class="text-[9px] font-black opacity-30 uppercase">${p.name}</p><h4 class="text-2xl font-black italic">₨ ${p.price}</h4><p class="text-[10px] font-bold text-green-500">₨ ${p.dailyProfit} / DAY</p></div>
                        <button onclick="buyNode('${p.name}', ${p.price}, ${p.dailyProfit})" class="bg-blue-600 px-8 py-4 rounded-2xl font-black text-[10px] uppercase shadow-lg">Buy</button>
                    </div>`;
                });
            });
            db.collection("settings").doc("gateways").onSnapshot(d => {
                const g = d.data() || { jazz: '---', easy: '---' };
                document.getElementById('gateways-display').innerHTML = `<div class="p-5 glass border-l-4 border-red-500 flex justify-between items-center"><p class="text-xs font-black uppercase">JazzCash</p><p class="text-lg font-black text-blue-500">${g.jazz}</p></div><div class="p-5 glass border-l-4 border-green-500 flex justify-between items-center"><p class="text-xs font-black uppercase">EasyPaisa</p><p class="text-lg font-black text-yellow-500">${g.easy}</p></div>`;
            });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").limit(10).onSnapshot(s => {
                const l = document.getElementById('history-list'); l.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    l.innerHTML += `<div class="glass p-4 flex justify-between items-center border-l-4 ${r.status==='approved'?'border-green-500':'border-yellow-500'}">
                        <div class="text-[10px] font-black uppercase"><h4>${r.type}</h4><p class="opacity-30">${new Date(r.time).toLocaleDateString()}</p></div>
                        <div class="text-right"><p class="text-sm font-black">₨ ${r.amount}</p><p class="text-[8px] uppercase font-bold italic">${r.status}</p></div>
                    </div>`;
                });
            });
        }

        // --- GOD MODE ACTIONS ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("GOD KEY:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        async function syncAdm() {
            db.collection("users").onSnapshot(s => { document.getElementById('adm-count').innerText = s.size; });
            db.collection("settings").doc("system").onSnapshot(d => { mActive = d.data()?.maintenance; document.getElementById('m-status').innerText = mActive ? "ON" : "OFF"; });
            db.collection("plans").onSnapshot(s => {
                const l = document.getElementById('adm-plans-list'); l.innerHTML = '';
                s.forEach(doc => { l.innerHTML += `<div class="flex justify-between p-3 bg-white/5 rounded-xl text-[9px] uppercase font-black"><span>${doc.data().name}</span><button onclick="db.collection('plans').doc('${doc.id}').delete()" class="text-red-500">DEL</button></div>`; });
            });
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const q = document.getElementById('adm-requests'); q.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    q.innerHTML += `<div class="glass p-6 border-l-8 border-purple-500">
                        <div class="flex flex-col md:flex-row gap-5 items-center">
                            ${r.proof ? `<img src="${r.proof}" class="w-full md:w-32 h-24 object-cover rounded-2xl border border-white/5 shadow-xl">` : ''}
                            <div class="flex-1 text-[10px] font-black uppercase"><p>${r.user} | ${r.type}</p><p class="text-green-500">Amount: ₨ ${r.amount}</p><p class="opacity-40">TID: ${r.tid || 'N/A'}</p></div>
                            <div class="flex gap-2 w-full md:w-auto"><button onclick="approveReq('${doc.id}','${r.user}',${r.amount},'${r.type}')" class="flex-1 py-4 bg-blue-600 rounded-xl font-black text-[9px]">APPROVE</button><button onclick="db.collection('requests').doc('${doc.id}').update({status:'rejected'})" class="flex-1 py-4 bg-red-600 rounded-xl font-black text-[9px]">REJECT</button></div>
                        </div>
                    </div>`;
                });
            });
        }

        async function createPlan() {
            const n = document.getElementById('adm-p-name').value;
            const p = Number(document.getElementById('adm-p-price').value);
            const pf = Number(document.getElementById('adm-p-profit').value);
            await db.collection("plans").add({ name: n, price: p, dailyProfit: pf });
            alert("NODE DEPLOYED! 😘");
        }

        async function updateGates() {
            const j = document.getElementById('adm-jazz').value;
            const e = document.getElementById('adm-easy').value;
            await db.collection("settings").doc("gateways").set({ jazz: j, easy: e });
            alert("GATES UPDATED! 😘");
        }

        async function toggleMaintenance() { await db.collection("settings").doc("system").set({ maintenance: !mActive }); }

        async function approveReq(id, u, a, type) {
            if(type === 'Deposit' || type === 'Profit') {
                await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
                // Referral Check
                const usr = await db.collection("users").doc(u).get();
                if(usr.data().refBy && usr.data().refBy !== 'Direct' && type === 'Deposit') {
                    await db.collection("users").doc(usr.data().refBy).update({ balance: firebase.firestore.FieldValue.increment(a * 0.10) });
                }
            }
            await db.collection("requests").doc(id).update({ status: 'approved' });
            alert("AUTHORIZED! 😘");
        }

        // --- USER ACTIONS ---
        async function buyNode(nm, pr, pf) {
            if(user.balance < pr) return alert("Low balance sweetie! 😘");
            if(confirm("Initialize Mining?")) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-pr), activePlan: nm, dailyYield: pf, lastClaim: Date.now() });
                await db.collection("requests").add({ user: user.name, amount: pr, type: 'Activation: '+nm, status: 'approved', time: Date.now() });
                alert("NODE ONLINE! 😘");
            }
        }

        function updateProfitTimer() {
            const wait = 24 * 60 * 60 * 1000; const diff = Date.now() - user.lastClaim;
            const btn = document.getElementById('claim-btn');
            if(diff >= wait) { btn.disabled = false; btn.innerText = "COLLECT PROFIT"; btn.classList.remove('opacity-30'); btn.classList.add('bg-green-600'); }
            else { const h = Math.floor((wait-diff)/3600000); btn.innerText = h + "H REMAINING"; }
        }

        async function requestProfit() {
            await db.collection("requests").add({ user: user.name, amount: user.dailyYield, type: 'Profit', status: 'pending', time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastClaim: Date.now() });
            alert("REQUESTED! 😘");
        }

        async function submitDep() {
            const a = Number(document.getElementById('dep-amt').value); const t = document.getElementById('dep-tid').value; const f = document.getElementById('dep-file').files[0];
            if(!a || !t || !f) return alert("Proof required! 😘");
            const r = new FileReader(); r.readAsDataURL(f);
            r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: r.result, type: 'Deposit', status: 'pending', time: Date.now() });
                alert("LOGGED! 😘"); changePage('home');
            };
        }

        async function submitWith() {
            const a = Number(document.getElementById('w-amt').value); if(user.balance < a || a < 500) return alert("Invalid amount! 😘");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").add({ user: user.name, amount: a, tid: document.getElementById('w-acc').value + " ("+document.getElementById('w-name').value+")", type: 'Withdraw', status: 'pending', time: Date.now() });
            alert("LOGGED! 😘"); changePage('home');
        }

        // --- UTILS ---
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); window.scrollTo(0,0); }
        db.collection("settings").doc("system").onSnapshot(d => {
            const m = d.data()?.maintenance;
            if(m && localStorage.getItem('mc_session') !== 'admin') document.getElementById('lock-screen').classList.remove('hidden');
            else document.getElementById('lock-screen').classList.add('hidden');
        });

        window.onload = () => { const s = localStorage.getItem('mc_session'); if(s) enterApp(s); }
        setInterval(() => { if(user?.activePlan) updateProfitTimer(); }, 60000);
    </script>
</body>
</html>
