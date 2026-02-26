<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Ultra | Elite Investing</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .scroller::-webkit-scrollbar { display: none; }
        .page { display: none; animation: fadeIn 0.3s ease-in-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
        .plan-card { transition: all 0.3s ease; border-left: 4px solid transparent; }
        .plan-card:hover { transform: translateX(5px); border-left-color: #3b82f6; background: rgba(59,130,246,0.05); }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="h-full flex items-center justify-center p-6 bg-[#010409] z-[1000] absolute inset-0">
        <div class="glass w-full max-w-sm p-10 rounded-[3rem] text-center border-t-8 border-blue-600 shadow-2xl">
            <h1 class="text-3xl font-black italic tracking-tighter mb-2">MINTCREST</h1>
            <p class="text-gray-500 text-[10px] uppercase tracking-widest mb-10">Secure Cloud Access</p>
            <input type="text" id="user-name" placeholder="Username" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none text-center mb-4 font-bold">
            <button onclick="login()" class="w-full bg-blue-600 py-4 rounded-2xl font-bold shadow-xl active:scale-95 transition">SIGN IN</button>
        </div>
    </section>

    <div id="app-ui" class="hidden flex-1 overflow-y-auto scroller pb-24">
        
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mb-8">
                <div><p class="text-[10px] text-gray-500 font-bold uppercase">Account Balance</p><h2 class="text-4xl font-black tracking-tighter text-blue-500" id="v-bal">₨ 0</h2></div>
                <div class="h-12 w-12 glass rounded-2xl flex items-center justify-center text-xl">🔱</div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <div class="glass p-5 rounded-3xl"><p class="text-[9px] text-gray-500 mb-1 font-bold">EARNINGS</p><p class="font-bold text-green-400" id="v-profit">₨ 0</p></div>
                <div class="glass p-5 rounded-3xl"><p class="text-[9px] text-gray-500 mb-1 font-bold">ACTIVE PLANS</p><p class="font-bold text-blue-400" id="v-plans">0</p></div>
            </div>

            <button onclick="changePage('wallet')" class="w-full glass p-6 rounded-[2rem] border-2 border-dashed border-blue-500/30 flex items-center justify-center gap-4 hover:bg-blue-600/5 transition">
                <span class="text-2xl">➕</span>
                <span class="font-bold text-sm">ADD FUNDS TO WALLET</span>
            </button>
        </div>

        <div id="p-invest" class="page p-6">
            <div class="mb-6">
                <h2 class="text-2xl font-black italic text-blue-500 uppercase">Market Tiers</h2>
                <p class="text-[10px] text-gray-500 font-bold">SELECT A PLAN TO START EARNING</p>
            </div>
            <div id="plans-list" class="space-y-3"></div>
        </div>

        <div id="p-wallet" class="page p-6">
            <h2 class="text-2xl font-black mb-8 italic text-center uppercase">Deposit Funds</h2>
            <div class="glass p-8 rounded-[3rem] border-t-8 border-blue-600 shadow-2xl">
                <div class="flex justify-around mb-8 p-2 glass rounded-2xl">
                    <div class="text-center"><p class="text-[8px] font-bold text-red-500">JAZZCASH</p><p class="text-xs font-bold">03705519562</p></div>
                    <div class="text-center border-l border-white/10 pl-4"><p class="text-[8px] font-bold text-green-500">EASYPAISA</p><p class="text-xs font-bold">03379827882</p></div>
                </div>
                
                <div class="space-y-4">
                    <div class="relative">
                        <span class="absolute left-4 top-4 text-gray-500 font-bold text-xs">₨</span>
                        <input type="number" id="dep-amount" placeholder="Amount (Min 500)" class="w-full bg-white/5 p-4 pl-8 rounded-2xl border border-white/10 outline-none focus:border-blue-500">
                    </div>
                    <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none focus:border-blue-500">
                    <button onclick="submitDeposit()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-xs shadow-lg active:scale-95 transition uppercase tracking-widest">Submit Verification</button>
                </div>
            </div>
        </div>
    </div>

    <div id="admin-panel" class="fixed inset-0 bg-[#010409] z-[5000] p-6 hidden overflow-y-auto">
        <div class="max-w-xl mx-auto">
            <div class="flex justify-between items-center mb-8 pb-4 border-b border-white/10">
                <h2 class="text-xl font-black text-blue-500">ADMIN CONTROL</h2>
                <button onclick="closeAdmin()" class="bg-red-600/20 text-red-500 px-4 py-2 rounded-xl text-[10px] font-bold">LOGOUT</button>
            </div>

            <div class="mb-10">
                <h3 class="text-[10px] font-bold text-gray-500 mb-4 uppercase tracking-[0.2em]">Pending Requests (<span id="req-count">0</span>)</h3>
                <div id="adm-req-list" class="space-y-3 text-center py-10 opacity-50" id="no-req">No Pending Requests</div>
            </div>

            <div class="glass p-6 rounded-[2rem] border border-blue-500/20">
                <h3 class="text-xs font-bold mb-4 uppercase">Direct Wallet Access</h3>
                <input type="text" id="adm-user" placeholder="Enter Exact Username" class="w-full bg-white/5 p-4 rounded-xl mb-4 border border-white/10">
                <div class="grid grid-cols-2 gap-4">
                    <button onclick="manualEdit('balance')" class="bg-blue-600 py-4 rounded-xl text-[10px] font-bold uppercase">Add Cash</button>
                    <button onclick="manualEdit('profit')" class="bg-green-600 py-4 rounded-xl text-[10px] font-bold uppercase">Add Profit</button>
                </div>
                <input type="number" id="adm-val" placeholder="Value" class="w-full bg-white/5 p-4 rounded-xl mt-4 border border-white/10">
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-4 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 active-tab">🏠<span class="text-[8px] font-bold uppercase">Home</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-1">📈<span class="text-[8px] font-bold uppercase">Tiers</span></button>
        <button onclick="changePage('wallet')" id="n-wallet" class="flex flex-col items-center gap-1">💰<span class="text-[8px] font-bold uppercase">Funds</span></button>
    </nav>

    <script>
        // CONFIG (Same as before)
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

        let user = null;
        // 20 DYNAMIC PLANS
        const plans = [
            { n: "Bronze 1", p: 500, r: "10%" }, { n: "Bronze 2", p: 1000, r: "15%" },
            { n: "Silver 1", p: 2500, r: "18%" }, { n: "Silver 2", p: 5000, r: "20%" },
            { n: "Gold 1", p: 10000, r: "25%" }, { n: "Gold 2", p: 25000, r: "30%" },
            { n: "Platinum 1", p: 50000, r: "35%" }, { n: "Platinum 2", p: 75000, r: "40%" },
            { n: "VIP Star", p: 100000, r: "50%" }, { n: "VIP Elite", p: 150000, r: "55%" },
            { n: "Master 1", p: 200000, r: "60%" }, { n: "Master 2", p: 300000, r: "70%" },
            { n: "Grand 1", p: 500000, r: "80%" }, { n: "Grand 2", p: 750000, r: "90%" },
            { n: "Elite 1", p: 1000000, r: "100%" }, { n: "Elite 2", p: 1500000, r: "120%" },
            { n: "Legend 1", p: 2000000, r: "150%" }, { n: "Legend 2", p: 3000000, r: "200%" },
            { n: "Kingdom", p: 4000000, r: "250%" }, { n: "Apex Infinity", p: 5000000, r: "400%" }
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim();
            if(!name) return alert("Enter Name!");
            const ref = db.collection("users").doc(name);
            const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, plans: 0 });
            startSync(name);
            document.getElementById('auth-ui').style.display = 'none';
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                if(doc.exists) {
                    user = doc.data();
                    document.getElementById('v-bal').innerText = "₨ " + user.balance.toLocaleString();
                    document.getElementById('v-profit').innerText = "₨ " + user.profit.toLocaleString();
                    document.getElementById('v-plans').innerText = user.plans || 0;
                }
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list');
            list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buy(${p.p})" class="glass p-5 rounded-2xl flex justify-between items-center plan-card cursor-pointer">
                    <div><h4 class="font-bold text-xs uppercase">${p.n}</h4><p class="text-[9px] text-green-400">RETURNS: ${p.r}</p></div>
                    <p class="font-black text-blue-500 text-sm">₨ ${p.p.toLocaleString()}</p>
                </div>`;
            });
        }

        async function submitDeposit() {
            const amt = document.getElementById('dep-amount').value;
            const trx = document.getElementById('dep-trx').value;
            if(amt < 500 || !trx) return alert("Min 500 & TID required!");
            
            // Ye Collection "deposits" automatic Firebase mein ban jayegi
            await db.collection("deposits").add({
                user: user.name,
                amount: parseInt(amt),
                tid: trx,
                status: "pending",
                timestamp: Date.now()
            });
            alert("Deposit Request Submitted Successfully!");
            changePage('home');
        }

        async function buy(price) {
            if(user.balance < price) { alert("Insufficient Funds!"); changePage('wallet'); }
            else { 
                await db.collection("users").doc(user.name).update({ 
                    balance: user.balance - price, 
                    plans: (user.plans || 0) + 1 
                }); 
                alert("Plan Activated!"); 
            }
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(page => page.classList.remove('active-page'));
            document.querySelectorAll('nav button').forEach(btn => btn.classList.remove('active-tab'));
            document.getElementById('p-' + p).classList.add('active-page');
            document.getElementById('n-' + p).classList.add('active-tab');
        }

        // --- ADMIN REALTIME SYNC ---
        window.addEventListener('keydown', e => {
            if(e.ctrlKey && e.shiftKey && e.key === 'A') {
                if(prompt("Admin Key:") === "mint786") {
                    document.getElementById('admin-panel').classList.remove('hidden');
                    syncAdminRequests();
                }
            }
        });

        function syncAdminRequests() {
            db.collection("deposits").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-req-list');
                document.getElementById('req-count').innerText = snap.size;
                if(snap.empty) { list.innerHTML = 'No Pending Requests'; return; }
                list.innerHTML = '';
                snap.forEach(doc => {
                    const d = doc.data();
                    list.innerHTML += `
                        <div class="glass p-5 rounded-2xl flex justify-between items-center border-l-4 border-yellow-500">
                            <div class="text-left text-[10px]">
                                <b class="text-blue-400 uppercase">${d.user}</b>
                                <p class="opacity-70">₨ ${d.amount} | TID: ${d.tid}</p>
                            </div>
                            <button onclick="approveDeposit('${doc.id}', '${d.user}', ${d.amount})" class="bg-green-600 px-4 py-2 rounded-xl text-[9px] font-black uppercase">Approve</button>
                        </div>`;
                });
            });
        }

        async function approveDeposit(id, target, amt) {
            const ref = db.collection("users").doc(target);
            const d = await ref.get();
            if(d.exists) {
                await ref.update({ balance: (d.data().balance || 0) + amt });
                await db.collection("deposits").doc(id).update({ status: "approved" });
                alert("Verified & Added!");
            }
        }

        async function manualEdit(field) {
            const u = document.getElementById('adm-user').value;
            const v = document.getElementById('adm-val').value;
            const ref = db.collection("users").doc(u);
            const d = await ref.get();
            if(d.exists) {
                await ref.update({ [field]: (d.data()[field] || 0) + parseInt(v) });
                alert("Success!");
            } else { alert("User Not Found!"); }
        }

        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
    </script>
</body>
</html>
