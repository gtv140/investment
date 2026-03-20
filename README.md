<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | Ultimate v20.0</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 100px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); backdrop-filter: blur(15px); }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #2563eb, #3b82f6); color: white; border-radius: 18px; font-weight: 800; border: none; cursor: pointer; transition: 0.3s; }
        input, select { background: #161b22; border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 14px; width: 100%; outline: none; font-size: 13px; margin-bottom: 10px; }
        .admin-section { background: rgba(37, 99, 235, 0.05); border: 1px solid rgba(37, 99, 235, 0.1); border-radius: 24px; padding: 16px; margin-bottom: 20px; }
        .trust-badge { font-size: 8px; background: rgba(16, 185, 129, 0.1); color: #10b981; padding: 3px 8px; border-radius: 6px; font-weight: 900; border: 1px solid rgba(16, 185, 129, 0.2); }
    </style>
</head>
<body>

    <div id="global-notice" class="hidden sticky top-0 z-[6000] bg-blue-600 text-white text-[10px] font-black p-2 text-center uppercase italic animate-pulse">
        <span id="notice-text">Connecting to Secure Node...</span>
    </div>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/90 backdrop-blur-xl border-b border-[var(--border)] p-4 flex justify-between items-center">
        <div class="flex items-center gap-3">
            <div onclick="adminTap()" class="w-10 h-10 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg cursor-pointer">M</div>
            <div>
                <h1 class="text-[12px] font-black uppercase tracking-tighter">MintCrest <span class="text-blue-500">Gold</span></h1>
                <span class="trust-badge"><i class="fa-solid fa-shield-check"></i> SSL SECURED</span>
            </div>
        </div>
        <div class="flex items-center gap-3">
            <div id="top-bal" class="text-[10px] font-black bg-blue-600/10 text-blue-500 px-4 py-2 rounded-full border border-blue-500/20 italic">₨ 0</div>
            <button onclick="logout()" class="w-9 h-9 rounded-2xl bg-red-600/10 text-red-500 border border-red-500/20 flex items-center justify-center transition-all active:scale-90"><i class="fa-solid fa-power-off"></i></button>
        </div>
    </header>

    <main id="app-ui" class="hidden p-4 space-y-6">
        
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-8 rounded-[3rem] bg-gradient-to-br from-blue-700 to-indigo-950 text-white shadow-2xl relative overflow-hidden">
                <div class="absolute top-[-20px] right-[-20px] w-40 h-40 bg-white/5 rounded-full blur-3xl"></div>
                <p class="text-[10px] font-black uppercase opacity-60 italic tracking-widest">Available Balance</p>
                <h2 class="text-5xl font-black tracking-tighter my-2" id="v-bal">₨ 0.00</h2>
                <div class="mt-6 flex gap-3">
                   <button onclick="changePage('wallet')" class="flex-1 bg-white/10 p-3 rounded-2xl text-[10px] font-black uppercase italic border border-white/10">Deposit</button>
                   <button onclick="changePage('withdraw')" class="flex-1 bg-blue-400/20 p-3 rounded-2xl text-[10px] font-black uppercase italic border border-blue-400/20">Withdraw</button>
                </div>
            </div>
            
            <div class="flex justify-between items-center px-2">
                <h3 class="text-[11px] font-black uppercase text-blue-500 italic">Mining Nodes</h3>
                <span class="text-[8px] font-bold opacity-40">Min Deposit: ₨ 200</span>
            </div>
            <div id="plans-grid" class="grid grid-cols-1 gap-4 pb-24"></div>
        </div>

        <div id="p-history" class="page space-y-4 pb-24 text-center">
            <h3 class="font-black uppercase text-blue-500 italic"><i class="fa-solid fa-clock-rotate-left mr-2"></i>Activity Statement</h3>
            <div id="user-logs-list" class="space-y-3"></div>
        </div>

        <div id="p-wallet" class="page">
            <div class="glass p-8 rounded-[3.5rem] space-y-6 text-center">
                <div class="w-16 h-16 bg-blue-600/10 text-blue-500 rounded-full flex items-center justify-center mx-auto text-2xl"><i class="fa-solid fa-wallet"></i></div>
                <h3 class="font-black uppercase text-blue-500 italic">Deposit Funds</h3>
                <p class="text-[10px] opacity-50 italic">Minimum deposit amount: ₨ 200</p>
                
                <div id="qr-area" class="hidden p-4 bg-white rounded-3xl mx-auto w-fit shadow-xl"><img id="qr-img" class="w-44 h-44"></div>
                
                <div class="flex gap-2">
                    <button onclick="showQR('Jazz')" class="flex-1 glass p-4 rounded-2xl text-[10px] font-black border-blue-500/20">JAZZCASH</button>
                    <button onclick="showQR('Easy')" class="flex-1 glass p-4 rounded-2xl text-[10px] font-black border-blue-500/20">EASYPAISA</button>
                </div>
                
                <input id="d-amt" type="number" placeholder="Deposit Amount (Min 200)">
                <input id="d-tid" placeholder="Transaction ID (TID)">
                <button onclick="sendDeposit()" class="w-full p-4 btn-prime text-[11px] font-black uppercase italic tracking-widest">Verify Payment</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <div class="glass p-8 rounded-[3.5rem] space-y-6 text-center">
                <div class="w-16 h-16 bg-green-600/10 text-green-500 rounded-full flex items-center justify-center mx-auto text-2xl"><i class="fa-solid fa-money-bill-trend-up"></i></div>
                <h3 class="font-black uppercase text-blue-500 italic">Request Payout</h3>
                <p class="text-[10px] opacity-50 italic">Minimum withdrawal amount: ₨ 100</p>
                <input id="w-amt" type="number" placeholder="Amount (Min 100)">
                <input id="w-acc" placeholder="Account Number">
                <select id="w-method"><option value="JazzCash">JazzCash</option><option value="EasyPaisa">EasyPaisa</option></select>
                <button onclick="sendWithdraw()" class="w-full p-4 btn-prime text-[11px] font-black uppercase italic tracking-widest">Submit Request</button>
            </div>
        </div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-6 pb-32">
        <div class="flex justify-between items-center mb-8 border-b border-white/5 pb-4">
            <h2 class="text-xl font-black text-blue-500 uppercase italic"><i class="fa-solid fa-screwdriver-wrench"></i> Goddess Center</h2>
            <button onclick="closeAdmin()" class="bg-red-600/10 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black border border-red-500/20">EXIT</button>
        </div>

        <div class="admin-section">
            <h4 class="text-[10px] font-black uppercase text-blue-400 mb-4 flex items-center gap-2"><i class="fa-solid fa-users"></i> Member Directory</h4>
            <div id="adm-user-list" class="space-y-1 max-h-48 overflow-y-auto"></div>
        </div>

        <div class="admin-section">
            <h4 class="text-[10px] font-black uppercase text-green-400 mb-4 flex items-center gap-2"><i class="fa-solid fa-pen-fancy"></i> Modify System</h4>
            <input id="adm-un" placeholder="Username (To update balance)">
            <input id="adm-ub" type="number" placeholder="New Balance (PKR)">
            <button onclick="updateUserBal()" class="w-full p-3 btn-prime text-[9px] uppercase mt-2 mb-4">Set Balance</button>
            
            <div class="border-t border-white/5 pt-4 space-y-2">
                <input id="adm-notice" placeholder="Global Notice Message">
                <input id="adm-jqr" placeholder="JazzCash QR URL">
                <input id="adm-eqr" placeholder="EasyPaisa QR URL">
                <button onclick="saveSettings()" class="w-full p-3 bg-white/5 border border-white/20 rounded-xl text-[9px] uppercase">Save Global Settings</button>
            </div>
        </div>

        <div class="admin-section">
            <h4 class="text-[10px] font-black uppercase text-purple-400 mb-4"><i class="fa-solid fa-plus-circle"></i> Add Plan</h4>
            <input id="p-name" placeholder="Plan Name">
            <div class="flex gap-2"><input id="p-price" type="number" placeholder="Price"><input id="p-profit" type="number" placeholder="Profit %"></div>
            <button onclick="addPlan()" class="w-full p-3 bg-white/5 border border-white/10 rounded-xl text-[9px] uppercase">Create Node</button>
        </div>

        <h4 class="text-[11px] font-black uppercase text-yellow-500 mb-4 italic px-2">Pending Verification</h4>
        <div id="adm-req-list" class="space-y-4"></div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-8">
        <div class="w-24 h-24 bg-blue-600 rounded-[2.5rem] mb-12 flex items-center justify-center text-6xl font-black italic text-white shadow-2xl shadow-blue-600/30">M</div>
        <div class="w-full max-w-[320px] space-y-5">
            <div class="text-center">
                <h2 class="font-black uppercase text-blue-500 italic tracking-widest text-xl">MintCrest Login</h2>
                <p class="text-[9px] opacity-40 font-bold uppercase mt-1">Authorized Access Only</p>
            </div>
            <input id="l-name" placeholder="ENTER USERNAME" class="text-center font-black uppercase italic tracking-widest border-blue-500/20">
            <button onclick="login()" class="w-full p-5 btn-prime uppercase text-[12px] font-black tracking-widest shadow-2xl transition-all active:scale-95">Connect to Node</button>
        </div>
    </section>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-5 flex justify-around items-center rounded-t-[3.5rem] z-[4000]">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-bolt-lightning text-lg"></i><span class="text-[7px] font-black uppercase">Nodes</span></button>
        <button onclick="changePage('wallet')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-plus-circle text-lg text-blue-500"></i><span class="text-[7px] font-black uppercase">Add Funds</span></button>
        <button onclick="changePage('history')" class="flex flex-col items-center gap-1"><i class="fa-solid fa-receipt text-lg"></i><span class="text-[7px] font-black uppercase">Logs</span></button>
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
        let user = null; let tapCount = 0; let globalSet = { jazz: '', easy: '', notice: '' };

        // 1. AUTH SYSTEM
        function login() {
            const n = document.getElementById('l-name').value.trim().toLowerCase();
            if(n.length < 3) return alert("Enter valid username sweetie! 😘");
            localStorage.setItem('mc_session', n);
            enterApp(n);
        }

        function logout() {
            if(confirm("Logout from MintCrest? 😘")) {
                localStorage.removeItem('mc_session');
                location.reload();
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

            db.collection("settings").doc("global").onSnapshot(d => {
                if(d.exists) {
                    globalSet = d.data();
                    if(globalSet.notice) {
                        document.getElementById('global-notice').classList.remove('hidden');
                        document.getElementById('notice-text').innerText = globalSet.notice;
                    } else document.getElementById('global-notice').classList.add('hidden');
                }
            });

            syncPlans();
            syncLogs();
        }

        // 2. INVESTMENT LOGIC
        async function buyPlan(id, price, name) {
            if(user.balance < price) {
                alert("Low Balance! Minimum needed: ₨ " + price + " 😘");
                return changePage('wallet');
            }
            if(confirm(`Activate ${name} Node for ₨ ${price}?`)) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-price) });
                await db.collection("logs").add({ user: user.name, msg: `Node Active: ${name}`, status: 'approved', time: Date.now() });
                alert("Node Activated Successfully! 🚀");
            }
        }

        // 3. TRANSACTIONS
        async function sendDeposit() {
            const a = parseInt(document.getElementById('d-amt').value);
            const t = document.getElementById('d-tid').value;
            if(!a || a < 200 || !t) return alert("Minimum Deposit is ₨ 200 with TID! 😘");
            
            const id = Date.now().toString();
            await db.collection("requests").doc(id).set({ user: user.name, amount: a, tid: t, status: 'pending', type: 'Deposit', time: Date.now() });
            await db.collection("logs").doc(id).set({ user: user.name, msg: `Deposit: ₨ ${a}`, status: 'pending', time: Date.now() });
            alert("Sent for approval! Keep patience. 😘");
        }

        async function sendWithdraw() {
            const a = parseInt(document.getElementById('w-amt').value);
            const acc = document.getElementById('w-acc').value;
            if(!a || a < 100 || user.balance < a) return alert("Min Withdrawal ₨ 100 & Check Balance! 😘");
            
            const id = Date.now().toString();
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").doc(id).set({ user: user.name, amount: a, acc: acc, status: 'pending', type: 'Withdraw', time: Date.now() });
            await db.collection("logs").doc(id).set({ user: user.name, msg: `Withdraw: ₨ ${a}`, status: 'pending', time: Date.now() });
            alert("Withdrawal request submitted! 😘");
        }

        // 4. ADMINISTRATION
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Goddess Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function showQR(t) { document.getElementById('qr-area').classList.remove('hidden'); document.getElementById('qr-img').src = (t==='Jazz'?globalSet.jazz:globalSet.easy); }

        async function saveSettings() {
            const j = document.getElementById('adm-jqr').value;
            const e = document.getElementById('adm-eqr').value;
            const n = document.getElementById('adm-notice').value;
            await db.collection("settings").doc("global").set({ jazz: j, easy: e, notice: n });
            alert("System settings updated! 😘");
        }

        async function updateUserBal() {
            const u = document.getElementById('adm-un').value.toLowerCase();
            const b = parseInt(document.getElementById('adm-ub').value);
            await db.collection("users").doc(u).update({ balance: b });
            alert("User balance updated! 😘");
        }

        async function addPlan() {
            const n = document.getElementById('p-name').value;
            const p = parseInt(document.getElementById('p-price').value);
            const pr = parseInt(document.getElementById('p-profit').value);
            if(n && p) await db.collection("plans").add({ name: n, price: p, profit: pr });
            alert("New Plan added! 😘");
        }

        async function syncAdm() {
            db.collection("users").onSnapshot(s => {
                const l = document.getElementById('adm-user-list'); l.innerHTML = '';
                s.forEach(doc => { l.innerHTML += `<div class="flex justify-between items-center p-2 border-b border-white/5 uppercase text-[9px] font-bold"><span>${doc.data().name}</span><span class="text-blue-500">₨ ${doc.data().balance}</span></div>`; });
            });
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-req-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    list.innerHTML += `<div class="glass p-4 rounded-3xl border-l-4 border-yellow-500 mb-2">
                        <p class="text-[9px] font-black uppercase text-blue-400">${r.user} | ${r.type}</p>
                        <h4 class="text-xl font-black">₨ ${r.amount}</h4>
                        <p class="text-[8px] opacity-40 italic">ID/ACC: ${r.tid || r.acc}</p>
                        <button onclick="approveReq('${doc.id}', '${r.user}', ${r.amount}, '${r.type}')" class="w-full bg-blue-600 p-2 rounded-xl text-[9px] font-black uppercase mt-3">Approve</button>
                    </div>`;
                });
            });
        }

        async function approveReq(id, u, a, type) {
            if(type === 'Deposit') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").doc(id).update({ status: 'approved' });
            await db.collection("logs").doc(id).update({ status: 'approved' });
            alert("Request Approved! 😘");
        }

        // 5. UI SYNC
        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    g.innerHTML += `<div class="glass p-6 rounded-[2.8rem] flex justify-between items-center border-l-4 border-blue-500 shadow-xl transition-all active:scale-95">
                        <div><p class="text-[9px] font-black opacity-40 uppercase tracking-widest">${p.name}</p><h4 class="text-2xl font-black italic">₨ ${p.price}</h4><p class="text-[8px] font-bold text-blue-400"><i class="fa-solid fa-chart-line"></i> ${p.profit}% Daily Profit</p></div>
                        <button onclick="buyPlan('${doc.id}', ${p.price}, '${p.name}')" class="btn-prime px-7 py-4 text-[11px] font-black uppercase italic shadow-lg shadow-blue-600/20">Activate</button>
                    </div>`;
                });
            });
        }

        function syncLogs() {
            db.collection("logs").where("user", "==", user.name).orderBy("time", "desc").limit(10).onSnapshot(s => {
                const l = document.getElementById('user-logs-list'); l.innerHTML = '';
                s.forEach(doc => {
                    const d = doc.data();
                    l.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center border-l-2 ${d.status==='approved'?'border-green-500':'border-yellow-500'}">
                        <p class="text-[10px] font-black uppercase">${d.msg}</p>
                        <span class="text-[8px] font-black uppercase ${d.status==='approved'?'text-green-500':'text-yellow-500'}">${d.status}</span>
                    </div>`;
                });
            });
        }

        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); 
            document.getElementById('p-'+p).classList.add('active-page'); 
            window.scrollTo(0,0);
        }

        window.onload = () => {
            const saved = localStorage.getItem('mc_session');
            if(saved) enterApp(saved);
        }
    </script>
</body>
</html>
