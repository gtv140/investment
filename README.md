<html lang="en" class="dark">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <title>MintCrest Gold | v130.0 Guardian</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&display=swap');
        :root { --bg: #010409; --card: #0d1117; --text: #f0f6fc; --border: #30363d; --blue: #3b82f6; --gold: #f59e0b; }
        
        /* Anti-Copy Protection */
        body { 
            font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 120px; 
            -webkit-user-select: none; -moz-user-select: none; -ms-user-select: none; user-select: none;
        }
        
        .glass { background: var(--card); border: 1px solid var(--border); backdrop-filter: blur(25px); border-radius: 30px; }
        .page { display: none; animation: slideIn 0.5s ease-out; }
        .active-page { display: block; }
        @keyframes slideIn { from { opacity: 0; transform: translateY(30px); } to { opacity: 1; transform: translateY(0); } }
        
        .btn-prime { background: linear-gradient(135deg, #1d4ed8, #3b82f6); color: white; border-radius: 20px; font-weight: 800; border: none; cursor: pointer; box-shadow: 0 10px 20px -10px rgba(59,130,246,0.5); }
        input { background: #161b22; border: 1px solid var(--border); color: var(--text); padding: 18px; border-radius: 18px; width: 100%; outline: none; margin-bottom: 12px; }
        
        .timer-box { background: rgba(59,130,246,0.1); border: 1px dashed var(--blue); padding: 10px; border-radius: 15px; font-size: 10px; font-weight: 800; color: var(--blue); text-align: center; }
        .float-wa { position: fixed; bottom: 120px; right: 20px; width: 60px; height: 60px; background: #25d366; border-radius: 50%; display: flex; items: center; justify-content: center; color: white; font-size: 30px; z-index: 9999; }
    </style>
</head>
<body oncontextmenu="return false;">

    <a href="https://wa.me/923705519562" class="float-wa hidden" id="wa-btn"><i class="fa-brands fa-whatsapp"></i></a>

    <header id="main-header" class="hidden sticky top-0 z-[5000] bg-[var(--bg)]/90 backdrop-blur-3xl border-b border-[var(--border)] p-6 flex justify-between items-center">
        <div class="flex items-center gap-4">
            <div onclick="adminTap()" class="w-11 h-11 bg-blue-600 rounded-2xl flex items-center justify-center font-black text-white italic shadow-lg cursor-pointer">M</div>
            <h1 class="text-[14px] font-black uppercase italic tracking-tighter">MintCrest <span class="text-blue-500">Gold</span></h1>
        </div>
        <div id="top-bal" class="text-[12px] font-black bg-blue-600/10 text-blue-500 px-5 py-2 rounded-full border border-blue-500/20 italic">₨ 0</div>
    </header>

    <main id="app-ui" class="hidden p-5 space-y-7">
        
        <div id="p-home" class="page active-page space-y-7">
            <div class="p-10 rounded-[4rem] bg-gradient-to-br from-blue-700 to-black text-white shadow-2xl text-center border border-white/5 relative overflow-hidden">
                <p class="text-[10px] font-black uppercase opacity-50 tracking-[0.4em] mb-2">Total Balance</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0.00</h2>
                <div class="mt-10 flex gap-3">
                   <button onclick="changePage('wallet')" class="flex-1 bg-white p-4 rounded-2xl text-[10px] font-black uppercase text-blue-950">Deposit</button>
                   <button onclick="changePage('withdraw')" class="flex-1 bg-blue-500/20 p-4 rounded-2xl text-[10px] font-black uppercase border border-white/10">Cashout</button>
                </div>
            </div>

            <div id="profit-section" class="hidden glass p-8 border-l-8 border-green-500 space-y-4">
                <div class="flex justify-between items-center">
                    <h4 class="text-xs font-black uppercase italic">Mining Engine Active</h4>
                    <span id="p-timer" class="text-[10px] font-bold text-green-500">Calculating...</span>
                </div>
                <button id="claim-profit-btn" disabled onclick="requestProfit()" class="w-full py-4 bg-green-600/20 text-green-500 rounded-2xl text-[10px] font-black uppercase border border-green-500/30">Next Profit in 24h</button>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="claimDaily()" id="claim-btn" class="glass p-6 text-center border-b-4 border-yellow-500">
                    <i class="fa-solid fa-coins text-yellow-500 mb-2"></i><p class="text-[10px] font-black uppercase">Daily Reward</p>
                </button>
                <button onclick="changePage('nodes')" class="glass p-6 text-center border-b-4 border-blue-500">
                    <i class="fa-solid fa-gem text-blue-500 mb-2"></i><p class="text-[10px] font-black uppercase">Buy Plans</p>
                </button>
            </div>
        </div>

        <div id="p-nodes" class="page space-y-6 pb-24">
            <h3 class="text-xl font-black italic text-center">INVESTMENT NODES</h3>
            <div id="plans-grid" class="grid grid-cols-1 gap-5"></div>
        </div>

        <div id="p-history" class="page space-y-4 pb-24"><div id="history-list" class="space-y-4"></div></div>
        
        <div id="p-wallet" class="page"><div class="glass p-10 text-center space-y-6">
            <h3 class="text-xl font-black text-blue-500">DEPOSIT FUNDS</h3>
            <div id="gate-nums" class="space-y-2 text-left"></div>
            <input id="d-amt" type="number" placeholder="Amount (PKR)">
            <input id="d-tid" placeholder="Transaction ID">
            <input id="d-proof-file" type="file" accept="image/*" class="text-[10px]">
            <button onclick="sendDeposit()" class="w-full p-6 btn-prime uppercase text-[10px]">Submit for Review</button>
        </div></div>

        <div id="p-withdraw" class="page"><div class="glass p-10 text-center space-y-6">
            <h3 class="text-xl font-black text-red-500">CASHOUT</h3>
            <input id="w-amt" type="number" placeholder="Withdraw Amount">
            <input id="w-acc" placeholder="Account Title & Number">
            <button onclick="sendWithdraw()" class="w-full p-6 btn-prime bg-red-600 text-[10px]">Request Payout</button>
        </div></div>

    </main>

    <div id="admin-ui" class="fixed inset-0 bg-black z-[100000] hidden overflow-y-auto p-7 pb-40">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-3xl font-black text-blue-500 italic">GUARDIAN ADMIN</h2>
            <button onclick="closeAdmin()" class="w-12 h-12 rounded-xl bg-red-600/20 text-red-500">X</button>
        </div>

        <div class="glass p-6 border-l-8 border-green-500 mb-8">
            <h4 class="text-[10px] font-black text-green-500 uppercase mb-4">Profit Requests Desk</h4>
            <div id="adm-profit-list" class="space-y-4"></div>
        </div>

        <div class="glass p-6 mb-8">
            <h4 class="text-[10px] font-black uppercase text-blue-400 mb-4">Plan Architect</h4>
            <input id="adm-p-name" placeholder="Plan Name">
            <div class="flex gap-2">
                <input id="adm-p-price" type="number" placeholder="Price">
                <input id="adm-p-profit" type="number" placeholder="Daily PKR">
            </div>
            <button onclick="createPlan()" class="w-full p-4 btn-prime text-[10px]">Deploy Plan</button>
        </div>

        <div class="glass p-6 mb-8">
            <h4 class="text-[10px] font-black uppercase text-yellow-500 mb-4">System Gateways</h4>
            <input id="adm-j" placeholder="JazzCash Number">
            <input id="adm-e" placeholder="EasyPaisa Number">
            <button onclick="saveGateways()" class="w-full p-4 bg-yellow-600 text-black font-black rounded-xl text-[10px]">Update Numbers</button>
        </div>

        <h4 class="text-xl font-black italic mb-6">Pending Deposits/Withdraws</h4>
        <div id="adm-req-list" class="space-y-6"></div>
    </div>

    <nav id="bottom-nav" class="hidden fixed bottom-0 w-full glass border-t border-[var(--border)] p-6 flex justify-around items-center rounded-t-[3rem] z-[4000]">
        <button onclick="changePage('home')" class="p-3"><i class="fa-solid fa-home text-lg"></i></button>
        <button onclick="changePage('nodes')" class="p-3"><i class="fa-solid fa-gem text-blue-500 text-2xl"></i></button>
        <button onclick="changePage('history')" class="p-3"><i class="fa-solid fa-list text-lg"></i></button>
    </nav>

    <section id="auth-ui" class="fixed inset-0 z-[20000] bg-black flex flex-col items-center justify-center p-12 text-center">
        <h2 class="text-3xl font-black uppercase tracking-[0.3em] mb-10">MintCrest <span class="text-blue-500 italic">Gold</span></h2>
        <input id="l-name" placeholder="YOUR NAME" class="text-center font-bold uppercase py-5">
        <button onclick="login()" class="w-full btn-prime p-6 uppercase text-[10px] font-black">Enter System</button>
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
            if(n.length < 3) return alert("Enter name, sweetie! 😘");
            db.collection("users").doc(n).get().then(doc => {
                if(!doc.exists) db.collection("users").doc(n).set({ name: n, balance: 0, lastClaim: 0, status: 'active', activePlan: null, lastProfitRequest: 0 });
                localStorage.setItem('mc_session', n); enterApp(n);
            });
        }

        function enterApp(n) {
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('main-header').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('wa-btn').classList.remove('hidden');

            db.collection("settings").doc("global").onSnapshot(d => {
                settings = d.data() || { jazzNum: '...', easyNum: '...' };
                document.getElementById('gate-nums').innerHTML = `
                    <div class="p-4 bg-white/5 rounded-xl border border-white/5"><p class="text-[8px] opacity-40">JAZZCASH</p><h4 class="font-black">${settings.jazzNum}</h4></div>
                    <div class="p-4 bg-white/5 rounded-xl border border-white/5"><p class="text-[8px] opacity-40">EASYPAISA</p><h4 class="font-black">${settings.easyNum}</h4></div>
                `;
            });

            db.collection("users").doc(n).onSnapshot(doc => {
                user = doc.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                updateProfitTimer();
            });
            syncPlans(); syncHistory(n);
        }

        // --- PROFIT LOGIC ---
        function updateProfitTimer() {
            if(!user.activePlan) return document.getElementById('profit-section').classList.add('hidden');
            document.getElementById('profit-section').classList.remove('hidden');
            
            const lastTime = user.lastProfitRequest || user.planStartTime;
            const now = Date.now();
            const diff = now - lastTime;
            const waitTime = 24 * 60 * 60 * 1000; // 24 Hours

            if(diff >= waitTime) {
                document.getElementById('claim-profit-btn').disabled = false;
                document.getElementById('claim-profit-btn').innerText = "CLAIM PROFIT REQUEST";
                document.getElementById('claim-profit-btn').style.background = "#16a34a";
                document.getElementById('claim-profit-btn').style.color = "white";
                document.getElementById('p-timer').innerText = "READY!";
            } else {
                const remaining = waitTime - diff;
                const hours = Math.floor(remaining / (1000 * 60 * 60));
                const mins = Math.floor((remaining % (1000 * 60 * 60)) / (1000 * 60));
                document.getElementById('p-timer').innerText = `${hours}h ${mins}m left`;
                document.getElementById('claim-profit-btn').disabled = true;
            }
        }

        async function requestProfit() {
            await db.collection("requests").add({ 
                user: user.name, amount: user.planDailyProfit, 
                type: 'Profit Request', status: 'pending', time: Date.now() 
            });
            await db.collection("users").doc(user.name).update({ lastProfitRequest: Date.now() });
            alert("Profit request sent to admin, sweetie! 😘");
        }

        // --- USER ACTIONS ---
        async function buyPlan(id, price, profit, name) {
            if(user.balance < price) return alert("Incomplete funds! Deposit first, sweetie. 😘");
            if(confirm(`Confirm Investment in ${name}?`)) {
                await db.collection("users").doc(user.name).update({ 
                    balance: firebase.firestore.FieldValue.increment(-price),
                    activePlan: name,
                    planDailyProfit: profit,
                    planStartTime: Date.now(),
                    lastProfitRequest: Date.now()
                });
                alert("Plan Engine Initialized! 🚀");
            }
        }

        // --- ADMIN GOD MODE ---
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Guardian Access:")==="mint786") { document.getElementById('admin-ui').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }

        async function createPlan() {
            const n = document.getElementById('adm-p-name').value;
            const p = parseInt(document.getElementById('adm-p-price').value);
            const pr = parseInt(document.getElementById('adm-p-profit').value);
            await db.collection("plans").add({ name: n, price: p, dailyProfit: pr });
            alert("Plan Deployed! 😘");
        }

        async function saveGateways() {
            await db.collection("settings").doc("global").set({ 
                jazzNum: document.getElementById('adm-j').value, 
                easyNum: document.getElementById('adm-e').value 
            }, { merge: true });
            alert("Numbers Updated! 😘");
        }

        function syncAdm() {
            // General Requests (Deposit/Withdraw)
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const list = document.getElementById('adm-req-list');
                const pList = document.getElementById('adm-profit-list');
                list.innerHTML = ''; pList.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data();
                    const html = `<div class="glass p-5 mb-4 border-l-4 ${r.type==='Deposit'?'border-blue-500':'border-red-500'}">
                        <p class="text-[9px] font-black uppercase opacity-50">${r.user} | ${r.type}</p>
                        <h4 class="text-xl font-black italic">₨ ${r.amount}</h4>
                        <div class="flex gap-2 mt-4">
                            <button onclick="approveReq('${doc.id}', '${r.user}', ${r.amount}, '${r.type}')" class="flex-1 bg-green-600 p-3 rounded-xl text-[9px] font-black">Approve</button>
                            <button onclick="rejectReq('${doc.id}', '${r.user}', ${r.amount}, '${r.type}')" class="flex-1 bg-red-600 p-3 rounded-xl text-[9px] font-black">Reject</button>
                        </div>
                    </div>`;
                    if(r.type === 'Profit Request') pList.innerHTML += html;
                    else list.innerHTML += html;
                });
            });
        }

        async function approveReq(id, u, a, type) {
            if(type === 'Deposit' || type === 'Profit Request') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").doc(id).update({ status: 'approved' });
            alert("Task Approved! 😘");
        }

        async function rejectReq(id, u, a, type) {
            if(type === 'Withdraw') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(a) });
            await db.collection("requests").doc(id).update({ status: 'rejected' });
            alert("Task Rejected! 😘");
        }

        // --- UTILS ---
        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const g = document.getElementById('plans-grid'); g.innerHTML = '';
                s.forEach(doc => {
                    const p = doc.data();
                    g.innerHTML += `<div class="glass p-8 border-l-[15px] border-blue-600 flex justify-between items-center shadow-xl">
                        <div><p class="text-[10px] font-black opacity-30 uppercase">${p.name}</p><h4 class="text-2xl font-black italic">₨ ${p.price}</h4><p class="text-[9px] font-bold text-blue-500 uppercase mt-2">₨ ${p.dailyProfit} Profit/24h</p></div>
                        <button onclick="buyPlan('${doc.id}', ${p.price}, ${p.dailyProfit}, '${p.name}')" class="btn-prime px-8 py-4 text-[10px] font-black uppercase">Buy Now</button>
                    </div>`;
                });
            });
        }

        function syncHistory(n) {
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const h = document.getElementById('history-list'); h.innerHTML = '';
                s.forEach(doc => {
                    const r = doc.data(); const clr = r.status==='pending'?'bg-yellow-500':(r.status==='approved'?'bg-green-500':'bg-red-600');
                    h.innerHTML += `<div class="glass p-6 flex justify-between items-center"><div><p class="text-[8px] opacity-30 font-black uppercase">${r.type}</p><h4 class="text-lg font-black italic">₨ ${r.amount}</h4></div><div class="px-4 py-1.5 rounded-full text-[8px] font-black uppercase ${clr} text-black">${r.status}</div></div>`;
                });
            });
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); window.scrollTo(0,0); }
        function copyRef() { navigator.clipboard.writeText(user.name.toUpperCase()); alert("Code Copied! 😘"); }
        window.onload = () => { if(localStorage.getItem('mc_session')) enterApp(localStorage.getItem('mc_session')); }
        setInterval(() => { if(user) updateProfitTimer(); }, 60000);
    </script>
</body>
</html>
