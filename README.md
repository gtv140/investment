<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | v50.0 Royal Elite</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --accent: #3b82f6; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 110px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); backdrop-filter: blur(20px); }
        .page { display: none; animation: slideUp 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1d4ed8, #3b82f6); color: white; border-radius: 25px; font-weight: 800; border: none; cursor: pointer; transition: 0.3s; box-shadow: 0 10px 25px -10px rgba(59, 130, 246, 0.5); }
        input { background: #161b22; border: 1px solid var(--border); color: var(--text); padding: 18px; border-radius: 22px; width: 100%; outline: none; font-size: 14px; margin-bottom: 14px; transition: 0.3s; }
        input:focus { border-color: #3b82f6; background: #0d1117; }
        .admin-card { background: linear-gradient(145deg, rgba(37,99,235,0.08), rgba(0,0,0,0)); border: 1px solid rgba(255,255,255,0.05); border-radius: 40px; padding: 30px; margin-bottom: 30px; }
        .status-badge { font-size: 10px; font-weight: 900; padding: 4px 12px; rounded-full: 50px; text-transform: uppercase; border-radius: 50px; }
    </style>
</head>
<body>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/95 backdrop-blur-2xl border-b border-[var(--border)] p-5 flex justify-between items-center">
        <div class="flex items-center gap-4">
            <div onclick="adminTap()" class="w-12 h-12 bg-blue-600 rounded-[1.3rem] flex items-center justify-center font-black text-white italic shadow-2xl active:scale-90 transition cursor-pointer">M</div>
            <div>
                <h1 class="text-[14px] font-black uppercase italic tracking-tighter">MintCrest <span class="text-blue-500">Gold</span></h1>
                <div class="flex items-center gap-1">
                    <p id="display-user" class="text-[9px] font-bold text-blue-400 uppercase tracking-widest">@USER</p>
                    <i class="fa-solid fa-circle-check text-blue-500 text-[8px]"></i>
                </div>
            </div>
        </div>
        <div id="top-bal" class="text-[12px] font-black bg-blue-600/10 text-blue-500 px-6 py-2.5 rounded-full border border-blue-500/30 italic">₨ 0</div>
    </header>

    <main id="app-ui" class="hidden p-5 space-y-7">
        
        <div id="p-home" class="page active-page space-y-7">
            <div class="p-12 rounded-[4.5rem] bg-gradient-to-br from-blue-700 via-blue-900 to-black text-white shadow-2xl relative overflow-hidden text-center border border-white/10">
                <div class="absolute -right-10 -top-10 w-40 h-40 bg-blue-400 opacity-10 blur-3xl"></div>
                <p class="text-[11px] font-black uppercase opacity-60 tracking-[0.5em] mb-3">Total Balance</p>
                <h2 class="text-6xl font-black tracking-tighter drop-shadow-2xl" id="v-bal">₨ 0.00</h2>
                <div class="mt-12 flex gap-4">
                   <button onclick="changePage('wallet')" class="flex-1 bg-white p-5 rounded-3xl text-[11px] font-black uppercase text-blue-950 shadow-2xl active:scale-95 transition">Deposit</button>
                   <button onclick="changePage('withdraw')" class="flex-1 bg-blue-500/20 p-5 rounded-3xl text-[11px] font-black uppercase border border-white/20 active:scale-95 transition backdrop-blur-xl">Withdraw</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-5">
                <button onclick="changePage('history')" class="glass p-8 rounded-[3.5rem] flex flex-col items-center gap-4 text-blue-400 shadow-2xl border-blue-500/10 active:scale-95 transition">
                    <i class="fa-solid fa-receipt text-3xl"></i>
                    <span class="text-[10px] font-black uppercase tracking-widest">My Records</span>
                </button>
                <a href="https://chat.whatsapp.com/YOUR_LINK" class="glass p-8 rounded-[3.5rem] flex flex-col items-center gap-4 text-green-500 shadow-2xl border-green-500/10 active:scale-95 transition">
                    <i class="fa-brands fa-whatsapp text-4xl"></i>
                    <span class="text-[10px] font-black uppercase tracking-widest">Support</span>
                </a>
            </div>
        </div>

        <div id="p-history" class="page space-y-5">
            <h3 class="text-2xl font-black uppercase italic text-center text-blue-500 tracking-tighter">Activity History</h3>
            <div id="history-list" class="space-y-4"></div>
        </div>

        <div id="p-nodes" class="page space-y-5 pb-24">
            <div class="text-center">
                <h3 class="text-2xl font-black uppercase italic text-white tracking-tighter">Mining Nodes</h3>
                <p class="text-[9px] text-blue-500 font-bold uppercase tracking-[0.4em]">Select Your Asset</p>
            </div>
            <div id="plans-grid" class="grid grid-cols-1 gap-5"></div>
        </div>

        <div id="p-wallet" class="page"><div class="glass p-10 rounded-[4.5rem] space-y-7 text-center shadow-2xl border-blue-500/20">
            <h3 class="font-black uppercase text-blue-500 italic text-xl tracking-[0.2em]">Add Capital</h3>
            <div class="space-y-4">
                <div class="p-6 bg-white/5 border border-white/5 rounded-[2.5rem] relative">
                    <p class="text-[9px] uppercase opacity-40 mb-1">JazzCash Merchant</p>
                    <h4 id="j-num-disp" class="text-2xl font-black text-white italic tracking-widest">03xx...</h4>
                    <button onclick="copy('j-num-disp')" class="absolute top-5 right-6 text-blue-500"><i class="fa-solid fa-copy"></i></button>
                </div>
                <div class="p-6 bg-white/5 border border-white/5 rounded-[2.5rem] relative">
                    <p class="text-[9px] uppercase opacity-40 mb-1">EasyPaisa Merchant</p>
                    <h4 id="e-num-disp" class="text-2xl font-black text-white italic tracking-widest">03xx...</h4>
                    <button onclick="copy('e-num-disp')" class="absolute top-5 right-6 text-blue-500"><i class="fa-solid fa-copy"></i></button>
                </div>
            </div>
            <input id="d-amt" type="number" placeholder="Enter Amount (PKR)">
            <input id="d-tid" placeholder="Transaction ID (TID)">
            <div class="text-left bg-blue-500/5 p-6 rounded-[2.5rem] border border-blue-500/10">
                <label class="text-[10px] font-black uppercase opacity-60 block mb-3">Upload Screenshot Proof:</label>
                <input id="d-proof-file" type="file" accept="image/*" class="text-[11px] file:bg-blue-600 file:border-none file:text-white file:rounded-xl file:px-5 file:py-2 cursor-pointer">
            </div>
            <button id="dep-btn" onclick="sendDeposit()" class="w-full p-6 btn-prime text-[12px] font-black uppercase italic tracking-[0.2em]">Secure Submit</button>
        </div></div>

        <div id="p-withdraw" class="page"><div class="glass p-10 rounded-[4.5rem] space-y-7 text-center shadow-2xl">
            <h3 class="font-black uppercase text-blue-500 italic text-xl tracking-[0.2em]">Liquidate Assets</h3>
            <input id="w-amt" type="number" placeholder="Amount to Withdraw">
            <input id="w-acc" placeholder="Account Name & Number">
            <button onclick="sendWithdraw()" class="w-full p-6 btn-prime text-[12px] font-black uppercase italic">Confirm Cashout</button>
        </div></div>
    </main>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-7 pb-44">
        <div class="flex justify-between items-center mb-12">
            <div>
                <h2 class="text-3xl font-black text-blue-500 italic uppercase tracking-tighter">Elite Admin</h2>
                <p class="text-[9px] font-black text-white/30 uppercase tracking-[0.5em]">Manual Control Mode</p>
            </div>
            <button onclick="closeAdmin()" class="w-16 h-16 rounded-[1.8rem] bg-red-600/20 text-red-500 border border-red-500/20 flex items-center justify-center font-black text-xl">X</button>
        </div>

        <div class="admin-card border-l-4 border-yellow-500">
            <h4 class="text-[11px] font-black uppercase text-yellow-500 mb-6 tracking-widest">Manual Profit & Bonuses</h4>
            <p class="text-[9px] opacity-40 mb-4 uppercase">Directly add money to any user's wallet</p>
            <input id="adm-bonus-user" placeholder="User Full Name">
            <input id="adm-bonus-amt" type="number" placeholder="Amount (e.g. 500 or -500)">
            <button onclick="sendManualBonus()" class="w-full p-5 bg-yellow-600 text-black rounded-2xl text-[11px] font-black uppercase shadow-xl">Apply Adjustment</button>
        </div>

        <div class="admin-card">
            <h4 class="text-[11px] font-black uppercase text-blue-400 mb-6 tracking-widest">Platform Gateways</h4>
            <input id="adm-jnum" placeholder="JazzCash Number">
            <input id="adm-enum" placeholder="EasyPaisa Number">
            <button onclick="savePaymentSettings()" class="w-full p-5 bg-blue-600 rounded-2xl text-[11px] font-black uppercase shadow-xl">Save Numbers</button>
        </div>

        <h4 class="text-[18px] font-black uppercase text-green-500 italic mb-8 tracking-tighter">Incoming Requests</h4>
        <div id="adm-req-list" class="space-y-10"></div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-7 flex justify-around items-center rounded-t-[4.5rem] z-[4000] shadow-2xl">
        <button onclick="changePage('home')" class="p-3 active:scale-90 transition"><i class="fa-solid fa-house-user text-xl"></i></button>
        <button onclick="changePage('nodes')" class="p-3 active:scale-90 transition"><i class="fa-solid fa-gem text-blue-500 text-4xl"></i></button>
        <button onclick="changePage('history')" class="p-3 active:scale-90 transition"><i class="fa-solid fa-clock-rotate-left text-xl"></i></button>
        <button onclick="logout()" class="p-3 active:scale-90 transition text-red-500/40"><i class="fa-solid fa-power-off text-xl"></i></button>
    </nav>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-[var(--bg)] flex flex-col items-center justify-center p-12 text-center">
        <div class="w-28 h-28 bg-blue-600 rounded-[3rem] mb-14 flex items-center justify-center text-6xl font-black italic text-white shadow-2xl shadow-blue-600/40 animate-pulse">M</div>
        <h2 class="text-2xl font-black uppercase tracking-[0.5em] mb-14">MintCrest <span class="text-blue-500 italic">Gold</span></h2>
        <input id="l-name" placeholder="YOUR NAME" class="max-w-[340px] text-center font-black uppercase italic border-blue-500/20 py-6">
        <button onclick="login()" class="w-full max-w-[340px] p-8 btn-prime uppercase text-[13px] font-black tracking-[0.4em] shadow-blue-600/30">Authorize Access</button>
        <p class="text-[10px] uppercase font-bold opacity-30 mt-10 tracking-widest italic">Prime Solutions Verified</p>
    </section>

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
            if(n.length < 3) return alert("Please enter your name, sweetie! 😘");
            localStorage.setItem('mc_session', n); enterApp(n);
        }

        async function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('main-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-user').innerText = "@" + n.toUpperCase();

            db.collection("settings").doc("global").onSnapshot(d => {
                settings = d.data() || {};
                document.getElementById('j-num-disp').innerText = settings.jazzNum || '03xx-xxxxxxx';
                document.getElementById('e-num-disp').innerText = settings.easyNum || '03xx-xxxxxxx';
            });

            db.collection("users").doc(n).onSnapshot(doc => {
                if(!doc.exists) return db.collection("users").doc(n).set({ name: n, balance: 0, status: 'active', joined: Date.now() });
                user = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                syncHistory(n);
            });
            syncPlans();
        }

        // --- ELITE ADMIN COMMANDS ---
        async function sendManualBonus() {
            const u = document.getElementById('adm-bonus-user').value.trim().toLowerCase();
            const a = parseFloat(document.getElementById('adm-bonus-amt').value);
            if(!u || isNaN(a)) return alert("Check user name and amount! 😘");
            
            await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").add({ user: u, amount: a, type: 'Manual Profit/Bonus', status: 'approved', time: Date.now() });
            alert(`Manual Adjustment of ₨ ${a} sent to ${u}! 😘`);
        }

        async function processRequest(id, u, a, action, type) {
            if(action === 'approve') {
                // If withdrawal, we already deducted balance during request. If deposit, we add it.
                if(type === 'Deposit') {
                    await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
                }
                await db.collection("requests").doc(id).update({ status: 'approved' });
                alert("Request Approved! 😘");
            } else {
                // If rejected withdrawal, we must refund the balance to the user
                if(type === 'Withdraw') {
                    await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
                }
                await db.collection("requests").doc(id).update({ status: 'rejected' });
                alert("Request Rejected! ❌");
            }
        }

        async function savePaymentSettings() {
            const j = document.getElementById('adm-jnum').value; const e = document.getElementById('adm-enum').value;
            await db.collection("settings").doc("global").set({ jazzNum: j, easyNum: e }, { merge: true });
            alert("Payment Numbers Updated! 😘");
        }

        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Access Key:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        async function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-req-list'); list.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    const isDep = r.type === 'Deposit';
                    list.innerHTML += `<div class="glass p-8 rounded-[3.5rem] border-l-8 ${isDep?'border-blue-500':'border-red-500'} mb-10 shadow-2xl text-center">
                        <p class="text-[11px] font-black text-blue-400 uppercase tracking-[0.2em]">${r.user} | ${r.type}</p>
                        <h4 class="text-4xl font-black my-4 tracking-tighter">₨ ${r.amount}</h4>
                        ${r.proof ? `<img src="${r.proof}" class="w-full rounded-[3rem] my-6 border border-white/10 shadow-2xl">` : `<p class="italic opacity-40 my-4">No Screenshot (Withdrawal)</p>`}
                        <p class="text-[10px] font-bold opacity-40 mb-6 italic">TID: ${r.tid || 'N/A'}</p>
                        <div class="flex gap-4">
                            <button onclick="processRequest('${doc.id}', '${r.user}', ${r.amount}, 'approve', '${r.type}')" class="flex-1 bg-green-600 p-5 rounded-2xl text-[11px] font-black uppercase">Approve</button>
                            <button onclick="processRequest('${doc.id}', '${r.user}', ${r.amount}, 'reject', '${r.type}')" class="flex-1 bg-red-600 p-5 rounded-2xl text-[11px] font-black uppercase">Reject</button>
                        </div>
                    </div>`;
                });
            });
        }

        // --- USER CORE LOGIC ---
        async function sendDeposit() {
            const a = parseInt(document.getElementById('d-amt').value); 
            const t = document.getElementById('d-tid').value;
            const file = document.getElementById('d-proof-file').files[0];
            if(!a || !t || !file) return alert("Sweetie, fill all fields & upload proof! 😘");
            
            const reader = new FileReader();
            reader.readAsDataURL(file);
            reader.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: a, tid: t, proof: reader.result, status: 'pending', type: 'Deposit', time: Date.now() });
                alert("Deposit request transmitted! Wait for manual approval. 😘");
                changePage('home');
            };
        }

        async function sendWithdraw() {
            const a = parseInt(document.getElementById('w-amt').value);
            const acc = document.getElementById('w-acc').value;
            if(!a || a < 100 || !acc) return alert("Check amount & account details! 😘");
            if(user.balance < a) return alert("Insufficient Balance! 😘");

            // Deduct immediately, if rejected we refund in admin logic
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-a) });
            await db.collection("requests").add({ user: user.name, amount: a, tid: acc, status: 'pending', type: 'Withdraw', time: Date.now() });
            alert("Withdrawal request sent! 😘");
            changePage('home');
        }

        function syncHistory(n) {
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const h = document.getElementById('history-list'); h.innerHTML = '';
                s.forEach(doc => {
                    doc.data();
                    const color = r.status === 'pending' ? 'bg-yellow-500' : (r.status === 'approved' ? 'bg-green-500' : 'bg-red-600');
                    h.innerHTML += `<div class="glass p-6 rounded-[2.5rem] flex justify-between items-center border border-white/5">
                        <div><p class="text-[9px] font-black uppercase opacity-40">${r.type}</p><h4 class="text-xl font-black">₨ ${r.amount}</h4></div>
                        <div class="status-badge ${color} text-black">${r.status}</div>
                    </div>`;
                });
            });
        }

        async function buyPlan(id, price, name) {
            if(user.balance < price) return alert("Sweetie, please deposit first! 😘");
            if(confirm(`Engage the ${name} node for ₨ ${price}?`)) {
                await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-price) });
                // Record the purchase in history
                await db.collection("requests").add({ user: user.name, amount: price, type: 'Plan Activation: '+name, status: 'approved', time: Date.now() });
                alert(`${name} is now active! Profits will be manually added by Admin. 🚀`);
            }
        }

        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => { const p = doc.data(); g.innerHTML += `<div class="glass p-9 rounded-[4rem] flex justify-between items-center border-l-[10px] border-blue-600 shadow-2xl relative overflow-hidden">
                    <div class="absolute -right-6 -bottom-6 w-24 h-24 bg-blue-600/5 rounded-full blur-2xl"></div>
                    <div><p class="text-[10px] font-black opacity-30 uppercase tracking-[0.3em]">${p.name}</p><h4 class="text-3xl font-black italic tracking-tighter">₨ ${p.price}</h4><p class="text-[10px] font-bold text-blue-500 uppercase mt-2 flex items-center gap-2"><i class="fa-solid fa-bolt-lightning"></i> ${p.profit}% Daily Profit</p></div>
                    <button onclick="buyPlan('${doc.id}', ${p.price}, '${p.name}')" class="btn-prime px-10 py-5 text-[12px] font-black uppercase tracking-widest active:scale-90 transition shadow-blue-500/20">Engage</button>
                </div>`; });
            });
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); window.scrollTo(0,0); }
        function logout() { localStorage.removeItem('mc_session'); location.reload(); }
        function copy(id) { navigator.clipboard.writeText(document.getElementById(id).innerText); alert("Copied to Clipboard! 😘"); }
        window.onload = () => { if(localStorage.getItem('mc_session')) enterApp(localStorage.getItem('mc_session')); }
    </script>
</body>
</html>
