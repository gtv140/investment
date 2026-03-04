<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Viral Edition</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;600;700&display=swap');
        * { box-sizing: border-box; -webkit-tap-highlight-color: transparent; font-family: 'Space Grotesk', sans-serif; }
        body { background: #000; color: #fff; height: 100vh; overflow: hidden; display: flex; flex-direction: column; }
        .glass-card { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(15px); border: 1px solid rgba(255, 255, 255, 0.08); border-radius: 1.5rem; }
        .page-container { flex: 1; overflow-y: auto; padding-bottom: 120px; }
        .page { display: none; padding: 20px; animation: fadeIn 0.3s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        .active-nav { color: #3b82f6; transform: scale(1.1); text-shadow: 0 0 10px #3b82f6; }
        input { background: rgba(255,255,255,0.05) !important; border: 1px solid rgba(255,255,255,0.1) !important; color: white !important; border-radius: 12px !important; }
    </style>
</head>
<body>

    <div id="global-note" class="hidden bg-blue-600 p-2 text-[10px] font-bold uppercase text-center z-[3000]"><span id="note-text">System Online</span></div>

    <section id="auth-ui" class="fixed inset-0 z-[4000] bg-black flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-4xl font-bold mb-8 italic uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <div class="glass-card p-8 border-t-2 border-blue-600">
                <input type="text" id="user-name" placeholder="CHOOSE USERNAME" class="w-full p-4 mb-4 text-center font-bold uppercase outline-none">
                <input type="text" id="ref-by" placeholder="REFERRAL ID (OPTIONAL)" class="w-full p-4 mb-6 text-center text-xs uppercase outline-none">
                <button onclick="login()" class="w-full bg-blue-600 p-4 rounded-xl font-bold uppercase text-xs tracking-widest active:scale-95">Start Mining</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden page-container">
        
        <div id="p-home" class="page active-page">
            <div class="glass-card p-8 mb-6 border-l-8 border-blue-500 shadow-2xl relative overflow-hidden">
                <div class="absolute top-0 right-0 p-4 text-[8px] font-bold opacity-20 uppercase" id="u-id-display">ID: ---</div>
                <p class="text-[10px] text-blue-400 font-bold uppercase tracking-widest mb-1">Total Valuation</p>
                <h2 class="text-5xl font-bold tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="timer-box" class="mt-6 hidden bg-blue-500/10 p-4 rounded-2xl border border-blue-500/20">
                    <div id="countdown" class="text-xl font-bold text-yellow-500 font-mono text-center">00:00:00</div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('wallet')" class="glass-card p-6 text-center">📥<br><span class="text-[10px] font-bold uppercase mt-2 block">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass-card p-6 text-center">📤<br><span class="text-[10px] font-bold uppercase mt-2 block text-red-500">Withdraw</span></button>
            </div>

            <div class="glass-card p-6 border-t-4 border-yellow-500">
                <p class="text-[10px] font-bold text-yellow-500 uppercase mb-2 italic">Refer & Earn</p>
                <p class="text-[9px] text-gray-400 mb-4">Share your Username as Referral ID to get ₨ 50 bonus per active user!</p>
                <div id="share-link" class="bg-black/50 p-3 rounded-xl text-center font-bold text-blue-400 text-xs select-all uppercase">---</div>
            </div>
        </div>

        <div id="p-invest" class="page">
            <h2 class="text-xl font-bold mb-6 text-center italic uppercase">Market Fleet</h2>
            <div id="plans-list" class="space-y-3"></div>
        </div>

        <div id="p-activity" class="page">
            <h2 class="text-xl font-bold mb-6 italic uppercase">Logs & Records</h2>
            <div id="user-history" class="space-y-2"></div>
        </div>

        <div id="p-support" class="page text-center">
            <div class="bg-blue-600 p-10 rounded-[2.5rem] mb-6 shadow-xl shadow-blue-600/20">
                <h3 class="font-bold text-2xl mb-4">Community</h3>
                <a href="https://chat.whatsapp.com/GTV140" class="bg-white text-blue-600 px-10 py-4 rounded-full font-bold uppercase text-xs inline-block">Join WhatsApp Group</a>
            </div>
            <div class="grid grid-cols-1 gap-3">
                <div class="glass-card p-5 text-left"><p class="text-[10px] font-bold text-blue-400 uppercase italic">Rules</p><p class="text-[9px] text-gray-500 mt-2">Withdrawals are processed within 24 hours. Multiple accounts will be banned.</p></div>
            </div>
        </div>

        <div id="p-wallet" class="page">
            <div class="glass-card p-8">
                <h3 class="text-center font-bold text-blue-400 mb-8 uppercase italic">Funds Injection</h3>
                <div class="bg-blue-900/20 p-5 rounded-2xl mb-6 text-[11px] text-center border border-blue-500/20">EASYPAISA: 03379827882<br>JAZZCASH: 03705519562</div>
                <input type="number" id="dep-amount" placeholder="PKR AMOUNT" class="w-full p-4 mb-4 text-center font-bold">
                <input type="text" id="dep-trx" placeholder="TRANSACTION TID" class="w-full p-4 mb-8 text-center uppercase font-bold">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 p-4 rounded-xl font-bold uppercase text-xs">Authorize Deposit</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <div class="glass-card p-8">
                <h3 class="text-center font-bold text-red-500 mb-8 uppercase italic">Extraction</h3>
                <input type="number" id="wd-amount" placeholder="MIN 500" class="w-full p-4 mb-4 text-center font-bold">
                <input type="text" id="wd-method" placeholder="AC DETAILS" class="w-full p-4 mb-8 text-center font-bold uppercase">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 p-4 rounded-xl font-bold uppercase text-xs">Request Payout</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass-card mx-6 my-4 p-4 flex justify-around items-center fixed bottom-0 left-0 right-0 z-[3000] bg-black/80 backdrop-blur-xl">
        <button onclick="changePage('home')" id="n-home" class="active-nav text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl">📊</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-2xl">📜</button>
        <button onclick="changePage('support')" id="n-support" class="text-2xl">🎧</button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-black z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-8"><h2 class="text-xl font-bold text-blue-500 uppercase italic">System Master</h2><button onclick="closeAdmin()" class="text-red-500 font-bold uppercase text-xs">Exit</button></div>
        <div class="glass-card p-6 mb-6">
            <input type="text" id="adm-user" placeholder="USER ID" class="w-full p-3 mb-2 text-xs uppercase">
            <input type="number" id="adm-val" placeholder="AMOUNT" class="w-full p-3 mb-4 text-xs">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="manualEdit('add')" class="bg-green-600 p-3 rounded-xl text-[10px] font-bold uppercase">Add Bal</button>
                <button onclick="manualEdit('rem')" class="bg-red-600 p-3 rounded-xl text-[10px] font-bold uppercase">Sub Bal</button>
            </div>
        </div>
        <h3 class="text-sm font-bold text-yellow-500 mb-4 uppercase italic px-2">Requests Pool</h3>
        <div id="adm-requests" class="space-y-2 mb-10"></div>
    </div>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        
        let user = null, tapCount = 0, timerInt = null;
        const plans = []; for(let i=1; i<=25; i++) { let prc = (i===1)?200:i*1000; plans.push({ id:`p${i}`, n: i>20?"Elite":"Basic", p: prc, r: 4+(i*0.4) }); }

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase();
            const refID = document.getElementById('ref-by').value.trim().toUpperCase();
            if(!name) return;
            const ref = db.collection("users").doc(name);
            const doc = await ref.get();
            if(!doc.exists) { 
                await ref.set({ name: name, balance: 0, activePlanId: null, lastRequestTime: Date.now(), referredBy: refID || null }); 
                if(refID) {
                    const rRef = db.collection("users").doc(refID);
                    const rDoc = await rRef.get();
                    if(rDoc.exists) {
                        await db.collection("requests").add({ user: refID, amount: 50, type: "REFERRAL BONUS", status: "pending", time: Date.now() });
                    }
                }
            }
            localStorage.setItem('mc_auth', name);
            ref.onSnapshot(d => { user = d.data(); if(user) { 
                document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                document.getElementById('share-link').innerText = user.name;
                document.getElementById('u-id-display').innerText = "ID: " + user.name;
                renderPlans(); startCountdown(); checkAutoProfit(); 
            } });
            document.getElementById('auth-ui').classList.add('hidden'); 
            document.getElementById('app-ui').classList.remove('hidden'); 
            document.getElementById('bottom-nav').classList.remove('hidden');
            syncHistory(); syncGlobalNote();
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); if(!l) return; l.innerHTML = '';
            plans.forEach(p => {
                const isOwned = user.activePlanId === p.id;
                l.innerHTML += `<div onclick="${isOwned?'':`buy(${p.p},'${p.id}',${p.r})`}" class="glass-card p-6 flex justify-between items-center transition-all ${isOwned?'border-blue-500 border-2 bg-blue-500/10':''}">
                    <div><h4 class="font-bold text-[10px] uppercase text-blue-400 italic">${p.n} ${p.id.substring(1)}</h4><p class="text-[8px] text-gray-500 font-bold">${p.r.toFixed(1)}% DAILY</p></div>
                    <div class="font-bold text-lg">₨ ${p.p.toLocaleString()}</div>
                </div>`;
            });
        }

        async function buy(p, id, rate) {
            if(user.balance < p) return alert("Sweetie, low balance!");
            if(user.activePlanId) return alert("One node at a time!");
            await db.collection("users").doc(user.name).update({ balance: user.balance-p, activePlanId: id, dailyRate: rate, lastPrice: p, lastRequestTime: Date.now() });
            alert("Fleet Active!");
        }

        function syncHistory() {
            db.collection("requests").where("user", "==", user.name).orderBy("time", "desc").onSnapshot(snap => {
                const list = document.getElementById('user-history'); if(!list) return;
                list.innerHTML = '';
                snap.forEach(d => { const r = d.data(); list.innerHTML += `<div class="glass-card p-4 flex justify-between items-center text-[10px] font-bold uppercase mb-1"><div>${r.type}<br><span class="opacity-25 text-[7px]">${new Date(r.time).toLocaleString()}</span></div><div class="text-right">₨ ${r.amount}<br><span class="${r.status==='approved'?'text-green-500':'text-yellow-500'} text-[8px]">${r.status}</span></div></div>`; });
            });
        }

        async function checkAutoProfit() {
            if(!user.activePlanId || Date.now() - user.lastRequestTime < 86400000) return;
            const amt = Math.floor((user.dailyRate/100)*user.lastPrice);
            await db.collection("requests").add({ user: user.name, amount: amt, type: "NODE PROFIT", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastRequestTime: Date.now() });
        }

        function startCountdown() {
            if(!user.activePlanId) return;
            document.getElementById('timer-box').classList.remove('hidden');
            if(timerInt) clearInterval(timerInt);
            timerInt = setInterval(() => {
                const diff = (user.lastRequestTime + 86400000) - Date.now();
                const cd = document.getElementById('countdown');
                if(diff <= 0) { cd.innerText = "PROFIT READY! ⚡"; return; }
                const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000);
                cd.innerText = `${String(h).padStart(2,'0')}:${String(m).padStart(2,'0')}:${String(s).padStart(2,'0')}`;
            }, 1000);
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-nav')); document.getElementById('n-'+p)?.classList.add('active-nav'); }
        function adminTap() { tapCount++; if(tapCount>=5) { if(prompt("Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        
        function syncAdmin() {
            db.collection("requests").where("status","==","pending").onSnapshot(snap => {
                const l = document.getElementById('adm-requests'); l.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); l.innerHTML += `<div class="glass-card p-4 text-[10px] mb-2 flex justify-between items-center border-l-4 border-yellow-500"><div>${d.user}<br>${d.type} - ₨ ${d.amount}</div><button onclick="hndl('${doc.id}','${d.user}',${d.amount})" class="bg-green-600 px-4 py-2 rounded-xl text-[8px] font-bold">APPROVE</button></div>`; });
            });
        }
        async function hndl(id, u, amt) { const ref = db.collection("users").doc(u); const d = (await ref.get()).data(); await ref.update({ balance: (d.balance||0) + amt }); await db.collection("requests").doc(id).update({ status: 'approved' }); }
        async function manualEdit(act) { const u = document.getElementById('adm-user').value.toUpperCase(), v = parseInt(document.getElementById('adm-val').value); const ref = db.collection("users").doc(u); const d = (await ref.get()).data(); await ref.update({ balance: act==='add'?(d.balance+v):(d.balance-v) }); alert("Done!"); }
        async function submitWithdraw() { const a = parseInt(document.getElementById('wd-amount').value), m = document.getElementById('wd-method').value; if(a < 500 || user.balance < a) return alert("Check Balance!"); await db.collection("requests").add({ user: user.name, amount: a, details: m, type: "WITHDRAW", status: "pending", time: Date.now() }); await db.collection("users").doc(user.name).update({ balance: user.balance - a }); alert("Requested!"); changePage('home'); }
        async function submitDeposit() { const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value; await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, type: "DEPOSIT", status: "pending", time: Date.now() }); alert("Sent!"); changePage('home'); }
        function syncGlobalNote() { db.collection("settings").doc("broadcast").onSnapshot(d => { const bar = document.getElementById('global-note'); if(d.exists && d.data().msg) { bar.classList.remove('hidden'); document.getElementById('note-text').innerText = d.data().msg; } }); }
        window.onload = () => { const s = localStorage.getItem('mc_auth'); if(s) { document.getElementById('user-name').value = s; login(); } };
    </script>
</body>
</html>
