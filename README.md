<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Final VIP</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.07); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; }
        .active-page { display: block; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-5xl font-black italic mb-2 cursor-pointer uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-12 font-bold italic">Official Digital Asset Vault</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 text-white">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95 transition-all">Connect Session</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600">
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Available Capital</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div class="mt-6 flex gap-3">
                    <span class="text-[8px] bg-green-500/10 text-green-400 px-4 py-2 rounded-full font-bold uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95">📥 <span class="text-[9px] font-black block mt-2 uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95">📤 <span class="text-[9px] font-black block mt-2 uppercase">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-xl tracking-widest">Trading Fleet</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-400">Security Ledger</h2>
            <div id="user-history" class="space-y-2 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="glass p-8 rounded-[3rem] text-center border-t-4 border-yellow-500">
                <h3 class="text-yellow-500 font-black text-[12px] uppercase mb-4 italic">Lucky Spin Node</h3>
                <div id="spin-visual" class="w-24 h-24 border-4 border-dashed border-yellow-500/20 rounded-full mx-auto mb-6 flex items-center justify-center text-3xl bg-white/5">🎡</div>
                <button id="spin-btn" onclick="startSpin()" class="bg-yellow-600 px-8 py-3 rounded-2xl font-black text-[10px] uppercase shadow-lg disabled:opacity-30">Spin Now</button>
                <p id="spin-timer" class="mt-4 text-[8px] font-bold text-gray-500 uppercase tracking-widest italic"></p>
            </div>

            <div class="glass p-8 rounded-[3rem] text-center border-b-4 border-green-500">
                <h3 class="text-green-500 font-black text-[10px] uppercase mb-4">Official Group</h3>
                <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" class="inline-block bg-green-600 px-8 py-3 rounded-2xl font-black text-[9px] uppercase tracking-widest">💬 Join WhatsApp</a>
            </div>

            <div class="glass p-8 rounded-[3rem]">
                <h3 class="text-blue-500 font-black text-[11px] uppercase italic text-center underline mb-4">Firm Privacy & Terms</h3>
                <div class="text-[8px] text-gray-400 space-y-3 font-bold uppercase italic text-left">
                    <p>1. Withdrawals: Processed within 24 hours. 10% tax applies.</p>
                    <p>2. Security: One account per device. Breach results in asset freeze.</p>
                    <p>3. Support: Official WhatsApp is the only support channel.</p>
                </div>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-[10px] font-black text-red-500 uppercase">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] text-center border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm italic tracking-widest">Funding Console</h3>
                <div class="space-y-2 mb-8 text-[10px] font-black text-left uppercase">
                    <p>JazzCash/Sadapay: 03705519562</p>
                    <p>Easypaisa: 03379827882</p>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none border border-white/5 text-white">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold uppercase outline-none border border-white/5 text-white">
                
                <input type="text" id="dep-promo" placeholder="PROMO CODE" class="w-full bg-blue-500/10 p-4 rounded-2xl mb-6 text-center text-[9px] font-black uppercase outline-none border border-blue-500/20 text-blue-400">
                
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[10px] uppercase shadow-lg">Verify Funding</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <h2 class="text-xl font-black text-blue-500 uppercase mb-10 italic">Master Console</h2>
        <div id="adm-sec-requests" class="space-y-3"></div>
        <button onclick="closeAdmin()" class="mt-10 bg-red-600 px-8 py-3 rounded-2xl text-[10px] font-black uppercase">Close Panel</button>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-2xl">📜</button>
        <button onclick="changePage('more')" id="n-more" class="text-2xl">🏢</button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), lastSpin: 0 });
            startSync(name);
            document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); updateUI(); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 mb-2 flex justify-between uppercase font-black text-[9px]"><span>${d.type}</span><span>Rs ${d.amount} (${d.status})</span></div>`; });
            });
        }

        function updateUI() {
            document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString();
            const last = user.lastSpin || 0;
            const btn = document.getElementById('spin-btn');
            if(Date.now() - last < 86400000) { btn.disabled = true; document.getElementById('spin-timer').innerText = "LOCKED"; }
            else { btn.disabled = false; document.getElementById('spin-timer').innerText = "READY"; }
        }

        async function startSpin() {
            const win = [10, 20, 50, 0][Math.floor(Math.random()*4)];
            if(win > 0) await db.collection("requests").add({ user: user.name, amount: win, type: "Spin Bonus", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastSpin: Date.now() });
            alert("Spin Done! Result sent to admin.");
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value, p = document.getElementById('dep-promo').value;
            if(!a || !t) return alert("Enter Details!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, promo: p||"NONE", type: "deposit", status: "pending", time: Date.now() });
            alert("Sent for Verification!"); changePage('activity');
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Master Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 flex justify-between text-[10px] font-black uppercase mb-2"><div>${d.user}<br>Rs ${d.amount}<br>Promo: ${d.promo}</div><button onclick="handle('${doc.id}','${d.user}',${d.amount})" class="bg-blue-600 px-6 rounded-xl">APPROVE</button></div>`; });
            });
        }
        async function handle(id, u, amt) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get(); 
            await ref.update({ balance: (doc.data().balance||0)+amt });
            await db.collection("requests").doc(id).update({ status: 'approved' });
        }
    </script>
</body>
</html>
