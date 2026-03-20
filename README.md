<html lang="en" id="main-html" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Official Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 110px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 16px; font-weight: 800; }
        input, select { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 14px; width: 100%; outline: none; margin-bottom: 10px; }
        .animate-marquee { display: inline-block; animation: marquee 20s linear infinite; white-space: nowrap; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
    </style>
</head>
<body>

    <div id="lock-screen" class="fixed inset-0 z-[1000000] bg-[var(--bg)] hidden flex flex-col items-center justify-center p-8 text-center">
        <div class="w-20 h-20 bg-red-600/20 rounded-full flex items-center justify-center mb-6 animate-pulse text-4xl">⚠️</div>
        <h1 id="lock-title" class="text-2xl font-black uppercase text-red-500 mb-2">Access Denied</h1>
        <p id="lock-msg" class="text-[10px] font-bold opacity-60 uppercase tracking-widest">System Restructuring.</p>
    </div>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)]">
        <div class="p-4 flex justify-between items-center">
            <div class="flex items-center gap-2">
                <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg cursor-pointer">M</div>
                <div>
                    <h1 class="text-[12px] font-black uppercase">MintCrest <span class="text-blue-500">Gold</span></h1>
                    <p class="text-[7px] font-bold opacity-40 uppercase">V4.0 Trusted Node</p>
                </div>
            </div>
            <div id="top-bal" class="text-[11px] font-black bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full border border-blue-500/20 italic">₨ 0</div>
        </div>
    </header>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8 text-center">
        <div class="w-16 h-16 bg-blue-600 rounded-3xl mb-6 flex items-center justify-center text-4xl font-black text-white italic shadow-2xl">M</div>
        <h1 class="text-2xl font-black italic tracking-tighter mb-8 uppercase">Initialize Node</h1>
        <div class="w-full max-w-[300px] space-y-4">
            <input type="text" id="user-login-name" placeholder="Enter Username" class="text-center font-bold uppercase">
            <button onclick="login()" class="w-full p-4 btn-prime uppercase text-[10px] tracking-widest shadow-xl">Connect Securely</button>
        </div>
    </section>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-8 rounded-[3rem] bg-gradient-to-br from-blue-700 to-blue-900 text-white shadow-2xl relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-60">Node Balance</p>
                <h2 class="text-5xl font-black tracking-tighter my-1" id="v-bal">₨ 0.00</h2>
                <div class="flex items-center gap-2 mt-4 bg-black/20 p-3 rounded-2xl border border-white/5">
                    <span id="v-timer" class="text-xs font-black tracking-tighter uppercase italic">Mining Status: Active</span>
                </div>
            </div>

            <div class="flex gap-2">
                <a href="https://chat.whatsapp.com/GTV140" target="_blank" class="flex-1 glass p-4 rounded-2xl flex items-center justify-center gap-2 text-[10px] font-black uppercase">
                    <span>🟢 WhatsApp Group</span>
                </a>
                <button onclick="logout()" class="glass p-4 rounded-2xl text-[10px] font-black uppercase">🚪 Logout</button>
            </div>

            <h3 class="text-[11px] font-black uppercase italic text-blue-500 px-2">Mining Nodes</h3>
            <div id="plans-grid" class="grid grid-cols-1 gap-4 pb-20"></div>
        </div>

        <div id="p-deposit" class="page space-y-6">
            <div class="glass p-6 rounded-[3rem] space-y-4">
                <h3 class="text-[11px] font-black uppercase text-center opacity-40">Payment Methods</h3>
                <div class="space-y-3">
                    <div class="p-4 bg-blue-600/10 border border-blue-500/20 rounded-2xl">
                        <p class="text-[9px] font-black text-blue-500 uppercase">JazzCash / SadaPay</p>
                        <p class="text-lg font-black tracking-tighter">03705519562</p>
                    </div>
                    <div class="p-4 bg-green-600/10 border border-green-500/20 rounded-2xl">
                        <p class="text-[9px] font-black text-green-500 uppercase">EasyPaisa</p>
                        <p class="text-lg font-black tracking-tighter">03379827882</p>
                    </div>
                </div>
                <input type="number" id="dep-amt" placeholder="Amount (₨)">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                <button onclick="sendDeposit()" class="w-full p-4 btn-prime text-[11px] uppercase tracking-widest">Verify Payment ⚡</button>
            </div>
        </div>

        <div id="p-withdraw" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] space-y-5">
                <h3 class="text-xl font-black text-blue-500 italic text-center uppercase">Extraction</h3>
                <input type="number" id="wit-amt" placeholder="Min: ₨ 500">
                <select id="wit-method">
                    <option value="JazzCash">JazzCash</option>
                    <option value="EasyPaisa">EasyPaisa</option>
                    <option value="SadaPay">SadaPay</option>
                </select>
                <input type="text" id="wit-acc" placeholder="Account Number">
                <button onclick="sendWithdraw()" class="w-full p-4 btn-prime text-[11px] uppercase tracking-widest">Request Funds</button>
            </div>
        </div>

        <div id="p-support" class="page space-y-6">
            <div class="glass p-8 rounded-[3rem] text-[10px] font-bold leading-loose">
                <h4 class="text-blue-500 font-black uppercase italic mb-2">MintCrest Gold Trust</h4>
                <p>Official UK Node ID: #MCG-2026-UK. <br>
                For Support: webhub262@gmail.com <br>
                All funds are protected by 256-bit AES Encryption. <br>
                Withdrawal Time: 2 to 24 Hours.</p>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-5 flex justify-around items-center rounded-t-[3rem] z-[4000] shadow-2xl">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1">🏠<span class="text-[7px] font-black uppercase">Home</span></button>
        <button onclick="changePage('deposit')" class="flex flex-col items-center gap-1">➕<span class="text-[7px] font-black uppercase">Fund</span></button>
        <button onclick="changePage('withdraw')" class="flex flex-col items-center gap-1">💸<span class="text-[7px] font-black uppercase">Withdraw</span></button>
        <button onclick="changePage('support')" class="flex flex-col items-center gap-1">🛡️<span class="text-[7px] font-black uppercase">About</span></button>
    </nav>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-500 uppercase">Master Controller</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>
        
        <div class="glass p-6 rounded-3xl space-y-4 mb-6">
            <h4 class="text-[10px] font-black uppercase text-blue-400">Manage Content</h4>
            <input type="text" id="new-p-name" placeholder="Plan Name">
            <input type="number" id="new-p-price" placeholder="Price">
            <input type="number" id="new-p-profit" placeholder="Daily Profit (%)">
            <button onclick="adminAddPlan()" class="w-full btn-prime p-4 text-[11px] uppercase">Add Plan 🚀</button>
        </div>

        <h4 class="text-[10px] font-black uppercase text-blue-400 mb-4">Pending Requests</h4>
        <div id="adm-req-list" class="space-y-3 pb-10"></div>
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

        // AUTH: Username only jaisa aapki file mein tha
        async function login() {
            const n = document.getElementById('user-login-name').value.trim().toLowerCase();
            if(!n) return alert("Enter Username!");
            
            const ref = db.collection("users").doc(n);
            const d = await ref.get();
            if(!d.exists) {
                await ref.set({ name: n, balance: 0, banned: false, time: Date.now() });
            } else if(d.data().banned) {
                return showLock("BANNED", "Your account is restricted.");
            }
            
            localStorage.setItem('mc_user', n);
            enterApp(n);
        }

        function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('main-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                if(user.banned) location.reload();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            });
            syncPlans();
        }

        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const grid = document.getElementById('plans-grid'); grid.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    grid.innerHTML += `<div class="glass p-6 rounded-3xl border-l-4 border-blue-500">
                        <div class="flex justify-between items-center">
                            <div><p class="text-[10px] font-black uppercase text-blue-500">${p.name}</p><h3 class="text-xl font-black">₨ ${p.price.toLocaleString()}</h3></div>
                            <span class="bg-blue-600/10 text-blue-500 px-3 py-1 rounded-full text-[8px] font-black">${p.profit}% DAILY</span>
                        </div>
                        <button onclick="buyNode('${doc.id}', ${p.price})" class="w-full mt-4 btn-prime p-3 text-[9px] uppercase">Activate Node</button>
                    </div>`;
                });
            });
        }

        async function buyNode(id, price) {
            if(user.balance < price) return alert("Insufficient Balance!");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-price), purchaseTime: Date.now() });
            alert("Mining Activated!");
        }

        async function sendDeposit() {
            const a = document.getElementById('dep-amt').value;
            const t = document.getElementById('dep-tid').value;
            if(!a || !t) return alert("Missing Details!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: 'Deposit', status: 'pending', time: Date.now() });
            alert("Request Sent!");
        }

        async function sendWithdraw() {
            const a = parseInt(document.getElementById('wit-amt').value);
            if(a < 500 || a > user.balance) return alert("Invalid Amount!");
            await db.collection("requests").add({ user: user.name, amount: a, method: document.getElementById('wit-method').value, account: document.getElementById('wit-acc').value, type: 'Withdrawal', status: 'pending', time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            alert("Withdrawal Requested!");
        }

        // ADMIN FUNCTIONS
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Pass:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        
        async function adminAddPlan() {
            const n = document.getElementById('new-p-name').value;
            const p = parseInt(document.getElementById('new-p-price').value);
            const pr = parseFloat(document.getElementById('new-p-profit').value);
            await db.collection("plans").add({ name: n, price: p, profit: pr });
            alert("Plan Added!");
        }

        function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-req-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center border-l-4 border-blue-500">
                        <div class="text-[10px] font-bold uppercase"><p>${r.user} | ₨ ${r.amount}</p><p class="opacity-40">${r.type} (${r.tid||r.account})</p></div>
                        <div class="flex gap-2">
                            <button onclick="approveReq('${doc.id}', '${r.user}', ${r.amount}, '${r.type}')" class="bg-blue-600 px-3 py-2 rounded-lg text-[8px] font-black">OK</button>
                            <button onclick="rejectReq('${doc.id}')" class="bg-red-600 px-3 py-2 rounded-lg text-[8px] font-black">X</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function approveReq(id, u, a, type) {
            if(type === 'Deposit') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").doc(id).update({ status: 'approved' });
        }
        async function rejectReq(id) { await db.collection("requests").doc(id).delete(); }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function showLock(t, m) { const s = document.getElementById('lock-screen'); s.classList.remove('hidden'); document.getElementById('lock-title').innerText = t; document.getElementById('lock-msg').innerText = m; }
        function logout() { localStorage.clear(); location.reload(); }
        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user')); }
    </script>
</body>
</html>
