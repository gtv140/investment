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
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); padding-bottom: 100px; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); }
        .page { display: none; animation: slideUp 0.4s ease forwards; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .btn-prime { background: linear-gradient(135deg, #1e40af, #3b82f6); color: white; border-radius: 16px; font-weight: 800; transition: 0.2s; }
        input, textarea, select { background: var(--card); border: 1px solid var(--border); color: var(--text); padding: 14px; border-radius: 14px; width: 100%; outline: none; margin-bottom: 10px; }
    </style>
</head>
<body>

    <header class="p-4 flex justify-between items-center sticky top-0 bg-[var(--bg)]/80 backdrop-blur-md border-b border-[var(--border)] z-[5000]">
        <div class="flex items-center gap-2">
            <div onclick="adminTap()" class="w-9 h-9 bg-blue-600 rounded-xl flex items-center justify-center font-black text-white italic cursor-pointer shadow-lg shadow-blue-500/20">M</div>
            <span class="font-black text-xs uppercase tracking-tighter">MintCrest <span class="text-blue-500">Gold</span></span>
        </div>
        <div class="flex items-center gap-2">
            <div id="top-bal" class="text-[10px] font-black bg-blue-600/10 text-blue-500 px-4 py-1.5 rounded-full border border-blue-500/20 italic">₨ 0</div>
            <button onclick="logout()" class="w-8 h-8 glass rounded-full flex items-center justify-center text-[10px]">🚪</button>
        </div>
    </header>

    <main id="app-ui" class="p-4 space-y-6">
        <div id="p-home" class="page active-page space-y-6">
            <div class="p-8 rounded-[2.5rem] bg-gradient-to-br from-blue-700 to-blue-900 text-white shadow-2xl relative">
                <p class="text-[10px] font-black uppercase opacity-60">Your Balance</p>
                <h2 class="text-4xl font-black tracking-tighter" id="v-bal">₨ 0.00</h2>
                <div id="v-timer-box" class="mt-4 flex justify-between items-center bg-black/20 p-3 rounded-xl backdrop-blur-sm">
                    <span class="text-[8px] font-bold uppercase italic">Next Yield In:</span>
                    <span id="v-timer" class="text-xs font-black tracking-tighter">--:--:--</span>
                </div>
            </div>
            
            <div id="plans-grid" class="grid grid-cols-1 gap-4 pb-20"></div>
        </div>

        <div id="p-deposit" class="page space-y-6">
            <h3 class="text-[10px] font-black uppercase text-center opacity-40 italic tracking-widest">Deposit Funds</h3>
            <div class="glass p-6 rounded-[2.5rem] space-y-4">
                <div class="space-y-2">
                    <div class="p-3 bg-blue-600/10 border border-blue-500/20 rounded-xl">
                        <p class="text-[8px] font-black text-blue-500 uppercase">JazzCash / SadaPay</p>
                        <p class="text-lg font-black">03705519562</p>
                    </div>
                    <div class="p-3 bg-green-600/10 border border-green-500/20 rounded-xl">
                        <p class="text-[8px] font-black text-green-500 uppercase">EasyPaisa</p>
                        <p class="text-lg font-black">03379827882</p>
                    </div>
                </div>
                <input type="number" id="dep-amt" placeholder="Amount (₨)">
                <input type="text" id="dep-tid" placeholder="Transaction ID (TID)">
                <button onclick="sendDeposit()" class="w-full p-4 btn-prime text-[10px] uppercase tracking-widest">Submit Proof ⚡</button>
            </div>
        </div>
    </main>

    <nav class="fixed bottom-0 w-full glass border-t border-[var(--border)] p-4 flex justify-around items-center rounded-t-[2.5rem] z-[4000]">
        <button onclick="changePage('home')" class="flex flex-col items-center gap-1">🏠<span class="text-[7px] font-black">HOME</span></button>
        <button onclick="changePage('deposit')" class="flex flex-col items-center gap-1">➕<span class="text-[7px] font-black">DEPOSIT</span></button>
        <button onclick="logout()" class="flex flex-col items-center gap-1 opacity-50">🚪<span class="text-[7px] font-black">LOGOUT</span></button>
    </nav>

    <div id="admin-ui" class="fixed inset-0 bg-[#010409] z-[100000] hidden overflow-y-auto p-4">
        <div class="flex justify-between items-center mb-6">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">God Mode</h2>
            <button onclick="closeAdmin()" class="bg-red-500 px-4 py-2 rounded-xl text-[10px] font-black tracking-widest">EXIT</button>
        </div>
        <div class="glass p-6 rounded-3xl space-y-4 mb-6">
            <p class="text-[9px] font-black text-blue-400 uppercase">Create New Plan</p>
            <input type="text" id="new-p-name" placeholder="Plan Name (e.g. Node v26)">
            <input type="number" id="new-p-price" placeholder="Price (₨)">
            <input type="number" id="new-p-profit" placeholder="Daily Profit (%)">
            <input type="number" id="new-p-days" placeholder="Duration (Days)">
            <button onclick="createNewPlan()" class="w-full btn-prime p-4 text-[10px] uppercase">Add Plan to Live Node</button>
        </div>
        <div id="adm-msg-list" class="space-y-4"></div>
    </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig);
        const db = firebase.firestore();
        let user = null; let tapCount = 0;

        // AUTH
        function enterApp(n) {
            db.collection("users").doc(n).onSnapshot(d => {
                user = d.data();
                document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                document.getElementById('top-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
                if(user.purchaseTime) startTimer(user.purchaseTime);
            });
            syncPlans();
        }

        // DYNAMIC PLANS SYNC
        function syncPlans() {
            db.collection("plans").orderBy("price", "asc").onSnapshot(s => {
                const grid = document.getElementById('plans-grid'); grid.innerHTML = '';
                
                // Add default 25 plans if collection is empty
                if(s.empty) { generateDefaultPlans(); return; }

                s.forEach(doc => {
                    const p = doc.data();
                    const totalProfit = (p.price * (p.profit/100) * p.days).toLocaleString();
                    grid.innerHTML += `
                    <div class="glass p-6 rounded-[2.5rem] border-l-4 border-blue-500 relative overflow-hidden">
                        <div class="flex justify-between items-start">
                            <div>
                                <h4 class="font-black text-[10px] uppercase text-blue-500 italic">${p.name}</h4>
                                <h3 class="text-xl font-black">₨ ${p.price.toLocaleString()}</h3>
                            </div>
                            <div class="text-right">
                                <span class="bg-blue-600/10 text-blue-500 px-3 py-1 rounded-full text-[8px] font-black">${p.profit}% DAILY</span>
                                <p class="text-[7px] opacity-40 mt-1 uppercase font-bold">${p.days} DAYS</p>
                            </div>
                        </div>
                        <div class="mt-4 pt-4 border-t border-white/5 flex justify-between items-center">
                            <p class="text-[8px] font-black opacity-60 uppercase tracking-tighter">Total Profit: ₨ ${totalProfit}</p>
                            <button onclick="buyNode('${doc.id}', ${p.price})" class="btn-prime px-6 py-2 text-[8px] uppercase tracking-widest shadow-lg shadow-blue-500/20">Activate Node</button>
                        </div>
                    </div>`;
                });
            });
        }

        // CREATE NEW PLAN (ADMIN)
        async function createNewPlan() {
            const name = document.getElementById('new-p-name').value;
            const price = parseInt(document.getElementById('new-p-price').value);
            const profit = parseFloat(document.getElementById('new-p-profit').value);
            const days = parseInt(document.getElementById('new-p-days').value);
            
            if(!name || !price) return alert("Details bhariye sweetie! 😘");
            await db.collection("plans").add({ name, price, profit, days, active: true });
            alert("Naya Plan Live ho gaya! 😘");
        }

        // BUY LOGIC
        async function buyNode(id, price) {
            if(user.balance < price) return alert("Oho! Balance kam hai sweetie, pehle deposit karein. 😘");
            await db.collection("users").doc(user.name).update({
                balance: firebase.firestore.FieldValue.increment(-price),
                purchaseTime: Date.now(),
                activeNodeId: id
            });
            alert("Mining Start ho chuki hai! 🚀");
        }

        // TIMER
        function startTimer(st) {
            setInterval(() => {
                const diff = (st + 86400000) - Date.now();
                if(diff <= 0) {
                    document.getElementById('v-timer').innerText = "READY FOR HARVEST!";
                    document.getElementById('v-timer').classList.add('text-green-500');
                } else {
                    const h = Math.floor(diff/3600000); const m = Math.floor((diff%3600000)/60000); const s = Math.floor((diff%60000)/1000);
                    document.getElementById('v-timer').innerText = `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
                }
            }, 1000);
        }

        async function sendDeposit() {
            const amt = document.getElementById('dep-amt').value;
            const tid = document.getElementById('dep-tid').value;
            if(!amt || !tid) return alert("Pura data likhein! 😘");
            await db.collection("deposits").add({ user: user.name, amount: amt, tid: tid, time: Date.now(), status: "pending" });
            alert("Request submitted! Admin check kar ke balance add kar dega. 😘");
        }

        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Pass:")==="mint786") document.getElementById('admin-ui').classList.remove('hidden'); tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-ui').classList.add('hidden'); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        
        async function generateDefaultPlans() {
            for(let i=1; i<=25; i++) {
                let p = 200 + (i * 300); // 200 se start ho kar barhte jayenge
                await db.collection("plans").add({ name: `Node v${i}`, price: p, profit: (i < 10 ? 5 : 8), days: 30 });
            }
        }

        window.onload = () => { if(localStorage.getItem('mc_user')) enterApp(localStorage.getItem('mc_user')); }
    </script>
</body>
</html>
