<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Secure v18.0</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 100px; }
        .glass { background: var(--card); border: 1px solid var(--border); backdrop-filter: blur(15px); }
        .page { display: none; animation: fadeIn 0.3s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        .btn-prime { background: linear-gradient(135deg, #2563eb, #3b82f6); color: white; border-radius: 16px; font-weight: 800; border: none; cursor: pointer; }
        input { background: #161b22; border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 12px; width: 100%; outline: none; font-size: 13px; margin-bottom: 10px; }
    </style>
</head>
<body>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)] p-4 flex justify-between items-center">
        <div class="flex items-center gap-2">
            <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg cursor-pointer">M</div>
            <div><h1 class="text-[12px] font-black uppercase">MintCrest <span class="text-blue-500">Gold</span></h1><p id="display-user" class="text-[8px] font-bold text-blue-400 uppercase italic">@Verified</p></div>
        </div>
        <div class="flex items-center gap-3">
            <div id="top-bal" class="text-[10px] font-black bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full border border-blue-500/20 italic">₨ 0</div>
            <button onclick="logout()" class="w-8 h-8 rounded-full bg-red-600/10 text-red-500 border border-red-500/20 flex items-center justify-center"><i class="fa-solid fa-power-off text-xs"></i></button>
        </div>
    </header>

    <main id="app-ui" class="hidden p-4 space-y-6">
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-8 rounded-[3rem] bg-gradient-to-br from-blue-700 to-indigo-950 text-white shadow-2xl relative">
                <p class="text-[10px] font-black uppercase opacity-60 italic">Current Wealth</p>
                <h2 class="text-5xl font-black tracking-tighter my-2" id="v-bal">₨ 0.00</h2>
                <div class="mt-6 flex gap-3">
                   <button onclick="changePage('wallet')" class="flex-1 bg-white/10 p-3 rounded-xl text-[9px] font-black uppercase italic">Deposit</button>
                   <button onclick="changePage('withdraw')" class="flex-1 bg-blue-400/20 p-3 rounded-xl text-[9px] font-black uppercase italic">Withdraw</button>
                </div>
            </div>
            <div id="plans-grid" class="grid grid-cols-1 gap-4 pb-20"></div>
        </div>

        <div id="p-history" class="page space-y-4 pb-20">
            <h3 class="text-center font-black uppercase text-blue-500 italic"><i class="fa-solid fa-receipt mr-2"></i>History Logs</h3>
            <div id="user-logs-list" class="space-y-3"></div>
        </div>

        <div id="p-wallet" class="page"><div class="glass p-8 rounded-[3rem] space-y-4 text-center">
            <h3 class="font-black uppercase text-blue-500 italic">Add Funds (Min 200)</h3>
            <div id="qr-area" class="hidden p-4 bg-white rounded-2xl mx-auto w-fit mb-4"><img id="qr-img" class="w-40 h-40"></div>
            <div class="flex gap-2 mb-4"><button onclick="showQR('Jazz')" class="flex-1 glass p-3 rounded-xl text-[10px] font-black">JAZZCASH</button><button onclick="showQR('Easy')" class="flex-1 glass p-3 rounded-xl text-[10px] font-black">EASYPAISA</button></div>
            <input id="d-amt" type="number" placeholder="Amount (Min 200)"><input id="d-tid" placeholder="TID Number"><button onclick="sendDeposit()" class="w-full p-4 btn-prime text-[11px] font-black uppercase italic">Deposit Now</button>
        </div></div>

        <div id="p-withdraw" class="page"><div class="glass p-8 rounded-[3rem] space-y-4 text-center">
            <h3 class="font-black uppercase text-blue-500 italic">Payout (Min 100)</h3>
            <input id="w-amt" type="number" placeholder="Amount"><input id="w-acc" placeholder="Account No"><select id="w-method"><option value="JazzCash">JazzCash</option><option value="EasyPaisa">EasyPaisa</option></select><button onclick="sendWithdraw()" class="w-full p-4 btn-prime text-[11px] font-black uppercase italic">Withdraw Now</button>
        </div></div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6 pb-32">
        <div class="flex justify-between items-center mb-8"><h2 class="text-xl font-black text-blue-500 uppercase italic">Admin Console</h2><button onclick="closeAdmin()" class="text-red-500 font-black">EXIT</button></div>
        <div class="glass p-4 rounded-2xl mb-6"><h4 class="text-[10px] font-black text-blue-400 mb-4 uppercase">Users Data</h4><div id="adm-user-list" class="space-y-2"></div></div>
        <div id="adm-req-list" class="space-y-4"></div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8">
        <div class="w-20 h-20 bg-blue-600 rounded-[2rem] mb-10 flex items-center justify-center text-5xl font-black italic text-white shadow-2xl">M</div>
        <div class="w-full max-w-[320px] space-y-4">
            <h2 class="text-center font-black uppercase text-blue-500 italic tracking-widest text-lg">MintCrest Access</h2>
            <input id="l-name" placeholder="YOUR USERNAME" class="text-center font-black uppercase italic">
            <button onclick="login()" class="w-full p-4 btn-prime uppercase text-[12px] font-black tracking-widest shadow-xl shadow-blue-600/20">Secure Login</button>
            <p class="text-[8px] text-center opacity-40 uppercase font-black">Prime Solutions © 2026 - Secured Node</p>
        </div>
    </section>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-5 flex justify-around items-center rounded-t-[3.5rem] z-[4000]">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-layer-group"></i><span class="text-[7px] font-black uppercase">Nodes</span></button>
        <button onclick="changePage('wallet')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-plus-circle"></i><span class="text-[7px] font-black uppercase">Add</span></button>
        <button onclick="changePage('history')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-clock-rotate-left"></i><span class="text-[7px] font-black uppercase">Logs</span></button>
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
        let user = null; let tapCount = 0; let qrLinks = { jazz: '', easy: '' };

        // LOGIN SYSTEM
        function login() {
            const n = document.getElementById('l-name').value.trim().toLowerCase();
            if(n && n.length > 2) {
                localStorage.setItem('mc_active_user', n);
                enterApp(n);
            } else { alert("Please enter a valid username sweetie! 😘"); }
        }

        // LOGOUT SYSTEM
        function logout() {
            if(confirm("Do you want to logout sweetie? 😘")) {
                localStorage.removeItem('mc_active_user');
                location.reload(); // Pura app refresh ho kar login screen par aa jayega
            }
        }

        async function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('main-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-user').innerText = "@" + n.toUpperCase();

            db.collection("users").doc(n).onSnapshot(doc => {
                if(!doc.exists) return db.collection("users").doc(n).set({ name: n, balance: 0, joined: Date.now() });
                user = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            });

            db.collection("settings").doc("qrs").onSnapshot(d => { if(d.exists) qrLinks = d.data(); });
            syncPlans();
            syncLogs();
        }

        // AUTO-LOGIN CHECK
        window.onload = () => {
            const savedUser = localStorage.getItem('mc_active_user');
            if(savedUser) { enterApp(savedUser); }
        }

        // PLAN & DEPOSIT LOGIC
        async function buyPlan(id, price, name) {
            if(user.balance < price) { alert("Insufficient Balance! 😘"); return changePage('wallet'); }
            if(confirm(`Activate ${name}?`)) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-price) });
                await db.collection("logs").add({ user: user.name, msg: `Node Active: ${name}`, status: 'approved', time: Date.now() });
                alert("Activated! 🚀");
            }
        }

        async function sendDeposit() {
            const a = parseInt(document.getElementById('d-amt').value); const t = document.getElementById('d-tid').value;
            if(!a || a < 200 || !t) return alert("Fill details (Min 200)!");
            const id = Date.now().toString();
            await db.collection("requests").doc(id).set({ user: user.name, amount: a, tid: t, status: 'pending', type: 'Deposit', time: Date.now() });
            await db.collection("logs").doc(id).set({ user: user.name, msg: `Deposit: ₨ ${a}`, status: 'pending', time: Date.now() });
            alert("Sent for approval!");
        }

        async function sendWithdraw() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value;
            if(!a || a < 100 || user.balance < a) return alert("Invalid amount/balance!");
            const id = Date.now().toString();
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").doc(id).set({ user: user.name, amount: a, acc: acc, status: 'pending', type: 'Withdraw', time: Date.now() });
            await db.collection("logs").doc(id).set({ user: user.name, msg: `Withdraw: ₨ ${a}`, status: 'pending', time: Date.now() });
            alert("Withdrawal requested!");
        }

        // ADMIN & UI
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Goddess Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function showQR(t) { document.getElementById('qr-area').classList.remove('hidden'); document.getElementById('qr-img').src = (t==='Jazz'?qrLinks.jazz:qrLinks.easy); }

        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    g.innerHTML += `<div class="glass p-6 rounded-[2.5rem] flex justify-between items-center border-l-4 border-blue-500">
                        <div><p class="text-[9px] font-black opacity-40 uppercase">${p.name}</p><h4 class="text-xl font-black italic">₨ ${p.price}</h4><p class="text-[8px] font-bold text-blue-400">${p.profit}% Profit</p></div>
                        <button onclick="buyPlan('${doc.id}', ${p.price}, '${p.name}')" class="btn-prime px-6 py-3 text-[10px] font-black uppercase italic">Buy</button>
                    </div>`;
                });
            });
        }

        async function syncAdm() {
            db.collection("users").onSnapshot(s => {
                const l = document.getElementById('adm-user-list'); l.innerHTML = '';
                s.forEach(doc => { l.innerHTML += `<div class="flex justify-between p-2 border-b border-white/5 uppercase font-bold text-[9px] text-white"><span>${doc.data().name}</span><span class="text-blue-500">₨ ${doc.data().balance}</span></div>`; });
            });
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-req-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-2xl border-l-4 border-yellow-500 mb-2">
                        <p class="text-[9px] font-black uppercase text-blue-400">${r.user} | ${r.type}</p>
                        <h4 class="text-xl font-black">₨ ${r.amount}</h4>
                        <div class="flex gap-2 mt-3"><button onclick="approveReq('${doc.id}', '${r.user}', ${r.amount}, '${r.type}')" class="flex-1 bg-blue-600 p-2 rounded-xl text-[9px] font-black uppercase">Approve</button></div>
                    </div>`;
                });
            });
        }

        async function approveReq(id, u, a, type) {
            if(type === 'Deposit') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").doc(id).update({ status: 'approved' });
            await db.collection("logs").doc(id).update({ status: 'approved' });
            alert("Success!");
        }

        function syncLogs() {
            db.collection("logs").where("user", "==", user.name).orderBy("time", "desc").limit(15).onSnapshot(s => {
                const l = document.getElementById('user-logs-list'); l.innerHTML = '';
                s.forEach(doc => {
                    const d = doc.data();
                    l.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center border-l-2 border-blue-600"><p class="text-[10px] font-black uppercase">${d.msg}</p><span class="text-[8px] font-black uppercase ${d.status==='pending'?'text-yellow-500':'text-green-500'}">${d.status}</span></div>`;
                });
            });
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
    </script>
</body>
</html>
