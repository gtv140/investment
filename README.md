<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Prime v9.0</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 110px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); backdrop-filter: blur(12px); }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 20px; font-weight: 800; transition: 0.3s; border: none; cursor: pointer; }
        .btn-prime:active { transform: scale(0.95); }
        input, textarea { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 16px; border-radius: 16px; width: 100%; outline: none; font-size: 13px; margin-bottom: 10px; }
        .status-badge { font-size: 8px; font-weight: 900; padding: 4px 10px; border-radius: 20px; text-transform: uppercase; }
        .pending { background: rgba(245, 158, 11, 0.1); color: #f59e0b; border: 1px solid rgba(245, 158, 11, 0.2); }
        .approved { background: rgba(16, 185, 129, 0.1); color: #10b981; border: 1px solid rgba(16, 185, 129, 0.2); }
    </style>
</head>
<body>

    <div id="bc-container" class="fixed top-20 inset-x-4 z-[9999] hidden animate-bounce">
        <div class="glass border-blue-500 p-4 rounded-2xl flex items-center gap-3 shadow-[0_0_20px_rgba(59,130,246,0.3)]">
            <i class="fa-solid fa-bullhorn text-blue-500 text-xl"></i>
            <p id="bc-text" class="text-[11px] font-black uppercase italic"></p>
        </div>
    </div>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)]">
        <div class="p-4 flex justify-between items-center">
            <div class="flex items-center gap-2">
                <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg cursor-pointer">M</div>
                <div><h1 class="text-[12px] font-black uppercase italic">MintCrest <span class="text-blue-500">Gold</span></h1><p id="display-user" class="text-[8px] font-bold text-blue-400 uppercase tracking-widest italic">@Connecting</p></div>
            </div>
            <div id="top-bal" class="text-[11px] font-black bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full border border-blue-500/20 italic">₨ 0</div>
        </div>
    </header>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8 text-center">
        <div class="w-20 h-20 bg-blue-600 rounded-[2.2rem] mb-6 flex items-center justify-center text-5xl font-black text-white italic shadow-2xl">M</div>
        <h1 class="text-3xl font-black italic mb-8 uppercase">Elite Mining</h1>
        <div class="w-full max-w-[300px] space-y-4">
            <input type="text" id="user-login-name" placeholder="USERNAME" class="text-center font-bold uppercase">
            <input type="text" id="ref-by" placeholder="INVITE CODE (OPTIONAL)" class="text-center font-bold uppercase text-[10px]">
            <button onclick="login()" class="w-full p-4 btn-prime uppercase text-[11px] tracking-widest">Authorize Session</button>
        </div>
    </section>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-8 rounded-[3.5rem] bg-gradient-to-br from-blue-600 to-indigo-900 text-white shadow-2xl relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-60">Mining Balance</p>
                <h2 class="text-5xl font-black tracking-tighter my-2" id="v-bal">₨ 0.00</h2>
                <div class="mt-6 flex gap-3">
                   <button onclick="claimDaily()" id="daily-btn" class="flex-1 bg-white/10 p-3 rounded-xl border border-white/10 text-[9px] font-black uppercase italic"><i class="fa-solid fa-gift mr-1"></i> Daily Bonus</button>
                   <button onclick="logout()" class="flex-1 bg-red-500/10 p-3 rounded-xl border border-red-500/20 text-[9px] font-black uppercase text-red-400 italic">Exit</button>
                </div>
            </div>

            <div class="glass p-5 rounded-[2.5rem] border-blue-500/10">
                <div class="flex gap-2 mb-3">
                    <input type="text" id="promo-input" placeholder="PROMO CODE" class="mb-0 uppercase font-black text-center text-[10px]">
                    <button onclick="applyPromo()" class="btn-prime px-6 text-[10px] uppercase">Apply</button>
                </div>
                <div class="p-4 bg-blue-600/5 rounded-2xl text-center border border-blue-500/10">
                    <p class="text-[7px] font-black opacity-50 uppercase mb-1">Your Referral Node ID</p>
                    <p id="my-ref-id" class="text-lg font-black text-blue-400 tracking-[0.2em] italic">-----</p>
                </div>
            </div>

            <h3 class="text-[11px] font-black uppercase text-blue-500 px-2 italic">Active Nodes</h3>
            <div id="plans-grid" class="grid grid-cols-1 gap-4 pb-20"></div>
        </div>

        <div id="p-history" class="page space-y-6">
            <h3 class="text-xl font-black text-center uppercase text-blue-500 italic">Node Logs</h3>
            <div id="user-logs" class="space-y-3 pb-20">
                </div>
        </div>

        <div id="p-wallet" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] space-y-4 border-blue-500/20">
                <h3 class="text-center font-black uppercase text-blue-500 italic"><i class="fa-solid fa-plus-circle mr-2"></i>Deposit</h3>
                <p class="text-[9px] text-center opacity-40">ADMIN NUMBER: 03379827882</p>
                <input type="number" id="dep-amt" placeholder="Amount (₨)">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                <button onclick="sendDeposit()" class="w-full p-4 btn-prime text-[11px] uppercase italic">Confirm Deposit</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-5 flex justify-around items-center rounded-t-[3.5rem] z-[4000]">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-microchip"></i><span class="text-[7px] font-black uppercase">Nodes</span></button>
        <button onclick="changePage('wallet')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-wallet"></i><span class="text-[7px] font-black uppercase">Wallet</span></button>
        <button onclick="changePage('history')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-scroll"></i><span class="text-[7px] font-black uppercase">Logs</span></button>
    </nav>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6 pb-28">
        <div class="flex justify-between items-center mb-8 border-b border-blue-500/20 pb-4">
            <h2 class="text-xl font-black text-blue-500 uppercase italic"><i class="fa-solid fa-user-shield mr-2"></i>God Mode</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-6 py-2 rounded-xl text-[10px] font-black uppercase">Exit</button>
        </div>

        <div class="glass p-6 rounded-3xl mb-6 space-y-4 border-blue-500/30">
            <h4 class="text-[10px] font-black uppercase text-blue-400 italic"><i class="fa-solid fa-tower-broadcast mr-2"></i>Global Broadcast</h4>
            <textarea id="adm-bc-text" placeholder="Type message for all users sweetie..." rows="2" class="mb-2"></textarea>
            <button onclick="sendBroadcast()" class="w-full p-3 btn-prime text-[9px] uppercase">Fire Message</button>
        </div>

        <div class="grid grid-cols-2 gap-4 mb-6">
            <div class="glass p-4 rounded-3xl text-center border-blue-500/10">
                <i class="fa-solid fa-users text-blue-500 text-xl mb-1"></i>
                <p class="text-[8px] font-black opacity-40 uppercase">Users</p>
                <h5 id="stat-users" class="font-black italic text-lg">0</h5>
            </div>
            <div class="glass p-4 rounded-3xl text-center border-green-500/10">
                <i class="fa-solid fa-check-double text-green-500 text-xl mb-1"></i>
                <p class="text-[8px] font-black opacity-40 uppercase">Success</p>
                <h5 id="stat-req" class="font-black italic text-lg">0</h5>
            </div>
        </div>

        <h4 class="text-[10px] font-black uppercase text-yellow-500 italic mb-4"><i class="fa-solid fa-hourglass-half mr-2"></i>Pending Nodes</h4>
        <div id="adm-req-list" class="space-y-4 mb-8"></div>

        <div class="glass p-6 rounded-3xl space-y-4 border-blue-500/20">
            <h4 class="text-[10px] font-black uppercase text-blue-400 italic">Node Architect</h4>
            <div class="flex gap-2">
                <input id="adm-ref-bonus" placeholder="Ref Bonus (₨)" class="mb-0 text-[10px]">
                <input id="adm-daily-amt" placeholder="Daily (₨)" class="mb-0 text-[10px]">
            </div>
            <button onclick="updateSystem()" class="w-full p-3 btn-prime text-[9px] uppercase">Update Config</button>
            <div class="flex gap-2 border-t border-blue-500/10 pt-4">
                <input id="adm-p-code" placeholder="PROMO" class="mb-0 text-[10px]">
                <input id="adm-p-val" placeholder="₨" class="mb-0 text-[10px]">
            </div>
            <button onclick="adminAddPromo()" class="w-full p-3 bg-white/5 border border-white/10 rounded-xl text-[9px] font-black uppercase">Create Promo</button>
        </div>
    </div>

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

        async function login() {
            const n = document.getElementById('user-login-name').value.trim().toLowerCase();
            const rb = document.getElementById('ref-by').value.trim().toLowerCase();
            if(!n) return alert("Enter Username sweetie!");
            localStorage.setItem('mc_user', n);
            enterApp(n, rb);
        }

        async function enterApp(n, rb) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('main-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-user').innerText = "@" + n.toUpperCase();
            document.getElementById('my-ref-id').innerText = n.toUpperCase();

            db.collection("users").doc(n).onSnapshot(doc => {
                if(!doc.exists) return db.collection("users").doc(n).set({ name: n, balance: 0, lastDaily: 0, referredBy: rb || null });
                user = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                checkDaily();
            });

            syncLogs(n);
            syncPlans();
            listenBroadcast();
        }

        // USER FUNCTIONS
        async function sendDeposit() {
            const a = document.getElementById('dep-amt').value; const t = document.getElementById('dep-tid').value;
            if(!a || !t) return alert("Fill details!");
            const id = Date.now().toString();
            await db.collection("requests").doc(id).set({ user: user.name, amount: parseInt(a), tid: t, status: 'pending', type: 'Deposit', time: Date.now() });
            await db.collection("logs").doc(id).set({ user: user.name, msg: `Deposit: ₨ ${a}`, status: 'pending', type: 'finance', time: Date.now() });
            alert("Sent for approval sweetie! 😘");
            document.getElementById('dep-amt').value = ''; document.getElementById('dep-tid').value = '';
        }

        function syncLogs(n) {
            db.collection("logs").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const l = document.getElementById('user-logs'); l.innerHTML = '';
                s.forEach(doc => {
                    const d = doc.data();
                    l.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center border-l-4 ${d.type==='profit'?'border-green-500':'border-blue-500'}">
                        <div><p class="text-[10px] font-black uppercase">${d.msg}</p><p class="text-[7px] opacity-40">${new Date(d.time).toLocaleTimeString()}</p></div>
                        ${d.status ? `<span class="status-badge ${d.status}">${d.status}</span>` : ''}
                    </div>`;
                });
            });
        }

        async function claimDaily() {
            const s = await db.collection("settings").doc("system").get();
            const amt = s.data().dailyBonus || 20;
            const today = new Date().toDateString();
            if(user.lastDailyDate === today) return alert("Already claimed today!");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(amt), lastDailyDate: today });
            await db.collection("logs").add({ user: user.name, msg: `Daily Bonus: ₨ ${amt}`, type: 'profit', time: Date.now() });
            alert(`₨ ${amt} added! 😘`);
        }

        function checkDaily() {
            const btn = document.getElementById('daily-btn');
            if(user.lastDailyDate === new Date().toDateString()) { btn.style.opacity = "0.5"; btn.innerText = "Claimed Today"; btn.disabled = true; }
        }

        async function applyPromo() {
            const code = document.getElementById('promo-input').value.toUpperCase();
            const d = await db.collection("promos").doc(code).get();
            if(!d.exists) return alert("Invalid Code!");
            if(user.usedPromos?.includes(code)) return alert("Used already!");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(d.data().reward), usedPromos: firebase.firestore.FieldValue.arrayUnion(code) });
            await db.collection("logs").add({ user: user.name, msg: `Promo: ${code} (+₨ ${d.data().reward})`, type: 'profit', time: Date.now() });
            alert("Bonus activated!");
            document.getElementById('promo-input').value = '';
        }

        // ADMIN FUNCTIONS
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("God Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        async function syncAdm() {
            db.collection("users").get().then(s => document.getElementById('stat-users').innerText = s.size);
            db.collection("requests").where("status","==","approved").get().then(s => document.getElementById('stat-req').innerText = s.size);
            
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-req-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-2xl border-l-4 border-yellow-500">
                        <div class="flex justify-between items-start mb-3">
                            <div><p class="text-[10px] font-black uppercase text-blue-400">${r.user}</p><h4 class="text-xl font-black italic">₨ ${r.amount}</h4></div>
                            <span class="text-[7px] opacity-50 uppercase">${new Date(r.time).toLocaleTimeString()}</span>
                        </div>
                        <p class="text-[8px] font-bold opacity-60 mb-4 tracking-tighter">TID: ${r.tid}</p>
                        <div class="flex gap-2"><button onclick="approveReq('${doc.id}', '${r.user}', ${r.amount})" class="flex-1 bg-blue-600 p-2 rounded-xl text-[9px] font-black uppercase">Approve</button>
                        <button onclick="db.collection('requests').doc('${doc.id}').delete()" class="p-2 text-red-500"><i class="fa-solid fa-trash"></i></button></div>
                    </div>`;
                });
            });
        }

        async function approveReq(id, u, a) {
            const uRef = db.collection("users").doc(u);
            const uData = (await uRef.get()).data();
            await uRef.update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").doc(id).update({ status: 'approved' });
            await db.collection("logs").doc(id).update({ status: 'approved' });
            
            // Referral Bonus Logic
            if(uData.referredBy && !uData.refPaid) {
                const sys = await db.collection("settings").doc("system").get();
                const bonus = sys.data().refBonus || 50;
                await db.collection("users").doc(uData.referredBy).update({ balance: firebase.firestore.FieldValue.increment(bonus) });
                await uRef.update({ refPaid: true });
                await db.collection("logs").add({ user: uData.referredBy, msg: `Ref Bonus (from ${u}): ₨ ${bonus}`, type: 'profit', time: Date.now() });
            }
            alert("Processed!");
        }

        async function sendBroadcast() {
            const msg = document.getElementById('adm-bc-text').value;
            if(!msg) return;
            await db.collection("settings").doc("broadcast").set({ text: msg, time: Date.now() });
            alert("Broadcast Fired!");
        }

        function listenBroadcast() {
            db.collection("settings").doc("broadcast").onSnapshot(d => {
                if(d.exists && (Date.now() - d.data().time < 600000)) {
                    document.getElementById('bc-container').classList.remove('hidden');
                    document.getElementById('bc-text').innerText = d.data().text;
                } else { document.getElementById('bc-container').classList.add('hidden'); }
            });
        }

        async function updateSystem() {
            const r = parseInt(document.getElementById('adm-ref-bonus').value);
            const d = parseInt(document.getElementById('adm-daily-amt').value);
            await   db.collection("settings").doc("system").set({ refBonus: r || 50, dailyBonus: d || 20 }, { merge: true });
            alert("Settings Updated!");
        }

        async function adminAddPromo() {
            const c = document.getElementById('adm-p-code').value.toUpperCase();
            const v = parseInt(document.getElementById('adm-p-val').value);
            if(c && v) await db.collection("promos").doc(c).set({ reward: v });
            alert("Promo Active!");
        }

        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    g.innerHTML += `<div class="glass p-6 rounded-[2.5rem] border-l-4 border-blue-500">
                        <div class="flex justify-between items-center mb-4"><div><p class="text-[9px] font-black uppercase opacity-40">${p.name}</p><h4 class="text-xl font-black">₨ ${p.price}</h4></div><span class="bg-blue-600/10 text-blue-500 px-3 py-1 rounded-full text-[8px] font-black">${p.profit}% Daily</span></div>
                        <button onclick="buyNode('${doc.id}', ${p.price})" class="w-full btn-prime p-4 text-[10px] uppercase font-black italic">Activate Node</button>
                    </div>`;
                });
            });
        }

        async function buyNode(id, pr) {
            if(user.balance < pr) return alert("Insufficient Balance!");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-pr) });
            await db.collection("logs").add({ user: user.name, msg: `Activated Node: ₨ ${pr}`, type: 'finance', time: Date.now() });
            alert("Mining Initialized!");
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function logout() { localStorage.clear(); location.reload(); }
        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user'), ""); }
    </script>
</body>
</html>
