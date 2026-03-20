<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | v26.0 Pro</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 100px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); backdrop-filter: blur(15px); }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #2563eb, #3b82f6); color: white; border-radius: 18px; font-weight: 800; border: none; cursor: pointer; transition: 0.3s; }
        input { background: #161b22; border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 14px; width: 100%; outline: none; font-size: 13px; margin-bottom: 10px; }
        .full-overlay { position: fixed; inset: 0; background: #000; z-index: 999999; display: none; flex-direction: column; items: center; justify-content: center; text-align: center; padding: 20px; }
    </style>
</head>
<body>

    <div id="maint-screen" class="full-overlay bg-slate-950">
        <i class="fa-solid fa-clock-rotate-left text-blue-500 text-6xl mb-4 animate-spin-slow"></i>
        <h1 class="text-2xl font-black uppercase text-white">Maintenance</h1>
        <p class="opacity-50 text-[10px] mt-2 italic text-blue-300">Wait for us, sweetie! 😘</p>
    </div>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/90 backdrop-blur-xl border-b border-[var(--border)] p-4 flex justify-between items-center">
        <div class="flex items-center gap-3">
            <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg">M</div>
            <div>
                <h1 class="text-[12px] font-black uppercase">MintCrest <span class="text-blue-500">Gold</span></h1>
                <p id="display-user" class="text-[8px] font-bold text-blue-400 italic">@USER</p>
            </div>
        </div>
        <div id="top-bal" class="text-[10px] font-black bg-blue-600/10 text-blue-500 px-4 py-2 rounded-full border border-blue-500/20">₨ 0</div>
    </header>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div id="global-notice-bar" class="hidden glass p-4 rounded-[2rem] text-[10px] font-black uppercase text-blue-400 border-blue-500/20 text-center"><i class="fa-solid fa-star mr-2"></i> <span id="ntc-txt"></span></div>
            
            <div class="p-10 rounded-[3.5rem] bg-gradient-to-br from-blue-700 to-indigo-950 text-white shadow-2xl relative overflow-hidden text-center">
                <p class="text-[10px] font-black uppercase opacity-60">Master Balance</p>
                <h2 class="text-5xl font-black tracking-tighter my-2" id="v-bal">₨ 0.00</h2>
                <div class="mt-8 flex gap-3">
                   <button onclick="changePage('wallet')" class="flex-1 bg-white/10 p-4 rounded-2xl text-[10px] font-black uppercase">Add Cash</button>
                   <button onclick="changePage('withdraw')" class="flex-1 bg-blue-400/20 p-4 rounded-2xl text-[10px] font-black uppercase">Withdraw</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <a href="https://chat.whatsapp.com/YOUR_LINK_HERE" class="glass p-5 rounded-[2.5rem] flex flex-col items-center gap-2 text-green-500">
                    <i class="fa-brands fa-whatsapp text-2xl"></i>
                    <span class="text-[9px] font-black uppercase">Join Group</span>
                </a>
                <button onclick="changePage('about')" class="glass p-5 rounded-[2.5rem] flex flex-col items-center gap-2 text-blue-400">
                    <i class="fa-solid fa-building-shield text-2xl"></i>
                    <span class="text-[9px] font-black uppercase">Company Info</span>
                </button>
            </div>
        </div>

        <div id="p-nodes" class="page space-y-4 pb-24">
            <div class="text-center mb-6">
                <h3 class="text-xl font-black text-blue-500 uppercase italic">Investment Nodes</h3>
                <p class="text-[9px] opacity-40 uppercase font-bold tracking-widest">Select your profit engine</p>
            </div>
            <div id="plans-grid" class="grid grid-cols-1 gap-4"></div>
        </div>

        <div id="p-history" class="page space-y-4 pb-24 text-center">
            <h3 class="font-black uppercase text-blue-500">Transaction History</h3>
            <div id="user-logs-list" class="space-y-3"></div>
        </div>

        <div id="p-about" class="page space-y-6 pb-24">
            <div class="glass p-8 rounded-[3.5rem] space-y-4 text-[12px] leading-relaxed">
                <h3 class="text-xl font-black text-blue-500 uppercase italic mb-4">MintCrest Details</h3>
                <p><b class="text-blue-400">Company:</b> Prime Solutions Digital Assets Management.</p>
                <p><b class="text-blue-400">Contact Email:</b> webhub262@gmail.com</p>
                <hr class="border-white/5">
                <p><b class="text-blue-400">Privacy Policy:</b> We encrypt all user data using Firebase security protocols. Your financial details are kept confidential and withdrawals are processed within 24 hours.</p>
                <p><b class="text-blue-400">Risk Warning:</b> Crypto and node investments carry risks. Only invest what you can afford to manage. 😘</p>
            </div>
        </div>

        <div id="p-wallet" class="page"><div class="glass p-8 rounded-[3.5rem] space-y-6 text-center">
            <h3 class="font-black uppercase text-blue-500">Deposit</h3>
            <div id="qr-area" class="hidden p-4 bg-white rounded-3xl mx-auto w-fit mb-4"><img id="qr-img" class="w-44 h-44"></div>
            <div class="flex gap-2"><button onclick="showQR('Jazz')" class="flex-1 glass p-4 rounded-2xl text-[10px] font-black uppercase">JazzCash</button><button onclick="showQR('Easy')" class="flex-1 glass p-4 rounded-2xl text-[10px] font-black uppercase">EasyPaisa</button></div>
            <div class="p-3 bg-blue-600/5 rounded-xl border border-blue-500/10"><h4 id="pay-num" class="text-lg font-black text-blue-400">---</h4></div>
            <input id="d-amt" type="number" placeholder="Amount (Min 200)"><input id="d-tid" placeholder="Transaction ID"><button onclick="sendDeposit()" class="w-full p-4 btn-prime text-[11px] font-black uppercase italic">Submit Deposit</button>
        </div></div>

        <div id="p-withdraw" class="page"><div class="glass p-8 rounded-[3.5rem] space-y-6 text-center">
            <h3 class="font-black uppercase text-blue-500">Withdraw</h3>
            <input id="w-amt" type="number" placeholder="Min 100"><input id="w-acc" placeholder="Account Number"><button onclick="sendWithdraw()" class="w-full p-4 btn-prime text-[11px] font-black uppercase italic">Request Funds</button>
        </div></div>

    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6 pb-32">
        <div class="flex justify-between items-center mb-6">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Admin Console</h2>
            <button onclick="closeAdmin()" class="bg-red-600 text-white px-4 py-2 rounded-xl text-[10px] font-black">CLOSE</button>
        </div>
        <div class="admin-section">
            <h4 class="text-[10px] font-black uppercase text-red-500 mb-4">Maintenance</h4>
            <div class="flex gap-2"><button onclick="toggleMaint(true)" class="flex-1 bg-red-600/20 p-3 rounded-xl text-[9px] font-black uppercase">Turn ON</button><button onclick="toggleMaint(false)" class="flex-1 bg-green-600/20 p-3 rounded-xl text-[9px] font-black uppercase">Turn OFF</button></div>
        </div>
        <div class="admin-section">
            <h4 class="text-[10px] font-black uppercase text-purple-400 mb-4">Manage Plans</h4>
            <div id="adm-plan-manager" class="space-y-2 mb-4"></div>
            <input id="pl-name" placeholder="Name"><div class="flex gap-2"><input id="pl-price" type="number" placeholder="Price"><input id="pl-profit" type="number" placeholder="Profit %"></div>
            <button onclick="createPlan()" class="w-full p-3 bg-purple-600 rounded-xl text-[9px] font-black uppercase">Add New Plan</button>
        </div>
        <div class="admin-section">
            <h4 class="text-[10px] font-black uppercase text-blue-400 mb-4">Requests</h4>
            <div id="adm-req-list" class="space-y-4"></div>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8 text-center">
        <div class="w-20 h-20 bg-blue-600 rounded-[2rem] mb-8 flex items-center justify-center text-4xl font-black italic text-white shadow-2xl">M</div>
        <h2 class="text-xl font-black uppercase tracking-widest mb-10">MintCrest <span class="text-blue-500 italic">Gold</span></h2>
        <input id="l-name" placeholder="YOUR NAME" class="max-w-[320px] text-center font-black uppercase italic border-blue-500/20">
        <button onclick="login()" class="w-full max-w-[320px] p-5 btn-prime uppercase text-[12px] font-black shadow-2xl">Access Dashboard</button>
    </section>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-6 flex justify-around items-center rounded-t-[3.5rem] z-[4000]">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-house"></i><span class="text-[7px] font-black uppercase">Home</span></button>
        <button onclick="changePage('nodes')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-cube text-blue-500"></i><span class="text-[7px] font-black uppercase">Nodes</span></button>
        <button onclick="changePage('history')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-receipt"></i><span class="text-[7px] font-black uppercase">History</span></button>
        <button onclick="logout()" class="flex flex-col items-center gap-1 text-red-500"><i class="fa-solid fa-power-off"></i><span class="text-[7px] font-black uppercase">Exit</span></button>
    </nav>

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
            if(n.length < 3) return alert("Enter your name, sweetie! 😘");
            localStorage.setItem('mc_session', n);
            enterApp(n);
        }

        async function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('main-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-user').innerText = "@" + n.toUpperCase();

            db.collection("settings").doc("global").onSnapshot(d => {
                settings = d.data() || {};
                const mScreen = document.getElementById('maint-screen');
                if(settings.maintenance === true) mScreen.style.display = 'flex'; else mScreen.style.display = 'none';
                if(settings.notice) {
                    document.getElementById('global-notice-bar').classList.remove('hidden');
                    document.getElementById('ntc-txt').innerText = settings.notice;
                }
            });

            db.collection("users").doc(n).onSnapshot(doc => {
                if(!doc.exists) return db.collection("users").doc(n).set({ name: n, balance: 0, status: 'active', joined: Date.now() });
                user = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            });

            syncPlans(); syncLogs();
        }

        // --- ADMIN ---
        async function toggleMaint(v) { await db.collection("settings").doc("global").update({ maintenance: v }); }
        async function createPlan() {
            const n = document.getElementById('pl-name').value; const p = parseInt(document.getElementById('pl-price').value); const pr = parseInt(document.getElementById('pl-profit').value);
            await db.collection("plans").add({ name: n, price: p, profit: pr }); alert("Node Created! 😘");
        }
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Pass:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        async function syncAdm() {
            db.collection("plans").onSnapshot(s => {
                const l = document.getElementById('adm-plan-manager'); l.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    l.innerHTML += `<div class="flex justify-between items-center p-2 border-b border-white/5 uppercase text-[9px] font-bold">
                        <span>${p.name} (₨ ${p.price})</span>
                        <button onclick="db.collection('plans').doc('${doc.id}').delete()" class="text-red-500"><i class="fa-solid fa-trash"></i></button>
                    </div>`;
                });
            });
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-req-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-3xl mb-2">
                        <p class="text-[9px] font-black uppercase text-blue-400">${r.user} | ${r.type}</p>
                        <h4 class="text-xl font-black">₨ ${r.amount}</h4>
                        <button onclick="approveReq('${doc.id}', '${r.user}', ${r.amount}, '${r.type}')" class="w-full bg-blue-600 p-2 rounded-xl text-[9px] font-black uppercase mt-3">Verify</button>
                    </div>`;
                });
            });
        }
        async function approveReq(id, u, a, t) {
            if(t==='Deposit') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").doc(id).update({ status: 'approved' });
            await db.collection("logs").doc(id).update({ status: 'approved' });
            alert("Done! 😘");
        }

        // --- USER ---
        async function buyPlan(id, price, name) {
            if(user.balance < price) return alert("Insufficient Balance! Deposit first. 😘");
            if(confirm(`Buy ${name}?`)) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-price) });
                await db.collection("logs").add({ user: user.name, msg: `Active: ${name}`, status: 'approved', time: Date.now() });
                alert("Successful! 😘");
            }
        }
        async function sendDeposit() {
            const a = parseInt(document.getElementById('d-amt').value); const t = document.getElementById('d-tid').value;
            if(!a || a < 200 || !t) return alert("Fill all details! 😘");
            const id = Date.now().toString();
            await db.collection("requests").doc(id).set({ user: user.name, amount: a, tid: t, status: 'pending', type: 'Deposit', time: Date.now() });
            await db.collection("logs").doc(id).set({ user: user.name, msg: `Deposit: ₨ ${a}`, status: 'pending', time: Date.now() });
            alert("Sent for verification! 😘");
        }
        function showQR(t) { document.getElementById('qr-area').classList.remove('hidden'); document.getElementById('qr-img').src = (t==='Jazz'?settings.jazzQR:settings.easyQR); document.getElementById('pay-num').innerText = (t==='Jazz'?settings.jazzNum:settings.easyNum); }
        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => { const p = doc.data(); g.innerHTML += `<div class="glass p-6 rounded-[2.8rem] flex justify-between items-center border-l-4 border-blue-500 shadow-xl"><div><p class="text-[9px] font-black opacity-40 uppercase">${p.name}</p><h4 class="text-2xl font-black italic">₨ ${p.price}</h4><p class="text-[8px] font-bold text-blue-400">${p.profit}% Daily Profit</p></div><button onclick="buyPlan('${doc.id}', ${p.price}, '${p.name}')" class="btn-prime px-7 py-4 text-[11px] font-black uppercase tracking-widest">Buy</button></div>`; });
            });
        }
        function syncLogs() {
            db.collection("logs").where("user", "==", user.name).orderBy("time", "desc").limit(10).onSnapshot(s => {
                const l = document.getElementById('user-logs-list'); l.innerHTML = '';
                s.forEach(doc => { const d = doc.data(); l.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center border-l-2 ${d.status==='approved'?'border-green-500':'border-yellow-500'}"><p class="text-[10px] font-black uppercase">${d.msg}</p><span class="text-[8px] font-black uppercase ${d.status==='approved'?'text-green-500':'text-yellow-500'}">${d.status}</span></div>`; });
            });
        }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); window.scrollTo(0,0); }
        function logout() { localStorage.removeItem('mc_session'); location.reload(); }
        window.onload = () => { const s = localStorage.getItem('mc_session'); if(s) enterApp(s); }
    </script>
</body>
</html>
